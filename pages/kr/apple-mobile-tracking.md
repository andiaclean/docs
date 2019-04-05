개요
---

![Apple Search Ads](/_assets/img/pages/deep-linked-ads/apple-search/search-ads-reduced-logo.png)

Branch는 Apple Search Ads 어트리뷰션 API를 가져와 **[Apple Search Ads](https://searchads.apple.com/)** 캠페인의 트래킹을 지원합니다.  그러면 여러분은 Apple Search Ads 대시보드에서 설정한 캠페인 이름과 같은 파라미터를 사용할 수 있으며, 인스톨 후 앱에서 특수한 조치를 취할 수 있습니다. 아니면 Branch 대시보드에서 총 인스톨 수, 리퍼럴, 앱 링크 통계와 같은 Branch 통계 자료를 사용하여 캠페인의 효과를 트래킹할 수도 있습니다.

* [Apple Search Ads](https://searchads.apple.com/)
* [Apple Search Ads WWDC](https://developer.apple.com/videos/play/wwdc2016/302/)

설정
---

사용자가 Apple Search Ads를 통해 유입되었는지 확인하려면 어트리뷰션 호출을 한 후 Branch를 실행해야 합니다. Apple's Search Ads 어트리뷰션 API의 경우 응답에 2초 이상 소요될 수 있습니다. 즉, Branch initSession에 호출을 보내면 콜백 차단이 실행되어 추가적으로 1초의 지연이 발생할 수 있습니다.

### iAD 및 AdSupport 가져오기

Apple의 **iAd.framework** 및 **AdSupport.framework** 를 Xcode 프로젝트의 **Linked Frameworks** 로 가져와 Apple Search Ads 확인을 활성화해야 합니다.

![이미지](/_assets/img/pages/apps/ios-frameworks.png)

#### Apple Search Ads 확인 활성화

이 확인을 활성화하려면 Branch 싱글톤을 생성한 후 `delayInitToCheckForSearchAds` 호출을 **AppDelegate.m** \(또는 **AppDelegate.swift** \) 파일에 추가하세요. 이 작업은 *initSession* 호출 `전에` 수행해야 합니다. 코드 형태는 다음과 같습니다.

```obj-c
Branch *branch = [Branch getInstance];
[branch delayInitToCheckForSearchAds];
[branch initSession.....
```

```swift
let branch: Branch = Branch.getInstance()
branch.delayInitToCheckForSearchAds()
branch.initSession.....
```

1초의 추가 지연이 우려되는 경우, 조건에 따라 실행 시간에 `delayInitToCheckForSearchAds`가 호출될 수 있습니다. 따라서 첫 인스톨 또는 좋아요만 확인하려면 이 메서드를 호출하지 마시기 바랍니다.

#### Apple Search Ads 디버그

Branch는 기능을 확인할 수 있는 디버그 모드도 추가했습니다. 디버그 모드는 아래처럼 사용할 수 있으며, 출시 전에 삭제해야 한다는 점을 기억해 두세요.

```obj-c
Branch *branch = [Branch getInstance];
[branch setAppleSearchAdsDebugMode];
[branch delayInitToCheckForSearchAds];
[branch initSession.....
```

```swift
let branch: Branch = Branch.getInstance()
branch.setAppleSearchAdsDebugMode()
branch.delayInitToCheckForSearchAds()
branch.initSession.....
```

비용 데이터 설정
---------

1. [Apple Search Ads UI > Settings > API](https://app.searchads.apple.com/cm/app/settings/apicertificates)로 이동합니다. *우측 상단의 계정 선택기를 사용하여 올바른 계정을 선택했는지 확인하세요.* 

   ![이미지](/_assets/img/pages/deep-linked-ads/apple-search/apple-api-screen.png)
2. API 인증서 생성

   ![이미지](/_assets/img/pages/deep-linked-ads/apple-search/apple-search-api.png)
3. API 인증서를 컴퓨터에 다운로드합니다. 폴더의 압축을 해제하면 `.key` 및 `.pem` 파일이 각각 하나씩 있습니다.

   ![이미지](/_assets/img/pages/deep-linked-ads/apple-search/apple-download-certs.png)
4. Branch 대시보드에서 [Apple Search Ads partner manager](https://dashboard.branch.io/ads/partner-management/a_apple?tab=settings)로 이동합니다.

5. 여기에서 각 파일을 선택하고 파란색 업로드 화살표를 클릭하여 인증서를 업로드합니다. *다음* 을 클릭하여 계속 진행합니다.

   ![이미지](/_assets/img/pages/deep-linked-ads/apple-search/apple-upload-certs.png)
6. 데이터를 수집할 조직을 선택하고 *Save* 를 클릭하여 Apple Search Ads의 비용 데이터를 활성화합니다.

\{\! ingredients/deep\-linked\-ads/cost\-data.md \!\}

### 비용 데이터 지원

#### "Next" 버튼을 클릭할 수 없는 경우

올바른 파일을 선택했는지 여부 *및* 파란색 업로드 화살표를 눌러 업로드를 완료했는지 여부를 확인하세요.

#### 비용, 클릭 및 노출 데이터가 표시되지 않는 경우

올바른 인증서를 선택했는지 확인하세요.

* *올바른 계정에 대한 인증서를 생성했나요?* Apple Search Ads UI의 우측 상단에서 현재 표시되는 계정을 토글로 선택할 수 있습니다.
* *인증서에 관련 권한이 할당되어 있나요?* Apple Search Ads 데이터를 가져오려면 인증서에 읽기 전용 이상의 권한이 할당되어 있어야 합니다.

그래도 표시되지 않는다면 새 인증서를 다운로드하여 Branch에 업로드해 보세요.

\{\! ingredients/deep\-linked\-ads/cost\-data\-discrepancies.md \!\}

Branch에 매핑된 Apple Search Ads 데이터
--------------------------------

Branch는 Apple Search Ads 어트리뷰션 API를 통해 다음 매개변수를 수신하여 매핑합니다.

| Apple Search Ads 매개변수 |                        Branch 매핑 필드                        |
|-----------------------|------------------------------------------------------------|
| iad\-campaign\-name | last\_attributed\_touch\_data\_tilde\_campaign        |
| iad\-campaign\-id   | last\_attributed\_touch\_data\_tilde\_campaign\_id   |
| iad\-adgroup\-name  | last\_attributed\_touch\_data\_tilde\_ad\_set\_name |
| iad\-adgroup\-id    | last\_attributed\_touch\_data\_tilde\_ad\_set\_id   |
| iad\-keyword         | last\_attributed\_touch\_data\_tilde\_keyword\_text  |

대시보드에서 어트리뷰션 확인
---------------

모든 어트리뷰션은 [Branch 대시보드 요약 페이지](https://dashboard.branch.io/)에서 확인할 수 있습니다. 이 채널을 통해 등록된 모든 인스톨 및 오픈에는 `channel`: `Apple App Store` 및 `Ad Partner`: `Apple Search Ads`가 자동으로 태그됩니다. `campaign`은 Apple Search Ads 대시보드에서 설정한 캠페인 이름으로 설정됩니다.

* 인스톨은 정확하지 않을 수 있지만 인스톨\+오픈 이벤트는 Apple Search Ads의 보고와 일치해야 합니다.
* API의 제한 사항으로 인해 기기의 전체 어트리뷰션은 최대 30일이 소요될 수 있습니다.

이러한 통계는 페이지 상단의 **날짜 범위에 한정된** 데이터입니다. 원하는 경우 해당 날짜 범위를 확장할 수 있습니다.

### 어트리뷰션 윈도우 변경

어트리뷰션 윈도우는 글로벌 계정 수준이나 우선 순위를 갖는 링크 수준 윈도우가 있는 각 링크에서 지정할 수 있습니다. 설정 방법은 다음 지침을 참고하세요.

#### 계정 수준 어트리뷰션 윈도우

Link Settings > Attribution Windows에서 어트리뷰션 윈도우를 편집할 수 있습니다.

![이미지](/_assets/img/pages/dashboard/people-based-attribution/attribution-windows.png)

계정 수준 어트리뷰션 윈도우에 대한 자세한 내용은 [사용자 중심 어트리뷰션](/dashboard/people-based-attribution/#attribution-windows)을 참조하세요.

#### 광고 네트워크 어트리뷰션 윈도우

광고 네트워크에서 어트리뷰션 윈도우를 사용해야 한다면 광고 네트워크 수준에서 어트리뷰션 윈도우를 편집할 수 있습니다. 이 방법은 인스톨에 대해 다른 어트리뷰션 윈도우 기간을 두는 Apple Search Ads, Facebook, Google과 같은 네트워크를 사용할 때 권장됩니다. 이렇게 하면 계정별 설정된 어트리뷰션 윈도우도 유지할 수 있습니다.

![이미지](/_assets/img/pages/deep-linked-ads/branch-universal-ads/anaw_clear.png)

통합 지원
-----

### Apple Search Ads 대시보드와 비교한 인스톨 불일치

Apple Search Ads와의 불일치에는 몇 가지 원인이 있을 수 있습니다. Apple Search Ads 어트리뷰션 설정은 자유롭게 맞춤 설정하기가 어렵기 때문에, 성과가 우수하고 보고 기능이 올바르게 작동하더라도 다른 플랫폼에 비해 Apple Search Ads에서 종종 더 큰 불일치가 발생합니다.

* *시간대.* Apple Search Ads의 시간대\(Settings > Overview > Account Information에서 확인\)가 Branch 대시보드의 시간대\(Account Settings 아래에서 확인\)와 일치하는지 확인하세요.
* *LAT\(광고 트래킹 제한\) 설정.* Apple Search Ads는 사용자가 광고 트래킹 제한을 설정했을 경우 인스톨을 제3자에게 보고하지 않습니다. 하지만 광고 트래킹 제한 상태와 상관없이 Apple Search Ads 대시보드에는 기본적으로 모든 인스톨이 표시됩니다. Apple Search Ads 보고 대시보드에 열을 추가하여 광고 트래킹 제한 설정 및 해제 인스톨의 대략적인 규모를 확인할 수 있습니다. Branch 대시보드에는 이러한 인스톨 정보가 표시되지 않습니다.
* *어트리뷰션 윈도우.* Apple Search Ads는 Apple Search Ads 클릭으로부터 30일 이내로 발생한 모든 인스톨에 대해 해당 클릭의 기여도를 인정합니다. Branch의 기본 인스톨 어트리뷰션 윈도우는 7일입니다. Branch의 클릭 후 인스톨 윈도우를 수정할 수 있으며, Branch에서 [Apple Search Ads 어트리뷰션 윈도우](#change-attribution-windows)를 수정할 수 있습니다.
* *최종 클릭 어트리뷰션.* Apple Search Ads는 Apple Search Ads 클릭으로부터 30일 이내로 발생한 모든 인스톨에 대해 해당 클릭의 기여도를 인정합니다. Branch는 어트리뷰션 윈도우 내 최종 클릭의 기여도를 인정하며, 이는 Apple Search Ads의 소스와 다를 수 있습니다.
* *리인스톨.* Apple Search Ads의 대시보드는 기본 보기에서 리인스톨을 전환으로 표시하지만, Branch는 이러한 인스톨을 "리인스톨"로 표시합니다. Apple 대시보드의 열 선택기에서 New Downloads 또는 Redownloads를 선택하여 데이터를 정렬하세요.
* *어트리뷰션 API 타임아웃 또는 지연.* Apple Search Ads 어트리뷰션 API는 반응이 느릴 수 있습니다. 고객이 타임아웃을 편집할 수 있지만 위 코드의 기본 Branch 타임아웃은 약 1초에 불과합니다. Apple Search Ads가 이 타임아웃 이후에 반응하는 경우 Branch는 해당 인스톨에 대한 Apple Search Ads의 기여도를 인정하지 않습니다.
* *오픈 vs. 인스톨.* Branch는 최초 오픈을 인스톨로 간주합니다. Apple Search Ads는 사용자가 앱을 다운로드한 것을 인스톨 시점으로 간주합니다. 이로 인해 수치 및 인스톨 날짜에 불일치가 발생할 수 있습니다.

### Apple Search Ads에 딥링킹 추가

이 통합이 Branch 링크를 사용하지 않기 때문에 딥링킹 옵션이 제한됩니다. Branch는 `campaign`에 사용하는 값을 Apple Search Ads 대시보드에 다시 돌려보냅니다. 이 값은 여러분이 제어하므로 원하는 대로 설정할 수 있지만, Apple Search Ads 대시보드에도 해당 설정이 반영됩니다. Branch는 정기적으로 인스톨을 트래킹합니다.

### 인스톨 또는 전환 이벤트가 Branch 대시보드에 키워드 없이 표시됨

Apple Search Ads에는 일치 출처로 '키워드' 및 '검색 일치'가 있습니다. 검색 일치 기능은 미리 할당된 키워드 지시문을 사용하지 않고, App Store에서 여러분의 광고가 관련성 높은 사용자 검색과 일치하는지 자동으로 확인합니다. 검색 일치를 통해 발생한 인스톨에는 관련 키워드가 없습니다. 검색 일치는 Apple Search Ads 대시보드의 광고 그룹 수준에서 활성화하거나 비활성화할 수 있습니다.

