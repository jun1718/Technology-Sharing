![image](https://user-images.githubusercontent.com/88138317/191433406-89230218-f51c-425a-a154-8c7e45a8ce4e.png)

# None Security Test
## 개요

* 반갑습니다 팀원 최겸준입니다!
좋은 아침 시작하기에 앞서 어제 있었던 이슈를 공유하고자 합니다.
* spring security가 적용되어있는경우에 그와 관계없는 webmvctest를 진행하려고 하면 여러가지 예외가 발생할수 있습니다.
* 그 이유는 실제 SecurityConfig에 있는 ProviderManager의 매개변수로 주입되어야할 UserDetailsVO가 주입되지 않아서이고 그외에도 SecurityConfig는
여러가지 의존성을 물고있기때문에 테스트를 하기에 매우 힘들어집니다.
* 또 한가지문제는 security config를 달고 테스트를하려할때 권한문제때문에 access denine이 발생합니다.
* 그래서 1)해당 속성을 꺼버리는 법과 해당속성에서 2)권한을 주고서 테스트를 하는 방법에 대해 간단히 알려드리겠습니다.

## 1)해당 속성을 꺼버리는법

* 아래 어노테이션 설정을 최상단에 해주면 필요한 컨트롤러 클래스만 생성한뒤에 security관련한 설정을 생성하지 않도록 제외시킬수 있습니다.
* exludeFilters가 SecurityConfig.class를 생성하지 마라는 의미이고 생성하지 않으면 기본 설정된 security가 설정됩니다.
* 그 security를 꺼버리는 것이 excludeAutoConfiguration입니다. 실제 구글등의 사이트에서 이런 방법으로 추천할때 SecurityAutoConfiguration.class까지만 알려줍니다.
* 그래서 저도 그정도만 했더니 계속 Oauth 관련 의존성 예외가 발생하더군요!
* Oauth2관련된 Auto설정도 꺼버리니 그다음부터는 아주 무결한 상태로 test를 진행할수 있었습니다.

```
@WebMvcTest(
    controllers = OAuthController.class,
    excludeAutoConfiguration = {SecurityAutoConfiguration.class, OAuth2ClientAutoConfiguration.class},
    excludeFilters = {        @ComponentScan.Filter(type = FilterType.ASSIGNABLE_TYPE, classes = SecurityConfig.class)
    }
)
```

## 2) 권한을 줘버리는법

* 아주 간단합니다.
* 아래 어노테이션을 주면 USER권한이 들어갑니다.
    * @WithMockUser(roles = "USER")
        * roles는 default 타입이 USER입니다.
* 다음과 같이 필요한 권한들을 그때그때 넣어주면 되겠습니다.
* 추가로 궁금한점이 있으시면 언제든지 답변드리겠습니다!!
