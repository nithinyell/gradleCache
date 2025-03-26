```
- script: |
    echo "Check if NDK path exists:"
    echo "$(Build.SourcesDirectory)/android/ndk/26.1.10909125"
    ls -la $(Build.SourcesDirectory)/android/ndk/26.1.10909125
  displayName: 'Debug: Confirm NDK folder before caching'
```

```
- task: Cache@2
  condition: and(always(), exists('$(Build.SourcesDirectory)/android/ndk/26.1.10909125'))
  displayName: '💾 Save NDK to Cache'
  inputs:
    key: 'ndk | "$(Agent.OS)" | 26.1.10909125'
    path: '$(Build.SourcesDirectory)/android/ndk/26.1.10909125'

```
