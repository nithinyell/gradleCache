```
void apply(Gradle gradle) {
        gradle.allprojects { project ->
            project.repositories.clear()
            project.repositories {
                maven {
                    url "https://bmostaging.jfrog.io/artifactory/olbbmobile-privatedependencies"
                    credentials {
                        username = System.getenv("ARTIFACTORY_SAAS_ID")
                        password = System.getenv("ARTIFACTORY_SAAS_TOKEN_ENV")
                    }
                }
            }

            project.buildscript.repositories.clear()
            project.buildscript.repositories {
                maven {
                    url "https://bmostaging.jfrog.io/artifactory/olbbmobile-privatedependencies"
                    credentials {
                        username = System.getenv("ARTIFACTORY_SAAS_ID")
                        password = System.getenv("ARTIFACTORY_SAAS_TOKEN_ENV")
                    }
                }
            }
        }
    }
```
