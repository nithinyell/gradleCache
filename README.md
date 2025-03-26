```
#!/bin/bash

set -e

NDK_VERSION=26.1.10909125
NDK_FOLDER=android-ndk-r26d
NDK_ZIP=${NDK_FOLDER}-darwin.zip
NDK_URL="https://dl.google.com/android/repository/$NDK_ZIP"

# Use writable workspace directory
NDK_BASE_DIR="$BUILD_SOURCESDIRECTORY/android/ndk"
NDK_WORK_DIR="$NDK_BASE_DIR/tmp"
INSTALL_DIR="$NDK_BASE_DIR/$NDK_VERSION"

echo "Downloading NDK $NDK_VERSION from $NDK_URL"
echo "NDK will be installed to $INSTALL_DIR"

# Use same strategy as your get_jdk.sh — no mkdir if unnecessary
rm -rf "$NDK_WORK_DIR"
rm -rf "$INSTALL_DIR"

curl -L -o "$NDK_WORK_DIR.zip" "$NDK_URL"
unzip -q "$NDK_WORK_DIR.zip" -d "$NDK_WORK_DIR"

# Validate extracted directory exists
if [ ! -d "$NDK_WORK_DIR/$NDK_FOLDER" ]; then
  echo "❌ Extracted folder not found in $NDK_WORK_DIR"
  ls -l "$NDK_WORK_DIR"
  exit 1
fi

mv "$NDK_WORK_DIR/$NDK_FOLDER" "$INSTALL_DIR"

# Set ANDROID_NDK_HOME
export ANDROID_NDK_HOME="$INSTALL_DIR"
echo "##vso[task.setvariable variable=ANDROID_NDK_HOME]$ANDROID_NDK_HOME"
echo "✅ NDK $NDK_VERSION installed at $INSTALL_DIR"



```
