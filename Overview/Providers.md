# Providers

프로바이더(Providers)는 Nest의 근본적인 개념이다. 대부분의 기본 Nest 클래스들은 대부분 프로바이더로 여겨진다 - 서비스(Services), 레포지토리(Repositories), 팩토리(Factories), 헬퍼(Helpers) 등. 프로바이더의 주요 아이디어는 이것이 의존성(Dependency)으로써 **주입**될 수 있다는 것이다.

![Providers_1](images/Providers_1.png)

이전의 챕터에서, 우리는 간단한 `CatsController`를 만들었다. 컨트롤러는 HTTP 요청들을 다루고 더 복잡한 작업들은 **프로바이더**에게 위임한다. 프로바이더는 모듈에서 `providers`라고 선언된 평범한 JavaScript 클래스이다.

> **힌트**
> Nest는 더 객체-지향적인 방식으로 의존성들을 디자인하고 조직하는 것을 가능하게 해주기 때문에, 우리는 SOLID 원리를 강력하게 따를 것을 권고한다.

### 서비스(Services)
간단한 `CatsService`를 만들어보자. 이 서비스는 데이터를 저장하고 꺼내는 역할을 하며, `CatsController`가 사용하도록 설계된다. 그러므로 이는 프로바이더로 정의되기 좋은 후보다.

```tsx
import { Injectable } from '@nestjs/common';
import { Cat } from './interfaces/cat.interface';

@Injectable()
export class CatsService {
  private readonly cats: Cat[] = [];

  create(cat: Cat) {
    this.cats.push(cat);
  }

  findAll(): Cat[] {
    return this.cats;
  }
}
```

> **힌트**
> CLI를 이용해 서비스를 생성하고 싶으면, `$ nest g service cats` 라고 실행하면 된다.

우리의 `CatsService`는 하나의 프로퍼티와 두 메소드를 가지는 기본 클래스다. 새로운 점은 이것이 `@Injectable` 데코레이터를 사용한다는 것이다. `@Injectable` 데코레이터는 `CatsService`가 Nest IoC 컨테이너에 의해 관리될 수 있다는 클래스 임을 선언하는 메타데이터를 붙인다. 그건 그렇고, 이 예제는 또한 다음과 같이 생긴 `Cat` 인터페이스를 사용한다. 

```tsx
export interface Cat {
  name: string;
  age: number;
  breed: string;
}
```

이제 우리는 고양이들(Cats)을 검색하는 서비스 클래스를 가진다, 이를 `CatsController` 안에서 사용해보자.

```tsx
import { Controller, Get, Post, Body } from '@nestjs/common';
import { CreateCatDto } from './dto/create-cat.dto';
import { CatsService } from './cats.service';
import { Cat } from './interfaces/cat.interface';

@Controller('cats')
export class CatsController {
  constructor(private catsService: CatsService) {}

  @Post()
  async create(@Body() createCatDto: CreateCatDto) {
    this.catsService.create(createCatDto);
  }

  @Get()
  async findAll(): Promise<Cat[]> {
    return this.catsService.findAll();
  }
}
```

`CatsService`는 클래스 생성자를 통해 **주입**된다. `private` 문법을 사용한 것을 주목해라. 이 약식은 한 번에 `catsService` 멤버를 선언하고 초기화할 수 있도록 해준다.

### 의존성 주입(Dependency injection)
Nest는 흔히 의존성 주입이라고 알려진 강력한 디자인 패턴을 기반으로 구축되었다. Angular 공식 문서에서 이 개념에 대한 훌륭한 글을 읽어보는 것을 추천한다.

Nest에서는, TypeScript의 능력 덕분에, 의존성들을 관리하는 것이 매우 쉽다. 왜냐하면 이들은 그저 타입으로 해결되기 때문이다. 밑의 예시에서, Nest는 `CatsService`의 인스턴스를 생성하고 반환함으로써 `catsService`를 해결한다 (또는, 싱글톤의 일반적인 경우에서는, 이것이 다른 어디에선가 요청된 적이 있다면 기존의 존재하는 인스턴스를 반환한다.) 이 의존성은 해결되고 컨트롤러의 생성자로 전달된다 (또는 정해진 프로퍼티에 할당된다). 

```tsx
constructor(private catsService: CatsService) {}
```

### 스코프(Scopes)
프로바이더는 보통 스코프라고 불리는, 어플리케이션 라이프사이클과 동기화된 수명을 가진다. 어플리케이션이 부팅되면, 모든 의존성은 해결되어야 한다. 그러므로 모든 프로바이더는 초기화 되어져야 한다. 비슷하게, 어플리케이션이 종료되면, 각 프로바이더는 파괴될 것이다. 하지만, 프로바이더의 수명을 **request-scoped**로 만들 수 있는 방법들이 있다. 이러한 기술들에 대해선 여기를 참고해라.

### 커스텀 프로바이더(Custom proviers)
Nest는 프로바이더들 간의 관계를 해결하는 내장 역제어(IoC) 컨테이너를 가지고 있다. 이 특징은 위에서 기술한 의존성 주입 기능의 기초가 되지만, 지금까지 기술한 것보다 훨씬 더 강력하다. 평범한 값, 클래스, 심지어 비동기 또는 동기 팩토리를 프로바이더로 정의할 수 있는 여러가지 방법들이 있다. 더 자세한 예시는 여기를 참고해라.

### 옵셔널 프로바이더(Optional providers)
경우에 따라, 반드시 해결되지 않아도 되는 의존성들을 가질 수도 있다. 예를 들어, 클래스가 **Configuration 객체**에 의존하는데 아무것도 전달되지 않는다면, 아마 기본 값이 사용될 것이다. 이러한 경우, 의존성은 선택 사항이 되는데 Configuration 프로바이더가 없어도 에러가 나지 않기 때문이다. 

프로바이더가 선택 사항임을 알려주려면, 생성자의 시그니쳐에서 `@Optional` 데코레이터를 사용하면 된다.

```tsx
import { Injectable, Optional, Inject } from '@nestjs/common';

@Injectable()
export class HttpService<T> {
  constructor(@Optional() @Inject('HTTP_OPTIONS') private httpClient: T) {}
}
```

위 예시는 `HTTP_OPTIONS` 커스텀 **토큰**을 사용했기 때문에 커스텀 프로바이더를 사용있다. 이전 예제는 클래스의 생성자를 통해 의존성을 나타내는 생성자-기반 주입에 대한 것이었다. 커스텀 프로바이더와 관련된 토큰들에 관해서는 여기를 참고해라.

### 프로퍼티-기반 주입(Property-based injection)
우리가 여태까지 사용했던 기술은 생성자-기반 주입으로, 프로바이더가 생성자 메소드를 통해 주입되는 것이다. 매우 특정한 상황에서는, **프로퍼티-기반 주입**이 유용할 수 있다. 예를 들어, 만약 최상위 클래스가 하나 또는 여러개의 프로바이더에 의존하는 경우, 하위 클래스들의 생성자에서 `super()`를 호출함으로써 위로 계속 전달하는 것은 매우 지루할 수 있다. 이를 방지하고 싶으면, 프로퍼티 레벨에서 `@Inject` 데코레이터를 사용하면 된다.

```tsx
import { Injectable, Inject } from '@nestjs/common';

@Injectable()
export class HttpService<T> {
  @Inject('HTTP_OPTIONS')
  private readonly httpClient: T;
}
```

> **주의**
> 만약 클래스가 다른 프로바이더를 `extends` 하지 않는다면, 항상 생성자-기반 주입을 사용할 것을 권장한다.

### 프로바이더 등록
이제 우리는 프로바이더(`CatsService`)를 정의했고, 서비스의 소비자(`CatsController`)를 가지고 있다. 서비스가 주입을 수행할 수 있도록 하려면 서비스를 등록해야 한다. 이는 모듈 파일(`app.module.ts`)에서 `@Module` 데코레이터의 `providers` 배열에 서비스를 추가함으로써 가능하다.

```tsx
import { Module } from '@nestjs/common';
import { CatsController } from './cats/cats.controller';
import { CatsService } from './cats/cats.service';

@Module({
  controllers: [CatsController],
  providers: [CatsService],
})
export class AppModule {}
```

Nest는 이제 `CatsController` 클래스의 의존성들을 해결할 수 있게 됐다.

이제 디렉토리 구조는 다음과 같이 되어야 한다.
```
src
|-cats
| |-dto
| | |-create-cat.dto.ts
| |-interfaces
| | |-cat.interface.ts
| |-cats.controller.ts
| |-cats.service.ts
|-app.module.ts
|-main.ts
```

### 수동 인스턴스화(Manual instantiation)
우리는 어떻게 Nest가 자동으로 의존성 해결을 다루는지에 대해 알아보았다. 특정 상황에서는, 의존성 주입 시스템의 외부에서 수동으로 프로바이더를 검색하거나 초기화할 필요도 있을 것이다. 이에 대한 두 가지 주제에 대해 짧게 다뤄보자.

기존에 존재하는 인스턴스를 얻기 위해선, 또는 프로바이더를 동적으로 초기화하기 위해선, 모듈 레퍼런스를 읽어보면 된다.

`bootstrap()` 함수 안에서 프로바이더를 얻기 위해선 (예를 들어 컨트롤러가 없는 Standalone 어플리케이션에서, 또는 부트스트랩핑 중 Configuration 서비스를 활용하는 경우), Standalone 어플리케이션을 참고해라.