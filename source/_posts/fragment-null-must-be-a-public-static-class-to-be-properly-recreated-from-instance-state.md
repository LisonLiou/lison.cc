---
title: 問題排查與解決：Fragment null must be a public static class to be properly recreated from instance state
tags:
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
date: 2020-9-15 20:06:28
desc: 問題分析：在用戶使用App的過程中，用戶切換App，返回主界面，又返回了App等操作時，Android Framework會重建Fragment，並且要求Fragment不能是匿名類型，具體見源碼：androidx\fragment\app\BackStackRecord.java
keywords: Fragment null must be a public static class to be  properly recreated from instance state
---

**前提條件：**

compileSdkVersion 29
buildToolsVersion “29.0.2”
將項目從android.support包升級到了androidx，升級過程很順利，沒有什麼異常，但是運行項目的時候出現了標題提到的錯誤。

**問題分析：
在用戶使用App的過程中，用戶切換App，返回主界面，又返回了App等操作時，Android Framework會重建Fragment，並且要求Fragment不能是匿名類型，具體見源碼：androidx\fragment\app\BackStackRecord.java**

<!--more-->

```
private void doAddOp(int containerViewId, Fragment fragment, 
@Nullable String tag, int opcmd) {
    final Class fragmentClass = fragment.getClass();
    final int modifiers = fragmentClass.getModifiers();
    if (fragmentClass.isAnonymousClass() || !Modifier.isPublic(modifiers)
            || (fragmentClass.isMemberClass() && !Modifier.isStatic(modifiers))) {
        throw new IllegalStateException("Fragment " + 
fragmentClass.getCanonicalName()
                + " must be a public static class to be  properly recreated from"
                + " instance state.");
    }
}
```

```
可以看到Fragment必須滿足：
不能是匿名類，必須是Public，必須是成員方法，是否是靜態方法

解決辦法：
如果你的Fragment是abstract類型，也需要重新new一個Fragment，
就這麼簡單。附上我的修改前（已註釋）和修改後的代碼：
//        mWebViewFragment = new WebViewFragment() {
//            @Override
//            protected String getLoadUrl() {
//                return mUrl;
//            }
//        };
        
        mWebViewFragment = new WebviewFragmentImpl(mUrl);
        mFragmentManager.beginTransaction()
        .replace(R.id.fl_content, mWebViewFragment).commit();
PS。看源碼是個好習慣
```

參考文章：
https://stackoverflow.com/questions/52355079/
illegalstateexception-fragment-must-be-public-static-class-to-be-properly-recrea
https://stackoverflow.com/questions/39295458/fragment-must-be-a-public-static-class-to-be-properly-recreated-from-instance-st