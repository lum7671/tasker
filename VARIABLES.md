# Tasker 변수 설정 가이드

이 프로젝트는 Tasker 전역 변수를 사용하여 WiFi SSID를 관리합니다.

## 필요한 변수 설정

Tasker에서 다음 전역 변수들을 설정해야 합니다:

### 방법 1: Tasker 앱에서 직접 설정

1. Tasker 앱 열기
2. 변수 (Variables) 탭 선택
3. + 버튼을 눌러 새 변수 추가
4. 아래 변수들을 하나씩 생성:

```
변수명: HOME_WIFI_5G
값: (실제 집 5GHz WiFi SSID)

변수명: HOME_WIFI_2G  
값: (실제 집 2.4GHz WiFi SSID)

변수명: OFFICE_WIFI
값: (실제 사무실 WiFi SSID)

변수명: GUEST_WIFI
값: (실제 게스트 WiFi SSID)
```

### 방법 2: 변수 설정 태스크 생성

다음과 같은 태스크를 만들어서 한 번에 설정할 수 있습니다:

1. 새 태스크 생성: "Setup WiFi Variables"
2. 다음 액션들 추가:
   - Variable Set: %HOME_WIFI_5G to "실제_5G_SSID"
   - Variable Set: %HOME_WIFI_2G to "실제_2G_SSID"  
   - Variable Set: %OFFICE_WIFI to "실제_사무실_SSID"
   - Variable Set: %GUEST_WIFI to "실제_게스트_SSID"

## 예시 설정

```
%HOME_WIFI_5G = "MyHome_5G"
%HOME_WIFI_2G = "MyHome_2G"
%OFFICE_WIFI = "CompanyWiFi"
%GUEST_WIFI = "Guest_Network"
```

## 보안 주의사항

- 실제 WiFi SSID가 포함된 설정은 개인 저장소에만 보관하세요
- variables-personal.xml 파일은 .gitignore에 포함되어 있습니다
- 변수 설정 후 home-personal.xml로 개인 설정을 저장하는 것을 권장합니다

## 변수 백업

중요한 변수 설정은 정기적으로 백업하세요:
1. Tasker → 데이터 → 변수 백업
2. 백업 파일은 안전한 개인 저장소에 보관
