# Le Chatelier Lenz 's Blog

> Le Chatelier Lenz，取自化学家勒夏特列和物理学家楞次

## 说明

- 本项目为LeChatelier-Lenz的个人博客，基于Hexo框架，结合keep主题，并使用Github Action进行部署
- 涵盖范围主要是CS领域的各种知识，包括神经网络、区块链、Go语言学习等等
- 也会包含一些实操性的经验心得，比如ubuntu使用、hexo+github action网页部署等等【待完善】
- 未来也可能添加一些专栏/视频/文章的推荐

## 一些血泪史

### 文章更新时间问题（还未完全解决）

- 利用CI(Github Action)进行自动部署时，会错误地把自动部署的git提交时间作为所有文章的更新时间，从而造成问题；
- solution：
  - 在 `.github/workflows/`下的某个负责构建部署的 `.yml`文件中的install步骤之前添加如下内容：

    ```xml
    - name: Restore file modification time
            run: find source/_posts -name '*.md' | while read file; do touch -d "$(git log -1 --format="@%ct" "$file")" "$file"; done
    ```

### Latex渲染问题【2024/10/10】

- 详见keep官方文档的解释[MathJax 数学公式 | Keep 主题使用手册 (xpoet.cn)](https://v3.keep-docs.xpoet.cn/writing/mathjax.html#latex-%E6%B8%B2%E6%9F%93%E9%97%AE%E9%A2%98)

### 图像呈现问题【2024/10/11】

- 首先需要在 `_config.yml`文件中更改 `post_asset_folder: true`, 保证每一次创建新文章时都会有同名资源文件夹
- `npm install hexo-asset-img --save` 安装图像路径处理的依赖包，可以使得远程部署后图像能够正常处理
- 最后需要确保本地路径设置时的正确性，需要按照：
  `<img src="{同名文件夹目录名}/{图片名字} />"的格式进行更改`
- `hexo-renderer-markdown-it-plus` 的抽象渲染问题：inline latex在 $$的末尾前要多一个空格，否则会出现渲染两遍的问题
