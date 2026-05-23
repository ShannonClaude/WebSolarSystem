# Solar System Billiards Sandbox / 太阳系桌球沙盒

[English](#english) | [中文](#中文)

---

## English

A 3D solar system physics sandbox built with Three.js + Cannon.js. Drag planets to launch them like a slingshot and watch them collide like billiard balls.

### Controls

| Action | Effect |
|------|------|
| Left-click + drag on a planet | Pull-back aiming (farther = more power) |
| Release left button | Launch planet in the opposite direction |
| Right-click + drag on empty space | Rotate camera |
| Mouse wheel | Zoom in/out |

### Tech Stack

- **Three.js r128** — 3D rendering
- **Cannon.js 0.6.2** — Rigid body physics engine
- **OrbitControls** — Camera control

### Physics

- Real-time solar gravity acting on all planets
- Fully elastic collisions between planets (restitution 0.9)
- Max launch speed: 250 units/sec, with color-coded power indicator (green → yellow → red)
- 8 planets with initial positions calculated from real orbital periods
- Visual drag feedback: planet displaces slightly toward cursor during aim (max 20 units)

### Local Usage

Open `index.html` directly in a browser. No build tools required.

Dependencies are loaded from CDN, so an internet connection is needed.

### File Structure

```
index.html   — Full application (single file)
README.md    — This file
```

---

## 中文

基于 Three.js + Cannon.js 的 3D 太阳系物理沙盒，支持拖拽星球进行「拉弓式」发射，模拟桌球般的星球撞击。

### 操作方式

| 操作 | 效果 |
|------|------|
| 左键按住星球拖动 | 拉弓瞄准，拉得越远力度越大 |
| 松开左键 | 发射星球，沿反方向飞出 |
| 右键拖动空白处 | 旋转视角 |
| 鼠标滚轮 | 缩放距离 |

### 技术栈

- **Three.js r128** — 3D 渲染
- **Cannon.js 0.6.2** — 刚体物理引擎
- **OrbitControls** — 视角控制

### 物理特性

- 太阳引力实时作用于所有行星
- 行星间完全弹性碰撞（restitution 0.9）
- 发射速度最高 250 单位/秒，力度分级显示（绿→黄→红）
- 8 颗行星初始位置基于真实轨道周期计算
- 拖拽视觉反馈：瞄准时星球随鼠标方向小幅位移（最大 20 单位）

### 本地运行

直接用浏览器打开 `index.html` 即可，无需构建工具。

依赖从 CDN 加载，需要网络连接。

### 文件结构

```
index.html   — 完整应用（单文件）
README.md    — 本文件
```
