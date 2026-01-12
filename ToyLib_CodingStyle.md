# ToyLib Coding Style Guide

ToyLib 全体でコードの品質と可読性を統一するための **公式コーディング規約**。  
C++20 + OpenGL/SDL2 ベースで、Actor/Component 構成のゲームエンジンとして長期保守しやすい基準をまとめています。


---

# 1. 命名規則（Naming Conventions）

## 1.1. クラス名
- **PascalCase**
- 例：`Actor`, `MeshComponent`, `FollowMoveComponent`

## 1.2. 関数名 / メソッド名
- **camelCase**
- 例：`update()`, `processInput()`, `computeWorldMatrix()`

## 1.3. 変数名
### メンバ変数
- **m + PascalCase**
- 例：`mPosition`, `mVelocity`, `mOwner`

### ローカル変数 / 関数引数
- **camelCase**
- 例：`deltaTime`, `forward`, `pos`, `color`

### 定数（C++）
- 全大文字 + アンダースコア  
- 例：`MAX_BONES`, `SHADOW_MAP_SIZE`

---

# 2. クラス構造 / ファイル構造

## 2.1. 1 クラス 1 ファイル
- `ClassName.h`  
- `ClassName.cpp`

## 2.2. include の順序
1. 対応するヘッダ（cpp の場合）
2. ToyLib 内部ヘッダ
3. 外部ライブラリ（SDL, GL, Assimp）
4. C++ 標準ライブラリ

---

# 3. 引数の扱い（const / 参照 / 値渡し）

## 3.1. 軽量構造体（Vector2, Vector3 など）
- 原則 **値渡し（const なし）**

## 3.2. 重いオブジェクト
- **const T&** を使用  
（Matrix4, Quaternion, std::string など）

## 3.3. 禁止パターン
❌ `const Vector3 pos`（読み取り専用コピーで不自然）

---

# 4. インクリメント規約（++i / i++）

## 4.1. ToyLib の統一ルール
- **基本型（int, size_t） → `i++`（後置）に統一**
- **イテレータ → `++it`（前置）に統一**

## 4.2. 理由
### ✔ 現代コンパイラでは基本型で `++i` と `i++` の性能差が無い  
最適化で同じ機械語になる。

### ✔ 可読性  
初心者にも自然で読みやすいのは `i++`。

### ✔ イテレータのみ前置を使うべき理由  
後置は内部コピーが必要になるケースがあるため。

---

# 5. クラス設計の基本方針

## 5.1 Actor / Component ルール
- Component は **Actor を所有しない**
- Actor が Component を **unique_ptr** で保持する
- Component は「最小責務」
- Update(), ProcessInput() を明確に使い分ける

## 5.2 PhysWorld の責務分離
物理処理が肥大化しないよう、以下は分割候補：
- SAT 判定
- 衝突検出
- 地形・地面判定
- レイキャスト
- AABB/OBB 形状処理

---

# 6. コードフォーマット（インデント / スペース / ブレース）

## 6.1. インデント
- **スペース 4**
- タブ禁止

## 6.2. ブレーススタイル
- **Allman スタイル（改行）**
```
if (cond)
{
    ...
}
```

## 6.3. 空行ルール
- ブロック間：1 行  
- 関数間：2 行

---

# 7. コメント規約

## 7.1. 基本
- `//` を使用
- 関数の説明は関数の上に記述

## 7.2. Doxygen スタイル（任意）
```
/**
 * @brief 影を描画する
 */
```

---

# 8. 安全性とメモリ管理

## 8.1. new/delete を使用しない
- unique_ptr/shared_ptr を使用する  
（外部ライブラリポインタは例外）

## 8.2. nullptr チェック必須

## 8.3. 配列は std::vector を使用

---

# 9. Shader / OpenGL 規約

## 9.1. uniform / varying 命名
- uniform: `u + PascalCase`
- varying/in: `v + PascalCase`

## 9.2. シェーダーファイル
- 1 シェーダ 1 ファイル  
- 未使用コードは削除する

---

# 10. ファイルヘッダの統一

すべてのヘッダファイルに：
```
#pragma once
```

---

# 11. ディレクトリ構造（推奨）

```
ToyLibCore/
  ├─ Visual/
  ├─ Physics/
  ├─ Input/
  ├─ Renderer/
  ├─ Component/
  └─ Util/

GameApp/
  ├─ Actors/
  ├─ Scenes/
  ├─ Assets/
  └─ Settings/
```

---

# 12. TODO / FIXME / NOTE の扱い

- `TODO:` → 後で実装  
- `FIXME:` → 確定したバグ  
- `NOTE:` → 補足説明

---

# 13. 自動フォーマット推奨ツール

## 13.1 .clang-format
```
BasedOnStyle: LLVM
IndentWidth: 4
UseTab: Never
ColumnLimit: 120
BreakBeforeBraces: Allman
```

## 13.2 clang-tidy
- modernize-*
- readability-*
- cppcoreguidelines-*

---

# まとめ

ToyLib のコーディングポリシー：

- **読みやすく破綻の少ない C++**
- **軽量型は値渡し、重い型は const&**
- **基本型は i++、イテレータは ++it**
- **OpenGL/SDL/Assimp の外部依存を明確に扱う**
- **Actor/Component の責務を明確に分離する**

これにより、長期保守しやすく初心者にも優しいコードベースを実現する。

