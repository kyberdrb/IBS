# IBS - Integrated Backup Suite

The complete mainenance guide with tools for sustainable and automated Arch Linux usage and adminstration

1. backup android apps - `backup_and_restore_android_apps`

    Enable `Debugging mode`.

        date && time "${HOME}/git/kyberdrb/Android_tutorials/backup_and_restore_android_apps/backup_apps.sh" "${HOME}/backup-sony_xa1/apps/" && date
        
    Disable `Debugging mode`.

    1. **[OPTIONAL - SKIP IF THE `apps` DIR IS NEWLY CREATED]** find duplicates in the directory with backed up apps - `duplicate_finder`

            date && time "${HOME}/git/kyberdrb/duplicate_finder/build-release.sh" && date
            date && time "${HOME}/git/kyberdrb/duplicate_finder/cmake-build-release/duplicate_finder" "${HOME}/backup-sony_xa1/apps/" && date

        Then delete the entire directory `DUPLICATE_FILES` in the `apps` directory.

            rm -rf "${HOME}/backup-sony_xa1/apps/DUPLICATE_FILES/"

    1. compress the `apps` directory

            date && time 7z a -t7z -mx=9 -ms=on -mf=on -mhc=on -mhe=on -m0=lzma2 -mfb=273 -md=64m -v4g "-pMY SUPER STRONK CLOUD PASSWORD" "${HOME}/backup-sony_xa1/apps.7z" "${HOME}/backup-sony_xa1/apps/" && date
            
    1. generate checksums to verify backup integrity in case of restoring the backup (`Linux_utils_and_gists/generate_checksum "apps.7z"`)

            sha256sum "${HOME}/backup-sony_xa1/apps.7z" | tee "${HOME}/backup-sony_xa1/apps.7z.sha256sum"

    1. upload the archive with its checksum to the cloud, replacing current backup

        After the upload is complete, delete the local files

            rm -rf "${HOME}/backup-sony_xa1/apps.7z"
            rm -rf "${HOME}/backup-sony_xa1/apps/"
    
1. backup android browser tabs - `backup_and_restore_browser_tabs`

    Enable `Debugging mode`.

        date && time "${HOME}/git/kyberdrb/Android_tutorials/backup_and_restore_browser_tabs/backup_tabs-Via_browser_by_Tu_Yafeng.sh" && date
        
    Disable `Debugging mode`.
    
1. backup contacts
    - Open `Contacts` app and export all contacts into `vcf` format to the root directory of the internal phone storage.

1. Manually backup chat history for instant messaging apps
    - WhatsApp: three dots -> Settings -> Chats -> Chat Backup, then click on `Back up` button to manually invoke the backup. I had `End-to-end encryption` option disabled.
        - https://duckduckgo.com/?q=backup+whatsapp&ia=web
        - https://faq.whatsapp.com/744445782709185/?cms_platform=android
    - Viber - three dots in the bottom bar -> Settings -> Account -> Viber Backup, check options `Include photos` and `Include videos`, select the account for backup, then click on `Back up now` button
        - https://duckduckgo.com/?q=backup+viber&ia=web
        - https://mobiletrans.wondershare.com/viber/complete-viber-backup.html
    - Messenger - not necessary: the chat history is automatically loaded at the first login onto the new device
        - click on the avatar in the top left corner -> Settings -> Download copy
    - Signal: backup successfully created, but the recovery failed
        - screenshot of the password -> adb pull to PC -> crop the password from the screenshot -> do an OCR on the cropped image to a text file -> remove any newlines from the text file -> push the text file back to the root dir on the internal storage of the phone as "signal-backup_password.txt"

1. backup SMS and call history e.g. with app `SMS Backup & Restore`
    - set up local backup onto custom location - the root directory in internal memory

1. copy regulary used text files with the computer

    Enable `Debugging mode`.

        gio trash "${HOME}/backup-sony_xa1/Phone/"
        mkdir --parents "${HOME}/backup-sony_xa1/Phone/"
        date && time adb shell find /sdcard/ -maxdepth 1 -type f | xargs -I {} adb pull "{}" "/home/laptop/backup-sony_xa1/Phone/" && date
        
    _Note: `adb pull` mode is preferred over MTP for copying because it preserves the timestamps and metadata of the files._
        
    Disable `Debugging mode`.
    
    Upload the files to the cloud: Either directly [PREFERRED] one-by-one in bulk mode via multiple-selection upload...
    
    [OPTIONAL] ...or when copying the files via MTP instad of `adb pull` by compressing them with:
    
        date && time 7z a -t7z -mx=9 -ms=on -mf=on -mhc=on -mhe=on -m0=lzma2 -mfb=273 -md=64m -v4g "-pMY SUPER STRONK CLOUD PASSWORD" "${HOME}/backup-sony_xa1/docs_from_root_dir.7z" "${HOME}/backup-sony_xa1/Phone/*" && date
        
    and uploading them as one file.

1. `Android_tutorials` - files backup

    Enable `Debugging mode`.

    Back up files:

        gio trash "${HOME}/backup-sony_xa1/Phone-complete/"
        mkdir --parents "${HOME}/backup-sony_xa1/Phone-complete/"
        date && time adb pull /storage/sdcard0/. "${HOME}/backup-sony_xa1/Phone-complete/" && date
        
    _Note: the dot `.` at the end of the source path for the `adb pull` makes the `pull` command copy the files **recursively**_
    
    Disable `Debugging mode`.

    Compress files to an archive:

        date && time 7z a -t7z -mx=9 -ms=on -mf=on -mhc=on -mhe=on -m0=lzma2 -mfb=273 -md=64m -v4g "-pMY SUPER STRONK CLOUD PASSWORD" "${HOME}/backup-sony_xa1/Phone-complete.7z" "${HOME}/backup-sony_xa1/Phone-complete/" && date

    Check the multipart archive with
    
        7z l Phone-complete.7z.001 | less
        
    Generate checksums for each part of the archive (`Linux_utils_and_gists/generate_checksums_for_split_archive.sh "Phone-complete.7z.001"`)
    
        find "${HOME}/backup-sony_xa1/" -name "Phone-complete.7z.[0-9]*" | sort | xargs -I "{}" sha256sum "{}" | tee "${HOME}/backup-sony_xa1/Phone-complete.7z.sha256sums"

        80490c66985bdeadd5db295e65ede921e44406ee00c6dbc6bbbcfdf92d442a61  /home/laptop/backup-sony_xa1/Phone-complete.7z.001
        854f1d2b061fb42f09e4585fc1fac8c8816f03a72a6c02c3b30322b053c49c9e  /home/laptop/backup-sony_xa1/Phone-complete.7z.002

        cat "${HOME}/backup-sony_xa1/Phone-complete.7z.sha256sums"

        80490c66985bdeadd5db295e65ede921e44406ee00c6dbc6bbbcfdf92d442a61  /home/laptop/backup-sony_xa1/Phone-complete.7z.001
        854f1d2b061fb42f09e4585fc1fac8c8816f03a72a6c02c3b30322b053c49c9e  /home/laptop/backup-sony_xa1/Phone-complete.7z.002

    Verify checksums

        sha256sum --check "${HOME}/backup-sony_xa1/Phone-complete.7z.sha256sums"

        /home/laptop/backup-sony_xa1/Phone-complete.7z.001: OK
        /home/laptop/backup-sony_xa1/Phone-complete.7z.002: OK

    Upload the archives with the checksums to the cloud, respecting the 4GB file limit e.g. on Terabox.

    Afterwards delete the `Phone-complete.7z.*` files and all directories inside `"${HOME}/backup-sony_xa1/Phone-complete/"` to save space on the drive.

        rm -rf "${HOME}"/backup-sony_xa1/Phone-complete.7z.*
        rm -rf "${HOME}"/backup-sony_xa1/Phone-complete/

    Notes on `Terabox` limitations:
    - Max size for uploaded file: 4GB
    - Max number of files uploaded at once: 500
    - The download of multi-file has an upper limit of 500MB, while the download of a single file is not limited

1. sync git repos - `git_manage_all_repositories`

1. Check the status of every repository

        date && time "${HOME}/git/kyberdrb/git_manage_all_repositories/git_status_all.sh" "${HOME}/git/" && date

    Commit & Push all changes in all modified repositories one-by-one and manually, to have things under control

1. After pushing all changes, check the status again, to make sure all repositories are in consistent and up-to-date state

        date && time "${HOME}/git/kyberdrb/git_manage_all_repositories/git_status_all.sh" "${HOME}/git/" && date

1. Pull all changes from the upstream for all repositories to sync changes to the local machine for files in such repositories that I usually update online via GitHub editor

        date && time "${HOME}/git/kyberdrb/git_manage_all_repositories/git_pull_all.sh" "${HOME}/git/" && date

1. `clean_pacman_cache_dir` - to reduce the backup size for Clonezilla as much as possible

    1. Build it

            date && time "${HOME}/git/kyberdrb/clean_pacman_cache_dir/build-release.sh" && date
    
    1. Make an overview of the packages

            date && time "${HOME}/git/kyberdrb/clean_pacman_cache_dir/cmake-build-release/clean_pacman_cache_dir" && date

    1. Check the logs with following command. The program will tell you where the logs are. For example:
    
                less "${HOME}/.config/cpmcd/logs/2022_10_10-14_23_52.log"
    
    1. Now run the program again, but with elevated priviledges, to allow moving files:

            date && time sudo "${HOME}/git/kyberdrb/clean_pacman_cache_dir/cmake-build-release/clean_pacman_cache_dir" && date

        Check the logs again, if you want to see what files had been moved.
        
            less "${HOME}/.config/cpmcd/logs/2022_10_10-14_38_09.log"

    1. Delete the package files directory:

        First, check free space with
        
            df -h
            
        Delete the package files directory

            date && time sudo rm -rf "/var/cache/pacman/pkg/PACKAGE_FILES_FOR_VERSIONS_OTHER_THAN_LOCALLY_INSTALLED/" && date

        Gained free space on disk, thus less time during cloning. Check again with
        
            df -h

1. `clonezilla_bootable_uefi_usb_creator` - backup entire harddrive by clonning

    1. Execute command

            "${HOME}/git/kyberdrb/clonezilla_bootable_uefi_usb_creator/make_clonezilla_usb.sh"
            
        Use commands from help to make an overview and select the USB drive name that you want to install Clonezilla onto.

    1. Create the UEFI bootable USB drive with Clonezilla

            date && time "${HOME}/git/kyberdrb/clonezilla_bootable_uefi_usb_creator/make_clonezilla_usb.sh" sdb && date

    After the USB had been prepared, close up all work, reboot, invoke the boot menu and select the USB drive with Clonezilla with an UEFI boot mode. I like to do a disk clone for immediately swappable drive.

## Update suite - `update_arch`

1. Update all nonignored packages

        date && time "${HOME}/git/kyberdrb/update_arch/update_arch.sh" && date

1. Reboot
1. Check functionalities
1. After successful boot and functionality check, update the rest

        date && time "${HOME}/git/kyberdrb/update_arch/update_all_installed_ignored_packages.sh" && date

1. Reboot
1. Check functionalities

## Sources

- https://www.linuxtechi.com/7zip-compression-tool-linux-terminal/
- https://duckduckgo.com/?q=7z+strongest+compression&ia=web
- https://stackoverflow.com/questions/29070855/get-maximum-compression-from-7zip-compression-algorithm
- https://superuser.com/questions/432025/different-compression-methods-in-7zip-which-is-best-suited-for-what-task
- file:///usr/share/doc/p7zip/DOC/MANUAL/cmdline/switches/method.htm#Deflate_FastBytes
- https://duckduckgo.com/?q=7z+solid+mode+archive&ia=web&iax=qa
- https://stackoverflow.com/questions/42399926/is-7zip-faster-archiving-solid-or-not-solid#42426924
- https://peazip.github.io/what-is-solid-compression.html
- https://duckduckgo.com/?q=dictionary+size+7z&ia=web
- https://superuser.com/questions/616785/how-does-dictionary-size-affect-compression
- https://duckduckgo.com/?q=7z+split+chunk+archive+cmd&ia=web
- https://superuser.com/questions/184557/how-to-create-multipart-7zip-file-in-linux
- https://duckduckgo.com/?q=7z+-v+volume+part+extract&ia=web
- https://duckduckgo.com/?q=7z+extract+multi+part+archive&ia=web
- https://www.toolfarm.com/knowledge-base/licensing/how-to-extract-multi-part-archive-files/
- https://duckduckgo.com/?q=7z+extract+one+specific+file&ia=web
- https://unix.stackexchange.com/questions/460615/extracting-a-specific-file-from-an-archive-using-7-zip
