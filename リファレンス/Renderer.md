  

Renderer は ToyLib の描画機能をまとめて管理するクラスです。  

アプリ側が直接使うのは主に次の 3 点です：

  

1. ****画面設定（クリア色・FOV・ウィンドウ情報）****

2. ****カメラ行列の設定****

3. ****VisualComponent（描画コンポーネント）の管理****

  

それ以外（シャドウ、シェーダー、スカイドーム）は、基本的に ****ToyLib が自動で扱うため、利用者は意識しなくても使えます。****

  

---

  

## 1. 初期化

  

Application クラス内で自動生成され、通常は `Initialize()` を呼ぶだけです。

  

```cpp

if (!GetRenderer()->Initialize()) {

    return false;

}

```

  

SDL / OpenGL / Shader など、必要な初期化はすべて内部で行われます。

  

---

  

## 2. 画面設定

  

### ● クリアカラーを設定

  

```cpp

renderer->SetClearColor(Vector3(0.1f, 0.1f, 0.2f));

```

  

---

  

### ● FOV（視野角）設定

  

```cpp

renderer->SetPerspectiveFov(60.0f);

```

  

---

  

### ● ウィンドウサイズ取得

  

```cpp

float w = renderer->GetScreenWidth();

float h = renderer->GetScreenHeight();

```

  

---

  

## 3. カメラ設定

  

ToyLib はカメラコンポーネントではなく ****ビュー行列を直接渡す方式**** です。

  

```cpp

Matrix4 view = Matrix4::CreateLookAt(eye, target, Vector3::UnitY);

renderer->SetViewMatrix(view);

```

  

プロジェクション行列は Renderer が内部で保持・更新します。

  

---

  

## 4. VisualComponent の管理（自動）

  

MeshComponent, SpriteComponent などの描画コンポーネントは  

****生成時に自動で Renderer に登録されます。****

  

利用者側で `AddVisualComp()` を手動で呼ぶ必要はありません。

  

---

  

## 5. VisualLayer（描画順）

  

VisualComponent は内部で自動的に以下のレイヤーへ分類されます：

  

順番｜レイヤー | 用途

---|---|---

① | Background2D | 背景スプライト

② | Effect3D | パーティクル・ビルボード

③ | Object3D | 通常のメッシュ・スキンメッシュ

④ | OverlayScreen | フルスクリーンエフェクト

⑤ | UI | HUD / メニュー

  

Renderer が順番に描画するため、利用者が制御する必要はありません。

  

---

  

## 6. SkyDome / Lighting（必要に応じて）

  

### ● SkyDome の登録

  

```cpp

renderer->RegisterSkyDome(sky);

```

  

### ● ライティングマネージャー

  

```cpp

renderer->SetLightingManager(manager);

```

  

空や天候・太陽光の設定は `LightingManager` と `SkyDomeComponent` が担当します。

  

---

  

## 7. シャドウマッピング（自動）

  

Renderer が内部で以下をすべて実行します：

  

- ShadowMap FBO 作成  

- LightSpaceMatrix 計算  

- Shadow Pass の描画  

  

利用者が触る必要はありません。  

デバッグ用で影テクスチャを確認したい場合のみ：

  

```cpp

auto shadowTex = renderer->GetShadowMapTexture();

```

  

---

  

## 8. 毎フレーム描画（Application が自動で呼ぶ）

  

```cpp

renderer->Draw();

```

  

通常は Application のループで毎フレーム呼び出されます。

  

---

  

## 9. デバッグモード

  

```cpp

renderer->SetDebugMode(true);

```

  

ON にすると：

  

- ワイヤーフレーム描画  

- AABB（当たり判定ボックス）表示  

- シャドウ可視化  

  

などが有効になります。

  

---

  

## 10. 使用例（最小構成）

  

```cpp

bool MyApp::Initialize()

{

    auto* renderer = GetRenderer();

    renderer->Initialize();

  

    renderer->SetClearColor(Vector3(0.05f, 0.07f, 0.1f));

    renderer->SetPerspectiveFov(60.0f);

  

    Matrix4 view = Matrix4::CreateLookAt(

        Vector3(0, 2, -5),

        Vector3(0, 1, 0),

        Vector3(0, 1, 0)

    );

    renderer->SetViewMatrix(view);

  

    return true;

}

  

void MyApp::RunLoop()

{

    while (!ShouldQuit())

    {

        ProcessInput();

        Update();

        GetRenderer()->Draw();

    }

}

```

  

---

  

# ✔ このページの目的

  

- Renderer の「利用者が知るべきこと」だけに絞って説明  

- ToyLib の描画機能を簡単に扱えるようにする  

- 内部仕様（OpenGL、Shadow、Shader）は不要  

  

内部実装は別ページ ****Renderer_Internal.md**** へ分離する構成がおすすめです。