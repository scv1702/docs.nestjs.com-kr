# First steps

본 문서에서는, Nest의 **근본적인 사항**들에 대해 배울 것이다. Nest 어플리케이션의 필수적인 구성 요소들에 대해 친숙해지기 위해, 대다수의 산업에서 사용될 수 있는 기본적인 CRUD 어플리케이션을 만들어볼 것이다.

### 언어

우리는 **TypeScript**와 사랑에 빠졌다! 하지만 무엇보다도, **Node.js**와도 사랑에 빠졌다. 이는 Nest가 TypeScript와 순수 JavaScript 둘 다를 지원하는 이유다. Nest는 최신 언어 특징들의 장점들을 가져왔기 때문에, 바닐라 JavaScript를 쓰고 싶다면 **Babel** 컴파일러가 필요하다.

우리는 본 예제의 대부분에서 TypeScript를 사용할 것이지만, 원한다면 바닐라 JavaScript로 코드 스니펫을 바꿀 수 있다. (본 프로젝트는 번역 프로젝트로, 이를 지원하지 않는다.)

### 우선사항

v13을 제외한 10.13.0 이상의 **Node.js**가 반드시 필요하다.

### 설정

**Nest CLI**를 통해 새로운 프로젝트를 쉽게 설정할 수 있다. **npm**이 설치되어 있다면, OS 터미널에서 다음과 같은 명령어로 새로운 Nest 프로젝트를 만들 수 있다.

```bash
$ npm i -g @nest/cli
$ nest new project-name
```

`project-name` 디렉토리에 node 모듈들과 필요한 여러가지 파일들이 생성된다. 그리고 `src/` 디렉토리에는 여러가지 중요한 파일들이 생성된다.

```bash
src
|-app.controller.spec.ts
|-app.controller.ts
|-app.module.ts
|-app.service.ts
|-main.ts
```

다음은 중요 파일들에 대한 짧은 요약이다.

- `app.controller.ts` : 하나의 라우트가 있는 기본 컨트롤러
- `app.controller.spec.ts` : 컨트롤러의 유닛 테스트를 위한 파일
- `app.module.ts` : 어플리케이션의 루트 모듈
- `app.service.ts` : 하나의 메소드가 있는 기본 서비스
- `main.ts` : Nest 어플리케이션 인스턴스를 생성하는 `NestFactory` 의 주요 함수를 사용하는 어플리케이션의 엔트리 파일

`main.ts` 는 어플리케이션을 시작시키는 비동기 함수를 포함한다:

```tsx
import { NestFactory } from '@nestjs/core';
import { AppModule } from './app.module';

async function bootstrap() {
  const app = await NestFactory.create(AppModule);
  await app.listen(3000);
}
bootstrap();
```

Nest 어플리케이션 인스턴스를 만들기 위해, `NestFactory` 클래스를 사용한다. `NestFactory` 에는 어플리케이션 인스턴스 생성을 돕는 여러가지 정적 메소드들이 있다. `create()` 메소드는 `INestApplication` 인터페이스를 충족하는 어플리케이션 객체를 반환한다. 이 객체는 다음 챕터들에서 기술할 몇가지 메소드들을 가지고 있다. 위의 `main.ts` 예제는, 단순히 HTTP 리스너를 시작시켰는데 이는 어플리케이션으로 오는 HTTP 요청들을 기다리도록 한다.

Nest CLI로 생성한 프로젝트는 기초적인 프로젝트 구조를 생성하는데, 이는 개발자들이 각 모듈들이 각자 지정된 디렉토리에 저장 되도록 하는 컨벤션을 따르도록 한다.

> **힌트**
기본적으로, 만약 어플리케이션을 생성하는 동안 에러가 발생하면 앱은 코드 `1` 과 함께 종료된다. 대신에 만약 앱이 에러를 던지도록 하고 싶다면 `NestFactory.create(AppModule, { abortOnError: false })` 로 `abortOnError` 옵션을 꺼야 한다.
> 

### 플랫폼

Nest는 플랫폼-불가지론적(platform-agnostic)인 프레임워크를 지향한다. 플랫폼 독립성은 개발자들이 여러 종류의 어플리케이션에서 재사용할 수 있는 로직들을 만들수 있도록 한다. 기술적으로, Nest는 어댑터만 있다면 어떠한 종류의 Node HTTP 프레임워크라도 사용할 수 있다. 기본으로 제공하는 두 가지의 HTTP 플랫폼에는  **express**와 **fastify**가 있다. 필요와 목적에 따라 둘 중 하나를 선택할 수 있다.

- `platform-express` : **Express**는 잘 알려진 Node의 웹 프레임워크로, 프로덕션 환경에서 바로 사용할 수 있는 라이브러리다. 커뮤니티에서 구현한 리소스들도 매우 다양하다. `@nestjs/platform-express` 패키지가 기본으로 사용된다.
- `platform-fastify` : **Fastify**는 높은 성능과 낮은 부하를 자랑하는 프레임워크로 효율성과 속도를 중시한다.

어떤 플랫폼을 사용하던 간에, 각 플랫폼은 고유의 어플리케이션 인터페이스를 가진다. 이는 각각 `NestExpressApplication`과 `NestFastifyApplication` 이다.

다음과 같이 `NestFactory.create()` 에 타입을 전달한다면, `app` 객체는 해당 플랫폼에서만 사용할 수 있는 메소드를 가진다. 하지만, 해당 플랫폼의 API에 진짜 접근하고 싶은 것이 아니면 타입을 명시할 필요가 없다.

```tsx
const app = await NestFactory.create<NestExpressApplication>(AppModdule);
```

### 어플리케이션 실행

설치 과정이 끝났다면, 다음 명령어를 통해 어플리케이션이 HTTP 요청을 기다리도록 시작할 수 있다.

```bash
$ npm run start
```

해당 명령어는 파일을 감시하면서 자동으로 다시 컴파일해 서버를 리로딩 한다.