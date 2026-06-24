# OpenClaw AntFarm RSS News Video Pipeline Prompt - Kimi K2.5 Version

> 用途：把本文档中的「总提示词」完整提交给 OpenClaw，让它在 `snarktank/antfarm` 框架中建立一个可运行、可验证、可重试的多 Agent RSS 新闻视频生产工作流。

## 1. 已确认目标

- 平台：OpenClaw + AntFarm。
- 工作流名称：`rss-daily-video-pipeline`。
- 触发方式：不设置固定运行时间；由手动命令或外部 cron 触发。
- 输入：运行时由用户直接提供一个 `RSS_URL`。
- 新闻范围：取过去 24 小时内最新最多 10 条新闻；不足 10 条时处理实际数量，不视为失败。
- 输出：一个完整 9:16 竖屏 MP4，不输出 10 个独立发布视频。
- 视频长度：目标 8-12 分钟；若新闻数量不足导致较短，必须在运行报告说明。
- 内容风格：中文热点解读口吻。
- 发布策略：第一版只生成视频、发布包、运行报告和数据库记录；用户观看后手动发布。后续只预留自动发布和后台数据抓取扩展点。
- 数据库：第一版使用独立业务 SQLite，未来保留迁移 PostgreSQL 的结构。
- 图片：优先使用 RSS / 原文图片；没有可用图片或原图下载失败时才调用 Wavespeed.ai Nano Banana Pro。
- 语音：调用 Voicebox.sh API，voice profile 固定为 `Pai-1`。
- 字幕：中文字幕必须烧录到视频画面。
- 清理策略：最终视频 QA 通过后删除图片、音频、字幕、片段视频、缓存、临时 JSON 等中间文件；只保留最终交付物、运行报告、发布包和数据库记录。

## 2. OpenRouter 模型确认

OpenRouter API model slug 固定为：

```text
moonshotai/kimi-k2.5
```

OpenClaw 配置中引用 OpenRouter 模型时使用：

```text
openrouter/moonshotai/kimi-k2.5
```

本文档中所有 OpenRouter 调用都必须使用 Kimi K2.5。不得默认改用 `openrouter/auto`、`kimi-latest`、其他 Kimi 版本、Claude、Gemini、DeepSeek 或任何更强/更贵 fallback 模型。

如果后续你要改成 Nitro、latest 或其他模型，再单独调整；当前版本只锁定标准 Kimi K2.5。

## 3. 外部系统与环境变量

OpenClaw / AntFarm 在建立系统前必须检查以下变量或配置。缺失时必须 fail fast，并在 run report 和 agent log 中记录明确原因。

```env
# Runtime input
RUNTIME_TASK_REQUIRES_RSS_URL=true
RSS_URL=<provided_by_user_when_running_antfarm_workflow>
PIPELINE_TIMEZONE=Asia/Taipei
NEWS_LOOKBACK_HOURS=24
MAX_NEWS_ITEMS=10

# Output and database
OUTPUT_ROOT=/Volumes/2ndHOME/Video_Publication/output
DB_DIR=/Volumes/2ndHOME/Video_Publication/DB
BUSINESS_DB_PATH=/Volumes/2ndHOME/Video_Publication/DB/rss_video_pipeline.sqlite

# OpenRouter LLM - fixed to Kimi K2.5
SUMMARY_PROVIDER=openrouter
SCRIPT_PROVIDER=openrouter
OPENROUTER_API_KEY=<already_set_in_environment_or_openclaw_secret>
OPENROUTER_BASE_URL=https://openrouter.ai/api/v1
OPENROUTER_MODEL_SLUG=moonshotai/kimi-k2.5
OPENCLAW_OPENROUTER_MODEL_ID=openrouter/moonshotai/kimi-k2.5
SUMMARY_MODEL=moonshotai/kimi-k2.5
SCRIPT_MODEL=moonshotai/kimi-k2.5
ALLOW_NON_KIMI_MODEL_FALLBACK=false

# Image generation
IMAGE_PROVIDER=wavespeed_ai
WAVESPEED_API_KEY=<already_set_in_environment_or_openclaw_secret>
WAVESPEED_TEXT_TO_IMAGE_ENDPOINT=https://api.wavespeed.ai/api/v3/google/nano-banana-pro/text-to-image
WAVESPEED_RESULT_ENDPOINT=https://api.wavespeed.ai/api/v3/predictions/{request_id}/result
IMAGE_MODEL=google/nano-banana-pro/text-to-image
IMAGE_ASPECT_RATIO=9:16
IMAGE_RESOLUTION=1k
IMAGE_OUTPUT_FORMAT=png
IMAGE_ENABLE_SYNC_MODE=false
IMAGE_ENABLE_BASE64_OUTPUT=false

# Voice generation
VOICE_PROVIDER=voicebox
VOICEBOX_BASE_URL=http://127.0.0.1:17493
VOICEBOX_GENERATE_ENDPOINT=/generate
VOICEBOX_PROFILE_ID=Pai-1
VOICEBOX_LANGUAGE=zh

# Video rendering
VIDEO_WIDTH=1080
VIDEO_HEIGHT=1920
VIDEO_FPS=30
VIDEO_FORMAT=mp4
VIDEO_CODEC=h264
AUDIO_CODEC=aac
TARGET_VIDEO_MINUTES_MIN=8
TARGET_VIDEO_MINUTES_MAX=12
TARGET_CLIP_SECONDS_MIN=45
TARGET_CLIP_SECONDS_MAX=60
```

运行示例：

```bash
antfarm workflow run rss-daily-video-pipeline "RSS_URL=https://example.com/feed.xml"
```

## 4. 总提示词

```text
请在 OpenClaw 中基于 snarktank/antfarm 建立一个自定义多 Agent 工作流。

工作流名称：
rss-daily-video-pipeline

核心目标：
从用户运行 workflow 时输入的 RSS_URL 读取过去 24 小时内最新最多 10 条新闻，抽取正文、来源、发布时间、canonical_url、时间、地点、人物、机构和图片候选，使用 OpenRouter 的 Kimi K2.5 生成中文热点解读摘要与播报稿，使用 Voicebox.sh 的 Pai-1 声音生成语音，为每条新闻生成一个 9:16 竖屏片段，最后合并为一个 8-12 分钟完整视频。第一版不自动发布，只生成最终视频、发布包、运行报告和 SQLite 业务数据库记录。最终视频 QA 通过后删除所有中间媒体文件。

必须遵守 AntFarm 框架：
1. 在 AntFarm 的 `workflows/rss-daily-video-pipeline/` 下创建 `workflow.yml`。
2. 为每个 agent 建立 `agents/<agent-id>/AGENTS.md`、`SOUL.md`、`IDENTITY.md`。
3. `workflow.yml` 必须使用 AntFarm 支持的 `agents`、`steps`、`expects`、`max_retries`、`on_fail`、`type: loop`。
4. 每个 step 输出必须包含 `STATUS: done`、`STATUS: retry` 或 `STATUS: failed`，并使用 `KEY: value` 形式传递后续变量。
5. loop 必须由 planner 输出 `STORIES_JSON` 驱动，每条新闻一个 story，最多 10 条。
6. 不要建立普通单体脚本代替 AntFarm workflow。可以写工具脚本，但必须由 AntFarm agent step 调用。
7. AntFarm 自身 SQLite 只用于 workflow 状态；业务数据必须写入独立 SQLite：`/Volumes/2ndHOME/Video_Publication/DB/rss_video_pipeline.sqlite`。
8. 不设置固定运行时间；工作流必须可由手动命令或外部 cron 触发。

目录要求：
- 配置目录：`/Volumes/2ndHOME/Video_Publication/config`
- 每日输出目录：`/Volumes/2ndHOME/Video_Publication/output/YYYY-MM-DD`
- 数据库目录：`/Volumes/2ndHOME/Video_Publication/DB`
- 最终视频：`/Volumes/2ndHOME/Video_Publication/output/YYYY-MM-DD/final/rss-news-YYYY-MM-DD.mp4`
- 发布包：`/Volumes/2ndHOME/Video_Publication/output/YYYY-MM-DD/package/publish-package.md`
- 运行报告：`/Volumes/2ndHOME/Video_Publication/output/YYYY-MM-DD/run-report.md`

保留文件：
- 最终完整 MP4
- 发布包 Markdown
- 运行报告 Markdown
- SQLite 业务数据库

QA 通过后必须删除的中间文件：
- 下载的新闻图片副本
- Wavespeed.ai 生成图片
- Voicebox 语音文件
- 临时字幕文件
- 单条新闻视频片段
- 渲染缓存
- 临时 JSON 和 working files

如果最终视频合并失败或 QA 不通过，不得清理中间文件，必须保留用于排查。

Agent 设计：

1. planner-agent
职责：
- 检查任务文本是否包含 `RSS_URL=...`。
- 校验环境变量或 secret：`OPENROUTER_API_KEY`、`SUMMARY_MODEL=moonshotai/kimi-k2.5`、`SCRIPT_MODEL=moonshotai/kimi-k2.5`、`WAVESPEED_API_KEY`、`VOICEBOX_BASE_URL`。
- 确认 `ALLOW_NON_KIMI_MODEL_FALLBACK=false`。
- 创建每日输出目录。
- 初始化 `daily_runs` 记录。
- 生成 `STORIES_JSON`，每条新闻一个 story。
输出：
STATUS: done
RUN_ID: <id>
RUN_DATE: <YYYY-MM-DD>
OUTPUT_DIR: <path>
BUSINESS_DB_PATH: <path>
RSS_URL: <rss url from task>
STORIES_JSON: <array>

2. rss-agent
职责：
- 从 planner-agent 输出读取 RSS_URL。
- 校验 RSS_URL 是有效 http/https URL。
- 读取过去 24 小时内最新最多 10 条新闻。
- 去重：优先按 canonical_url，其次按 title + published_at hash。
- 写入 `rss_sources`、`news_items`。
- 如果不足 10 条，继续处理实际数量，不视为失败。
输出：
STATUS: done
NEWS_COUNT: <number>
NEWS_ITEMS_JSON: <array>

3. article-agent
职责：
- 作为 loop step 逐条处理新闻。
- 抓取原文 HTML。
- 使用稳定库抽取正文，不要用脆弱字符串切割。
- 抽取作者、来源、发布时间、canonical_url。
- 抽取图片候选：RSS media、OpenGraph image、Twitter image、正文首图。
- 特别记录时间、地点、人物、机构的原始线索，但不得编造。
- 写入 `articles`。
输出：
STATUS: done
ARTICLE_ID: <id>
ARTICLE_TEXT_PATH: <path or cleaned metadata>
IMAGE_CANDIDATES_JSON: <array>
FACT_SOURCE_NOTES: <short notes>

4. script-agent
职责：
- 作为 loop step 逐条处理新闻。
- 必须通过 OpenRouter 调用 Kimi K2.5。
- OpenRouter API key 必须从 `OPENROUTER_API_KEY` 读取，不得写入代码、workflow.yml、日志或数据库明文。
- OpenRouter base URL 使用 `OPENROUTER_BASE_URL`，默认 `https://openrouter.ai/api/v1`。
- `SUMMARY_MODEL` 与 `SCRIPT_MODEL` 必须都是 `moonshotai/kimi-k2.5`。
- 不允许默认使用 `openrouter/auto`、`kimi-latest` 或非 Kimi K2.5 fallback。
- 生成：
  - 约 300 字中文摘要
  - 45-60 秒热点解读口吻播报稿
  - 标题钩子
  - 关键词标签
  - 结构化事实字段：time_entities、location_entities、person_entities、organization_entities
- 摘要必须特别注意时间、地点、人物、机构。
- 若原文没有明确时间、地点、人物或机构，字段写 `unknown`，不得猜测。
- 如果 RSS 时间和原文发布时间不同，两者都保存。
- 如 Kimi K2.5 输出不合格，最多重试 2 次；仍失败则输出 `STATUS: retry` 或 `STATUS: failed`，不得自动切换到其他模型。
输出：
STATUS: done
SUMMARY_ID: <id>
SUMMARY_TEXT: <text>
NARRATION_TEXT: <text>
TITLE_HOOK: <text>
TAGS_JSON: <array>
FACTS_JSON: <object>
MODEL_USED: moonshotai/kimi-k2.5
FALLBACK_USED: false

5. image-agent
职责：
- 作为 loop step 逐条处理新闻。
- 图片优先级：
  1. RSS media enclosure / thumbnail
  2. 原文 OpenGraph image
  3. 原文 Twitter card image
  4. 原文正文首图
  5. 以上都不可用或下载失败才调用 Wavespeed.ai
- 使用原图时必须记录 original_image_url、source_article_url、image_source_type、downloaded_at、license_note。
- 如果图片不是 9:16，可以裁切、模糊背景或居中适配，但不得改变事实含义。
- 调用 Wavespeed.ai 时：
  - 使用 `WAVESPEED_API_KEY`。
  - POST `https://api.wavespeed.ai/api/v3/google/nano-banana-pro/text-to-image`。
  - Header: `Authorization: Bearer ${WAVESPEED_API_KEY}`。
  - 参数至少包含 prompt、aspect_ratio=9:16、resolution=1k、output_format=png、enable_sync_mode=false、enable_base64_output=false。
  - 根据返回 request id 轮询 `/api/v3/predictions/{request_id}/result`，直到 completed 或 failed。
- 生成图必须是新闻概念图 / 编辑性插图 / 产业示意图，不能假装是真实新闻现场照片。
- 不得生成假新闻截图、假文件、假 Logo、未经来源支持的真实人物肖像。
- 图片中避免可读文字。
- 写入 `media_assets`。
图片生成 prompt 模板：
Create a vertical 9:16 editorial news explainer image for a mobile video.
Topic: <news_topic>
Core event: <core_event>
Time context: <time_entities or unknown>
Location context: <location_entities or unknown>
People involved: <person_entities or unknown>
Organizations involved: <organization_entities or unknown>
Industry context: <industry_context>
Visual direction: realistic editorial illustration, cinematic but factual, clean composition, strong central subject, suitable for a Chinese-language news explainer video.
Avoid: readable text, captions, logos, trademarks, fake screenshots, fake documents, fake news websites, identifiable public figures not sourced from the article image.
Output: one high-quality vertical image, 9:16.
输出：
STATUS: done
USABLE_IMAGE_PATH: <path>
IMAGE_PROVIDER: source|wavespeed_ai
IMAGE_MODEL: source|google/nano-banana-pro/text-to-image
IMAGE_PROMPT: <prompt or empty>
WAVESPEED_REQUEST_ID: <id or empty>

6. voice-agent
职责：
- 作为 loop step 逐条处理新闻。
- 调用 Voicebox.sh REST API。
- 默认 endpoint：`${VOICEBOX_BASE_URL}/generate`。
- 请求包含 text、profile_id=Pai-1、language=zh。
- 输入文本使用 `NARRATION_TEXT`。
- 校验音频存在、可播放、时长接近 45-60 秒；允许因语速产生合理偏差。
- 写入 `media_assets`。
输出：
STATUS: done
AUDIO_PATH: <path>
VOICE_PROFILE_ID: Pai-1
AUDIO_DURATION_SECONDS: <number>

7. render-agent
职责：
- 作为 loop step 逐条处理新闻片段，并在 loop 后合并最终视频。
- 每条片段使用一张图片 + 语音 + 内嵌字幕。
- 视频规格：1080x1920、30fps、H.264 MP4、AAC audio。
- 每条片段画面：
  - 上方：新闻序号和标题钩子
  - 中间：图片，轻微推拉或平移动态
  - 下方：大字号中文字幕
  - 角落：来源和日期
- 字幕必须烧录进视频。
- 所有片段完成后合并：片头、新闻 1 到 N、片尾 / 总结。
- 最终长度目标 8-12 分钟；若新闻不足 10 条则允许较短，并在报告说明。
输出：
STATUS: done
CLIP_PATH: <path for loop item>
FINAL_VIDEO_PATH: <path after merge step>
FINAL_DURATION_SECONDS: <number>

8. package-agent
职责：
- 生成手动发布包，不自动上传平台。
- 发布包包含：
  - 建议标题 3 个
  - 简介
  - 标签
  - 新闻来源清单
  - 图片来源清单
  - 手动发布检查表
  - 后续填写发布 URL 的位置
  - 未来自动发布字段占位
输出：
STATUS: done
PUBLISH_PACKAGE_PATH: <path>

9. archive-agent
职责：
- 确保所有 RSS、正文、摘要、事实字段、图片、语音、字幕、片段、最终视频、发布包路径或清理状态写入业务 SQLite。
- 不把业务数据写入 AntFarm 自身状态库。
输出：
STATUS: done
ARCHIVE_COMPLETE: true

10. qa-agent
职责：
- 使用 verification role。
- 不修改文件。
- 检查：
  - RSS 条目数量与实际处理数量一致。
  - 每条新闻有正文或明确失败原因。
  - 摘要保留或明确标记时间、地点、人物、机构。
  - `MODEL_USED` 必须是 `moonshotai/kimi-k2.5`。
  - `FALLBACK_USED` 必须是 false，除非用户后续明确改配置。
  - 每条新闻有 usable image。
  - Wavespeed.ai 只在无可用原图或原图下载失败时调用。
  - 每条新闻有语音。
  - 每条新闻有片段视频。
  - 最终 MP4 存在、可播放、9:16、带音频、带内嵌字幕。
  - 发布包存在。
  - 数据库记录完整。
- 通过时输出 STATUS: done；不通过时输出 STATUS: retry 和 ISSUES。
输出：
STATUS: done
QA_REPORT: <summary>

11. cleanup-agent
职责：
- 仅在 qa-agent 通过后运行。
- 删除所有中间媒体和临时文件。
- 不删除最终 MP4、发布包、运行报告、SQLite 数据库。
- 更新 `media_assets.cleaned_up=true`、`cleaned_up_at`。
输出：
STATUS: done
CLEANUP_COMPLETE: true

推荐 workflow.yml 步骤：
1. init_run -> planner-agent
2. ingest_rss -> rss-agent
3. process_news_items -> type: loop over stories
   - retrieve_article -> article-agent
   - create_summary_script -> script-agent
   - prepare_image -> image-agent
   - generate_voice -> voice-agent
   - render_clip -> render-agent
4. merge_video -> render-agent
5. create_publish_package -> package-agent
6. archive_database -> archive-agent
7. verify_quality -> qa-agent
   - on failure retry the failed producing step when possible
   - after retry exhaustion escalate_to human
8. cleanup_intermediate_files -> cleanup-agent

业务数据库 schema 至少包含：

rss_sources:
- id
- name
- url
- timezone
- source_type
- enabled
- created_at
- updated_at

daily_runs:
- id
- run_date
- started_at
- completed_at
- trigger_type
- status
- output_dir
- final_video_id
- cleanup_status
- error_summary

news_items:
- id
- run_id
- rss_source_id
- title
- link
- canonical_url
- rss_published_at
- fetched_at
- dedupe_hash
- processing_status
- failure_reason

articles:
- id
- news_item_id
- source_name
- author
- article_published_at
- article_text
- article_text_hash
- image_candidates_json
- extraction_status

summaries:
- id
- news_item_id
- summary_text
- narration_text
- title_hook
- tags_json
- time_entities_json
- location_entities_json
- person_entities_json
- organization_entities_json
- missing_fact_fields_json
- fact_confidence
- model_name
- prompt_version
- input_token_estimate
- output_token_estimate
- retry_count
- fallback_used

media_assets:
- id
- news_item_id
- asset_type
- file_path
- original_url
- source_article_url
- image_source_type
- image_provider
- image_model
- image_prompt
- wavespeed_request_id
- voice_provider
- voice_profile_id
- duration_seconds
- generated_at
- downloaded_at
- cleaned_up
- cleaned_up_at

final_videos:
- id
- run_id
- file_path
- width
- height
- fps
- duration_seconds
- format
- qa_status
- created_at

publish_records:
- id
- final_video_id
- platform
- manual_or_auto
- publish_url
- published_at
- status
- notes

performance_metrics:
- id
- publish_record_id
- collected_at
- views
- likes
- comments
- shares
- favorites
- completion_rate
- avg_watch_seconds
- raw_metrics_json

agent_logs:
- id
- run_id
- agent_id
- step_id
- news_item_id
- status
- message
- model_name
- external_service
- request_id
- started_at
- completed_at
- error

runtime_inputs:
- id
- run_id
- key
- value_hash
- value_redacted
- created_at

实现要求：
- 用 Python 或 Node.js 编写工具脚本均可，但必须服务于 AntFarm workflow。
- RSS 和网页正文抽取应使用稳定库，不要用脆弱的字符串切割。
- 视频渲染优先使用 ffmpeg，因为本流程是一张图 + 音频 + 字幕的高效率生产线，不需要复杂视频引擎。
- 所有外部 API 调用必须有 timeout、retry、错误记录。
- 所有 secret 不得写入代码、workflow.yml、日志或数据库明文。
- RSS_URL 是运行时用户输入，不需要固定 RSS 配置文件；但必须写入 `rss_sources` 和 `daily_runs` 的关联记录。
- 对新闻正文与图片必须记录来源，便于手动发布前审核。
- 第一版不要自动发布到任何平台。
- 后续自动发布与后台数据抓取只保留数据库字段和 agent 扩展点。

验收标准：
1. `antfarm workflow install rss-daily-video-pipeline` 可以安装 workflow。
2. `antfarm workflow run rss-daily-video-pipeline "RSS_URL=https://example.com/feed.xml"` 可以启动一次生成。
3. 运行完成后，每日输出目录存在最终 MP4、发布包、运行报告。
4. SQLite 可查询每条新闻从 RSS 到最终视频的完整链路。
5. 最终视频为 9:16，带音频和内嵌中文字幕。
6. 摘要与播报稿必须使用 `moonshotai/kimi-k2.5`。
7. 不得自动切换到非 Kimi K2.5 模型。
8. QA 通过后中间图片、音频、字幕、片段视频和缓存已删除。
9. 删除中间文件后，数据库仍保留来源、摘要、事实字段、图片来源、语音状态、片段状态和清理状态。
10. 任一 agent 失败必须记录原因并触发 retry 或 escalate，不允许静默失败。

请先生成 workflow 目录结构、workflow.yml、agent 指令文件、配置模板、数据库初始化脚本、工具脚本和 README。生成后执行一次 dry-run 或最小样例测试，确认 AntFarm workflow 结构可被安装。
```

## 5. 给 OpenClaw 的简短提交方式

如果希望用更短的方式提交，可以复制下面这段，并附上本文档：

```text
请读取我提供的 Markdown 文档，严格按照其中的「总提示词」在 OpenClaw 的 AntFarm 中建立 `rss-daily-video-pipeline` 多 Agent 工作流。不要简化为普通脚本。所有 OpenRouter 模型必须固定使用 Kimi K2.5：raw OpenRouter slug 为 `moonshotai/kimi-k2.5`，OpenClaw model id 为 `openrouter/moonshotai/kimi-k2.5`。先检查运行时输入、环境变量、外部 API 和 AntFarm 版本，再生成 workflow.yml、agent workspace 文件、数据库 schema、工具脚本和 README。完成后做 dry-run 验证。
```

## 6. 你需要在系统中准备的内容

- OpenClaw v2026.2.9+。
- AntFarm 已安装，或允许 OpenClaw 安装 `github.com/snarktank/antfarm`。
- Node.js >= 22。
- ffmpeg 可用。
- 运行 AntFarm workflow 时在任务文本中提供 `RSS_URL=...`。
- `OPENROUTER_API_KEY` 已作为环境变量或 OpenClaw secret 可读。
- `SUMMARY_MODEL=moonshotai/kimi-k2.5`。
- `SCRIPT_MODEL=moonshotai/kimi-k2.5`。
- 如需配置 OpenClaw agent model，使用 `openrouter/moonshotai/kimi-k2.5`。
- `WAVESPEED_API_KEY` 已作为环境变量或 OpenClaw secret 可读。
- Voicebox.sh 本地服务已启动，默认 `http://127.0.0.1:17493`。
- Voicebox 中存在可用 profile：`Pai-1`。
- `/Volumes/2ndHOME/Video_Publication/output` 与 `/Volumes/2ndHOME/Video_Publication/DB` 可写。

## 7. 本次检查结论

- 原文档的主流程、loop、11 个 agent、SQLite 业务库、发布包、QA、cleanup、运行时 RSS 输入都已覆盖。
- 已补强 AntFarm loop 需要 `STORIES_JSON` 的要求。
- 已将所有 OpenRouter 模型固定为 Kimi K2.5。
- 已移除非 Kimi fallback 的默认路径，避免运行时自动切换到其他模型。
- 已明确 OpenRouter raw slug 与 OpenClaw model id 的差异：
  - OpenRouter API：`moonshotai/kimi-k2.5`
  - OpenClaw：`openrouter/moonshotai/kimi-k2.5`

## 8. 部署后修复记录 (2026-06-24)

### 8.1 Cron Job Delivery 配置修复

**问题现象：**
Agent cron jobs 执行失败，错误信息：
```
Channel is required when multiple channels are configured: discord, openclaw-weixin
Set delivery.channel explicitly or use a main session with a previous channel.
```

**根本原因：**
`antfarm workflow ensure-crons` 命令创建的 cron jobs 默认使用 `"channel": "last"`，但 isolated sessions 没有 "last" channel 上下文。

**修复方案：**
将所有 agent cron jobs 的 delivery 配置改为 `"mode": "none"`：

```json
{
  "delivery": {
    "mode": "none"
  }
}
```

**修复命令：**
```bash
# 重新生成所有 agent cron jobs
node ~/.openclaw/workspace/antfarm/dist/cli/cli.js workflow ensure-crons rss-daily-video-pipeline

# 然后手动更新每个 cron job 的 delivery.mode 为 "none"
```

### 8.2 Email 通知功能添加

**新增功能：**
Package agent 完成后自动发送 email 通知，包含最终视频和发布包。

**配置要求：**
- 发送者：`paipeiming@gmail.com` (通过 GOG_ACCOUNT 设置)
- 接收者：`peiming.pai@gmail.com`
- 附件：最终 MP4 视频 + publish-package.md

**前置条件：**
```bash
# 1. 确保 gog CLI 已配置 Gmail OAuth
gog status

# 2. 验证 credentials 文件存在
ls ~/Library/Application\ Support/gogcli/credentials.json

# 3. 设置环境变量
export GOG_ACCOUNT=paipeiming@gmail.com
```

**Package Agent 指令更新：**
在 `agents/package/AGENTS.md` 中添加 email 发送步骤：

```bash
# 获取最终视频路径并发送邮件
FINAL_VIDEO=$(ls /Volumes/2ndHOME/Video_Publication/output/YYYY-MM-DD/*.mp4 | head -1)
PUBLISH_PACKAGE=/Volumes/2ndHOME/Video_Publication/output/YYYY-MM-DD/package/publish-package.md

GOG_ACCOUNT=paipeiming@gmail.com gog gmail send \
  --to peiming.pai@gmail.com \
  --subject "[RSS Video] Daily AI News - $(date +%Y-%m-%d)" \
  --body "RSS Daily Video Pipeline completed.\n\nFinal video: $FINAL_VIDEO\nPublish package: $PUBLISH_PACKAGE\n\nPlease upload to YouTube Shorts, TikTok, and Instagram Reels." \
  --attach "$FINAL_VIDEO" \
  --attach "$PUBLISH_PACKAGE"
```

**注意：** 如果 gog 不可用或邮件发送失败，记录错误但不阻塞 workflow。视频文件是主要输出。

### 8.3 Workflow 状态检查命令

```bash
# 检查 workflow 状态
node ~/.openclaw/workspace/antfarm/dist/cli/cli.js workflow status "<rss-url>"

# 查看 workflow 日志
node ~/.openclaw/workspace/antfarm/dist/cli/cli.js logs "<rss-url>"

# 恢复失败的 workflow
node ~/.openclaw/workspace/antfarm/dist/cli/cli.js workflow resume <run-id>

# 停止 workflow
node ~/.openclaw/workspace/antfarm/dist/cli/cli.js workflow stop <run-id>
```

### 8.4 已知限制

1. **Workflow 代码位置：** 当前 workflow 位于 `~/.openclaw/workspace/antfarm/workflows/rss-daily-video-pipeline/`，这是 snarktank/antfarm 的本地副本。用户没有直接 push 权限到上游仓库。

2. **本地修改：** 对 workflow 的修改（如添加 email 通知）是本地更改，需要通过其他方式备份或同步。

3. **Cron job 管理：** 每次运行 `ensure-crons` 后，需要重新应用 delivery.mode="none" 的修复。

## 9. 参考依据

- AntFarm README：`https://github.com/snarktank/antfarm`
- AntFarm custom workflow guide：`https://github.com/snarktank/antfarm/blob/main/docs/creating-workflows.md`
- OpenRouter Kimi K2.5 model page：`https://openrouter.ai/moonshotai/kimi-k2.5`
- OpenRouter OpenClaw integration：`https://openrouter.ai/docs/cookbook/coding-agents/openclaw-integration`
- Wavespeed.ai Nano Banana Pro Text To Image API：`https://wavespeed.ai/docs/docs-api/google/google-nano-banana-pro-text-to-image`
- Voicebox API example：`https://github.com/jamiepine/voicebox`
