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

如果没有该目录，请手动创建：

```bash
mkdir -p public
```

推荐的目录结构（可根据实际需求调整）：

```text
public/
  data/        # 各类业务数据：JSON / GeoJSON / CSV 等
  images/      # 图片资源：图标、底图、图例等
  config/      # 配置类 JSON：图层配置、样式配置等
  tiles/       # 可选：地图切片 / 栅格数据等
```

在代码中通常类似这样访问数据：

```js
// 示例：从 public/data 目录读取数据
fetch('public/data/example.json')
  .then(res => res.json())
  .then(data => {
    // 在地图或场景上渲染数据 …
  });
```

> ⚠️ **务必确保**：`main.js` 中使用的路径（例如 `public/data/xxx.json`）与实际文件所在路径一致，否则会出现 404。

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

---

## 数据存储约定：`public/` 目录

为了避免跨域和路径问题，**建议所有需要在前端通过 `fetch` / `XMLHttpRequest` 加载的静态数据，都放在 `public/` 目录下**，例如：

```text
public/
  data/
    ships.geojson
    ports.json
    stats.csv
  images/
    logo.png
    legend.png
  config/
    layers.json
    styles.json
  tiles/
    {z}/{x}/{y}.png   # 若有自定义瓦片
```

在本地开发环境下，对应访问路径为：

* `http://localhost:8080/public/data/ships.geojson`
* `http://localhost:8080/public/images/logo.png`
* `http://localhost:8080/public/config/layers.json`

只要 `http-server` 的根目录是项目根目录（`.`），`public/` 下的文件都能以上述路径访问。

在生产环境中，也可以将整个项目（或构建后的目录）放到 Nginx / Apache / OSS / S3 等静态服务器上，`public/` 的路径规则同理。

---

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
  public/              # 建议创建，用于存放运行时数据（需自行添加）
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
* 从 `public/` 目录加载数据

  * 通过 `fetch('public/data/xxx.json')` 读取业务数据、配置文件；
  * 将数据转换为图层（点、线、面）、标记或其他可视元素。
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

#### `project/`

* 存放与当前项目强关联的资源，例如：

  * 某个具体场景的配置（如图层列表、样式配置）；
  * 示例数据；
  * 特定业务脚本。

当项目变复杂时，可以在此目录下按功能再划分子目录（如 `config/`、`styles/`、`examples/` 等）。

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

#### `public/`（需手动创建）

* **运行时数据与静态资源目录**，供前端通过 HTTP 访问。
* 建议存放：

  * 业务数据：`public/data/*.json`、`*.geojson`、`*.csv` 等；
  * 图片：`public/images/*.png`、`*.jpg`；
  * 配置文件：`public/config/*.json`；
  * 大体量地图瓦片或栅格：`public/tiles/...`。

公共约定：**前端代码里用的路径以 `public/` 开头**，方便统一管理。

---

#### `node_modules/`

* 运行 `npm install` 后生成，包含所有第三方依赖包。
* **不要手动修改**，也不应提交到 Git（已在 `.gitignore` 中忽略）。

---

## 开发与二次扩展建议

1. **增加新的数据图层**

   * 将新数据放入 `public/data/`；
   * 在 `main.js` 中添加对应的 `fetch` 逻辑；
   * 将数据转换为 maptalks 图层或 Cesium 实体后加入场景。

2. **抽离可配置部分**

   * 将图层列表、颜色样式、阈值等抽成 JSON 放在 `public/config/`；
   * 由 `main.js` 动态读取配置并生成图层，可简化后续调参。

3. **引入/扩展 3D 场景（Cesium）**

   * 确保 `index.html` 正确引入 `scripts/Cesium/` 下的相关 JS/CSS；
   * 在 `main.js` 中初始化 `Cesium.Viewer`，根据需要从 `public/` 加载 3D Tiles、地形数据等；
   * 可实现 2D/3D 联动或单独的三维可视化场景。

4. **构建生产环境（可选）**

   * 当前项目可直接以静态文件部署；
   * 若后期代码复杂，可引入构建工具（如 Vite / Webpack / Rollup）对 `main.js` 等进行打包、压缩；
   * 打包结果通常输出到 `dist/`，再由静态服务器托管。

---

## 常见问题（FAQ）

### 1. 打开 `http://localhost:8080` 是空白页怎么办？

* 确认是通过 `http-server` 访问，而不是直接用浏览器打开 `index.html` 的 `file://` 路径；
* 打开浏览器控制台（F12 → Console / Network）：

  * 如果有资源 404，检查：

    * `public/` 目录是否存在；
    * 数据文件是否放对位置；
    * `main.js` 中的路径是否与实际一致。

---

### 2. 修改了 JS 或数据文件，刷新后没变化？

* 启动命令需要加 `-c-1` 禁用缓存：

  ```bash
  npx http-server . -p 8080 -c-1
  ```

* 或在浏览器中使用「强制刷新」：

  * Windows：`Ctrl + F5`
  * macOS：`Cmd + Shift + R`

---

### 3. 提示端口 8080 已被占用？

* 换一个端口即可，例如：

  ```bash
  npx http-server . -p 8081 -c-1
  ```

---

> 如果你在科研或业务中有更具体的场景（如海上目标监测、海事交通分析等），可以在本 README 顶部「项目简介」部分进一步补充说明，以便协作人员快速理解项目背景和用途。

```
::contentReference[oaicite:0]{index=0}
```
