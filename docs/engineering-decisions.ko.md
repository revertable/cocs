# 설계 기록

[COC'S](../README.ko.md) · [English](engineering-decisions.md)

기존 README에 기록한 경험과 같은 주제에 관한 작성자의 후속 회고를 선별한 구현 코드와 연결합니다. 화면 구조, HTTP 요청, 그리드 통합, 서버 저장 순서로 읽을 수 있습니다.

<a id="case-01"></a>
## 01 — 화면 상태와 컴포넌트 초기화의 공통화

**설계 의도.** 화면 전체 사이클을 이해하고 통제할 수 있는 구조를 원했습니다. Vuex 패턴을 참고해 화면 상태를 `viewX` 네임스페이스로 관리하고, 콘텐츠 변화를 UI 초기화에 연결했습니다. 반복 작업과 불필요한 DOM 업데이트를 줄이고, 각 기능 간 결합도를 낮추는 것이 목표였습니다.

**선택.** 화면마다 초기화와 이벤트 핸들러를 모으는 공통 규약을 사용했습니다. SBUx 연동에서는 콘텐츠 변화, 렌더링, 그리드, 버튼 액션, 권한과 라우팅을 하나의 주기로 연결했습니다. 화면 이동이 많은 환경에서 팀이 일관된 방식으로 화면을 만들도록 했습니다.

**코드 읽기.** [ViewX.js](../cases/01-screen-lifecycle/ViewX.js), [SBUx-render.js](../cases/01-screen-lifecycle/SBUx-render.js), [Menu.js](../cases/02-requests-and-navigation/Menu.js) 순으로 읽고, [BGA010.html](../cases/04-grid-save-pipeline/BGA010.html)의 `viewX` 선언과 핸들러로 이어집니다.

**경험.** “리액트엔 이런 게 있어요”라는 팀원의 말은 공통 규약을 만드는 데 영향을 주었습니다. 다른 팀원이 Vue와 비슷한 익숙함을 이야기한 것도 팀이 알아볼 수 있는 구조를 만들겠다는 의도와 맞닿아 있습니다. 저는 이 접근이 개발·유지보수를 단순하게 하고 팀 생산성을 높였다고 느꼈습니다.

<a id="case-02"></a>
## 02 — 익숙한 HTTP API와 화면 전환 제어

**설계 의도.** exios는 axios 스타일의 API를 차용해 익숙하고 직관적인 HTTP 호출을 지향했습니다. JSP 기반 개발을 포함한 프로젝트의 레거시 프런트엔드 환경에서 사용할 수 있도록 설계했습니다. 요청 토큰 관리로 동시 작업과 중복 요청을 통제하고, 공통 내비게이션 규약으로 요청과 화면 전환을 연결하려 했습니다.

**코드 읽기.** [Korbiztech.exios.js](../cases/02-requests-and-navigation/Korbiztech.exios.js), [Menu.js](../cases/02-requests-and-navigation/Menu.js), [Router.js](../cases/02-requests-and-navigation/Router.js)를 함께 읽습니다. 공통 요청 유틸리티, 콘텐츠 로딩 진입점, URL 기반 중복 이동 확인을 볼 수 있습니다.

**기존 README에 기록한 경험.** SPA 방식 화면 전환 중 오래 걸리는 이전 화면의 조회가 이동 뒤에 응답하면서, 다음 화면에 렌더링되는 일을 겪었습니다. 당시에는 페이지 전환과 분리된 JavaScript 모듈 사이의 타이밍이 맞물려, 취소가 적용되어야 할 시점에 응답이 유효성 판단을 통과하고 렌더링까지 이어졌다고 회고했습니다.

제가 기억하는 개선 방향은 **응답 도착 시 유효성 조건을 재확인하고**, 일정 시점 이후에는 요청을 무시하거나 폐기하는 구조였습니다. 이 경험으로 요청 타이밍 통제와 상태 보존·무효화의 실무적 중요성을 깊이 체감했습니다.

**교훈.** 신뢰할 수 있는 비동기 상호작용에는 상태와 응답이 유효한 시점을 통제하는 설계가 필요합니다. 당시 경험을 통해 기록한 교훈입니다.

<a id="case-03"></a>
## 03 — 재사용 가능한 그리드 동작과 통합

**설계 의도.** 그리드 기반 데이터 입력·조회·Excel 연동을 공통 코드로 통합했습니다. 페이지 구조, 권한, 메뉴 바인딩도 전체 UI/UX 통합 전략의 일부로 다뤘습니다.

**후속 회고.** 프로젝트 요구사항에 맞추어 SBGrid API와 직접 작성한 JavaScript 동작을 결합하고, 공통 확장 계층으로 구성했습니다. 공통 설정과 API 래퍼, 행 편집·삭제, 편집과 집계 표시 전환 흐름을 여러 업무 화면에서 재사용하도록 했습니다.

**코드 읽기.** [Korbiztech.js](../cases/03-grid-integration/Korbiztech.js)는 원본 유틸리티 파일 전체를 보존합니다. 그리드 설정·생성 함수에서 `gridAction`으로 읽고, [BGA010.html](../cases/04-grid-save-pipeline/BGA010.html)의 사용 지점으로 이어집니다. 파일 전체를 함께 두어 원래 구성과 인접 유틸리티의 관계를 보존했습니다.

**경험.** 팀 전체의 납기를 위해 공통 기반 마련을 우선했습니다. 공통 동작을 먼저 완성하고 세부 검증·표시 규칙을 다듬는 순서를 선택했습니다. 구조의 일관성과 프로젝트 일정을 함께 고려했던 판단입니다.

<a id="case-04"></a>
## 04 — 화면 동작에서 서버 저장까지의 연결

**설계 의도.** 공통 화면 구조를 그리드 동작과 백엔드 업무 로직까지 연결했습니다. BGA010은 이 연결을 간결하게 읽는 예시이고, 기존 README의 BGA030은 트리·다중 그리드·업로드·모달을 결합한 확장된 업무 화면입니다.

**코드 읽기.** 다음 순서로 파일을 따라갑니다.

| 단계 | 선별한 원본 파일 |
|---|---|
| 화면 상태·이벤트·조회·저장 핸들러 | [BGA010.html](../cases/04-grid-save-pipeline/BGA010.html) |
| 공통 그리드 동작과 요청 API | [Korbiztech.js](../cases/03-grid-integration/Korbiztech.js) · [exios](../cases/02-requests-and-navigation/Korbiztech.exios.js) |
| HTTP 진입점과 업무 서비스 | [BGA010_RestController.java](../cases/04-grid-save-pipeline/BGA010_RestController.java) · [BGA010_Service.java](../cases/04-grid-save-pipeline/BGA010_Service.java) |
| 변경 데이터·파싱·업무 값 | [SaveGridDataVO.java](../cases/04-grid-save-pipeline/SaveGridDataVO.java) · [JsonUtil.java](../cases/04-grid-save-pipeline/JsonUtil.java) · [BGA010_VO.java](../cases/04-grid-save-pipeline/BGA010_VO.java) |
| 저장 인터페이스와 SQL 매핑 | [BGA010_DAO.java](../cases/04-grid-save-pipeline/BGA010_DAO.java) · [BGA010.xml](../cases/04-grid-save-pipeline/BGA010.xml) |

### 오류 메시지 정책: 기술이사와의 의견 차이

한 기술 이사는 DB 오류가 발생한 필드를 서버에서 상세히 응답해야 한다고 주장했습니다. 저는 사용자가 작업하는 자리에서 프런트엔드가 즉시 검증하고 피드백을 주는 방식을 선호했습니다.

선택한 정책은 프런트엔드가 실시간 검증과 상세 안내를 맡고, 서버는 요청의 유효성을 판단하며 보안을 위해 최소한의 정보를 제공하는 것이었습니다. 저는 이 역할 구분이 전체 사이클과 유지보수를 단순하게 했다고 느꼈습니다. 프런트엔드 개발자들로부터도 “예상 외로 쉽고, 명확했다”는 피드백을 받았습니다.

### Service와 ServiceImpl

일부 개발자는 Mock 구현체와 향후 대체 구현을 위한 서비스 인터페이스를 선호하며 확장성과 유연성을 강조했습니다. 저는 실무 경험을 바탕으로 그 가능성과 계층 추가에 따른 구현·유지보수 비용을 함께 고려했습니다. 여러 SI 프로젝트에서 구체 서비스 클래스가 실제 구현 요구를 충족한다고 판단했습니다.

단일 구체 서비스 클래스 구조를 선택했고, 개발 속도와 가독성이 높아지며 유지보수가 단순해졌다고 느꼈습니다. 후속 회고에서는 짧은 일정 안에서 팀이 구현하고 유지할 수 있는 구조를 우선했다는 맥락도 함께 설명했습니다.

## 출처

- [기준 커밋의 기존 README](https://github.com/revertable/cocs/blob/ffc04bfbac31d731e98e6589f97e8ea30f4a94cb/README.md): 프로젝트 범위·역할·기술·설계 의도·팀 피드백과 세 가지 결정 일화.
- 작성자의 후속 회고: 같은 주제에 관한 공통 기반과 개발 순서, 팀 피드백, SBGrid 통합 경험.
- [출처 manifest](source-manifest.json): `ffc04bfbac31d731e98e6589f97e8ea30f4a94cb`에서 선별한 원본 파일 14개의 경로와 SHA-256 해시.
