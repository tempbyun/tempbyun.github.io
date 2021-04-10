---
layout: post
title: "Infrastructure as Code - Kief Morris : 1. 2020년 4분기에 내가 한 일 & 동기"
date:   2020-12-04
author: 변준석
categories: Infrastructure
comments: true
---


# 왜? 갑자기 이책을?
*"내 업무 중 1/3 이 Infrastructure as Code 업무이다."*

## 2020년 4분기에 내가 한 일
내가 일하고 있는 HBsmith 는 모든 인프라를 코드로 관리하고 있다. 흔히들 사용하는 Terraform 같은 기술을 사용하지 않고 **직접 awscli 를 python 으로 래핑**하고 있다. 
 - 생각해보면 awscli가 여러모로 stable하다.
 - 반면에 Terraform 같은 경우 하위호환성을 깨먹는 경우가 많기도 하다. 
 
 HBsmith 의 [johanna](https://github.com/HardBoiledSmith/johanna)는 config파일로 템플릿화된 인프라를 올리는 오픈소스 스크립트 뭉치인데, awscli를 쓰는 사람의 행위를 그대로 automation시킨 코드에 가깝기 때문에 하위호환성 관리나 유지보수가 쉬웠다.

<br />

### aws SES와 SQS 에 종속적인 lambda 함수를 프로비저닝하는 스크립트
🔗 : https://github.com/HardBoiledSmith/johanna/pull/407





### aws Elasticbeanstalk 의 amazon linux2 python 환경에 django application을 프로비저닝하는 스크립트
🔗 : https://github.com/HardBoiledSmith/johanna/pull/414




<br />

<center> <img src = "/assets/post_image/2020-12-04/infrastructure_as_code_medium.png"></center>