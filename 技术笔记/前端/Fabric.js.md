# Events
[Events](http://fabricjs.com/fabric-intro-part-2#events)
[Event inspector](http://fabricjs.com/events)

## object:scaling
```json
{
	"e": {
		"isTrusted": true
	},
	"transform": {
		"target": {
			"type": "circle",
			"version": "5.2.1",
			"originX": "left",
			"originY": "top",
			"left": 242,
			"top": 108.49,
			"width": 40,
			"height": 40,
			"fill": "blue",
			"stroke": null,
			"strokeWidth": 1,
			"strokeDashArray": null,
			"strokeLineCap": "butt",
			"strokeDashOffset": 0,
			"strokeLineJoin": "miter",
			"strokeUniform": false,
			"strokeMiterLimit": 4,
			"scaleX": 2.18,
			"scaleY": 2.18,
			"angle": 0,
			"flipX": false,
			"flipY": false,
			"opacity": 1,
			"shadow": null,
			"visible": true,
			"backgroundColor": "",
			"fillRule": "nonzero",
			"paintFirst": "fill",
			"globalCompositeOperation": "source-over",
			"skewX": 0,
			"skewY": 0,
			"radius": 20,
			"startAngle": 0,
			"endAngle": 360
		},
		"action": "scale",
		"corner": "tr",
		"scaleX": 1.7075711343346573,
		"scaleY": 1.7075711343346573,
		"skewX": 0,
		"skewY": 0,
		"offsetX": 68.3333330154419,
		"offsetY": -1.6770834922790527,
		"originX": "left",
		"originY": "bottom",
		"ex": 310.3333330154419,
		"ey": 126.3125,
		"lastX": 310.3333330154419,
		"lastY": 126.3125,
		"theta": 0,
		"width": 68.30284537338629,
		"shiftKey": false,
		"altKey": false,
		"original": {
			"scaleX": 1.7075711343346573,
			"scaleY": 1.7075711343346573,
			"skewX": 0,
			"skewY": 0,
			"angle": 0,
			"left": 242,
			"flipX": false,
			"flipY": false,
			"top": 127.98958349227905,
			"originX": "left",
			"originY": "bottom"
		},
		"reset": false,
		"signX": 1,
		"signY": -1,
		"actionPerformed": true
	},
	"pointer": {
		"x": 340.3333330154419,
		"y": 117.3125
	},
	"target": {
		"type": "circle",
		"version": "5.2.1",
		"originX": "left",
		"originY": "top",
		"left": 242,
		"top": 108.49,
		"width": 40,
		"height": 40,
		"fill": "blue",
		"stroke": null,
		"strokeWidth": 1,
		"strokeDashArray": null,
		"strokeLineCap": "butt",
		"strokeDashOffset": 0,
		"strokeLineJoin": "miter",
		"strokeUniform": false,
		"strokeMiterLimit": 4,
		"scaleX": 2.18,
		"scaleY": 2.18,
		"angle": 0,
		"flipX": false,
		"flipY": false,
		"opacity": 1,
		"shadow": null,
		"visible": true,
		"backgroundColor": "",
		"fillRule": "nonzero",
		"paintFirst": "fill",
		"globalCompositeOperation": "source-over",
		"skewX": 0,
		"skewY": 0,
		"radius": 20,
		"startAngle": 0,
		"endAngle": 360
	}
}
```

# originX、originY 和 left、top
以左上角为基准点，left 和 top 指明了原点的位置；
originX 和 originY 指明了矩形的水平和垂直方向哪个点与原点重合；
originX 取值有 left, right 和 center；
originY 取值有 top, bottom 和 center；
如 left = 100, top = 100, originX = left, originY = top 时，代表原点为 (100, 100)，矩形左上角的点与原点重合。
http://fabricjs.com/test/misc/origin.html
https://www.cnblogs.com/xc-dh/p/17218233.html
object:scaling 事件发生时，transfrom.target.originX 和 transform.target.originY 是用户拖拽点对侧的点。如用户拖拽左上角，则 originX = right，originY = bottom。

# 属性解释
```Json
lockScalingFlip: true // 禁止水平翻转
centeredRotation: true // 启用中心旋转
```

# [Fabric.js 禁止元素超出画布](https://cloud.tencent.com/developer/article/2119056)

# [我从 fabric.js 中学到了什么](https://segmentfault.com/a/1190000019054853)