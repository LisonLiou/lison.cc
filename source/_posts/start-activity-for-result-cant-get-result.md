---
title: startActivityForResult获取不到返回值 – Android
tags:
  - android
categories:
  - android
author: Lison
img: ''
top: false
cover: false
coverImg: ''
toc: false
mathjax: false
summary: ''
date: 2015-03-26 21:50:50
desc:
keywords:
---

使用startActivityForResult与setResult通常解决的场景是：
A Activity打开B Activity，B执行完业务后返回结果给A。

<!--more-->

```
// 弹出区域选择框（辅助GPS与网络自动获取，提示用户打开GPS与网络，不强制；打开后自动获取，供用户选择）
Intent i = new Intent(MainActivity.this, ProvinceActivity.class);
i.putExtra("currentPosition", "");
startActivityForResult(i, 0);
```

```
/**
* 重写onActivityResult，
*/
@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {

switch (resultCode) { // resultCode为回传的标记，我在B中回传的是RESULT_OK
		case RESULT_OK:// 取出字符串
			Bundle bundle = data.getExtras();
			String[] result = bundle.getStringArray("currentPosition");

			Log.i("position", result.toString());
			break;
		}

		super.onActivityResult(requestCode, resultCode, data);
	}
 

Intent i = new Intent(ProvinceActivity.this, MainActivity.class);
Bundle b = new Bundle();
b.putStringArray("currentPosition", new String[] { mCurrentProviceName, mCurrentCityName, mCurrentDistrictName, mCurrentZipCode });
i.putExtras(b);
this.setResult(RESULT_OK, i);
this.finish();
```

​	结果发现无论如何也不能输出结果，查找网络后发现Activity在Manifest注册时lauchMode不能使用singleInstance模式；将A与B的singleInstance去掉之后，正常获取值。