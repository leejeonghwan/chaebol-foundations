# 배포 계획

## 현재 단계 (정적 사이트)

- `index.html` + `data/*.json` 뿐. 빌드 단계 없음.
- 종속성: Tailwind CDN + Chart.js CDN + Google Fonts. 모두 외부 CDN.

## 1단계: GitHub Pages (현재 진행)

레포 Settings → Pages:
- Source: `Deploy from a branch`
- Branch: `main`
- Folder: `/ (root)`

URL: `https://USERNAME.github.io/chaebol-foundations/`

**장점:** 무료 · HTTPS 자동 · CDN.
**제한:** 커스텀 도메인 붙이려면 별도 DNS 작업. 빌드/cron 안 됨.

## 2단계: EC2 이전

### 인스턴스 사양 권장 (정적 단계)

- t4g.nano (ARM) — 월 ~$3, 정적 사이트엔 충분
- 또는 t3.micro (프리티어)

### Nginx 셋업

```bash
sudo apt update && sudo apt install -y nginx git
sudo mkdir -p /var/www/chaebol-foundations
sudo chown -R $USER:$USER /var/www/chaebol-foundations
cd /var/www
git clone https://github.com/USERNAME/chaebol-foundations.git
```

`/etc/nginx/sites-available/chaebol-foundations`:

```nginx
server {
    listen 80;
    server_name foundations.example.com;
    root /var/www/chaebol-foundations;
    index index.html;

    location / { try_files $uri $uri/ =404; }

    # JSON에 약한 캐시 (갱신 빈도 고려)
    location ~* \.json$ {
        add_header Cache-Control "public, max-age=300";
    }

    # HTML도 짧게
    location ~* \.html$ {
        add_header Cache-Control "public, max-age=600";
    }

    gzip on;
    gzip_types application/json text/html text/css application/javascript;
}
```

```bash
sudo ln -s /etc/nginx/sites-available/chaebol-foundations /etc/nginx/sites-enabled/
sudo nginx -t && sudo systemctl reload nginx
```

### HTTPS (Let's Encrypt)

```bash
sudo apt install -y certbot python3-certbot-nginx
sudo certbot --nginx -d foundations.example.com
```

### 자동 배포 (간단 cron)

```bash
# /etc/cron.d/chaebol-foundations-pull
*/15 * * * * www-data cd /var/www/chaebol-foundations && git pull --quiet
```

또는 GitHub Actions → SSH로 rsync (취향).

## 3단계: 데이터 자동 수집 (예정)

`scripts/` 폴더에 cron으로 도는 수집기 추가.

### 후보 1: 국세청 공익법인 공시 크롤러

- `npoinfo.hometax.go.kr` 또는 `teht.hometax.go.kr` — JavaScript 렌더링. Playwright 필요.
- 모집단 232개 × 결산서류 → JSON 변환 → `data/structure.json` 등 갱신
- 빈도: 분기 1회

### 후보 2: 공정위 보도자료 모니터링

- RSS 또는 `https://www.ftc.go.kr/www/selectBbsNttList.do?key=12&bordCd=3` 스크래핑
- 새 「비영리법인 운영현황」 발표 감지 → 슬랙·이메일 알림

### 후보 3: CEO스코어 보도 모니터링

- 「공익법인 사업수행비용 조사」 키워드 알림
- 새 발표 → 어시스턴트가 ratio.json 초안 만들어 PR 생성

## 4단계: Node 백엔드 (가능)

데이터가 자주 바뀌고 API가 필요해지면:

```
chaebol-foundations/
├── public/         # 정적 파일 (index.html, data/)
├── server/
│   ├── index.js    # Express
│   ├── routes/
│   └── jobs/       # cron (node-cron 또는 BullMQ)
└── package.json
```

PM2 또는 systemd로 띄움:

```bash
pm2 start server/index.js --name foundations
pm2 save
pm2 startup
```

Nginx는 reverse proxy로:

```nginx
location /api/ {
    proxy_pass http://127.0.0.1:3000/;
    proxy_set_header Host $host;
}
```

## 보안 체크리스트

- [ ] SSH 키 인증만 (비밀번호 비활성)
- [ ] UFW 또는 보안그룹: 22 / 80 / 443만 오픈
- [ ] `unattended-upgrades`
- [ ] Nginx access log 모니터링
- [ ] 백업: data/ 폴더는 git에 있으므로 자동, 추가 결산서 원본 다운로드 보관 시 S3 또는 EBS 스냅샷

## 비용 추정 (1년 정적 단계)

- EC2 t4g.nano: ~$36
- EBS gp3 8GB: ~$8
- 도메인: $12~
- 트래픽: 무료 한도 내 가능성 큼
- **합계: 연 $60 안팎**
