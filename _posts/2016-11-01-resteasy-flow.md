---
layout: post
title:  "resteasy��������"
date:   2016-11-01 11:11:11 +0800
categories: RPC
tags: rpc
author: Jimmy Lee
---

* content
{:toc}

### ǰ��
��resteasy��������ϵͳ�ķ���,���õ�http pool,Ϊ�˼���һ�º�ʱ�ͷŵ�����,debug��һ��,�Ե�����·�����ʼ�</br>
resteasy version:3.0.19.Final


### ������·
* ִ��ǰ</br>
ClientProxy </br>
-> ClientInvoker </br>
-> ClientRequest </br>
-> ClientExecutionContextImpl </br>
-> ApacheHttpClient4Executor </br>
-> CloseableHttpClient </br>
-> InternalHttpClient.doExecute </br>
</br>
* Ȼ��return
</br>
* ֱ�����ص�ClientInvoker -> BodyEntityExtractor


### ����˵��
1.��ApacheHttpClient4Executor, �������ͷ����Ӻʹ�stream�л�ȡ������� </br>
2.BodyEntityExtractor, ������http status�Ƿ��쳣���쳣�����쳣,�ҷ����쳣�Ķ����ʱ������һ��copy stream�Ķ�����</br>
������֤�ͷ��˵�ǰ������, �ֱ�����ԭʼ��������, ��������ͻ�����Ҫ�����Զ����һЩ��������Ȼ���Զ�������