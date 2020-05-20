+++
content = ""
date = "2019-03-28T20:36:26+00:00"
tags = ["powershell", "dsc"]
title = "Creating 100s of NTFS permissions with Powershell and DSC"

+++
Starting at my new employer one of the first major projects to hit implementation phase is a migration of the company file server.  The major wrinkle is that the old file server is on Novell, with 25+ years of permission and folder buildup. Millions of files, hundreds of thousands of folders.

The normal Windows to Windows robocopy won't work to preserve permissions, from a Windows perspective the owner and ACL for all files/folders on a Novell share is 'Everyone.'  All permissions must be mapped from Novell OUs, Groups, & Users to AD OUs, Groups, & Users and subsequently applied to the newly copied files and folders on a Windows file server.

If the permissions were simple this would be no issue, but with so many years of inertia it is a bit of a mess with around 600 different points where permissions are applied, inheritance broken, or traversal needing to be mapped to match 1 to 1. No way I am doing that by hand.

Luckily I have Powershell Desired State Configuration and foreach at my disposal. It comes down to two components. A CSV file that my boss can use to map the existing Novell permissions to their new AD permissions and a powershell script heavily utilizing [cNtfsAccessControl](https://github.com/SNikalaichyk/cNtfsAccessControl "cNtfsAccessControl"). Because of the number of lines in the output *.mof and the need for unique naming I utilize guid generation for naming.

Here's a small snip of the start of my script. Available [here](https://github.com/tonyharmelink/powershell-scripts/tree/master/MigrateFileServer "https://github.com/tonyharmelink/powershell-scripts/tree/master/MigrateFileServer").

{{< highlight ps1 >}}
        # Parse the lines from our CSV, expected columns are:
        # Folder path, full path with drive D:\[bla\]
        # newFull to designate a principal to recieve a Full Access permission
        # newRW to designate a principal to recieve a Read and Write permission
        # newRO to designate a principal to recieve a Read Only permission
        # folderTraverse to designate a principal to recieve a Folder Traversal permission
        # noAccess to break inheretance, make permissions, and remove the noAccess principal

            Import-Csv ".\home.csv" | ForEach-Object {
    
                # If there is a principal to apply a Full Access permission to
                if (-not ([string]::IsNullOrEmpty($_.newFull)))
                {
    
                    cNtfsPermissionEntry "@{ Result = $([guid]::NewGuid()).$_.Folder.$_.newFull }"
                    {
                        Ensure = 'Present'
                        Path = $_.Folder
                        Principal = $_.newFull
                        AccessControlInformation = @(
                            cNtfsAccessControlInformation
                            {
                                AccessControlType = 'Allow'
                                FileSystemRights = 'FullControl'
                                Inheritance = 'ThisFolderSubfoldersAndFiles'
                                NoPropagateInherit = $false
                            }
                        )
                    }
                }

{{< /highlight >}}

The execution went well. All of the files were pre-copied with robocopy onto the new server. The days leading up to and the night of the cutover we used Beyond Compare to sync any delta changes. With DSC we can re-run the same script and it only makes changes to new files/folders or any permission alterations we have made to the CSV.

Huge success!