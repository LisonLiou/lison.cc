---
title: BootstrapValidator 页面验证多个form
tags:
  - bootstrap
categories:
  - 前端
author: Lison
img: ''
top: false
cover: false
coverImg: ''
toc: false
mathjax: false
summary: ''
date: 2017-10-25 15:11:34
desc: BootstrapValidator 同一个页面多个form验证；BootstrapValidator Multi form validate
keywords: BootstrapValidator 验证多个form, BootstrapValidator 多个form,BootstrapValidator 多个表单
---

只做记录，没什么技术含量。

每个页面多个form，没问题，form id不同，name不同，然后“标识”不同，所谓标识就是放一个<input type=”hidden” 指定比如name=”type” ，那么多个form在action到同一个method的时候，就使用type判断当前提交的是哪个form；那bootstrapValidator里绑定的内容如下：

<!--more-->

```
$("form").bootstrapValidator(validator_config).on('success.form.bv', function (e) {
        e.preventDefault();
        $("button[id*='submit_']").attr("disabled", "disabled");

        $.scojs_message.delay = $.scojs_message.delay * 10;
        $.scojs_message('请稍候...', $.scojs_message.TYPE_WAIT);
        $.ajax({
            type: "POST",
            data: $(this).serialize(),
            success: function (response) {
                var dataObj = $.parseJSON(response);
                if (dataObj.status) {
                    $.scojs_message('操作成功,3秒后将返回...', $.scojs_message.TYPE_OK);
                    aci.GoUrl(SITE_URL + folder_name + '/resource/edit/' + movie_id);
                } else {
                    $.scojs_message(dataObj.tips, $.scojs_message.TYPE_ERROR);
                    $("button[id*='submit_']").removeAttr("disabled");
                }
            },
            error: function (request, status, error) {
                $.scojs_message(request.responseText, $.scojs_message.TYPE_ERROR);
                $("button[id*='submit_']").removeAttr("disabled");
            }
        });

    }).on('error.form.bv', function (e) {
        $.scojs_message('带*号不能为空', $.scojs_message.TYPE_ERROR);
        $("button[id*='submit_cover']").removeAttr("disabled");
    });
```

第一行jQuery选择器匹配所有form并添加bootstrapValidator，然后post时使用this传递form data，其他的form处理都一样，就四酱。