---
title: node 实现简单的爬虫
tags: 爬虫
category: node
abbrlink: 60410
date: 2020-04-27 20:30:33
---

## 使用 node 简单的 电影天堂的爬虫

### 核心库
- [request](https://www.npmjs.com/package/request)
> 请求库，2020年三月 已经废弃，不再维护，可以使用[got](https://www.npmjs.com/search?q=got)替代
- iconv-lite
> js实现的 字符编码 转换库
- [cheerio](https://www.npmjs.com/package/cheerio)
> 后端的jquery
```javascript

const originRequest = require('request');
const iconv = require('iconv-lite'); 
const cheerio = require('cheerio'); // 


function request(url, callback){
  const option = {
    encoding: null
  }
  originRequest(url, option, callback)
}

function main (min, max) {
  for(let i = min; i < max; i ++ ) {
    let url = `https://www.dy2018.com/i/${i}.html`;
    request(url, function(err, res, body){
      // console.log('err', err);
      // console.log('res', res);
     
      const html = iconv.decode(body, 'gb2312');
     
      const $ = cheerio.load(html);

      // console.log($('.title_all h1').text());
      console.log($('#Zoom').children('br').html());
      
    })
  }
}
main(102258, 102260);


```

