# RFC5849-kr

The OAuth 1.0 Protocol 를 공부하기 위함

# The OAuth 1.0 Protocol

## Abstract

OAuth는 클라이언트가 리소스 소유자 (예 : 다른 클라이언트 또는 최종 사용자)를 대신하여 서버 리소스에 액세스 할 수있는 방법을 제공합니다. 또한 최종 사용자가 사용자 에이전트 리디렉션을 사용하여 자격 증명 (일반적으로 사용자 이름 및 암호 쌍)을 공유하지 않고 서버 리소스에 대한 타사 액세스 권한을 부여하는 프로세스를 제공합니다.

## Status of This Memo

This document is not an Internet Standards Track specification; it is published for informational purposes.

This document is a product of the Internet Engineering Task Force (IETF). It represents the consensus of the IETF community. It has received public review and has been approved for publication by the Internet Engineering Steering Group (IESG). Not all documents approved by the IESG are a candidate for any level of Internet Standard; see Section 2 of RFC 5741.

Information about the current status of this document, any errata, and how to provide feedback on it may be obtained at http://www.rfc-editor.org/info/rfc5849.

## Copyright Notice

Copyright (c) 2010 IETF Trust and the persons identified as the document authors. All rights reserved.

This document is subject to BCP 78 and the IETF Trust's Legal Provisions Relating to IETF Documents (http://trustee.ietf.org/license-info) in effect on the date of publication of this document. Please review these documents carefully, as they describe your rights and restrictions with respect to this document. Code Components extracted from this document must include Simplified BSD License text as described in Section 4.e of the Trust Legal Provisions and are provided without warranty as described in the Simplified BSD License.

# Table of Contents

- [1. 소개](#1-소개)
  - [1.1. Terminology](#11-terminology)
  - [1.2. 예](#12-예)
  - [1.3. Notational Conventions](#13-notational-conventions)
- [2. 리디렉션 기반 권한 부여](#2-리디렉션-기반-권한-부여)
  - [2.1. 임시 자격 증명](#21-임시-자격-증명)
  - [2.2. 리소스 소유자 권한 부여](#22-리소스-소유자-권한-부여)
  - [2.3. 토큰 자격 증명](#23-토큰-자격-증명)
- [3. 인증 된 요청](#3-인증-된-요청)
  - [3.1. 요청 생성](#31-요청-생성)
  - [3.2. 요청 확인](#32-요청-확인)
  - [3.3. Nonce 및 Timestamp](#33-nonce-및-timestamp)
  - [3.4. Signature(서명)](#34-signature서명)
    - [3.4.1. 서명 기본 문자열](#341-서명-기본-문자열)
      - [3.4.1.1. 문자열 구성](#3411-문자열-구성)
      - [3.4.1.2. 기본 문자열 URI](#3412-기본-문자열-uri)
      - [3.4.1.3. 요청 매개 변수](#3413-요청-매개-변수)
        - [3.4.1.3.1. 매개 변수 소스](#34131-매개-변수-소스)
        - [3.4.1.3.2. 매개 변수 정규화](#34132-매개-변수-정규화)
    - [3.4.2. HMAC-SHA1](#342-hmac-sha1)
    - [3.4.3. RSA-SHA1](#343-rsa-sha1)
    - [3.4.3. RSA-SHA1](#343-rsa-sha1-1)
    - [3.4.4. PLAINTEXT](#344-plaintext)
  - [3.5. 매개 변수 전송](#35-매개-변수-전송)
    - [3.5.1. Authorization Header](#351-authorization-header)
    - [3.5.2. Form-Encoded Body](#352-form-encoded-body)
    - [3.5.3. URI 쿼리 요청](#353-uri-쿼리-요청)
  - [3.6. Percent Encoding](#36-percent-encoding)
- [4. 보안 고려 사항](#4-보안-고려-사항)
  - [4.1. RSA-SHA1 서명 방법](#41-rsa-sha1-서명-방법)
  - [4.2. 요청의 기밀성](#42-요청의-기밀성)
  - [4.3. 위조 서버에 의한 스푸핑](#43-위조-서버에-의한-스푸핑)
  - [4.4. 인증된 콘텐츠의 프록시 및 캐싱](#44-인증된-콘텐츠의-프록시-및-캐싱)
  - [4.5. 자격 증명의 일반 텍스트 저장소](#45-자격-증명의-일반-텍스트-저장소)
  - [4.6. 클라이언트 자격 증명의 비밀](#46-클라이언트-자격-증명의-비밀)
  - [4.7. 피싱 공격](#47-피싱-공격)
  - [4.8. 액세스 요청 범위](#48-액세스-요청-범위)
  - [4.9. 비밀의 엔트로피](#49-비밀의-엔트로피)
  - [4.10. 서비스 거부 / 자원 고갈 공격](#410-서비스-거부-자원-고갈-공격)
  - [4.11. SHA-1 암호화 공격](#411-sha-1-암호화-공격)
  - [4.12. 서명 기본 문자열 제한](#412-서명-기본-문자열-제한)
  - [4.13. CSRF (Cross-Site Request Forgery)](#413-csrf-cross-site-request-forgery)
  - [4.14. 사용자 인터페이스 교정](#414-사용자-인터페이스-교정)
  - [4.15. 반복 권한 부여의 자동 처리](#415-반복-권한-부여의-자동-처리)
- [5. Acknowledgments](#5-acknowledgments)
- [6. References](#6-references)
  - [6.1. Normative References](#61-normative-references)
  - [6.2. Informative References](#62-informative-references)

# 1. 소개

The OAuth protocol was originally created by a small community of web developers from a variety of websites and other Internet services who wanted to solve the common problem of enabling delegated access to protected resources. The resulting OAuth protocol was stabilized at version 1.0 in October 2007, and revised in June 2009 (Revision A) as published at <http://oauth.net/core/1.0a>.

This specification provides an informational documentation of OAuth Core 1.0 Revision A, addresses several errata reported since that time, and makes numerous editorial clarifications. While this specification is not an item of the IETF's OAuth Working Group, which at the time of writing is working on an OAuth version that can be appropriate for publication on the standards track, it has been transferred to the IETF for change control by authors of the original work.

전통적인 클라이언트-서버 인증 모델에서 클라이언트는 자격 증명을 사용하여 서버에서 호스팅하는 리소스에 액세스합니다. 분산 웹 서비스 및 클라우드 컴퓨팅의 사용이 증가함에 따라 타사 응용 프로그램은 이러한 서버 호스팅 리소스에 액세스해야합니다.

OAuth는 기존 클라이언트-서버 인증 모델에 세 번째 역할 인 리소스 소유자를 도입합니다. OAuth 모델에서 클라이언트 (리소스 소유자가 아니지만 대신 작동)는 리소스 소유자가 제어하지만 서버에서 호스팅하는 리소스에 대한 액세스를 요청합니다. 또한 OAuth를 통해 서버는 리소스 소유자 권한뿐만 아니라 요청하는 클라이언트의 ID도 확인할 수 있습니다.

OAuth는 클라이언트가 리소스 소유자 (예 : 다른 클라이언트 또는 최종 사용자)를 대신하여 서버 리소스에 액세스 할 수있는 방법을 제공합니다. 또한 최종 사용자가 사용자 에이전트 리디렉션을 사용하여 자격 증명 (일반적으로 사용자 이름 및 암호 쌍)을 공유하지 않고 서버 리소스에 대한 제 3 자 액세스 권한을 부여 할 수있는 프로세스를 제공합니다.

예를 들어 웹 사용자 (자원 소유자)는 사용자 이름과 암호를 인쇄 서비스와 공유하지 않고 사진 공유 서비스 (서버)에 저장된 개인 사진에 대한 인쇄 서비스 (클라이언트) 액세스 권한을 부여 할 수 있습니다. 대신, 그녀는 인쇄 서비스 위임 관련 자격 증명을 발급하는 사진 공유 서비스로 직접 인증합니다.

클라이언트가 리소스에 액세스하려면 먼저 리소스 소유자로부터 권한을 얻어야합니다. 이 권한은 토큰 및 일치하는 공유 비밀 형식으로 표현됩니다. 토큰의 목적은 리소스 소유자가 자격 증명을 클라이언트와 공유 할 필요가 없도록하는 것입니다. 리소스 소유자 자격 증명과 달리 토큰은 제한된 범위 및 제한된 수명으로 발급 될 수 있으며 독립적으로 취소 될 수 있습니다.

이 사양은 두 부분으로 구성됩니다. 첫 번째 부분은 서버에서 직접 인증하고 인증 방법과 함께 사용할 토큰을 클라이언트에 프로비저닝하여 최종 사용자가 리소스에 대한 클라이언트 액세스 권한을 부여하는 리디렉션 기반 사용자 에이전트 프로세스를 정의합니다. 두 번째 부분은 두 세트의 자격 증명을 사용하여 인증 된 HTTP [RFC2616] 요청을 만드는 방법을 정의합니다. 하나는 요청을 만드는 클라이언트를 식별하고 다른 하나는 요청을 대신하는 리소스 소유자를 식별합니다.

[RFC2616] 이외의 전송 프로토콜과 함께 OAuth를 사용하는 것은 정의되지 않았습니다.

## 1.1. Terminology

client  
OAuth 인증 요청을 할 수있는 HTTP 클라이언트 ([RFC2616]) ([Section 3](#3-인증-된-요청)).

server  
OAuth 인증 요청을 수락 할 수있는 HTTP 서버 ([RFC2616]) ([Section 3](#3-인증-된-요청)).

protected resource(보호 자원)  
OAuth 인증 요청을 사용하여 서버에서 얻을 수있는 액세스 제한 리소스 ([Section 3](#3-인증-된-요청)).

resource owner(자원 소유자)  
자격 증명을 사용하여 서버에 인증함으로써 보호된 리소스에 액세스하고 제어 할 수있는자 입니다.

credentials(자격 증명)  
자격 증명은 고유 식별자와 일치하는 공유 비밀의 쌍입니다. OAuth는 클라이언트, 임시 및 토큰의 세 가지 자격 증명 클래스를 정의하며, 각각 요청을 만드는 클라이언트, 권한 부여 요청 및 액세스 권한을 식별하고 인증하는 데 사용됩니다.

token(토큰)  
인증 된 요청을 클라이언트가 권한을 요청했거나 획득 한 리소스 소유자와 연결하기 위해 서버에서 발급하고 클라이언트가 사용하는 고유 식별자입니다. 토큰에는 클라이언트가 토큰 소유권을 설정하는 데 사용하는 일치하는 공유 암호와 리소스 소유자를 나타내는 권한이 있습니다.

원래 커뮤니티 사양에서는 다음과 같이이 사양에 매핑되는 다소 다른 용어를 사용했습니다 (원래 커뮤니티 용어 제공).:

Consumer: client

Service Provider: server

User: resource owner

Consumer Key and Secret: client credentials

Request Token and Secret: temporary credentials

Access Token and Secret: token credentials

## 1.2. 예

Jane (자원 소유자)은 최근 사진 공유 사이트 'photos.example.net'(서버)에 개인 휴가 사진 (보호 자원)을 업로드했습니다. 그녀는 'printer.example.com'웹 사이트 (클라이언트)를 사용하여 이러한 사진 중 하나를 인쇄하려고합니다. 일반적으로 Jane은 사용자 이름과 비밀번호를 사용하여 'photos.example.net'에 로그인합니다.

그러나 Jane은 사진을 인쇄하기 위해 액세스해야하는 'printer.example.com'웹 사이트와 사용자 이름 및 비밀번호를 공유하고 싶지 않습니다. 사용자에게 더 나은 서비스를 제공하기 위해 'printer.example.com'은 미리 'photos.example.net'클라이언트 자격 증명 세트에 등록했습니다:

Client Identifier(ID)  
dpf43f3p2l4k3l03

Client 공유 비밀:  
kd94hf93k423kf44

'printer.example.com'웹 사이트는 또한 "HMAC-SHA1"서명 방법을 사용하는 'photos.example.net'API 문서에 나열된 프로토콜 엔드 포인트를 사용하도록 애플리케이션을 구성했습니다:

Temporary Credential Request(임시 자격 증명 요청)  
https://photos.example.net/initiate

Resource Owner Authorization URI(리소스 소유자 권한 부여 URI):  
https://photos.example.net/authorize

Token Request URI(토큰 요청 URI):  
https://photos.example.net/token

'printer.example.com'이 Jane에게 사진에 대한 액세스 권한을 요청하기 전에 먼저 위임 요청을 식별하기 위해 'photos.example.net'으로 임시 자격 증명 집합을 설정해야합니다. 이를 위해 클라이언트는 다음 HTTPS [RFC2818] 요청을 서버에 보냅니다:

     POST /initiate HTTP/1.1
     Host: photos.example.net
     Authorization: OAuth realm="Photos",
        oauth_consumer_key="dpf43f3p2l4k3l03",
        oauth_signature_method="HMAC-SHA1",
        oauth_timestamp="137131200",
        oauth_nonce="wIjqoS",
        oauth_callback="http%3A%2F%2Fprinter.example.com%2Fready",
        oauth_signature="74KNZJeDHnMBp0EMJ9ZHt%2FXKycU%3D"

서버는 요청의 유효성을 검사하고 HTTP 응답 본문의 임시 자격 증명 집합으로 응답합니다 (줄 바꿈은 표시 목적으로 만 사용됨):

     HTTP/1.1 200 OK
     Content-Type: application/x-www-form-urlencoded

     oauth_token=hh5s93j4hdidpola&oauth_token_secret=hdhd0244k9j7ao03&
     oauth_callback_confirmed=true

클라이언트는 Jane의 사용자 에이전트를 서버의 리소스 소유자 자격증명 엔드 포인트로 리디렉션하여 Jane의 개인 사진 액세스에 대한 승인을 얻습니다:

     https://photos.example.net/authorize?oauth_token=hh5s93j4hdidpola

서버는 Jane에게 자신의 사용자 이름과 비밀번호를 사용하여 로그인하도록 요청하고 성공한 경우 개인 사진에 대한 'printer.example.com'액세스 권한 부여를 승인하도록 요청합니다. Jane은 요청을 승인하고 사용자 에이전트는 이전 요청에서 클라이언트가 제공 한 콜백 URI로 리디렉션됩니다 (줄 바꿈은 표시 용으로 만 사용됨):

     http://printer.example.com/ready?
     oauth_token=hh5s93j4hdidpola&oauth_verifier=hfdp7dh39dks9884

콜백 요청은 Jane이 권한 부여 프로세스를 완료했음을 클라이언트에 알립니다. 그런 다음 클라이언트는 임시 자격 증명을 사용하여 토큰 자격 증명 집합을 요청합니다 (보안 TLS (전송 계층 보안) 채널을 통해):

     POST /token HTTP/1.1
     Host: photos.example.net
     Authorization: OAuth realm="Photos",
        oauth_consumer_key="dpf43f3p2l4k3l03",
        oauth_token="hh5s93j4hdidpola",
        oauth_signature_method="HMAC-SHA1",
        oauth_timestamp="137131201",
        oauth_nonce="walatlh",
        oauth_verifier="hfdp7dh39dks9884",
        oauth_signature="gKgrFCywp7rO0OXSjdot%2FIHF7IU%3D"

서버는 요청의 유효성을 검사하고 HTTP 응답 본문의 토큰 자격 증명 집합으로 응답합니다:

     HTTP/1.1 200 OK
     Content-Type: application/x-www-form-urlencoded

     oauth_token=nnch734d00sl2jdk&oauth_token_secret=pfkkdhi9sl3r4s00

토큰 자격 증명 집합을 사용하여 클라이언트는 이제 비공개 사진을 요청할 준비가되었습니다:

     GET /photos?file=vacation.jpg&size=original HTTP/1.1
     Host: photos.example.net
     Authorization: OAuth realm="Photos",
        oauth_consumer_key="dpf43f3p2l4k3l03",
        oauth_token="nnch734d00sl2jdk",
        oauth_signature_method="HMAC-SHA1",
        oauth_timestamp="137131202",
        oauth_nonce="chapoH",
        oauth_signature="MdpQcU8iPSUjWoN%2FUDMsK2sui9I%3D"

'photos.example.net'서버는 요청의 유효성을 검사하고 요청된 사진으로 응답합니다. 'printer.example.com'은 Jane의 권한 부여 기간 동안 또는 Jane이 액세스를 취소 할 때까지 동일한 토큰 자격 증명을 사용하여 Jane의 비공개 사진에 계속 액세스 할 수 있습니다.

## 1.3. Notational Conventions

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC2119].

# 2. 리디렉션 기반 권한 부여

OAuth는 토큰을 사용하여 리소스 소유자가 클라이언트에 부여한 권한을 나타냅니다. 일반적으로 토큰 자격 증명은 리소스 소유자의 ID를 인증 한 후 (일반적으로 사용자 이름과 암호를 사용하여) 리소스 소유자의 요청에 따라 서버에서 발급됩니다.

서버가 토큰 자격 증명의 프로비저닝을 용이하게 할 수있는 많은 방법이 있습니다. 이 섹션에서는 HTTP 리디렉션 및 리소스 소유자의 사용자 에이전트를 사용하여 이러한 방법을 정의합니다. 이 리디렉션 기반 권한 부여 방법에는 다음 세 단계가 포함됩니다.

1. 클라이언트는 서버에서 임시 자격 증명 집합 (식별자 및 공유 비밀 형식)을 얻습니다. 임시 자격 증명은 권한 부여 프로세스 전체에서 액세스 요청을 식별하는 데 사용됩니다.

2. 리소스 소유자는 서버가 클라이언트의 액세스 요청 (임시 자격 증명으로 식별 됨)을 허용 할 권한을 부여합니다.

3. 클라이언트는 임시 자격 증명을 사용하여 서버에서 토큰 자격 증명 집합을 요청하여 리소스 소유자의 보호 된 리소스에 액세스 할 수 있도록합니다.

서버는 토큰 자격 증명을 얻기 위해 한 번 사용한 후에 임시 자격 증명을 취소해야합니다. 임시 자격 증명의 수명이 제한되는 것이 좋습니다. 서버는 리소스 소유자가 클라이언트에게 발급 된 토큰 자격 증명을 취소 할 수 있도록해야합니다 (SHOULD).

클라이언트가 이러한 단계를 수행하려면 서버가 다음 세 엔드 포인트의 URI를 공시해야합니다.

임시 자격 증명 요청  
[Section 2.1](#21-임시-자격-증명)에 설명 된대로 클라이언트가 임시 자격 증명 집합을 얻기 위해 사용하는 엔드 포인트입니다.

리소스 소유자 권한 부여  
[Section 2.2](#22-리소스-소유자-권한-부여)에 설명 된대로 권한을 부여하기 위해 리소스 소유자가 리디렉션되는 엔드 포인트입니다.

토큰 요청  
[Section 2.3](#23-토큰-자격-증명)에 설명 된대로 임시 자격 증명 집합을 사용하여 토큰 자격 증명 집합을 요청하기 위해 클라이언트가 사용하는 엔드 포인트입니다.

서버가 알리는 3 개의 URI는 [RFC3986] Section 3에 정의 된 쿼리 구성 요소를 포함 할 수 있지만 존재하는 경우 쿼리는 사용시 URI에 추가 된 프로토콜 매개 변수와의 충돌을 피하기 위해 "oauth\_"접두어로 시작하는 매개 변수를 포함하지 않아야합니다.

서버가 3 개의 엔드 포인트를 알리고 문서화하는 방법은 이 사양의 범위를 벗어납니다. 클라이언트는 토큰 크기 및 기타 서버 생성 값에 대한 가정을 피해야합니다.이 값은이 사양에서 정의하지 않습니다. 또한 프로토콜 매개 변수는 전송할 때 인코딩이 필요한 값을 포함 할 수 있습니다. 클라이언트와 서버는 값의 가능한 범위에 대해 가정해서는 안됩니다.

## 2.1. 임시 자격 증명

클라이언트는 임시 자격 증명 요청 엔드 포엔트에 대한 인증된 ([Section 3](#3-인증-된-요청)3) HTTP "POST"요청을 수행하여 서버에서 임시 자격 증명 집합을 얻습니다 (서버가 클라이언트가 사용할 다른 HTTP 요청 방법을 공시하지 않는 한). 클라이언트는 다음 REQUIRED 매개 변수를 요청에 추가하여 요청 URI를 구성합니다 (다른 프로토콜 매개 변수에도 동일한 매개 변수 전송 방법을 사용합니다.).

oauth_callback:  
리소스 소유자 권한 부여 단계 ([Section 2.2](#22-리소스-소유자-권한-부))가 완료되면 서버가 리소스 소유자를 리디렉션 할 절대 URI입니다. 클라이언트가 콜백을 수신 할 수 없거나 다른 수단을 통해 콜백 URI가 설정된 경우 매개 변수 값은 대역 외 구성을 나타 내기 위해 "oob"(대소문자 구분)로 설정되어야합니다.

서버는 추가 매개 변수를 지정할 수 있습니다.

요청을 할 때 클라이언트는 클라이언트 자격 증명만 사용하여 인증합니다. 클라이언트는 요청에서 빈 "oauth_token"프로토콜 매개 변수를 생략 할 수 있으며 토큰 비밀값으로 빈 문자열을 사용해야합니다.

요청으로 인해 HTTP 응답에서 일반 텍스트 자격 증명이 전송되므로 서버는 TLS 또는 SSL (Secure Socket Layer)과 같은 전송 계층 메커니즘 (또는 이와 동등한 보호 기능이있는 보안 채널)을 사용해야합니다.

예를 들어 클라이언트는 다음 HTTPS 요청을 수행합니다:

     POST /request_temp_credentials HTTP/1.1
     Host: server.example.com
     Authorization: OAuth realm="Example",
        oauth_consumer_key="jd83jd92dhsh93js",
        oauth_signature_method="PLAINTEXT",
        oauth_callback="http%3A%2F%2Fclient.example.net%2Fcb%3Fx%3D1",
        oauth_signature="ja893SD9%26"

서버는 요청을 확인하고 ([Section 3.2](#32-요청-확인)) 유효한 경우 임시 자격 증명 세트 (식별자 및 공유 비밀 형식)로 클라이언트에 다시 응답해야합니다. 임시 자격 증명은 200 상태 코드 (OK)와 함께 [W3C.REC-html40-19980424]에 정의 된 "application/x-www-form-urlencoded"콘텐츠 유형을 사용하여 HTTP 응답 본문에 포함됩니다.

응답에는 다음 필수 매개 변수가 포함됩니다.

oauth_token  
임시 자격 증명 식별자입니다.

oauth_token_secret  
임시 자격 증명 공유 비밀입니다.

oauth_callback_confirmed  
"true"로 설정해야합니다. 이 매개 변수는 프로토콜의 이전 버전과 구별하는 데 사용됩니다.

매개 변수 이름에 '토큰'이라는 용어가 포함되어 있더라도 이러한 자격 증명은 토큰 자격 증명이 아니지만 토큰 자격 증명과 유사한 방식으로 다음 두 단계에서 사용됩니다.

예 (줄 바꿈은 표시 목적으로 만 사용됨):

     HTTP/1.1 200 OK
     Content-Type: application/x-www-form-urlencoded

     oauth_token=hdk48Djdsa&oauth_token_secret=xyz4992k83j47x0b&
     oauth_callback_confirmed=true

## 2.2. 리소스 소유자 권한 부여

클라이언트는 서버에서 토큰 자격 증명 집합을 요청하기 전에 사용자를 서버로 보내 요청을 승인해야합니다. 클라이언트는 리소스 소유자 권한 부여 엔드 포인트 URI에 다음 REQUIRED 쿼리 매개 변수를 추가하여 요청 URI를 생성합니다.

oauth_token  
"oauth_token"매개 변수의 [Section 2.1](#21-임시-자격-증명)에서 얻은 임시 자격 증명 식별자입니다. 서버는 이 매개 변수를 OPTIONAL로 선언 할 수 있으며,이 경우 리소스 소유자가 다른 수단을 통해 식별자를 표시하는 방법을 제공해야합니다.

서버는 추가 매개 변수를 지정할 수 있습니다.

클라이언트는 HTTP 리디렉션 응답을 사용하거나 리소스 소유자의 사용자 에이전트를 통해 사용할 수 있는 다른 방법을 사용하여 구성된 URI로 리소스 소유자를 보냅니다. 요청은 반드시 HTTP "GET"메소드를 사용해야합니다.

예를 들어 클라이언트는 리소스 소유자의 사용자 에이전트를 리디렉션하여 다음 HTTPS 요청을 수행합니다.

     GET /authorize_access?oauth_token=hdk48Djdsa HTTP/1.1
     Host: server.example.com

TLS/SSL과 같은 보안 채널을 사용하는지 여부를 포함하여 서버가 권한 부여 요청을 처리하는 방법은 이 사양의 범위를 벗어납니다. 그러나 서버는 먼저 리소스 소유자의 신원을 확인해야합니다.

리소스 소유자에게 요청된 액세스 권한을 요청할 때 서버는 클라이언트 ID와 임시 자격 증명의 연결을 기반으로 액세스를 요청하는 클라이언트에 대한 정보를 리소스 소유자에게 제시해야합니다 (SHOULD). 그러한 정보를 표시 할 때 서버는 정보가 확인되었는지 여부를 표시해야합니다 (SHOULD).

자원 소유자로부터 권한 결정을 받은 후 서버는 "oauth_callback"매개 변수 또는 다른 방법으로 제공된 경우 자원 소유자를 콜백 URI로 리디렉션합니다.

액세스 권한을 부여하는 리소스 소유자가 프로세스를 완료하기 위해 클라이언트에게 반환하는 동일한 리소스 소유자인지 확인하기 위해 서버는 반드시 확인 코드를 생성해야합니다: 리소스 소유자를 통해 클라이언트에 전달 된 추측 할 수 없는 값이며 프로세스를 완료하는 데 필요합니다. 서버는 콜백 URI 쿼리 구성 요소에 다음 REQUIRED 매개 변수를 추가하여 요청 URI를 구성합니다.

oauth_token  
클라이언트에서 받은 임시 자격 증명 식별자(ID)입니다.

oauth_verifier  
인증 코드입니다.

콜백 URI에 쿼리 구성 요소가 이미 포함되어있는 경우 서버는 기존 쿼리 끝에 OAuth 매개 변수를 추가해야합니다.

예를 들어 서버는 다음 HTTP 요청을 만들기 위해 리소스 소유자의 사용자 에이전트를 리디렉션합니다:

     GET /cb?x=1&oauth_token=hdk48Djdsa&oauth_verifier=473f82d3 HTTP/1.1
     Host: client.example.net

클라이언트가 콜백 URI를 제공하지 않은 경우 서버는 확인 코드의 값을 표시하고 리소스 소유자에게 권한이 완료되었음을 클라이언트에게 수동으로 알리도록 지시해야합니다. 서버가 클라이언트가 제한된 장치에서 실행되고 있음을 알고 있다면 검증 값이 적합한 지 확인해야합니다 (SHOULD).

## 2.3. 토큰 자격 증명

클라이언트는 토큰 요청 엔드 포인트에 대해 인증된 ([Section 3](#3-인증-된-요청)) HTTP "POST"요청을 수행하여 서버에서 토큰 자격 증명 집합을 얻습니다 (서버가 클라이언트가 사용할 다른 HTTP 요청 방법을 알리지 않는 경우). 클라이언트는 다음 REQUIRED 매개 변수를 요청에 추가하여 요청 URI를 구성합니다 (다른 프로토콜 매개 변수에도 동일한 매개 변수 전송 방법을 사용).

oauth_verifier  
이전 단계에서 서버로부터 받은 확인 코드입니다.

요청을 할 때 클라이언트는 클라이언트 자격 증명과 임시 자격 증명을 사용하여 인증합니다. 임시 자격 증명은 인증된 요청에서 토큰 자격 증명 대신 사용되며 "oauth_token"매개 변수를 사용하여 전송됩니다.

요청으로 인해 HTTP 응답에서 일반 텍스트 자격 증명이 전송되므로 서버는 TLS 또는 SSL (또는 이와 동등한 보호 기능이있는 보안 채널)과 같은 전송 계층 메커니즘을 사용해야합니다.

예를 들어 클라이언트는 다음 HTTPS 요청을 수행합니다:

     POST /request_token HTTP/1.1
     Host: server.example.com
     Authorization: OAuth realm="Example",
        oauth_consumer_key="jd83jd92dhsh93js",
        oauth_token="hdk48Djdsa",
        oauth_signature_method="PLAINTEXT",
        oauth_verifier="473f82d3",
        oauth_signature="ja893SD9%26xyz4992k83j47x0b"

서버는 요청의 유효성을 확인하고 ([Section 3.2](#32-요청-확인)), 리소스 소유자가 클라이언트에 대한 토큰 자격 증명의 프로비저닝을 승인했는지 확인하고, 임시 자격 증명이 만료되었거나 이전에 사용되지 않았는지 확인해야합니다. 서버는 또한 클라이언트로부터 받은 확인 코드를 확인해야합니다. 요청이 유효하고 승인 된 경우 토큰 자격 증명은 200 status code (OK)와 함께 [W3C.REC-html40-19980424]에 정의 된 "application/x-www-form-urlencoded"콘텐츠 유형을 사용하여 HTTP 응답 본문에 포함됩니다.

응답에는 다음 필수 매개 변수가 포함됩니다:

oauth_token
토큰 식별자입니다.

oauth_token_secret
토큰 공유 비밀.

예를 들면 :

     HTTP/1.1 200 OK
     Content-Type: application/x-www-form-urlencoded

     oauth_token=j49ddk933skd9dks&oauth_token_secret=ll399dj47dskfjdk

서버는 리소스 소유자가 승인 한 범위, 기간 및 기타 속성을 유지해야하며 발행 된 토큰 자격 증명으로 이루어진 클라이언트 요청을 받을 때 이러한 제한을 적용해야합니다.

클라이언트가 토큰 자격 증명을 받고 저장하면 받은 토큰 자격 증명과 함께 클라이언트 자격 증명을 사용하여 인증된 요청 ([Section 3](#3-인증-된-요청))을 만들어 리소스 소유자 대신 보호된 리소스에 액세스 할 수 있습니다.

# 3. 인증 된 요청

[RFC2617]에서 정의한 HTTP 인증 방법을 사용하면 클라이언트가 인증된 HTTP 요청을 할 수 있습니다. 이러한 방법을 사용하는 클라이언트는 자격 증명 (일반적으로 사용자 이름 및 암호 쌍)을 사용하여 보호 된 리소스에 액세스 할 수 있으며,이를 통해 서버는 인증을 확인할 수 있습니다. 이러한 위임 방법을 사용하려면 클라이언트가 리소스 소유자의 역할을 맡아야합니다.

OAuth는 각 요청에 두 세트의 자격 증명을 포함하도록 설계된 방법을 제공합니다. 하나는 클라이언트를 식별하고 다른 하나는 리소스 소유자를 식별합니다. 클라이언트가 리소스 소유자를 대신하여 인증된 요청을 할 수 있으려면 먼저 리소스 소유자가 승인 한 토큰을 얻어야합니다. [Section 2](#2-리디렉션-기반-권한-부여)는 클라이언트가 리소스 소유자가 승인 한 토큰을 얻을 수있는 한 가지 방법을 제공합니다.

클라이언트 자격 증명은 고유 식별자 및 관련 공유 비밀 또는 RSA 키 쌍의 형식을 취합니다. 인증된 요청을 하기 전에 클라이언트는 서버에 자격 증명 집합을 설정합니다. 이들을 프로비저닝하기 위한 프로세스 및 요구 사항은 이 사양의 범위를 벗어납니다. 구현자는 클라이언트 자격 증명을 사용하여 보안에 미치는 영향을 고려해야하며, 그중 일부는 [Section 4.6](#46-클라이언트-자격-증명의-비밀)에 설명되어 있습니다.

인증된 요청을하려면 서버 구성에 대한 사전 지식이 필요합니다. OAuth에는 요청과 함께 프로토콜 매개 변수를 전송하는 여러 방법 ([Section 3.5](#35-매개-변수-전송))과 클라이언트가 사용된 자격 증명의 정당한 소유권을 증명하는 여러 방법 ([Section 3.4](#34-signature서명))이 포함됩니다. 클라이언트가 필요한 구성을 검색하는 방법은 이 사양의 범위를 벗어납니다.

## 3.1. 요청 생성

인증 된 요청에는 여러 프로토콜 매개 변수가 포함됩니다. 각 매개 변수 이름은 "oauth\_"접두사로 시작하며 매개 변수 이름과 값은 대소문자를 구분합니다. 클라이언트는 프로토콜 매개 변수 집합의 값을 계산하고 다음과 같이 HTTP 요청에 추가하여 인증된 요청을합니다.

1. 클라이언트는 각 필수 (달리 지정되지 않는 한) 프로토콜 매개 변수 각각에 값을 할당합니다.

   oauth_consumer_key  
   클라이언트 자격 증명의 식별자 부분 (사용자 이름과 동일). 매개 변수 이름은 사양의 이전 개정에서 사용된 더 이상 사용되지 않는 용어 (Consumer Key)를 반영하며 이전 버전과의 호환성을 유지하기 위해 유지되었습니다.

   oauth_token  
   요청을 리소스 소유자와 연결하는 데 사용되는 토큰 값입니다. 요청이 리소스 소유자와 연결되지 않은 경우 (사용 가능한 토큰 없음) 클라이언트는 매개 변수를 생략 할 수 있습니다.

   oauth_signature_method  
   [Section 3.4](#34-signature서명)에 정의 된대로 요청에 서명하기 위해 클라이언트가 사용하는 서명 방법의 이름입니다.

   oauth_timestamp  
   [Section 3.3](#33-nonce-및-timestamp)에 정의 된 타임 스탬프 값. "PLAINTEXT"서명 방법을 사용하는 경우 매개 변수를 생략 할 수 있습니다.

   oauth_nonce  
   [Section 3.3](#33-nonce-및-timestamp)에 정의 된 nonce 값입니다. "PLAINTEXT"서명 방법을 사용하는 경우 매개 변수를 생략 할 수 있습니다.

   oauth_version  
   OPTIONAL. 있는 경우 "1.0"으로 설정해야합니다. 이 사양에 정의 된대로 인증 프로세스의 버전을 제공합니다.

2. 프로토콜 매개 변수는 [Section 3.5](#35-매개-변수-전송)에 나열된 전송 방법 중 하나를 사용하여 요청에 추가됩니다. 각 매개 변수는 요청 당 두 번 이상 나타나지 않아야합니다.

3. 클라이언트는 [Section 3.4](#34-signature서명)에 설명 된대로 "oauth_signature"매개 변수의 값을 계산 및 할당하고 이전 단계에서와 동일한 방법을 사용하여 요청에 매개 변수를 추가합니다.

4. 클라이언트는 인증 된 HTTP 요청을 서버로 보냅니다.

예를 들어 다음 HTTP 요청을 인증하려면 (다음 예에서 "c2&a3=2+q"문자열은 form-encoded entity-body의 영향을 설명하는 데 사용됩니다.):

     POST /request?b5=%3D%253D&a3=a&c%40=&a2=r%20b HTTP/1.1
     Host: example.com
     Content-Type: application/x-www-form-urlencoded

     c2&a3=2+q

클라이언트는 클라이언트 자격 증명, 토큰 자격 증명, 현재 타임 스탬프, 고유하게 생성 된 nonce를 사용하여 다음 프로토콜 매개 변수에 값을 할당하고 "HMAC-SHA1"서명 방법을 사용할 것임을 나타냅니다.

     oauth_consumer_key:     9djdj82h48djs9d2
     oauth_token:            kkk9d7dh3k39sjv7
     oauth_signature_method: HMAC-SHA1
     oauth_timestamp:        137131201
     oauth_nonce:            7d8f3e4a

클라이언트는 OAuth HTTP "Authorization"헤더 필드를 사용하여 요청에 프로토콜 매개 변수를 추가합니다.

     Authorization: OAuth realm="Example",
                    oauth_consumer_key="9djdj82h48djs9d2",
                    oauth_token="kkk9d7dh3k39sjv7",
                    oauth_signature_method="HMAC-SHA1",
                    oauth_timestamp="137131201",
                    oauth_nonce="7d8f3e4a"

그런 다음 "oauth_signature"매개 변수의 값 (클라이언트 비밀 "j49sk3j29djd"및 토큰 비밀 "dh893hdasih9"사용)을 계산하고 요청에 추가 한 다음 HTTP 요청을 서버로 보냅니다.

     POST /request?b5=%3D%253D&a3=a&c%40=&a2=r%20b HTTP/1.1
     Host: example.com
     Content-Type: application/x-www-form-urlencoded
     Authorization: OAuth realm="Example",
                    oauth_consumer_key="9djdj82h48djs9d2",
                    oauth_token="kkk9d7dh3k39sjv7",
                    oauth_signature_method="HMAC-SHA1",
                    oauth_timestamp="137131201",
                    oauth_nonce="7d8f3e4a",
                    oauth_signature="bYT5CMsGcbgUdFHObYMEfcx6bsw%3D"

     c2&a3=2+q

## 3.2. 요청 확인

인증된 요청을 수신하는 서버는 다음을 통해 확인해야합니다.

o [Section 3.4](#34-signature서명)에 설명 된대로 요청 서명을 독립적으로 재계산하고 "oauth_signature"매개 변수를 통해 클라이언트로부터 받은 값과 비교합니다.

o "HMAC-SHA1"또는 "RSA-SHA1"서명 방법을 사용하는 경우 이전 요청에서 클라이언트로부터 받은 nonce/timestamp/token이 있다면 이전에 사용되지 않았는지 확인합니다 (서버는 [Section 3.3](#33-nonce-및-timestamp)에 설명 된대로 오래된 timestamp가 있는 요청을 거부 할 수 있습니다).

o 토큰이있는 경우 토큰으로 표시되는 클라이언트 인증의 범위 및 상태를 확인합니다 (서버는 토큰 사용을 발행된 클라이언트로 제한할 수 있습니다).

o "oauth_version"매개 변수가있는 경우 해당 값이 "1.0"인지 확인합니다.

요청이 확인에 실패하면 서버는 적절한 HTTP 응답 상태 코드로 응답해야합니다 (SHOULD). 서버는 응답 본문에 요청이 거부 된 이유에 대한 자세한 정보를 포함 할 수 있습니다.

서버는 지원되지 않는 매개 변수, 지원되지 않는 서명 방법, 누락 된 매개 변수 또는 중복된 프로토콜 매개 변수가있는 요청을 수신 할 때 400 (Bad Request) 상태 코드를 반환해야합니다 (SHOULD). 서버는 유효하지 않은 클라이언트 자격 증명, 유효하지 않거나 만료 된 토큰, 유효하지 않은 서명 또는 유효하지 않거나 사용 된 임시 값이있는 요청을 수신 할 때 401 (Unauthorized) 상태 코드를 반환해야합니다 (SHOULD).

## 3.3. Nonce 및 Timestamp

타임 스탬프 값은 양의 정수 여야합니다. 서버 설명서에 달리 지정되지 않는 한 타임 스탬프는 January 1, 1970 00:00:00 GMT 이후의 초 수로 표시됩니다.

nonce는 클라이언트가 고유하게 생성 한 임의의 문자열로, 서버가 이전에 요청한 적이 없는지 확인하고 비보안 채널을 통해 요청이 있을 때 재생 공격을 방지하는 데 도움이됩니다. nonce 값은 타임 스탬프, 클라이언트 자격 증명 및 토큰 조합이 동일한 모든 요청에서 고유해야합니다.

향후 검사를 위해 nonce 값을 무한히 유지할 필요가 없도록 서버는 이전 타임 스탬프가 있는 요청이 거부되는 기간을 제한하도록 선택할 수 있습니다. 이 제한은 클라이언트와 서버 시계 간의 동기화 수준을 의미합니다. 이러한 제한을 적용하는 서버는 클라이언트가 서버의 시계와 동기화 할 수있는 방법을 제공 할 수 있습니다. 또는 두 시스템 모두 신뢰할 수있는 시간 서비스와 동기화 할 수 있습니다. 클록 동기화 전략의 세부 사항은 이 사양의 범위를 벗어납니다.

## 3.4. Signature(서명)

OAuth 인증 요청은 "oauth_consumer_key"매개 변수를 통해 전달 된 것과 "oauth_token"매개 변수의 두 가지 자격 증명 세트를 가질 수 있습니다. 서버가 요청의 진위를 확인하고 무단 액세스를 방지하려면 클라이언트가 자격 증명의 정당한 소유자임을 증명해야합니다. 이는 각 자격 증명 집합의 공유 비밀 (또는 RSA 키) 부분을 사용하여 수행됩니다.

OAuth는 클라이언트가 자격 증명의 정당한 소유권을 증명할 수있는 세 가지 방법을 제공합니다: "HMAC-SHA1", "RSA-SHA1"및 "PLAINTEXT". 이러한 메서드는 "PLAINTEXT"에 서명이 포함되지 않더라도 일반적으로 서명 메서드라고합니다. 또한 "RSA-SHA1"은 클라이언트 자격 증명과 관련된 공유 비밀 대신 RSA 키를 사용합니다.

OAuth는 각 구현에 고유 한 요구 사항이있을 수 있으므로 특정 서명 방법을 요구하지 않습니다. 서버는 자체 사용자 지정 메서드를 자유롭게 구현하고 문서화 할 수 있습니다. 특정 방법을 권장하는 것은 이 사양의 범위를 벗어납니다. 구현자는 지원할 방법을 결정하기 전에 보안 고려 사항 [Section 4](#4-보안-고려-사항)을 검토해야합니다.

클라이언트는 "oauth_signature_method"매개 변수를 통해 사용되는 서명 방법을 선언합니다. 그런 다음 서명 (또는 동등한 값의 문자열)을 생성하고 "oauth_signature"매개 변수에 포함합니다. 서버는 각 방법에 지정된대로 서명을 확인합니다.

서명 프로세스는 "oauth_signature"매개 변수를 제외하고 요청 또는 해당 매개 변수를 변경하지 않습니다.

### 3.4.1. 서명 기본 문자열

서명 기본 문자열은 여러 HTTP 요청 요소를 단일 문자열로 일관되고 재현 가능한 연결입니다. 이 문자열은 "HMAC-SHA1"및 "RSA-SHA1"서명 방법에 대한 입력으로 사용됩니다.

서명 기본 문자열에는 HTTP 요청의 다음 구성 요소가 포함됩니다.

o HTTP 요청 방법 (예: "GET", "POST"등).

o HTTP "Host"요청 헤더 필드에 선언된 권한.

o 요청 리소스 URI의 경로 및 쿼리 구성 요소.

o "oauth_signature"를 제외한 프로토콜 매개 변수.

o [Section 3.4.1.3](#3413-요청-매개-변수)에 정의 된 엄격한 제한 사항을 준수하는 경우 요청 엔티티 본문에 포함 된 매개 변수.

서명 기본 문자열은 전체 HTTP 요청을 포함하지 않습니다. 특히 대부분의 요청에 entity-body을 포함하지 않으며 대부분의 HTTP entity-headers도 포함하지 않습니다. 서버는 SSL / TLS 또는 기타 방법과 같은 추가 보호를 사용하지 않고 배재된 요청 구성 요소의 신뢰성을 확인할 수 없다는 점에 유의해야합니다.

#### 3.4.1.1. 문자열 구성

서명 기본 문자열은 다음 HTTP 요청 요소를 순서대로 함께 연결하여 구성됩니다.

1. 대문자로 된 HTTP 요청 방법. 예: "HEAD", "GET", "POST"등. 요청이 사용자 지정 HTTP 메서드를 사용하는 경우 반드시 인코딩되어야합니다 ([Section 3.6](#36-percent-encoding)).

2. "&"문자 (ASCII code 38).

3. 인코딩 후 [Section 3.4.1.2](#3412-기본-문자열-uri)의 기본 문자열 URI ([Section 3.6](#36-percent-encoding)).

4. "&"문자 (ASCII 코드 38).

5. 인코딩 된 후 섹션 [Section 3.4.1.3.2](#34132-매개-변수-정규화)에서 정규화 된 요청 매개 변수 ([Section 3.6](#36-percent-encoding)).

예를 들어, HTTP 요청:

     POST /request?b5=%3D%253D&a3=a&c%40=&a2=r%20b HTTP/1.1
     Host: example.com
     Content-Type: application/x-www-form-urlencoded
     Authorization: OAuth realm="Example",
                    oauth_consumer_key="9djdj82h48djs9d2",
                    oauth_token="kkk9d7dh3k39sjv7",
                    oauth_signature_method="HMAC-SHA1",
                    oauth_timestamp="137131201",
                    oauth_nonce="7d8f3e4a",
                    oauth_signature="bYT5CMsGcbgUdFHObYMEfcx6bsw%3D"

     c2&a3=2+q

는 다음 서명 기본 문자열로 표시됩니다 (줄 바꿈은 표시 목적으로만 사용됨):

     POST&http%3A%2F%2Fexample.com%2Frequest&a2%3Dr%2520b%26a3%3D2%2520q
     %26a3%3Da%26b5%3D%253D%25253D%26c%2540%3D%26c2%3D%26oauth_consumer_
     key%3D9djdj82h48djs9d2%26oauth_nonce%3D7d8f3e4a%26oauth_signature_m
     ethod%3DHMAC-SHA1%26oauth_timestamp%3D137131201%26oauth_token%3Dkkk
     9d7dh3k39sjv7

#### 3.4.1.2. 기본 문자열 URI

요청 리소스 URI [RFC3986]의 스키마, 권한 및 경로는 다음과 같이 요청 리소스 (query 또는 fragment 없이)를 나타내는 "http"또는 "https"URI를 구성하여 포함됩니다.

1. scheme와 host는 소문자 여야합니다.

2. host 및 포트 값은 HTTP 요청 "Host"헤더 필드의 내용과 일치해야합니다.

3. 포트가 스키마의 기본 포트가 아닌 경우 반드시 포함되어야하며, 기본 포트 인 경우 제외되어야합니다. 특히 포트 80에 대한 HTTP 요청 [RFC2616]을 만들거나 포트 443에 대한 HTTPS 요청 [RFC2818]을 만들 때 포트를 제외해야합니다. 다른 모든 비 기본 포트 번호는 반드시 포함되어야 합니다.

예를 들어, HTTP 요청:

     GET /r%20v/X?id=123 HTTP/1.1
     Host: EXAMPLE.COM:80

은 기본 문자열 URI "http://example.com/r%20v/X"로 표시됩니다.

또 다른 예 HTTPS 요청:

     GET /?q=1 HTTP/1.1
     Host: www.example.net:8080

은 기본 문자열 URI "https://www.example.net:8080/"로 표시됩니다.

#### 3.4.1.3. 요청 매개 변수

요청 매개 변수의 일관되고 재현 가능한 표현을 보장하기 위해 매개 변수가 수집되고 원래의 디코딩된 형식으로 디코딩됩니다. 그런 다음 원래 인코딩 체계와 다른 특정 방식으로 정렬 및 인코딩되고 단일 문자열로 연결됩니다.

##### 3.4.1.3.1. 매개 변수 소스

다음 소스의 매개 변수는 이름/값 쌍의 단일 목록으로 수집됩니다.

o [RFC3986] Section 3.4에 정의 된 HTTP 요청 URI의 쿼리 구성 요소. 쿼리 구성 요소는 "application/x-www-form-urlencoded"문자열로 취급하여 이름과 값을 분리하고 [W3C.REC-html40-19980424], Section 17.13.4에 정의 된대로 이름과 값을 분리하고 디코딩합니다.

o OAuth HTTP "Authorization"헤더 필드 ([Section 3.5.1](#351-authorization-header))가 있는 경우 헤더의 내용은 "realm"매개 변수가 있는 경우 제외하고 이름/값 쌍 목록으로 구문 분석됩니다. 매개 변수 값은 [Section 3.5.1](#351-authorization-header)에 ​​정의 된대로 디코딩됩니다.

o HTTP 요청 entity-body (다음 조건이 모두 충족되는 경우에만 해당)

      * entity-body는 single-part입니다.

      * entity-body는 [W3C.REC-html40-19980424]에 정의 된 "application/x-www-form-urlencoded"컨텐츠 유형의 인코딩 요구 사항을 따릅니다.

      * HTTP 요청 엔티티 헤더에는 "application/x-www-form-urlencoded"로 설정된 "Content-Type"헤더 필드가 포함됩니다.

엔티티 본문은 [W3C.REC-html40-19980424], Section 17.13.4에 설명 된대로 디코딩 된 이름/값 쌍 목록으로 구문 분석됩니다.

"oauth_signature"매개 변수가 있는 경우 서명 기본 문자열에서 제외되어야합니다. 요청에 명시 적으로 포함되지 않은 매개 변수는 서명 기본 문자열에서 제외되어야합니다 (예: 생략 된 경우 "oauth_version"매개 변수).

예를 들어, HTTP 요청:

       POST /request?b5=%3D%253D&a3=a&c%40=&a2=r%20b HTTP/1.1
       Host: example.com
       Content-Type: application/x-www-form-urlencoded
       Authorization: OAuth realm="Example",
                      oauth_consumer_key="9djdj82h48djs9d2",
                      oauth_token="kkk9d7dh3k39sjv7",
                      oauth_signature_method="HMAC-SHA1",
                      oauth_timestamp="137131201",
                      oauth_nonce="7d8f3e4a",
                      oauth_signature="djosJKDKJSD8743243%2Fjdk33klY%3D"

       c2&a3=2+q

는 서명 기반 문자열에 사용되는 다음 (완전히 디코딩 된) 매개 변수를 포함합니다.

          +------------------------+------------------+
          |          Name          |       Value      |
          +------------------------+------------------+
          |           b5           |       =%3D       |
          |           a3           |         a        |
          |           c@           |                  |
          |           a2           |        r b       |
          |   oauth_consumer_key   | 9djdj82h48djs9d2 |
          |       oauth_token      | kkk9d7dh3k39sjv7 |
          | oauth_signature_method |     HMAC-SHA1    |
          |     oauth_timestamp    |     137131201    |
          |       oauth_nonce      |     7d8f3e4a     |
          |           c2           |                  |
          |           a3           |        2 q       |
          +------------------------+------------------+

"b5"의 값은 "=="가 아니라 "=%3D"입니다. "c@"및 "c2"는 모두 빈 값을 갖습니다. 서명 기본 문자열을 구성하기 위해 이 사양에 지정된 인코딩 규칙은 인코딩 된 공백 문자 (ASCII code 32)를 나타내는 "+"문자 (ASCII code 43)의 사용을 제외하지만,이 관행은 "application/x-www-form-urlencoded"로 인코딩 된 값이며"a3 "매개 변수 인스턴스 중 하나에 의해 입증 된대로 적절하게 디코딩되어야합니다 (이 요청에서"a3"매개 변수가 두 번 사용됨).

##### 3.4.1.3.2. 매개 변수 정규화

[Section 3.4.1.3](#3413-요청-매개-변수)에서 수집 된 매개 변수는 다음과 같이 단일 문자열로 정규화됩니다.

1. 먼저 각 매개 변수의 이름과 값이 인코딩됩니다 ([Section 3.6](#36-percent-encoding)).

2. 매개 변수는 오름차순 바이트 값 순서를 사용하여 이름별로 정렬됩니다. 둘 이상의 매개 변수가 동일한 이름을 공유하는 경우 값을 기준으로 정렬됩니다.

3. 각 매개 변수의 이름은 값이 비어 있더라도 "="문자 (ASCII code 61)를 구분 기호로 사용하여 해당 값에 연결됩니다.

4. 정렬된 이름/값 쌍은 "&"문자 (ASCII code 38)를 구분 기호로 사용하여 단일 문자열로 함께 연결됩니다.

예를 들어, 이전 Section의 매개 변수 목록은 다음과 같이 정규화됩니다:

                              Encoded:

          +------------------------+------------------+
          |          Name          |       Value      |
          +------------------------+------------------+
          |           b5           |     %3D%253D     |
          |           a3           |         a        |
          |          c%40          |                  |
          |           a2           |       r%20b      |
          |   oauth_consumer_key   | 9djdj82h48djs9d2 |
          |       oauth_token      | kkk9d7dh3k39sjv7 |
          | oauth_signature_method |     HMAC-SHA1    |
          |     oauth_timestamp    |     137131201    |
          |       oauth_nonce      |     7d8f3e4a     |
          |           c2           |                  |
          |           a3           |       2%20q      |
          +------------------------+------------------+

                              Sorted:

          +------------------------+------------------+
          |          Name          |       Value      |
          +------------------------+------------------+
          |           a2           |       r%20b      |
          |           a3           |       2%20q      |
          |           a3           |         a        |
          |           b5           |     %3D%253D     |
          |          c%40          |                  |
          |           c2           |                  |
          |   oauth_consumer_key   | 9djdj82h48djs9d2 |
          |       oauth_nonce      |     7d8f3e4a     |
          | oauth_signature_method |     HMAC-SHA1    |
          |     oauth_timestamp    |     137131201    |
          |       oauth_token      | kkk9d7dh3k39sjv7 |
          +------------------------+------------------+

                         Concatenated Pairs:

               +-------------------------------------+
               |              Name=Value             |
               +-------------------------------------+
               |               a2=r%20b              |
               |               a3=2%20q              |
               |                 a3=a                |
               |             b5=%3D%253D             |
               |                c%40=                |
               |                 c2=                 |
               | oauth_consumer_key=9djdj82h48djs9d2 |
               |         oauth_nonce=7d8f3e4a        |
               |   oauth_signature_method=HMAC-SHA1  |
               |      oauth_timestamp=137131201      |
               |     oauth_token=kkk9d7dh3k39sjv7    |
               +-------------------------------------+

그리고 단일 문자열로 함께 연결됩니다 (줄 바꿈은 표시 목적으로 만 사용됨):

     a2=r%20b&a3=2%20q&a3=a&b5=%3D%253D&c%40=&c2=&oauth_consumer_key=9dj
     dj82h48djs9d2&oauth_nonce=7d8f3e4a&oauth_signature_method=HMAC-SHA1
     &oauth_timestamp=137131201&oauth_token=kkk9d7dh3k39sjv7

### 3.4.2. HMAC-SHA1

"HMAC-SHA1"서명 방법은 [RFC2104]에 정의 된 HMAC-SHA1 서명 알고리즘을 사용합니다.

      digest = HMAC-SHA1 (key, text)

HMAC-SHA1 함수 변수는 다음과 같은 방식으로 사용됩니다.

text는 [Section 3.4.1.1](#3411-문자열-구성)의 서명 기본 문자열 값으로 설정됩니다.

키는 다음의 연결된 값으로 설정됩니다.

1. 인코딩 된 후 클라이언트 공유 비밀 ([Section 3.6](#36-percent-encoding)).

2. "&"문자 (ASCII code 38). 비밀이 비어있는 경우에도 반드시 포함되어야합니다.

3. 인코딩 된 후 토큰 공유 비밀 ([Section 3.6](#36-percent-encoding)).

결과 옥텟 문자열이 [RFC2045], Section 6.8에 따라 base64로 인코딩 된 후 digest는 "oauth_signature"프로토콜 매개 변수의 값을 설정하는 데 사용됩니다.

### 3.4.3. RSA-SHA1

"RSA-SHA1"서명 방법은 EMSA-PKCS1-v1_5의 해시 함수로 SHA-1을 사용하여 [RFC3447], Section 8.2 (PKCS#1이라고도 함)에 정의 된 RSASSA-PKCS1-v1_5 서명 알고리즘을 사용합니다. 이 방법을 사용하려면 클라이언트는 RSA 공개키를 포함하는 서버에 클라이언트 자격 증명을 설정해야합니다 (이 사양의 범위를 벗어난 방식으로).

서명 기본 문자열은 [RFC3447], Section 8.2.1에 따라 클라이언트의 RSA 개인 키를 사용하여 서명됩니다.

### 3.4.3. RSA-SHA1

     S = RSASSA-PKCS1-V1_5-SIGN (K, M)

K는 클라이언트의 RSA 개인 키로 설정됩니다.

M은 [Section 3.4.1.1](#3411-문자열-구성)의 서명 기본 문자열 값으로 설정되고, S는 결과 octet 문자열이 [RFC2045] Section 6.8에 따라 base64로 인코딩 된 후 "oauth_signature"프로토콜 매개 변수의 값을 설정하는데 사용되는 결과 서명입니다.

서버는 [RFC3447] Section 8.2.2에 따라 서명을 확인합니다.

     RSASSA-PKCS1-V1_5-VERIFY ((n, e), M, S)

(n, e)는 클라이언트의 RSA 공개 키로 설정됩니다.

M은 섹션 [Section 3.4.1.1](#3411-문자열-구성)의 서명 기본 문자열 값으로 설정되고

S는 클라이언트로부터 받은 "oauth_signature"프로토콜 매개 변수의 octet 문자열값으로 설정됩니다.

### 3.4.4. PLAINTEXT

"PLAINTEXT"메서드는 서명 알고리즘을 사용하지 않습니다. TLS 또는 SSL과 같은 전송 계층 메커니즘과 함께 사용해야합니다 (또는 동등한 보호 기능이있는 보안 채널을 통해 전송). 서명 기본 문자열 또는 "oauth_timestamp"및 "oauth_nonce"매개 변수를 사용하지 않습니다.

"oauth_signature"프로토콜 매개 변수는 다음의 연결된 값으로 설정됩니다:

1. 인코딩 된 후 클라이언트 공유 비밀 ([Section 3.6](#36-percent-encoding)).

2. "&"문자 (ASCII code 38). 비밀이 비어있는 경우에도 반드시 포함되어야합니다.

3. 인코딩 된 후 토큰 공유 비밀 ([Section 3.6](#36-percent-encoding)).

## 3.5. 매개 변수 전송

OAuth 인증 요청을 할 때 프로토콜 매개 변수와 "oauth\_"접두사를 사용하는 다른 매개 변수는 선호하는 순서대로 나열된 다음 리스트 중 하나만 사용하여 요청에 포함되어야합니다.

1. [Section 3.5.1](#351-authorization-header)에 설명 된 HTTP "Authorization"헤더 필드.

2. [Section 3.5.2](#352-form-encoded-body)에 설명 된 HTTP 요청 엔티티 본문.

3. [Section 3.5.3](#353-uri-쿼리-요청)에 설명 된 HTTP 요청 URI 쿼리.

이 세 가지 방법 외에도 향후 확장은 요청에 프로토콜 매개 변수를 포함하기위한 다른 방법을 정의 할 수 있습니다.

### 3.5.1. Authorization Header

프로토콜 매개 변수는 인증 체계 이름이 "OAuth"(대소문자 구분 안 함)로 설정된 [RFC2617]에 정의 된대로 HTTP "Authorization"헤더 필드를 사용하여 전송할 수 있습니다.

예를 들면 :

     Authorization: OAuth realm="Example",
        oauth_consumer_key="0685bd9184jfhq22",
        oauth_token="ad180jjd733klru7",
        oauth_signature_method="HMAC-SHA1",
        oauth_signature="wOJIO9A2W5mFwDgiDvZbTSMK%2FPY%3D",
        oauth_timestamp="137131200",
        oauth_nonce="4572616e48616d6d65724c61686176",
        oauth_version="1.0"

프로토콜 매개 변수는 다음과 같이 "Authorization"헤더 필드에 포함되어야합니다.

1. 매개 변수 이름과 값은 매개 변수 인코딩 ([Section 3.6](#36-percent-encoding))에 따라 인코딩됩니다.

2. 각 매개 변수의 이름 바로 뒤에는 "="문자 (ASCII code 61), """문자 (ASCII code 34), 매개 변수 값 (비어있을 수 있음) 및 다른"""문자 (ASCII code 34)가옵니다.

3. 매개 변수는 ","문자 (ASCII code 44) 및 [RFC2617]에 따라 OPTIONAL 공백으로 구분됩니다.

4. OPTIONAL "realm"매개 변수는 [RFC2617] Section 1.2에 따라 추가 및 해석 될 수 있습니다.

서버는 보호된 리소스에 대한 클라이언트 요청시 HTTP "WWW-Authenticate"응답 헤더 필드를 반환하여 "OAuth"인증 체계에 대한 지원을 표시 할 수 있습니다. [RFC2617]에 따라 이러한 응답에는 추가 HTTP "WWW-Authenticate"헤더 필드가 포함될 수 있습니다.

예를 들면:

     WWW-Authenticate: OAuth realm="http://server.example.com/"

realm 매개 변수는 [RFC2617], Section 1.2에 따라 보호 영역을 정의합니다.

### 3.5.2. Form-Encoded Body

프로토콜 매개 변수는 HTTP 요청 엔티티 본문에서 전송할 수 있지만 다음 REQUIRED 조건이 충족되는 경우에만 가능합니다.

o 엔티티 바디는 single-part입니다.

o entity-body는 [W3C.REC-html40-19980424]에 정의 된 "application/x-www-form-urlencoded"컨텐츠 유형의 인코딩 요구 사항을 따릅니다.

o HTTP 요청 엔티티 헤더는 "application/x-www-form-urlencoded"로 설정된 "Content-Type"헤더 필드를 포함합니다.

예 (줄 바꿈은 표시 목적으로 만 사용됨):

     oauth_consumer_key=0685bd9184jfhq22&oauth_token=ad180jjd733klr
     u7&oauth_signature_method=HMAC-SHA1&oauth_signature=wOJIO9A2W5
     mFwDgiDvZbTSMK%2FPY%3D&oauth_timestamp=137131200&oauth_nonce=4
     572616e48616d6d65724c61686176&oauth_version=1.0

엔티티 본문은 다른 요청 특정 매개 변수를 포함 할 수 있습니다. 이 경우 프로토콜 매개 변수는 "&"문자 (ASCII code 38)로 적절히 구분 된 요청 특정 매개 변수 다음에 추가되어야합니다.

### 3.5.3. URI 쿼리 요청

프로토콜 파라미터는 [RFC3986], Section 3에 정의 된 쿼리 파라미터로 HTTP 요청 URI에 추가하여 전송할 수 있습니다.

예 (줄 바꿈은 표시 목적으로 만 사용됨) :

     GET /example/path?oauth_consumer_key=0685bd9184jfhq22&
     oauth_token=ad180jjd733klru7&oauth_signature_method=HM
     AC-SHA1&oauth_signature=wOJIO9A2W5mFwDgiDvZbTSMK%2FPY%
     3D&oauth_timestamp=137131200&oauth_nonce=4572616e48616
     d6d65724c61686176&oauth_version=1.0 HTTP/1.1

요청 URI는 다른 요청 특정 쿼리 매개 변수를 포함 할 수 있습니다. 이 경우 프로토콜 매개 변수는 "&"문자 (ASCII code 38)로 적절하게 구분 된 요청 특정 매개 변수 다음에 추가되어야합니다.

## 3.6. Percent Encoding

기존 Percent 인코딩 방법은 서명 기본 문자열의 일관된 구성을 보장하지 않습니다. 다음 퍼센트 인코딩 방법은 [RFC3986] 및 [W3C.REC-html40-19980424]에 정의 된 기존 인코딩 방법을 대체하기 위해 정의되지 않았습니다. 서명 기본 문자열 및 "Authorization"헤더 필드의 구성에만 사용됩니다.

이 사양은 Percent 인코딩 문자열에 대해 다음 방법을 정의합니다.

1. 텍스트 값은 [RFC3629] 당 UTF-8 octets이 아닌 경우 먼저 인코딩됩니다. 여기에는 사람이 사용할 수 없는 바이너리 값이 포함되지 않습니다.

2. 값은 다음과 같이 [RFC3986] percent 인코딩 (%XX) 메커니즘을 사용하여 escape됩니다.

   -[RFC3986], Section 2.3 (ALPHA, DIGIT, "-", ".", "\_", "~")에 정의 된 예약되지 않은 문자 집합의 문자는 인코딩되지 않아야합니다.

   -다른 모든 문자는 인코딩해야합니다.

   -인코딩 된 문자를 나타내는 데 사용되는 두 개의 16 진수 문자는 대문자 여야합니다.

이 방법은 "application/x-www-form-urlencoded"콘텐츠 유형에서 사용하는 인코딩 체계와 다릅니다 (예: 공백 문자를 "+"문자를 사용하지 않고 "%20"으로 인코딩). 웹 개발 프레임 워크에서 제공하는 Percent 인코딩 기능과 다를 수 있습니다 (예: 다른 문자 인코딩, 소문자 16 진수 문자 사용).

# 4. 보안 고려 사항

[RFC2617]에 명시된 바와 같이 가장 큰 위험 원은 일반적으로 핵심 프로토콜 자체가 아니라 사용을 둘러싼 정책 및 절차에서 발견됩니다. 구현자는 이 프로토콜이 보안 요구 사항을 충족하는지 여부를 스스로 평가하기를 권장합니다.

## 4.1. RSA-SHA1 서명 방법

"RSA-SHA1"서명으로 이루어진 인증된 요청은 토큰 공유 비밀 또는 프로비저닝 된 클라이언트 공유 비밀을 사용하지 않습니다. 이는 요청이 요청에 서명하기 위해 클라이언트가 사용하는 개인 키의 비밀에 전적으로 의존한다는 것을 의미합니다.

## 4.2. 요청의 기밀성

이 프로토콜은 요청의 무결성을 확인하는 메커니즘을 제공하지만 요청 기밀성을 보장하지는 않습니다. 추가 예방 조치를 취하지 않는 한 도청자는 콘텐츠 요청에 대한 전체 액세스 권한을 갖습니다. 서버는 이러한 요청의 일부로 전송 될 수있는 데이터의 종류를 신중하게 고려해야하며 민감한 리소스를 보호하기 위해 전송 계층 보안 메커니즘을 사용해야합니다.

## 4.3. 위조 서버에 의한 스푸핑

이 프로토콜은 서버의 진위 여부를 확인하지 않습니다. 적대적인 당사자는 클라이언트의 요청을 가로 채서 오해의 소지가 있거나 잘못된 응답을 반환함으로써 이를 이용할 수 있습니다. 서비스 공급자는 이 프로토콜을 사용하여 서비스를 개발할 때 이러한 공격을 고려해야하며 서버 또는 요청 응답의 신뢰성이 문제가되는 모든 요청에 ​​대해 전송 계층 보안을 요구해야합니다.

## 4.4. 인증된 콘텐츠의 프록시 및 캐싱

HTTP 인증 체계 ([Section 3.5.1](#351-authorization-header))는 선택 사항입니다. 그러나 [RFC2616]은 "Authorization"및 "WWW-Authenticate"헤더 필드에 의존하여 보호 할 수 있도록 인증된 콘텐츠를 구분합니다. 특히 프록시와 캐시는 이러한 헤더 필드를 사용하지 않는 요청을 적절하게 보호하지 못할 수 있습니다.

예를 들어, 개인 인증 콘텐츠는 공개적으로 액세스 할 수 있는 캐시에 저장 (따라서 검색 가능) 할 수 있습니다. HTTP "Authorization"헤더 필드를 사용하지 않는 서버는 "Cache-Control"헤더 필드와 같은 다른 메커니즘을 사용하여 인증 된 콘텐츠를 보호해야합니다.

## 4.5. 자격 증명의 일반 텍스트 저장소

클라이언트 공유 비밀 및 토큰 공유 비밀은 기존 인증 시스템에서 암호와 동일한 방식으로 작동합니다. "RSA-SHA1"이 외의 방법에서 사용되는 서명을 계산하려면 서버가 일반 텍스트 형식으로 이러한 비밀에 액세스 할 수 있어야합니다. 예를 들어 사용자 자격 증명의 단방향 해시만 저장하는 최신 운영 체제와는 대조적입니다.

공격자가 이러한 비밀에 액세스하거나 더 나쁜 경우에는 이러한 모든 비밀의 서버 데이터베이스에 액세스 할 수 있다면 모든 리소스 소유자를 대신하여 모든 작업을 수행 할 수 있습니다. 따라서 서버는 무단 액세스로부터 이러한 비밀을 보호하는 것이 중요합니다.

## 4.6. 클라이언트 자격 증명의 비밀

대부분의 경우 클라이언트 응용 프로그램은 잠재적으로 신뢰할 수 없는 당사자의 제어를 받습니다. 예를 들어 클라이언트가 무료로 사용할 수 있는 소스 코드 또는 실행 가능한 바이너리가 있는 데스크톱 애플리케이션인 경우 공격자는 분석을 위해 복사본을 다운로드 할 수 있습니다. 이러한 경우 공격자는 클라이언트 자격 증명을 복구 할 수 있습니다.

따라서 서버는 클라이언트의 신원을 확인하기 위해 클라이언트 자격 증명만 사용해서는 안됩니다. 가능한 경우 IP 주소와 같은 다른 요소도 사용해야합니다.

## 4.7. 피싱 공격

이 프로토콜과 유사한 프로토콜을 광범위하게 배포하면 리소스 소유자가 암호를 입력하라는 웹 사이트로 리디렉션되는 관행에 빠져들게 될 수 있습니다. 리소스 소유자가 자격 증명을 입력하기 전에 이러한 웹 사이트의 진위를 확인하지 않으면 공격자가 이 방법을 악용하여 리소스 소유자의 암호를 훔칠 수 있습니다.

서버는 피싱 공격이 제기하는 위험에 대해 리소스 소유자를 교육해야하며 리소스 소유자가 사이트의 신뢰성을 쉽게 확인할 수있는 메커니즘을 제공해야합니다. 클라이언트 개발자는 사용자 에이전트와 상호 작용하는 방식 (예: 별도의 창, 임베디드)의 보안 영향과 최종 사용자가 서버 웹 사이트의 진위를 확인하는 능력을 고려해야합니다.

## 4.8. 액세스 요청 범위

자체적으로 이 프로토콜은 클라이언트에게 부여된 액세스 권한의 범위를 지정하는 방법을 제공하지 않습니다. 그러나 대부분의 응용 프로그램에는 보다 세분화된 액세스 권한이 필요합니다. 예를 들어, 서버는 일부 보호된 리소스에 대한 액세스 권한을 부여하고 다른 리소스에는 액세스하지 못하도록하거나 이러한 보호된 리소스에 제한된 액세스 권한 (예: 읽기 전용 액세스) 만 부여 할 수 있습니다.

이 프로토콜을 구현할 때 서버는 액세스 리소스 소유자가 클라이언트에 부여 할 수 있는 액세스 유형을 고려해야하며 이를 위한 메커니즘을 제공해야합니다. 서버는 또한 리소스 소유자가 자신이 부여하는 액세스와 관련 될 수 있는 모든 위험을 이해하도록 주의해야합니다.

## 4.9. 비밀의 엔트로피

전송 계층 보안 프로토콜을 사용하지 않는 한 도청자는 인증된 요청 및 서명에 대한 전체 액세스 권한을 갖게되므로 사용된 자격 증명을 복구하기 위해 오프라인 무차별 대입 공격을 수행 할 수 있습니다. 서버는 공유 비밀이 유효한 기간 동안 이러한 공격에 저항 할 수 있을만큼 충분히 길고 무작위로 공유 비밀을 할당하는 것에 주의해야합니다.

예를 들어 공유 비밀이 2 주 동안 유효한 경우 서버는 공유 비밀을 복구하는 무차별 대입 공격을 2 주 이내에 수행 할 수 없도록 해야합니다. 물론 서버는 주의를 기울이고 합리적으로 가장 긴 비밀을 사용하도록 촉구됩니다.

이러한 비밀을 생성하는 데 사용되는 PRNG (pseudo-random number generator)의 품질이 충분히 높은 것도 마찬가지로 중요합니다. 많은 PRNG 구현은 무작위로 보일 수 있는 숫자 시퀀스를 생성하지만 그럼에도 불구하고 암호화 분석 또는 무차별 대입 공격을 쉽게 만드는 패턴이나 기타 약점을 나타냅니다. 구현자는 이러한 문제를 방지하기 위해 암호화 보안 PRNG를 사용는 것에 주의해야합니다.

## 4.10. 서비스 거부 / 자원 고갈 공격

이 사양에는 서버에 대한 리소스 고갈 공격을 가능하게하는 여러 기능이 포함되어 있습니다. 예를 들어, 이 프로토콜은 사용된 nonce를 추적하는 서버가 필요합니다. 공격자가 많은 nonce를 빠르게 사용할 수 있는 경우 이를 추적하는 데 필요한 리소스가 사용 가능한 용량을 고갈시킬 수 있습니다. 또한 이 프로토콜은 들어오는 요청의 서명을 확인하기 위해 서버가 잠재적으로 비용이 많이 드는 계산을 수행하도록 요구할 수 있습니다. 공격자는 이를 악용하여 많은 수의 유효하지 않은 요청을 서버에 전송하여 서비스 거부 공격을 수행 할 수 있습니다.

리소스 고갈 공격은 이 사양에만 국한되지 않습니다. 그러나 구현자는 이 프로토콜이 노출하는 추가 공격 경로를 신중하게 고려하고 그에 따라 구현을 설계해야합니다. 예를 들어, 엔트로피 고갈은 일반적으로 시스템이 새로운 엔트로피를 기다리는 동안 완전한 서비스 거부 또는 약한 (추측하기 쉬운) 비밀을 초래합니다. 이 프로토콜을 구현할 때 서버는 이들 중 어떤 것이 애플리케이션 및 설계에 더 심각한 위험을 나타내는 지 고려해야합니다.

## 4.11. SHA-1 암호화 공격

"HMAC-SHA1"및 "RSA-SHA1"서명 방법에 사용되는 해시 알고리즘 인 SHA-1은 충돌 공격에 대한 저항력을 크게 감소시키는 여러 가지 암호화 취약점을 가지고있는 것으로 나타났습니다. 이러한 취약점은 HMAC (Hash-based Message Authentication Code)와 함께 SHA-1을 사용하는 데 영향을 미치지 않는 것으로 보이며 "HMAC-SHA1"서명 방법과 "RSA-SHA1"사용에 영향을 미칠 수 있습니다. NIST [NIST_SHA-1Comments]는 2010 년까지 디지털 서명에서 SHA-1 사용을 단계적으로 중단 할 것이라고 발표했습니다.

실제로 이러한 약점은 악용하기 어려우며 그 자체로는 이 프로토콜 사용자에게 심각한 위험을 초래하지 않습니다. 그러나 이들은보다 효율적인 공격을 가능하게 할 수 있으며 서버는 SHA-1이 애플리케이션에 적절한 수준의 보안을 제공하는지 여부를 고려할 때 이를 고려해야합니다.

## 4.12. 서명 기본 문자열 제한

서명 기본 문자열은 이 사양에 정의 된 서명 방법을 지원하도록 설계되었습니다. 추가 서명 방법을 설계하는 사람들은 서명 기본 문자열과 보안 요구 사항의 호환성을 평가해야합니다.

서명 기본 문자열은 대부분의 요청 엔티티 본문, 대부분의 엔티티 헤더 및 매개 변수가 전송되는 순서와 같은 전체 HTTP 요청을 포함하지 않으므로 서버는 이러한 요소를 보호하기 위해 추가 메커니즘을 사용해야합니다.

## 4.13. CSRF (Cross-Site Request Forgery)

CSRF (Cross-Site Request Forgery)는 웹 기반 공격으로 웹 사이트가 신뢰하거나 인증 한 사용자로부터 HTTP 요청이 전송됩니다. 권한 부여 승인에 대한 CSRF 공격을 통해 공격자는 사용자의 동의없이 보호된 리소스에 대한 권한을 얻을 수 있습니다. 서버는 모든 프로토콜 인증 끝점에서 CSRF 방지의 모범 사례를 강력하게 고려해야합니다.

클라이언트가 호스팅하는 OAuth 콜백 URI에 대한 CSRF 공격도 가능합니다. 클라이언트는 클라이언트 사이트의 리소스 소유자가 서버와의 OAuth 협상을 완료하려고 했는지 확인하여 OAuth 콜백 URI에 대한 CSRF 공격을 방지해야합니다. 이러한 CSRF 공격을 방지하는 방법은 이 사양의 범위를 벗어납니다.

## 4.14. 사용자 인터페이스 교정

서버는 사용자 인터페이스 (UI) 교정 공격 ( "clickjacking"이라고도 함)으로부터 인증 프로세스를 보호해야합니다. 이 글을 쓰는 시점에서 UI 교정에 대한 완전한 방어는 불가능합니다. 서버는 다음 기술을 사용하여 UI 교정 공격의 위험을 완화 할 수 있습니다.

o JavaScript frame busting.

o JavaScript frame busting과 브라우저가 인증 페이지에서 JavaScript를 활성화해야합니다.

o 브라우저 별 frame 방지 기술.

o OAuth 토큰을 발급하기 전에 비밀번호 재입력을 요구합니다.

## 4.15. 반복 권한 부여의 자동 처리

서버는 리소스 소유자가 이전에 권한을 부여한 클라이언트의 권한 요청 ([Section 2.2](#22-리소스-소유자-권한-부여))을 ​​자동으로 처리 할 수 ​​있습니다. 리소스 소유자가 액세스 권한을 부여하기 위해 서버로 리디렉션되면 서버는 리소스 소유자가 해당 특정 클라이언트에 대한 액세스 권한을 이미 부여했음을 감지합니다. 리소스 소유자에게 승인을 요청하는 대신 서버는 자동으로 리소스 소유자를 클라이언트로 다시 리디렉션합니다.

클라이언트 자격 증명이 손상되면 자동 처리로 인해 추가 보안 위험이 발생합니다. 공격자는 훔친 클라이언트 자격 증명을 사용하여 권한 부여 요청을 통해 리소스 소유자를 서버로 리디렉션 할 수 있습니다. 그런 다음 서버는 리소스 소유자의 명시적인 승인 또는 공격에 대한 인식없이 리소스 소유자의 데이터에 대한 액세스 권한을 부여합니다. 자동 승인이 구현되지 않은 경우 공격자는 소셜 엔지니어링을 사용하여 리소스 소유자가 액세스를 승인하도록 설득해야합니다.

서버는 자동 승인을 통해 획득한 토큰 자격 증명의 범위를 제한함으로써 자동 처리와 관련된 위험을 완화 할 수 있습니다. 명시적인 리소스 소유자 동의를 통해 얻은 토큰 자격 증명은 영향을 받지 않을 수 있습니다. 클라이언트는 클라이언트 자격 증명을 보호하여 자동 처리와 관련된 위험을 완화 할 수 있습니다.

# 5. Acknowledgments

This specification is directly based on the OAuth Core 1.0 Revision A community specification, which in turn was modeled after existing proprietary protocols and best practices that have been independently implemented by various companies.

The community specification was edited by Eran Hammer-Lahav and authored by: Mark Atwood, Dirk Balfanz, Darren Bounds, Richard M. Conlan, Blaine Cook, Leah Culver, Breno de Medeiros, Brian Eaton, Kellan Elliott-McCrea, Larry Halff, Eran Hammer-Lahav, Ben Laurie, Chris Messina, John Panzer, Sam Quigley, David Recordon, Eran Sandler, Jonathan Sergent, Todd Sieling, Brian Slesinsky, and Andy Smith.

The editor would like to thank the following individuals for their invaluable contribution to the publication of this edition of the protocol: Lisa Dusseault, Justin Hart, Avshalom Houri, Chris Messina, Mark Nottingham, Tim Polk, Peter Saint-Andre, Joseph Smarr, and Paul Walker.

Appendix A. Differences from the Community Edition

This specification includes the following changes made to the original community document [OAuthCore1.0_RevisionA] in order to correct mistakes and omissions identified since the document was originally published at <http://oauth.net>.

o Changed using TLS/SSL when sending or requesting plain text credentials from SHOULD to MUST. This change affects any use of the "PLAINTEXT" signature method, as well as requesting temporary credentials ([Section 2.1](#21-임시-자격-증명)) and obtaining token credentials ([Section 2.3](#23-토큰-자격-증명)).

o Adjusted nonce language to indicate it is unique per token/ timestamp/client combination.

o Removed the requirement for timestamps to be equal to or greater than the timestamp used in the previous request.

o Changed the nonce and timestamp parameters to OPTIONAL when using the "PLAINTEXT" signature method.

o Extended signature base string coverage that includes "application/x-www-form-urlencoded" entity-body parameters when the HTTP method used is other than "POST" and URI query parameters when the HTTP method used is other than "GET".

o Incorporated corrections to the instructions in each signature method to encode the signature value before inserting it into the "oauth_signature" parameter, removing errors that would have caused double-encoded values.

o Allowed omitting the "oauth_token" parameter when empty.

o Permitted sending requests for temporary credentials with an empty "oauth_token" parameter.

o Removed the restrictions from defining additional "oauth\_" parameters.

# 6. References

## 6.1. Normative References

[RFC2045] Freed, N. and N. Borenstein, "Multipurpose Internet Mail Extensions (MIME) Part One: Format of Internet Message Bodies", RFC 2045, November 1996.

[RFC2104] Krawczyk, H., Bellare, M., and R. Canetti, "HMAC: Keyed- Hashing for Message Authentication", RFC 2104, February 1997.

[RFC2119] Bradner, S., "Key words for use in RFCs to Indicate Requirement Levels", BCP 14, RFC 2119, March 1997.

[RFC2616] Fielding, R., Gettys, J., Mogul, J., Frystyk, H., Masinter, L., Leach, P., and T. Berners-Lee, "Hypertext Transfer Protocol -- HTTP/1.1", RFC 2616, June 1999.

[RFC2617] Franks, J., Hallam-Baker, P., Hostetler, J., Lawrence, S., Leach, P., Luotonen, A., and L. Stewart, "HTTP Authentication: Basic and Digest Access Authentication", RFC 2617, June 1999.

[RFC2818] Rescorla, E., "HTTP Over TLS", RFC 2818, May 2000.

[RFC3447] Jonsson, J. and B. Kaliski, "Public-Key Cryptography Standards (PKCS) #1: RSA Cryptography Specifications Version 2.1", RFC 3447, February 2003.

[RFC3629] Yergeau, F., "UTF-8, a transformation format of ISO 10646", STD 63, RFC 3629, November 2003.

[RFC3986] Berners-Lee, T., Fielding, R., and L. Masinter, "Uniform Resource Identifier (URI): Generic Syntax", STD 66, RFC 3986, January 2005.

[W3C.REC-html40-19980424] Hors, A., Raggett, D., and I. Jacobs, "HTML 4.0 Specification", World Wide Web Consortium Recommendation REC-html40-19980424, April 1998, <http://www.w3.org/TR/1998/REC-html40-19980424>.

## 6.2. Informative References

[NIST_SHA-1Comments] Burr, W., "NIST Comments on Cryptanalytic Attacks on SHA-1", <http://csrc.nist.gov/groups/ST/hash/statement.html>.

[OAuthCore1.0_RevisionA] OAuth Community, "OAuth Core 1.0 Revision A", <http://oauth.net/core/1.0a>.

Author's Address

Eran Hammer-Lahav (editor)

EMail: eran@hueniverse.com  
URI: http://hueniverse.com
