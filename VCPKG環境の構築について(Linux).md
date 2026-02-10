
# OS機能のインストール
```bash
sudo apt update

# ビルドツール一式
sudo apt install -y \
  build-essential \
  cmake \
  git \
  pkg-config

# SDL や OpenAL が内部で使うことがある開発ヘッダ類（ある程度多めに）
sudo apt install -y \
  libx11-dev \
  libxext-dev \
  libxrandr-dev \
  libxi-dev \
  libxinerama-dev \
  libxfixes-dev \
  libasound2-dev \
  libpulse-dev \
  libdbus-1-dev \
  libudev-dev \
  libgl-dev

# SDL3のインストールに必要
sudo apt install autoconf autoconf-archive automake libtool

# vcpkg の libsystemd ビルドで必要になることがある
sudo apt install -y python3-venv

# mpg123 は「システム側」から使う想定
sudo apt install -y libmpg123-dev
```

# vcpkgのインストール
インストールディレクトリを、~/Repos/と仮定

``` bash
mkdir -p ~/Repos
cd ~/Repos

git clone https://github.com/microsoft/vcpkg.git
cd vcpkg
./bootstrap-vcpkg.sh
```

# 環境変数
## シェル変数に以下を追加
```bash
export VCPKG_ROOT="$HOME/Repos/vcpkg"
export VCPKG_TRIPLET="x64-linux"
```
## Cmake実行時
``` bash
cmake -S . -B build \
  -DCMAKE_TOOLCHAIN_FILE=$VCPKG_ROOT/scripts/buildsystems/vcpkg.cmake \
  -DCMAKE_BUILD_TYPE=Debug
```

# vcpkgでの必要ライブラリのインストール
```bash
cd ~/Repos/vcpkg

./vcpkg install \
  sdl3 \
  sdl3-image[jpeg,png] \
  sdl3-ttf \
  assimp \
  openal-soft \
  fmt \
  --triplet x64-linux
```

# CMakeListsのポイント
``` cmake
elseif(UNIX AND NOT APPLE)
    message(STATUS "Configuring for Linux build")

    find_package(OpenGL REQUIRED)

    # SDL3 系
    find_package(SDL3        REQUIRED)
    find_package(SDL3_image  REQUIRED)
    find_package(SDL3_ttf    REQUIRED)

    find_package(GLEW   REQUIRED)
    find_package(assimp REQUIRED)
    find_package(OpenAL REQUIRED)
    find_package(fmt CONFIG REQUIRED)

    # mpg123 はシステムから手動取得
    find_path(MPG123_INCLUDE_DIR
        NAMES mpg123.h
        PATHS
            /usr/include
            /usr/local/include
            /usr/include/mpg123
            /usr/local/include/mpg123
    )
    find_library(MPG123_LIBRARY
        NAMES mpg123
        PATHS
            /usr/lib
            /usr/local/lib
            /usr/lib/x86_64-linux-gnu
            /usr/lib64
    )

    # include
    target_include_directories(${PROJECT_NAME} PRIVATE
        ${SDL3_INCLUDE_DIRS}
        ${SDL3_IMAGE_INCLUDE_DIRS}
        ${SDL3_TTF_INCLUDE_DIRS}
        ${ASSIMP_INCLUDE_DIRS}
        ${OPENAL_INCLUDE_DIR}
        ${MPG123_INCLUDE_DIR}
    )

    # リンク順が重要（OpenAL の後ろに fmt）
    target_link_libraries(${PROJECT_NAME}
        OpenGL::GL
        SDL3::SDL3
        SDL3_image::SDL3_image
        SDL3_ttf::SDL3_ttf
        assimp::assimp
        OpenAL::OpenAL   # or ${OPENAL_LIBRARY}
        fmt::fmt
        ${MPG123_LIBRARY}
    )
endif()
```