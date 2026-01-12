# ToyLib Namespace Migration – Target Header List

ToyLib のヘッダファイルのうち、  
**`namespace toy` を適用すべき対象ファイル一覧**です。

`Utils/MathUtil` など汎用ユーティリティ系は対象外としています。

---

## 1. Engine/Core

- include/Engine/Core/Application.h  
- include/Engine/Core/ApplicationEntry.h  
- include/Engine/Core/Actor.h  
- include/Engine/Core/Component.h  

---

## 2. Engine/Runtime

- include/Engine/Runtime/InputSystem.h  
- include/Engine/Runtime/TimeOfDaySystem.h  
- include/Engine/Runtime/SingleInstance.h  

---

## 3. Engine/Render

- include/Engine/Render/Renderer.h  
- include/Engine/Render/Shader.h  
- include/Engine/Render/LightingManager.h  

---

## 4. Movement

- include/Movement/MoveComponent.h  
- include/Movement/FollowMoveComponent.h  
- include/Movement/OrbitMoveComponent.h  
- include/Movement/DirMoveComponent.h  
- include/Movement/FPSMoveComponent.h  
- include/Movement/InertiaMoveComponent.h  

---

## 5. Camera

- include/Camera/CameraComponent.h  
- include/Camera/FollowCameraComponent.h  
- include/Camera/OrbitCameraComponent.h  

---

## 6. Physics

- include/Physics/PhysWorld.h  
- include/Physics/ColliderComponent.h  
- include/Physics/BoundingVolumeComponent.h  
- include/Physics/GravityComponent.h  
- include/Physics/LaserColliderComponent.h  
- include/Physics/OBB.h  
- include/Physics/Ray.h  

---

## 7. Graphics – Visual Components

- include/Graphics/VisualComponent.h  

---

## 8. Graphics – Mesh

- include/Graphics/Mesh/MeshComponent.h  
- include/Graphics/Mesh/SkeletalMeshComponent.h  
- include/Graphics/Mesh/AnimationPlayer.h  

---

## 9. Graphics – Sprite / Billboard

- include/Graphics/Sprite/SpriteComponent.h  
- include/Graphics/Sprite/BillboardComponent.h  
- include/Graphics/Sprite/TextSpriteComponent.h  

---

## 10. Graphics – Effect

- include/Graphics/Effect/ParticleComponent.h  
- include/Graphics/Effect/WireframeComponent.h  
- include/Graphics/Effect/ShadowSpriteComponent.h  

---

## 11. Environment

- include/Environment/SkyDomeComponent.h  
- include/Environment/WeatherDomeComponent.h  
- include/Environment/WeatherManager.h  
- include/Environment/WeatherOverlayComponent.h  

---

## 12. Asset – Manager

- include/Asset/AssetManager.h  

---

## 13. Asset – Material

- include/Asset/Material/Material.h  
- include/Asset/Material/Texture.h  

---

## 14. Asset – Geometry

- include/Asset/Geometry/Mesh.h  
- include/Asset/Geometry/MeshBone.h  
- include/Asset/Geometry/VertexArray.h  
- include/Asset/Geometry/Polygon.h  

---

## 15. Asset – Font/Text

- include/Asset/Font/TextFont.h  

---

## 16. Asset – Audio

- include/Asset/Audio/Music.h  
- include/Asset/Audio/SoundEffect.h  

---

## 17. Audio – Mixer / Component

- include/Audio/SoundComponent.h  
- include/Audio/SoundMixer.h  

---

# 対象外（namespace toy へ入れないことを推奨）

- include/Utils/MathUtil.h  
- include/Utils/StringUtil.h  
- include/Utils/JsonHelper.h  
- include/Utils/TextureUtil.h  
- include/Utils/Random.h  
- include/Utils/Timer.h  
- include/Utils/Frustum.h  
- include/Utils/FrustumUtil.h  
- include/ToyLib.h  

---