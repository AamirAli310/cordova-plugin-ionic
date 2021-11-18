## Steps to install the Plugin (New/Existing) Apps & Performing Live Updates Testing
## Example (On Existing or New Ionic-Cordova [Angular] Apps):
===============================================================================
## My Environment state:
node -v: v16.0.0
ionic -v: 6.18.0
cordova -v: 8.0.0
npm -v: 8.1.2
Ionic:
   Ionic CLI                     : 6.18.0 (/Users/aali/.nvm/versions/node/v16.0.0/lib/node_modules/@ionic/cli)
   Ionic Framework               : @ionic/angular 5.8.5

Cordova:
   Cordova CLI       : 8.0.0
   Cordova Platforms : ios 4.5.5
   Cordova Plugins   : cordova-plugin-ionic 5.5.1, cordova-plugin-ionic-webview 5.0.0

Utility:
   cordova-res : 0.15.3
   native-run  : 1.5.0

System:
   Android SDK Tools : 26.1.1 (/Users/aali/Library/Android/sdk)
   NodeJS            : v16.0.0 (/Users/aali/.nvm/versions/node/v16.0.0/bin/node)
   npm               : 8.1.2
   OS                : macOS Big Sur
   Xcode             : Xcode 13.1 Build version 13A1030d
   

## Step-1 (Only in Fresh App Case)
- ionic start TestApp blank
- Selected framework: Angular
-  > After blank App download completion: 


## Step-2 (Integrate Cordova) (If Step-1 performed)
- ionic integration disable capacitor
- ionic integration enable cordova


## Step-3 (Live Update Starts): Common for both (New | Existing) App 
- ionic cordova plugin add https://github.com/AamirAli310/cordova-plugin-ionic.git --variable APP_ID="{Your-App-Name}" --variable CHANNEL_NAME="{Environment-Mode}" --variable UPDATE_API="{Your-Server-Name}"
- APP_ID: Same ID will be sent by Plugin to server to check for new version. 
- CHANNEL_NAME: It's mode of deployment.... Normally it is Production/Staging, You can use whatever suitable to you but it has to manage on the Server end accordingly.
- UPDATE_API: It is your own Server host name like http://www.google.com, http://localhost:3000, http://api.myserver.com
- For Testing cycle you may create own localhost server. Check my repositiry <a href="https://github.com/AamirAli310/MyLocalServer/blob/main/README.md" rel="follow">'Local Server in NodeJS'</a>
- If you're using my repo: MyLocalServer then use App_ID: 'TestPluginApp', CHANNEL_NAME: 'UAT', UPDATE_API: "http://localhost:3000'

- Noted: If you forget to pass above variable, no issue, The plugin will install with default value which you can change in config.xml/package.json

## Step-3 Example-  
- ionic cordova plugin add https://github.com/AamirAli310/cordova-plugin-ionic.git --variable APP_ID="TestPluginApp" --variable CHANNEL_NAME="UAT" --variable UPDATE_API="http://localhost:3000"
- On successful plugin installation, you should able to see below lines on your console:
- Adding cordova-plugin-ionic to package.json
- Saved plugin info for "cordova-plugin-ionic" to config.xml

## Step-4 (Add Platform, If Step-1 performed)
- ionic cordova platform add ios

## Step-5 (After Plugin Installed successfully)
- Check config.xml and you should able to see your provided information under cordova-plugin-ionic plugin tag. Now time to prepare App with our own server's API info
- ionic cordova prepare ios

## Step-6 (Run App on Simulator [If Server is running on Local machine, then it's best option to test])
- Using Xcode to run the project, Go to Project > Platforms > iOS> Open MyApp.xcworkspace file.
- Select simulator in the target-device and press run button .
- If Apps loaded landing page of the app or stop on splash-screen, don't worry.
- Check on console, You should able to see below message : 
- Got Native app preferences: {
    appId = YOUR-APP-ID;
    binaryVersion = "0.0.1";
    binaryVersionCode = "0.0.1";
    binaryVersionName = "0.0.1";
    channel = YOUR-GIVEN-CHANNEL-NAME;
    disabled = 0;
    host = "YOUR-GIVEN-SERVER-URL";
    maxVersions = 2;
    minBackgroundDuration = 30;
    updateMethod = auto;
}
- If above output has been found that means all is good to go, Now just need to work on server-end to manage the versions against each client's call.
- Noted: 
-   API-End Point which will check for new version availability is: (I made single API Call which can serve all the app's of a company)
-   http://localhost:3000/apps/check-updates

## Step-7: Deploy your build on your server/local-matchine.
-  Make sure the URL field in the server response must match the directory names & structure as per provided Input data (App_ID, Channel_Name, app_version_id)
-  Now We need to copy www folder and place it on your server and put it in an structural directory.
-  If You're running my local server respository then deployment path will be: Path_Of_MyLocalServer_Directory > AATests > apps > TestPluginApp(Rename it as per your ) > UAT: 
-  Paste www from your app Create new Directory here as 1.0.2
-   For example Directory structure on server will be like:
-   -   server-path:/apps
-                 - TestPluginApp (make sure it match your given App ID)"
-                       - Production
-                         - 1.0.0                       
-                       - UAT
-                         - 1.0.0 (Rename www to 1.0.0)
-                         - 1.0.1


- For multi-platforms : 
-   -  server-path:/apps
-                 - TestPluginApp (make sure it match your given App ID)"
-                     - iOS
-                       - Production
-                         - 1.0.0                       
-                       - UAT
-                         - 1.0.0 (Rename www to 1.0.0)
-                         - 1.0.1                         
-                     - Android
-                       - Production
-                         - 1.0.0                       
-                       - UAT
-                         - 1.0.0 (Rename www to 1.0.0)
-                         - 1.0.1   
-   Noted: Your www must have pro-manifiest.json file.


## Step-8 (Server Side: In my case it's 'Localhost')
- A Server is created using Nodejs which may help you to test on your own machine. <a href="https://github.com/AamirAli310/MyLocalServer/blob/main/README.md" rel="follow">'Run Local Server'</a>

**- http://localhost:3000/apps/check-updates** (Plugin will make a hit on this call with some input data:)

- Query Input Data:
- Req Body:  {
  channel_name: 'UAT',
  app_id: 'TestPluginApp',
  app_version_id: null,
  build: null,
  device: {
    binary_version: '0.0.1',
    device_id: '4CD188A7-0AF0-4EAE-AA62-86EC08A66235',
    platform: 'ios',
    platform_version: '15.0',
    snapshot: null,
    build: null
  },
  plugin_version: '5.5.1',
  manifest: true
}
- Response should be based upon Input app_id, channel and app_version_id (I ignored build ID but if someone wants to manage builds too, so can add build in comparision on server end)
- Response Example (If New Version Available):
{
    "data": {
        "url": "https://localhost:3000/AATests/apps/**TestPluginApp**/**UAT**/**1.0.3**/pro-manifest.json",
        "incompatibleUpdateAvailable": false,
        "build": 1003,
        "partial": false,
        "snapshot": "1.0.3",
        "compatible": true,
        "available": true
    }
}

**Noted:**
- url field contains pro-manifiest.json (1.0.3 == www) which will be used for comparision on client-end (Plugin stuff) to download changed files for 1.0.3 version (un-changed files will be coppied by plugin auto-metically in this new version).
- inside URL field after apps, APP_ID/CHANNEL_NAME/NEW_VERSION_ID (Your Server will decide after comparing app_version_id field from Input Data)


- After app reload function execution finished, plugin will again make check-updates call and this time input & output of will be like this:
- http://localhost:3000/apps/check-updates
- Input:
- Req Body:  {
  channel_name: 'UAT',
  app_id: 'TestPluginApp',
  app_version_id: '1.0.3',
  build: 1000,
  device: {},
  manifest: true
}

- Response:
{
    "data": {
        "url": "",
        "incompatibleUpdateAvailable": false,
        "build": null,
        "partial": false,
        "snapshot": null,
        "compatible": false,
        "available": false
    }
}

--- Now our latested deployed version is Up & Running on the device--- (Y)
