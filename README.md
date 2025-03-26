```
#!/bin/bash

set -e

NDK_VERSION=26.1.10909125
NDK_FOLDER=android-ndk-r26d
NDK_ZIP=${NDK_FOLDER}-darwin.zip
NDK_URL="https://dl.google.com/android/repository/${NDK_ZIP}"
INSTALL_DIR="${ANDROID_HOME}/ndk/${NDK_VERSION}"

echo "Downloading NDK from $NDK_URL"
mkdir -p "$ANDROID_HOME/ndk"

curl -L -o /tmp/$NDK_ZIP "$NDK_URL"
unzip -q /tmp/$NDK_ZIP -d /tmp/

mv /tmp/$NDK_FOLDER "$INSTALL_DIR"

# Set ANDROID_NDK_HOME
export ANDROID_NDK_HOME="$INSTALL_DIR"
echo "##vso[task.setvariable variable=ANDROID_NDK_HOME]$ANDROID_NDK_HOME"

echo "NDK $NDK_VERSION installed at $INSTALL_DIR"

```
