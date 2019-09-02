---
layout: post
title: 多指兼容
---

多指兼容小结

需要先阻止掉PC的默认事件`ev.preventDefault()`
### IOS下的事件：
`gesturestart`:多指触摸事件：当手指触摸元素，当前屏幕上有2根或者2根以上的手指      
```
	box.addEventListener('gesturestart',function(e){
		
	})
```

`gestureend`：多指触摸结束：当触发多指触摸事件后手指都离开或者在元素上面的手指离开后触发。   

```
	box.addEventListener('gestureend',function(e){
		
	})    
```

`gesturechange`：当屏幕上有两根手指（触发gesturestart）后，移动手指，触发gesturechange事件。

```
	box.addEventListener('gesturechange',function(e){
		// e.scale缩放比值：change时两根手指之间的距离和start时候手指之间的距离的比值。
		e.scale; 
	
		// e.rotation旋转差：当start的时候两根手指的距离练成一条直线，然后两根手指旋转，旋转后的角度与start时候的角度的夹角值。
		e.rotation;
	})
```


