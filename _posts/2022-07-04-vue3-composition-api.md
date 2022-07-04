---
title:  "Vue3 Compostion API 정리"
search: false
categories: 
  - Vue
toc: true  
tags:
  - Vue
author: 김도겸
---
<br>
# Intro
안녕하세요. 김도겸입니다. <br>
Vue3의 핵심인 composition api에 대해 스터디하며, 문법과 기초 이론을 정리해보았습니다.

<br><br>

# 1. Compostion API란?

## 개념
 컴포지션 API는 컴포넌트 내에서 사용하는 특정 기능을 갖는 코드를 유연하게 구성하여 사용할 수 있도록 Vue3 버전에 추가된 함수 기반의 API입니다.    
 API라는 이름을 붙인 것처럼 특정 기능을 갖는 함수를 정의하고 API처럼 사용할 수 있게 해주며, 궁극적인 목적인 코드에 대한 재활용성을 높이고, 코드의 가독성을 높이기 위해 추가된 기능입니다.

## Options API와 비교
기존 방식인 Options API는 비슷한 기능을 가진 로직들이 옵션에 따라 data, methods, computed 등으로 각각 분리됩니다.   
그렇다 보니 코드가 길어지면 길어질 수록 뭐가 뭔지 잘 모르게 되며 위에서 설명했던 궁극적인 목적인 코드의 가독성 향상에 어긋나게 됩니다.   
그러면서 '규모가 커질 수록 관리가 힘들다'는 것이 Vue의 단점으로 여겨졌습니다.   
이를 보완하기 위해 compostion api에서는 setup()이라는 메서드에 한 덩어리로 코드를 구현하고, 관련된 기능들을 한 부분으로 묶을 수 있습니다.

![Options API vs Composition API](/assets/images/vue3-composition-api/OptionsAPI_vs_CompositionAPI.png)

<br>

## 장점과 단점
장점은 코드의 가독성이 높아지고, 재사용성


<br>

# 2. setup() & LifeCycle hook

options api에서의 lifecycle과는 좀 다르게 구성되어 있습니다.   
접두사 "on"을 추가함으로써 컴포넌트의 라이프 사이클 훅에 접근할 수 있고, setup 메서드 내에서 모든 라이프 사이클 훅을 선언할 수 있습니다.

![life cycle compare](/assets/images/vue3-composition-api/lifecycle_compare.png)   
위 사진에서 보셨다시피 setup()이라는 메서드가 beforeCreate와 created를 대체한다고 보시면 됩니다.   
따라서 beforeCreate created 라이프 사이클 훅에서 쓰일 코드는 모두 setup()에서 직접 작성하면 됩니다.     

> setup은 beforeCreate, created 라이프 사이클 훅 사이에 실행되는 시점이므로, beforeCreate()가 setup 직전에 호출되고 created()가 호출된다고만 생각하시고, 이를 명시적으로 정의할 필요는 없습니다.

![composition api life cycle](/assets/images/vue3-composition-api/composiionAPI_lifecycle.jpg)   
부모컴포넌트가 마운트되기 시작하는 시점부터 마운트가 끝나는 시점 사이에에 자식컴포넌트의 setup이 호출되고 마운트된다는 것을 알 수 있습니다.


<br>

# 3. 반응성 (ref(), reactive())

vue3와 vue2의 차이점 중 하나가 이 반응성 시스템의 구현 방식인데, 
이 차이를 설명하기 위해서는 반응성이 무엇인지 알고 넘어가야 합니다.

반응성이란, 데이터가 변경되었을 때 이를 감지하고 이에 반응하여 부가적인 동작을 수행하기 위한 성질입니다.   
흔히 엑셀에서 A셀과 B셀의 합을 C셀에 sum함수를 설정하면 A셀, B셀이 변경 시에 이에 반응하여 C셀이 변경됩니다.   
즉, 데이터를 변경하면 화면이 변경되는 것입니다.