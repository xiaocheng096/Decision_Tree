# 决策树分类系统（单文件离线版）

## 项目简介

一个**单文件、零依赖、可离线运行**的决策树分类演示网页。所有内容（样式、脚本、训练好的模型、决策树 SVG 图）都内嵌在一个 HTML 文件中，双击即可在浏览器打开。

- 算法：信息增益（ID3 思想）二分决策树 + 悲观错误剪枝（PEP）
- 模型：训练后以 JSON 内嵌，页面运行时**只推理、不训练**
- 界面：深色“多元宇宙实验室”风格，含交互推演、路径高亮、数据与算法说明

## 功能说明

- **双数据集切换**
  - 动物分类图鉴：31 个样本、11 个二值特征、5 个类别、训练准确率 100%
  - 重生之人生选择：40 个样本、12 个二值特征、5 个类别、训练准确率 95.0%
- **交互式推演**：逐项设置“是 / 否”特征，或一键载入预设样本，点击“开始推演”得到分类结果。
- **可解释输出**：展示决策步数、路径节点数、叶节点样本数、分支纯度，以及逐步的判断路径。
- **决策树全景**：SVG 树图支持放大、缩小、适应；推演后自动切换到该叶节点对应的金色高亮路径图。
- **数据图谱**：展示训练样本表、类别说明（含样本数）与特征字典。
- **算法说明**：页面内给出熵、信息增益、PEP 公式及构建流程。

## 目录结构

```text
Decision_Tree/
├── README.md                       # 本说明文档
├── index.html                      # GitHub Pages 首页（与单文件版内容相同）
└── 决策树分类系统_单文件版.html     # 完整程序（HTML + CSS + JS + 内嵌模型）
```

单个 HTML 文件内部按以下顺序组织：

```text
<head>  <style>      主题变量与全部页面样式
<body>  <header>     顶部导航、离线状态标识
        <main>
          hero      数据集卡片与统计指标
          lab       特征输入面板 + 推演结果面板
          tree      决策树 SVG 全景与缩放控件
          data      训练样本、类别、特征字典
          method    算法原理说明
        </main>
        <footer>
<script> DATA         两个数据集的模型 JSON（features/classes/samples/stats/config/tree/svg）
         渲染函数     renderModel / renderFeatures / renderSamples / renderClasses / renderDict / renderResult
         推理函数     localPredict → predict
         事件初始化   DOMContentLoaded
```

## 安装与运行步骤

1. 克隆仓库：

   ```bash
   git clone https://github.com/xiaocheng096/Decision_Tree.git
   cd Decision_Tree
   ```

   也可直接下载 `决策树分类系统_单文件版.html`，无需安装任何依赖。

2. 运行（任选其一）：

   - 直接双击 `决策树分类系统_单文件版.html`，用默认浏览器打开；
   - 或在本目录启动本地服务：

     ```bash
     python -m http.server 8000
     ```

     然后访问 `http://localhost:8000/决策树分类系统_单文件版.html`。

3. 环境要求：支持 HTML5 / SVG / ES5+ 的现代浏览器（Chrome、Edge、Firefox、Safari）。无需 Node.js、Python、后端服务或网络连接。

## 使用说明

1. 在页面顶部数据集卡片中点击按钮，切换“动物分类图鉴”或“重生之人生选择”。
2. 在“特征面板”中逐项点击“否 / 是”开关；也可点击样本胶囊快速载入已有样本。
3. 点击金色按钮“开始推演”，右侧显示分类名称、分支纯度、决策步数及逐步判断路径。
4. 点击“在树中查看金色路径”跳转到决策树全景，查看本次推演经过的节点；用 `− / + / 适应` 调整显示比例。树图支持拖动平移：桌面端按住鼠标拖动、`Ctrl/⌘ + 滚轮` 缩放；手机端单指拖动、双指缩放。
5. 点击“重置”清空输入并恢复初始树图。

### 运行流程

```text
DOMContentLoaded
  → switchDataset('animals')
    → renderModel()：更新统计、渲染特征/样本/类别/字典、加载基础树图
  → 用户设置特征或载入样本 → state.values
  → predict() → localPredict()：从根节点按特征值沿 tree.children 走到叶节点
    → renderResult()：输出分类、置信度与决策路径
    → 切换 svg.leaves[leaf.nodeId]，高亮金色路径
  → reset() 可回到初始状态
```

## 依赖关系

- **运行时依赖：无。** 纯原生 HTML / CSS / JavaScript；无 CDN、无第三方库、无 `fetch` / 网络请求。
- 决策树图像以 SVG 字符串形式存放在 `DATA.<dataset>.svg`（`base` 为全树，`leaves` 为各叶节点高亮版本）。
- 训练参数内嵌于 `DATA.<dataset>.config`：`maxDepth=6`、`minSamples=2`、`minGain=0.0001`、`prune=true`；训练器本身不在本仓库中。

## 注意事项

- 页面中的“训练准确率”是内嵌模型的训练集指标，不代表对新数据的泛化能力。
- “分支纯度”是叶节点中多数类样本占比，不是经过校准的概率。
- 当前版本**没有**模型训练、CSV 导入或导出功能；页面文案中提到的“通过 CSV 扩展”仅为说明性文字。
- 已知显示问题：推理结果里的“决策路径”说明文本取自 `yes_text / no_text`，而内嵌特征字段只有 `yes / no`，因此各步骤的说明文字为空；分类结果与路径高亮不受影响。
- 若需部署到 GitHub Pages，请将 HTML 另存为 `index.html`；当前中文文件名在部分静态托管环境下需要 URL 编码。
- “重生之人生选择”数据集为教学构造，其人生分支仅供演示，不构成现实建议。
