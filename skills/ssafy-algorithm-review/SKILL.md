---
name: ssafy-algorithm-review
description: Analyze daily Java solution changes on the wh branch of YEOUL0520/STUDYWITHSSAFY_16 and generate consistent GitHub Issue drafts and PR descriptions for the requested date.
---

# SSAFY Algorithm Issue & PR Generator

## Purpose
Create consistent GitHub Issue drafts and Pull Request descriptions for daily algorithm-solution commits in `YEOUL0520/STUDYWITHSSAFY_16`, especially files under the `wh` branch.

## Trigger
Use this skill when the user asks things such as:
- `0907 변경 사항 확인`
- `오늘 커밋 기준 이슈 작성`
- `wh 브랜치 변경 확인하고 PR 작성`
- `0904 커밋 확인하고 PR, issue 텍스트 작성`
- `누락된 이슈 작성`

## Default Repository Context
- Repository: `YEOUL0520/STUDYWITHSSAFY_16`
- Base branch: `main`
- Head branch: `wh`
- Target files: `wh/MMDD_wh_<problem>.java`
- Author suffix in issue title: `채원형`

If the user explicitly names another repository, branch, date, or naming convention, use the user's values instead.

## Required Inputs
At minimum, infer or obtain:
1. Target date in `MMDD` format.
2. Repository and head/base branches.
3. GitHub access capable of reading the repository and files.

Do not ask for values already known from the current conversation or skill defaults.

## Workflow

### 1. Compare branches
Compare `main...wh` and inspect the changed-file list.

Important:
- The branch may be far behind `main` and old, unmerged files can reappear in the diff.
- Do **not** assume every file in the branch comparison belongs to the requested date.
- Filter by filename date first.

For a requested date `MMDD`, only treat files matching:

```text
wh/MMDD_wh_*.java
```

as daily issue candidates unless the user explicitly asks for a wider range.

### 2. Distinguish new files from unrelated historical changes
For the requested date:
- `added` Java files are normally new issue candidates.
- `modified` Java files are included only when the user asks for modifications/reviews or the modification is materially part of the requested day's work.
- Ignore unrelated `.gitignore`, old personal files, or files with different dates unless the user asks to include them.

### 3. Read each candidate file
Fetch the complete Java source from the `wh` branch.

From the source determine:
- problem number
- problem name
- problem constraints stated in comments
- actual algorithm used
- data structures used
- important implementation choices
- time/space complexity
- potential alternative approach

Do not invent problem details that are absent from the source. If comments are incomplete, describe only what can be supported by the implementation and known problem structure.

### 4. Check implementation accuracy before documenting
The Issue should explain the **intended/correct algorithm**, not blindly copy a bug.

When the committed code contains a clear defect:
- Do not silently present the bug as correct.
- Write the Issue around the correct intended approach when it is unambiguous.
- After the draft, briefly call out the implementation issue separately if it would affect submission correctness.

Examples of defects to flag:
- stale debug output
- wrong variable printed
- undefined variable
- missing visited update
- invalid Java collection type
- missing final sort/recalculation
- time-dependent BFS visited-state error

### 5. Generate one Issue draft per problem

#### Issue title

```text
MMDD_<problem-number>_<problem-name>_채원형
```

Examples:
```text
0907_1952_수영장_채원형
0907_4008_숫자 만들기_채원형
```

#### Issue body structure
Prefer this structure:

```markdown
# 문제명

문제 핵심 설명

## [제약 사항]

- 핵심 제약

## [입력]

<details>
<summary>입력 요약</summary>

입력 설명 또는 짧은 예시

</details>

## [출력]

<details>
<summary>출력 요약</summary>

짧은 예시

</details>

## 접근 방법

선택한 알고리즘과 이유

## 핵심 구현

### 구현 포인트 1

```java
...
```

### 구현 포인트 2

```java
...
```

## 복잡도

- 시간 복잡도: `...`
- 공간 복잡도: `...`

## 다른 아이디어

대안 접근법
```

Guidelines:
- Keep the explanation focused on algorithm review/study, not bug-tracker language.
- Use code snippets that are short and representative.
- Prefer corrected snippets when the source contains an obvious bug.
- Explain why a key condition or data structure matters.

### 6. Generate the daily PR text

#### PR title

```text
MMDD 알고리즘 문제 풀이 추가
```

#### PR body

```markdown
# MMDD 알고리즘 문제 풀이 추가

## 변경 사항

`wh` 브랜치에서 작업한 MMDD 알고리즘 문제 풀이를 추가했습니다.

### <problem-number> <problem-name>

- 핵심 접근법 1
- 핵심 접근법 2
- 사용 자료구조/알고리즘

## 주요 알고리즘 및 자료구조

- ...
- ...

## 추가 파일

- `wh/MMDD_wh_xxxx.java`
- ...

## 확인 사항

- [ ] 문제별 핵심 로직 확인
- [ ] 예제 및 제출 결과 확인
- [ ] 코드 리뷰 후 `main` 브랜치 병합
```

Only mention files from the requested date unless the user asks for a multi-day PR.

### 7. Handle `main` not containing previous daily work
If `main...wh` shows prior-date files together with today's files:
- State briefly that previous unmerged work is still present in the comparison.
- For a daily request, keep the Issue/PR text scoped to the requested date.
- Warn that an actual PR from `wh` to `main` may include those previous files unless prior PRs are merged or branch history is reorganized.

### 8. Actual GitHub writes
If the user asks to create Issues or a PR, attempt the GitHub write only when the connector has access.

Before creating multiple Issues:
- Search for existing issues with the same title to avoid duplicates when needed.

If GitHub returns:

```text
403 Resource not accessible by integration
```

explain that the GitHub App lacks write access to that repository. Do not repeatedly retry every issue when the same permission problem will block all writes.

## Output Modes

### User asks for text only
Return:
1. issue title + complete issue body for each problem
2. PR title + complete PR body

### User asks for actual GitHub creation
Attempt creation and report the resulting issue/PR numbers and links if successful.

### User asks only to inspect changes
Return a compact summary of matching daily files and algorithms; do not generate full drafts unless requested.

## Quality Checklist
Before finalizing:
- [ ] Requested date is correct.
- [ ] Only matching daily files are included.
- [ ] Problem number/name match the source.
- [ ] Algorithm description matches actual implementation or clearly corrected intent.
- [ ] Complexity is plausible.
- [ ] Issue title follows `MMDD_번호_문제명_채원형`.
- [ ] PR title follows `MMDD 알고리즘 문제 풀이 추가`.
- [ ] Old unmerged files are not accidentally included in a daily summary.
- [ ] Any correctness-affecting implementation defect is called out.
