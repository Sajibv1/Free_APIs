# 🗄️ Free API Vault

> A quick-grab reference for **free / free-tier** APIs, grouped by use case.
> Goal: find a working, free API fast — with the exact Python call, params, and where to grab credentials.

**Legend for `Type` column**
- **Free / No key** — call it directly, no signup.
- **Free tier** — free quota, needs an API key (paid plans exist above).
- **Open (self-host)** — open-source, run it yourself, no per-call cost.
- **Hosted (provider)** — a provider runs it; free tier or free demo key.

**Conventions**
- All Python examples assume `import requests` (and `os` for keys). Install with `pip install requests`.
- Store keys in env vars: `export SERVICE_KEY=...` then `os.environ["SERVICE_KEY"]`.
- Endpoints/params reflect stable public docs; always confirm against the linked docs (pricing/limits change).

---

## Table of Contents
1. [Weather](#-weather)
2. [LLM (text generation)](#-llm-text-generation)
3. [STT — Speech-to-Text](#-stt--speech-to-text)
4. [TTS — Text-to-Speech](#-tts--text-to-speech)
5. [OCR](#-ocr)
6. [Web Scraping](#-web-scraping)
7. [Vision Models](#-vision-models)
8. [Search](#-search)
9. [Wiki / Knowledge](#-wiki--knowledge)
10. [Research / Academic Data](#-research--academic-data)
11. [Historical / Geographic Data](#-historical--geographic-data)
12. [Translation](#-translation)
13. [Email / SMS](#-email--sms)
14. [Image Generation](#-image-generation)
15. [Maps / Routing](#-maps--routing)
16. [Bonus: Finance, Geocoding, Misc](#-bonus-finance-geocoding-misc)

---

## 🌤 Weather

| API | Type | Python call (endpoint · params · returns) | Docs | Credentials |
|---|---|---|---|---|
| **Open-Meteo** | Free / No key | `requests.get("https://api.open-meteo.com/v1/forecast", params={"latitude":52.52,"longitude":13.41,"current":"temperature_2m,wind_speed_10m"})` → JSON `{"current":{"temperature_2m":..,"wind_speed_10m":..}}`. **Params:** `latitude,longitude,current/hourly/daily,timezone`. **Returns:** nested weather values + units. | [docs](https://open-meteo.com/en/docs) | None needed |
| **OpenWeatherMap** | Free tier | `requests.get("https://api.openweathermap.org/data/2.5/weather", params={"q":"London","appid":KEY,"units":"metric"})` → `{"main":{"temp":..},"weather":[{"description":..}]}`. **Params:** `q` or `lat/lon`, `appid`, `units`. **Returns:** `main`, `weather`, `wind`, `sys`. | [docs](https://openweathermap.org/current) | [Get key](https://home.openweathermap.org/api_keys) |
| **WeatherAPI.com** | Free tier | `requests.get("http://api.weatherapi.com/v1/current.json", params={"key":KEY,"q":"Paris"})` → `{"location":{..},"current":{"temp_c":..,"condition":{..}}}`. **Params:** `key`, `q` (city/latlon/IP). **Returns:** `location`, `current`. | [docs](https://www.weatherapi.com/docs/) | [Get key](https://www.weatherapi.com/signup.aspx) |
| **US NWS (weather.gov)** | Free / No key (US) | `requests.get("https://api.weather.gov/points/39.74,-104.99", headers={"User-Agent":"you@email"})` → grid URL; follow `properties.forecast`. **Params:** lat,lon in path; **User-Agent required**. **Returns:** GeoJSON forecast periods. | [docs](https://www.weather.gov/documentation/services-web-api) | None (set User-Agent) |

---

## 🤖 LLM (text generation)

| API | Type | Python call (endpoint · params · returns) | Docs | Credentials |
|---|---|---|---|---|
| **Groq** (fast OSS models) | Free tier | OpenAI-compatible: `requests.post("https://api.groq.com/openai/v1/chat/completions", headers={"Authorization":f"Bearer {KEY}"}, json={"model":"llama-3.3-70b-versatile","messages":[{"role":"user","content":"hi"}]})` → `{"choices":[{"message":{"content":..}}]}`. **Params:** `model`, `messages`, `temperature`, `max_tokens`. | [docs](https://console.groq.com/docs) | [Get key](https://console.groq.com/keys) |
| **Google Gemini** | Free tier | `requests.post(f"https://generativelanguage.googleapis.com/v1beta/models/gemini-2.0-flash:generateContent?key={KEY}", json={"contents":[{"parts":[{"text":"hi"}]}]})` → `{"candidates":[{"content":{"parts":[{"text":..}]}}]}`. **Params:** `contents`, `generationConfig`. | [docs](https://ai.google.dev/gemini-api/docs) | [Get key](https://aistudio.google.com/apikey) |
| **OpenRouter** (many models, some free) | Free tier | `requests.post("https://openrouter.ai/api/v1/chat/completions", headers={"Authorization":f"Bearer {KEY}"}, json={"model":"meta-llama/llama-3.1-8b-instruct:free","messages":[...]})` → OpenAI-shaped. **Tip:** models suffixed `:free`. | [docs](https://openrouter.ai/docs) | [Get key](https://openrouter.ai/keys) |
| **Hugging Face Inference** | Free tier | `requests.post("https://api-inference.huggingface.co/models/HuggingFaceH4/zephyr-7b-beta", headers={"Authorization":f"Bearer {KEY}"}, json={"inputs":"Hello"})` → `[{"generated_text":..}]`. **Params:** `inputs`, `parameters`. | [docs](https://huggingface.co/docs/api-inference) | [Get token](https://huggingface.co/settings/tokens) |
| **Cerebras** | Free tier | OpenAI-compatible: `requests.post("https://api.cerebras.ai/v1/chat/completions", headers={"Authorization":f"Bearer {KEY}"}, json={"model":"llama3.1-8b","messages":[...]})`. | [docs](https://inference-docs.cerebras.ai/) | [Get key](https://cloud.cerebras.ai/) |
| **Ollama** (local) | Open (self-host) | `requests.post("http://localhost:11434/api/generate", json={"model":"llama3.2","prompt":"hi","stream":False})` → `{"response":..}`. Run `ollama pull llama3.2` first. **Params:** `model`, `prompt`, `stream`. | [docs](https://github.com/ollama/ollama/blob/main/docs/api.md) | None (local) |

---

## 🎙 STT — Speech-to-Text

| API | Type | Python call (endpoint · params · returns) | Docs | Credentials |
|---|---|---|---|---|
| **Groq Whisper** | Free tier | `requests.post("https://api.groq.com/openai/v1/audio/transcriptions", headers={"Authorization":f"Bearer {KEY}"}, files={"file":open("a.mp3","rb")}, data={"model":"whisper-large-v3"})` → `{"text":..}`. **Params:** `file`, `model`, `language`, `response_format`. | [docs](https://console.groq.com/docs/speech-to-text) | [Get key](https://console.groq.com/keys) |
| **AssemblyAI** | Free tier | Upload then poll: `requests.post("https://api.assemblyai.com/v2/transcript", headers={"authorization":KEY}, json={"audio_url":URL})` → `{"id":..}`; GET `/v2/transcript/{id}` → `{"status":"completed","text":..}`. | [docs](https://www.assemblyai.com/docs) | [Get key](https://www.assemblyai.com/dashboard/signup) |
| **Deepgram** | Free tier | `requests.post("https://api.deepgram.com/v1/listen", headers={"Authorization":f"Token {KEY}"}, params={"model":"nova-2"}, data=open("a.wav","rb"))` → `{"results":{"channels":[{"alternatives":[{"transcript":..}]}]}}`. | [docs](https://developers.deepgram.com/docs) | [Get key](https://console.deepgram.com/signup) |
| **faster-whisper** (local) | Open (self-host) | `from faster_whisper import WhisperModel; m=WhisperModel("base"); segs,info=m.transcribe("a.mp3")` → iterate `segs` for `.text`. No HTTP. `pip install faster-whisper`. | [docs](https://github.com/SYSTRAN/faster-whisper) | None (local) |

---

## 🔊 TTS — Text-to-Speech

| API | Type | Python call (endpoint · params · returns) | Docs | Credentials |
|---|---|---|---|---|
| **ElevenLabs** | Free tier | `requests.post(f"https://api.elevenlabs.io/v1/text-to-speech/{VOICE_ID}", headers={"xi-api-key":KEY}, json={"text":"Hello","model_id":"eleven_multilingual_v2"})` → MP3 bytes in `.content`. **Params:** `text`, `model_id`, `voice_settings`. | [docs](https://elevenlabs.io/docs) | [Get key](https://elevenlabs.io/app/settings/api-keys) |
| **Deepgram Aura** | Free tier | `requests.post("https://api.deepgram.com/v1/speak", headers={"Authorization":f"Token {KEY}"}, params={"model":"aura-asteria-en"}, json={"text":"Hello"})` → audio bytes in `.content`. | [docs](https://developers.deepgram.com/docs/text-to-speech) | [Get key](https://console.deepgram.com/signup) |
| **Google Gemini TTS** | Free tier | `generateContent` with `responseModalities:["AUDIO"]` on a `-tts` model; returns base64 PCM in `candidates[].content.parts[].inlineData.data`. | [docs](https://ai.google.dev/gemini-api/docs/speech-generation) | [Get key](https://aistudio.google.com/apikey) |
| **Piper** (local) | Open (self-host) | CLI/lib: `echo "Hello" \| piper -m en_US-amy-medium.onnx -f out.wav`. Neural TTS, fully offline. | [docs](https://github.com/rhasspy/piper) | None (local) |
| **Coqui TTS** (local) | Open (self-host) | `from TTS.api import TTS; TTS("tts_models/en/ljspeech/tacotron2-DDC").tts_to_file("Hello","out.wav")`. `pip install TTS`. | [docs](https://github.com/coqui-ai/TTS) | None (local) |

---

## 📄 OCR

| API | Type | Python call (endpoint · params · returns) | Docs | Credentials |
|---|---|---|---|---|
| **OCR.space** | Free tier | `requests.post("https://api.ocr.space/parse/image", data={"apikey":KEY,"language":"eng"}, files={"file":open("img.png","rb")})` → `{"ParsedResults":[{"ParsedText":..}]}`. **Params:** `apikey`, `file`/`url`, `language`, `OCREngine`. | [docs](https://ocr.space/ocrapi) | [Get key](https://ocr.space/ocrapi/freekey) |
| **Tesseract** (local) | Open (self-host) | `import pytesseract; from PIL import Image; pytesseract.image_to_string(Image.open("img.png"))` → `str`. Needs system `tesseract` + `pip install pytesseract pillow`. | [docs](https://github.com/tesseract-ocr/tesseract) | None (local) |
| **EasyOCR** (local) | Open (self-host) | `import easyocr; r=easyocr.Reader(["en"]); r.readtext("img.png")` → list of `(bbox, text, conf)`. `pip install easyocr`. | [docs](https://github.com/JaidedAI/EasyOCR) | None (local) |
| **Google Gemini (vision)** | Free tier | Send image + prompt "extract all text" via `generateContent` (see Vision section). Great for messy/handwritten. | [docs](https://ai.google.dev/gemini-api/docs/vision) | [Get key](https://aistudio.google.com/apikey) |

---

## 🕸 Web Scraping

| API | Type | Python call (endpoint · params · returns) | Docs | Credentials |
|---|---|---|---|---|
| **Jina Reader** | Free / No key | `requests.get("https://r.jina.ai/https://example.com")` → clean Markdown of the page as text. Add `Authorization: Bearer` for higher limits. **Returns:** page as LLM-ready markdown. | [docs](https://jina.ai/reader/) | [Optional key](https://jina.ai/reader/) |
| **ScraperAPI** | Free tier | `requests.get("https://api.scraperapi.com", params={"api_key":KEY,"url":"https://example.com","render":"true"})` → raw HTML. Handles proxies/JS. | [docs](https://docs.scraperapi.com/) | [Get key](https://www.scraperapi.com/signup) |
| **ScrapingBee** | Free tier | `requests.get("https://app.scrapingbee.com/api/v1/", params={"api_key":KEY,"url":URL,"render_js":"true"})` → HTML/JSON. | [docs](https://www.scrapingbee.com/documentation/) | [Get key](https://app.scrapingbee.com/account/register) |
| **Firecrawl** | Free tier | `requests.post("https://api.firecrawl.dev/v1/scrape", headers={"Authorization":f"Bearer {KEY}"}, json={"url":URL,"formats":["markdown"]})` → `{"data":{"markdown":..}}`. Also `/crawl`. | [docs](https://docs.firecrawl.dev/) | [Get key](https://www.firecrawl.dev/app) |
| **BeautifulSoup + requests** (local) | Open (self-host) | `from bs4 import BeautifulSoup; html=requests.get(URL).text; soup=BeautifulSoup(html,"html.parser"); soup.find_all("a")`. No JS rendering. | [docs](https://www.crummy.com/software/BeautifulSoup/bs4/doc/) | None (local) |

---

## 👁 Vision Models

| API | Type | Python call (endpoint · params · returns) | Docs | Credentials |
|---|---|---|---|---|
| **Google Gemini Vision** | Free tier | `requests.post(f"https://generativelanguage.googleapis.com/v1beta/models/gemini-2.0-flash:generateContent?key={KEY}", json={"contents":[{"parts":[{"text":"describe"},{"inline_data":{"mime_type":"image/png","data":B64}}]}]})` → text in `candidates`. **B64** = base64 of image bytes. | [docs](https://ai.google.dev/gemini-api/docs/vision) | [Get key](https://aistudio.google.com/apikey) |
| **Groq (vision models)** | Free tier | Chat completions with `messages:[{"role":"user","content":[{"type":"text","text":"..."},{"type":"image_url","image_url":{"url":"data:image/png;base64,"+B64}}]}]`, `model:"llama-3.2-11b-vision-preview"`. | [docs](https://console.groq.com/docs/vision) | [Get key](https://console.groq.com/keys) |
| **OpenRouter (vision)** | Free tier | Same OpenAI vision message shape; pick a vision-capable free model. | [docs](https://openrouter.ai/docs) | [Get key](https://openrouter.ai/keys) |
| **Hugging Face (BLIP/DETR)** | Free tier | `requests.post("https://api-inference.huggingface.co/models/Salesforce/blip-image-captioning-large", headers={"Authorization":f"Bearer {KEY}"}, data=open("img.jpg","rb"))` → caption/labels JSON. | [docs](https://huggingface.co/docs/api-inference) | [Get token](https://huggingface.co/settings/tokens) |

---

## 🔎 Search

| API | Type | Python call (endpoint · params · returns) | Docs | Credentials |
|---|---|---|---|---|
| **DuckDuckGo (ddgs lib)** | Free / No key | `from ddgs import DDGS; list(DDGS().text("python", max_results=5))` → `[{"title":..,"href":..,"body":..}]`. `pip install ddgs`. Unofficial but keyless. | [docs](https://github.com/deedy5/ddgs) | None |
| **Brave Search** | Free tier | `requests.get("https://api.search.brave.com/res/v1/web/search", headers={"X-Subscription-Token":KEY}, params={"q":"python"})` → `{"web":{"results":[{"title":..,"url":..,"description":..}]}}`. | [docs](https://api-dashboard.search.brave.com/app/documentation) | [Get key](https://api-dashboard.search.brave.com/register) |
| **Tavily** (LLM search) | Free tier | `requests.post("https://api.tavily.com/search", json={"api_key":KEY,"query":"python","max_results":5})` → `{"results":[{"title":..,"url":..,"content":..}]}`. | [docs](https://docs.tavily.com/) | [Get key](https://app.tavily.com/) |
| **SerpApi** (Google results) | Free tier | `requests.get("https://serpapi.com/search", params={"api_key":KEY,"q":"python","engine":"google"})` → `{"organic_results":[...]}`. | [docs](https://serpapi.com/search-api) | [Get key](https://serpapi.com/users/sign_up) |
| **SearXNG** (self-host) | Open (self-host) | `requests.get(f"{BASE}/search", params={"q":"python","format":"json"})` → aggregated `{"results":[...]}`. Run your own instance. | [docs](https://docs.searxng.org/) | None (self-host) |

---

## 📚 Wiki / Knowledge

| API | Type | Python call (endpoint · params · returns) | Docs | Credentials |
|---|---|---|---|---|
| **Wikipedia REST** | Free / No key | `requests.get("https://en.wikipedia.org/api/rest_v1/page/summary/Python_(programming_language)", headers={"User-Agent":"you@email"})` → `{"title":..,"extract":..,"thumbnail":..}`. | [docs](https://en.wikipedia.org/api/rest_v1/) | None (set User-Agent) |
| **MediaWiki Action API** | Free / No key | `requests.get("https://en.wikipedia.org/w/api.php", params={"action":"query","format":"json","titles":"Earth","prop":"extracts","exintro":True})` → `{"query":{"pages":{..}}}`. | [docs](https://www.mediawiki.org/wiki/API:Main_page) | None |
| **Wikidata** | Free / No key | `requests.get("https://www.wikidata.org/w/api.php", params={"action":"wbgetentities","ids":"Q42","format":"json"})` → structured entity claims. SPARQL at `query.wikidata.org/sparql`. | [docs](https://www.wikidata.org/wiki/Wikidata:Data_access) | None |
| **DBpedia SPARQL** | Free / No key | `requests.get("https://dbpedia.org/sparql", params={"query":"SELECT ...","format":"application/sparql-results+json"})` → SPARQL JSON bindings. | [docs](https://www.dbpedia.org/resources/sparql/) | None |
| **Wiktionary (via MediaWiki)** | Free / No key | Same `w/api.php` on `en.wiktionary.org` for definitions. | [docs](https://en.wiktionary.org/w/api.php) | None |

---

## 🔬 Research / Academic Data

| API | Type | Python call (endpoint · params · returns) | Docs | Credentials |
|---|---|---|---|---|
| **arXiv** | Free / No key | `requests.get("http://export.arxiv.org/api/query", params={"search_query":"all:transformers","max_results":5})` → **Atom XML** (parse with `feedparser`). **Returns:** entries w/ title, summary, authors, pdf link. | [docs](https://info.arxiv.org/help/api/index.html) | None |
| **Semantic Scholar** | Free / No key (key ↑ limits) | `requests.get("https://api.semanticscholar.org/graph/v1/paper/search", params={"query":"nlp","fields":"title,abstract,year","limit":5})` → `{"data":[{...}]}`. | [docs](https://api.semanticscholar.org/api-docs/) | [Optional key](https://www.semanticscholar.org/product/api#api-key) |
| **CrossRef** | Free / No key | `requests.get("https://api.crossref.org/works", params={"query":"deep learning","rows":5}, headers={"User-Agent":"you@email"})` → `{"message":{"items":[{...DOI, title, authors}]}}`. | [docs](https://api.crossref.org/) | None (add mailto) |
| **OpenAlex** | Free / No key | `requests.get("https://api.openalex.org/works", params={"search":"climate","per-page":5,"mailto":"you@email"})` → `{"results":[{...}]}`. Works/authors/institutions. | [docs](https://docs.openalex.org/) | None (add mailto) |
| **CORE** | Free tier | `requests.get("https://api.core.ac.uk/v3/search/works", headers={"Authorization":f"Bearer {KEY}"}, params={"q":"AI"})` → full-text OA papers. | [docs](https://api.core.ac.uk/docs/v3) | [Get key](https://core.ac.uk/services/api) |
| **PubMed E-utilities** | Free / No key | `requests.get("https://eutils.ncbi.nlm.nih.gov/entrez/eutils/esearch.fcgi", params={"db":"pubmed","term":"cancer","retmode":"json"})` → PMIDs; then `efetch`/`esummary`. | [docs](https://www.ncbi.nlm.nih.gov/books/NBK25501/) | [Optional key](https://www.ncbi.nlm.nih.gov/account/) |

---

## 🏛 Historical / Geographic Data

| API | Type | Python call (endpoint · params · returns) | Docs | Credentials |
|---|---|---|---|---|
| **World Bank** | Free / No key | `requests.get("https://api.worldbank.org/v2/country/US/indicator/NY.GDP.MCAP.CD", params={"format":"json","date":"2000:2020"})` → `[meta, [ {date,value},.. ]]`. Historical socio-economic. | [docs](https://datahelpdesk.worldbank.org/knowledgebase/articles/889392) | None |
| **REST Countries** | Free / No key | `requests.get("https://restcountries.com/v3.1/name/germany")` → `[{"name":..,"capital":..,"population":..,"currencies":..}]`. | [docs](https://restcountries.com/) | None |
| **GeoNames** | Free tier | `requests.get("http://api.geonames.org/searchJSON", params={"q":"London","maxRows":5,"username":USER})` → `{"geonames":[{"name":..,"lat":..,"lng":..}]}`. | [docs](https://www.geonames.org/export/web-services.html) | [Register username](https://www.geonames.org/login) |
| **Nager.Date (public holidays)** | Free / No key | `requests.get("https://date.nager.at/api/v3/PublicHolidays/2024/US")` → `[{"date":..,"localName":..,"name":..}]`. | [docs](https://date.nager.at/swagger) | None |
| **Chronicling America (historic US news)** | Free / No key | `requests.get("https://chroniclingamerica.loc.gov/search/pages/results/", params={"andtext":"war","format":"json"})` → digitized newspaper pages. | [docs](https://chroniclingamerica.loc.gov/about/api/) | None |
| **Open Library** | Free / No key | `requests.get("https://openlibrary.org/search.json", params={"q":"tolkien"})` → `{"docs":[{"title":..,"author_name":..,"first_publish_year":..}]}`. | [docs](https://openlibrary.org/developers/api) | None |

---

## 🌐 Translation

| API | Type | Python call (endpoint · params · returns) | Docs | Credentials |
|---|---|---|---|---|
| **LibreTranslate** | Open (self-host) / Hosted | `requests.post("https://libretranslate.com/translate", json={"q":"Hello","source":"en","target":"es","format":"text"})` → `{"translatedText":"Hola"}`. **Params:** `q`, `source` (or `"auto"`), `target`, `format`, `api_key`. Public instance rate-limited; self-host for free unlimited. | [docs](https://libretranslate.com/docs/) | [Optional key](https://portal.libretranslate.com/) |
| **MyMemory** | Free / No key | `requests.get("https://api.mymemory.translated.net/get", params={"q":"Hello","langpair":"en|es"})` → `{"responseData":{"translatedText":"Hola"}}`. **Params:** `q`, `langpair`, `de` (email ↑ limit). ~5k words/day anon. | [docs](https://mymemory.translated.net/doc/spec.php) | None (add `de` email) |
| **DeepL** | Free tier | `requests.post("https://api-free.deepl.com/v2/translate", headers={"Authorization":f"DeepL-Auth-Key {KEY}"}, data={"text":"Hello","target_lang":"ES"})` → `{"translations":[{"text":"Hola","detected_source_language":"EN"}]}`. 500k chars/mo free. | [docs](https://developers.deepl.com/docs) | [Get key](https://www.deepl.com/pro-api) |
| **Lingva (Google Translate proxy)** | Free / No key | `requests.get("https://lingva.ml/api/v1/en/es/Hello")` → `{"translation":"Hola"}`. Path: `/api/v1/{source}/{target}/{text}`. Unofficial, keyless. | [docs](https://github.com/thedaviddelta/lingva-translate) | None |
| **Argos Translate** (local) | Open (self-host) | `import argostranslate.translate as t; t.translate("Hello","en","es")` → `"Hola"`. Offline neural MT. `pip install argostranslate` + download language packages. No HTTP. | [docs](https://github.com/argosopentech/argos-translate) | None (local) |

---

## 📧 Email / SMS

| API | Type | Python call (endpoint · params · returns) | Docs | Credentials |
|---|---|---|---|---|
| **Resend (email)** | Free tier | `requests.post("https://api.resend.com/emails", headers={"Authorization":f"Bearer {KEY}"}, json={"from":"you@yourdomain.com","to":["x@y.com"],"subject":"Hi","html":"<p>Hello</p>"})` → `{"id":..}`. 3k emails/mo, 100/day free. | [docs](https://resend.com/docs) | [Get key](https://resend.com/api-keys) |
| **SendGrid (email)** | Free tier | `requests.post("https://api.sendgrid.com/v3/mail/send", headers={"Authorization":f"Bearer {KEY}"}, json={"personalizations":[{"to":[{"email":"x@y.com"}]}],"from":{"email":"you@d.com"},"subject":"Hi","content":[{"type":"text/plain","value":"Hello"}]})` → `202` empty body. 100 emails/day free. | [docs](https://www.twilio.com/docs/sendgrid/api-reference) | [Get key](https://app.sendgrid.com/settings/api_keys) |
| **Brevo (email/SMS)** | Free tier | `requests.post("https://api.brevo.com/v3/smtp/email", headers={"api-key":KEY}, json={"sender":{"email":"you@d.com"},"to":[{"email":"x@y.com"}],"subject":"Hi","htmlContent":"<p>Hello</p>"})` → `{"messageId":..}`. 300 emails/day free. | [docs](https://developers.brevo.com/) | [Get key](https://app.brevo.com/settings/keys/api) |
| **Mailtrap (email testing)** | Free tier | `requests.post("https://send.api.mailtrap.io/api/send", headers={"Authorization":f"Bearer {KEY}"}, json={"from":{"email":"you@d.com"},"to":[{"email":"x@y.com"}],"subject":"Hi","text":"Hello"})` → `{"success":true}`. Sandbox inbox for dev. | [docs](https://api-docs.mailtrap.io/) | [Get key](https://mailtrap.io/api-tokens) |
| **Twilio (SMS)** | Free tier (trial) | `requests.post(f"https://api.twilio.com/2010-04-01/Accounts/{SID}/Messages.json", auth=(SID,TOKEN), data={"To":"+1...","From":"+1...","Body":"Hello"})` → `{"sid":..,"status":"queued"}`. Trial credit; verified numbers only. | [docs](https://www.twilio.com/docs/sms) | [Get creds](https://console.twilio.com/) |
| **Vonage (SMS)** | Free tier (trial) | `requests.post("https://rest.nexmo.com/sms/json", data={"api_key":KEY,"api_secret":SECRET,"to":"1...","from":"Vonage","text":"Hello"})` → `{"messages":[{"status":"0"}]}`. Trial credit. | [docs](https://developer.vonage.com/en/messaging/sms/overview) | [Get creds](https://dashboard.nexmo.com/) |

---

## 🎨 Image Generation

| API | Type | Python call (endpoint · params · returns) | Docs | Credentials |
|---|---|---|---|---|
| **Pollinations** | Free / No key | `requests.get("https://image.pollinations.ai/prompt/a%20cat%20astronaut", params={"width":1024,"height":1024,"seed":42})` → PNG bytes in `.content`. Prompt is URL-encoded in path. Keyless. | [docs](https://github.com/pollinations/pollinations) | None |
| **Hugging Face (diffusers)** | Free tier | `requests.post("https://api-inference.huggingface.co/models/black-forest-labs/FLUX.1-dev", headers={"Authorization":f"Bearer {KEY}"}, json={"inputs":"a cat astronaut"})` → image bytes in `.content`. | [docs](https://huggingface.co/docs/api-inference) | [Get token](https://huggingface.co/settings/tokens) |
| **Google Gemini (Imagen)** | Free tier | `requests.post(f"https://generativelanguage.googleapis.com/v1beta/models/imagen-3.0-generate-002:predict?key={KEY}", json={"instances":[{"prompt":"a cat astronaut"}],"parameters":{"sampleCount":1}})` → base64 image in `predictions`. | [docs](https://ai.google.dev/gemini-api/docs/imagen) | [Get key](https://aistudio.google.com/apikey) |
| **Cloudflare Workers AI** | Free tier | `requests.post(f"https://api.cloudflare.com/client/v4/accounts/{ACCT}/ai/run/@cf/black-forest-labs/flux-1-schnell", headers={"Authorization":f"Bearer {KEY}"}, json={"prompt":"a cat astronaut"})` → `{"result":{"image": <base64>}}`. 10k neurons/day free. | [docs](https://developers.cloudflare.com/workers-ai/) | [Get key](https://dash.cloudflare.com/profile/api-tokens) |
| **Stable Diffusion WebUI** (local) | Open (self-host) | `requests.post("http://localhost:7860/sdapi/v1/txt2img", json={"prompt":"a cat astronaut","steps":20})` → `{"images":[<base64>]}`. Run AUTOMATIC1111 with `--api`. | [docs](https://github.com/AUTOMATIC1111/stable-diffusion-webui/wiki/API) | None (local) |

---

## 🗺 Maps / Routing

| API | Type | Python call (endpoint · params · returns) | Docs | Credentials |
|---|---|---|---|---|
| **OSRM (routing)** | Free / No key | `requests.get("https://router.project-osrm.org/route/v1/driving/13.39,52.52;13.42,52.50", params={"overview":"full","geometries":"geojson"})` → `{"routes":[{"distance":..,"duration":..,"geometry":..}]}`. Coords are `lon,lat`. Demo server; self-host for volume. | [docs](http://project-osrm.org/docs/v5.24.0/api/) | None |
| **OpenRouteService** | Free tier | `requests.post("https://api.openrouteservice.org/v2/directions/driving-car", headers={"Authorization":KEY}, json={"coordinates":[[8.68,49.41],[8.69,49.42]]})` → GeoJSON route w/ distance/duration. Also isochrones, geocoding, matrix. 2k req/day free. | [docs](https://openrouteservice.org/dev/#/api-docs) | [Get key](https://openrouteservice.org/dev/#/signup) |
| **Overpass (OSM data)** | Free / No key | `requests.get("https://overpass-api.de/api/interpreter", params={"data":'[out:json];node["amenity"="cafe"](52.5,13.3,52.6,13.5);out;'})` → `{"elements":[{"lat":..,"lon":..,"tags":..}]}`. Query OSM features. | [docs](https://wiki.openstreetmap.org/wiki/Overpass_API) | None |
| **GraphHopper (routing)** | Free tier | `requests.get("https://graphhopper.com/api/1/route", params={"point":["52.52,13.39","52.50,13.42"],"vehicle":"car","key":KEY})` → `{"paths":[{"distance":..,"time":..,"points":..}]}`. | [docs](https://docs.graphhopper.com/) | [Get key](https://www.graphhopper.com/dashboard/) |
| **MapTiler (tiles/geocoding)** | Free tier | `requests.get(f"https://api.maptiler.com/geocoding/Berlin.json", params={"key":KEY})` → GeoJSON features w/ coordinates. Tiles at `/maps/{style}/{z}/{x}/{y}.png?key=`. 100k tiles/mo free. | [docs](https://docs.maptiler.com/cloud/api/) | [Get key](https://cloud.maptiler.com/account/keys/) |

---

## 🎁 Bonus: Finance, Geocoding, Misc

| API | Type | Python call (endpoint · params · returns) | Docs | Credentials |
|---|---|---|---|---|
| **Frankfurter (FX rates)** | Free / No key | `requests.get("https://api.frankfurter.app/latest", params={"from":"USD","to":"EUR"})` → `{"rates":{"EUR":..}}`. ECB data. | [docs](https://www.frankfurter.app/docs/) | None |
| **CoinGecko (crypto)** | Free tier | `requests.get("https://api.coingecko.com/api/v3/simple/price", params={"ids":"bitcoin","vs_currencies":"usd"})` → `{"bitcoin":{"usd":..}}`. | [docs](https://docs.coingecko.com/) | [Optional key](https://www.coingecko.com/en/api/pricing) |
| **Nominatim (OSM geocoding)** | Free / No key | `requests.get("https://nominatim.openstreetmap.org/search", params={"q":"Eiffel Tower","format":"json"}, headers={"User-Agent":"you@email"})` → `[{"lat":..,"lon":..,"display_name":..}]`. Max 1 req/s. | [docs](https://nominatim.org/release-docs/latest/api/Search/) | None (set User-Agent) |
| **ipapi (IP geolocation)** | Free tier | `requests.get("https://ipapi.co/8.8.8.8/json/")` → `{"city":..,"country_name":..,"latitude":..}`. | [docs](https://ipapi.co/api/) | [Optional key](https://ipapi.co/) |
| **Alpha Vantage (stocks)** | Free tier | `requests.get("https://www.alphavantage.co/query", params={"function":"GLOBAL_QUOTE","symbol":"IBM","apikey":KEY})` → `{"Global Quote":{"05. price":..}}`. | [docs](https://www.alphavantage.co/documentation/) | [Get key](https://www.alphavantage.co/support/#api-key) |
| **NASA APOD / open data** | Free tier | `requests.get("https://api.nasa.gov/planetary/apod", params={"api_key":KEY})` → `{"title":..,"url":..,"explanation":..}`. `DEMO_KEY` works for light use. | [docs](https://api.nasa.gov/) | [Get key](https://api.nasa.gov/) |

---

## 🖥 Self-Hosting: Minimum Hardware & Setup

For every *Open (self-host)* / local tool above — what it takes to run it and where to start. "Min" = it runs (slowly); "Comfortable" = usable speed. GPU is optional unless noted; CPU-only works for all except real-time image generation.

| Tool | Section | Min hardware | Comfortable | Setup guide |
|---|---|---|---|---|
| **Ollama** | LLM | 8 GB RAM (runs 1–3B models, e.g. `llama3.2:1b`) | 16 GB RAM or 8 GB VRAM for 7–8B; 32 GB+/24 GB VRAM for 70B | [Install & run](https://github.com/ollama/ollama/blob/main/README.md#quickstart) |
| **faster-whisper** | STT | 4 GB RAM (`tiny`/`base` on CPU) | 6 GB VRAM for `large-v3`; CPU works but ~real-time only on small models | [Setup](https://github.com/SYSTRAN/faster-whisper#installation) |
| **Piper** | TTS | 2 GB RAM, any modern CPU (no GPU) | Same — extremely light, runs on Raspberry Pi | [Install](https://github.com/rhasspy/piper#installation) |
| **Coqui TTS** | TTS | 4 GB RAM (CPU) | 4 GB VRAM for fast/higher-quality models (e.g. XTTS) | [Install](https://github.com/coqui-ai/TTS#installation) |
| **Tesseract** | OCR | 1 GB RAM, any CPU (no GPU) | Same — very light | [Install](https://tesseract-ocr.github.io/tessdoc/Installation.html) |
| **EasyOCR** | OCR | 4 GB RAM (CPU) | 4 GB VRAM (GPU ~5–10× faster) | [Setup](https://github.com/JaidedAI/EasyOCR#installation) |
| **BeautifulSoup + requests** | Web Scraping | Negligible — any Python environment | Same | [Install](https://www.crummy.com/software/BeautifulSoup/bs4/doc/#installing-beautiful-soup) |
| **Argos Translate** | Translation | 2 GB RAM, any CPU (no GPU) | 4 GB RAM for many language packs | [Install](https://github.com/argosopentech/argos-translate#install) |
| **SearXNG** | Search | 2 GB RAM (Docker recommended) | 2 GB RAM — mostly network-bound | [Install (Docker)](https://docs.searxng.org/admin/installation-docker.html) |
| **Stable Diffusion WebUI** | Image Gen | 4 GB VRAM (SD 1.5, GPU effectively required) | 8–12 GB VRAM for SDXL/FLUX; CPU-only is very slow | [Install](https://github.com/AUTOMATIC1111/stable-diffusion-webui/wiki/Install-and-Run) |
| **OSRM** | Maps/Routing | 2 GB RAM (small region extract) | RAM scales with map size: a full continent needs 32 GB+ to preprocess | [Setup (Docker)](https://github.com/Project-OSRM/osrm-backend#using-docker) |
| **LibreTranslate** | Translation | 4 GB RAM (CPU) | 8 GB RAM for many models loaded; GPU optional | [Install](https://github.com/LibreTranslate/LibreTranslate#install-and-run) |

**Notes**
- **LLM sizing rule of thumb:** a quantized (Q4) model needs roughly `params × 0.6 GB` of RAM/VRAM — a 7B model ≈ 4–5 GB, a 70B ≈ 40 GB. Ollama auto-quantizes and can spill to CPU RAM if VRAM is short (slower).
- **GPU helps most** for: LLMs (Ollama), image generation (SD WebUI, effectively required), and large Whisper models. It barely matters for Piper, Tesseract, Argos, and scraping.
- **Docker** is the fastest path for SearXNG, LibreTranslate, and OSRM — no dependency juggling.

---

## ✅ Quick usage tips
- **Keyless first:** if you just need data now, start with the *Free / No key* rows.
- **Set a `User-Agent`** for Wikipedia, NWS, Nominatim, CrossRef, OpenAlex — they can block/deprioritize anonymous traffic.
- **Respect rate limits:** Nominatim = 1 req/s; most free tiers cap daily/monthly calls. Cache responses.
- **OpenAI-compatible cluster:** Groq, OpenRouter, Cerebras, and Ollama all accept the OpenAI chat schema — swap `base_url` + `model` and reuse the same code.
- **Local = unlimited & private:** Ollama, faster-whisper, Piper, Tesseract, EasyOCR, BeautifulSoup run offline with no per-call cost.
- **Always re-check limits/pricing** at the linked docs — free tiers change.

_Last curated: 2026-07-19._
