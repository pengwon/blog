---
title: hexo静态网站部署到腾讯云cos
date: 2023-02-21 14:04:46
categories:
tags:
  - hexo
  - github
  - ci/cd
---

hexo支持很多部署方案，最直接的就是部署在GitHub Pages服务上，国内gitee、coding等代码托管平台也都支持静态网站服务，而且免费。

但是GitHub在国内访问不太稳定，国内的代码托管平台资源和服务也不太稳定，后来想了想，干脆通过云存储部署吧。

云存储有很多选择，而且新用户往往都有很多优惠包，甚至免费额度，有很多人都选择七牛云做图床，就是七牛云的免费额度真是良心，小站基本够用。看完几家国内的云服务商计费方式后，最终选择了腾讯云cos对象存储服务。一是背靠大厂，服务相对稳定，二是价格尚可，虽不免费，但可接受。而且像阿里云、腾讯云这些大服务商，很多一条龙服务，比如网站备案、证书等等，在一个控制台里就全操作了，不用跳来跳去，比较省心。

<!-- more -->

## 部署方案

- 运行环境：腾讯云COS + CDN
- 部署方式：GitHub Actions

## GitHub Actions

使用 GitHub Actions 将 Hexo 博客部署到腾讯云 COS 上，可以将博客静态文件存储在腾讯云上，实现静态文件的分发和加速，提高博客访问速度和用户体验。下面是具体的步骤。

### 1\. 创建腾讯云 COS 存储桶

首先需要在腾讯云控制台创建一个存储桶。登录腾讯云控制台，进入 COS 服务，点击“新建存储桶”，按照提示完成创建存储桶的操作。

### 2\. 申请腾讯云访问密钥

在腾讯云控制台中，点击右上角的账号头像，选择“访问管理”，进入访问管理控制台。在左侧菜单栏中选择“访问密钥”，然后点击“新建密钥”按钮，按照提示完成申请访问密钥的操作。

### 3\. 在 GitHub 上配置 Secrets

在 GitHub 仓库中，点击“Settings”按钮，进入仓库设置页面，选择“Secrets”选项卡。在 Secrets 页面中，点击“New repository secret”按钮，创建两个 Secrets：

*   `TENCENT_CLOUD_SECRET_ID`：腾讯云访问密钥的 Secret ID。
*   `TENCENT_CLOUD_SECRET_KEY`：腾讯云访问密钥的 Secret Key。
*   `COS_BUCKET`：腾讯云COS存储桶。
*   `COS_REGION`：腾讯云COS存储区域。

将刚刚申请的腾讯云访问密钥的 Secret ID 和 Secret Key 分别填写到上面对应 Secrets 中，存储桶和区域也要填上。

### 4\. workflows

可以使用腾讯云cos action进行部署，在仓库下新建`.github/workflows/cos-deploy.yml`文件，然后添加以下内容，就可以将hexo生成的页面部署到cos：

``` yml
env:
  TZ: Asia/Shanghai # 时区
  
on:
  push:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest
    name: A job to deploy blog.
    steps:
    - name: Checkout
      uses: actions/checkout@v3
      with:
        submodules: true # Checkout private submodules(themes or something else).
    
    # Caching dependencies to speed up workflows. (GitHub will remove any cache entries that have not been accessed in over 7 days.)
    - name: Cache node modules
      uses: actions/cache@v3
      id: cache
      with:
        path: node_modules
        key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}
        restore-keys: |
          ${{ runner.os }}-node-
    - name: Install Dependencies
      if: steps.cache.outputs.cache-hit != 'true'
      run: npm ci

    # Caching public
    - name: cache public
      id: cache-public
      uses: actions/cache@v3
      with:
        path: |
          public
          db.json
        key: ${{ runner.os }}-public-${{ hashFiles('**/db.json') }}
        restore-keys: |
          ${{ runner.os }}-public-

    # Hexo generate
    - name: Hexo generate
      id: generate
      run: |
        npx hexo generate

    # Deploy hexo blog website.
    - name: Deploy
      id: deploy
      uses: TencentCloud/cos-action@v1
      with:
        secret_id: ${{ secrets.TENCENT_CLOUD_SECRET_ID }}
        secret_key: ${{ secrets.TENCENT_CLOUD_SECRET_KEY }}
        cos_bucket: ${{ secrets.COS_BUCKET }}
        cos_region: ${{ secrets.COS_REGION }}
        local_path: public
        remote_path: /
        # Optional Set to true for cleaning files on COS path which are not existed in local path. Default is false.
        # clean: true
        # Optional Set to true for using accelerate domain to upload files. Default is false.
        # accelerate: true

    
    # Use the output from the `deploy` step(use for test action)
    - name: Get the output
      run: |
        echo "${{ steps.deploy.outputs.notify }}"
```
Workflow 文件中包含一个 Job，名为 `build-and-deploy`，这个 Job 包含四个步骤：

1. `Checkout`：使用 `actions/checkout` 动作从 GitHub 仓库中检出代码。
2. `Cache node modules`：使用 `actions/cache` 动作安装 Node.js 运行环境和hexo相关依赖。
3. `cache public`：缓存hexo生成的静态文件。
4. `Hexo generate`：使用 Hexo 生成静态文件。
5. `Deploy`：使用腾讯云官方提供的 `TencentCloud/cos-action` 动作将生成的静态文件部署到 COS 存储桶中。

腾讯云也提供了非常好用的cli工具，只需几条命令就可以上传文件到cos，所以也可以使用cli工具部署：

``` yml
# workflow
name: Blog

on:
  push:
    branches:
      - master

jobs:
  deploy:
    name: Deploy Blog
    runs-on: ubuntu-latest
    env:
      TZ: Asia/Shanghai

    steps:
    # check it to your workflow can access it
    # from: https://github.com/actions/checkout
    - name: Checkout Repository master branch
      uses: actions/checkout@v2
      with: 
        ref: 'master'
        submodules: true

  # from: https://github.com/actions/setup-node
    - name: Setup Node.js
      uses: actions/setup-node@master
      with: 
        node-version: "14.x"

    - name: Yarn Install Cache
      uses: c-hive/gha-yarn-cache@v1

    - name: Install Dependencies
      run: yarn install

    ## generate files
    - name: Generate Hexo Site Public Files & Create Files for Blog Assets
      run: yarn build

    ## deploy to tencent cos
    - name: Deploy to Tencent COS
      env:
        SECRET_ID: ${{ secrets.TENCENT_CLOUD_SECRET_ID }}
        SECRET_KEY: ${{ secrets.TENCENT_CLOUD_SECRET_KEY }}
        BUCKET: ${{ secrets.COS_BUCKET }}
        REGION: ${{ secrets.COS_REGION }}
      run: |
        sudo pip install coscmd
        coscmd config -a ${SECRET_ID} -s ${SECRET_KEY} -b ${BUCKET} -r ${REGION}
        coscmd upload -rs --delete ./public/ / -f
```

命令 `coscmd upload -rs --delete ./public/ / -f` 的意思是比较当前 COS 存储桶内的文件，如果发生变更则更新，如果存储桶中存在但 public 目录中不存在，则删除对应的文件，`coscmd` 的具体使用方法可以参考[腾讯云官方文档](https://cloud.tencent.com/document/product/436/10976)。

### 5\. 将 workflow 文件提交到 GitHub 仓库

将刚刚创建的 workflow 文件提交到 GitHub 仓库中，GitHub Actions 会自动检测并执行该 Workflow。可以通过在 GitHub 仓库中的“Actions”选项卡查看 Workflow 的执行情况。

至此，使用 GitHub Actions 将 Hexo 博客部署到腾讯云 COS 上的步骤已经完成。每次提交代码后，GitHub Actions 会自动构建和部署静态文件到腾讯云上，无需手动操作，非常方便。

## 开启cos静态网站功能

将网站文件上传到 COS 还不够，需要开启 COS 的静态网站功能，打开存储桶 -> 基础配置 -> 静态网站，按照下图所示进行配置：

![](https://imgs.boringhex.top/blog/20230221153646.png)

这时就可以通过图中的访问节点提供的域名来访问网站了，当然也可以绑定自定义域名，前提是域名已经备案。

## cdn加速

### 创建域名

如果域名有备案的话，同时可以使用腾讯云 CDN 加速 COS 静态网站，配置十分简单，在 CDN 页面中创建一个域名，按下图所示进行配置：

![](https://imgs.boringhex.top/blog/20230221154433.png)

### 配置缓存

缓存配置十分重要，由于站点是静态站点，网站的内容变化比较少，为了降低 CDN 的回源请求，一定要配置节点缓存策略，可以参考我的配置如下，其中浏览器缓存可以按需配置：

![](https://imgs.boringhex.top/blog/20230221154520.png)

注意：这里不用担心节点缓存时间配置的过长会导致页面无法更新，后面我会讲如何配置缓存的自动刷新。

关于 CDN 配置的更多细节就不讲了，都很简单，一看就懂。

## cdn节点缓存自动刷新

### 官方方案

由于上一步中配置了 CDN 节点的缓存策略，请求只要能够命中缓存，就不会进行回源请求了，这会导致我们的页面更新不能及时的展现给用户，因此需要考虑如何进行 CDN 节点的缓存自动刷新。

腾讯云官方给我们提供了一个解决方案，可以在 COS 存储桶的函数计算 ->CDN 缓存刷新函数中配置一个函数，可参考下图所示配置：

![](https://imgs.boringhex.top/blog/20230221154652.png)

但这个方案存在一个问题，由于我们的静态网站有默认索引页面 index.html，而官方提供的这个函数只会刷新对应的文件的 URL，而不会刷新索引 URL，例如 https://blog.boringhex.top/index.html 这个文件，通常我们的请求是 https://blog.boringhex.top/，因此官方的方案针对于静态网站来说不算完美。

### 优化方案

可以通过简单修改官方的函数来解决这个问题，点击刚刚创建的 CDN缓存刷新函数列表中的函数名称，可以跳转的函数的编辑页面：

![](https://imgs.boringhex.top/blog/20230221154837.png)

将 index.js 文件内容替换为下面的代码，最后点击右上角的 “部署” 按钮即可：

``` js
'use strict'

const CosSdk = require('cos-nodejs-sdk-v5')
const CdnSdk = require('./common/CdnSdk')
const CdnRefreshTask = require('./common/CdnRefreshTask')
const {
  getParams,
  getObjectUrl,
  logger,
  getLogSummary
} = require('./common/utils')

exports.main_handler = async (event, context, callback) => {
  /**
   * parse param from event and process.env
   */
  const { objects, cdnHosts, secretId, secretKey, token } = getParams(event)

  logger({
    title: 'param is parsed success, param as follow: ',
    data: { objects, cdnHosts, event }
  })
  /**
   * init cos instance
   */
  if (!secretId || !secretKey || !token) {
    throw new Error(`secretId, secretKey or token is missing`)
  }

  const cdnSdkInstance = new CdnSdk({ secretId, secretKey, token })
  const cosInstance = new CosSdk({
    SecretId: secretId,
    SecretKey: secretKey,
    XCosSecurityToken: token
  })

  const taskList = objects.map(({ bucket, region, key }) => {
    const purgeUrls = [];
    // 主要变更内容在这个位置
    cdnHosts.forEach(host => {
      const tempUrl = getObjectUrl({
        cosInstance,
        bucket,
        region,
        key,
        origin: `${/^(http\:\/\/|https\:\/\/)/.test(host) ? '' : 'https://'}${host}`
      });
      purgeUrls.push(tempUrl);
      // 如果以 /index.html 结尾，则增加目录首页/
      // 例如 https://www.xxxx.com/index.html, 则增加 https://www.xxxx.com/
      if(tempUrl.lastIndexOf('/index.html') == (tempUrl.length - 11)){
        purgeUrls.push(tempUrl.substr(0, tempUrl.length - 10))
      }
    });
    return new CdnRefreshTask({
      cdnSdkInstance,
      urls: purgeUrls
    })
  })

  const taskResults = []
  for (const task of taskList) {
    const results = await task.runPurgeTasks()
    taskResults.push(...results)
  }

  logger({
    title: 'cdn refresh full logs:',
    data: taskResults
  })

  const { status, messages } = getLogSummary(taskResults)

  logger({
    messages: messages.map(item => item.replace(/\,\ /g, '\n'))
  })

  if (status === 'fail') {
    throw messages.join('; ')
  } else {
    return messages.join('; ')
  }
}
```
