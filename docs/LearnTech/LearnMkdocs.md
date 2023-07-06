---
icon: material/file/document/edit
---

# Mkdocs文档用法

!!! note "参考"
    Click [MkDocs官网](https://squidfunk.github.io/mkdocs-material/)

## hide 的用法

### 用法示例

在md文件头部添加一下代码即可选择性地隐藏导航栏

=== "代码"
    ```{.yaml .copy}
    ---
    hide:
        - navigation
        - toc
    ---
    ```

## Admonition的用法

### 用法示例

!!! note
    === "摘要"
        这是一个普通的Admonition
    === "代码"
        ```{.md .copy}
        !!! note
            ...
        ```

??? note
    === "摘要"
        这是一个可折叠的Admonition
    === "代码"
        ```{.md .copy}
        ??? note
            ...
        ```

???+ note
    === "摘要"
        这是一个默认展开的可折叠Admonition
    === "代码"
        ```{.md .copy}
        ???+ note
            ...
        ```

!!! success
    === "摘要"
        这是一个其他type的Admonition
    === "代码"
        ```{.md .copy}
        ???+ success|(or other types listed below)
            ...
        ```

!!! note "还能更改Admonition小标题"
    === "摘要"
        还能更改Admonition小标题
    === "代码"
        ```{.md .copy}
        !!! note "your title"
            ...
        ```

### 自定义示例

=== "内置的type"
    ???+ tip "以下是所有支持的Admonition的type"
        !!! note
        !!! abstract
        !!! info
        !!! tip
        !!! success
        !!! question
        !!! warning
        !!! failure
        !!! danger
        !!! bug
        !!! example
        !!! quote
=== "修改默认图标的方式"
    ???+ tip "在yml文件中添加一下部分可以修改Admonition的图标"
        ```yaml
        theme:
        icon:
            admonition:
            <type>: <icon>
        ```

## Content tabs选项卡的用法

## Button的用法

### 用法示例

=== "透明Button"
    [透明Button示例](# Button的用法){ .md-button }
    !!! note "代码"
        ```{.md .copy}
        [透明Button示例](# Button的用法){ .md-button }
        ```

=== "实心Button"
    [实心Button示例](# Button的用法){ .md-button .md-button--primary }
    !!! note "代码"
        ```{.md .copy}
        [实心Button示例](# Button的用法){ .md-button .md-button--primary }
        ```
 
=== "带图标的Button"
    [图标示例 :fontawesome-solid-paper-plane:](# Button的用法){ .md-button }
    !!! note "代码"
        ```{.md .copy}
        [图标示例 :fontawesome-solid-paper-plane:](# Button的用法){ .md-button }
        ```

=== "链接到网页的Button"
    [网页链接示例 :homes:](https://roll0814.cn){ .md-button }
    !!! note "代码"
        ```{.md .copy}
        [网页链接示例 :homes:](https://roll0814.cn){ .md-button }
        ```

## code copy button的用法

### 代码复制按钮

=== "开启code copy button(默认打开)"
    !!! note "代码"
        ```{.md .copy}
            ```{.md .copy}
                content
            ```
        ```
=== "关闭code copy button"
    !!! note "代码"
        ```{.md .no-copy}
            ```{.md .no-copy}
                content
            ```
        ```

## code block的用法

### 基础使用

``` yaml title="这是一个加了标题的code block"
    ```md title="这是一个code block"
        content
    ```
```

### 高亮

``` yaml linenums="1" title="这是一个加了行号的code block"
    ``` yaml linenums="1" title="这是一个加了行号的code block"
        content
    ```
```

=== "特殊行高亮"
    === "效果"
        ``` md hl_lines="5 6" title="这是一个加了特殊行高亮的code block"
        李白前时原有月，惟有李白诗能说。 
        李白如今已仙去，月在青天几圆缺。 
        今人犹歌李白诗，明月还如李白时。 
        我学李白对明月，白与明月安能知？ 
        李白能诗复能酒，我今百杯复千首。 
        我愧虽无李白才，料应月不嫌我丑。 
        我也不登天子船，我也不上长安眠。 
        姑苏城外一茅屋，万树梅花月满天。 
                    唐寅《把酒对月歌》 
        ```
    === "实现"
        ``` md
            ``` md hl_lines="5 6" title="这是一个加了特殊行高亮的code block"
            李白前时原有月，惟有李白诗能说。 
            李白如今已仙去，月在青天几圆缺。 
            今人犹歌李白诗，明月还如李白时。 
            我学李白对明月，白与明月安能知？ 
            李白能诗复能酒，我今百杯复千首。 
            我愧虽无李白才，料应月不嫌我丑。 
            我也不登天子船，我也不上长安眠。 
            姑苏城外一茅屋，万树梅花月满天。 
                        唐寅《把酒对月歌》 
            ```
        ```

### 行内代码

=== "行内code block"
    === "效果"
        The `#!python inline()` function is used to generate a sequence of numbers.
    === "实现"
        ```md title="实现代码"
            ··· `#!python inline()` ···
        ```

### 定义code block的颜色

!!! note "更改code block的颜色"
    === "更改extra.css所用到的参数"
        ```yaml
        --md-code-hl-number-color
        --md-code-hl-special-color
        --md-code-hl-function-color
        --md-code-hl-constant-color
        --md-code-hl-keyword-color
        --md-code-hl-string-color
        --md-code-hl-name-color
        --md-code-hl-operator-color
        --md-code-hl-punctuation-color
        --md-code-hl-comment-color
        --md-code-hl-generic-color
        --md-code-hl-variable-color
        ```
    === "更改前景背景颜色所用到的参数"
        ```yaml
        --md-code-fg-color
        --md-code-bg-color
        --md-code-hl-color
        ```
    === "更改方式"
        !!! warning "注意root后接 > *"
        ```css
        :root > * {
        --md-code-hl-string-color: #0FF1CE;
        }

        ```

## Data tables的用法

### Data tables用法示例

=== "左对齐"
    === "效果"
        | Method      | Description                          |
        | :---------- | :----------------------------------- |
        | `GET`       | :material-check:     Fetch resource  |
        | `PUT`       | :material-check-all: Update resource |
        | `DELETE`    | :material-close:     Delete resource |
    === "实现"
        ```yaml title="code"
        | Method      | Description                          |
        | :---------- | :----------------------------------- |
        | `GET`       | :material-check:     Fetch resource  |
        | `PUT`       | :material-check-all: Update resource |
        | `DELETE`    | :material-close:     Delete resource |
        ```
=== "居中对齐"
    === "效果"
        | Method      | Description                          |
        | :---------: | :----------------------------------: |
        | `GET`       | :material-check:     Fetch resource  |
        | `PUT`       | :material-check-all: Update resource |
        | `DELETE`    | :material-close:     Delete resource |
    === "实现"
        ```yaml title="code"
        | Method      | Description                          |
        | :---------: | :----------------------------------: |
        | `GET`       | :material-check:     Fetch resource  |
        | `PUT`       | :material-check-all: Update resource |
        | `DELETE`    | :material-close:     Delete resource |
        ```
=== "右对齐"
    === "效果"
        | Method      | Description                          |
        | ----------: | -----------------------------------: |
        | `GET`       | :material-check:     Fetch resource  |
        | `PUT`       | :material-check-all: Update resource |
        | `DELETE`    | :material-close:     Delete resource |
    === "实现"
        ```yaml title="code"
        | Method      | Description                          |
        | ----------: | -----------------------------------: |
        | `GET`       | :material-check:     Fetch resource  |
        | `PUT`       | :material-check-all: Update resource |
        | `DELETE`    | :material-close:     Delete resource |
        ```

### 从文件中载入表格

=== "效果"
    {{ read_excel('./example.xlsx', engine='openpyxl', sheet_name="Sheet1") }}
=== "等同于"
    ```yaml
    {{ read_excel('./example.xlsx', engine='openpyxl', sheet_name="Sheet1") }}
    ```
=== "实现"
    ```md title="code"
    **此处为了展示代码，在语句中插入了\*号,使用时应删去**
    {*{ read_excel('./example.xlsx', engine='openpyxl', sheet_name="Sheet1") }*}
    ```
!!! warning "注意"
    - 载入表格的效果比较局限，合并等其他效果均无法实现
    - 最后的效果与普通的**data table**类似
    - 文档打开时无法载入
    - 文档中对载入语句注释无效（**mkdocs**的文法与**markdown**的注释存在冲突）
