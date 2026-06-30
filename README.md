feeds:
  - name: 日本経済新聞
    url: https://rss.nikkei.co.jp/rss/topix.xml
  - name: Bloomberg Japan
    url: https://www.bloomberg.co.jp/feeds/rss/news.xml
  - name: Reuters Japan
    url: https://jp.reuters.com/rss/topnews.xml
max_articles: 8
hours_back: 24

# OpenClawインストール
curl -fsSL https://get.openclaw.ai | sh

# 作業フォルダ作成＆収集スキル導入
mkdir -p ~/.openclaw/workspace/skills
cd ~/.openclaw/workspace/skills
git clone https://github.com/sonnytheai/openclaw-briefing.git news-briefing
chmod +x news-briefing/scripts/*
