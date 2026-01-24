# ToyLib Physics System – Current Specification（現行仕様解説）

> 対象：PhysWorld / GravityComponent / MoveComponent  
> 前提：OBB + Ray + MTV を組み合わせた軽量キャラコントローラ型物理

---

## 1. 全体構成と責務分離

ToyLib の物理は、**用途別に処理を分離**する設計を採用している。

| 役割 | 担当 |
|---|---|
| 重力・接地・床追従 | GravityComponent |
| プレイヤー入力による移動 | MoveComponent |
| 壁衝突・押し戻し | PhysWorld::CollideAndCallback |
| 先行判定（壁） | Ray（RayHitWall） |
| 形状衝突判定 | OBB + MTV（SAT） |

**設計思想**
- 「床か壁か」はフラグではなく**法線・用途**で決める
- MTV 押し戻しは**最後の保険**
- 壁は Ray、床は Gravity が主担当

---

## 2. Collider とフラグの使い方

### 2.1 フラグの位置づけ

フラグは **候補の絞り込み** に使うだけで、最終挙動は法線や allowY によって決定する。

- C_WALL : 壁判定 Ray / 壁用 MTV の対象
- C_GROUND : 地面サンプル対象
- C_FOOT : 接地判定用
- C_BODY : キャラ本体

※ C_GROUND | C_WALL を併せ持つ斜面も正当な構成

---

## 3. GravityComponent（床と重力）

### 3.1 役割

- 重力加速
- 接地判定
- 床スナップ
- 動く床追従
- 地面法線に基づく姿勢制御（任意）

### 3.2 接地判定フロー

1. 足元 OBB の最下点を取得
2. 真下に床サンプル（複数点）
3. 最も高い床を採用
4. 法線を取得して GroundPose に保存

---

## 4. MoveComponent（移動）

### 4.1 Update()

- 純粋な速度 → 位置変換のみ
- 衝突・床判定は行わない

### 4.2 TryMoveWithRayCheck()

1. 入力ベクトルから goal を算出
2. 壁判定 Ray を先行
3. ヒットしたら stopPos に制限
4. 最後に MTV を一度だけ適用

**重要**
- Ray は胴体〜腰高さのみ
- 足元 Ray は使わない

---

## 5. RayHitWall

- 対象：C_WALL を持つ Collider の OBB
- 最も近いヒットのみ採用
- hitPos は少し手前に補正

床か壁かの判断はここでは行わない。

---

## 6. CollideAndCallback（MTV）

- Ray / Gravity で解決できなかっためり込み除去
- 最大 push を 1 回だけ適用
- allowY=false の場合は Y 成分を除外

### 床っぽい接触の判定

- MTV 法線 n.y を使用
- n.y >= cos(maxSlope) の場合は床扱い
- 壁処理ではスキップ

---

## 7. 今回の問題と解決

**問題**
- C_GROUND|C_WALL 斜面で引っかかる

**原因**
- 足元 Ray が床側面を壁として検出

**解決**
- kRayLow を撤廃
- 床は Gravity に完全委任
- ignoreActor / ignoreCollider は不要

---

## 8. 現行仕様の強み

- フラグ依存が少ない
- 床 / 壁の責務分離が明確
- 斜面・動く床に強い
- デバッグしやすい

---

## 9. 運用ルール（明文化）

- 足元 Ray で壁判定しない
- 床/壁をフラグで決めない
- 床 = Gravity
- 壁 = Ray
- 最後に MTV

---
