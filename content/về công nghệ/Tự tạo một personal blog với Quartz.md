---
tags:
  - tools
---

### Quartz là gì?


### Các bước tạo
Các bước tạo đầy đủ ở đây, nhưng mình sẽ note ra các bước mình đã làm để có thể tạo được một blog hoàn chỉnh.

#### 1. Cài đặt node và npm

```

```
#### Clone project
Bước đầu tiên, bạn cài node v18 và npm v vào máy

```
git clone https://github.com/jackyzha0/quartz.git
cd quartz
npm i
npx quartz create
```

#### Config
Truy cập file: `quartz.config.ts`


#### Building your Quartz

##### Build ở local

```
npx quartz build --serve
```

Truy cập link: http://localhost:8080/ để xem giao diện.

##### Deploy lên server

Tạo Github repository,
Khi tạo repo trên Github bạn không chọn như hình bên dưới nhé.

![[github-init-repo-options.png]]

```
# add your repository
git remote add origin REMOTE-URL 

# track the main quartz repository for updatesgit remote add upstream https://github.com/jackyzha0/quartz.git
```

```
git remote -v
```

```
npx quartz sync --no-pull
```


## GitHub Pages[§](https://quartz.jzhao.xyz/hosting#github-pages)

In your local Quartz, create a new file `quartz/.github/workflows/deploy.yml`.

quartz/.github/workflows/deploy.yml

```
name: Deploy Quartz site to GitHub Pages
 
on:
  push:
    branches:
      - v4
 
permissions:
  contents: read
  pages: write
  id-token: write
 
concurrency:
  group: "pages"
  cancel-in-progress: false
 
jobs:
  build:
    runs-on: ubuntu-22.04
    steps:
      - uses: actions/checkout@v3
        with:
          fetch-depth: 0 # Fetch all history for git info
      - uses: actions/setup-node@v3
        with:
          node-version: 18.14
      - name: Install Dependencies
        run: npm ci
      - name: Build Quartz
        run: npx quartz build
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v2
        with:
          path: public
 
  deploy:
    needs: build
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v2
```

1. Head to “Settings” tab of your forked repository and in the sidebar, click “Pages”. Under “Source”, select “GitHub Actions”.
2. Commit these changes by doing `npx quartz sync`. This should deploy your site to `<github-username>.github.io/<repository-name>`.
	https://hatcaiday.github.io/garden/