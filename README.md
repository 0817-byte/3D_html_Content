[README.md](https://github.com/user-attachments/files/34724057/README.md)
# Three.js 디지털 트윈 실습 파일 분석

> 실행 방법 · 전체 문법 구조 · 코드 흐름 정리

| 항목 | `ThreeJS_한국_시내버스_조립공장_3D.html` | `smart_classroom_camera_stable.html` |
|---|---|---|
| 주제 | 한국형 저상 전기버스 **조립 공정** 디지털 트윈 | 스마트 학교 **교실 환경** 디지털 트윈 |
| 분량 | 668줄 · 약 58KB | 502줄 · 약 56KB |
| Three.js | r180 (`three@0.180.0`) | r164 (`three@0.164.1`) |
| 핵심 기능 | 8단계 조립 애니메이션, X-RAY, 분해 보기, 부품 클릭 설명 | 가상 IoT 센서 계산, 설비 제어 슬라이더, 데이터 내보내기 |
| 3D 모델 | 외부 파일 없음 (코드로 생성한 절차적 지오메트리) | 외부 파일 없음 (절차적 지오메트리 + 캔버스 텍스처) |
| 데이터 | 교육용 가상 공정 데이터 | 교육용 가상 센서 데이터 (실제 센서 연결 없음) |

---

## 1. 실행 방법

### 1-1. 필요한 환경

- **브라우저**: 최신 Chrome 또는 Edge (WebGL 2 지원)
- **인터넷 연결**: Three.js를 jsDelivr CDN에서 불러옵니다. 오프라인에서는 3D가 뜨지 않습니다.
- **하드웨어 가속 켜기**: Chrome `설정 → 시스템 → 가능한 경우 그래픽 가속 사용`
- 별도 설치(npm, 빌드 도구)는 **필요 없습니다.** 두 파일 모두 HTML 한 장으로 완결됩니다.

### 1-2. 방법 A — 파일 더블클릭

HTML 파일을 더블클릭해 브라우저로 엽니다. 라이브러리를 `https://` CDN에서 가져오므로 대부분 그대로 동작합니다.
다만 브라우저 보안 정책에 따라 `file://` 주소에서 모듈 로딩이 막힐 수 있으므로, 화면이 뜨지 않으면 방법 B를 쓰세요.

### 1-3. 방법 B — 로컬 서버 (권장)

파일이 있는 폴더에서 터미널을 열고 아래 중 하나를 실행합니다.

```bash
# Python 3
python -m http.server 8000

# Node.js
npx serve .
```

브라우저에서 접속:

```
http://localhost:8000/smart_classroom_camera_stable.html
http://localhost:8000/ThreeJS_한국_시내버스_조립공장_3D.html
```

VS Code를 쓴다면 **Live Server** 확장에서 파일을 우클릭하고 `Open with Live Server`를 눌러도 됩니다.

### 1-4. 방법 C — GitHub Pages 배포

1. 저장소 루트에 두 HTML 파일과 메인 `index.html`을 올립니다.
2. `Settings → Pages → Build and deployment`에서 Branch를 `main`, 폴더를 `/ (root)`로 지정합니다.
3. 1~2분 뒤 `https://<계정명>.github.io/<저장소명>/`로 접속합니다.

> 한글 파일명도 동작하지만 주소창에서는 `%ED%95%9C...` 형태로 인코딩됩니다. 링크를 공유할 일이 많다면 `bus_assembly_3d.html`처럼 영문 파일명으로 바꾸는 것을 권장합니다.

### 1-5. 조작 방법

**스마트 교실**

| 입력 | 동작 |
|---|---|
| 마우스 드래그 | 카메라 회전 |
| 마우스 휠 | 확대 · 축소 |
| 우클릭 드래그 | 화면 이동(팬) |
| 하단 `CAMERA` 버튼 | 교실 전체 · 학생 시점 · 전자칠판 · 창가 · 평면 조망 |
| 오른쪽 슬라이더 | 재실 인원 · 자연광 · 목표 온도 · 환기 · LED · 블라인드 |
| `SCENARIO` 버튼 | 수업 중 · 쉬는 시간 · 집중 환기 · 야간 소등 |
| 상단 버튼 | 장면 JSON · 센서 CSV · 3D 화면 PNG 내려받기 |

**버스 조립공장**

| 입력 | 동작 |
|---|---|
| `Space` | 자동 조립 실행 / 일시 정지 |
| `←` / `→` | 이전 / 다음 단계 |
| `Esc` | 모바일 메뉴 닫기 |
| 왼쪽 공정 목록 | 원하는 단계로 이동 |
| 3D 부품 클릭 | 오른쪽 패널에 부품 설명 표시 |
| 상단 시점 버튼 | 사선 · 측면 · 전면 · 후면 · 실내 · 상부 · 하부 |
| 오른쪽 아이콘 | X-RAY(외판 반투명) · 360° 자동 회전 · 기본 시점 |

### 1-6. 문제 해결

| 증상 | 원인 | 해결 |
|---|---|---|
| 로딩 화면에서 멈춤 | CDN 접속 실패 | 인터넷 연결, 회사·학교 방화벽 확인 |
| 2D 버스 그림과 "불러오지 못했습니다" 표시 (버스) | Three.js 모듈 로딩 실패 | 로컬 서버로 실행 후 `F12 → Console` 오류 확인 |
| "3D 교실 실행에 실패했습니다" (교실) | 모듈 로딩 실패 또는 14초 초과 | 위와 동일, 하드웨어 가속 확인 |
| "WebGL 초기화 실패" | 그래픽 가속 꺼짐 / 구형 GPU | 하드웨어 가속 켜기, 다른 브라우저 시도 |
| "그래픽 연결이 끊겼습니다" (교실) | GPU 컨텍스트 손실 | 잠시 기다리거나 `다시 실행` |
| 화면이 끊기거나 느림 | 고해상도 화면에서 GPU 부담 | 브라우저 창 크기 줄이기, 다른 탭 닫기 |

---

## 2. 공통 문법 구조

두 파일은 같은 뼈대를 공유합니다. **HTML 한 장 안에 CSS · UI · 3D 코드가 모두 들어 있는 단일 파일 구조**입니다.

```text
<!DOCTYPE html>
<html lang="ko">
├─ <head>
│   ├─ <meta>              문자셋, 뷰포트, 테마 색
│   ├─ <style>             CSS 변수(:root) + 레이아웃 + 반응형(@media)
│   └─ (교실만) <script type="application/json" id="sceneSpec">  장면 설정 데이터
└─ <body>
    ├─ UI 마크업           헤더, 패널, 버튼, KPI 카드, 로딩·오류 화면
    ├─ <script>             (선택) 모듈 실패 감지용 일반 스크립트
    ├─ <script type="importmap">   'three' 이름을 CDN 주소에 연결
    └─ <script type="module">      Three.js 3D 장면 전체
```

### 2-1. Import Map + ES Module

빌드 도구 없이 `import` 문을 쓰기 위한 표준 문법입니다.

```html
<script type="importmap">
{ "imports": {
    "three": "https://cdn.jsdelivr.net/npm/three@0.180.0/build/three.module.js",
    "three/addons/": "https://cdn.jsdelivr.net/npm/three@0.180.0/examples/jsm/"
} }
</script>

<script type="module">
import * as THREE from 'three';                                         // 본체
import { OrbitControls } from 'three/addons/controls/OrbitControls.js'; // 마우스 카메라 조작
</script>
```

- `importmap`이 모듈보다 **먼저** 나와야 합니다.
- `type="module"` 스크립트는 자동으로 `defer`처럼 동작하고(HTML을 다 읽은 뒤 실행), 자체 스코프를 가지므로 변수가 전역으로 새지 않습니다.
- `"three/addons/"`처럼 끝이 `/`인 키는 **경로 접두사 매핑**입니다.

### 2-2. Three.js 기본 파이프라인

두 파일 모두 아래 순서로 3D 장면을 만듭니다.

```js
// ① 장면(Scene): 모든 객체를 담는 컨테이너
const scene = new THREE.Scene();
scene.background = new THREE.Color('#b4d0de');
scene.fog = new THREE.Fog('#b4d0de', 34, 82);         // 멀수록 흐려짐

// ② 카메라(Camera): 원근 투영 (시야각, 화면비, near, far)
const camera = new THREE.PerspectiveCamera(42, innerWidth/innerHeight, .08, 130);

// ③ 렌더러(Renderer): WebGL로 실제 픽셀을 그림
const renderer = new THREE.WebGLRenderer({ antialias:true, preserveDrawingBuffer:true });
renderer.outputColorSpace = THREE.SRGBColorSpace;      // 색 공간
renderer.toneMapping = THREE.ACESFilmicToneMapping;    // 영화풍 톤 매핑
renderer.shadowMap.enabled = true;                     // 그림자
host.appendChild(renderer.domElement);                 // <canvas>를 페이지에 붙임

// ④ 조명(Light)
scene.add(new THREE.HemisphereLight(sky, ground, 1.5)); // 하늘·바닥 환경광
const sun = new THREE.DirectionalLight('#ffeaca', 2.35); // 태양광 + 그림자
sun.castShadow = true;

// ⑤ 물체(Mesh) = 형태(Geometry) + 재질(Material)
const mesh = new THREE.Mesh(
  new THREE.BoxGeometry(w, h, d),
  new THREE.MeshStandardMaterial({ color, roughness, metalness })  // PBR 재질
);
mesh.position.set(x, y, z);
group.add(mesh);                                        // 그룹 → 장면 계층 구조

// ⑥ 카메라 조작
const controls = new OrbitControls(camera, renderer.domElement);
controls.enableDamping = true;                          // 관성

// ⑦ 애니메이션 루프: 매 프레임 상태 갱신 후 렌더링
function animate(){
  requestAnimationFrame(animate);   // 버스 파일은 renderer.setAnimationLoop() 사용
  controls.update();
  renderer.render(scene, camera);
}
```

### 2-3. 좌표계와 단위

- 단위는 **미터**. Three.js는 `X 오른쪽, Y 위, Z 화면 앞쪽(관찰자 방향)`입니다.
- **버스**: 차량 앞이 `-X`, 승객 출입문이 `+Z` 쪽입니다.
- **교실**: 칠판 벽이 `Z = -4.8`, 창문 벽이 `X = -6.4`이고, Blender 변환식 `Blender(X,Y,Z) = (X, -Z, Y)`가 주석과 JSON에 적혀 있습니다.

### 2-4. 헬퍼 함수 패턴

수백 개의 부품을 짧게 만들기 위해 반복 코드를 작은 함수로 묶었습니다.

| 파일 | 함수 | 역할 |
|---|---|---|
| 버스 | `G(geo, mat, x, y, z)` | 메시 생성 + 위치 + 그림자 설정 |
| 버스 | `bx / cyl / sph / tube` | 상자 · 원기둥 · 구 · 곡선관 생성 후 부모에 추가 |
| 버스 | `canvasLabel / labelPlane` | `<canvas>`에 글자를 그려 3D 표지판으로 사용 |
| 교실 | `material(hex, rough, metal)` | 표준 재질 생성 단축 함수 |
| 교실 | `group / add` | 이름 있는 그룹 생성(→ JSON 내보내기에 이름이 그대로 쓰임) |
| 교실 | `bx / plane / cyl / sphere / rod` | 기본 도형. `rod`는 두 점을 잇는 막대(쿼터니언으로 방향 맞춤) |
| 교실 | `texCanvas / rng` | 시드 고정 난수로 바닥·벽 질감을 캔버스에 그려 텍스처로 사용 |

### 2-5. 공통 자바스크립트 문법 요소

| 문법 | 사용 예 | 의미 |
|---|---|---|
| 화살표 함수 | `const $=id=>document.getElementById(id)` | DOM 찾기 단축 함수 |
| 구조 분해·전개 | `camera.position.set(...v.cam)` | 배열 `[x,y,z]`를 인자 3개로 펼침 |
| 템플릿 문자열 | `` `STEP ${n}` `` | 문자열 안에 값 삽입 |
| 옵셔널 체이닝 | `err?.message` | 값이 없어도 오류 없이 `undefined` |
| `for...of` | `for(const z of [-1,1])` | 좌우 대칭 부품을 한 번에 생성 |
| `Map` | `materialDefaults`(버스) | 재질별 원래 투명도 저장 |
| `dataset` | `data-view="cabin"` → `b.dataset.view` | 버튼에 시점 이름 저장 |
| `addEventListener` | 클릭, 키보드, 리사이즈, 포인터 | UI ↔ 3D 연결 |
| `try...catch` | WebGL 생성, PNG 저장 | 실패 시 안내 화면 |

---

## 3. `smart_classroom_camera_stable.html` 구조

### 3-1. 파일 구간 지도

| 줄 | 구간 | 내용 |
|---|---|---|
| 1–20 | `<head>` · CSS | 다크 틸 팔레트(`--teal #72dfcb`), 글래스 패널, 반응형 4단계 |
| 21 | importmap | Three.js r164 |
| 22–38 | `sceneSpec` JSON | 방 크기, 가구 좌표, 센서 초기값, 계산식, 카메라 시점, Blender 변환 규칙 |
| 41–71 | UI 마크업 | 헤더, KPI 4개, 시나리오, 슬라이더 6개, CO₂ 차트, 카메라 버튼, 로딩 |
| 72–112 | 일반 `<script>` | 모듈 실패·14초 시간 초과 감지 → 오류 화면 표시 |
| 113–499 | `<script type="module">` | 3D 장면과 시뮬레이션 전체 (아래 표) |

**모듈 내부**

| 줄 | 역할 |
|---|---|
| 123–165 | `SPEC` 읽기, 렌더러(pixelRatio ≤ 1.2, 정적 그림자), 카메라, OrbitControls 제한, `clampTarget()` |
| 166–183 | 헬퍼 함수, 시드 난수, 바닥·벽 절차 텍스처 |
| 184–236 | 건축물, 창문 4개 + 블라인드, `updateWallCutaway()` 벽 자동 숨김 |
| 238–249 | 창밖 건물 · 나무 |
| 251–307 | 책상 24개 · 의자 · 학생 24명 · 교탁 · 책장 · 화분 |
| 309–352 | 전자칠판(캔버스 텍스처), 프로젝터, 에어컨, 공기청정기, IoT 센서, LED 패널 6개, 조명 |
| 354–356 | 3D 센서 라벨(Sprite) |
| 357–428 | `sim` 상태, 칠판·라벨·KPI·차트 갱신, 슬라이더 · 시나리오 · 토글 이벤트 |
| 429–441 | 카메라 시점 트윈, 시점 초기화 |
| 442–450 | JSON · CSV · PNG 내보내기, 샘플 기록 |
| 451–468 | `animate()` 루프: 시뮬레이션 계산 → UI → 카메라 트윈 → 제한 → 렌더 |
| 469–493 | 리사이즈, WebGL 컨텍스트 손실·복구 |
| 495–498 | 초기화 실행, 준비 완료 표시 |

### 3-2. 데이터 흐름

```text
[슬라이더 / 시나리오 버튼]
        │ input · click
        ▼
readInputs()  ──►  sim { occupancy, ventilation, led, blinds, ... }
        │
        ├─► updateVisual()   3D 반영: 학생 표시 수, 조명 밝기, 블라인드 높이, 공기 흐름
        └─► updateUI()       KPI 카드 · 경고 문구 · 3D 라벨 · 전자칠판 다시 그리기
                                   ▲
animate() 매 프레임 ───────────────┘  sim.co2 / sim.roomTemp 를 목표값으로 서서히 수렴
        │ 8초(가상)마다
        └─► sample()  → history(차트 52개) · samples(CSV 최대 6000개)
```

### 3-3. 시뮬레이션 계산식

`sceneSpec`에 적힌 식이 `animate()`와 `calcLux()`에 그대로 구현돼 있습니다.

| 값 | 식 | 반응 속도 |
|---|---|---|
| CO₂ 평형값 | `430 + 인원×60 / (0.8 + 환기%/50)` | 시상수 95초(가상) |
| 조도 | `자연광%×3.8×(0.35+0.65×블라인드%/100) + LED%×3.5` | 즉시 |
| 실내 온도 | `목표온도 + 인원×0.025 + 일사량×0.60 − 환기%×0.003` | 시상수 240초(가상) |
| 경고 기준 | CO₂ ≥ 1000ppm · 조도 < 300 또는 > 700lux · 온도 < 20 또는 > 27℃ | — |

수렴은 지수 평활 `값 += (목표 − 값) × (1 − e^(−dt/τ))` 형태이며, 가상 시간은 실제 1초당 약 12초로 흐릅니다.

### 3-4. 카메라 안정화 기법

파일 이름의 `camera_stable`에 해당하는 부분입니다.

- `minDistance 3.1 / maxDistance 32`, `maxPolarAngle 0.47π` → 바닥 아래·너무 먼 곳으로 가지 않음
- `clampTarget()` → 팬으로 기준점이 교실 밖으로 나가지 않게 제한
- `controls.addEventListener('start', …)` → 시점 이동 중 사용자가 드래그하면 트윈 취소
- `updateWallCutaway()` → 카메라가 벽 바깥으로 나가면 앞벽·창벽을 숨김(경계에 여유값을 둬 깜빡임 방지)
- `shadowMap.autoUpdate = false` → 정적 그림자를 한 번만 계산, 학생 모델은 그림자 제외
- `webglcontextlost / restored` → 그래픽 장치가 끊겨도 복구 시도

---

## 4. `ThreeJS_한국_시내버스_조립공장_3D.html` 구조

### 4-1. 파일 구간 지도

| 줄 | 구간 | 내용 |
|---|---|---|
| 1–13 | `<head>` · CSS | 네이비·블루 팔레트, 3단 그리드(공정 · 3D · 관제), 반응형 3단계 |
| 15–67 | UI 마크업 | 공정 목록(초기 8개 div → 실행 시 버튼으로 교체), 컨트롤, 색상, 시점, 진행 바, KPI, 로그 |
| 68 | `<dialog>` | 조작 도움말 |
| 69 | importmap | Three.js r180 |
| 70–661 | `<script type="module">` | 3D 장면과 조립 로직 전체 (아래 표) |
| 662–667 | 일반 `<script>` | 12초 뒤 준비 안 됐으면 2D 대체 화면 표시 |

**모듈 내부**

| 줄 | 역할 |
|---|---|
| 79–88 | `stageData` — 8단계 이름·설명 배열 |
| 89–118 | 장면, 카메라, 렌더러(`try...catch`), OrbitControls, 조명 |
| 119–135 | 재질 정의 (강철, 고무, 유리, 버스 도장 `busPaint` 등) |
| 137–154 | 헬퍼 함수, 캔버스 라벨 |
| 156–245 | 공장: 바닥, 벽, 창, 트러스, 천장 조명, 덕트, 컨베이어, 크레인, 로봇 팔 2대, 작업대, 표지판 |
| 248–258 | `bus` 그룹, `part()` 부품 등록 함수 |
| 259–459 | 버스 모듈 14개 생성 (아래 표) |
| 460–476 | `state` 상태 객체, `applyXray()`, `changePaint()` |
| 477–500 | 이벤트 로그, 부품 선택 |
| 501–550 | `uiStage()` 화면 갱신, `setStage()` 단계 전환, 자동 실행, 도어, 분해 |
| 551–584 | 시점 프리셋 7개, 레이캐스트로 부품 클릭 |
| 585–622 | 버튼 · 키보드 · 모바일 메뉴 · 리사이즈 이벤트 |
| 624–658 | `setAnimationLoop` 루프 |
| 659 | `showFailure()` |

### 4-2. 부품 등록 구조 — `part()`

모든 버스 부품은 `THREE.Group`으로 만든 뒤 `part()`로 등록됩니다. 이 레코드 하나가 조립 · 분해 · 클릭 설명을 모두 결정합니다.

```js
part(id, title, stage, info, group, from=[0,0,0], explode=[0,0,0], kind='mechanical')
```

| 필드 | 의미 | 예 (`battery`) |
|---|---|---|
| `id` | 고유 이름 (레이캐스트 결과와 연결) | `'battery'` |
| `title / info` | 오른쪽 패널에 표시할 이름·설명 | `'고전압 배터리·전기 모터'` |
| `stage` | 등장 단계 (0~7) | `2` |
| `group` | 실제 3D 객체 묶음 | `power` |
| `from` | 조립 시작 위치 오프셋 (여기서 날아와 붙음) | `[0,-2.1,0]` (아래에서 올라옴) |
| `explode` | 분해 보기 때의 위치 | `[0,-1.0,0]` |
| `kind` | `'shell'`·`'glass'`이면 X-RAY 대상 재질로 등록 | `'mechanical'` |

**단계별 모듈**

| 단계 | 부품 id |
|---|---|
| 01 섀시 프레임 | `chassis` |
| 02 차축·바퀴 | `wheels` |
| 03 배터리·구동계 | `battery` |
| 04 차체 골격 | `skeleton` |
| 05 외판·지붕 | `shell-near`, `shell-far`, `front`, `rear`, `roof` |
| 06 창문·출입문 | `glass`, `doors`, `lights` |
| 07 실내 | `interior` |
| 08 품질 검사 | `qa` |

### 4-3. 상태 객체와 조립 애니메이션

```js
const state = { stage:0, auto:false, speed:1, door:false, xray:false,
                explode:false, spin:false, transTime:1.4, hold:0, selected:null, view:'overview' };
```

- `setStage(n)` → `stage ≤ n`인 부품만 보이게 하고, 이번 단계 부품은 `from` 위치로 옮겨 둡니다.
- 루프에서 `transTime`을 1.4초까지 늘리며 `ease = 1 − (1 − t)³` (ease-out cubic)로 원위치까지 이동합니다.
- 자동 실행이면 이동이 끝난 뒤 `hold`가 약 2초 쌓이면 다음 단계로 넘어갑니다.
- X-RAY는 `materialDefaults`에 저장한 원래 투명도를 기준으로 외판·유리 재질의 `opacity`를 낮춥니다.

### 4-4. 부품 클릭 (Raycaster)

```text
pointerdown 위치 저장 → pointerup 위치와 6px 이내면 '클릭'으로 판단
→ 화면 좌표를 −1~1 정규화 좌표로 변환
→ raycaster.setFromCamera(pointer, camera)
→ 보이는 부품 메시들과 교차 검사 → userData.partId → selectPart(id)
```

드래그(회전)와 클릭을 이동 거리로 구분하는 점이 핵심입니다.

---

## 5. 두 파일 구조 비교

| 관점 | 버스 조립공장 | 스마트 교실 |
|---|---|---|
| 설계 중심 | **시간 순서**(공정 단계)에 따른 상태 변화 | **수치 모델**(센서 계산)에 따른 상태 변화 |
| 설정 데이터 | JS 배열 `stageData` | JSON 블록 `sceneSpec` (Blender 변환까지 염두) |
| 상태 객체 | `state` (단계·토글) | `sim` (센서값·설비값) |
| 렌더 루프 | `renderer.setAnimationLoop()` | `requestAnimationFrame()` |
| 3D ↔ UI 연결 | 부품 클릭 → 패널, 단계 → 진행 바 | 슬라이더 → 계산 → 라벨·칠판·차트 |
| 카메라 | 프리셋 7개, 매 프레임 lerp로 접근 | 프리셋 5개, 1초 smoothstep 트윈 + 범위 제한 |
| 그림자 | 매 프레임 갱신 | 정적 그림자, 필요할 때만 갱신 |
| 오류 대비 | 12초 타임아웃 + 2D 대체 그림 | 전역 오류 감지 + 14초 타임아웃 + WebGL 복구 |
| 내보내기 | PNG | JSON · CSV · PNG |

---

## 6. 분석 중 확인한 주의점

**버스 조립공장 (이번에 첨부한 버전)**

1. **분해 보기 위치가 계속 밀리는 문제**: 루프의 `const pos=state.explode?p.explode:v3(0,0,0)` 뒤에 `pos.addScaledVector(...)`가 실행되어, 분해 보기를 켠 채 조립 중이면 원본 `p.explode` 벡터 자체가 변합니다. `p.explode.clone()`으로 복사해야 합니다.
2. **부품 선택 시 3D 강조가 없음**: `selectPart()`가 원래 발광값만 저장하고 실제 강조는 하지 않습니다. `selectedMeshMat` 변수도 쓰이지 않습니다.
3. **지오메트리 재사용 없음**: `bx / cyl / sph`가 호출될 때마다 새 Geometry를 만들어 GPU 메모리를 더 씁니다.
4. **카메라 충돌**: 시점 이동 중 드래그하면 lerp 이동과 사용자 조작이 부딪힙니다. 이동 범위 제한도 없습니다.
5. **조명 부담**: 천장 PointLight 18개, pixelRatio 최대 1.65, 그림자 맵 2048로 저사양 PC에서 느릴 수 있습니다.
6. **분해 보기 중 새 단계**: `setStage()`에서 새 부품을 `from` 위치로만 옮기고 분해 오프셋을 더하지 않습니다.

**스마트 교실**

- 큰 오류는 없습니다. 카메라 안정화, 정적 그림자, WebGL 복구가 이미 들어 있어 버스 파일을 개선할 때 참고 모델로 쓰기 좋습니다.
- 모든 IoT 값은 교육용 가상 데이터입니다(`simulationDisclaimer`).
