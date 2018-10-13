# Run React Native Android app on remote emulator

If you want to write and compile React Native Android application on one machine and debug it remotely on the emulator launched on another follow the instructions below:

## Preparation

First of all you need to install SSH server on your remote machine.

### Ubuntu

You can use [OpenSSH](https://help.ubuntu.com/lts/serverguide/openssh-server.html.en).

`sudo apt install openssh-server`

### Windows 

You can install OpenSSH on Windows following [this link](https://winscp.net/eng/docs/guide_windows_openssh_server).

## Connection

Now you must connect local machine to remote server using SSH tunnel.

The emulator listens on two TCP ports per instance: 5554 for the telnet interface and 5555 for control communication with tools like DDMS. So you could probably get away with only forwarding port 5555. Each subsequent emulator takes the next available even+odd port number tuple.

Note that you need to enter the ip address of emulator running on remote server instead of `emulator-ip` and also remote machine username and hostname instead of `myuser` and `remote-server`.

### Ubuntu

On the local machine try:

`ssh -NL 5554:emulator-ip:5554 -L 5555:emulator-ip:5555 myuser@remote-server`

### Windows

Follow [this link](https://intranet.cs.hku.hk/csintranet/contents/technical/howto/putty-portforward.jsp) instructions and add two source ports and destinations below:

```
Source port: 5554
Destination: emulator-ip:5554

Source port: 5555
Destination: emulator-ip:5555
```

then connect to remote server.

## ADB

The emulator tries to notify a local adb server at startup; hence you need to restart adb in order for it to probe the local 5554+ ports.

`adb kill-server; adb devices`

`adb devices` shows a new emulator — emulator-5554 — and you can use it as if it is running on your local machine.

## Running

Finally run:

`react-native run-android`

the app should be installed on emulator, but you may face an error:

```
error: more than one device/emulator
Could not run adb reverse: Command failed: path/to/sdk/adb -s emulator-5554 reverse tcp:8081 tcp:8081
```

you need to specify your debug host IP and port in app's Developer menu -> Dev settings -> Debug server host & port, that is your local machine ip address and 8081 for port.

> Note that after APK installation you don't need to do any of these steps. You can just start the npm server: `npm start` in the project directory of your local machine, run the app on the emulator in remote machine and then reload.

if there was another error:

`Exception in thread "Device List Monitor" java.lang.NullPointerException`

try:

`react-native run-android --deviceId <remoteEmulatorDeviceId>`
