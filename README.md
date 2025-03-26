```
#!/bin/bash

set -e

NDK_VERSION=26.1.10909125
NDK_FOLDER=android-ndk-r26d
NDK_ZIP=${NDK_FOLDER}-darwin.zip
NDK_URL="https://dl.google.com/android/repository/$NDK_ZIP"
INSTALL_DIR="$BUILD_SOURCESDIRECTORY/android/ndk/$NDK_VERSION"

echo "📦 Downloading NDK $NDK_VERSION..."
echo "👉 Target install dir: $INSTALL_DIR"

# Clean any previous runs
rm -rf "$INSTALL_DIR"
rm -f "$BUILD_SOURCESDIRECTORY/$NDK_ZIP"

# Download zip directly into the source directory
curl -L -o "$BUILD_SOURCESDIRECTORY/$NDK_ZIP" "$NDK_URL"

# Unzip right where we need it
unzip -q "$BUILD_SOURCESDIRECTORY/$NDK_ZIP" -d "$BUILD_SOURCESDIRECTORY/android/ndk"

# Rename the extracted folder to match versioned install dir
mv "$BUILD_SOURCESDIRECTORY/android/ndk/$NDK_FOLDER" "$INSTALL_DIR"

# Set ANDROID_NDK_HOME
export ANDROID_NDK_HOME="$INSTALL_DIR"
echo "##vso[task.setvariable variable=ANDROID_NDK_HOME]$ANDROID_NDK_HOME"

echo "✅ NDK $NDK_VERSION installed at $INSTALL_DIR"

```
