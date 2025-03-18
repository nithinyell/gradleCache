import os
import requests

# List of URLs from your Gradle errors
urls = [
    "https://plugins.gradle.org/m2/org/jetbrains/kotlin/kotlin-stdlib/1.9.24/kotlin-stdlib-1.9.24.pom",
    "https://plugins.gradle.org/m2/com/google/code/gson/gson/2.8.9/gson-2.8.9.pom",
    "https://plugins.gradle.org/m2/com/google/guava/guava/31.0.1-jre/guava-31.0.1-jre.pom",
    "https://plugins.gradle.org/m2/com/squareup/javapoet/1.13.0/javapoet-1.13.0.pom"
]

# Set download folder (macOS Downloads path)
download_folder = os.path.expanduser("~/Downloads")

for url in urls:
    file_name = url.split('/')[-1]
    file_path = os.path.join(download_folder, file_name)

    print(f"Downloading {url} ...")
    response = requests.get(url)
    if response.status_code == 200:
        with open(file_path, 'wb') as f:
            f.write(response.content)
        print(f"Saved to {file_path}")
    else:
        print(f"Failed to download {url} with status code {response.status_code}")
