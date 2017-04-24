---
title: Hexo简易使用手册
date: 2017-04-17 13:14:53
tags:
    - Hexo
    - 使用教程
categories:
    - Hexo
toc: true
---

## 说明 ##

这篇文章只摘取了自己认为重要的信息，方便日后查找。具体请参考 [Hexo 官方文档](https://hexo.io/zh-cn/docs/index.html)。

## 开始使用 ##

### 安装 ###

#### 安装前提 ####

在安装 Hexo 之前，请确保系统已经安装了以下软件：

* [Node.js](http://nodejs.org/)
* [Git](http://git-scm.com/)

#### 安装 Hexo ####

    $ npm install -g hexo-cli

---

### 建站 ###

执行以下命令初始化博客项目，其中 `<folder>` 为博客目录名称。

    $ hexo init <folder>
    $ cd <folder>

### 文件说明 ###

新建完成后，生成的文件目录如下：

    ├── _config.yml
    ├── package.json
    ├── scaffolds
    ├── source
    |   ├── _drafts
    |   └── _posts
    └── themes

#### _config.yml ####

博客的配置文件，可以配置生成器、插件、站点信息等大部分参数。

#### scaffolds ####

模板文件夹。新建文章时，Hexo 会根据 scaffolds 来填充新建的 markdown 文件的内容。

#### source ####

存放用户资源的地方。除了 `_posts` 文件夹之外，开头命名为 `_` 的文件/文件夹和隐藏的文件将会被忽略。Markdown 和 HTML 文件会被解析并放到 `public` 文件夹，而其它文件会被挎贝过去。

#### themes ####

主题文件夹。Hexo 会根据主题来生成静态页面。

---

### 配置 ###

可以在 `_config.yml` 修改大部分配置。

#### 网站 ####

| 参数 | 描述 |
|:-----|:-----|
| `title` | 网站标题 |
| `subtitle` | 网站副标题 |
| `description` | 网站描述 |
| `author` | 您的名字 |
| `language` | 网站使用的语言 |
| `timezone` | 网站时区，Hexo 默认使用您电脑的时区。[时区列表](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones)。比如说：`America/New_York`，`Asia/Shanghai` |

其中，`description` 主要用于SEO，告诉搜索引擎一个关于您站点的简单描述，通常建议在其中包含您网站的关键词。`author` 参数用于主题显示文章的作者。

#### 网址 ####

| 参数 | 描述 | 默认值 |
|:-----|:-----|:-----|
| `url` | 网址 |  |
| `root` | 网站根目录 |  |
| `permalink` | 文章的永久链接格式 | `:year/:month:/day/:title/` |
| `permalink_defaults` | 永久链接中各部分的默认值 |  |

如果您的网站存在于子目录中，例如 `http://yoursite.com/blog`，则请将 `url` 设为 `http://yoursite.com/blog` 并把 `root` 设为 `/blog/`。

#### 目录 ####

| 参数 | 描述 | 默认值 |
|:---|:---|:---|
| `source_dir` | 资源文件夹，这个文件夹来存放内容。 | source |
| `public_dir` | 公共文件夹，这个文件夹用于存放生成的站点文件。 | public |
| `tag_dir` | 标签文件夹 | tags |
| `archive_dir` | 归档文件夹 | archives |
| `category_dir` | 分类文件夹 | categories |
| `code_dir` | include code 文件夹 | downloads/code |
| `i18n_dir` | 国际化（i18n）文件夹 | :lang |
| `skip_render` | 路过指定文件的渲染，您可使用 glob 表达式来匹配路径。 ||

#### 文章 ####

| 参数 | 描述 | 默认值 |
|:---|:---|:---|
| `new_post_name` | 新文章的文件名称 | :title.md |
| `default_layout` | 预设布局 | post |
| `auto_spacing` | 在中文和英文之间加入空格 | false |
| `titlecase` | 把标题转换为 title case | false |
| `external_link` | 在新标签中打开链接 | true |
| `filename_case` | 把文件名称转换为(1)小写或(2)大写 | 0 |
| `render_drafts` | 显示草稿 | false |
| `post_asset_folder` | 启动 Asset 文件夹 | false |
| `relative_link` | 把链接改为与根目录的相对位址 | false |
| `future` | 显示未来的文章 | true |
| `highlight` | 代码块的设置 |  |

默认情况下，Hexo 生成的超链接都是绝对地址。而相对链接无论用什么域名访问该站点都没有关系，这在进行反向代理时可能用到。通常情况下，建议使用绝对地址。

#### 分类&标签 ####

| 参数 | 描述 | 默认值 |
|:---|:---|:---|
| `default_category` | 默认分类 | uncategorized |
| `category_map` | 分类别名 |  |
| `tag_map` | 标签别名 |  |

#### 日期/时间 ####

Hexo 使用 [Moment.js](http://momentjs.com/)来解析和显示时间。

| 参数 | 描述 | 默认值 |
|:---|:---|:---|
| `date_format` | 日期格式 | YYYY-MM-DD |
| `time_format` | 时间格式 | H:mm:ss |

#### 分页 ####

| 参数 | 描述 | 默认值 |
|:---|:---|:---|
| `per_page` | 每页显示的文章量（0=关闭分页功能） | 10 |
| `pagination_dir` | 分页目录 | page |

#### 扩展 ####

| 参数 | 描述 |
|:---|:---|
| `theme` | 当前主题名称。值为 `false` 时禁用主题 |
| `deploy` | 部署部分的设置 |

关于 `deploy` 的详细设置请参考[此处](#部署)。

---

### Hexo 指令 ###

#### init ####

    $ hexo new [folder]

新建一个网站。如果 `folder` 为空，则默认在当前目录建立网站。

#### new ####

    $ hexo new [layout] <title>
    
新建一篇文章。如果没有指定 `layout`，默认使用 [_config.yml](#文章)中的 `default_layout` 参数代替。如果标题包含空格的话，请使用引号括起来。

#### generate ####

    $ hexo generate

或：

    $ hexo g

生成静态文件。

| 选项 | 描述 |
|:---|:---|
| `-d`, `--deploy` | 文件生成后立即部署网站 |
| `-w`, `--watch` | 监视文件变动 |

#### publish ####

    $ hexo publish [layout] <filename>

发表草稿。

#### server ####

    $ hexo server
    
或：

    $ hexo s
    
启动服务器。默认情况下，访问网址为：`http://localhost:4000/`。

Hexo 3.0 把服务器独立成了个别模块，您必须先安装 hexo-server 才能使用。

    $ npm install hexo-server --save

| 选项 | 描述 |
|:---|:---|
| `-p`, `--port` | 重设端口 |
| `-s`, `--static` | 只使用静态文件 |
| `-l`, `--log` | 启动日记记录，使用覆盖记录格式 |

#### deploy ####

    $ hexo deploy
    
或：

    $ hexo d
    
部署网站。

| 选项 | 描述 |
|:---|:---|
| `-g`, `--generate` | 部署之前预先生成静态文件 |

#### rendder ####

    $ hexo render <file1> [file2] ...
    
渲染文件。

| 选项 | 描述 |
|:---|:---|
| `-o`, `--output` | 设置输出路径 |

#### migrate ####

    $ hexo migrate <type>
    
从其它博客系统迁移内容，具体参考[此处](https://hexo.io/zh-cn/docs/migration.html)。

#### clean ####

    $ hexo clean
    
清除缓存文件（`db.json`）和已生成的静态文件（`public`）。

#### list ####

    $ hexo list

列出网站资料。

#### version ####

    $ hexo version
    
显示 Hexo 版本。

#### 全局选项 ####

##### 安全模式 #####

    $ hexo --safe
    
安全模式下，不会载入插件和脚本。如果在安装新插件时遭遇问题，可以尝试以安全模式重新执行。

##### 调试模式 #####

    $ hexo --debug
    
在终端中显示调试信息并记录到 `debug.log`。

##### 简洁模式 #####

    $ hexo --slient

隐藏终端信息。

##### 自定义配置文件的路径 #####

    $ hexo --config custom.yml
    
自定义配置文件路径，执行后将不再使用 `_config.yml`。

##### 显示草稿 #####

    $ hexo --draft
    
显示 `source/_drafts` 文件夹中的草稿文章。

##### 自定义CWD #####

    $ hexo --cwd /path/to/cwd
    
自定义当前工作目录 (current working directory) 的路径。

---

## 基本操作 ##

### 写作 ###

通过以下命令创建一篇文章。

    $ hexo new [layout] <title>
    
默认布局为 `post`。

#### 布局（Layout） ####

Hexo 有三种默认布局：`post`、`page` 和 `draft`，它们分别对应不同的路径，而您自定义的其它布局和 `post` 相同，都将会储存到 `source/_posts` 文件夹。

| 布局 | 路径 |
|:---|:---|
| `post` | `source/_posts` |
| `page` | `source` |
| `draft` | `source/_drafts` |

#### 文件名称 ####

Hexo 默认以标题作为文件名称。`_config.yml` 文件的 `new_post_name` 参数支持以下变量。

| 变量 | 描述 |
|:---|:---|
| `:title` | 标题（小写，空格将会被替换为短杠） |
| `:year` | 建立的年份，比如, `2017` |
| `:month` | 建立的月份（有前导零），比如， `04` |
| `:i_month` | 建立的月份（无前导零），比如，`4` |
| `:day` | 建立的日期（有前导零），比如，`07` |
| `:i_day` | 建立的日期（无前导零），比如，`7` |

#### 草稿 ####

草稿是一种特殊布局，可以通过 `publish` 命令将草稿移动到 `source/_posts` 文件夹，也可以在命令中指定 `layout` 来指定布局。

    $ hexo publish [layout] <title>
    
草稿默认不会显示在页面中，可以在执行时加上`--draft`参数，或者是把 `render_drafts` 参数设为 `true` 来预览草稿。

#### 模板（Scaffold） ####

在新建文章时，Hexo 会根据 `scaffolds`文件夹内相对应的文件来建立文件，例如：

    $ hexo new photo "My Gallery"
    
在执行这行命令时，Hexo 会尝试在 `scaffolds` 文件夹中寻找 `photo.md`，并根据其内容建立文章，以下是您可以在模版中使用的变量：

| 变量 | 描述 |
|:---|:---|
| `layout` | 布局 |
| `title` | 标题 |
| `date` | 文件建立日期 |

---

### Front-matter ###

Front-matter 是文件最上方以 `---` 分隔的区域，用于指定个别文件的变量。以下是预先定义的参数，您可以在模板中使用这些参数值并加以利用。

| 参数 | 描述 | 默认值 |
|:---|:---|:---|
| `layout` | 布局 |  |
| `title` | 标题 |  |
| `date` | 建立日期 | 文件建立日期 |
| `updated` | 更新日期 | 文件更新日期 |
|`comments`| 开启文章的评论功能 | true |
| `tags` | 标签（不适用于分页） |  |
| `categories` | 分类（不适用于分页） |  |
| `pwemLINK` | 覆盖文章网址 |  |

如果希望文章不可见，可以将 `layout` 变量设置为 `false`。

#### 分类和标签 ####

只有文章支持分类和标签，你可以在 Front-matter 中设置。在 Hexo 中分类和标签有着明显的差别：分类具有顺序性和层次性，而标签则相反。另外，Hexo 不支持多个同级的分类。例如：

    categories:
        - Tools
        - Git
    tags:
        - Git
        - Github
        - CVS

以上设置中，会使 `Git` 分类成为 `Tools` 的子分类，而不是并列分类。

#### JSON Front-matter ####

除了 YAML 之外，也可以使用 JSON 来编写 Front-matter，只要将 `---` 替换为 `;;;` 即可。

---

### 标签插件 ###

标签插件和 Front-matter 中的标签不同，这综合症是用于在文章中快速插入特定内容的插件。

#### 引用块 ####

在文章中插入引言，可包含作者，来源和标题。

    {% blockquote [author[, source]] [link] [source_link_title] %}
    content
    {% endblockquote %}
    
例如，引用书上的句子：

{% blockquote David Levithan, Wide Awake %}
Do not just seek happiness for yourself. Seek happiness for all. Through mercy.
{% endblockquote %}

#### 代码块 ####

在文章中插入代码。

    {% codeblock [title] [lang:language] [url] [link text] %}
    code snippet
    {% endcodeblock %}
    
#### 反引号代码块 ####

另一种形式的代码块，不同的是它使用三个反引号来包裹。

    ``` [language] [title] [url] [link text] code snippet ```
    
#### Pull Quote ####

在文章中插入 Pull Quote。

    {% pullquote [class] %}
    content
    {% endpullquote %}
    
#### jsFiddle ####

在文章中嵌入 jsFiddle。

    {% jsfiddle shorttag [tabs] [skin] [width] [height] %}
    
#### Gist ####

在文章中嵌入 Gist。

    {% gist gist_id [filename] %}
    
#### iframe ####

在文章中插入 iframe。

    ｛% iframe url [width] [height] %｝
    
#### image ####

在文章中插入指定大小的图片。

    {% img [class names] /path/to/image [width] [height] [title text [alt text]] %}
    
#### Link ####

在文章中插入链接，并自动给外部链接添加 `target="_blank"` 属性。

    {% link text url [external] [title] %}
    
#### Include Code ####

插入 `source` 文件夹内的代码文件。

    {% include_code [title] [lang:language] path/to/file %}
    
#### Youtube ####

在文章中插入 Youtube 视频。

    {% youtube video_id %}
    
#### Vimeo ####

在文章中插入 Vimeo 视频。

    {% vimeo video_id %}
    
#### 引用文章 ####

引用其它文章的链接。

    {% post_path slug %}
    {% post_link slug [title] %}
    
#### 引用资源 ####

引用文章的资源。

    {% asset_path slug %}
    {% asset_img slug [title] %}
    {% asset_link slug [title] %}
    
#### Raw ####

如果您想在文章中插入 Swig 标签，可以尝试使用 Raw 标签，以免发生解析异常。

    {% raw %}
    content
    {% endraw %}
    

---


### 资源文件夹 ###

资源（Asset）代表 `source` 文件夹中除了文章以外的所有文件。访问它们的最简单方法是使用相对路径的常规 markdown 语法`![](/xxx/xxx.jpg)`。但该方法不能在首页上正确工作。

#### 文章资源文件夹 ####

如果想单独组织和管理各个文章的资源，可以将`_config.yml`的`post_asset_folder`的选项设为`true`。Hexo会在每次通过`hexo new [layout] <title>`命令创建新文章时自动创建一个与 markdown 文件一样名字的文件夹，可以将所有与该文章有关的资源放到这个关联文件夹中，就可以通过相对路径来引用它们。方法参考[引用资源](#引用资源)。

例如：

    {% asset_img example.jpg This is an example image. %}


---

### 数据文件 ###

如果您需要在主题中使用某些资源，而这些资料并不会在文章内，并且是需要重复使用的，那么可以考虑 数据文件 功能。此功能会载入 `source/_data` 内的 YAML 或 JSON 文件。

举例来说，在 `source/_data` 文件夹中新建 `menu.yml` 文件：

    Home: /
    Gallery: /gallery/
    Archives: /archives/
    
就可以在模板中使用：
    
    <% for (var link in site.data.menu) { %>
    <% <a href="<%= site.data.menu[link]  %>"> <%= link %> </a> %>
    <% } %>


### 部署 ###

在开始之前，您必须先在 `_config.yml`中修改参数，一个正确的部署配置中至少要有 `type` 参数，例如：

    deploy:
        type: git

您可以同时使用多个 deployer，Hexo 会依照顺序执行每个 deployer。

    deploy:
    - type: git
      repo:
    - type: heroku
      repo:

#### 部署到 Git ####

安装 `hexo-deployer-git`。

    $ npm install hexo-deployer-git --save
    
修改配置：

    deploy:
      type:git
      repo: <repository url>
      branch: [branch]
      message: [message]
      
| 参数 | 描述 |
|:---|:---|
| `repo` | 库（Repository）地址。 |
| `branch` | 分支名称。 |
| `message` | 自定义提交信息。 |

其它部署类型请参考[此处](https://hexo.io/zh-cn/docs/deployment.html)