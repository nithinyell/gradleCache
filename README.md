```
#!/bin/bash
set -e

# Constants
NDK_VERSION=26.1.10909125
NDK_FOLDER=android-ndk-r26d
NDK_ZIP=${NDK_FOLDER}-darwin.zip
NDK_URL="https://dl.google.com/android/repository/${NDK_ZIP}"

# Place NDK inside android_sdk_root for side-by-side detection
SDK_ROOT="$BUILD_SOURCESDIRECTORY/android/android_sdk_root"
NDK_ROOT="$SDK_ROOT/ndk"
INSTALL_DIR="$NDK_ROOT/$NDK_VERSION"
ZIP_PATH="$NDK_ROOT/$NDK_ZIP"

echo "Target install dir: $INSTALL_DIR"

# Skip if already installed
if [ -d "$INSTALL_DIR" ]; then
  echo "✅ Cached NDK already exists at $INSTALL_DIR, skipping download"
  export ANDROID_NDK_HOME="$INSTALL_DIR"
  echo "##vso[task.setvariable variable=ANDROID_NDK_HOME]$ANDROID_NDK_HOME"
  exit 0
fi

# Download and unzip
mkdir -p "$NDK_ROOT"
curl -L -o "$ZIP_PATH" "$NDK_URL"
unzip -q "$ZIP_PATH" -d "$NDK_ROOT"
mv "$NDK_ROOT/$NDK_FOLDER" "$INSTALL_DIR"

# Set ANDROID_NDK_HOME for reference
export ANDROID_NDK_HOME="$INSTALL_DIR"
echo "##vso[task.setvariable variable=ANDROID_NDK_HOME]$ANDROID_NDK_HOME"

# Write ndk.dir for legacy plugins (some still need this)
LOCAL_PROPS="$BUILD_SOURCESDIRECTORY/android/local.properties"
echo "ndk.dir=$INSTALL_DIR" >> "$LOCAL_PROPS"

echo "✅ Installed NDK $NDK_VERSION at $INSTALL_DIR"
```
