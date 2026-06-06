# 스프링 부트 - 핵심 원리와 활용

인프런 김영한 강의 **[스프링 부트 - 핵심 원리와 활용](https://www.inflearn.com/course/스프링부트-핵심원리-활용)** 을 따라하며 학습한 코드입니다(Kotlin).
스프링 부트가 등장하기까지의 과정(내장 톰캣, 서블릿 컨테이너 초기화)부터 자동 구성(Auto Configuration)과 직접 만든 스타터 라이브러리를 사용하는 과정을 단계별 모듈로 다룹니다.

## 사용 기술

- Kotlin 1.9.23 (JVM target 17)
- Spring Boot 3.2.5 / Spring Web MVC 6.1.6
- 내장 톰캣 (`tomcat-embed-core` 11.0.0-M20), Jakarta Servlet API 6.1.0-M2
- kotlin-logging-jvm
- Gradle (Kotlin DSL, 멀티 모듈)

## 모듈 구성

멀티 모듈로 강의의 단계를 분리하여 구성합니다.

### `spring-boot` 모듈 — 스프링 부트 없이 직접 구현
스프링 부트가 자동으로 해주는 일을 수동으로 구현하며 원리를 학습합니다.
- 내장 톰캣을 코드로 직접 띄우기 (`boot/MySpringApplication`, `boot/MySpringBootApplication`)
- 서블릿 컨테이너 초기화: `ServletContainerInitializer`와 `@HandlesTypes`, 애플리케이션 초기화 인터페이스 (`container/AppInit`, `AppInitV1Servlet`, `AppInitV2Spring`, `AppInitV3SpringMvc`, `MyContainerInitV1`, `MyContainerInitV2`)
- 서블릿/스프링 컨테이너/스프링 MVC 등록 단계별 비교 (`servlet/TestServlet`, `spring/HelloConfig`, `spring/HelloController`)
- 빌드: `war` 플러그인, `resources/META-INF/services/jakarta.servlet.ServletContainerInitializer`

### `memory-v1`, `memory-v2` 모듈 — 외부 라이브러리 / 자동 구성
- `memory-v1`: 일반 모듈 형태의 라이브러리 (`memory/Memory`, `MemoryController`, `MemoryFinder`)
- `memory-v2`: 자동 구성(Auto Configuration) 라이브러리. `@AutoConfiguration` 클래스(`MemoryAutoConfig`)를 `META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports` 에 등록하여 자동 등록되도록 구성
- 두 라이브러리는 jar로 빌드되어 `boot` 모듈에서 사용 (`boot/libs/memory-v1-...jar`, `memory-v2.jar`)

### `boot` 모듈 — 스프링 부트 사용
- 스프링 부트 기반 애플리케이션 (`BootApplication`, `controller/HelloController`)
- `memory-v2.jar`(자동 구성 라이브러리)를 의존성으로 사용 (`config/MemoryConfig`)
- `@Import`와 `ImportSelector`를 통한 동적 빈 등록 학습 (테스트: `selector/HelloImportSelector`, `ImportSelectorTest`)

## 프로젝트 구조

```
spring-boot/                       # 부트 없이 직접 구현 (내장 톰캣, 컨테이너 초기화)
│   └── src/main/kotlin/com/inflearn/springboot/{boot,container,servlet,spring}
memory-v1/                         # 일반 라이브러리 모듈
memory-v2/                         # 자동 구성(Auto Configuration) 라이브러리 모듈
boot/                              # 스프링 부트로 라이브러리 사용
    └── src/main/kotlin/hello/{config,controller}
```
