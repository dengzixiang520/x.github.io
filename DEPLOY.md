# GitHub Pages 部署上传步骤

## 前提条件

- 已安装 [Node.js](https://nodejs.org/)（v18 或以上）
- 已安装 [Git](https://git-scm.com/)
- 拥有 GitHub 账号，且仓库 `x.github.io` 已创建

## 一、本地构建

```bash
# 1. 进入项目目录
cd noise-portfilo

# 2. 安装依赖（首次运行）
npm install

# 3. 本地预览（可选，浏览器打开 http://localhost:3000）
npm run dev

# 4. 构建生产版本
npm run build
```

构建产物在 `dist/` 目录中。

## 二、部署到 GitHub Pages

### 方式 A：手动上传（推荐）

```bash
# 1. 克隆你的 GitHub Pages 仓库
git clone https://github.com/dengzixiang520/x.github.io.git
cd x.github.io

# 2. 清空仓库（保留 .git 目录）
find . -not -path './.git' -not -path './.git/*' -not -name '.git' -delete

# 3. 复制构建产物
cp -r /path/to/noise-portfilo/dist/* .

# 4. 提交并推送
git add -A
git commit -m "更新博客"
git push origin main

# 5. 同时更新 master 分支（如果 GitHub Pages 配置的是 master）
git push origin main:master --force
```

### 方式 B：使用 gh-pages 分支

在 GitHub 仓库 Settings → Pages → Source 中选择 "Deploy from a branch"，分支选 `main`（或 `master`），目录选 `/ (root)`。

### 方式 C：GitHub Actions 自动部署

创建 `.github/workflows/deploy.yml`：

```yaml
name: Deploy to GitHub Pages
on:
  push:
    branches: [main]
jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: 18
      - run: npm ci
      - run: npm run build
      - uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./dist
```

## 三、验证

推送后等待约 1-2 分钟，访问：

👉 https://dengzixiang520.github.io/x.github.io

如果看到 404，检查：
1. GitHub 仓库 Settings → Pages → Branch 是否选择了正确的分支
2. 分支根目录是否有 `index.html`
3. 如果使用自定义域名，检查 CNAME 记录

## 四、更新文章

1. 编辑 `src/data/blogData.js`，按格式添加新文章
2. 将文章配图放入 `public/` 对应目录
3. 重新构建：`npm run build`
4. 按上述步骤重新部署
