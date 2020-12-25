# How to import iCloud Photos to Synology Moments

I'd like to share my experience and solution of issues when importing files from iCloud Photos to Synology Moments in Windows 10. I could not find any comprehensive solution so I had to investigate on my own so this tutorial might save you some time.

**Issues to overcome:**
* At some point backup of all photos from my iPhone to Synology Moments failed due to allegedly corrupted file or other error so I had to find a way how to transfer all the photos to Synology Moments manually.
* Photos and videos stored in iCloud from Whatsapp and FB Messenger lack the EXIF metadata so there is issue when downloaded to Synology Moments. The pictures appear all to be from the date of when they were copied to NAS and there is no date/time ordering whatsoever - absolutely impossible to find anything reasonably in the timeline view in Synology Moments. Even if you manage to keep file's created date the Synology Moments consider the modified date as date of the picture.

**Warning**

Eventhough I try to write this tutorial as most straigth forward as possible please be cautious when typing those commands. You should have some basic experience with Microsoft PowerShell.

## Step 1 - Download files from iCloud to local hardrive
This is possible in two reasonable ways from my point of view.

1. Login to icloud.com in the browser and manually select maximum 1000 files at once and download "originals" by clicking on the download button in top right corner. This is quite tedious work when you have thousands of files in iCloud to download. Anyway the files are downloaded in zip file(s) which can be then extracted to one folder.
1. Install [iCloud for Windows](https://support.apple.com/en-us/HT204283) and let all your pictures and videos to sync to your PC. Sounds simple but there are two remarks:
    * iCloud photos are listed in your `user-folder\Pictures\iCloud Photos\Photos` folder as **online only** files meaning they are not downloaded until opened. To download them upfront you need to select all files, right click and from the context menu select "Always keep on this device" - this will initiate the download.
    * Downloading files with iCloud client for Windows is incredibly slow and unreliable - [this is known fact](https://discussions.apple.com/thread/250890633) from many complaits.
    
    Pictures and videos you are going to upload to Synology Moments should be copied into a different local folder that the iCloud Photos sync folder to protect files from changes in the next step.
    
    I used ROBOCOPY command to download & copy files from iCloud Photos sync folder to a new folder as following:
      * Click on Start & type "powershell" & open listed Windows PowerShell app
      * Navigate to your iCloud photos folder typing command `cd "C:\Users\[your-username]\Pictures\iCloud Photos\Photos"`
      * Copy all files to a new location i.e. `C:\Temp\toMoments` with command `robocopy .\ C:\Temp\toMoments` if the files are not downloaded yet copying will automatically initiate the download but expect it to be extremely slow.
      
## Step 2 - Update datetime attributes of files
Synology Moments app takes last modified date of the file as the date of the picture and so the date has to be changed to same value as file's created date using PowerShell command.

1. Click on Start & type "powershell" & open listed Windows PowerShell app.
1. Navigate to folder with your downloaded files i.e. `C:\Temp\toMoments` in my sample by typing command `cd C:\Temp\toMoments`
1. Copy and paste following command and hit enter:
   ```text
   Get-ChildItem .\* -Recurse | foreach {
     $x = $_.CreationTime
     $new_folder_name = $x.ToString("yyyy-MM")
     $prefix = $x.toString("yyyy-MM-dd_HH-mm_")
     $des_path = ".\$new_folder_name"
     $_.LastWriteTime = $x
     $_.LastAccessTime = $x
     if (-Not(test-path $des_path)){
       new-item -ItemType directory -Path $des_path
     }
     Move-Item $_.fullname "$des_path\$prefix$($_.name)"
   }
   ```
   
   The command does following:
      * changes file's last modification time and last access time to the same value as created time
      * sorts out files into subfolder by months i.e. 2020-01. You can have it sorted differently by changing format in `$new_folder_name = $x.ToString("yyyy-MM")`
      * prefixes the file name with date and time value i.e. 2020-01-01_15-45_IMG_0001.JPG so alphabetic sorting will have it sorted by time too
      
## Step 3 - copy updated files to Synology NAS Moments folder
For this I have my Synylogy Drive folder mapped into network drive `S:`. Use robocopy command to copy files to Synology. **Important:** Robocopy persists the date/time attributes of the copied files. Standard drag&drop copy in windows explorer changes file's time to the time of the copy.

1. Navigate to folder with your updated files i.e. `C:\Temp\toMoments` in my sample by typing command `cd C:\Temp\toMoments`
1. Copy files to Moments folder using robocopy command `robocopy .\ "S:\Drive\Moments\Mobile\iCloud" /S`

## Step 4 - Cleanup and reindex
1. If you have some previous partial uploads from your mobile device or other upload attempts use Synology Drive or File station to remove those files and folders in Moments folder.
1. Deleting or changing files out of Synology Moments' interface may cause some inconsistencies so I recommend to reindex all files in Moments. Open up Synology Moments in the web browser, click on your profile icon in left bottom and navigate to Settings > General tab > Select re-index option which suits you best and click Re-index button.

That's it. Hope someone finds it useful. 

Good luck
  
