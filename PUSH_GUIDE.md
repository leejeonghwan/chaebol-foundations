# GitHub Push 가이드

`chaebol-foundations/` 폴더에 git 초기화·첫 커밋까지 완료됐습니다. 남은 일은:

1. GitHub에 빈 레포 만들기
2. remote 연결 + push
3. GitHub Pages 활성화

본인 터미널에서 직접 실행하세요. **`USERNAME`은 본인 GitHub 사용자명으로 바꿔주세요.**

## 경로 A — `gh` CLI가 깔려 있다면 (가장 간단)

```bash
cd ~/work/harness/chaebol-foundations

# 1. GitHub에 public 레포 만들고 push까지 한 번에
gh repo create chaebol-foundations --public --source=. --remote=origin --push

# 2. GitHub Pages 활성화 (main 브랜치 / 루트)
#    GitHub API가 최근 build_type 필드를 필수로 요구하므로 --input 으로 JSON 직접 넘김
gh api -X POST repos/:owner/chaebol-foundations/pages \
  --input - <<'EOF'
{
  "build_type": "legacy",
  "source": { "branch": "main", "path": "/" }
}
EOF

# 3. URL 확인 (수 분 후 활성화)
gh repo view --web
```

`gh` 없으면 macOS: `brew install gh` → `gh auth login`.

## 경로 B — `gh` 없이 웹 + git만으로

### B-1. GitHub 웹에서 빈 레포 만들기

1. <https://github.com/new> 접속
2. Repository name: `chaebol-foundations`
3. Visibility: **Public**
4. README/Add gitignore/LICENSE는 **체크하지 말 것** (이미 로컬에 있음)
5. Create repository 클릭

### B-2. 터미널에서 remote 연결 + push

화면에 뜨는 "…or push an existing repository from the command line" 박스 내용 그대로:

```bash
cd ~/work/harness/chaebol-foundations
git remote add origin git@github.com:USERNAME/chaebol-foundations.git
# (HTTPS면) git remote add origin https://github.com/USERNAME/chaebol-foundations.git
git push -u origin main
```

### B-3. GitHub Pages 활성화

1. 레포 페이지 → **Settings** 탭
2. 왼쪽 사이드바 → **Pages**
3. **Source**: `Deploy from a branch`
4. **Branch**: `main` / Folder: `/ (root)` → **Save**
5. 1~3분 후 상단에 `Your site is live at https://USERNAME.github.io/chaebol-foundations/` 메시지

## 확인

```bash
# 로컬에서 fetch가 되는지 (위 경로 둘 다 끝낸 후)
open https://USERNAME.github.io/chaebol-foundations/
```

브라우저에서 데이터 로딩 잘 되면 끝.

## 자주 막히는 부분

- **`Permission denied (publickey)`** — SSH 키 등록 안 됨. HTTPS로 remote 다시 추가:
  ```bash
  git remote set-url origin https://github.com/USERNAME/chaebol-foundations.git
  ```
- **`error: src refspec main does not match any`** — 첫 커밋이 안 됐을 수도. `git log` 확인 후 다시 push.
- **GitHub Pages가 404** — Pages 활성화 후 1~3분 기다림. 빌드 상태는 Settings → Pages 페이지 상단에서 확인.

## 다음에 데이터만 갱신할 때

```bash
cd ~/work/harness/chaebol-foundations

# data/*.json 수정 후
git add data/
git commit -m "data: 2025 4분기 결산 갱신"
git push
```

GitHub Pages가 자동으로 1~2분 안에 갱신.

## EC2 이전 시점이 오면

`docs/DEPLOY.md` 참고. 같은 레포를 EC2에서 `git clone` → Nginx로 서빙하면 됩니다.
