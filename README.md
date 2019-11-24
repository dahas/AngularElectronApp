# AngularElectronApp

This is a step by step description of how to create a distributable desktop app with Angular and Electron.

## 1. Basic Requirements

- NodeJS
- Angular Framework and CLI

## 2. Create Angular Project

Create or open a folder of your Angular projects. Launch a Shell in that folder and run the following commands:

`$ ng new ProjectName`  
`$ cd ProjectName`  
`$ ng serve --open` (Run app in browser)  

Minimize the browser window and return to the Shell. Press Ctrl + C and shut down the server.

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
If you run `ng serve` again and open the developer toolbar in the browser you will see `IS_ELECTRON: false` printed in the console. This is because the Electron Api is not accessible in the web browser.

## 6. Distribute with Electron

Download a prebuild distribution from here: https://github.com/electron/electron/releases. Choose the latest stable release (no Beta) for your operating system. E.g. for Windows 64Bit choose [electron-v6.1.5-win32-x64.zip](https://github.com/electron/electron/releases/download/v6.1.5/electron-v6.1.5-win32-x64.zip).

Create or open a folder of your Electron projects. 




# rcedit
With this tool you can edit resources and detailed infos attached to a windows executable (exe) file.

## Download
https://github.com/electron/rcedit/releases

## Usage

#### Set icon:
```
$ rcedit "D:\path\to\app.exe" --set-icon "D:\path\to\app.ico"
```

#### Set description:
```
$ rcedit "D:\path\to\app.exe" --set-version-string "FileDescription" "Text ..."
```

#### Set file version:
```
$ rcedit "D:\path\to\app.exe" --set-file-version "x.x.x.x"
```

#### Set product name:
```
$ rcedit "D:\path\to\app.exe" --set-version-string "ProductName" "Text ..."
```

#### Set product version:
```
$ rcedit "D:\path\to\app.exe" --set-product-version "x.x.x"
```

#### Set copyright:
```
$ rcedit "D:\path\to\app.exe" --set-version-string "LegalCopyright" "Copyright (C) 2019 Name. All rights reserved."
```

#### Set original file name:
```
$ rcedit "D:\path\to\app.exe" --set-version-string "OriginalFilename" "app.exe"
```
