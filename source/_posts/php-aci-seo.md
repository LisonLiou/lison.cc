---
title: PHP框架ACI动态设置页面SEO信息
tags:
  - seo
  - aci
categories:
  - php
author: Lison
img: ''
top: false
cover: false
coverImg: ''
toc: false
mathjax: false
summary: ''
date: 2017-8-10 14:51:13
desc: php为每个页面设置固定的SEO信息为单个页面动态设置SEO信息，例如产品详情页，动态展示产品名称，参数（对应title、keyword等内容）
keywords: php aci 设置seo，aci动态设置seo，php aci seo
---

### 背景

PHP框架 [CI](https://codeigniter.org.cn/) 不多说，[ACI框架](http://www.autocodeigniter.com/)同样不多说，说一下如何为ACI框架的每个页面设置单独的SEO信息。直接说源码部分（本文做折腾记录，面向php技能比较生的手，或者出家时不是修行的php，而是半路改行的家伙）。

<!--more-->

### 需求
1.为每个页面设置固定的SEO信息
2.为单个页面动态设置SEO信息，例如产品详情页，动态展示产品名称，参数（对应title、keyword等内容）**

### 涉及到的文件
*1. application/config/seo.php*
*2. application/core/MY_Controller.php*
*3. application/views/template/header.php*
*4. 自定义的Controller*

### 折腾
ACI安装完成之后，要为单独页面设置SEO，先来看下源码目录结构，大约找到了这个文件：application/config/seo.php，打开之后将$config[‘seo’][‘default’] 修改成我们自己的title, keyword,description之后果然生效了，OK, 这是第一步。

上面的修改之所以能够生效，原因在于application/views/template/header.php这个文件，打开之后发现head中有如下代码：

```
  <meta name="description" content="<?php echo $description?>">
    <meta name="keyword" content="<?php echo $keyword?>">
    <meta name="author" content="autocodeigniter.com">
    <link rel="icon" href="favicon.ico">

    <title><?php echo $title?></title>
```

很明显$title, $keyword, $description都是动态设置的（废了个话），然后就应该找到每个页面对应的Controller，于是第二步顺藤摸瓜，找到welcome.php的controller页面，发现controller里面没有任何关于设置title、keyword的东西。然后看一下Welcome的继承关系（ ->代表继承关系）：
Welcome -> Front_Controller-> **MY_Controller** -> CI_Controller

看一下MY_Controller的代码：

```
class MY_Controller extends CI_Controller
{
	private  $is_load_captcha;
	public $aci_config;
	public $aci_status;
	public $all_module_menu;
	protected $page_data = array(
		'module_name' => '',
		'controller_name' => '',
		'method_name' => '',
	);


	function __construct(){
		parent::__construct();
		$this->load->driver('cache',array('adapter'=>'file'));
		$this->load->helper(array('global','url','string','text','language','auto_codeIgniter_helper','member'));

		$this->page_data['folder_name']=trim(substr($this->router->directory, 0, -1)) ;
		$this->page_data['controller_name']= trim($this->router->class);
		$this->page_data['method_name']= trim($this->router->method);
		$this->page_data['controller_info']= $this->config->item($this->page_data['controller_name'],'module');

		$this->config->load('aci');
		$this->aci_config = $this->config->item('aci_module');
		$this->aci_status = $this->config->item('aci_status');

		$_pageseo = $this->config->item($this->router->class,'seo');
		$_default_pageseo = $this->config->item('default','seo');
		$this->page_data['title'] = isset($_pageseo['title'])?$_pageseo['title'] : $_default_pageseo['title'];
		$this->page_data['keyword'] = isset($_pageseo['keywords'])?$_pageseo['keywords'] : $_default_pageseo['keywords'];
		$this->page_data['description'] = isset($_pageseo['decriptions'])?$_pageseo['decriptions'] : $_default_pageseo['decriptions'];
		unset($_pageseo);
		unset($_default_pageseo);
		//如果未安装，执行安装
		if(!$this->aci_status['installED']&&$this->page_data['folder_name']!="setup") die("未安装");

		$this->all_module_menu = getcache("cache_module_menu_all");
		$this->load->vars($this->page_data);
		$this->_check_module();

	}
```

主要看标红的部分，$_default_pageseo是没有设置seo信息时默认的内容，就是第一步seo.php里修改的文字，若设置了seo信息，则从$_pageseo里获取，$_pageseo的内容来自哪里？来自seo.php，新增一个的话：

```
$config['seo']['movie']['title']= 'title movie';
$config['seo']['movie']['keywords']= 'keyword movie';
$config['seo']['movie']['decriptions']= 'description movie';
```

movie是对应的controller名字，这样实现的是固定的页面seo内容。

我的问题是，动态的SEO内容，就像打开一个产品页面，显示每一个具体产品的SEO信息，而不是一个固定的。

OK，解决方法是在Front_Controller中加入方法：

```
/**
     * 自定义页面seo内容，子页面在渲染页面之前调用(view之前)
     * @param $title
     * @param $keyword
     * @param $description
     */
    function seo($title, $keyword, $description)
    {
        $_default_pageseo = $this->config->item('default', 'seo');

        $this->page_data['title'] = empty($title) ? $_default_pageseo['title'] : $title;
        $this->page_data['keyword'] = empty($keyword) ? $_default_pageseo['keywords'] : $keyword;
        $this->page_data['description'] = empty($description) ? $_default_pageseo['decriptions'] : $description;

        $this->load->vars($this->page_data);
    }
```

上面代码不做解释，然后在每个Controller的action中，渲染页面之前（$this->view()），调用$this->seo($title, $keyword, $description)。

就是这样，中午没睡好，脑子不灵，语言组织不搭。。。