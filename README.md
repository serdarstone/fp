# FlappyBird — iOS 14+ / arm64 / arm64e / RootHide

## Codemagic ile Build (önerilen)

1. GitHub'a push et
2. codemagic.io → "Start for free"
3. GitHub repoyu bağla
4. codemagic.yaml otomatik algılanır
5. "Start build" → 10 dakika → Artifacts'tan deb + ipa indir

## Manuel Build (Mac gerekir)

```bash
brew install ldid dpkg

SDK=$(xcrun --sdk iphoneos --show-sdk-path)

swiftc FlappyBird/*.swift \
  -sdk $SDK -target arm64-apple-ios14.0 \
  -framework UIKit -framework SpriteKit -framework AudioToolbox \
  -module-name FlappyBird -parse-as-library -o build/FlappyBird_arm64

swiftc FlappyBird/*.swift \
  -sdk $SDK -target arm64e-apple-ios14.0 \
  -framework UIKit -framework SpriteKit -framework AudioToolbox \
  -module-name FlappyBird -parse-as-library -o build/FlappyBird_arm64e

lipo -create build/FlappyBird_arm64 build/FlappyBird_arm64e -output build/FlappyBird

mkdir -p package/Applications/FlappyBird.app
cp build/FlappyBird package/Applications/FlappyBird.app/
cp FlappyBird/Info.plist package/Applications/FlappyBird.app/
ldid -SFlappyBird/FlappyBird.entitlements package/Applications/FlappyBird.app/FlappyBird
dpkg-deb -Zgzip --build package FlappyBird_1.0_iphoneos-arm64.deb

mkdir -p ipa_stage/Payload
cp -r package/Applications/FlappyBird.app ipa_stage/Payload/
cd ipa_stage && zip -r ../FlappyBird.ipa Payload/
```

## Yükleme

- **deb** → Sileo / Zebra / SSH + dpkg
- **ipa** → TrollStore / Sideloadly
- **RootHide** → uicache -p /var/jb/Applications/FlappyBird.app
