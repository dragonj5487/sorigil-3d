# 소리길 3D — 보행 레이더 (실지도 버전)

VWorld 3D 지도 Open API 위에서, 강동역을 향해 걸어가는 가상의 300m 경로를 체험하는 프로토타입입니다.
장애물에 가까워지면 그 방향으로 음성 안내·좌우 신호음·진동·화면 경고가 뜹니다.

> Claude Artifact로 만든 [설명 페이지](https://claude.ai/artifact/PpeM6r5ymj1NMBbC4jQHYm) / [Three.js 가상 지도 버전](https://claude.ai/artifact/EGbPTW8xK1rqiEfNisrGqg)의 후속작입니다.
> 저 두 페이지는 Claude Artifact 샌드박스 안에서 돌기 때문에 VWorld 같은 외부 지도 서버에 접속할 수 없어서,
> 실제 지도를 붙이려면 이렇게 별도의 정적 웹페이지로 분리해야 했습니다.

---

## 1. VWorld API 키 발급받기

1. https://dev.vworld.kr 에서 회원가입합니다.
2. **오픈API → 인증키 발급**에서 "3D 지도" 용 인증키를 신청합니다.
3. 신청할 때 **도메인**을 입력해야 합니다. 이 페이지를 GitHub Pages로 올린다면, 저장소 이름이
   `sorigil-3d`이고 계정이 `dragonj5487`이라면 도메인은 보통 다음과 같습니다.

   ```
   dragonj5487.github.io
   ```

4. 심사/승인까지 보통 몇 시간~1일 정도 걸릴 수 있습니다.

## 2. 키를 코드에 넣기

`index.html` 상단의 다음 줄을 찾아서:

```html
<script type="text/javascript"
  src="https://map.vworld.kr/js/webglMapInit.js.do?version=2.0&apiKey=VWORLD_API_KEY&domain=VWORLD_DOMAIN">
</script>
```

`VWORLD_API_KEY`는 발급받은 인증키로, `VWORLD_DOMAIN`은 1번에서 등록한 도메인으로 바꿔주세요.

> **반드시 `https://`로 시작해야 합니다.** GitHub Pages는 https로 서비스되는데 스크립트를 `http://`로
> 불러오면 브라우저가 "혼합 콘텐츠(mixed content)"로 보고 조용히 차단합니다 — 콘솔에만 에러가 남고
> 화면에는 아무 안내도 없이 지도가 안 뜹니다.

## 3. 로컬에서 확인하기

```bash
npx serve .
```

또는 아무 정적 서버로 `index.html`을 열면 됩니다. 키가 없거나 잘못되어도 지도만 비어 보일 뿐,
소리·진동·화면 경고 같은 체험 로직은 그대로 동작합니다.

## 4. 조정이 필요할 수 있는 값들

VWorld 3D API의 정확한 동작은 실제 키를 발급받아 화면을 보면서 맞춰야 합니다. `index.html` 안에서
다음 두 값을 특히 눈여겨봐 주세요.

```js
var CAMERA_HEIGHT = 220;  // 해수면 기준 추정 고도(m)
var CAMERA_TILT = -58;    // 아래를 내려다보는 각도(추정)
```

- 카메라가 건물 속에 파묻혀 보이면 `CAMERA_HEIGHT`를 높이세요.
- 하늘만 보이고 길이 안 보이면 `CAMERA_TILT`를 더 음수로(아래를 더 내려다보게) 바꾸세요.
- `TRAVEL_BEARING` (현재 35도)은 걷는 방향(나침반 기준)입니다. 실제 보도 방향에 맞게 조정할 수 있습니다.

장애물 위치·간격(`OBST_DEFS`, `CROSSWALK`)은 전부 시연용 가상 데이터이며, 도착 좌표(강동역)만
실제 좌표입니다.

## 5. GitHub Pages로 배포하기

1. 이 폴더를 새 GitHub 저장소(예: `sorigil-3d`)로 푸시합니다.
2. 저장소 **Settings → Pages**에서 "GitHub Actions"를 소스로 선택합니다.
3. `main` 브랜치에 푸시하면 `.github/workflows/deploy.yml`이 자동으로 배포합니다.
4. 배포된 주소(`https://<계정>.github.io/sorigil-3d/`)가 1번에서 등록한 도메인과 일치해야
   VWorld 지도가 정상적으로 뜹니다.
