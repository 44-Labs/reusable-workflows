# Reusable GitHub Actions Workflows

이 레포지토리는 조직 내에서 일관된 CI/CD 및 자동화 프로세스를 구축하고 유지하기 위한 **재사용 가능한 GitHub Actions 워크플로우 템플릿**을 제공합니다. 

## 목적
- **워크플로우 표준화**: 조직 내 모든 프로젝트에서 동일한 CI/CD 프로세스를 사용하여 품질과 일관성을 유지합니다.
- **생산성 향상**: 반복 작업을 자동화하고 개발자들이 비즈니스 로직에 집중할 수 있도록 돕습니다.
- **중복 제거**: 모든 프로젝트에서 공통적으로 사용되는 워크플로우를 템플릿화하여 유지 관리가 쉽도록 합니다.

## 주요 기능
- **CI/CD 자동화**: 코드 빌드, 테스트 및 배포 자동화
- **보안 검사**: 코드 스캐닝 및 종속성 검사
- **PR 관리**: 풀 리퀘스트 분류 및 라벨링 자동화
- **유지보수 편리성**: 재사용 가능한 워크플로우 호출을 통해 유지보수가 용이합니다.

## 사용 방법

### 1. 템플릿 사용하기
1. `.github` 레포지토리의 `workflow-templates` 디렉터리에서 원하는 템플릿을 선택합니다.
2. 프로젝트의 `.github/workflows` 디렉터리에 복사하거나, 직접 호출합니다.

```yaml
name: Reusable Backend CI

on:
  workflow_call:
    secrets:
      token:
        required: true
    inputs:
      java_version:
        required: false
        type: string
        default: '17'
      


jobs:
  run-common-unit-tests:
    name: 'Run Backend CI'
    runs-on: ubuntu-latest
    permissions: write-all

    steps:
      - name: Checkout
        uses: actions/checkout@v3

      - name: Set up JDK 
        uses: actions/setup-java@v3
        with:
          distribution: temurin
          java-version: ${{ inputs.java_version }}

      - name: Clean Task & Run Test
        run: ./gradlew clean test  

      - name: Build All Image
        run: ./gradlew jibDockerBuildAll

      - name: Test Coverage Report 🦞
        if: ${{ github.event_name == 'pull_request' }}
        id: jacoco
        uses: madrapps/jacoco-report@v1.6.1
        with:
          paths: |
            ${{ github.workspace }}/**/build/jacoco/test/jacocoTestReport.xml
          token: ${{ secrets.token }}
          fail-emoji: 😵
          pass-emoji: ✅
          min-coverage-overall: 1
          title: Code Coverage
          update-comment: true
```

### 2. 새 템플릿 추가하기
1. 이 레포지토리에 새로운 워크플로우를 추가하려면 `workflow-templates` 디렉터리에 `.yml` 파일을 생성합니다.
2. 동일한 이름의 `.properties.json` 파일을 생성하여 메타데이터를 추가합니다.

예시:
- `workflow-templates/example-ci.yml`
- `workflow-templates/example-ci.properties.json`

```json
{
    "name": "Reusable Backend CI Workflow",
    "description": "44Labs Organization Backend CI workflow template.",
    "iconName": "octicon workflow",
    "categories": ["Spring Boot", "CI"]
}
```

## 디렉터리 구조
```
.github
└── workflow-templates
    ├── build-and-deploy.yml
    └── build-and-deploy.properties.json
```

## 기여하기
1. 새로운 워크플로우 템플릿을 제안하거나 수정 사항이 있을 경우, 풀 리퀘스트(PR)를 생성해 주세요.
2. 템플릿 수정 시, 기존 프로젝트에 영향을 주지 않도록 하위 호환성을 고려해 주세요.

## 라이선스
이 프로젝트는 [MIT 라이선스](LICENSE)를 따릅니다.

---
**문의:**
- 팀 내 워크플로우 템플릿 관련 문의는 내부 문서를 참고하거나 관리자에게 문의하세요.

