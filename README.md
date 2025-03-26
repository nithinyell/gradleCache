```
- script: |
    if [ -d "$(Build.SourcesDirectory)/android/ndk/26.1.10909125" ]; then
      echo "✅ NDK dir exists"
      echo "##vso[task.setvariable variable=NDK_CACHE_READY]true"
    else
      echo "❌ NDK dir missing"
    fi
  displayName: 'Check if NDK is ready to cache'
```

```
- task: Cache@2
  displayName: '💾 Save NDK to Cache'
  condition: and(always(), eq(variables['NDK_CACHE_READY'], 'true'))
  inputs:
    key: 'ndk | "$(Agent.OS)" | 26.1.10909125'
    path: '$(Build.SourcesDirectory)/android/ndk/26.1.10909125'
```
