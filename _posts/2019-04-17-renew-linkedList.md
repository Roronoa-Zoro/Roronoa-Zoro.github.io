---
layout: post
title:  "重新组合链表"
date:   2019-04-17 +0800
categories: JAVA
tags: algorithm
author: Jimmy Lee
---

* content
{:toc}


#### 按照左右半区的方式重新组合链表，具体问题描述自行搜索 


### 技术版本
java: 1.8  


### 代码如下
```java
    @Test
    public void cycleNode() {
        Node head = new Node(1);
        Node lhead = head;
        int len = 6;
        for (int i = 2; i <= len; i++) {
            Node n = new Node(i);
            lhead.setNext(n);
            lhead = n;
        }

        int mid = len / 2;
        int mod = len % 2;
        int step = mod == 0 ? mid : (mid + 1);
        Node rpre = null;
        Node rhead = null;
        lhead = head;
        while (step > 0) {
            step--;
            lhead = lhead.getNext();
            if (step == 1) {//最后一步
                rpre = lhead;
                rhead = rpre.getNext();
            }
        }
        lhead = head;
        //当是偶数数量时, lhead 和 rpre重合时,则剩余最后的Ln,Rn
        while (rhead != null && lhead != rpre) {
            Node lnext = lhead.getNext();
            lhead.setNext(rhead);
            rpre.setNext(rhead.getNext());
            rhead.setNext(lnext);

            rhead = rpre.getNext();
            if (rhead != null) {
                lhead = lhead.getNext().getNext();
            }

        }

        lhead = head;
        while (lhead != null) {
            System.out.print("node:" + lhead.getVal() + " 's next==>");
            lhead = lhead.getNext();
        }

    }
```
