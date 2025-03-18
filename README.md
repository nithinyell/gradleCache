```
#!/bin/bash

# List of URLs
urls=(
"https://plugins.gradle.org/m2/org/jetbrains/kotlin/kotlin-stdlib/1.9.24/kotlin-stdlib-1.9.24.pom"
"https://plugins.gradle.org/m2/com/google/code/gson/gson/2.8.9/gson-2.8.9.pom"
"https://plugins.gradle.org/m2/com/google/guava/guava/31.0.1-jre/guava-31.0.1-jre.pom"
"https://plugins.gradle.org/m2/com/squareup/javapoet/1.13.0/javapoet-1.13.0.pom"
)

# Download location (macOS Downloads folder)
download_folder="$HOME/Downloads"

# Loop through and download each file
for url in "${urls[@]}"
do
    echo "Downloading $url ..."
    curl -o "$download_folder/$(basename $url)" "$url"
done

echo "Download completed. Files are saved in $download_folder"
```
