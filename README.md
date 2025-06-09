# Telegram Translator Bot 🤖

This is a Python application that listens to specific Telegram channels, auto-detects the language of incoming messages, translates them to English using a **locally-hosted LibreTranslate instance**, and forwards the translated message (with metadata) to a **target channel**.

---

## 🚀 Features

✅ Listen to any number of Telegram channels (configured in `main.py`)  
✅ Automatically detect the source language and its confidence  
✅ Translate to English using a **self-hosted LibreTranslate**  
✅ Forward the translated message, detected language, and sender info to a target Telegram channel  
✅ Fully self-hosted translation (no external API costs!)

---

## 🛠️ Configuration

All configuration is in `main.py`:

```python
# === CONFIGURATION ===
api_id = 'YOUR_API_ID'
api_hash = 'YOUR_API_HASH'
phone_number = '+1234567890'  # Your phone number with country code
target_channel = 'your_target_channel_username_or_id'  # Public username or numeric ID
source_chats = [
    'source_channel_username1',
    'source_channel_username2',
    # Add as many as needed!
]
````

✅ Replace these with your actual:

* **Telegram API credentials**: [Get from my.telegram.org](https://my.telegram.org)
* **Your phone number** (with country code)
* **Target channel username** (or numeric chat ID)
* **Source channels** (usernames or IDs) you want to listen to

---

## 🌍 Setting up LibreTranslate locally

This project requires you to **self-host LibreTranslate** for translation.

### 1️⃣ Install Docker

Download and install Docker Desktop from [https://www.docker.com/products/docker-desktop/](https://www.docker.com/products/docker-desktop/).

---

### 2️⃣ Run LibreTranslate

Use the following command to launch LibreTranslate locally (port **5555** is recommended to avoid conflicts):

```bash
docker run -it -p 5555:5000 libretranslate/libretranslate
```

✅ This will:

* Download and run LibreTranslate, exposing it on `http://localhost:5555`.
* On first startup, download translation models (this takes a few minutes).
* When complete, you’ll see logs like:

  ```
  Loaded support for 47 languages (94 models total)!
  Running on http://*:5000
  ```

---

### 3️⃣ Verify it’s running

Open your browser to:

```
http://localhost:5555
```

Or test it with:

```bash
curl -X POST "http://localhost:5555/translate" \
     -H "Content-Type: application/json" \
     -d '{"q":"Привет, как дела?","source":"auto","target":"en"}'
```

✅ If you see a translation result, it’s working!

---

## 🟢 Install Python dependencies

Create a virtual environment (recommended):

```bash
python -m venv .venv
```

Activate it:

```bash
# Windows
.venv\Scripts\activate
# macOS/Linux
source .venv/bin/activate
```

Install required packages:

```bash
pip install -r requirements.txt
```

**`requirements.txt`**:

```plaintext
telethon>=1.28
requests>=2.31
```

✅ Note: The script uses Python’s built-in `asyncio` module (no need to install it separately — it’s part of Python!).

---

## 🚀 Running the bot

Once everything is set up:

```bash
python main.py
```

✅ The bot will:

* Connect to Telegram using Telethon
* Listen for new messages in your **source chats**
* Detect the source language and confidence
* Translate to English
* Forward the translated text (with detected language & confidence) to your **target channel**

---

## 🔧 How the translation works

The script’s helper function:

```python
def translate_with_libretranslate(text, target_lang='en'):
    url_translate = "http://localhost:5555/translate"
    url_detect = "http://localhost:5555/detect"
    headers = {"Content-Type": "application/json"}

    try:
        # Detect the language
        detect_resp = requests.post(url_detect, json={"q": text}, headers=headers, timeout=10)
        detect_resp.raise_for_status()
        detection = detect_resp.json()[0]
        detected_lang = detection["language"]
        confidence = detection["confidence"]

        # Translate to English
        data = {
            "q": text,
            "source": detected_lang,
            "target": target_lang,
            "format": "text"
        }
        translate_resp = requests.post(url_translate, json=data, headers=headers, timeout=10)
        translate_resp.raise_for_status()
        translated_text = translate_resp.json()["translatedText"]

        return {
            "detected_language": detected_lang,
            "confidence": confidence,
            "translation": translated_text
        }

    except requests.RequestException as e:
        print(f"Translation error: {e}")
        return None
```

✅ This:

* Detects source language (`/detect`)
* Translates to English (`/translate`)
* Returns detected language, confidence, and translated text

---

## 📝 Notes

* LibreTranslate’s port (`5555`) is used in the script. If you change it, **update the URLs in `main.py`** accordingly:

  ```python
  url_translate = "http://localhost:5555/translate"
  url_detect = "http://localhost:5555/detect"
  ```

* For best results, keep LibreTranslate running in the background while using the bot.

---

## 📦 Project structure

```
.
├── main.py             # Main bot logic (configuration + translation logic)
├── requirements.txt    # Python dependencies
└── README.md           # This readme!
```

---
