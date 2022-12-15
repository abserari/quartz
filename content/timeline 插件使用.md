### 添加事件实例
<span 
	  class='ob-timelines' 
	  data-date='{{date}}' 
	  data-title='' 
	  data-class='orange' 
	  data-img = '' 
	  data-type='range' 
	  data-end='{{date}}'> 
	{{title}}
</span>

注意 tags 需要同时满足要求并且有 timeline 标识的 markdown 文件才会被搜索上述 span 并生成 timeline。
### 展示示例
```obsidian
```timeline
myevent
```
### 垂直可以这样设置
```obsidian
```timeline-vis
tags=2
startDate=2020
endDate=2023
fivHeight=8
minDate=10
```

### 静态页面
生成静态 html 页面这样设置，然后使用 `Timelines: Render Timeline` 命令生成。
```html
<!--TIMELINE BEGIN tags='test;now'--><!--TIMELINE END-->
```