---
layout: post
title:  "drools when的高级语法"
date:   2017-02-08 +0800
categories: RULE
tags: drools
author: Jimmy Lee
---

* content
{:toc}

### 前言
drools when的高级语法之forall,from,collect笔记

### from示例
规则如下
```drools
rule fromSyntaxRule
    when
        $order : OrderBOM(canContinue == true)
        $par : ParticipantBOM(sex == 1) from $order.insured
    then
        System.out.println("result:" + $par);
end
```
在java代码如下
```java
OrderBOM order = new OrderBOM();
order.setAmount("sss");
List<ParticipantBOM> list = new ArrayList<ParticipantBOM>(5);
order.setInsured(list);
for (int i = 0; i < 5; i++) {
     ParticipantBOM in = new ParticipantBOM();
     in.setSex(i%2);
     in.setUserName(i + "");
     list.add(in);
}
kieSession.insert(order);
kieSession.fireAllRules(new AgendaFilter() {
     public boolean accept(Match match) {
                return match.getRule().getName().equals("forallSyntaxRule");
            }
});
```
解释:
一个order对象，包含5个参与者对象，执行结果是上面的rule被执行了2次 

### collect 示例
* 示例1,规则文件如下  
```
rule collectSyntaxRule2
    when
        $order : OrderBOM(canContinue == true)
        $li : ArrayList(size >= 3)
                from collect (
                    ParticipantBOM(sex == 1) from $order.insured
                )
    then
        System.out.println("collectSyntaxRule2 has at least 3 elements:" + $li.size());
end
```
java代码如上
解释:查找order下面的参与者列表中性别为1，且聚合后元素数量>=3时才触发规则(即执行then部分的代码)

* 示例2,规则文件如下
```
rule collectSyntaxRule
    when
        $order : OrderBOM(canContinue == true)
        $li : LinkedList()
                from collect (
                    ParticipantBOM(sex == 1) from $order.insured
                )
    then
        System.out.println("collect result:" + $li.size());
end
```
解释:和上面类似，但是如果把sex==2改成sex==10，则依然会触发规则(即then部分,应该是LinkedList默认是一个对象而不是null)

### forall示例
规则文件如下
```
rule forallSyntaxRule
    when
        $order : OrderBOM(canContinue == true)
        forall(ParticipantBOM(sex == 1) from $order.insured)
    then
        System.out.println("forallSyntaxRule, all sex is 1");
end
```
这种情况是必须order下面所有的参与者对象的性别都是1时才会触发规则(即then部分的代码)


