# web开发
- 盒子模型
  - def
    - 描述了HTML元素在页面中占据空间的方式
  - core
    - 细分主题 1
- html
  - def
    - HTML是一种标记语言，用于创建和构建网页的结构。它不是编程语言，而是一套标记标签，用来描述网页内容。
  - core
    - 标记语言（实在）
  - content
    - 基本结构
      - 示例
    - 元素
      - 标题标签
      - 段落和文本
        - p
        - strong
        - em（斜体）
        - br（换行）
        - hr（水平线）
      - 链接
        - <a href="https://www.example.com">访问示例网站</a>
        - <a href="#section1">跳转到章节1</a>
          - 页内跳转到section1的？？
      - 图像
        - <img src="image.jpg" alt="图片描述" width="300" height="200">
      - 列表
        - <!-- 无序列表 -->
<ul>
    <li>项目一</li>
    <li>项目二</li>
    <li>项目三</li>
</ul>

<!-- 有序列表 -->
<ol>
    <li>第一步</li>
    <li>第二步</li>
    <li>第三步</li>
</ol>
      - 表格
        - <table border="1">
    <tr>
        <th>姓名</th>
        <th>年龄</th>
        <th>城市</th>
    </tr>
    <tr>
        <td>张三</td>
        <td>25</td>
        <td>北京</td>
    </tr>
    <tr>
        <td>李四</td>
        <td>30</td>
        <td>上海</td>
    </tr>
</table>
      - 表单
        - example
        - elements
          - form
            - def
              - 表单声明
            - 参数
              - action
          - label
            - def
              - 前面显示的
            - 参数
              - for
          - input
            - def
              - 输入
            - 参数
              - type
              - id
              - name
              - value
          - textarea
            - 参数
              - id
              - name
              - rows
              - cols
      - 语义化标签（html5+）
        - header
          - 页面头部
        - nav
          - 导航栏
        - main
          - 主要内容
        - section
          - 文档的某个区域
        - article
          - 独立的文章内容
        - aside
          - 侧边栏内容
        - footer
          - 页面底部
    - 属性
      - class
        - 为元素指定类名
      - id
        - 为元素指定唯一ID
      - style
        - 内联css样式
      - src
      - alt
        - 图像替代文本
