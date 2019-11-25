# AngularElectronApp

This is a complete step by step guide of how to create a distributable Windows desktop app with Angular and Electron. 

## 1. Basic Requirements

- NodeJS
- Angular Framework and CLI

## 2. Create an Angular Project

Choose a folder, that should contain your Angular project. Launch a Shell in that folder and run the following commands:

`$ ng new ProjectName`  
`$ cd ProjectName`  
`$ ng serve --open` (Run app in browser)  

Your app works even though it does not yet contain any useful features. Save their implementation for later. Minimize the browser window and return to the Shell. Press Ctrl + C to shut down the server.

## 3. Install Electron as Dependency

While still in the project directory, run:

`$ npm i electron`  
`$ npm i ngx-electron` (Wrapper for Angular)  

## 4. Add Wrapper to AppModule

In your favourite Editor open `path/to/AngularProjects/ProjectName/src/app/app.module.ts` and import the wrapper module:

```
import { NgxElectronModule } from 'ngx-electron';

@NgModule({
    declarations: [],
    imports: [
      NgxElectronModule
    ],
    bootstrap: []
})
export class AppModule {}
```

## 5. Using the Wrapper as a Service

Open `path/to/AngularProjects/ProjectName/src/app/app.component.ts` and import the wrapper service like shown below:

```
import { ElectronService } from 'ngx-electron';

@Component({
  ...
})
export class AppComponent {
    constructor(private electronSrv: ElectronService) { 
      console.log('IS_ELECTRON:', this.electronSrv.isElectronApp);
    }
}
```
Inject the service the same way into another component, that you will create later to extend your app.

If you run `ng serve` again and open the developer toolbar in the browser you will see `IS_ELECTRON: false` printed in the console. This is because the Electron API is not accessible in the web browser.

## 6. Prepare Electron

- Download a prebuild Electron distribution from here: https://github.com/electron/electron/releases. Choose the latest stable release (no Beta) for your operating system. E.g. for Windows 64Bit choose [electron-v6.1.5-win32-x64.zip](https://github.com/electron/electron/releases/download/v6.1.5/electron-v6.1.5-win32-x64.zip).

- Create a new folder for the Electron version of your project:   
`$ mkdir ProjectName`

- Unzip the content of the archive you just downloaded right into this folder. 

- Open the `resources` subfolder and create a folder named `app` inside of it.  
`$ cd resources`  
`$ mkdir app`

- Inside of `app` create a folder named `dist`. Leave it empty.

- Also create a `packagae.json` file inside of `app`. Set `name` and `version` properties accordingly:
```
{
  "name": "project_name",
  "version": "1.0.0",
  "main": "main.js",
  "scripts": {
    "start": "electron ."
  }
}
```

- Create a second file `main.js` with following content:
```
const { app, BrowserWindow } = require('electron');

let win;

// Allow only a single instance of the app
const gotTheLock = app.requestSingleInstanceLock();

if (!gotTheLock) {
  app.quit()
} else {
  app.on('second-instance', () => {
    if (win) { // Bring running app to front
      if (win.isMinimized()) win.restore()
      win.focus()
    }
  })

  app.on('ready', createWindow)

  app.on('window-all-closed', () => {
    if (process.platform !== 'darwin') {
      app.quit()
    }
  })

  app.on('activate', () => {
    if (win === null) {
      createWindow()
    }
  })

  function createWindow() {
    win = new BrowserWindow({
      show: false, // Hidden until 'ready-to-show'.
      width: 600,
      height: 400,
      title: 'Your title here',
      // icon: __dirname + '/dist/favicon.ico',
      // resizable: false,
      // maximizable: false,
      // transparent: true,
      // frame: false, // Hides the OS window

      webPreferences: {
        nodeIntegration: true
      }
    });

    win.loadFile('dist/index.html'); // The file to launch at start up.
    
    // win.setMenu(null); // Removes the OS menu

    win.once('ready-to-show', () => { win.show() });
    win.on('closed', () => { win = null });
  }
}

```
The `createWindow` function mainly defines the appereance of your app. 

Next you must configure Angular to deploy your app into the `dist` folder of your Electron project.

## 7. Prepare Angular

- In the root folder of your Angular app, open `angular.json`. Search for the `build` section and set the output path, so that it points to the previously created `dist` folder of your Electron project:
```
...
"build": {
  "builder": "@angular-devkit/build-angular:browser",
  "options": {
    "outputPath": "path/to/ElectronProjects/ProjectName/resources/app/dist",
    ...
  }
}
...
```

- Next open `tsconfig.json`. You also find it in the root folder. Change the target EcmaScript Version from es2015 to es5. This step is only necessary if you encounter the "Failed to load module script" error:
```
"compilerOptions": {
  ...
  "target": "es5",
  ...
}
```

- Last but not least set the `base` tag correctly. Open the `index.html` file inside the `src` folder and change the `href` attribute of the `base` tag from "/" to "./":
```
<base href="./">
```

## 8. Deploy your Angular App

By running the `ng build` command the app will be deployed into the `dist` folder of your Electron project.
```
$ ng build
```

If you now run "electron.exe", from within the root folder of your Electron project, you will see your app running in a Windows frame. 

## 9. Individualize "electron.exe"

- Rename `electron.exe` to `projectName.exe`.

Renaming won´t be enough. You surely want to change the icon and the details, that can be seen when right-clicking the exe file, as well. Therefore proceed with the next step.

- Download a small tool called [rcedit](https://github.com/electron/rcedit/releases). With this tool you can edit the resources and infos attached to a Windows executable. Choose the exe for either the 64 or 32 Bit version of your Windows OS.

- Place the file in a folder and rename it to `rcedit.exe`.

- Add the path to `rcedit.exe` as a PATH varaible.

- Now use it as follows:

#### Set icon:
Note: A Windows compliant application icon, that´ll be used as a short-cut, a file type, or embedded in an executable file, must contain at least the following sizes: `256x256px, 48x48px, 32x32px, 16x16px`. With [gimp](https://www.gimp.org/) you can create such an multi-size ico file.
```
$ rcedit "D:\path\to\Electron\projectName\projectName.exe" --set-icon "D:\path\to\your.ico"
```

#### Set description:
```
$ rcedit "D:\path\to\Electron\projectName\projectName.exe" --set-version-string "FileDescription" "Text ..."
```

#### Set file version:
```
$ rcedit "D:\path\to\Electron\projectName\projectName.exe" --set-file-version "x.x.x.x"
```

#### Set product name:
```
$ rcedit "D:\path\to\Electron\projectName\projectName.exe" --set-version-string "ProductName" "Text ..."
```

#### Set product version:
```
$ rcedit "D:\path\to\Electron\projectName\projectName.exe" --set-product-version "x.x.x"
```

#### Set copyright:
```
$ rcedit "D:\path\to\Electron\projectName\projectName.exe" --set-version-string "LegalCopyright" "Copyright (C) 2019 Name. All rights reserved."
```

#### Set original file name:
```
$ rcedit "D:\path\to\Electron\projectName\projectName.exe" --set-version-string "OriginalFilename" "app.exe"
```

## 10. Create a compressed Package with asar

Before this step you usually add functionlity to your app. But for the sake of demonstration, let´s continue ...

- Install `asar` globally:
```
$ npm install -g asar
```

- Inside the `resources` folder of your electron app, run the following command in a Shell:
```
asar pack app app.asar
```

- Remove the `app` folder.

(To unpack an archive you would run `$ asar extract app.asar app`.)

## 11. Create an Installer

InnoSetup is a free and secure Open Source tool to create Windows installer packages.

- Download the basic package [innosetup-6.0.3.exe](http://www.jrsoftware.org/download.php/is.exe?site=2).
- Start InnoSetup and select "Create a new script file using the Script Wizard" in the prompt. Click next.
- Leave "Create empty script file" unchecked. Click next.
- Enter basic information. Click next.
- Change Application folder name. Click next.
- Browse to the directory of your Electron project and select the exe of your app.
- Also add the complete folder of your Electron project in the section below. Confirm to include subfolders.
- Click next until you reach the compiler settings.
- Specify an output folder and change the output file name to `install`.
- Click next until finish.
- Confirm to compile the script now. Optinally you can save the script file.

Now you can install your app and it will appear in the Windows startmenu and/or as a shortcut on the desktop. 

**That should have been everything.**
