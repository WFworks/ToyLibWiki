## 動作要件

### 対応プラットフォーム
- Windows 10 / 11 (Visual Studio + MSVC + vcpkg + VSCode)
- Linux (GCC / Clang + pkg-config + VSCode)
- macOS (Intel または Apple Silicon、Homebrew + VSCode)


### 必須ソフトウェア / 開発環境

#### Windows
- Visual Studio 2022/2026（C++ 開発ツール含む）
- CMake 3.15 以上
- vcpkg
- VSCode（C++拡張機能）
- PowerShell または CMD（管理者権限推奨）

#### Linux
- CMake 3.15 以上
- GCC または Clang
- pkg-config
- vcpkg
- VSCode（C++拡張機能）

#### macOS
- Xcode コマンドラインツール
- CMake 3.15 以上
- Homebrew
- VSCode（C++拡張機能）

### 必須ライブラリ
#### 概要
- OpenGL 4.1以降(各OSで利用可能なもの)
- SDL3
- SDL3_image
- SDL3_ttf
- OpenAL-Soft
- MPG123
- Assimp

### 必須ライブラリのインストール
#### Windows / Linux（vcpkg でインストール）
```
# 基本ライブラリ
vcpkg install sdl3:x64-windows
vcpkg install sdl2-image[jpeg,png]:x64-windows
vcpkg install sdl3-ttf:x64-windows
vcpkg install assimp:x64-windows
vcpkg install libjpeg-turbo:x64-windows
vcpkg install mpg123:x64-windows

```
-


#### macOS（Homebrew 例）
```
brew install sdl3 sdl3_image sdl3_ttf openal-soft assimp
```

### 実行要件
- OpenGL 4.1 以上が動作する GPU ドライバ
- SDL3 対応環境

### 注意事項
- Windows では DLL がビルド後に `build/` または `build/Debug/` にコピーされます
- Linux / macOS ではライブラリパスや環境変数設定を適切に行う必要があります
- Assimp は GitHub カレントブランチから clone し `-DASSIMP_INSTALL_CMAKE_CONFIG=ON` を指定してビルド・インストールを行ってください

