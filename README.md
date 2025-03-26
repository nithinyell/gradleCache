```
#!/bin/bash

set -e

NDK_VERSION=26.1.10909125
NDK_FOLDER=android-ndk-r26d
NDK_ZIP=${NDK_FOLDER}-darwin.zip
NDK_WORK_DIR="$BUILD_SOURCESDIRECTORY/ndk-temp"
INSTALL_DIR="$BUILD_SOURCESDIRECTORY/android/ndk/$NDK_VERSION"
NDK_URL="https://dl.google.com/android/repository/$NDK_ZIP"

mkdir -p "$NDK_WORK_DIR"
curl -L -o "$NDK_WORK_DIR/$NDK_ZIP" "$NDK_URL"
unzip -q "$NDK_WORK_DIR/$NDK_ZIP" -d "$NDK_WORK_DIR"

# Check if folder exists
if [ ! -d "$NDK_WORK_DIR/$NDK_FOLDER" ]; then
  echo "❌ Extracted NDK folder not found!"
  ls -l "$NDK_WORK_DIR"
  exit 1
fi

mkdir -p "$(dirname "$INSTALL_DIR")"
mv "$NDK_WORK_DIR/$NDK_FOLDER" "$INSTALL_DIR"

export ANDROID_NDK_HOME="$INSTALL_DIR"
echo "##vso[task.setvariable variable=ANDROID_NDK_HOME]$ANDROID_NDK_HOME"

echo "✅ NDK $NDK_VERSION installed at $INSTALL_DIR"


```
