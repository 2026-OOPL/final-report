# 2026 OOPL Final Report

## 組別資訊

組別：第 17 組 <br>
組員：邱冠勛 <br>
復刻遊戲：元氣騎士

## 專案簡介
本專案以復刻涼屋遊戲的元氣騎士為目標，並結合 C++ 的物件導向理念與 PTSD 遊戲框架進行開發。
意旨使用上學期習得的程式設計技巧，如：繼承、多型、虛擬函式等概念，解決在實作遊戲過程只中遇到的困難。
由於時間與技術上的限制，本專案並非追求百分百的細節還原，而是關注在遊戲中的核心部分與程式的正確性，如：地圖生成、碰撞、指標處理等方面；
反之在其他部分如：美術、動畫、特效、音效等方面的琢磨會較為粗糙。

### 遊戲簡介
《元氣騎士》是一款由涼屋遊戲開發的像素射擊彈幕遊戲，主角會在地下城中通過與怪物戰鬥獲得 MP 值以發射武器的子彈，
同時也需維持自身的血量，以保證能夠撐到最終的 Boss 關卡，在連續討伐 3 隻 Boss 後即判定獲勝。

### 組別分工

#### 邱冠勛
1. 隨機地圖與怪物生成系統製作
2. 遠程 AI 走位系統製作
3. 遊戲的開始畫面、可互動的 UI 元素包括按鈕與滑桿
4. 音效與背景音樂系統製作
5. 場景與關卡切換系統製作
6. 結尾結算動畫與製作人員名單製作
7. 相機系統製作

#### 朱政全
1. 

## 遊戲介紹

### 遊戲規則

### 遊戲畫面
![MainMenu](assets/mainmenu.png)
![Gameplay](assets/gameplay.png)

## 程式設計
### 程式架構
#### 場景概念 (Scene)
遊戲中所有的畫面都是由 Scene 支撐，從初始介面到遊玩場景甚至是最後的鳴謝清單。且 GameLoop 會不斷詢問目前的 Scene 是否有切換場景的需求，以此實現場景切換的功能。

```mermaid
classDiagram
  namespace AppRoot {
    class Scene {
        +Update() void
        +GetRedirection() std::shared_ptr~Scene~
    }
    class MainMenu
  }
  namespace MapModule {
    class MapSystem
  }
  namespace SceneModule {
    class GameoverScene
    class CastingScene
    class MapTest
    class LevelSwitch
    class PreloadScene
  }
  namespace External {
    class Util_GameObject
  }
  MapSystem <|-- MapTest
  Scene <|-- CastingScene
  Scene <|-- GameoverScene
  Scene <|-- LevelSwitch
  Scene <|-- MainMenu
  Scene <|-- MapSystem
  Scene <|-- PreloadScene
  Util_GameObject <|-- Scene
```

#### 遊戲角色
遊玩畫面中的角色無論是否能被玩家操控，都繼承自 ``Character`` 類別，且經由 ``GetMoveIntent()`` 與 ``GetFaceDirection()`` 等虛擬函式來讓子類別回傳移動方向。

```mermaid
classDiagram
  namespace CharacterModule {
    class Character {
        +GetWeapon() std::shared_ptr~Weapon~
        +SetWeapon(std::shared_ptr~Weapon~ weapon) void
        +GetCurrentHealth() int
        +GetMaxHealth() int
        +ApplyDamage(int damage) void
        +Heal(int amount) void
        +IsDead() bool
        +GetMoveIntent()* glm::vec2
        +GetFaceDirection()* glm::vec2
    }
  }
  namespace MobsModule {
    class Ghost
    class ZulanInRuinsCannonVisual
    class ZulanInRuinsBeamVisual
    class ZulanInRuins_FloatingCannon
    class ZulanInRuins
    class AncientGearSetBeamVisual
    class AncientGearSet
    class RuinsSearcher
    class BowRuinsGuard
    class RuinsGuardEquipmentVisual
    class RuinsGuard
    class VitaminCMechaBeamVisual
    class VitaminCMecha
    class Mob
    class ShearRuinsGuard
    class RuinsTurretBeamVisual
    class RuinsTurret
    class GhostKing
    class PortalMob
    class GoblinGuard
  }
  namespace PlayerModule {
    class Knight
    class PlayerHudState
    class Player
  }

  Character <|-- Mob
  Character <|-- Player
  Mob <|-- AncientGearSet
  Mob <|-- BowRuinsGuard
  Mob <|-- Ghost
  Mob <|-- GhostKing
  Mob <|-- GoblinGuard
  Mob <|-- PortalMob
  Mob <|-- RuinsGuard
  Mob <|-- RuinsSearcher
  Mob <|-- RuinsTurret
  Mob <|-- ShearRuinsGuard
  Mob <|-- VitaminCMecha
  Mob <|-- ZulanInRuins
  Player <|-- Knight
```
#### UI 介面
所有可互動的按鈕或置頂顯示的元素都基於 UI 介面之上，並且可以再 ``Scene`` 類別中心增成員變數來將 UI 介面顯示在 Scene 之上，且基礎類別中的 ``GetExitSignal()`` 可以即時返回此 UI 是否要關閉，以方便刪除開啟的介面。
```mermaid
classDiagram
  namespace ButtonModule {
    class ImageButtonTheme
    class ButtonHitbox
    class ImageButton
    class TextButtonTheme
    class TextButton
    class ButtonAction
    class Button {
        +GetButonHitboxSize()* glm::vec2
        +GetButonHitboxTranslation()* glm::vec2
        +Update() void
        +GetButtonState() ButtonState
    }
  }
  namespace SliderModule {
    class Slider
  }
  namespace UIModule {
    class BaseUI {
        +Update() void
        +GetExitSignal() bool
    }
    class RespawnUI
    class PlayUI_BossHudState
    class PlayUI
    class PauseUI
    class SettingsUI
    class AdvertisementUI
  }
  namespace External {
    class Util_GameObject
  }
  BaseUI <|-- AdvertisementUI
  BaseUI <|-- PauseUI
  BaseUI <|-- PlayUI
  BaseUI <|-- RespawnUI
  BaseUI <|-- SettingsUI
  Button <|-- ImageButton
  Button <|-- TextButton
  Util_GameObject <|-- BaseUI
  Util_GameObject <|-- Button
  Util_GameObject <|-- Slider
```

#### 怪物AI
遊戲中的 AI 有一個父類別，並且具備兩個子類別，分別是套用於進戰武器的``MeleeAI`` 與遠程武器的 ``RangedAI``，他們分別時做了不同的移動與攻擊邏輯。
```mermaid
classDiagram
  namespace AIModule {
    class MeleeAIConfig
    class MeleeAI
    class StateMachine
    class RangedAI
    class AI {
        +Freeze() void
        +UnFreeze() void
        +IsFrozen() bool
        +GetMoveDirection()* glm::vec2
        +GetFaceDirection()* glm::vec2
        +GetAttackDirection()* glm::vec2
    }
  }
  
  AI <|-- MeleeAI
  AI <|-- RangedAI
```

#### 相機
在場景中如果有定位角色的需求可以使用相機，相機的父類別是一個抽象的類別，且子類別的相機會去實作各自的運鏡邏輯與移動速曲線，以 ``TraceCamera`` 與 ``EaseOutQubicCurve`` 配合來說，他會以 ``EaseOutQubic`` 這調速度曲線去追隨 Target 的位置。

```mermaid
classDiagram
  namespace CameraModule {
    class TraceCamera
    class Curve
    class LinearCurve
    class EaseOutQubicCurve
    class MultiTraceCamera
    class Camera {
        +GetVisibilityByCamera(std::shared_ptr~MapObject~ object) bool
        +GetTransformByCamera(std::shared_ptr~MapObject~ object)* Util::Transform
        +GetTargetCooridinate()* glm::vec2
        +GetCooridinate() glm::vec2
        +SetCooridinate(glm::vec2 coordinate) void
    }
  }
  
  Camera <|-- MultiTraceCamera
  Camera <|-- TraceCamera
  Curve <|-- EaseOutQubicCurve
  Curve <|-- LinearCurve
```

#### 碰撞系統
```mermaid
classDiagram
  namespace PropModule {
    class MechanicalRuinsObstacleConfig
    class MechanicalRuinsBlock
    class MechanicalRuinsFence
    class MechanicalRuinsPillar
    class Prop {
        +Initialize(MapSystem* mapSystem) void
        +IsDestroyRequested() bool
        +RequestDestroy() void
    }
    class BlockingProp
    class Chest_Visuals
    class Chest_Config
    class Chest
    class DroppedWeapon_Config
    class DroppedWeapon
    class AmmoOrb_Config
    class AmmoOrb
    class IChestReward
    class WeaponChestReward
    class ConsumableChestReward
    class IndestructibleObstacle_Config
    class IndestructibleObstacle
    class Portal
  }
  namespace External {
    class Util_GameObject
  }
  BlockingProp <|-- Chest
  BlockingProp <|-- IndestructibleObstacle
  IChestReward <|-- ConsumableChestReward
  IChestReward <|-- WeaponChestReward
  IndestructibleObstacle <|-- MechanicalRuinsBlock
  IndestructibleObstacle <|-- MechanicalRuinsFence
  IndestructibleObstacle <|-- MechanicalRuinsPillar
  Prop <|-- AmmoOrb
  Prop <|-- BlockingProp
  Prop <|-- DroppedWeapon
  Prop <|-- Portal
  Util_GameObject <|-- Prop
```

#### 武器與子彈系統
```mermaid
classDiagram
  namespace BulletsModule {
    class TestBullet
    class BulletFactory
    class SpinningBullet
    class BulletConfig
    class ConfiguredBullet
    class TimedBullet
  }
  namespace ComponentGeneral {
    class Weapon {
        +ShotBullet() bool
        +GetWeaponId() WeaponId
        +GetAmmoCostPerShot() int
        +GetBulletDamage() int
        +GetWeaponType()* WeaponType
        +Update() void
    }
    class Bullet {
        +Update() void
        +GetDamage() int
        +GetMomentum() glm::vec2
        +GetFaction() CombatFaction
        +RequestDestroy() void
        +IsDestroyRequested() bool
    }
  }
  namespace WeaponsModule {
    class ShearAttackEffect
    class Shear
    class Bow
    class BadPistol
    class SniperRifle
    class Plunger
    class AK47
  }
  namespace External {
    class Util_GameObject
  }
  Bullet <|-- ConfiguredBullet
  Bullet <|-- SpinningBullet
  Bullet <|-- TestBullet
  ConfiguredBullet <|-- TimedBullet
  Util_GameObject <|-- Bullet
  Util_GameObject <|-- ShearAttackEffect
  Util_GameObject <|-- Weapon
  Weapon <|-- AK47
  Weapon <|-- BadPistol
  Weapon <|-- Bow
  Weapon <|-- Plunger
  Weapon <|-- Shear
  Weapon <|-- SniperRifle
```

#### 地圖生成系統
```mermaid
classDiagram
  namespace GeneratorModule {
    class GenPortalChamber
    class SpawnInfo
    class RoomInfo
    class GenBossChamber
    class GenRewardChamber
    class MapGenerator
    class GenFightChamber
    class GenChamber
    class MapBlueprint
  }
  namespace MapModule {
    class RectMapArea
    class MapPiece
    class MapSystemConfig_MapInfo
    class MapSystemConfig_PlayerInfo
    class MapSystemConfig_MapConfig
    class MapSystem
    class RoomTransitionSystem_DoorPassageContext
    class RoomTransitionSystem
    class BaseRoomWallLayoutConfig_WallSideLayout
    class BaseRoomWallLayoutConfig_WallLayout
    class Gangway_Config
    class Gangway
    class GangwayLayoutConfig_LayoutProfile
    class RewardRoom
    class BossRoom
    class FightRoom
    class StarterRoom
    class Door_Visuals
    class Door
    class BaseRoom_PassageSocket
    class BaseRoom_DoorBuildInfo
    class BaseRoom {
        +Initialize(MapSystem* mapSystem) void
        +OnPlayerEnter() void
        +OnPlayerLeave() void
        +OpenAllDoors() void
        +CloseAllDoors() void
        +AddMob(std::shared_ptr~Mob~ mob) void
    }
    class PortalRoom
  }
  namespace External {
    class Util_GameObject
  }
  BaseRoom <|-- FightRoom
  BaseRoom <|-- PortalRoom
  BaseRoom <|-- RewardRoom
  BaseRoom <|-- StarterRoom
  FightRoom <|-- BossRoom
  GenChamber <|-- GenBossChamber
  GenChamber <|-- GenFightChamber
  GenChamber <|-- GenPortalChamber
  GenChamber <|-- GenRewardChamber
  MapPiece <|-- Door
  MapPiece <|-- RectMapArea
  RectMapArea <|-- BaseRoom
  RectMapArea <|-- Gangway
  Util_GameObject <|-- MapPiece
```

### 程式技術

### 使用到 AI/AI Agent 的部分 (沒有用到者，不需要寫這篇)

## 結語

### 問題與解決方法
### 自評

| 項次 | 項目                   | 完成 |
|------|------------------------|-------|
| 1    | 這是範例 |  V  |
| 2    | 完成專案權限改為 public |    |
| 3    | 具有 debug mode 的功能  |    |
| 4    | 解決專案上所有 Memory Leak 的問題  |    |
| 5    | 報告中沒有任何錯字，以及沒有任何一項遺漏  |    |
| 6    | 報告至少保持基本的美感，人類可讀  |    |

### 心得
### 貢獻比例
