# คู่มือติดตั้ง React Native บน Windows 10 (Android)

## 1. Git

ดาวน์โหลด: https://git-scm.com/downloads/win

ติดตั้ง: - Next ไปเรื่อย ๆ - เลือก **Git from the command line** - Finish

ตรวจสอบ

``` bash
git --version
```

------------------------------------------------------------------------

## 2. Node.js (LTS)

ดาวน์โหลด: https://nodejs.org/

ติดตั้งค่าเริ่มต้นทั้งหมด

ตรวจสอบ

``` bash
node -v
npm -v
```

------------------------------------------------------------------------

## 3. OpenJDK 17

ดาวน์โหลด: https://adoptium.net/temurin/releases/?version=17

เลือก - Windows - x64 - MSI

ตรวจสอบ

``` bash
java -version
```

------------------------------------------------------------------------

## 4. Android Studio

ดาวน์โหลด: https://developer.android.com/studio

ติดตั้ง - Android SDK - Android SDK Platform - Android SDK Platform Tools -
Android SDK Build Tools - Android Virtual Device

หลังติดตั้ง เปิด **SDK Manager** และติดตั้ง - Android SDK Platform (API
ล่าสุดที่รองรับ) - Android SDK Build-Tools - Android SDK Platform-Tools -
Android SDK Command-line Tools (latest)

------------------------------------------------------------------------

## 5. ตั้งค่า Environment Variables

เพิ่มตัวแปร

    ANDROID_HOME

เช่น

    C:\Users\<ชื่อผู้ใช้>\AppData\Local\Android\Sdk

เพิ่มใน PATH

    %ANDROID_HOME%\platform-tools
    %ANDROID_HOME%\emulator
    %ANDROID_HOME%\cmdline-tools\latest\bin

ตรวจสอบ

``` bash
adb version
sdkmanager --list
```

------------------------------------------------------------------------

## 6. VS Code

ดาวน์โหลด: https://code.visualstudio.com/

ส่วนขยายที่แนะนำ - ESLint - Prettier - React Native Tools - GitLens - Error
Lens - Path Intellisense

------------------------------------------------------------------------

## 7. สร้างโปรเจกต์

``` bash
npx @react-native-community/cli init MyApp
cd MyApp
npm install
```

------------------------------------------------------------------------

## 8. เปิด Developer Options บนมือถือ

-   แตะ Build Number 7 ครั้ง
-   เปิด USB Debugging
-   เชื่อมต่อสาย USB
-   อนุญาต USB Debugging

ตรวจสอบ

``` bash
adb devices
```

------------------------------------------------------------------------

## 9. รันแอป

``` bash
npx react-native run-android
```

------------------------------------------------------------------------

## เครื่องมือแนะนำเพิ่มเติม

-   7-Zip https://www.7-zip.org/
-   Postman https://www.postman.com/downloads/
