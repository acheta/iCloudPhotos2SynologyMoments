# icloudPhotos2SynologyMoments

I'd like to share my experience and solution of issues when importing files from iCloud Photos to Synology Moments in Windows 10.

**Issues to overcome:**
* At some point backup of all photos from my iPhone to Synology Moments failed due to allegedly corrupted file or other error so I had to find a way how to transfer all the photos to Synology Moments manually.
* Photos and videos stored in iCloud from Whatsapp and FB Messenger lack the EXIF metadata so there is issue when downloaded to Synology Moments. The pictures appear all to be from the date of when they were copied to NAS and there is no date/time ordering whatsoever - absolutely impossible to find anything reasonably in the timeline view in Synology Moments.

## Step 1 - Download from iCloud to local hardrive
This is possible in two reasonable ways from my point of view.

1. Login to icloud.com in the browser and manually select maximum 1000 files at once and download "originals" by clicking on the download button in top right corner. This is quite tedious work when you have thousands of files in iCloud to download. Anyway files are downloaded in x zip files which can be then extracted to one folder.
1. Install [iCloud for Windows](https://support.apple.com/en-us/HT204283) and let all your pictures and videos sync to your PC. Sounds simple but there are two remarks:
    * iCloud photos are listed in your `user-folder\Pictures\iCloud Photos\Photos` as online only files meaning they are not downloaded until opened. To download them upfront you need to select all files, right click and from the context menu select "Always keep on this device" - this will initiate the download.
    * Downloading files with iCloud client for Windows is incredibly slow and unreliable - [this is known fact](https://discussions.apple.com/thread/250890633) from many complaits.
  
    Either way you decide to download pictures
  
