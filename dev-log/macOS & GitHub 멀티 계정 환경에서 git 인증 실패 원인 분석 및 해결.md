
### 원인

`git`과 `gh`는 **credential(인증 정보)을 별도로 관리**합니다.

```
git pull 실행 시 credential 조회 순서:

1. macOS Keychain (osxkeychain)  ← Xcode가 설정한 시스템 기본값
2. gh auth git-credential         ← gh CLI가 설정한 전역 설정
```

`gh auth switch`로 계정을 전환해도, **macOS Keychain에 이전 계정의 토큰이 캐시**되어 있으면 git이 그걸 먼저 사용합니다. 즉:

- `gh` CLI는 Brom-Buzzni로 전환 완료
- `git`은 Keychain에 캐시된 **다른 계정(kbsowo14)의 토큰**으로 접근 시도
- kbsowo14에 repo 접근 권한이 없으므로 → `Repository not found`

### 해결

`gh auth setup-git`을 실행하면 git이 `gh`의 credential을 우선 사용하도록 갱신됩니다. 현재는 정상적으로 `gh auth git-credential`이 먼저 호출되어 올바른 계정으로 인증되고 있는 상태입니다.

### 앞으로 계정 전환 시

`~/.zshrc`에 alias를 추가하면 편합니다:

```bash
alias ghsw-work='gh auth switch -u Brom-Buzzni && gh auth setup-git'
alias ghsw-personal='gh auth switch -u kbsowo14 && gh auth setup-git'
```

사용법:

```bash
ghsw-work       # 회사 계정으로 전환
ghsw-personal   # 개인 계정으로 전환
```

이렇게 하면 `gh auth switch` + credential 갱신이 한 번에 처리되어 `git pull`이 바로 동작합니다.