---
noteId: "db46cc21d86511edb7d82f06d610f713"
tags: []
excalidraw-plugin: "parsed"

---

==⚠  Switch to EXCALIDRAW VIEW in the MORE OPTIONS menu of this document. ⚠==


# Text Elements
forkexec ^kMJAWDs4

helloworld ^CQQvOz4N

user_shell ^Sh19z4I9

OS kernel ^bD4D8zwE

1. OS 初始化 ^HaFIP3HB

2. 找到initproc执行程序并
   创建initproc进程控制块 ^f2t3WwHz

3. 执行initproc APP ^s2oL4MM0

initproc ^mvEkzmmP

4. initproc执行fork+exec(user_shell)
   ，执行waitpid等待子进程  ^mqgNMG6Q

5. user_shell在得到字符串输入"forexec"后，
   执行fork+exec(forkexec)，
   执行waitpid等待子进程 ^yS2BD4Bl

7. hello_world执行完毕并退出 ^LJKDmhfK

6. forkexec执行fork+exec(helloworld)
   ，执行waitpid等待子进程 ^qPuAzxPL

8. forkexec执行waitpid返回，并退出   ^rl9IsCzH

9. user_shell执行waitpid返回，
   回到第5步等待字符串输入 ^kFEqilbY

%%
# Drawing
```json
{
	"type": "excalidraw",
	"version": 2,
	"source": "https://excalidraw.com",
	"elements": [
		{
			"id": "MECKvCUMudtgky48psTr1",
			"type": "rectangle",
			"x": -653.2413330078125,
			"y": -394.38746643066406,
			"width": 165.91072581728358,
			"height": 67.96344746479794,
			"angle": 0,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"seed": 1779067785,
			"version": 269,
			"versionNonce": 1015611657,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1648546276416,
			"link": null
		},
		{
			"id": "kMJAWDs4",
			"type": "text",
			"x": -641.7472374172331,
			"y": -379.75703091652315,
			"width": 140,
			"height": 43,
			"angle": 0,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"seed": 659826215,
			"version": 192,
			"versionNonce": 1833578503,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1648546276417,
			"link": null,
			"text": "forkexec",
			"rawText": "forkexec",
			"fontSize": 34.00342088127022,
			"fontFamily": 1,
			"textAlign": "left",
			"verticalAlign": "top",
			"baseline": 30,
			"containerId": null,
			"originalText": "forkexec"
		},
		{
			"type": "rectangle",
			"version": 319,
			"versionNonce": 129365993,
			"isDeleted": false,
			"id": "bI0ktHIgO1YbwoNyj8-Xy",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -653.1031589927036,
			"y": -323.0946516834244,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 165.91072581728358,
			"height": 67.96344746479794,
			"seed": 321123849,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1648546276417,
			"link": null
		},
		{
			"type": "text",
			"version": 259,
			"versionNonce": 1890883367,
			"isDeleted": false,
			"id": "CQQvOz4N",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -646.7440767435686,
			"y": -308.46421616928336,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 154,
			"height": 43,
			"seed": 432343815,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1648546276417,
			"link": null,
			"fontSize": 34.00342088127022,
			"fontFamily": 1,
			"text": "helloworld",
			"rawText": "helloworld",
			"baseline": 30,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "helloworld"
		},
		{
			"type": "rectangle",
			"version": 321,
			"versionNonce": 333517513,
			"isDeleted": false,
			"id": "R_EdrWDe8B1eBseDTe9yh",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -652.9447287365892,
			"y": -252.68695113725067,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 165.91072581728358,
			"height": 67.96344746479794,
			"seed": 2114111433,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1648546276417,
			"link": null
		},
		{
			"type": "text",
			"version": 291,
			"versionNonce": 1686444615,
			"isDeleted": false,
			"id": "Sh19z4I9",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -652.2277420605612,
			"y": -239.8091401348284,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 171,
			"height": 43,
			"seed": 1850029383,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1648546276417,
			"link": null,
			"fontSize": 34.00342088127022,
			"fontFamily": 1,
			"text": "user_shell",
			"rawText": "user_shell",
			"baseline": 30,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "user_shell"
		},
		{
			"type": "rectangle",
			"version": 325,
			"versionNonce": 1705365929,
			"isDeleted": false,
			"id": "y0EFx_1_L2POtef7spW-W",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -654.1162481110996,
			"y": -113.81710268742566,
			"strokeColor": "#000000",
			"backgroundColor": "#7950f2",
			"width": 165.91072581728358,
			"height": 67.96344746479794,
			"seed": 1131725097,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1648546276417,
			"link": null
		},
		{
			"type": "text",
			"version": 264,
			"versionNonce": 228079975,
			"isDeleted": false,
			"id": "bD4D8zwE",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -646.8809537327589,
			"y": -98.18722006551022,
			"strokeColor": "#000000",
			"backgroundColor": "#7950f2",
			"width": 156,
			"height": 43,
			"seed": 1813119463,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1648546276417,
			"link": null,
			"fontSize": 34.00342088127022,
			"fontFamily": 1,
			"text": "OS kernel",
			"rawText": "OS kernel",
			"baseline": 30,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "OS kernel"
		},
		{
			"id": "HaFIP3HB",
			"type": "text",
			"x": -453.4320068359375,
			"y": -394.6240692138672,
			"width": 205,
			"height": 42,
			"angle": 0,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"seed": 1835269767,
			"version": 133,
			"versionNonce": 478369383,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1648546288967,
			"link": null,
			"text": "1. OS 初始化",
			"rawText": "1. OS 初始化",
			"fontSize": 38.32380814985793,
			"fontFamily": 4,
			"textAlign": "left",
			"verticalAlign": "top",
			"baseline": 35,
			"containerId": null,
			"originalText": "1. OS 初始化"
		},
		{
			"type": "text",
			"version": 313,
			"versionNonce": 1624303497,
			"isDeleted": false,
			"id": "f2t3WwHz",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -450.77439186789763,
			"y": -335.85850524902344,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 457,
			"height": 84,
			"seed": 367193033,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1648546288967,
			"link": null,
			"fontSize": 38.32380814985793,
			"fontFamily": 4,
			"text": "2. 找到initproc执行程序并\n   创建initproc进程控制块",
			"rawText": "2. 找到initproc执行程序并\n   创建initproc进程控制块",
			"baseline": 77,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "2. 找到initproc执行程序并\n   创建initproc进程控制块"
		},
		{
			"type": "text",
			"version": 366,
			"versionNonce": 2126115207,
			"isDeleted": false,
			"id": "s2oL4MM0",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -452.46710205078125,
			"y": -243.6785125732422,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 335,
			"height": 42,
			"seed": 953820679,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1648546288967,
			"link": null,
			"fontSize": 38.32380814985793,
			"fontFamily": 4,
			"text": "3. 执行initproc APP",
			"rawText": "3. 执行initproc APP",
			"baseline": 35,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "3. 执行initproc APP"
		},
		{
			"type": "rectangle",
			"version": 362,
			"versionNonce": 1647293577,
			"isDeleted": false,
			"id": "-k2klJbQtyC_eKVY4ap8p",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -653.8260904477042,
			"y": -183.90306467478177,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 165.91072581728358,
			"height": 67.96344746479794,
			"seed": 593007943,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1648546276417,
			"link": null
		},
		{
			"type": "text",
			"version": 304,
			"versionNonce": 1631220871,
			"isDeleted": false,
			"id": "mvEkzmmP",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -637.3346897091762,
			"y": -169.27262916064075,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 117,
			"height": 43,
			"seed": 1276872361,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1648546276417,
			"link": null,
			"fontSize": 34.00342088127022,
			"fontFamily": 1,
			"text": "initproc",
			"rawText": "initproc",
			"baseline": 30,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "initproc"
		},
		{
			"type": "text",
			"version": 467,
			"versionNonce": 1463619177,
			"isDeleted": false,
			"id": "mqgNMG6Q",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -449.73626708984375,
			"y": -185.72837829589844,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 627,
			"height": 84,
			"seed": 972835081,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1648546288967,
			"link": null,
			"fontSize": 38.32380814985793,
			"fontFamily": 4,
			"text": "4. initproc执行fork+exec(user_shell)\n   ，执行waitpid等待子进程 ",
			"rawText": "4. initproc执行fork+exec(user_shell)\n   ，执行waitpid等待子进程 ",
			"baseline": 77,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "4. initproc执行fork+exec(user_shell)\n   ，执行waitpid等待子进程 "
		},
		{
			"type": "text",
			"version": 692,
			"versionNonce": 1533050023,
			"isDeleted": false,
			"id": "yS2BD4Bl",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 210.7996049360795,
			"y": -392.2303203235973,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 746,
			"height": 126,
			"seed": 1015723751,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1648546323649,
			"link": null,
			"fontSize": 38.32380814985793,
			"fontFamily": 4,
			"text": "5. user_shell在得到字符串输入\"forexec\"后，\n   执行fork+exec(forkexec)，\n   执行waitpid等待子进程",
			"rawText": "5. user_shell在得到字符串输入\"forexec\"后，\n   执行fork+exec(forkexec)，\n   执行waitpid等待子进程",
			"baseline": 119,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "5. user_shell在得到字符串输入\"forexec\"后，\n   执行fork+exec(forkexec)，\n   执行waitpid等待子进程"
		},
		{
			"type": "text",
			"version": 656,
			"versionNonce": 1718633801,
			"isDeleted": false,
			"id": "LJKDmhfK",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 204.27708296342325,
			"y": -138.1132548939098,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 496,
			"height": 42,
			"seed": 2034602025,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1648546323649,
			"link": null,
			"fontSize": 38.32380814985793,
			"fontFamily": 4,
			"text": "7. hello_world执行完毕并退出",
			"rawText": "7. hello_world执行完毕并退出",
			"baseline": 35,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "7. hello_world执行完毕并退出"
		},
		{
			"type": "text",
			"version": 572,
			"versionNonce": 731402183,
			"isDeleted": false,
			"id": "qPuAzxPL",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 212.6177201704545,
			"y": -245.37991749156606,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 630,
			"height": 84,
			"seed": 1693110535,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1648546323649,
			"link": null,
			"fontSize": 38.32380814985793,
			"fontFamily": 4,
			"text": "6. forkexec执行fork+exec(helloworld)\n   ，执行waitpid等待子进程",
			"rawText": "6. forkexec执行fork+exec(helloworld)\n   ，执行waitpid等待子进程",
			"baseline": 77,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "6. forkexec执行fork+exec(helloworld)\n   ，执行waitpid等待子进程"
		},
		{
			"type": "text",
			"version": 653,
			"versionNonce": 883602473,
			"isDeleted": false,
			"id": "rl9IsCzH",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 202.798750443892,
			"y": -72.34549366344106,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 652,
			"height": 42,
			"seed": 1563287975,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1648546323649,
			"link": null,
			"fontSize": 38.32380814985793,
			"fontFamily": 4,
			"text": "8. forkexec执行waitpid返回，并退出  ",
			"rawText": "8. forkexec执行waitpid返回，并退出  ",
			"baseline": 35,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "8. forkexec执行waitpid返回，并退出  "
		},
		{
			"type": "text",
			"version": 608,
			"versionNonce": 1202241255,
			"isDeleted": false,
			"id": "kFEqilbY",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 198.500410600142,
			"y": -5.5773662220348115,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 530,
			"height": 84,
			"seed": 1723331527,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1648546323649,
			"link": null,
			"fontSize": 38.32380814985793,
			"fontFamily": 4,
			"text": "9. user_shell执行waitpid返回，\n   回到第5步等待字符串输入",
			"rawText": "9. user_shell执行waitpid返回，\n   回到第5步等待字符串输入",
			"baseline": 77,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "9. user_shell执行waitpid返回，\n   回到第5步等待字符串输入"
		},
		{
			"id": "J83m5xaWUCVZ5Cxn6iawi",
			"type": "line",
			"x": 187.66569741106946,
			"y": -454.0931604558771,
			"width": 1.593350497159122,
			"height": 546.5275435014205,
			"angle": 0,
			"strokeColor": "#000000",
			"backgroundColor": "#7950f2",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"strokeSharpness": "round",
			"seed": 1646152199,
			"version": 95,
			"versionNonce": 1460810983,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1648546338884,
			"link": null,
			"points": [
				[
					0,
					0
				],
				[
					1.593350497159122,
					546.5275435014205
				]
			],
			"lastCommittedPoint": null,
			"startBinding": null,
			"endBinding": null,
			"startArrowhead": null,
			"endArrowhead": null
		},
		{
			"id": "3D7-wyri3kEoeDBL4oGtV",
			"type": "line",
			"x": -474.38153270256686,
			"y": -450.9064039750533,
			"width": 6.373457475142061,
			"height": 528.2037353515625,
			"angle": 0,
			"strokeColor": "#000000",
			"backgroundColor": "#7950f2",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"strokeSharpness": "round",
			"seed": 33894665,
			"version": 75,
			"versionNonce": 1883998057,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1648546345454,
			"link": null,
			"points": [
				[
					0,
					0
				],
				[
					-6.373457475142061,
					528.2037353515625
				]
			],
			"lastCommittedPoint": null,
			"startBinding": null,
			"endBinding": null,
			"startArrowhead": null,
			"endArrowhead": null
		}
	],
	"appState": {
		"theme": "light",
		"viewBackgroundColor": "#ffffff",
		"currentItemStrokeColor": "#000000",
		"currentItemBackgroundColor": "#7950f2",
		"currentItemFillStyle": "hachure",
		"currentItemStrokeWidth": 1,
		"currentItemStrokeStyle": "solid",
		"currentItemRoughness": 1,
		"currentItemOpacity": 100,
		"currentItemFontFamily": 4,
		"currentItemFontSize": 20,
		"currentItemTextAlign": "left",
		"currentItemStrokeSharpness": "sharp",
		"currentItemStartArrowhead": null,
		"currentItemEndArrowhead": "arrow",
		"currentItemLinearStrokeSharpness": "round",
		"gridSize": null
	},
	"files": {}
}
```
%%