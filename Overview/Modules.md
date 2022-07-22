# Modules

모듈(Module)는 `@Module()` 데코레이터로 주석이 달린 클래스이다. `@Module()` 데코레이터는 Nest가 어플리케이션 구조를 조직하는데 필요한 메타데이터를 제공한다.

![Modules_1](images/Modules_1.png)

각 어플리케이션은 적어도 하나의 모듈, **루트 모듈**을 가진다. 루트 모듈은 Nest가 **어플리케이션 그래프** - Nest가 모듈과 프로바이더 관계들 그리고 의존성들 해결하는데 사용하는 내부 데이터 구조 - 를 만드는 시작점이다. 아주 작은 어플리케이션은 이론적으로 하나의 루트 모듈로도 충분하지만, 보통은 그렇지 않다. 우리는 모듈이 컴포넌트를 조직하는데 **가장** 효율적인 방법임을 강조하고 싶다. 그러므로, 대부분의 어플리케이션에서 최종 아키텍쳐는 각각이 밀접하게 관련된 기능들의 집합을 캡슐화하는 여러개의 모듈들을 사용한다.

`@Module` 데코레이터는 모듈을 설명하는 속성들을 가지는 하나의 객체를 사용한다.

| 데코레이터       |                   |
| ------------- | --------------------------------- |
| `providers`   | Nest 인젝터에 의해 초기화되고 해당 모듈 안에서 공유될 수 있는 프로바이더들      |
| `controllers` | 해당 모듈 안에 정의되어 초기화 되어야 하는 컨트롤러들의 집합                  |
| `imports`     | 해당 모듈에서 필요로 모듈들의 리스트. 이 리스트의 모듈들은 프로바이더들을 노출한다. |
| `exports`     | 해당 모듈이 제공하는 `providers`의 부분 집합이다. 해당 모듈을 가져온 다른 모듈들에서 사용할 수 있도록 한다. 프로바이더 자체 또는 토큰(`provide` 값)만을 사용할 수 있다. |

모듈은 기본적으로 프로바이더를 **캡슐화**한다. 이는 현재 모듈의 일부분이 아니거나 가져온 모듈에서 내보내진 것이 아닌 프로바이더는 현재 모듈에 주입할 수 없다는 것을 의미한다. 그러므로, 모듈에서부터 내보내진 프로바이더들을 모듈의 공개 인터페이스, 또는 API로 고려해야 한다.

### 기능 모듈(Feature modules)

`CatsController`와 `CatsService`는 같은 어플리케이션 도메인에 속해있다. 이들은 밀접하게 관련있기 때문에, 같은 기능 모듈에 넣는 것이 적절하다. 기능 모듈은 단순히 특정 기능과 관련된 코드들을 조직하고, 깔끔하게 구분 짓는다. 이는 특히 어플리케이션이나 팀의 크기가 커질때 SOLID 원리에 따라 복잡도를 관리하고 개발할 수 있도록 도와준다.

이를 입증하기 위해, 우리는 `CatsModule`를 만들었다.

```tsx
import { Module } from '@nestjs/common';
import { CatsController } from './cats.controller';
import { CatsService } from './cats.service';

@Module({
  controllers: [CatsController],
  providers: [CatsService],
})
export class CatsModule {}
```

> **힌트**
> CLI를 이용해 서비스를 생성하고 싶으면, `$ nest g module cats` 라고 실행하면 된다.

위에서, `cats.module.ts` 파일에서 `CatsModule`을 정의하고 이 모듈과 관련된 모든 것들을 `cats` 디렉토리 안으로 옮겼다. 마지막으로 해야 하는 것은 이 모듈을 루트 모듈(`app.module.ts` 파일에 정의된 `AppModule`)에 가져온 것이다.

```tsx
import { Module } from '@nestjs/common';
import { CatsModule } from './cats/cats.module';

@Module({
  imports: [CatsModule],
})
export class AppModule {}
```

현재 디렉토리 구조는 다음과 같다.

```
src
|-cats
| |-dto
| | |-create-cat.dto.ts
| |-interfaces
| | |-cat.interface.ts
| |-cats.controller.ts
| |-cats.module.ts
| |-cats.service.ts
|-app.module.ts
|-main.ts
```

### 공유 모듈(Shared modules)

Nest에서, 모듈들은 기본적으로 **싱글톤**이므로, 여러 모듈 간 쉽게 프로바이더의 동일한 인스턴스를 공유할 수 있다.

![Modules_2](images/Modules_2.png)

모든 모듈은 자동으로 **공유 모듈**이다. 한 번 생성되고 나면 이는 다른 모듈에서 재사용될 수 있다. 여러 모듈 간 `CatsService`의 인스턴스를 공유하고 싶다고 상상해보자. 그렇게 하기 위해선, 먼저 아래와 같이 모듈의 `exports` 배열에 `CatsService` 프로바이더를 추가함으로써 이를 **내보내야** 한다.

```tsx
import { Module } from '@nestjs/common';
import { CatsController } from './cats.controller';
import { CatsService } from './cats.service';

@Module({
  controllers: [CatsController],
  providers: [CatsService],
  exports: [CatsService]
})
export class CatsModule {}
```

이제 `CatsModule`를 가져온 다른 모듈에서 `CatsService`에 접근할 수 있게 됐으며 또한 이들은 모두 같은 인스턴스를 공유한다.

### 모듈 다시 내보내기(Module re-exporting)

위에서 보았듯이, 모듈은 내부 프로바이더를 내보낼 수 있다. 또한 이들이 가져온 모듈들을 다시 내보낼 수 있다. 아래 예시와 같이, `CommonModule`은 `CoreModule`로 가져온 동시에 내보내진다. 이는, `CoreModule`를 가져온 다른 모듈에서 `CommonModule`를 사용할 수 있게 한다.

```tsx
@Module({
  imports: [CommonModule],
  exports: [CommonModule],
})
export class CoreModule {}
```

### 의존성 주입(Dependency injection)

모듈 클래스 또한 프로바이더를 주입할 수 있다. (설정 관련 목적 등)
```tsx
import { Module } from '@nestjs/common';
import { CatsController } from './cats.controller';
import { CatsService } from './cats.service';

@Module({
  controllers: [CatsController],
  providers: [CatsService],
})
export class CatsModule {
  constructor(private catsService: CatsService) {}
}
```

하지만, 모듈 클래스들은 순환 독립성에 따라 프로바이더로 주입될 순 없다.

### 전역 모듈 (Global modules)

만약 모든 곳에서 같은 모듈을 필요로 한다면 이를 일일이 가져오는 것은 귀찮은 일이다. Nest와는 다르게, Angular `providers`는 전역에 등록될 수 있다. 등록되고 나면, 어디서든지 사용 가능하다. 하지만 Nest는 프로바이더들을 모듈 범위 안으로 캡슐화하고 있다. 캡슐화하고 있는 모듈을 가져오지 않고선 모듈의 프로바이더를 사용할 순 없다.

만약 프로바이더들을 모든 곳에서 사용할 수 있도록 만들고 싶으면, `@Global()` 데코레이터를 사용해 모듈을 **전역**으로 만들면 된다.

```tsx
import { Module, Global } from '@nestjs/common';
import { CatsController } from './cats.controller';
import { CatsService } from './cats.service';

@Global()
@Module({
  controllers: [CatsController],
  providers: [CatsService],
  exports: [CatsService],
})
export class CatsModule {}
```

`@Global()` 데코레이터는 모듈을 전역 범위로 만든다. 전역 모듈은 일반적으로 루트 또는 코어 모듈에 의해 오직 한 번만 등록되어야 한다. 위 예시를 보면 `CatsService` 프로바이더는 전역에서 사용할 수 있게 되면서 이 서비스를 주입하고 싶은 모듈들은 `imports` 배열에 `CatsModule`를 넣지 않아도 되게 된다.

> **힌트**
> 모든 모듈을 전역으로 만드는 것은 좋은 설계가 아니다. 전역 모듈은 중복되는 코드를 줄이기 위해 사용한다. 일반적으로 모듈의 API를 제공하는 좋은 방법은 `imports` 배열을 사용하는 것이다.

### 동적 모듈(Dynamic modules)

Nest 모듈 시스템은 **동적 모듈**이라고 불리는 강력한 기능을 가지고 있다. 이 기능은 프로바이더를 동적으로 등록하고 설정할 수 있는 커스텀 모듈을 쉽게 만들 수 있도록 해준다. 동적 모듈에 관한 내용은 다음을 참고해라. 이 챕터는 모듈에 관한 전반적인 내용을 설명하기 위해 간략한 설명만 담고 있다. 

다음은 동적 모듈 `DatabaseModule`에 대한 정의다.

```tsx
import { Module, DynamicModule } from '@nestjs/common';
import { createDatabaseProviders } from './database.providers';
import { Connection } from './connection.provider';

@Module({
  providers: [Connection],
})
export class DatabaseModule {
  static forRoot(entities = [], options?): DynamicModule {
    const providers = createDatabaseProviders(options, entities);
    return {
      module: DatabaseModule,
      providers: providers,
      exports: providers,
    };
  }
}
```

> **힌트**
> `forRoot()` 메소드는 동기 또는 비동기적(`Promise`를 통해)으로 동적 모듈을 반환한다. 

이 모듈은 `Connection` 프로바이더를 기본으로 정의한다. 하지만 추가적으로 `forRoot()` 메소드에 전달된 `entities`와 `options` 객체에 의존하면서 프로바이더 콜랙션, 예를 들어 레포지토리, 을 노출한다. 동적 모듈에 의해 반환된 프로퍼티들은 `@Module()` 데코레이터에 정의된 기본 모듈 데이터를 (덮어 쓰기 보다는) **확장**한다. 이를 통해 정적으로 선언된 `Connection` 프로바이더와 **그리고** 동적으로 생성된 레포지토리 프로바이더가 모듈 밖으로 내보내질 수 있게 되는 방식이다.

동적 모듈을 전역으로 선언하고 싶으면, `global` 프로퍼티를 `true`로 하면 된다.

```tsx
{
  global: true,
  module: DatabaseModule,
  providers: providers,
  exports: providers,
}
```

> **주의**
> 위에서 말했듯이, 모두 전역으로 만드는 것은 **좋은 설계가 아니다**.

`DatabaseModule`은 다음과 같이 가져와진 다음 설정될 수 있다.

```tsx
import { Module } from '@nestjs/common';
import { DatabaseModule } from './database/database.module';
import { User } from './users/entities/user.entity';

@Module({
  imports: [DatabaseModule.forRoot([User])],
})
export class AppModule {}
```

다시 이 동적 모듈을 내보내고 싶다면 `exports` 배열에서는 `forRoot()` 메소드 호출을 생략할 수 있다.

```tsx
import { Module } from '@nestjs/common';
import { DatabaseModule } from './database/database.module';
import { User } from './users/entities/user.entity';

@Module({
  imports: [DatabaseModule.forRoot([User])],
  exports: [DatabaseModule],
})
export class AppModule {}
```

동적 모듈 챕터에서 이 주제에 대해 더 자세히 다루고 있으며 관련 예시 또한 있으니 참고하길 바란다.

> **주의**
> `ConfigurableModuleBuilder`를 이용하면 더 상세히 동적 모듈을 커스텀하여 빌드할 수 있는데, 해당 방법에 대해선 이 챕터를 참고하길 바란다.