# Solar System Billiards Sandbox / 太阳系桌球沙盒

[English](#english) | [中文](#中文)

---

## English

A 3D solar system physics sandbox built with **Three.js** + **Cannon.js**. Drag planets like a slingshot and watch **N-body gravity**, collisions, moons, wormholes, and shattering.

### Controls

| Action | Effect |
|--------|--------|
| Left-click + drag on a planet | Pull-back aiming (farther = more power) |
| Release left button | Launch; trajectory enters full physics simulation |
| Right-click + drag on empty space | Rotate camera |
| Mouse wheel | Zoom in/out |

### Tech Stack

- **Three.js r128** — 3D rendering
- **Cannon.js 0.6.2** — Rigid body physics
- **OrbitControls** — Camera

### Scale & Space (hard-realism layout)

- **Sun** \(M_\odot = 180{,}000\) — dominates gravity (~99.86% “feel” in-game)
- **Radii**: Earth ≈ \(R_\odot/20\); Jupiter/Saturn ≈ **11× / 9×** Earth; terrestrials are tiny next to the Sun
- **Orbits**: inner compact (**64–114**); outer giants **252–848** (Titius–Bode style); catalog motion at **`ORBIT_TIME_SCALE = 0.3`** (30% angular rate, easier to watch)
- Camera `maxDistance` **4200**, far clip **84k**; `MAX_LAUNCH_SPEED` **720** for cross-system slingshots
- **Wormholes** beyond Pluto (~**1028** units): BH mass **240 000**, WH mass **−55 000**, capture radius **72**, dedicated low softening + **3.5×** pull/repulse within **500** units

### Physics

#### N-body universal gravity

- `applyUniversalGravity()` replaces sun-only gravity
- All massive bodies attract: **Sun**, **9 planets**, **moons**, **debris**, **scattered asteroids**
- Pairwise \(F = G \frac{m_i m_j}{r^2}\) with softening
- **Black hole / white hole**: use `applyWormholeGravity()` with **`WORMHOLE_GRAV_SOFTENING = 3.5`** (not the global 22) so deep-space funnel works; **boost** inside 500 units
- **White hole** repulsion: negative mass; teleport spawn at **1.5× core radius** outward (avoids zero-vector at center)
- **Teleport** only for **launched** planets (`orbitMode === false`); capture sphere radius **72**
- Catalog orbits: \(v = \sqrt{GM/r} \times 0.3\); on `wakeFromOrbit()` velocity restored to **full** \(\sqrt{GM/r}\) for real N-body slingshots
- Sun stays fixed (kinematic); does not drift from forces

#### Momentum & HP

- Impact damage from reduced-mass kinetic energy \(K = \frac{1}{2}\mu v^2\)
- **Solid** planets: high brittleness, shatter into opaque icosahedron debris
- **Gas giants**: high HP, absorb ~55% damage, visible **collapse** (radius/mass shrink) before breakup; collisions eject **nebula plumes** (large transparent spheres, BackSide)
- **Gas–gas** or high-speed impact on gas: no blocky shards — dual **gas jets** at the impact point
- **Earth (solid-liquid)**: intermediate HP + ocean visual layer
- Mass from **density × volume** per body

#### Soft plasma ingestion (Sun contact)

- `ContactMaterial(physics, sun)` uses **`collisionResponse: false`** — no cement-like bounce; bodies **sink through** the photosphere
- **Multi-stage ablation** before accretion:
  1. Entering the solar volume starts `solarAblation` (not instant delete)
  2. Each frame: velocity ×**0.3** (70% plasma drag), radius shrinks, **white/yellow emissive** up to ~**14+** intensity
  3. Near the core (`dist < 0.2 R☉`): `accreteMassOntoSun` + destroy; gas giants trigger the mega-blast here

#### Stellar mass accretion & energy VFX

- Sun `mass` / radius grow as \(R \propto M^{1/3}\); live `getSunMass()` drives N-body orbits
- **Mega-blast** shock shells + ring use **`AdditiveBlending`** + `depthWrite: false` — overlapping waves **stack brightness** (no plastic overlap)
- Shock opacity fades **exponentially**; **plasma** debris uses additive glow + **helical flare** forces in `updateDebris` (prominence-like curl)

#### Roche limit & tidal disruption

- Each frame, solid / solid-liquid planets and moons are checked against **Sun**, **gas giants** (Jupiter-scale), and the **black hole**
- Roche radius: \(\max\bigl(1.2 R_{\mathrm{large}} (\rho_{\mathrm{large}}/\rho_{\mathrm{small}})^{1/3},\; 1.65\, R_{\mathrm{large}}\bigr)\)
- Inside the limit: rapid HP drain, **strip debris** along the body–source line, slow radius peel until shatter
- Gas giants are not tidally shredded (they resist Roche in this build)

#### Debris budget (no leak)

- Global cap **400** debris / **12** shockwaves; oldest pieces disposed (geometry/material cleaned when not shared)

#### Moons (parent–child)

| Parent | Moons |
|--------|--------|
| Earth | Moon |
| Mars | Phobos |
| Jupiter | Io, Europa, Ganymede |
| Saturn | Titan |

- Moons start on stable circular orbits around parent (\(v = \sqrt{GM/r}\) + parent velocity)
- **Dynamic N-body**: when parent is launched or disturbed, moons follow via mutual gravity
- Moons are **not draggable**; have their own HP and can shatter

#### Other systems

- Slingshot aim with **gravity-curved predict line** (multi-step integration)
- Wormhole teleport: black hole → white hole **repulsion safe zone** (not the singularity point)
- **Soft plasma sink** + staged melt + **additive** mega-blast; **Roche tidal shredding** near massive bodies
- Asteroid belt between Earth–Mars orbits (not draggable, scatter on impact)
- Orbiting planets use kinematic circular motion until disturbed; then dynamic + N-body

### Local Usage

Open `index.html` in a browser. CDN required for Three.js / Cannon.js / OrbitControls.

### Files

```
index.html   — Full application
README.md    — This file
```

---

## 中文

基于 **Three.js + Cannon.js** 的 3D 太阳系物理沙盒：拉弓发射行星，体验 **N-body 万有引力**、碰撞碎裂、卫星跟随与深空虫洞。

### 操作方式

| 操作 | 效果 |
|------|------|
| 左键按住行星拖动 | 拉弓瞄准，越远力度越大 |
| 松开左键 | 发射，进入完整物理模拟 |
| 右键拖动空白处 | 旋转视角 |
| 鼠标滚轮 | 缩放 |

### 空间比例（硬核拟真）

- **太阳质量 180000**，半径 36；**地球半径 = 太阳/20**，木星约为地球的 **11.2 倍**
- **内圈**（水金地火 **64–114**）紧凑，**外圈**（木土天海冥 **252–848**）指数级拉开
- 公转展示 **`ORBIT_TIME_SCALE = 0.3`**；发射后 `wakeFromOrbit` 恢复完整 \(\sqrt{GM/r}\)
- 虫洞在冥王星外（~1028）：BH **240000** / WH **−55000**，专用引力软化 + 近场增强，捕获 **72**
- 白洞传送至出口安全区；预测线已同步虫洞引力模型

### 物理系统

#### 全天体万有引力（N-body）

- 使用 `applyUniversalGravity()`，不再仅用太阳单向引力
- 太阳、九大行星、卫星、碎片、被撞飞的小行星均参与 \(F=G\frac{m_i m_j}{r^2}\)
- **黑洞**：固定位置、超大质量，吸引周围天体
- **虫洞失效修复**：原先全局软化 22 在千级距离上把虫洞引力抹平；现虫洞独立软化 **3.5**、质量加大、捕获圈 **72**
- **白洞**：负质量斥力；传送落点在背向太阳方向 **1.5×核心半径** 安全区；仅**已发射**行星可触发传送
- **公转降速**：`circularOrbitSpeed(..., true)` ×0.3；拖拽发射后 `wakeFromOrbit` 恢复物理速度 \(\sqrt{GM/r}\)
- 太阳固定不动，吸收引力但不漂移

#### 动量定理与 HP

- 碰撞伤害由质心系相对动能 \(K=\frac{1}{2}\mu v^2\) 换算
- **固态行星**：脆、HP 归零 → 不透明坚硬碎屑
- **气态巨行星**：HP 厚、吸收约 55% 伤害，碰撞时 **体积塌陷**（半径/质量下降）；喷出 **星云状** 大球半透明气流（BackSide），气–气对撞为双流喷射
- **地球（固液）**：中等 HP + 海洋视觉层
- 质量 = **密度 × 体积**

#### 软等离子吞噬（太阳接触）

- 太阳 `ContactMaterial` 设置 **`collisionResponse: false`**：撞击**不反弹**，直接陷入等离子体
- **多阶段熔融**（`updateSolarAblation`）：
  1. 进入光球层后开始消融，不会一触即没
  2. 每帧速度保留 **30%**（70% 等离子阻力）、半径急速缩小、**白热自发光**（强度 14+）
  3. 深入核心（`dist < 0.2 R☉`）才 `accreteMassOntoSun` 并销毁；气态巨星在此时触发超新星

#### 质量兼并与白炽能量 VFX

- 兼并后太阳变大，N-body 使用实时 `getSunMass()`
- 激波球/环与 **plasma** 碎片：`AdditiveBlending` + `depthWrite: false`，能量叠加越亮越白炽
- 激波 **指数淡出**；等离子碎片在 `updateDebris` 中带 **日珥式螺旋扰动**

#### 洛希极限潮汐撕裂

- 每帧检测固态/固液行星与卫星 vs **太阳、气态巨行星、黑洞**
- 破坏距离：\(\max(1.2 R_{\mathrm{大}}(\rho_{\mathrm{大}}/\rho_{\mathrm{小}})^{1/3},\; 1.65 R_{\mathrm{大}})\)
- 进入极限：HP 快速流失、沿连线 **剥屑**、半径被撕薄直至碎裂
- 气态巨行星不参与被撕（仅作为潮汐源）

#### 碎片与内存

- 碎片上限 **400**、激波 **12**；超限淘汰并 `dispose` 材质/几何，共享几何体不重复释放

#### 卫星系统

| 母星 | 卫星 |
|------|------|
| 地球 | 月球 |
| 火星 | 火卫一 |
| 木星 | 木卫一、木卫二、木卫三 |
| 土星 | 土卫六 |

- 初始速度 = 母星速度 + 绕母星切向轨道速度，保证稳定公转
- 母星被拖拽发射或碰撞改轨后，卫星由 **N-body 引力自然尾随**
- 卫星不可拖动，有独立 HP，可被撞击摧毁

#### 其他

- 瞄准 **预测线** 经多步引力积分，显示弯曲轨迹（引力弹弓预判）
- 黑洞 → 白洞 **排斥安全区**传送；软吞噬 / additive 超新星 / 洛希撕裂；小行星带位于地火之间
- 未扰动行星：运动学圆轨道；发射后：动态刚体 + 万有引力

### 本地运行

浏览器直接打开 `index.html`，需能访问 CDN。

### 文件结构

```
index.html   — 完整应用
README.md    — 本文件
```
