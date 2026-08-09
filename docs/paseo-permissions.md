# Paseo Provider Permission Modes

이 문서는 Paseo에서 Product Owner, Developer, Reviewer 등의 하위 에이전트를 실행할 때
Provider별 권한 모드와 승인 처리 방법을 정리한 운영 가이드다.

## Provider별 모드

### Claude

Paseo에서 확인된 모드:

- `plan`
- `default`
- `acceptEdits`
- `auto`
- `bypassPermissions`

생성 예시:

```bash
paseo run --provider claude/claude-sonnet-5 --mode bypassPermissions \
  --workspace <workspace-id> --background "<task prompt>"
```

### Codex

Paseo에서 확인된 모드:

- `auto`
- `auto-review`
- `full-access`

Codex Provider에는 `bypassPermissions` 모드가 노출되지 않는다. `full-access`가 Paseo에서
사용할 수 있는 가장 높은 권한 모드다.

생성 예시:

```bash
paseo run --provider codex/gpt-5.6-luna --mode full-access \
  --workspace <workspace-id> --background "<task prompt>"
```

실행 중인 에이전트의 모드 변경:

```bash
paseo agent mode <agent-id> full-access
```

## 권한 요청 일괄 승인

특정 에이전트의 현재 대기 중인 권한 요청을 한 번에 허용할 수 있다.

```bash
paseo permit allow --all <agent-id>
```

이 명령은 해당 에이전트의 현재 대기 요청을 처리한다. 미래의 모든 에이전트나 모든
워크스페이스에 대한 영구적인 bypass 설정은 아니다.

## Codex CLI 직접 실행

Paseo를 거치지 않고 Codex CLI를 직접 실행할 때는 다음 옵션을 사용할 수 있다.

일반 무승인 실행:

```bash
codex --ask-for-approval never --sandbox danger-full-access
```

샌드박스와 확인을 모두 우회하는 위험한 실행:

```bash
codex --dangerously-bypass-approvals-and-sandbox
```

후자는 외부 샌드박스가 이미 작업을 격리하는 자동화 환경에서만 사용한다.

## 설정 주의사항

`~/.codex/config.toml`의 설정과 Paseo 하위 에이전트의 실행 설정은 다를 수 있다.
Paseo가 Codex를 실행할 때 `approval_policy=on-request`와 `approvals_reviewer=user`를
주입하면, Codex 설정 파일에 `danger-full-access`가 있어도 하위 에이전트에 승인 카드가
나타날 수 있다.

따라서 문제를 진단할 때는 다음을 각각 확인한다.

1. `paseo provider ls --json`의 Provider별 모드 목록
2. `paseo agent inspect <agent-id>`의 현재 모드와 대기 권한
3. `paseo permit ls`의 현재 대기 요청
4. Paseo를 통하지 않은 경우 `codex --help`의 CLI 옵션

## 안전 원칙

- 읽기·테스트·태스크 문서 작성은 가능한 경우 `auto` 또는 `full-access`로 처리한다.
- 외부 네트워크, 삭제, 운영 데이터 변경은 별도로 검토한다.
- `--dangerously-bypass-approvals-and-sandbox`는 일반 프로젝트 기본값으로 사용하지 않는다.
- 권한 정책은 Provider가 제공하는 모드와 Paseo의 안전 정책을 넘어서 프롬프트만으로 바꿀 수 없다.

## 확인 정보

- 확인 도구: `paseo --help`, `paseo run --help`, `paseo provider ls --json`, `codex --help`
- 확인 환경: Paseo local daemon / Codex Provider
- 확인일: 2026-08-09
