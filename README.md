# news-to-podcast
feeds:
  - name: 日本経済新聞
    url: https://rss.nikkei.co.jp/rss/topix.xml
  - name: Bloomberg Japan
    url: https://www.bloomberg.co.jp/feeds/rss/news.xml
  - name: Reuters Japan
    url: https://jp.reuters.com/rss/topnews.xml
max_articles: 8
hours_back: 24
```{insert\_element\_2\_}

### 1.2 収集スキル導入
```bash
cd ~/.openclaw/workspace/skills
git clone https://github.com/sonnytheai/openclaw-briefing.git news-briefing
chmod +x news-briefing/scripts/*
```{insert\_element\_3\_}

---

## 📌 ステップ2：AI要約設定
`~/.openclaw/workspace/briefings/prompts/podcast-script.md`
---

## 📌 ステップ3：TTSで音声→MP3作成
### 3.1 ElevenLabs例（高音質推奨）
`~/.openclaw/workspace/skills/text2speech.sh`
```bash
#!/bin/bash
TEXT=$1
OUT=$2
curl -X POST "https://api.elevenlabs.io/v1/text-to-speech/VOICE_ID" \
  -H "xi-api-key: YOUR_KEY" -H "Content-Type: application/json" \
  -d '{"text":"'"$TEXT"'","model_id":"eleven_multilingual_v2"}' \
  --output "$OUT"
ffmpeg -i "$OUT" -b:a 128k "$OUT.mp3" && rm "$OUT"
```{insert\_element\_4\_}

### 3.2 OpenAI TTS例
```bash
openclaw tts --input summary.txt --output news.mp3 --voice nova
#!/bin/bash
cd ~/.openclaw/workspace/news2podcast
DATE=$(date +%Y-%m-%d)

# 1. 収集
openclaw briefing run --config rss.yaml --output raw.json

# 2. 要約→台本
openclaw llm run --prompt prompts/podcast-script.md --input raw.json --output script.txt

# 3. TTS→MP3
./skills/text2speech.sh script.txt output/news-$DATE.mp3

# 4. RSS更新（任意）
./update-rss.sh output/news-$DATE.mp3 "$DATE 朝刊ニュース"
```{insert\_element\_5\_}

実行権限：`chmod +x daily-run.sh`

---

## 📌 ステップ5：毎朝自動起動（Cron）
```bash
openclaw cron add --name "morning-news-podcast" \
  --cron "0 6 * * *" \
  --agent main \
  --task "Run ~/workspace/news2podcast/daily-run.sh
