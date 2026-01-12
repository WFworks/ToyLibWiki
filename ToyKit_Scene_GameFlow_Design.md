# ToyKit Scene / GameFlow 設計まとめ

## 目的

ToyKit は ToyLib の上に構築される **ゲーム進行テンプレート層**であり、

- ToyLib（エンジン本体）を汚さない
- ゲームの流れ（タイトル → ステージ → ムービー等）を整理する
- 学習用途・サンプル用途にも使いやすい

ことを目的とする。

---

## 名前空間構成

```cpp
namespace toy        // エンジン本体
namespace toy::kit   // ゲーム進行・テンプレート層
```

- `toy`  
  Actor / Component / Renderer / PhysWorld / AssetManager など

- `toy::kit`  
  Scene / GameFlow / Transition などのゲーム構造

---

## 全体構造

```
Application
 ├─ Renderer
 ├─ PhysWorld
 ├─ AssetManager
 ├─ InputSystem
 └─ GameFlow
      └─ Scene
           └─ Actor（Scene が所有・寿命管理）
```

---

## Application の役割

### Application が担当するもの

- メインループ
- SDL イベント / 入力更新
- Renderer / PhysWorld / AssetManager の管理
- Actor の実体管理

### Application が知らないもの

- Scene の種類
- ゲーム進行ロジック
- ステージ構成

---

## Scene（IScene）

Scene は **ゲームの1状態（1シーン）**を表す。

---

## Scene による Actor 管理

- Scene は Actor を **所有（寿命管理）**する
- Actor の生成は **Scene::CreateActor() 経由のみ**
- Scene 終了時に Actor を一括破棄する

---

## GameFlow

GameFlow は **Scene の流れを組み立てる管理者**。

- Scene の中身は知らない
- どの Scene に遷移するかだけを知る

---

## 設計方針まとめ

- ToyLib と ToyKit を明確に分離する
- Scene で「何が起きるか」を完結させる
- GameFlow で「どう繋がるか」を定義する

---

## 一言まとめ

**Scene で点を作り、GameFlow で線を引く**
