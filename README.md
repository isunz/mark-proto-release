# MARKPROTO 배포 저장소

설치 파일·플랫폼별 업데이트 정보·배포 도구를 관리함. 제품 소스는 별도 비공개 저장소에 유지함.

- 확정 정책: [빌드·업데이트 정책](https://github.com/isunz/mark-proto-space/blob/main/doc/policies/release/README.md)임.
- Mac에서 실행하면 Mac 로컬 빌드와 Windows CI를 연결함. Windows에서 실행하면 Windows 로컬 빌드와 Mac CI를 연결함.
- 웹은 별도로 커밋·푸시하며 서버 재시작은 운영자가 수행함.
- macOS는 DMG를 열고 앱을 응용 프로그램 폴더에 끌어다 놓아 대치함. Apple 공증·코드 서명은 아직 적용하지 않음.
- Windows는 앱의 업데이트 버튼으로 설치 후 재시작함. 최초 버전에는 업데이트 기능이 없으므로 최초 1회 직접 설치가 필요함.

```bash
# packages/client·apps/desktop 커밋/푸시 및 app-source.properties 고정 후 실행함
node scripts/release.js build        # 현재 OS 로컬 + 다른 OS CI
node scripts/release.js build local  # 현재 OS만
node scripts/release.js build remote # 다른 OS만
node scripts/release.js stage        # 비공개 초안으로 업로드하여 검토함
node scripts/release.js publish      # 검증된 산출물 서명·업로드·채널 반영
```

작업 공간의 로컬 경로는 `release/`이며 원격은 `isunz/mark-proto-release`임. 본체는 `../packages/client`, 데스크톱은 `../apps/desktop`을 사용함. 별도 배치는 `MARKPROTO_APP_DIR`·`MARKPROTO_DESKTOP_DIR`로 지정함. [명명 정책](../doc/policies/repository/README.md)을 따름.

필수 도구: Node 22 이상, Git, gh 로그인, 로컬 Tauri 빌드 환경임. Windows는 Git Bash에서 실행함. 공개 배포는 저장소 공개 설정을 먼저 확인해야 함. 사용자 확정에 따라 배포 저장소는 공개임. 소스 저장소는 비공개로 유지함.

- 업데이트 개인키는 `MARKPROTO_SIGNING_KEY` 또는 `~/.config/markproto-release/updater.key`에서 읽음. 키를 커밋하지 않으며 별도 안전한 백업이 필요함. 비밀번호 사용 시 `TAURI_SIGNING_PRIVATE_KEY_PASSWORD`를 설정함.
- 데스크톱 CI는 `APP_SOURCE_DEPLOY_KEY`로 고정한 공통 app 소스만 읽음. 읽기 전용 deploy key이며 배포 개인키·GitHub 개인 토큰을 CI에 넣지 않음.
- `.artifacts/`는 로컬 임시 산출물임. 실패 시 해당 버전 폴더를 점검하고 재시도함. 공개 태그·설치 파일은 덮어쓰지 않음.
