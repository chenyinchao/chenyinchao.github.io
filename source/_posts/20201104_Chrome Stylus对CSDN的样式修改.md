---
title: Chrome Stylus对CSDN的样式修改
date: 2020-11-04 16:35:29
---

```css
#csdn-toolbar,
.article-info-box,
.hide-article-box,
.blog-expert-recommend-box,
.recommend-item-box.type_hot_word,
.recommend-ad-box,
.isGreatIcon,
.tool-box,
.meau-gotop-box,
.more-toolbox,
.recommend-end-box {
  display: none !important;
}

body {
  font-family: source-han-serif-tc, -apple-system, BlinkMacSystemFont, "Segoe UI",
    Roboto, Oxygen, Ubuntu, Cantarell, "Fira Sans", "Droid Sans", "Helvetica Neue",
    sans-serif !important;
  min-width: unset;
  background: none !important;
  background-color: rgba(10, 10, 10, 0.8) !important;
}

#article_content {
  height: auto !important;
}

#mainBox {
  margin-left: auto;
  margin-right: auto;
  width: 60%;
}

#mainBox > main {
  display: block !important;
  float: none;
  width: 100%;
}

#mainBox > aside {
  position: unset !important;
  float: none;
  display: none !important;
}

.pulllog-box {
  display: none !important;
}

.recommend-box {
  display: flex;
  flex-wrap: wrap;
  background: white;
  padding-top: 3rem;
  position: relative;
}

.recommend-box::before {
  content: "相关文章";
  display: block;
  position: absolute;
  top: 1rem;
  font-size: 1.3rem;
  left: 1rem;
}

.recommend-item-box {
  width: 50%;
  max-width: 25rem;
}

.recommend-item-box::before {
  display: none;
}

.recommend-item-box .content,
.recommend-item-box h4 {
  width: 100% !important;
}

.blog_title_box.oneline {
  display: none;
}
```
