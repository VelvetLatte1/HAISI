# HAISI

HAISI 是一个基于 **JavaScript** 的前端可视化项目，主要用于 **地图 / 空间数据的展示与交互**。  
项目采用纯静态页面形式（`index.html + main.js + 静态数据`），通过本地静态服务器即可运行，**无需后端服务**。

目前项目主要依赖：

- [maptalks](https://maptalks.org/)：2D 地图展示与空间可视化
- Cesium（位于 `scripts/Cesium/`）：3D 地球 / 场景展示（如使用）

---

## 环境与依赖

- **Node.js**：推荐版本 ≥ 18（需要支持 `npx`）
- **npm**：随 Node.js 自动安装
- **现代浏览器**：Chrome / Edge / Firefox 等

项目依赖在 `package.json` 中定义，例如：

- `maptalks`
- 其他前端库（见实际 `package.json`）

首次使用建议在仓库根目录执行：

```bash
npm install
# 如需手动安装 maptalks，可以执行：
# npm install maptalks --save
````

---

## 快速开始

### 1. 克隆仓库

```bash
git clone https://github.com/VelvetLatte1/HAISI.git
cd HAISI
```

### 2. 安装依赖

```bash
npm install
```

### 3. 准备数据目录 `public/`

项目约定：**运行时加载的数据统一存放在仓库根目录的 `public/` 文件夹**。



---

## 本地启动（使用 `npx http-server`）

本项目是纯静态站点，推荐使用 [`http-server`](https://www.npmjs.com/package/http-server) 启动本地开发服务器。

在项目根目录执行：

```bash
npx http-server . -p 8080 -c-1
```

参数说明：

* `.`：以当前目录作为网站根目录（包含 `index.html` 的目录）
* `-p 8080`：指定端口为 `8080`，可根据需要改成其他端口（如 `3000`、`8000`）
* `-c-1`：关闭缓存（cache），保证调试时浏览器每次都拉取最新 JS 和数据文件

启动成功后，在浏览器访问：

```text
http://localhost:8080
```

即可看到 HAISI 页面。

> 也可以选择全局安装：
>
> ```bash
> npm install -g http-server
> http-server . -p 8080 -c-1
> ```
>
> 或在 `package.json` 中添加快捷脚本（见下文）。

---

## 使用 npm 脚本启动（可选）

你可以在 `package.json` 中添加一个启动脚本，简化命令：

```jsonc
{
  "scripts": {
    "start": "http-server . -p 8080 -c-1"
  }
}
```

之后在项目根目录执行：

```bash
npm run start
```

即可启动项目。

## 项目结构与各文件说明

仓库根目录大致结构如下（以当前仓库为例）：

```text
HAISI/
  .idea/
  lib/
  node_modules/
  project/
  scripts/
    Cesium/
  public/              # 下载数据
  index.html
  main.js
  package.json
  package-lock.json
  README.md
  .gitignore
```

下面对主要文件和目录逐一说明。

### 顶层文件

#### `index.html`

* 项目的入口页面（HTML 模板）。
* 主要职责：

  * 提供基础 HTML 结构；
  * 定义地图、图表等挂载容器（如 `<div id="map">`、`<div id="app">` 等）；
  * 引入必要的 CSS / JS 文件（包括 `main.js`、Cesium、maptalks 等）；
  * 设置页面标题、基本样式。

常见修改点：

* 修改 `<title>` 更换页面标题；
* 新增按钮、图例面板、说明文字等 DOM 结构；
* 调整整体布局（比如左右分栏、顶部工具栏等）。

---

#### `main.js`

* 前端逻辑的**主入口文件**。

典型职责包括（视实际代码而定）：

* 初始化 2D/3D 地图或地球场景

  * 创建 maptalks Map / Cesium Viewer；
  * 设置底图、坐标系、初始视角等。
* 绑定交互逻辑

  * 地图缩放、拖拽、选中、鼠标悬停；
  * 图层开关、弹出信息框、侧栏信息联动等。
* 实现业务逻辑

  * 例如不同类型要素的分类展示、统计信息可视化、时间序列播放等。

> 如果你需要：
>
> * 新增/删除图层；
> * 替换或增加数据源；
> * 改变交互方式；
>   基本都需要修改 `main.js`（或从中拆分出的模块）。

---

#### `package.json`

* Node 项目的配置文件，主要包含：

  * 项目名称、版本、描述等元信息；
  * 依赖列表（`dependencies` / `devDependencies`）；
  * 脚本命令（`scripts`）。

可以根据需要：

* 增加 `start` 脚本（见前文）；
* 增加 `build`、`lint` 等脚本（如后续接入构建工具）。

---

#### `package-lock.json`

* 由 `npm` 自动生成，用于锁定依赖包的精确版本，保证不同环境依赖一致。
* 一般不需要手动编辑，建议与 `package.json` 一起提交。

---

#### `.gitignore`

* Git 忽略规则配置文件。
* 通常会忽略：

  * `node_modules/`
  * 日志文件
  * IDE 缓存/临时文件等

如后续在 `public/` 下引入非常大的原始数据，可以在这里选择性忽略相关路径，以控制仓库体积。

---

#### `README.md`

* 项目的说明文档（本文件）。
* 建议持续补充：

  * 更详细的业务背景（科研/生产场景）；
  * 数据格式说明；
  * 实验步骤或使用示例；
  * 论文引用格式（如有）。

---

### project

#### `front_page.html`

* 首页。

---

#### `museum.html`

* 电子博物馆页面。

---

#### `splat/load_gs_scene.html`

* 加载gs场景的示例。

---

#### `mesh/load_tiles.html`

* 加载mesh场景的示例。

---

### 目录说明

#### `.idea/`

* JetBrains 系列 IDE（如 IntelliJ IDEA / WebStorm）生成的工程配置目录。
* 包含运行配置、代码样式等 IDE 相关设置。
* 是否纳入版本控制可视团队协作习惯而定。

---

#### `lib/`

* 自定义工具库 / 封装模块目录，通常包含：

  * 通用工具函数（数学、统计、坐标转换等）；
  * 对 maptalks / Cesium 的二次封装（统一创建图层、统一样式接口等）；
  * 公共 UI 相关工具。

建议将**与具体业务相对无关、可复用**的代码放在这里，方便跨项目复用。

---

#### `scripts/Cesium/`

* 存放 **Cesium** 相关的脚本与资源，通常包括：

  * `Cesium.js`
  * `Widgets/` 目录及其 CSS/图片资源等。

如使用三维地球/三维场景：

* `index.html` 会从该目录引入 Cesium JS 和 CSS；
* `main.js` 中会创建 `Cesium.Viewer` 并加载相关数据（3D Tiles、地形等）。

一般无需频繁修改此目录内容，除非需要升级 Cesium 版本或调整加载方式 / 路径。

---

#### `public/`（需下载）

* **运行时数据与静态资源目录**，供前端通过 HTTP 访问。
* 建议存放：

  * 3dgs场景：`public/splat/*.splat` 等；
  * 图片：`public/images/*.png`、`*.jpg`；
  * mesh场景：`public/sanjiangkou/`、`public/jingdian_tiles/ship`。
  * 视频：`public/videos`

公共约定：**前端代码里用的路径以 `public/` 开头**，方便统一管理。

---

#### `node_modules/`

* 运行 `npm install` 后生成，包含所有第三方依赖包。
* **不要手动修改**，也不应提交到 Git（已在 `.gitignore` 中忽略）。

---

```
::contentReference[oaicite:0]{index=0}
```
