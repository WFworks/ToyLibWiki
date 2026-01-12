# 必要ツールのインストール
- Visual Studio 2026/2022
- cmake
- git
- VSCODE

# vcpkgセットアップ
インストール先をC:¥Reposと仮定
``` powershell
mkdir C:\tools
cd C:\Repos

git clone https://github.com/microsoft/vcpkg.git
cd vcpkg
bootstrap-vcpkg.bat
```

# vspkg経由で依存ライブラリをインストール

``` powershell
vcpkg install ^
  sdl3:x64-windows ^
  sdl3-image[libjpeg-turbo]:x64-windows ^
  sdl3-ttf:x64-windows ^
  assimp:x64-windows ^
  openal-soft:x64-windows ^
  fmt:x64-windows ^
  mpg123:x64-windows ^
  sdl3-mixer[mpg123]:x64-windows
```

# **CMake で vcpkg を使う設定**
``` powershell
cmake -S . -B build `
  -DCMAKE_TOOLCHAIN_FILE=C:\Repos\vcpkg\scripts\buildsystems\vcpkg.cmake `
  -DVCPKG_TARGET_TRIPLET=x64-windows `
  -DCMAKE_BUILD_TYPE=Debug
```

# CMakeLists の Windows 部分（推奨構成）
``` cmake
elseif (WIN32)
    message(STATUS "Configuring for Windows build")

    find_package(OpenGL REQUIRED)
    find_package(SDL3 CONFIG REQUIRED)
    find_package(SDL3_image CONFIG REQUIRED)
    find_package(SDL3_ttf CONFIG REQUIRED)
    find_package(assimp CONFIG REQUIRED)
    find_package(OpenAL CONFIG REQUIRED)
    find_package(fmt CONFIG REQUIRED)
    find_package(SDL3_mixer CONFIG REQUIRED)

    target_link_libraries(${PROJECT_NAME}
        OpenGL::GL

        SDL3::SDL3
        SDL3_image::SDL3_image
        SDL3_ttf::SDL3_ttf
        SDL3_mixer::SDL3_mixer

        assimp::assimp
        OpenAL::OpenAL
        fmt::fmt
    )
endif()
```