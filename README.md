# IBS - Integrated Backup Suite

The complete mainenance guide with tools for sustainable and automated Arch Linux usage and adminstration

1. backup android apps - `backup_and_restore_android_apps`

        "${HOME}/git/kyberdrb/Android_tutorials/backup_and_restore_android_apps/backup_apps.sh" "${HOME}/backup-sony_xa1/apps/"

1. find duplicates in the directory with backed up apps - `duplicate_finder`

        "${HOME}/git/kyberdrb/duplicate_finder/build-release.sh"
        "${HOME}/git/kyberdrb/duplicate_finder/cmake-build-release/duplicate_finder" "${HOME}/backup-sony_xa1/apps/"

    Then delete the entire directory `DUPLICATE_FILES` in the `apps` directory.

        rm -rf "${HOME}/backup-sony_xa1/apps/DUPLICATE_FILES/"

1. compress the `apps` directory

        date && time 7z a -t7z -m0=lzma2 -mx=9 -mfb=273 -md=64m -ms=on apps.7z "${HOME}/backup-sony_xa1/apps/" && date

    Then upload the archive to the cloud, replacing current backup.

1. backup android browser tabs - `backup_and_restore_browser_tabs`

        "${HOME}/git/kyberdrb/Android_tutorials/backup_and_restore_browser_tabs/backup_tabs-Via_browser_by_Tu_Yafeng.sh"

1. move contents of usual text files with the computer
1. backup contacts from the `Contacts` app into `vcf` format

1. `Android_tutorials` - backup android files...

        cd "${HOME}"
        rm -rf "${HOME}/backup-sony_xa1/Phone/*"
        date && time adb pull /storage/sdcard0/. "${HOME}/backup-sony_xa1/Phone/" && date

    ...compress them to an archive...

        date && time 7z a -t7z -m0=lzma2 -mx=9 -mfb=273 -md=64m -ms=on -v4g Phone.7z "${HOME}/backup-sony_xa1/Phone/" && date

    ...then upload the archive to the cloud, part-by-part, respecting the 4GB file limit e.g. on Terabox.  

    Check the multipart archive with `7z l Phone.7z.001 | less`

    Afterwards delete the `Phone.7z.*` files and all directories inside `"${HOME}/backup-sony_xa1/Phone/"` to save space on the drive.

        find "${HOME}" -mindepth 1 -maxdepth 1 -name "Phone.7z.*"
        find "${HOME}" -mindepth 1 -maxdepth 1 -name "Phone.7z.*" -exec gio trash "{}" \;

        find "${HOME}/backup-sony_xa1/Phone/" -maxdepth 1 -mindepth 1 -type d
        find "${HOME}/backup-sony_xa1/Phone/" -maxdepth 1 -mindepth 1 -type d -exec gio trash "{}" \;

        gio trash apps_and_multi_apps.7z

    Notes on Terabox limitations:
    - Max size for uploaded file: 4GB
    - Max number of files uploaded at once: 500
    - The download of multi-file has an upper limit of 500MB, while the download of a single file is not limited

1. sync git repos - `git_manage_all_repositories`

1. Check the status of every repository

        "${HOME}/git/kyberdrb/git_manage_all_repositories/git_status_all.sh" "${HOME}/git/"

        Commit & Push all changes in all modified repositories one-by-one and manually, to have things under control

1. After pushing all changes, check the status again, to make sure all repositories are in consistent and up-to-date state

        "${HOME}/git/kyberdrb/git_manage_all_repositories/git_status_all.sh" "${HOME}/git/"

1. Pull all changes from the upstream for all repositories to sync changes to the local machine for files in such repositories that I usually update online via GitHub editor

        "${HOME}/git/kyberdrb/git_manage_all_repositories/git_pull_all.sh" "${HOME}/git/"

1. `clean_pacman_cache_dir` - to reduce the backup size for Clonezilla as much as possible

    1. Build it

            "${HOME}/git/kyberdrb/clean_pacman_cache_dir/build-release.sh"
    
    1. Make an overview of the packages

            "${HOME}/git/kyberdrb/clean_pacman_cache_dir/cmake-build-release/clean_pacman_cache_dir"

    1. Check the logs. The program will tell you where they are.
    1. Now run the program again.

            sudo "${HOME}/git/kyberdrb/clean_pacman_cache_dir/cmake-build-release/clean_pacman_cache_dir"

        Check the logs again, if you want to see what just happened.

    1. Delete the package files

            sudo rm -rf "/var/cache/pacman/pkg/PACKAGE_FILES_FOR_VERSIONS_OTHER_THAN_LOCALLY_INSTALLED/"

        Gained free space on disk, thus less time during cloning.

1. `clonezilla_bootable_uefi_usb_creator` - backup entire harddrive by clonning

    1. Execute command

            lsblk
    
    1. Look at the output of the command

    1. Insert a USB drive the Clonezilla will boot from

    1. Execute command

            lsblk

        Look at the output of the command again and see what entries had been added - the added entry is the USB drive. Let's say it's listed under the name 'sdb'

    1. Create the UEFI bootable USB drive with Clonezilla

            "${HOME}/git/kyberdrb/clonezilla_bootable_uefi_usb_creator/make_clonezilla_usb.sh" sdb

    After the USB had been prepared, close up all work, reboot, invoke the boot menu and select the USB drive with Clonezilla with an UEFI boot mode. Then continue as usual.

## Update suite - `update_arch`

1. Update all nonignored packages

        "${HOME}/git/kyberdrb/update_arch/update_arch.sh"

1. Reboot
1. Check functionalities
1. After successful boot and functionality check, update the rest

        "${HOME}/git/kyberdrb/update_arch/update_all_installed_ignored_packages.sh"

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
