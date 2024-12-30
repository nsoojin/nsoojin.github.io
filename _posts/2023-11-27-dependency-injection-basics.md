---
layout: post
title: "의존성 주입에 대한 오해와 진실"
categories: []
tags:
- dependency injection
type: post
published: true
meta: {}
---

## 의존성 주입에 대한 오해

### 1. 의존성 주입은 유닛 테스트만을 위한 것이다.

의존성 주입이 유닛 테스트를 하기 위한 핵심 기반이긴 합니다. 하지만 유닛 테스트를 작성하려는 목적이 아니더라도 DI를 적용하면 여러 이점들을 똑같이 누릴수 있습니다. 유닛 테스트는 그 이점들 중 하나일 뿐입니다.

### 2. 의존성 주입은 추상 팩토리 그 이상 그 이하도 아니다.

앱의 의존성들을 생성해주는 추상 팩토리 같은 객체가 필요하다고 생각하셨다면 바로 지워야 합니다. 오히려 그 반대입니다. 의존성 주입이란 의존성을 '어디선가 가져오는'게 아니라, 소비자가 제공하도록 코드의 구조를 잡는 것입니다.

### 3. 의존성 주입을 하려면 DI Container가 필요하다.

2번 미신과 연관지어서 DI Container를 추상 팩토리로 사용해야한다고 생각하는 사람들도 있습니다만 잘못된 생각입니다. DI Container는 앱을 조합하는 과정을 쉽게 해줄 수 있는 라이브러리지만 필수는 아닙니다. 의존성 주입은 원칙과 패턴의 모음입니다. DI Container는 유용하지만 선택사항인 도구입니다.

## 의존성 주입의 목적

의존성 주입 자체는 목적이 될 수 없습니다. 의존성 주입은 느슨한 결합을 가능케 해주고, 느슨한 결합은 코드를 유지보수하기 좋게 만들어줍니다. 우리 주변에서 쉽게 찾아볼 수 있는 느슨한 결합의 아주 좋은 예는 전기 콘센트입니다. 우리 집 벽에 튀어나와 있는 전기 콘센트는 그와 연결된 기기들의 플러그(구현체)를 통해 연결됩니다. 그 둘의 모양이 맞고(인터페이스를 구현하면), 전압과 주파수가 호환된다면(인터페이스의 계약을 준수) 무사히 작동하고, 또 우리가 원하는 대로 다양하게 전자 기기들을 조합해서 사용할 수 있습니다. 

반면에 여러분의 티비가 벽에 직접 연결되어 있었다고 상상해보세요. 티비가 고장나면 그 티비와 건물 인프라를 둘 다 잘 아는 기술자를 불러야할 뿐더러 수리를 하는 동안 집 전체에 전기를 끊어야할 수도 있습니다. 이게 강하게 결합된 코드의 모습입니다. 다른 한 쪽에 영향을 주지 않으면서 다른 하나를 건드릴 수 없습니다.

의존성 주입은 어떻게 코드의 유지보수성을 좋게 해줄까요? 의존성 주입이 가져다 주는 장점은 예전 글을 통해 확인하시기 바랍니다. [느슨한 결합이 가져다 주는 이점 5개](https://soojin.ro/blog/loose-coupling)

## 의존성 주입을 왜 해야 하나?

의존성 주입은 도구나 기술이라기 보다는 코드를 설계하는 방식에 대한 원칙과 패턴의 모음입니다. 코드를 어떻게 구성하는게 좋은지 판단할 수 있는 틀을 제공해줍니다. 그래서 의존성 주입은 코드 전체에 퍼져있어야 합니다. 느슨한 결합으로 얻을 수 있는 열매는 앱이 복잡해질수록, 코드양이 많아질수록 잘 드러납니다. 

흔히 말하는 스파게티 코드란 의존성 주입이 적용되지 않은 강하게 결합된 코드를 의미합니다. 의존성 주입은 `구현에 의존하지 말고 인터페이스에 의존하라(Program to an interface, not an implementation)` 이라고 했던 Gang of Four의 격언을 현실화하는 방법입니다.

더 상세한 내용과 실제 코드와 예제 앱을 통해 확인하고 싶으시다면 아래 사전 신청서를 작성해주세요.

--

# 📝 Swift 개발자를 위한 DI 강의 사전 신청

의존성 주입의 원칙과 패턴에 대해서 더 깊게 알고 싶으시다면, 의존성 주입이 앱 개발자에게 가져다 주는 이점을 구체적으로 확인해보고 싶으시다면 현재 준비중인 동영상 강의 사전 신청서를 작성해주세요. 강의가 준비되면 가장 먼저 알려드리고 30% 할인 쿠폰을 드립니다.

📎 할인 쿠폰 신청하기: [https://forms.gle/JXwKctVS6XqKoHF76](https://forms.gle/JXwKctVS6XqKoHF76)