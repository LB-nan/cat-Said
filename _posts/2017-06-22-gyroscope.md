---
layout: post
title: 陀螺仪
categories: javascript
description: 字符串方法
keywords: javascript, mobile, 移动端
---

陀螺仪小结

## devicemotion：window的事件，检测手机晃动事件
1、accelerationIncludingGravity：加速度对象          
1.1、x：x方向速度     
1.2、y： y方向速度               
1.3、z： z方向速度       
1.4、重力加速度的值在IOS和安卓是相反的。 
```
	(function(){
				var box = document.querySelector('#div');
				window.addEventListener('devicemotion', function(e){
					var motion = e.accelerationIncludingGravity;
					var x = Math.round(motion.x);
					var y = Math.round(motion.y);
					var z = Math.round(motion.z);
					if(getAdr()){
						x = -x;
						y = -y;
						z = -z;
					}
				})
			})()
			
			function getAdr(){
				var u = navigator.userAgent; //浏览器信息
				return isAndroid = u.indexOf('android') > -1 || u.indexOf('Adr') > -1;
			}
```

2、orientationchange：横竖屏切换事件：         
2.1、window.orientation				
2.1.1、属性的值为0正常，横屏的时候是90和-90；竖屏是0和-180              
2.2、可以锁定横竖屏但是目前只有QQ和UC支持。     

3、deviceorientation：检测手机倾斜旋转        
3.1、e.alpha 		
3.2、e.beta:x轴旋转		
3.3、e.gamma       
```
	window.addEventListener('deviceorientation', function(e){
		var x = e.beta(motion.y);
		var y = e.gamma(motion.z);
		var z = e.alpha(motion.x);
	})
```
