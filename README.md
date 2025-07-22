# Tasker 자동화 프로젝트

Android용 Tasker 앱을 위한 자동화 설정 파일들을 관리하는 프로젝트입니다.

## 프로젝트 개요

이 프로젝트는 네트워크 연결 상태에 따라 Tailscale VPN과 GPS 설정을 자동으로 제어하는 Tasker 프로필과 태스크들을 포함합니다.

## 파일 구조

- `home.xml` - Tasker 설정 파일 (변수 사용 버전)
- `home-template.xml` - 공개용 템플릿 파일 (예시 SSID 포함)
- `VARIABLES.md` - Tasker 변수 설정 가이드
- `.gitignore` - 개인 설정 파일 제외 목록

## 포함된 프로필 (Profiles)

### 1. Wifi Network (프로필 ID: 10)

- **트리거**: WiFi 네트워크 연결 상태 (Active: Any)
- **연결된 태스크**: Tailscale Switch (태스크 7)

### 2. Mobile Network (프로필 ID: 11)

- **트리거**: 모바일 네트워크 연결 상태
- **연결된 태스크**: Tailscale OFF on Mobile (태스크 6)

### 3. Home Wifi (프로필 ID: 9)

- **트리거**: 집 WiFi 네트워크 연결 (`%HOME_WIFI_5G`, `%HOME_WIFI_2G`) (Active: Any)
- **연결된 태스크**: Tailscale OFF on Mobile (태스크 6)

### 4. Inside of a building (프로필 ID: 12)

- **트리거**: 특정 WiFi 네트워크 연결 (`%HOME_WIFI_5G`, `%HOME_WIFI_2G`, `%OFFICE_WIFI`, `%GUEST_WIFI`) (Active: Any)
- **연결된 태스크**: GPS OFF (태스크 13), GPS ON (태스크 14)

## 포함된 태스크 (Tasks)

### 1. Tailscale OFF on Mobile (태스크 ID: 6)

- **기능**: 모바일 네트워크에서 Tailscale VPN 연결 해제
- **로직**:
  1. 30초 대기 (네트워크 안정화 대기)
  2. 네트워크 상태 확인:
     - WiFi가 꺼져 있고 (WiFi ≠ on)
     - 모바일 신호가 있는 경우 (CELLSIG > 0)
  3. 조건 만족 시 VPN 연결 해제
- **액션**: `com.tailscale.ipn.DISCONNECT_VPN` 브로드캐스트 전송

### 2. Tailscale Switch (태스크 ID: 7)

- **기능**: 조건부 Tailscale VPN 제어
- **로직**:
  1. 30초 대기 (WiFi 연결 안정화 대기)
  2. WiFi 연결 상태 확인:
     - WiFi가 켜져 있고 (WiFi = on)
     - WiFi가 연결되어 있으며 (WIFII ~connected)
     - 집 WiFi SSID인 경우 (`%HOME_WIFI_5G` 또는 `%HOME_WIFI_2G`)
  3. 조건 만족 시 VPN 재연결 수행
- **액션**:
  1. VPN 연결 해제
  2. 잠시 대기
  3. VPN 다시 연결

### 3. GPS OFF (태스크 ID: 13)

- **기능**: GPS 설정 비활성화

### 4. GPS ON (태스크 ID: 14)

- **기능**: GPS 설정 활성화

## 자동화 시나리오

1. **집 WiFi 연결 시**: 30초 후 WiFi 상태를 확인하고, 집 WiFi에 연결되어 있으면 Tailscale VPN을 재연결합니다.
2. **모바일 네트워크 사용 시**: 30초 후 네트워크 상태를 확인하고, 실제로 모바일 네트워크를 사용 중이면 Tailscale VPN을 해제합니다.
3. **특정 WiFi 환경에서**: WiFi 연결 시 GPS가 꺼지고, 연결 해제 시 GPS가 다시 켜집니다.
4. **일반 WiFi 연결 시**: 30초 후 상태를 확인하고 조건에 맞으면 Tailscale VPN을 제어합니다.

## 주요 개선사항

### 네트워크 상태 안정화

- 모든 VPN 제어 태스크에 30초 대기 시간 추가
- 네트워크 연결이 안정화된 후 VPN 제어 수행

### VPN 충돌 방지

- WiFi State 프로필의 Active 파라미터를 "Any"로 설정
- VPN 연결로 인한 WiFi Active 상태 변경이 프로필에 영향을 주지 않음

### 조건부 실행

- 실제 네트워크 상태를 확인한 후에만 VPN 제어 실행
- 불필요한 VPN 제어 방지

## 사용 방법

### 빠른 시작 (추천)

1. **변수 설정**: `VARIABLES.md` 파일을 참고하여 Tasker 전역 변수를 먼저 설정합니다:
   - `%HOME_WIFI_5G` - 집 5GHz WiFi SSID
   - `%HOME_WIFI_2G` - 집 2.4GHz WiFi SSID
   - `%OFFICE_WIFI` - 사무실 WiFi SSID
   - `%GUEST_WIFI` - 게스트 WiFi SSID

2. **파일 가져오기**: Tasker 앱에서 "Import"를 통해 `home.xml` 파일을 가져옵니다.

3. **Tailscale 설치**: Tailscale 앱이 설치되어 있어야 VPN 제어 기능이 작동합니다.

### 대안 방법 (템플릿 사용)

1. `home-template.xml` 파일을 복사하여 `home-personal.xml`로 이름 변경
2. 파일 내의 예시 SSID를 실제 네트워크 이름으로 수정:
   - `YOUR_HOME_WIFI_5G` → 실제 5GHz WiFi SSID
   - `YOUR_HOME_WIFI_2G` → 실제 2.4GHz WiFi SSID  
   - `OFFICE_WIFI` → 실제 사무실 WiFi SSID
   - `GUEST_WIFI` → 실제 게스트 WiFi SSID
3. 수정된 `home-personal.xml` 파일을 Tasker에 가져오기

## 보안 고려사항

### 파일 구조를 통한 보안

이 프로젝트는 2가지 방식으로 개인정보를 보호합니다:

1. **변수 방식 (`home.xml`)**: Tasker 전역 변수를 사용하여 실제 SSID 정보를 분리
2. **템플릿 방식 (`home-template.xml`)**: 예시 SSID만 포함된 공개용 템플릿

### 개인정보 보호 가이드

- **개인 설정 파일은 GitHub에 업로드하지 마세요**
  - `home-personal.xml` (개인 수정 버전)
  - `variables-personal.xml` (변수 백업 파일)
- **실제 사용 시 주의사항**:
  - Fork 후 개인 브랜치에서 실제 SSID로 수정
  - 개인 설정이 담긴 파일은 private 저장소에 보관
- **변수 사용의 장점**:
  - 설정 파일과 개인정보 완전 분리
  - 변수만 변경하면 여러 프로필에 일괄 적용
  - GitHub에 실제 SSID 노출 없이 공유 가능

### 권장 보안 방법

1. **변수 활용**: `VARIABLES.md` 가이드에 따라 Tasker 변수 설정 (권장)
2. **개인 브랜치 사용**: 개인 설정이 담긴 브랜치는 private으로 관리
3. **정기적 백업**: Tasker 변수 설정을 정기적으로 백업
4. **SSID 관리**: 보안을 위해 정기적으로 WiFi SSID 변경 고려

## 요구사항

- Android 기기
- Tasker 앱
- Tailscale 앱 (VPN 기능 사용 시)
- 적절한 시스템 권한 (WiFi 상태 읽기, GPS 제어, 브로드캐스트 전송 등)

## 라이선스

이 프로젝트는 LICENSE 파일에 명시된 라이선스를 따릅니다.
