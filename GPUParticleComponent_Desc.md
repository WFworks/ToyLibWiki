# GPUParticleComponent::Desc 設定リファレンス

このドキュメントは **ToyLib の `GPUParticleComponent`** における  
`Desc` 構造体（GPU パーティクル設定）の仕様と使い方をまとめたものです。

本 `Desc` は **C++ から直接指定**することも、  
**JSON ファイルから `InitFromFile()` で読み込む**ことも可能です。

---

## 基本コンセプト

- GPU パーティクル（Transform Feedback）
- 粒子の生成・更新は GPU
- Actor に追従するエミッタ（ローカルオフセット指定）
- 無限発生 / 有限寿命の両対応
- 旧 ParticleComponent（CPU）と同等以上の表現力

---

## Desc 構造体定義

```cpp
struct Desc
{
    ParticleMode mode = ParticleMode::Spark;

    uint32_t maxParticles = 64;

    float componentLife   = 0.0f;  // 0 = infinite
    float particleLife    = 0.6f;  // seconds
    float size            = 1.0f;

    float spawnRatePerSec = 60.0f; // respawns per second (approx)
    float spawnRampSec    = 0.0f;  // spawn ramp time (0 = none)

    float spread          = 2.0f;

    float gravity         = 0.0f;
    float lift            = 0.0f;

    bool  additiveBlend   = true;
    bool  warmStart       = true;

    Vector3 emitterOffset = Vector3::Zero; // Actor local offset
};
```

---

## 各パラメータ解説

### ParticleMode mode

| Mode  | 説明 |
|------|------|
| Spark | 爆発・火花（拡散・加算合成） |
| Smoke | 煙・炎（上昇） |
| Water | 水・雨（落下） |

---

### maxParticles
同時に存在可能な最大粒子数。GPU バッファ容量を決定。

---

### componentLife
コンポーネント自体の寿命（秒）。0 で無限。

---

### particleLife
1 粒子の寿命（秒）。

---

### size
描画サイズ（ビルボードクアッドのスケール）。

---

### spawnRatePerSec
1 秒あたりの再生成レート（確率的）。

---

### spawnRampSec
発生レートの立ち上がり時間。

---

### spread
初速のばらつき・拡散量。

---

### gravity
下方向加速度（水・雨用）。

---

### lift
上方向加速度（煙・炎用）。

---

### additiveBlend
加算合成を使うかどうか。

---

### warmStart
起動時に粒子をばらけさせるか。

---

### emitterOffset
Actor ローカル空間での発生位置。

---

## 基本使用例（C++）

```cpp
toy::GPUParticleComponent::Desc desc;
desc.mode            = toy::GPUParticleComponent::ParticleMode::Smoke;
desc.maxParticles    = 100;
desc.particleLife    = 0.6f;
desc.size            = 5.5f;
desc.spawnRatePerSec = 30.0f;
desc.spawnRampSec    = 0.6f;
desc.lift            = 2.0f;
desc.additiveBlend   = true;
desc.emitterOffset   = Vector3(0, 0, 0);

particleComp->SetTexture(texture);
particleComp->Init(desc);
particleComp->Start();
```

---

## 設計思想まとめ

- CPU は演出と制御
- GPU は大量更新
- Desc は演出定義
- JSON は調整用プリセット
