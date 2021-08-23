---
layout: post
title: "小爱课程表华科适配小计"
date: 2021-04-28 
description: " HUST华中科技大学教务系统小爱课程表导入"
tag: 技术
---   

---

>上周互联网课程结课了。虽然上课基本没听......不过还是稍微接触了一点前端的知识。就想找点小项目练练手，所以刚好趁着这个机会花了一个下午把学校的小爱课程表课程导入功能给实现了（其实寒假就想做了，可惜没(fei)时(chang)间(lan)学这些东西2333）

废话不多说，记录一下代码思路和开发过程中遇到的坑

---

## 参考文档
*  官方开发文档
https://ldtu0m3md0.feishu.cn/docs/doccnhZPl8KnswEthRXUz8ivnhb#26bux2

*  Cheerio官方翻译文档
 https://juejin.im/post/6844904135767097352


## 整体思路

通过官方文档以及开发者工具，整个开发过程我们只需要编辑两个文件：

> *scheduleHtmlProvider.js*
> 
> *scheduleHtmlPasrse.js*

Provider 文件是用于获取 html 的函数，将获取到包含课程表数据的 html 传给 Parser 进行数据处理，将课程信息提取出来并封装成官方要求的数据结构后输出即可

## 代码

### Provider

``` JavaScript
function scheduleHtmlProvider(dom = document) {                        
   var info = dom.getElementById('ktlist')//找到存放课程数据的区域
   //console.log(info)
   return info.innerHTML
}
```
这部分没什么好说的，在网页中检查元素挑出课程信息对应的区域返回即可
![](https://pic4.zhimg.com/80/v2-c3bdea452a80bac431b6eba8ce818123.png)

运行函数后截取的 html 片段会以弹出窗口的形式展现

![](https://pic4.zhimg.com/80/v2-6122dccb06ace169ff5aed564d6107c0.png)
没必要完美地把课程表完美抠出来，包含到关键信息就行，主要的处理还是要看 Parser 函数

在这里一定要吐槽一下学校的**垃圾hub系统**！电脑端的网页和移动端的网页提供的是不一样的数据格式！
![](https://pic4.zhimg.com/80/v2-c9759da482aa22601dd251a70f8c1624.png)

电脑端的网页元素里面只提供了课程名称，任课教师和上课时间，一个页面里面最多也只有一个月的课程数据。显然这并不符合我们的要求。最后是我在手机微信上登录了微校园把查看课表页面在浏览器打开再把链接发到电脑上才解决了数据不全的问题。。。

也就是这个问题浪费了我一大半的时间。。。有被气到

### Parser

这部分代码比较关键，不过认真一点，按照官方要求的数据格式
一个 part 一个 part 来添加就是了

具体看代码，思路都在注释里了

```JavaScript
function scheduleHtmlParser(html) {
    let result = []
    let all_class = $('li')
    //console.log(all_class)
    for(let i = 0;i < all_class.length;i++){
        
        //课程
        let className = $(all_class[i]).find('strong').text()
        
        //任课教师
        let teacher = $($(all_class[i]).find('p')[0]).text()
        teacher = teacher.replace('任课教师：','')

        //找到存放周次节次时间地点等数据的表格区域
        let grid = $(all_class[i]).find('.grid.demo-grid')

        for(let j = 1;j<grid.length;j++){//从1开始，all_class[0]是表头标题
            let re = { sections: [], weeks: [] }
            re.name = className.replace(/^\s*|\s*$/g,"")//正则匹配去除字符串两头空格
            re.teacher = teacher.replace(/^\s*|\s*$/g,"")
            //周次
            let weekrange = $($(grid[j]).find('div')[0]).text().replace(/^\s*|\s*$/g,"")
            //将 x-y 格式转换成按数组形式
            let beginWeek = weekrange.split('-')[0]
            let endWeek = weekrange.split('-')[1]
            for(let k = Number(beginWeek);k <= Number(endWeek);k++){
                re.weeks.push(k)
            }

            //星期（需要把中文转换成数字）
            let day = $($(grid[j]).find('div')[1]).text().replace(/^\s*|\s*$/g,"")
            //用到了switch-case结构，简单粗暴2333
            switch(day[2]){
                case '一':
                    re.day = '1'
                    break;
                case '二':
                    re.day = '2'
                    break;
                case '三':
                    re.day = '3'
                    break;
                case '四':
                    re.day = '4'
                    break;
                case '五':
                    re.day = '5'
                    break;
                case '六':
                    re.day = '6'
                    break;
                case '日':
                    re.day = '7'
                    break;
              
            }
            //节次
            let section = $($(grid[j]).find('div')[2]).text().replace(/^\s*|\s*$/g,"")
            section = section.match(/[0-9]*-[0-9]*/)[0] //正则表达式提取出需要的x-y格式
            //这里的处理和上面周次的一样
            let beginSec = section.split('-')[0]
            let endSec = section.split('-')[1]
            for(let k = Number(beginSec);k <= Number(endSec);k++){
                re.sections.push({ section: k })
            }

            //地点
            let position = $($(grid[j]).find('div')[3]).text().replace(/^\s*|\s*$/g,"")
            re.position = position

            
            //console.log(re)
            result.push(re)
        }
        
    }
    var sectionTimes = [
      {
        "section": 1,
        "startTime": '08:00',
        "endTime": '08:45'
      },
      {
        "section": 2,
        "startTime": '08:55',
        "endTime": '09:40'
      },
      {
        "section": 3,
        "startTime": '10:10',
        "endTime": '10:55'
      },
      {
        "section": 4,
        "startTime": '11:05',
        "endTime": '11:50'
      },
      {
        "section": 5,
        "startTime": '14:00',
        "endTime": '14:45'
      },
      {
        "section": 6,
        "startTime": '14:50',
        "endTime": '15:35'
      },
      {
        "section": 7,
        "startTime": '15:55',
        "endTime": '16:40'
      },
      {
        "section": 8,
        "startTime": '16:45',
        "endTime":'17:30'
      },
      {
        "section": 9,
        "startTime": '18:30',
        "endTime": '19:15'
      },
      {
        "section": 10,
        "startTime": '19:20',
        "endTime": '20:05'
      },
      {
        "section": 11,
        "startTime": '20:15',
        "endTime": '21:00'
      },
      {
        "section": 12,
        "startTime": '21:05',
        "endTime": '21:50'
      }
    ]

    return { courseInfos: result ,
            sectionTimes:sectionTimes}
}

```

这里也有一个需要注意的点：Parser 函数里面不能使用document和window对象，因为这部分是在服务端解析的。

所以我们就使用 Cheerio 来解析，这里只用到了选择器和一点遍历操作，和 jQuery 的语法也比较相似，学起来还是挺快的（指多写错几次就知道怎么用了）。

等代码全部调试正确后，console 控制台会提示 `All run successfully`
![](https://pic4.zhimg.com/80/v2-1c487ac0ac196f338a4be807f7a96816.png)

同时也会弹出窗口返回获取到的格式化后的课程数据

![](https://pic4.zhimg.com/80/v2-8dd2aa00cc3270cfd6e0ff311e6697ac.png)

这时候就可以上传到云端进行 E2E 自测了

## 测试

在手机端打开小爱课程表，搜索学校，点击自己提交的适配
![](https://pic4.zhimg.com/80/v2-ffb90f75c7898094ea4c9ee14956a0da.png)
![导入成功](https://pic4.zhimg.com/80/v2-667d48338cbc0c5095b7ab25ae7050ac.png)

**Perfect！** 

点击完美

至此，适配工作已经完成了

坐等审核通过上线

![](https://pic4.zhimg.com/80/v2-c6ea8cbf0f8b1627d5fb2c288abb45bf.png)
