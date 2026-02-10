# ToyLib Vulkan キャッチアップ標準手順  
(macOS / Vulkan SDK / SDL3 / Xcode)

このドキュメントは、**ToyLib の Vulkan バックエンド検討・実装に向けた当面の標準開発環境**として、

- macOS
- **LunarG Vulkan SDK（MoltenVK）**
- **SDL3**
- **Xcode（CMakeなし）**

を使い、**安定して Vulkan の初期化〜描画まで進められる状態**を作るための手順をまとめたものです。

> 目的  
> - Vulkan を macOS で確実に動かす  
> - 学習と ToyLib 実装の土台を共通化する  
> - Xcode 直実行でハマらない環境を作る  

---

## 0. 前提条件

- Xcode がインストールされている
- Vulkan SDK（LunarG）がインストール済み
- SDL3 が利用可能（brew または自前ビルド）
- Apple Silicon / Intel どちらでも可

---

## 1. Xcode の開発者ディレクトリ確認（重要）

`xcodebuild` がエラーになる場合、CommandLineTools が選ばれていることがあります。

```bash
sudo xcode-select -s /Applications/Xcode.app/Contents/Developer
xcodebuild -version
```

これで Xcode のバージョンが表示されれば OK。

---

## 2. Vulkan SDK（LunarG）の利用

### 2.1 Vulkan SDK の配置例

```
~/VulkanSDK/1.4.341.0/macOS
```

以下が含まれていれば OK：

- `include/vulkan`
- `lib/libvulkan.dylib`
- `share/vulkan/icd.d/MoltenVK_icd.json`
- `share/vulkan/explicit_layer.d`

### 2.2 ターミナルでの動作確認（任意）

```bash
source ~/VulkanSDK/1.4.341.0/setup-env.sh
vulkaninfo
```

`vulkaninfo` が動けば SDK + MoltenVK は正常。

---

## 3. SDL3 の準備

### 3.1 Homebrew（推奨）

```bash
brew install sdl3
```

標準パス：
- include: `/opt/homebrew/include/SDL3`
- lib: `/opt/homebrew/lib/libSDL3.dylib`

### 3.2 自前ビルドの場合

以下が揃っていれば OK：

- `<prefix>/include/SDL3/SDL.h`
- `<prefix>/lib/libSDL3.dylib`

---

## 4. Xcode プロジェクト作成（CMakeなし）

1. Xcode → **File > New > Project**
2. **macOS > Command Line Tool**
3. Language: **C++**
4. Product Name: 例 `VkSDL3Baseline`

> SDL3 がウィンドウを作るため、Command Line Tool が最も簡単。

---

## 5. Build Settings 設定（SDL3 + Vulkan）

### 5.1 Header Search Paths

**Build Settings** → `Header Search Paths` に追加：

- `$(HOME)/VulkanSDK/1.4.341.0/macOS/include`
- SDL3:
  - brew: `/opt/homebrew/include`
  - 自前: `<prefix>/include`

### 5.2 Library Search Paths

**Library Search Paths** に追加：

- `$(HOME)/VulkanSDK/1.4.341.0/macOS/lib`
- SDL3:
  - brew: `/opt/homebrew/lib`
  - 自前: `<prefix>/lib`

### 5.3 Link Binary With Libraries

**Build Phases** → `Link Binary With Libraries`

追加：
- `libvulkan.dylib`
- `libSDL3.dylib`

（表示されない場合は **Add Other…** で直接指定）

---

## 6. 実行時環境変数（Xcode Scheme）

Xcode 実行時は shell の環境変数が使われないため、**必ず設定**する。

**Product > Scheme > Edit Scheme… > Run > Environment Variables**

### 6.1 必須

- `VULKAN_SDK`  
  `$(HOME)/VulkanSDK/1.4.341.0/macOS`

- `VK_ICD_FILENAMES`  
  `$(HOME)/VulkanSDK/1.4.341.0/macOS/share/vulkan/icd.d/MoltenVK_icd.json`

### 6.2 推奨（デバッグ用）

- `VK_LAYER_PATH`  
  `$(HOME)/VulkanSDK/1.4.341.0/macOS/share/vulkan/explicit_layer.d`

- `VK_INSTANCE_LAYERS`  
  `VK_LAYER_KHRONOS_validation`

---

## 7. 最小ベースラインコード（初期化確認）

この段階の目標：

- SDL3 ウィンドウ作成
- Vulkan Instance 作成
- Surface 作成
- Physical Device / Logical Device 作成

### 7.1 macOS 固有の注意点

MoltenVK では **必ず portability opt-in** が必要：

- Instance extension  
  `VK_KHR_portability_enumeration`
- Instance flag  
  `VK_INSTANCE_CREATE_ENUMERATE_PORTABILITY_BIT_KHR`

---

### 7.2 main.cpp（ベースライン）

```cpp
#include <SDL3/SDL.h>
#include <SDL3/SDL_vulkan.h>
#include <vulkan/vulkan.h>

#include <cstdio>
#include <vector>
#include <optional>

static void Check(VkResult r, const char* msg)
{
    if (r != VK_SUCCESS)
    {
        std::fprintf(stderr, "Vulkan error %d: %s\n", (int)r, msg);
        std::abort();
    }
}

int main()
{
    if (!SDL_Init(SDL_INIT_VIDEO))
    {
        std::fprintf(stderr, "SDL_Init failed: %s\n", SDL_GetError());
        return 1;
    }

    SDL_Window* window = SDL_CreateWindow(
        "VkSDL3 Baseline",
        1280, 720,
        SDL_WINDOW_VULKAN | SDL_WINDOW_RESIZABLE);

    uint32_t extCount = 0;
    SDL_Vulkan_GetInstanceExtensions(&extCount, nullptr);
    std::vector<const char*> exts(extCount);
    SDL_Vulkan_GetInstanceExtensions(&extCount, exts.data());

    exts.push_back(VK_KHR_PORTABILITY_ENUMERATION_EXTENSION_NAME);

    VkApplicationInfo app{ VK_STRUCTURE_TYPE_APPLICATION_INFO };
    app.apiVersion = VK_API_VERSION_1_2;

    VkInstanceCreateInfo ci{ VK_STRUCTURE_TYPE_INSTANCE_CREATE_INFO };
    ci.pApplicationInfo = &app;
    ci.enabledExtensionCount = (uint32_t)exts.size();
    ci.ppEnabledExtensionNames = exts.data();
    ci.flags = VK_INSTANCE_CREATE_ENUMERATE_PORTABILITY_BIT_KHR;

    VkInstance instance;
    Check(vkCreateInstance(&ci, nullptr, &instance), "vkCreateInstance");

    VkSurfaceKHR surface;
    SDL_Vulkan_CreateSurface(window, instance, nullptr, &surface);

    std::puts("Vulkan baseline init OK");

    vkDestroySurfaceKHR(instance, surface, nullptr);
    vkDestroyInstance(instance, nullptr);

    SDL_DestroyWindow(window);
    SDL_Quit();
    return 0;
}
```

---

## 8. 次のステップ（三角形描画まで）

このベースラインが動いたら、以下を順に追加：

1. Swapchain 作成
2. RenderPass 作成
3. Pipeline（SPIR-V）
4. Framebuffer
5. CommandBuffer 記録
6. Semaphore / Fence
7. Present loop

---

## 9. ToyLib 向け運用メモ

- 学習・実装期間は **SDK前提**で問題なし
- 配布時は SDK 依存を外すか検討
- RendererCapabilities を明示して macOS 制約を吸収
- Validation は「学習用」ではなく「品質保証用」

---

## 10. まとめ

- **macOS Vulkan は SDK + MoltenVK が前提**
- **Xcode直実行では環境変数設定が必須**
- **SDL3 + Vulkan は安定して動く**
- この手順を ToyLib Vulkan キャッチアップの標準とする

---

（この後は swapchain〜triangle の完成版を別.mdに切り出すのがおすすめ）
