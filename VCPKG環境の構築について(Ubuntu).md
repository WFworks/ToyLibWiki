# 前提環境
## ディストリビューション
- Ubuntu 25.10 
## ツール、ライブラリ
- VSCODE
- G++, gdb, cmake
- SDL3, SDL3-image, SDL3-ttf
- OpenGL
- OpenAL-Soft
- Assimp

# OS機能ライブラリのインストール
```bash
sudo apt update

# ビルドツール一式
sudo apt install -y \
  build-essential \
  cmake \
  gdb \
  git \
  pkg-config

# 必要ライブラリ一式
sudo apt install -y \
  libgl-dev \
  libsdl3-dev \
  libsdl3-image-dev \
  libsdl3-ttf-dev \
  libassimp-dev \
  libopenal-dev \
  libfmt-dev \
  libmpg123-dev
```

# ```

