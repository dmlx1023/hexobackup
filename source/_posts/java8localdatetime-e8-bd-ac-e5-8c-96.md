---
title: java8localdatetime转化
url: 194.html
id: 194
categories:
  - java基础
date: 2018-07-11 20:39:35
tags:
---

如题
==

            LocalDateTime nowDateTime =LocalDateTime.of(2018,03,31,00,00);
            System.out.println("now" + nowDateTime);
            Instant instantNow = nowDateTime.atZone(ZoneId.systemDefault()).toInstant();
            Date date = Date.from(instantNow);
            System.out.println("date"+date);
            LocalDateTime l2 = LocalDateTime.of(LocalDate.now(), LocalTime.now());
            LocalDateTime l1=LocalDateTime.ofInstant(date.toInstant(), ZoneId.systemDefault());
            System.out.println("l1" + l1);