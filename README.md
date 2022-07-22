<p align="center">
  <a href="http://nestjs.com/" target="blank"><img src="https://nestjs.com/img/logo-small.svg" width="120" alt="Nest Logo" /></a>
</p>

[travis-image]: https://api.travis-ci.org/nestjs/nest.svg?branch=master
[travis-url]: https://travis-ci.org/nestjs/nest
[linux-image]: https://img.shields.io/travis/nestjs/nest/master.svg?label=linux
[linux-url]: https://travis-ci.org/nestjs/nest

  <p align="center">효율적이고 확장 가능한 서버 단 어플리케이션 구축을 위한 진보적인 <a href="http://nodejs.org" target="_blank">Node.js</a> 프레임워크.</p>
    <p align="center">
<a href="https://www.npmjs.com/~nestjscore"><img src="https://img.shields.io/npm/v/@nestjs/core.svg" alt="NPM Version" /></a>
<a href="https://www.npmjs.com/~nestjscore"><img src="https://img.shields.io/npm/l/@nestjs/core.svg" alt="Package License" /></a>
<a href="https://www.npmjs.com/~nestjscore"><img src="https://img.shields.io/npm/dm/@nestjs/core.svg" alt="NPM Downloads" /></a>
<a href="https://travis-ci.org/nestjs/nest"><img src="https://api.travis-ci.org/nestjs/nest.svg?branch=master" alt="Travis" /></a>
<a href="https://travis-ci.org/nestjs/nest"><img src="https://img.shields.io/travis/nestjs/nest/master.svg?label=linux" alt="Linux" /></a>
<a href="https://coveralls.io/github/nestjs/nest?branch=master"><img src="https://coveralls.io/repos/github/nestjs/nest/badge.svg?branch=master#6" alt="Coverage" /></a>
<a href="https://discord.gg/G7Qnnhy" target="_blank"><img src="https://img.shields.io/badge/discord-online-brightgreen.svg" alt="Discord"/></a>
  <a href="https://dev.to/nestjs"><img src="https://img.shields.io/badge/blog-dev.to-green"/></a>
<a href="https://opencollective.com/nest#backer"><img src="https://opencollective.com/nest/backers/badge.svg" alt="Backers on Open Collective" /></a>
<a href="https://opencollective.com/nest#sponsor"><img src="https://opencollective.com/nest/sponsors/badge.svg" alt="Sponsors on Open Collective" /></a>
  <a href="https://paypal.me/kamilmysliwiec"><img src="https://img.shields.io/badge/Donate-PayPal-dc3d53.svg"/></a>
  <a href="https://twitter.com/nestframework"><img src="https://img.shields.io/twitter/follow/nestframework.svg?style=social&label=Follow"></a>
</p>
  <!--[![Backers on Open Collective](https://opencollective.com/nest/backers/badge.svg)](https://opencollective.com/nest#backer)
  [![Sponsors on Open Collective](https://opencollective.com/nest/sponsors/badge.svg)](https://opencollective.com/nest#sponsor)-->

## 설명

이 프로젝트는 [Angular CLI](https://github.com/angular/angular-cli)를 기반으로 제작되었다. 마크다운 포맷으로 작성한 소스 문서를 출판하기 위해 [Dgeni 문서 생성기](https://github.com/angular/dgeni)를 사용했다. 이 레포지토리는 공식 Nest 문서인 [docs.nestjs.com](https://docs.nestjs.com)의 소스 코드를 포함한다.

## 설치

프로젝트 의존성을 설치하고 다음 터미널 명령어에 따라 로컬 서버를 시작해라:

```bash
$ npm install
$ npm run start
```

[`http://localhost:4200/`](http://localhost:4200/)로 접속해라.

모든 페이지들은 [마크다운](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet)으로 작성되었으며 `content` 디렉토리에 위치한다.

## 빌드

프로젝트를 빌드하기 위해선 `npm run build`를 실행하면 된다. 빌드 아티펙트들은 `dist/` 디렉토리에 저장된다.

빌드를 _감시 모드(watch mode)_ 로 실행하려면, `npm run build:watch`를 실행해라. 파일이 바뀌면 자동으로 다시 컴파일되고 빌드 되며, [`http://localhost:4200/`](http://localhost:4200/)에서 제공된다.

프로덕션 빌드를 하려면 `npm run build:prod`를 사용해라.

## 지원

Nest는 MIT-라이센스인 오픈 소스 프로젝트다. 스폰서들과 놀라온 후원자들의 지원 덕분에 성장할 수 있었다. 참여를 원하면, [다음을 참고해라](https://opencollective.com/nest).

## 연락

- 저자 - [Kamil Myśliwiec](https://twitter.com/kammysliwiec)
- 웹사이트 - [https://nestjs.com](https://nestjs.com/)
- 트위터 - [@nestframework](https://twitter.com/nestframework)
- 번역 - [Changyu Shin](https://scv1702.tistory.com/)

## 라이센스

Nest는 [MIT 라이센스](LICENSE)다.