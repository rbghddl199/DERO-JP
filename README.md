# DERO — デスク環境シミュレーター

**実測スペックで検証してから買う、3Dデスクセットアップ。** 製品の実寸（mm）・VESA規格・耐荷重を
スペックDBとして持ち、購入前にレイアウト・互換性・予算を3D上で検証する。AIはイメージ図を描くだけで、
判定はすべてスペックアルゴリズムが行う。

- 運用中: <https://www.dero.life>
- チームプロジェクト（2026.07 – 2026.08）— チーム ブランチーズ

## 主な機能

- **3D配置シミュレーター** — 実測スケール固定（サイズ変更なし）、表面スナップ、衝突警告、カメラプリセット5種
- **互換性判定** — モニターアーム↔デスク（クランプ取付可否）、モニター↔アーム（VESA・耐荷重）、小物↔デスク（設置面積）。3段階判定＋数値による根拠＋代替製品の提示
- **リアルタイム見積もり** — 配置を変えるたびに合計金額と予算比を即時更新
- **AIイメージ図** — 自分のレイアウトのスクリーンショットを実写風に変換（gpt-image-2）
- **コミュニティ** — 公開レイアウトのフィード・テンプレートのコピー（リミックス、出典を自動表記）・投稿（Markdown＋レイアウトカード）
- **検索** — Elasticsearch + Nori、カテゴリ・オートコンプリート

## 技術スタック

| 領域 | スタック |
|---|---|
| Frontend | Next.js 16 (App Router) · React 19 · Three.js · TypeScript · Tailwind 4 · Zustand |
| Backend | Java 21 · Spring Boot 4.1 · JPA + QueryDSL · Spring Security (OAuth2) · Flyway |
| Data | PostgreSQL 18 · Redis 8 · Elasticsearch 9.4 (Nori) · AWS S3 |
| Infra | Docker Compose · nginx · Jenkins（blue-green無停止デプロイ）· Prometheus · Grafana · Kibana |

## リポジトリ構成

```
backend/dero-api/   Spring Boot APIサーバー
frontend/           Next.js + Three.js Webアプリ
docs/               企画・設計の正本（SPEC、REQUIREMENTS、ADR、デプロイ構成など）
infra/              nginx設定
observability/      ES・Kibana・Prometheus・Grafana・Filebeat設定
scripts/            デプロイ切替・アセットパイプラインのスクリプト
compose*.yaml       アプリ / データ / 監視 の3層Docker Compose
```

## クイックスタート（ローカル）

```bash
docker compose -f compose.data.yaml up -d        # postgres + redis

cd backend/dero-api
./gradlew bootRun --args='--spring.profiles.active=local --spring.datasource.url=jdbc:postgresql://localhost:5432/dero_db'

cd frontend
npm ci && npm run dev                            # http://localhost:3000
```

ビルド・デプロイ手順、環境変数の詳細: **[docs/context/deployment.md](docs/context/deployment.md)**

### IntelliJ コードスタイル（バックエンド、初回のみ）

`docs/GoogleStyle_custom.xml` がフォーマットの正本であり、IntelliJのコードスタイルスキームでもある。全員同じ手順で適用する。

1. `Settings/Preferences > Editor > Code Style`
2. 歯車アイコン → `Import Scheme > IntelliJ IDEA code style XML`
3. `docs/GoogleStyle_custom.xml` を選択してインポート
4. `GoogleStyle-custom` スキームを選んで適用

**2026-07-30以前にインポートした場合は再インポートすること** — 旧スキームはタブ設定ではないため、保存のたびにインデントが
戻ってしまう。以降は `Reformat Code`（⌘/Ctrl + Alt + L）で規約どおりに整形される。

## ドキュメント

| ドキュメント | 内容 |
|---|---|
| [docs/SPEC.md](docs/SPEC.md) | 機能チェックリスト |
| [docs/REQUIREMENTS.md](docs/REQUIREMENTS.md) | 要件・受け入れ基準 |
| [docs/USER-FLOWS.md](docs/USER-FLOWS.md) | ユーザーフロー |
| [docs/context/api-spec.md](docs/context/api-spec.md) | API仕様 |
| [docs/context/deployment.md](docs/context/deployment.md) | デプロイ構成の正本 |
| [docs/adr/](docs/adr/) | 技術的意思決定の記録（ADR） |

※ `docs/` 配下のドキュメントは韓国語で書かれています。

## 出典・ライセンス表記

- 依存ライブラリ: `frontend/package.json`、`backend/dero-api/build.gradle` に記載。各パッケージのライセンスに従う。
- 環境HDRI `frontend/public/assets/env/studio_small_09_1k.hdr`: [Poly Haven](https://polyhaven.com/a/studio_small_09)（CC0）
- 外部スキルの複製元: [mattpocock/skills](https://github.com/mattpocock/skills) (MIT) · [emilkowalski/skills](https://github.com/emilkowalski/skills) (MIT) · [Leonxlnx/taste-skill](https://github.com/Leonxlnx/taste-skill) (MIT) · [JuliusBrussee/caveman](https://github.com/JuliusBrussee/caveman) (Apache-2.0) · [D4Vinci/Scrapling](https://github.com/D4Vinci/Scrapling) (BSD-3)。出典一覧は `skills-lock.json`。
- 3Dモデル・サムネイル・サンプル写真・ホームのイラストは、チームが自作またはAI生成ツールで作成したアセットであり、第三者の著作物は含まない。
