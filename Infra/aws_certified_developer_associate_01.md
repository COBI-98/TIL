# AWS Certified Developer - Associate

# IAM and AWS CLI
`Root 사용자`는 AWS 계정을 생성할 때 자동으로 생성되는 사용자로, AWS 계정에 대한 완전한 관리 권한을 가지지만 <br> 
`IAM 사용자`는 WS 계정 소유자가 생성한 개별 사용자 계정으로, 특정 권한과 정책을 통해 AWS 리소스에 접근할 수 있다.

<br>

## IAM (Identity and Access Management)

### 1) IAM 사용자(User)
IAM 사용자는 AWS 리소스에 액세스할 수 있는 개별 엔터티
각 사용자에게 고유한 자격 증명(액세스 키 또는 비밀번호)이 할당할 수 있다.

<br>

### 2) IAM 그룹(Group)
IAM 그룹은 여러 사용자를 하나로 묶어 동일한 권한을 부여할 수 있는 방식으로, 동일한 정책을 여러 사용자에게 적용할 수 있으며 사용자가 다인 그룹을 가질 수 있다.

<br>

### 3) IAM 역할(Role)
역할은 특정 사용자나 서비스가 특정한 권한을 일시적으로 사용할 수 있도록 허용한다. <br> 
주로 AWS 리소스 간에 권한을 위임할 때 사용된다.

### 4) IAM 정책(Policy)
정책은 AWS 리소스에 대한 액세스 권한을 정의하는 JSON 형식의 문서이며, 허용할 작업, 사용할 리소스, 조건 등을 정의할 수 있다.

정책 예시 (JSON)

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "s3:PutObject",
            "Resource": "arn:aws:s3:::example-bucket/*"
        }
    ]
}
```
위 정책은 특정 S3 버킷에 객체를 업로드할 수 있는 권한을 허용 정책이다.

### 5) 정책 종류
관리형 정책: AWS에서 제공하는 일반적인 정책.
사용자 정의 정책: 사용자가 직접 정의하는 정책.

<br>

## IAM MFA 
### Virtual MFA Device 
Twilio Authy 방식으로 다단계 인증을 사용할수있으며 다음과 같은 장치를 사용할 수도있다.
 
Universal 2nd Factor (U2F), Yubikey, <br>
Hardware Key Fod MFA Device, <br>
Hardware Key Fod MFA Device for AWS GovCloud

<br>

<br>

# AWS Accesskey CLI and SDK

## AWS CLI
AWS CLI는 명령어 기반의 AWS 리소스 관리 도구이며 google에 aws cli install Windows version 2 (window)를 설치할 수 있으며, 설치하면 다음과같은 명령어로 다양한 서비스와 스크립트를 확인할 수 있다.

```bash
aws --version //version

aws iam list-users

aws <서비스 이름> <명령어> [옵션]
```

## AWS SDK
AWS 서비스를 사용하는 애플리케이션을 더 쉽게 개발할 수 있도록 지원하는 라이브러리이며 다음과 같은 장점이 있다.

- 다양한 프로그래밍 언어 지원: AWS SDK는 Java, Python (boto3), JavaScript, .NET, PHP, Go 등 여러 언어를 지원기능

- 간편한 통합: AWS SDK는 AWS의 모든 주요 서비스와 쉽게 통합할 수 있다. 예를 들어, S3에 파일을 업로드하거나, EC2 인스턴스를 관리하거나, DynamoDB에 데이터를 저장하는 기능 등

- 기능성: AWS SDK는 오류 처리, 인증, 보안과 같은 여러 기능을 내장하여 복잡한 작업도 쉽게 구현가능하다.

## AWS CloudShell
브라우저 기반의 CLI 환경으로, 사용자가 별도의 설정 없이 AWS 리소스를 관리할 수 있는 터미널을 제공하는데 비전 별로 사용가능한 지역이 나뉘어져있다.

<br>

