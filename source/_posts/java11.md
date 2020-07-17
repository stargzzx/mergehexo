---
title: java | 四种监听类用法
date: 2020-07-17 20:16:34
categories:
- [java,基础]
tags:
- java
---
在此列举四种方法：

- 自身类实现ActionListener接口，作为事件监听器
- 通过匿名类处理
- 通过内部类处理
- 通过外部类处理

<!-- more -->

<br/>

# 参考资料

<br/>

- [java中的四种监听类用法](https://www.cnblogs.com/newen/p/4548582.html)

<br/>

# 四种监听类

<br/>

## 自身类实现ActionListener接口，作为事件监听器

```java
package com.star;

import java.awt.Color;
import java.awt.Container;
import java.awt.FlowLayout;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import javax.swing.JButton;
import javax.swing.JDialog;
import javax.swing.JFrame;

public class Main extends JFrame implements ActionListener {

    private JButton btBlue, btDialog;

	// 构造方法
    public Main() {

        // 设置标题栏内容
        setTitle("Java GUI 事件监听处理");
        // 设置初始化窗口位置
        setBounds(100, 100, 500, 350);
        // 设置窗口布局
        setLayout(new FlowLayout());
        // 创建按钮对象
        btBlue = new JButton("蓝色");
        // 将按钮添加事件监听器
        btBlue.addActionListener(this);
        // 创建按钮对象
        btDialog = new JButton("弹窗");
        // 将按钮添加事件监听器
        btDialog.addActionListener(this);
        // 把按钮容器添加到JFrame容器上
        add(btBlue);
        add(btDialog);
        // 设置窗口可视化
        setVisible(true);
        // 设置窗口关闭
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
    }


// ***************************事件处理***************************

    @Override
    public void actionPerformed(ActionEvent e) {
        // 判断最初发生Event事件的对象
        if (e.getSource() == btBlue) {
            // 获得容器
            Container c = getContentPane();
            // 设置容器背景颜色
            c.setBackground(Color.BLUE);
        } else if (e.getSource() == btDialog) {
            // 创建JDialog窗口对象
            JDialog dialog = new JDialog();
            dialog.setBounds(300, 200, 400, 300);
            dialog.setVisible(true);
        }
    }

// ***************************主方法***************************

    public static void main(String[] args) {
        new Main();
    }
}
```

## 通过匿名类处理

这是比较好的一种方法

```java
package com.star;

import java.awt.Color;
import java.awt.Container;
import java.awt.FlowLayout;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import javax.swing.JButton;
import javax.swing.JDialog;
import javax.swing.JFrame;


public class Main extends JFrame {

    private JButton btBlue, btDialog;

    // 构造方法
    public Main() {
        // 设置标题栏内容
        setTitle("Java GUI 事件监听处理");
        // 设置初始化窗口位置
        setBounds(100, 100, 500, 350);
        // 设置窗口布局
        setLayout(new FlowLayout());
        // 创建按钮对象
        btBlue = new JButton("蓝色");
        // 添加事件监听器(此处即为匿名类)
        btBlue.addActionListener(new ActionListener() {
        // 事件处理
            @Override
            public void actionPerformed(ActionEvent e) {
                // 获得容器，设置容器背景颜色
                Container c = getContentPane();
                c.setBackground(Color.BLUE);
            }
        });

        // 创建按钮对象,并添加事件监听器
        btDialog = new JButton("弹窗");
        btDialog.addActionListener(new ActionListener() {
        // 事件处理
            @Override
            public void actionPerformed(ActionEvent e) {
                // 创建JDialog窗口对象
                JDialog dialog = new JDialog();
                dialog.setBounds(300, 200, 400, 300);
                dialog.setVisible(true);
            }
        });

        // 把按钮容器添加到JFrame容器上
        add(btBlue);
        add(btDialog);
        // 设置窗口可视化
        setVisible(true);
        // 设置窗口关闭
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
    }

// ***************************主方法***************************
    public static void main(String[] args) {
        new Main();
    }
}
```

## 通过内部类处理。

该种方法更符合面向对象编程的思想。

```java
package com.star;

import java.awt.Color;
import java.awt.Container;
import java.awt.FlowLayout;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import javax.swing.JButton;
import javax.swing.JDialog;
import javax.swing.JFrame;


public class Main extends JFrame {

    private JButton btBlue, btDialog;
    // 构造方法
    public Main() {
        // 设置标题栏内容
        setTitle("Java GUI 事件监听处理");
        // 设置初始化窗口位置
        setBounds(100, 100, 500, 350);
        // 设置窗口布局
        setLayout(new FlowLayout());
        // 创建按钮对象
        btBlue = new JButton("蓝色");
        // 添加事件监听器对象(面向对象思想)
        btBlue.addActionListener(new ColorEventListener());
        btDialog = new JButton("弹窗");
        btDialog.addActionListener(new DialogEventListener());
        // 把按钮容器添加到JFrame容器上
        add(btBlue);
        add(btDialog);
        // 设置窗口可视化
        setVisible(true);

        // 设置窗口关闭
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
    }

    // 内部类ColorEventListener，实现ActionListener接口
    class ColorEventListener implements ActionListener {
        @Override
        public void actionPerformed(ActionEvent e) {
            Container c = getContentPane();
            c.setBackground(Color.BLUE);
        }
    }

    // 内部类DialogEventListener，实现ActionListener接口
    class DialogEventListener implements ActionListener {
        @Override
        public void actionPerformed(ActionEvent e) {
        // 创建JDialog窗口对象
            JDialog dialog = new JDialog();
            dialog.setBounds(300, 200, 400, 300);
            dialog.setVisible(true);
        }
    }

// ***************************主方法***************************
    public static void main(String[] args) {
        new Main();
    }
}
```

## 通过外部类处理

```java
package com.star;

import java.awt.Color;
import java.awt.Container;
import java.awt.FlowLayout;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import javax.swing.JButton;
import javax.swing.JDialog;
import javax.swing.JFrame;


public class Main extends JFrame {

    private JButton btBlue, btDialog;

    // 构造方法
    public Main() {
        // 设置标题栏内容
        setTitle("Java GUI 事件监听处理");
        // 设置初始化窗口位置
        setBounds(100, 100, 500, 350);
        // 设置窗口布局
        setLayout(new FlowLayout());
        // 创建按钮对象
        btBlue = new JButton("蓝色");
        // 将按钮添加事件监听器
        btBlue.addActionListener(new ColorEventListener(this));
        // 创建按钮对象
        btDialog = new JButton("弹窗");
        // 将按钮添加事件监听器
        btDialog.addActionListener(new DialogEventListener());
        // 把按钮容器添加到JFrame容器上
        add(btBlue);
        add(btDialog);
        // 设置窗口可视化
        setVisible(true);
        // 设置窗口关闭
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
    }


// ***************************主方法***************************

    public static void main(String[] args) {
        new Main();
    }
}


// 外部类ColorEventListener，实现ActionListener接口
class ColorEventListener implements ActionListener {
    private Main el;

    ColorEventListener(Main el) {
        this.el = el;
    }

    @Override
    public void actionPerformed(ActionEvent e) {
        Container c = el.getContentPane();
        c.setBackground(Color.BLUE);
    }
}


// 外部类DialogEventListener，实现ActionListener接口
class DialogEventListener implements ActionListener {

    @Override
    public void actionPerformed(ActionEvent e) {
        // 创建JDialog窗口对象
        JDialog dialog = new JDialog();
        dialog.setBounds(300, 200, 400, 300);
        dialog.setVisible(true);
    }
}
```

你可能注意到为什么我写了两个监听事件，就是加以区分这些方法的区别：

- 第一种的监听处理部分，如果有多个（我就写过三十多个的事件监听，包含菜单栏按钮事件监听和工具栏按钮事件监听），那就需要一个个去判断，从理论上说是影响程序速度的。
- 第二种和第三种比较常用，如果程序的监听事件比较少，可以用第二种，匿名类很合适。
- 第三种符合面向对象编程（可以设置内部类只提供自身类使用，而且方便使用自身类的资源），尤其是适合多个监听事件的处理，当然也适合第二种方法情况。
- 第四种是外部类，如果多个监听事件相同，就可以选用此种方法。