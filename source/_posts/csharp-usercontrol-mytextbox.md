---
title: C# Winform编程自定义控件（UserControl）可自适应大小的TextBox复合控件
tags:
  - 自定义控件
categories:
  - csharp
author: Lison
img: ''
top: false
cover: false
coverImg: ''
toc: false
mathjax: false
summary: ''
date: 2022-05-09 14:43:32
desc:
keywords: c# 自定义控件TextBox自适应
---


<!--more-->

#### 一个C# Winform自定义控件（UserControl）MyTextBox，实现功能：

1. 窗体大小变化时重新计算自身位置，始终保持在居中状态。
2. 自定义键盘回车事件，回车键触发时，调用用户传递的回调。
3. 不使用*.Designer.cs文件，直接放在*.cs中。
4. 重写控件属性Text与BackColor

直接上代码：

```
using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Data;
using System.Drawing;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Windows.Forms;

namespace BubbleMessage
{
    public class MyTextBox : UserControl
    {
        public MyTextBox()
        {
            InitializeComponent();
        }

        protected override void OnLayout(LayoutEventArgs e)
        {
            base.OnLayout(e);

            //获取子控件
            if (this.Controls.Count == 0) return;
            Control c = this.Controls[0];

            //父窗口参数
            Padding p = this.Padding;
            int x = 0, y = 0;
            int w = this.Width, h = this.Height;
            w -= (p.Left + p.Right);
            x += p.Left;

            //计算文本框的高度，使其显示在中间
            int h2 = c.PreferredSize.Height;
            if (h2 > h) h2 = h;
            y = (h - h2) / 2;

            c.Location = new Point(x, y);
            c.Size = new Size(w, h2);
        }


        public event EventHandler ReturnPressed;

        /// <summary> 
        /// 必需的设计器变量。
        /// </summary>
        private System.ComponentModel.IContainer components = null;

        /// <summary> 
        /// 清理所有正在使用的资源。
        /// </summary>
        /// <param name="disposing">如果应释放托管资源，为 true；否则为 false。</param>
        protected override void Dispose(bool disposing)
        {
            if (disposing && (components != null))
            {
                components.Dispose();
            }
            base.Dispose(disposing);
        }

        #region 组件设计器生成的代码

        /// <summary> 
        /// 设计器支持所需的方法 - 不要修改
        /// 使用代码编辑器修改此方法的内容。
        /// </summary>
        private void InitializeComponent()
        {
            this.edit = new System.Windows.Forms.TextBox();
            this.SuspendLayout();
            // 
            // edit
            // 
            this.edit.Anchor = ((System.Windows.Forms.AnchorStyles)((((System.Windows.Forms.AnchorStyles.Top | System.Windows.Forms.AnchorStyles.Bottom)
            | System.Windows.Forms.AnchorStyles.Left)
            | System.Windows.Forms.AnchorStyles.Right)));
            this.edit.BackColor = System.Drawing.Color.White;
            this.edit.BorderStyle = System.Windows.Forms.BorderStyle.None;
            this.edit.Location = new System.Drawing.Point(3, 17);
            this.edit.Name = "edit";
            this.edit.Size = new System.Drawing.Size(336, 18);
            this.edit.TabIndex = 0;
            this.edit.KeyPress += new System.Windows.Forms.KeyPressEventHandler(this.edit_KeyPress);
            // 
            // MyTextBox
            // 
            this.AutoScaleDimensions = new System.Drawing.SizeF(8F, 15F);
            this.AutoScaleMode = System.Windows.Forms.AutoScaleMode.Font;
            this.BackColor = System.Drawing.Color.White;
            this.Controls.Add(this.edit);
            this.Name = "MyTextBox";
            this.Size = new System.Drawing.Size(344, 53);
            this.ResumeLayout(false);
            this.PerformLayout();

        }

        #endregion

        private System.Windows.Forms.TextBox edit;

        private void edit_KeyPress(object sender, KeyPressEventArgs e)
        {
            if (e.KeyChar == '\r')
            {
                ReturnPressed?.Invoke(this, e);
            }
        }

        public override string Text
        {
            get { return edit.Text; }
            set
            {
                edit.Text = value;
            }
        }

        
        public override Color BackColor
        {
            get { return base.BackColor; }
            set
            {
                edit.BackColor = value;
                base.BackColor = value;
            }
        }
    }
}

```