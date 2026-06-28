# Giscus 评论 + 阅读量统计配置教程

---

## 一、Giscus 评论系统配置

Giscus 是一个基于 GitHub Discussions 的评论系统，免费、开源、无广告。

### 1.1 前置条件

- 你的 GitHub 仓库必须是 **公开的（Public）**
- 仓库已启用 Discussions 功能

### 1.2 启用 Discussions

1. 打开你的 GitHub 仓库：https://github.com/dengzixiang520/x.github.io
2. 点击 **Settings** 标签
3. 下拉找到 **Features** 部分
4. 勾选 **Discussions** 复选框
5. 页面会自动保存

### 1.3 安装 Giscus App

1. 访问 https://github.com/apps/giscus
2. 点击绿色的 **Install** 按钮
3. 选择 **Only select repositories**
4. 在下拉菜单中选择 `dengzixiang520/x.github.io`
5. 点击 **Install**

### 1.4 获取配置参数

1. 访问 https://giscus.app/zh-CN
2. 在「仓库」输入框中填写：`dengzixiang520/x.github.io`
3. 页面会自动验证并显示你的仓库
4. 在「页面 ↔️ discussion 映射关系」选择：**路径名（pathname）**（确保不同文章评论隔离）
5. 在「Discussion 分类」下拉框中选择一个分类（推荐选 **Announcements** 或 **General**）
6. 在「特性」中勾选你需要的功能：
   - ✅ 评论框在上方
   - ✅ 反应（点赞等）
   - ✅ 懒加载评论
7. 在「主题」中选择与博客匹配的主题（我们的博客会自动切换深浅色）
8. 页面下方会自动生成配置代码，复制以下两项：
   - `data-repo-id`（仓库 ID，类似 `R_kgDO...`）
   - `data-category-id`（分类 ID，类似 `DIC_kwDO...`）

### 1.5 填入配置

打开 `src/components/GiscusComments.jsx`，找到这段代码：

```javascript
const GISCUS_CONFIG = {
  repo: "dengzixiang520/x.github.io",       // 你的仓库名
  repoId: "R_kgDOOOOOOO",                     // ← 替换为你的仓库 ID
  category: "Announcements",                  // ← 替换为你的分类名
  categoryId: "DIC_kwDOOOOOOO",               // ← 替换为你的分类 ID
};
```

将 `repoId` 和 `categoryId` 替换为你在 step 8 获取的值，然后重新构建部署。

---

## 二、不蒜子（Busuanzi）阅读统计配置

不蒜子是一个极简的网页访问计数器，**无需注册、无需配置后端**，开箱即用。

### 2.1 原理说明

不蒜子通过在页面中插入一个 `<script>` 标签和服务端约定的 `<span>` 标签，自动统计每个独立 URL 的页面访问量（PV）。

- 每个 URL 独立计数
- 同一用户短时间内多次访问不计入
- 仅统计页面展示次数，不是独立访客（UV）

### 2.2 已完成的配置

项目中已完成以下配置，无需额外操作：

1. **`index.html`** 已引入不蒜子脚本：

```html
<script async src="//busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js"></script>
```

2. **文章卡片**（`ArticleCard.jsx`）中已添加阅读量显示：

```jsx
<span id={pagePath} class="busuanzi_container_page_pv" style="display:none">
  阅读：<span class="busuanzi_value_page_pv"></span>
</span>
```

3. **文章详情页**（`ArticlePage.jsx`）中标题下方已添加阅读量显示。

### 2.3 工作原理

- 每个文章页面有一个唯一路径（如 `/blog/my-youth`）
- 不蒜子脚本自动向服务端发送该路径
- 服务端返回该路径的总访问次数
- 页面上的 `<span class="busuanzi_value_page_pv">` 自动显示数字

### 2.4 常见问题

**Q: 阅读量不显示？**
A: 检查浏览器是否屏蔽了第三方脚本（广告拦截插件可能拦截 busuanzi）。

**Q: 想要显示站点总访问量？**
A: 在任意位置添加：
```html
<span id="busuanzi_container_site_pv">
  本站总访问量 <span id="busuanzi_value_site_pv"></span> 次
</span>
```

**Q: 想换成其他统计服务？**
A: 可以替换为：
- **LeanCloud**：需要注册账号，API 更稳定
- **Umami**：需要自建或使用 Umami Cloud
- **Google Analytics**：功能全面但加载较慢

替换时只需修改 `index.html` 中的脚本引用和相关组件中的 span 标签即可。

---

## 三、配置检查清单

重新构建部署前，确认以下项目：

- [ ] GitHub 仓库为 Public
- [ ] 仓库已启用 Discussions
- [ ] 已安装 Giscus GitHub App
- [ ] 已在 giscus.app 获取 repoId 和 categoryId
- [ ] 已将配置填入 `GiscusComments.jsx`
- [ ] 已运行 `npm run build` 重新构建
- [ ] 已推送 `dist/` 目录到 GitHub Pages 仓库

全部完成后，访问博客文章详情页，评论区应正常显示 Giscus 界面，首页文章卡片应显示阅读数字。
