react-native-dev-env
====================

Local development environment for react-native inside docker.

Initially copied from of https://github.com/elviejokike/docker-hub/tree/master/react-native-android (Big thanks to elviejokike)
Added some more android dependencies which we need and udev rules for most devices

Example usage
--------------------

Create file `docker-compose.yml` in your react-native app with the following contents:
``` yaml
# docker-compose.yml
packager:
    build: .
    dockerfile: docker/Dockerfile
    net: host
    working_dir: /app
    privileged: true
    volumes:
     - "./:/app"
     - ./gradle_cache:/root/.gradle
     - "/dev:/dev"
     - "/dev/bus/usb:/dev/bus/usb"
    command: |
     bash -c "
     npm install
     adb kill-server
     adb reverse tcp:8081 tcp:8081
     react-native start &
     react-native run-android
     adb logcat *:S ReactNative:V ReactNativeJS:V
     "
```

Then just run `docker-compose up -d` inside your project folder.
Note that you need to accept the computer at your android device after the first run.
`adb reverse` requires android >= 5.0.

Additional tricks
---------------------

As the `net` mode is set to `host`, if you have API's or services running at your host, you can access them from your emulator or android device by adding extra `adb reverse tcp:12001 tcp:12001`.
