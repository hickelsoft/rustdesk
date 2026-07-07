# Patch flutter-build.yml für HickelSOFT

---

(1) Environment Sektion (`env`):

Entfernen:
```yaml
  VERSION: "1.4.9"
  NDK_VERSION: "r28c"
  #signing keys env variable checks
  ANDROID_SIGNING_KEY: "${{ secrets.ANDROID_SIGNING_KEY }}"
  MACOS_P12_BASE64: "${{ secrets.MACOS_P12_BASE64 }}"
  UPLOAD_ARTIFACT: "${{ inputs.upload-artifact }}"
  SIGN_BASE_URL: "${{ secrets.SIGN_BASE_URL }}-2"
```

Ersetzen durch:
```yaml
  UPLOAD_ARTIFACT: "${{ inputs.upload-artifact }}"
  # To make a custom build with your own servers set the below secret values
  RS_PUB_KEY: '${{ secrets.RS_PUB_KEY }}'
  RENDEZVOUS_SERVER: '${{ secrets.RENDEZVOUS_SERVER }}'
  API_SERVER: '${{ secrets.API_SERVER }}'
  RELEASE_PASSWORD: '${{ secrets.RELEASE_PASSWORD }}'
  UPDATER_HMAC_SECRET: '${{ secrets.UPDATER_HMAC_SECRET }}'
```

---

(2) Sektion 64 Bit Windows (`build-for-windows-flutter`):

(2.0) ENTFERNEN:
```yaml
          - {
              target: aarch64-pc-windows-msvc,
              os: windows-11-arm,
              arch: aarch64,
              flutter-arch: arm64,
              vcpkg-triplet: arm64-windows-static,
              # vram is x86/x64-only (NVENC needs CUDA, Intel MediaSDK needs __rdtsc);
              # no NV/Intel/AMD hardware exists on Windows-on-ARM, so vram stays disabled here.
              build-args: "",
            }
```	

(2.1) ENTFERNEN: `name: find Runner.res`

(2.2) ENTFERNEN: `name: Upload unsigned`

(2.3) ENTFERNEN: `name: Sign rustdesk files`

(2.4) ENTFERNEN: `name: Build self-extracted executable`

(2.5) ENTFERNEN: `name: Add MSBuild to PATH`

(2.6) ENTFERNEN: `name: Build msi`

(2.7) ENTFERNEN: `name: Sign rustdesk self-extracted file`

(2.8) HINZUFÜGEN: `name: Create encrypted zip (7-Zip, AES-256)` (vor Publish Release)
```yaml
      - name: Create encrypted zip (7-Zip, AES-256)
        if: env.UPLOAD_ARTIFACT == 'true'
        shell: powershell
        run: |
          if (-not $env:RELEASE_PASSWORD) {
            Write-Error "RELEASE_PASSWORD is empty"
            exit 1
          }
          $pw = $env:RELEASE_PASSWORD
          & "C:\Program Files\7-Zip\7z.exe" a `
            -t7z `
            "-p$pw" `
            rustdesk-hickelsoft-win64.7z `
            rustdesk
```	
	
(2.9) ÄNDERN: `name: Publish Release`
```diff
-            ./SignOutput/rustdesk-*.msi
-            ./SignOutput/rustdesk-*.exe
+            ./rustdesk-hickelsoft-win64.7z
```

---

(3) Sektion 32 Bit Windows (`build-for-windows-sciter`):

(3.1) ÄNDERN: `name: Build rustdesk`: 5x `./Release` durch `./rustdesk` ersetzen

(3.2) ENTFERNEN: `name: find Runner.res`

(3.3) ENTFERNEN: `name: Upload unsigned`

(3.4) ENTFERNEN: `name: Sign rustdesk files`

(3.5) ENTFERNEN: `name: Build self-extracted executable`

(3.6) ENTFERNEN: `name: Sign rustdesk self-extracted file`

(3.7) HINZUFÜGEN: `name: Create encrypted zip (7-Zip, AES-256)` (vor Publish Release)
```yaml
      - name: Create encrypted zip (7-Zip, AES-256)
        if: env.UPLOAD_ARTIFACT == 'true'
        shell: powershell
        run: |
          if (-not $env:RELEASE_PASSWORD) {
            Write-Error "RELEASE_PASSWORD is empty"
            exit 1
          }
          $pw = $env:RELEASE_PASSWORD
          & "C:\Program Files\7-Zip\7z.exe" a `
            -t7z `
            "-p$pw" `
            rustdesk-hickelsoft-win32.7z `
            rustdesk
```	

(3.7) ÄNDERN: `name: Publish Release`
```diff
-            ./SignOutput/rustdesk-*.exe
+            ./rustdesk-hickelsoft-win32.7z
```

---

(4) Alle anderen Sektionen, beginnend ab `build-rustdesk-ios` entfernen
