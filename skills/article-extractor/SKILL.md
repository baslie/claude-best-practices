---
name: article-extractor
description: Extract clean article content from URLs (blog posts, articles, tutorials) and save as Markdown. Uses crawl4ai (Playwright-based crawler optimized for LLM). Handles JavaScript-rendered pages, SPA, dynamic sites. Use when the user provides a URL and wants the text content, asks to "download article", "extract content from URL", "save this blog post", "сохрани статью", "скачай статью", "извлеки текст", or needs clean article text without ads/navigation clutter.
allowed-tools: Bash,Write
---

# Article Extractor (crawl4ai)

Extract main content from web articles using **crawl4ai** (Playwright-based crawler). Removes navigation, ads, clutter. Saves clean Markdown.

## Critical: Windows Encoding

**On Windows (Git Bash/MSYS2), Python defaults to cp1251**, which crashes on Unicode. **ALWAYS** prefix Python commands with `PYTHONIOENCODING=utf-8`:

```bash
# CORRECT
PYTHONIOENCODING=utf-8 python -c "..."

# WRONG — will crash on non-ASCII
python -c "..."
```

## Installation

```bash
pip install crawl4ai && crawl4ai-setup
```

Check: `python -c "import crawl4ai" 2>/dev/null`

## Complete Workflow

```bash
ARTICLE_URL="https://example.com/article"

# 1. Check if crawl4ai is installed
if ! python -c "import crawl4ai" 2>/dev/null; then
    echo "crawl4ai is not installed. Install with:"
    echo "  pip install crawl4ai && crawl4ai-setup"
    exit 1
fi

# 2. Extract article via crawl4ai Python API
# PYTHONIOENCODING=utf-8 is REQUIRED on Windows to avoid cp1251 crashes
OUTPUT=$(PYTHONIOENCODING=utf-8 python -c "
import asyncio
from crawl4ai import AsyncWebCrawler, CrawlerRunConfig, BrowserConfig

async def extract():
    browser_config = BrowserConfig(headless=True)
    crawler_config = CrawlerRunConfig(
        word_count_threshold=50,
        excluded_tags=['nav', 'footer', 'header'],
        exclude_external_links=True,
        exclude_social_media_links=True,
    )
    async with AsyncWebCrawler(config=browser_config) as crawler:
        result = await crawler.arun(url='$ARTICLE_URL', config=crawler_config)
        if result.success:
            title = result.metadata.get('title', 'Article') if result.metadata else 'Article'
            print(f'TITLE:{title}')
            print('---CONTENT---')
            print(result.markdown)
        else:
            print(f'ERROR:{result.error_message}')

asyncio.run(extract())
")

# 3. Check for errors
if echo "$OUTPUT" | grep -q "^ERROR:"; then
    echo "Extraction failed: $(echo "$OUTPUT" | grep "^ERROR:" | sed 's/^ERROR://')"
    exit 1
fi

# 4. Extract title and content
TITLE=$(echo "$OUTPUT" | grep "^TITLE:" | sed 's/^TITLE://')
CONTENT=$(echo "$OUTPUT" | sed -n '/^---CONTENT---$/,$ p' | tail -n +2)

# 5. Check content is not empty
if [ -z "$CONTENT" ]; then
    echo "Error: Extraction returned empty content. The page may require authentication or use unsupported rendering."
    exit 1
fi

# 6. Clean filename
# Use sed for character replacement (tr with empty replacement causes errors)
# Use cut -c 1-120 — Cyrillic chars are multibyte, shorter cut is too aggressive
FILENAME=$(echo "$TITLE" | sed 's/[\/:<>"|?*]/-/g' | sed 's/--*/-/g' | cut -c 1-120 | sed 's/^ *//;s/ *$//')
FILENAME="${FILENAME}.md"

# 7. Save content to .md file (printf avoids echo issues with -e/-n at start)
printf '%s\n' "$CONTENT" > "$FILENAME"

# 8. Show result
echo "Extracted article: $TITLE"
echo "Saved to: $FILENAME"
echo ""
echo "Preview (first 15 lines):"
head -n 15 "$FILENAME"
```

## Error Handling

| Problem | Solution |
|---|---|
| crawl4ai not installed | `pip install crawl4ai && crawl4ai-setup` |
| UnicodeEncodeError (cp1251) | Prefix with `PYTHONIOENCODING=utf-8` — never omit on Windows |
| `result.success == False` | Show `result.error_message` to user |
| Empty content | Page may need auth or uses unsupported rendering |
| Paywall/login required | crawl4ai cannot bypass auth walls — inform user |
| `tr` error "string2 must be non-empty" | Use `sed 's/[\/:<>"|?*]/-/g'` instead of `tr` |
| Filename too short for Cyrillic | Use `cut -c 1-120` (not 80 — multibyte chars) |

## After Extraction

Display: article title, filename, preview (first 10-15 lines).
