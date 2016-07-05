---
layout: post
title: "Hiberbate版本更新"
date: 2016-07-04 16:23
categories: [Hiberbate]
tags: Hibernate
---

### Hibernate

##### 1. HQL占位符
> hibernate 4.1之后对于HQL中查询参数的占位符做了改进，如果仍然用老式的占位符会有类似如下的告警信息:

{% highlight java linenos %}
//错误
Positional parameter are considered deprecated; use named parameters or JPA-style positional parameters instead.
//老代码
String hql = "select t from Blog t where t.site=?";
query.setParameter(0, "micmiu.com");
//改进一
String hql2 = "select t from Blog t where t.site=:site";
query2.setParameter("site", "micmiu.com");
//改进二
String hql3 = "select t from Blog t where t.site=?0";
query2.setParameter(0, "micmiu.com");/*其中"?"后面的"0"代表索引位置，在HQL语句中可重复出现，并不一定要从0开始，可以是任何数字，只是参数要与其对应上。*/
{% endhighlight linenos %}

