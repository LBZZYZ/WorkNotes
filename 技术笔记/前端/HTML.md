Hyper Text Makeup Language

Hypertext代表页面可以包含链接，让用户可以跳转到页面其他位置或完全跳转到另一个文档
first created by Tim Berners-Lee, Robert Cailliau, and others

## Makeup Language
**tags**和**attributes**是**HTML**的基本元素
Makeup Language：一种计算机之间用于控制文本如何被处理和呈现的方式

### tags
mark up the start of an HTML element
usually enclosed in angle brackets
ex: `<h1>`
`<h1></h1>`

### attributes
example
he image source (src) and the alt text (alt) are attributes of the `<img>` tag.  
```html
<img src="mydog.jpg" alt="A photo of my dog.">
```


#### Golden Rules To Remember

1. The vast majority of tags must be **opened** (`<tag>`) and **closed** (`</tag>`) with the element information such as a title or text resting between the tags.
2. When using multiple tags, the tags must be **closed in the order in which they were opened**.

These tags should be placed underneath each other **at the top of every HTML page** that you create.

[`<!DOCTYPE html>`](https://html.com/tags/doctype/) — This tag **specifies the language** you will write on the page. In this case, the language is HTML 5.

[`<html>`](https://html.com/tags/html/) — This tag signals that from here on we are going to write in HTML code.

[`<head>`](https://html.com/tags/head/) — This is where all the **metadata for the page** goes — stuff mostly meant for search engines and other computer programs.

[`<body>`](https://html.com/tags/body/) — This is where the **content of the page** goes.

```html
<!DOCTYPE html>
<html>
<head>
</head>
<body>
</body>
</html>
```

head标签中，
`<title>`标签是必须包含的，设置之后，该标签中的内容就会作为网页的标题
`<meta>`标签，同样存在于head中，设置字符编码，名称和描述
```html
<head> 
<title>My First Webpage</title> 
<meta charset="UTF-8"> 
<meta name="description" content="This field contains information about your page. It is usually around two sentences long.">. 
<meta name="author" content="Conor Sheils"> 
</header>
```

body tag是展示给用户看的内容
**text, images, tables, forms** and everything else that we see on the internet each day. 
h标签用于展示标题
```html
<h1>
  <h2>
    <h3>
```

## 添加文字
`<p>`标签，paragraph的缩写，所有常规文字都放到该标签里面

### 夹杂在文字中的标签
```
**Element****Meaning****Purpose****<b>**BoldHighlight important information**<strong>**StrongSimilarly to bold, to highlight key text**<i>**ItalicTo denote text**<em>**Emphasised TextUsually used as image captions**<mark>**Marked TextHighlight the background of the text**<small>**Small TextTo shrink the text**<strike>**Striked Out TextTo place a horizontal line across the text**<u>**Underlined TextUsed for links or text highlights**<ins>**Inserted TextDisplayed with an underline to show an inserted text**<sub>**Subscript TextTypographical stylistic choice**<sup>**Superscript TextAnother typographical presentation style 
```


## 添加链接
[[a标签]]


## 显示图像
`<img>`
```
<img src="yourimage.jpg" alt="Describe the image" height="X" width="X">
```

## 列表
ordered list
unordered list
defination list

## table
|**Table Tag**|**Meaning**|**Location**|
|---|---|---|
|``**<thead>**``|Table Head|Top of the table|
|**<tbody>**|Table Body|Content of the table|
|**<tfoot>**|Table Foot|Bottom of the table|
|**<colgroup>**|Column Group|Within the table|
|**<th>**|Table Header|Data cell for the table header|

