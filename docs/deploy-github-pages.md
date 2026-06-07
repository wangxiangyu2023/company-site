# 公司官网部署到 GitHub Pages — 操作说明

> 用途:为「上海一抚尺科技有限公司 / Shanghai Yifuchi Technology Co., Ltd.」搭建一个海外可访问的中英双语官网,供 Apple 组织开发者账号审核使用。
> 文件:`index.html`(中英双语单页,已包含公司全称、业务介绍、核心服务、联系邮箱、隐私政策)。

---

## 给 Claude Code 的任务指令

请帮我初始化一个 GitHub 仓库并部署到 GitHub Pages,完成后给我最终可访问的网址。具体要求:

1. 在当前目录(包含 `index.html`)初始化 git 仓库
2. 用 `gh` CLI 在 GitHub 上新建一个 **public** 仓库,名称建议 `company-site`(若被占用可加后缀)
3. 提交 `index.html` 并推送到 `main` 分支
4. 通过 `gh` CLI 或 API 开启 GitHub Pages(source: branch `main`,目录 `/` root)
5. 输出最终访问网址(形如 `https://<用户名>.github.io/company-site/`),并确认可访问

> 注意:GitHub Pages 免费版要求仓库为 **public**;若需私有仓库需 GitHub Pro。

---

## 部署前必须确认/修改的内容

在推送前,请检查并按需修改 `index.html`:

- [ ] **联系邮箱**:把文件中所有 `contact@example.com` 替换为真实公司邮箱(共出现在「联系我们」区、隐私政策两处、页脚,合计若干处)
- [ ] **公司英文名**:当前用的是 `Shanghai Yifuchi Technology Co., Ltd.`(拼音标准译法)。必须与邓白氏(D-U-N-S)申请、工商登记的英文名保持一致,不一致会导致 Apple 交叉核对失败
- [ ] **文件名**:保持 `index.html` 不变(Pages 默认以此为首页)

---

## 手动部署步骤(命令行参考)

如果手动操作,在 `index.html` 所在目录执行:

```bash
# 1. 初始化仓库
git init
git add index.html
git commit -m "company site"
git branch -M main

# 2. 用 gh CLI 创建 public 仓库并推送(推荐)
gh repo create company-site --public --source=. --remote=origin --push

# 或:手动创建后推送
# git remote add origin https://github.com/<你的用户名>/company-site.git
# git push -u origin main

# 3. 开启 GitHub Pages(从 main 分支 root 目录部署)
gh api -X POST repos/<你的用户名>/company-site/pages \
  -f "source[branch]=main" \
  -f "source[path]=/"

# 4. 查看 Pages 状态与网址
gh api repos/<你的用户名>/company-site/pages
```

部署完成后,网址形如:

```
https://<你的用户名>.github.io/company-site/
```

等 1–2 分钟生效,浏览器打开确认正常显示即可。

---

## 部署后验证清单

- [ ] 网址能正常打开,页面完整显示
- [ ] 联系邮箱显示正确(已替换 example 占位)
- [ ] 中英文公司名显示正确、与工商/邓白氏一致
- [ ] 把该网址填入 Apple 开发者账号申请的「网站」栏

---

## 关于备案与海外访问

- GitHub Pages 服务器在海外,**Apple 审核可正常访问,无需 ICP 备案**。
- 国内 ICP 备案可并行办理:等阿里云境内服务器 + 备案号下来后,把相同内容部署到境内服务器、绑定备案域名,并在 `index.html` 页脚注释处(`<!-- ICP 备案号... -->`)填入备案号即可。

## (可选)绑定自定义域名

如需用自己的域名(更正式):

1. GitHub 仓库 **Settings → Pages → Custom domain** 填入你的域名
2. 到域名 DNS 处添加一条 `CNAME` 记录,指向 `<你的用户名>.github.io`
3. 等待 DNS 生效并启用 HTTPS

> 自定义域名同样不影响海外访问;若该域名同时要做国内访问,再走 ICP 备案流程。
