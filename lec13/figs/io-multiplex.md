---
noteId: "daf10b00d86511edb7d82f06d610f713"
tags: []
excalidraw-plugin: "parsed"

---

==⚠  Switch to EXCALIDRAW VIEW in the MORE OPTIONS menu of this document. ⚠==


# Text Elements
进程阻塞于select/epoll
系统调用，等待某个文件
描述符变为可读 ^civJ0kAx

进程 ^BCJVH8ZY

select/epoll ^1YqUYTPI

系统调用 ^Lm01RAUs

OS kernel ^vhIkgWjV

数据没准备好 ^zFS35GNs

等待磁盘数据 ^4l1N0fGM


复制数据 ^UTvK4Os9

复制完成 ^fUTFDj0v

系统调用成功 ^Peq5q6BO

进程继续执行 ^SC6hOMAr

将数据从内核空间
复制到进程用户空间 ^55xRyF4q

返回文件可读 ^lc2OE96D

系统调用 ^CX5Tse0r

sys_read ^ndKmyRwY

数据准备好 ^Hoo7qA2e

进程阻塞于sys_read
系统调用 ^c4kXCDFJ


# Embedded files
68c7c6dd54ceeaf9fe3714d73426a7e28acb1343: [[Pasted Image 20221231180422_323.png]]
169984e76ba14bc041af28d804b7d3a330edff64: [[Pasted Image 20221231180422_344.png]]

%%
# Drawing
```json
{
	"type": "excalidraw",
	"version": 2,
	"source": "https://excalidraw.com",
	"elements": [
		{
			"type": "image",
			"version": 272,
			"versionNonce": 1142341435,
			"isDeleted": false,
			"id": "tWjTglTJnXPD5ypf0Uv0_",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -692.9691686869377,
			"y": -930.7712665283202,
			"strokeColor": "transparent",
			"backgroundColor": "transparent",
			"width": 46.69623005434154,
			"height": 202.47485351562491,
			"seed": 826701723,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1672483699000,
			"link": null,
			"status": "pending",
			"fileId": "68c7c6dd54ceeaf9fe3714d73426a7e28acb1343",
			"scale": [
				1,
				1
			]
		},
		{
			"type": "image",
			"version": 435,
			"versionNonce": 1025779189,
			"isDeleted": false,
			"id": "0rvQufkGA8v1SYcfAMa1V",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -270.34835069309804,
			"y": -928.2470966064452,
			"strokeColor": "transparent",
			"backgroundColor": "transparent",
			"width": 48.43805275104141,
			"height": 169.37693284557702,
			"seed": 158804021,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1672483699000,
			"link": null,
			"status": "pending",
			"fileId": "169984e76ba14bc041af28d804b7d3a330edff64",
			"scale": [
				1,
				1
			]
		},
		{
			"type": "text",
			"version": 539,
			"versionNonce": 1095280245,
			"isDeleted": false,
			"id": "civJ0kAx",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -889.2741809570311,
			"y": -876.0377765380858,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 220,
			"height": 66,
			"seed": 961748245,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1672483653478,
			"link": null,
			"fontSize": 20.00000000000001,
			"fontFamily": 4,
			"text": "进程阻塞于select/epoll\n系统调用，等待某个文件\n描述符变为可读",
			"rawText": "进程阻塞于select/epoll\n系统调用，等待某个文件\n描述符变为可读",
			"baseline": 62,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "进程阻塞于select/epoll\n系统调用，等待某个文件\n描述符变为可读"
		},
		{
			"type": "text",
			"version": 40,
			"versionNonce": 621924853,
			"isDeleted": false,
			"id": "BCJVH8ZY",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -635.306132861328,
			"y": -962.2713123046874,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 40,
			"height": 22,
			"seed": 1926638421,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1672482505410,
			"link": null,
			"fontSize": 20,
			"fontFamily": 4,
			"text": "进程",
			"rawText": "进程",
			"baseline": 19,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "进程"
		},
		{
			"type": "text",
			"version": 73,
			"versionNonce": 1048687227,
			"isDeleted": false,
			"id": "1YqUYTPI",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -637.6558643066405,
			"y": -936.5470538818358,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 106,
			"height": 22,
			"seed": 472292667,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1672483456161,
			"link": null,
			"fontSize": 20,
			"fontFamily": 4,
			"text": "select/epoll",
			"rawText": "select/epoll",
			"baseline": 19,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "select/epoll"
		},
		{
			"type": "arrow",
			"version": 51,
			"versionNonce": 454502101,
			"isDeleted": false,
			"id": "6HE0Y-_UNkpavyzncr-GU",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -524.3594775878905,
			"y": -925.4031635009765,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 108.8433837890625,
			"height": 1.577423095703125,
			"seed": 1958440789,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1672483465706,
			"link": null,
			"startBinding": {
				"elementId": "VW1MNzEKMKJFs7Ub9FUyN",
				"focus": 2.1744742781056132,
				"gap": 13.442169189453125
			},
			"endBinding": {
				"elementId": "JRGHmxS6cjUVipSJOj0sr",
				"focus": 0.3693967420633184,
				"gap": 5.36431884765625
			},
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					108.8433837890625,
					1.577423095703125
				]
			]
		},
		{
			"type": "text",
			"version": 43,
			"versionNonce": 768016507,
			"isDeleted": false,
			"id": "Lm01RAUs",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -512.8773608886718,
			"y": -960.8453326904296,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 80,
			"height": 22,
			"seed": 1818665915,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "6HE0Y-_UNkpavyzncr-GU",
					"type": "arrow"
				}
			],
			"updated": 1672482505410,
			"link": null,
			"fontSize": 20,
			"fontFamily": 4,
			"text": "系统调用",
			"rawText": "系统调用",
			"baseline": 19,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "系统调用"
		},
		{
			"type": "text",
			"version": 40,
			"versionNonce": 914593973,
			"isDeleted": false,
			"id": "vhIkgWjV",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -398.8904224121093,
			"y": -962.9132190429686,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 83,
			"height": 22,
			"seed": 1475401717,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1672482505410,
			"link": null,
			"fontSize": 20,
			"fontFamily": 4,
			"text": "OS kernel",
			"rawText": "OS kernel",
			"baseline": 19,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "OS kernel"
		},
		{
			"type": "text",
			"version": 56,
			"versionNonce": 1671812693,
			"isDeleted": false,
			"id": "zFS35GNs",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -410.1517749511718,
			"y": -929.4839435302733,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 120,
			"height": 22,
			"seed": 446568533,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "6HE0Y-_UNkpavyzncr-GU",
					"type": "arrow"
				},
				{
					"id": "huYITf2AQ0nEBaUmQsx8X",
					"type": "arrow"
				}
			],
			"updated": 1672482616238,
			"link": null,
			"fontSize": 20,
			"fontFamily": 4,
			"text": "数据没准备好",
			"rawText": "数据没准备好",
			"baseline": 19,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "数据没准备好"
		},
		{
			"type": "text",
			"version": 87,
			"versionNonce": 2141271291,
			"isDeleted": false,
			"id": "4l1N0fGM",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -206.35148198242177,
			"y": -818.6130328857421,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 120,
			"height": 22,
			"seed": 1451708949,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1672482531272,
			"link": null,
			"fontSize": 20,
			"fontFamily": 4,
			"text": "等待磁盘数据",
			"rawText": "等待磁盘数据",
			"baseline": 19,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "等待磁盘数据"
		},
		{
			"type": "text",
			"version": 261,
			"versionNonce": 1377943317,
			"isDeleted": false,
			"id": "UTvK4Os9",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -371.7280688964843,
			"y": -750.1977496826171,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 80,
			"height": 44,
			"seed": 1266129525,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "2Ci7XkikVvawIiRKlPR_q",
					"type": "arrow"
				}
			],
			"updated": 1672483557136,
			"link": null,
			"fontSize": 20,
			"fontFamily": 4,
			"text": "\n复制数据",
			"rawText": "\n复制数据",
			"baseline": 40,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "\n复制数据"
		},
		{
			"type": "arrow",
			"version": 469,
			"versionNonce": 1779069589,
			"isDeleted": false,
			"id": "2Ci7XkikVvawIiRKlPR_q",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -343.4741510513841,
			"y": -698.2437124282203,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 0.2690326088111874,
			"height": 122.72542218381545,
			"seed": 776705557,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1672483591965,
			"link": null,
			"startBinding": {
				"elementId": "UTvK4Os9",
				"focus": 0.30039336368019837,
				"gap": 7.9540372543967806
			},
			"endBinding": {
				"elementId": "fUTFDj0v",
				"focus": -0.19752879859620348,
				"gap": 10.463088169209641
			},
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					-0.2690326088111874,
					122.72542218381545
				]
			]
		},
		{
			"type": "text",
			"version": 137,
			"versionNonce": 1744530747,
			"isDeleted": false,
			"id": "fUTFDj0v",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -375.8843188964843,
			"y": -565.0552020751952,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 80,
			"height": 22,
			"seed": 1842214773,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "2Ci7XkikVvawIiRKlPR_q",
					"type": "arrow"
				}
			],
			"updated": 1672483591965,
			"link": null,
			"fontSize": 20,
			"fontFamily": 4,
			"text": "复制完成",
			"rawText": "复制完成",
			"baseline": 19,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "复制完成"
		},
		{
			"type": "text",
			"version": 121,
			"versionNonce": 1202686549,
			"isDeleted": false,
			"id": "Peq5q6BO",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -522.667949267578,
			"y": -568.7202411376952,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 120,
			"height": 22,
			"seed": 1787385749,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "T-Fje2WKVwSizg-69ozMZ",
					"type": "arrow"
				}
			],
			"updated": 1672483587353,
			"link": null,
			"fontSize": 20,
			"fontFamily": 4,
			"text": "系统调用成功",
			"rawText": "系统调用成功",
			"baseline": 19,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "系统调用成功"
		},
		{
			"type": "arrow",
			"version": 68,
			"versionNonce": 1956950395,
			"isDeleted": false,
			"id": "T-Fje2WKVwSizg-69ozMZ",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -394.9217334472655,
			"y": -543.2242694580077,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 140.3922119140625,
			"height": 0,
			"seed": 62831573,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1672483587353,
			"link": null,
			"startBinding": {
				"elementId": "Peq5q6BO",
				"focus": -1.3178156072443181,
				"gap": 7.7462158203125
			},
			"endBinding": {
				"elementId": "SC6hOMAr",
				"focus": 0.7624234286221591,
				"gap": 13.58642578125
			},
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					-140.3922119140625,
					0
				]
			]
		},
		{
			"type": "text",
			"version": 165,
			"versionNonce": 834273749,
			"isDeleted": false,
			"id": "SC6hOMAr",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -668.900371142578,
			"y": -562.6109271728515,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 120,
			"height": 22,
			"seed": 1358357787,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "T-Fje2WKVwSizg-69ozMZ",
					"type": "arrow"
				}
			],
			"updated": 1672483585683,
			"link": null,
			"fontSize": 20,
			"fontFamily": 4,
			"text": "进程继续执行",
			"rawText": "进程继续执行",
			"baseline": 19,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "进程继续执行"
		},
		{
			"type": "image",
			"version": 469,
			"versionNonce": 856760283,
			"isDeleted": false,
			"id": "IRixo4n4dgJoOBAABNkah",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -273.38765043629985,
			"y": -734.2102008544921,
			"strokeColor": "transparent",
			"backgroundColor": "transparent",
			"width": 51.71310199582706,
			"height": 180.82904052734364,
			"seed": 1670839931,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1672483699000,
			"link": null,
			"status": "pending",
			"fileId": "169984e76ba14bc041af28d804b7d3a330edff64",
			"scale": [
				1,
				1
			]
		},
		{
			"type": "text",
			"version": 190,
			"versionNonce": 1010839957,
			"isDeleted": false,
			"id": "55xRyF4q",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -220.80668217773427,
			"y": -629.2264056884765,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 180,
			"height": 44,
			"seed": 1330768475,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1672482524153,
			"link": null,
			"fontSize": 20,
			"fontFamily": 4,
			"text": "将数据从内核空间\n复制到进程用户空间",
			"rawText": "将数据从内核空间\n复制到进程用户空间",
			"baseline": 40,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "将数据从内核空间\n复制到进程用户空间"
		},
		{
			"type": "arrow",
			"version": 470,
			"versionNonce": 1542519355,
			"isDeleted": false,
			"id": "fRyHx13747DA6L4vfI2Bt",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -394.05098800814153,
			"y": -765.9696438862728,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 146.9468737644093,
			"height": 0.5625961493994964,
			"seed": 59324437,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1672483536449,
			"link": null,
			"startBinding": {
				"elementId": "Hoo7qA2e",
				"focus": -0.11941718962351085,
				"gap": 15.941830854821319
			},
			"endBinding": null,
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					-146.9468737644093,
					-0.5625961493994964
				]
			]
		},
		{
			"type": "text",
			"version": 344,
			"versionNonce": 653709115,
			"isDeleted": false,
			"id": "lc2OE96D",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -522.2417385448317,
			"y": -795.9019327304406,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 120,
			"height": 22,
			"seed": 1054195643,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "fRyHx13747DA6L4vfI2Bt",
					"type": "arrow"
				}
			],
			"updated": 1672483537859,
			"link": null,
			"fontSize": 20,
			"fontFamily": 4,
			"text": "返回文件可读",
			"rawText": "返回文件可读",
			"baseline": 19,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "返回文件可读"
		},
		{
			"type": "arrow",
			"version": 243,
			"versionNonce": 1030526741,
			"isDeleted": false,
			"id": "oww0Le_GxBWY42IRrYML8",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -526.8289213345003,
			"y": -713.7896088386966,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 127.17029741369186,
			"height": 0.7008398655801784,
			"seed": 1961456053,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1672483574722,
			"link": null,
			"startBinding": null,
			"endBinding": null,
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					127.17029741369186,
					0.7008398655801784
				]
			]
		},
		{
			"type": "text",
			"version": 161,
			"versionNonce": 245092539,
			"isDeleted": false,
			"id": "CX5Tse0r",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -504.03075526846465,
			"y": -740.9065790317102,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 80,
			"height": 22,
			"seed": 1038577691,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "fRyHx13747DA6L4vfI2Bt",
					"type": "arrow"
				}
			],
			"updated": 1672483574722,
			"link": null,
			"fontSize": 20,
			"fontFamily": 4,
			"text": "系统调用",
			"rawText": "系统调用",
			"baseline": 19,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "系统调用"
		},
		{
			"type": "text",
			"version": 119,
			"versionNonce": 1327993307,
			"isDeleted": false,
			"id": "ndKmyRwY",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -622.780482836914,
			"y": -734.3638034545897,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 72,
			"height": 22,
			"seed": 1322687387,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1672483571483,
			"link": null,
			"fontSize": 20,
			"fontFamily": 4,
			"text": "sys_read",
			"rawText": "sys_read",
			"baseline": 19,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "sys_read"
		},
		{
			"type": "arrow",
			"version": 599,
			"versionNonce": 2006515669,
			"isDeleted": false,
			"id": "huYITf2AQ0nEBaUmQsx8X",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 6.278643115939836,
			"x": -346.6602412999357,
			"y": -901.4230410417731,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 1.0793637054941314,
			"height": 114.89096749294833,
			"seed": 558812597,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1672483529954,
			"link": null,
			"startBinding": {
				"elementId": "zFS35GNs",
				"focus": -0.04970453973905764,
				"gap": 6.06411962724269
			},
			"endBinding": {
				"elementId": "Hoo7qA2e",
				"focus": -0.3443435660437235,
				"gap": 8.482999930393817
			},
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					1.0793637054941314,
					114.89096749294833
				]
			]
		},
		{
			"type": "image",
			"version": 411,
			"versionNonce": 435527509,
			"isDeleted": false,
			"id": "8-pkUF-TS7NiZhKq6oSUt",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -689.0558876508984,
			"y": -716.4705234252929,
			"strokeColor": "transparent",
			"backgroundColor": "transparent",
			"width": 35.276912675132564,
			"height": 152.9606933593748,
			"seed": 592902171,
			"groupIds": [],
			"strokeSharpness": "round",
			"boundElements": [],
			"updated": 1672483699000,
			"link": null,
			"status": "pending",
			"fileId": "68c7c6dd54ceeaf9fe3714d73426a7e28acb1343",
			"scale": [
				1,
				1
			]
		},
		{
			"type": "text",
			"version": 114,
			"versionNonce": 710366203,
			"isDeleted": false,
			"id": "Hoo7qA2e",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -378.1091571533202,
			"y": -778.0519443237304,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 100,
			"height": 22,
			"seed": 836002165,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [
				{
					"id": "fRyHx13747DA6L4vfI2Bt",
					"type": "arrow"
				},
				{
					"id": "huYITf2AQ0nEBaUmQsx8X",
					"type": "arrow"
				}
			],
			"updated": 1672483529954,
			"link": null,
			"fontSize": 20,
			"fontFamily": 4,
			"text": "数据准备好",
			"rawText": "数据准备好",
			"baseline": 19,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "数据准备好"
		},
		{
			"type": "text",
			"version": 620,
			"versionNonce": 1258865147,
			"isDeleted": false,
			"id": "c4kXCDFJ",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -847.5245929443358,
			"y": -669.8634067260741,
			"strokeColor": "#000000",
			"backgroundColor": "transparent",
			"width": 172,
			"height": 44,
			"seed": 195773429,
			"groupIds": [],
			"strokeSharpness": "sharp",
			"boundElements": [],
			"updated": 1672483689387,
			"link": null,
			"fontSize": 20.00000000000001,
			"fontFamily": 4,
			"text": "进程阻塞于sys_read\n系统调用",
			"rawText": "进程阻塞于sys_read\n系统调用",
			"baseline": 40,
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "进程阻塞于sys_read\n系统调用"
		}
	],
	"appState": {
		"theme": "light",
		"viewBackgroundColor": "#ffffff",
		"currentItemStrokeColor": "#000000",
		"currentItemBackgroundColor": "transparent",
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