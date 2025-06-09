# Telegram Translator Bot 🤖

This is a Python application that listens to specific Telegram channels, auto-detects the language of incoming messages, translates them to English using a **locally-hosted LibreTranslate instance**, and forwards the translated message (with metadata) to a **target channel**.

---

## 🚀 Quick start

### 1️⃣ Clone the repository

```bash
git clone https://github.com/chrispyroberts/TelegramScanner.git
````

---

### 2️⃣ Install Python dependencies

✅ Create a virtual environment (recommended):

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

✅ Install required packages:

```bash
pip install -r requirements.txt
```

---

### 3️⃣ Set up Telegram configuration

Edit the `main.py` file to set:

* **Telegram API credentials**
* **Your phone number**
* **Target channel username**
* **Source channels** you want to listen to

Example snippet in `main.py`:

```python
api_id = 'YOUR_API_ID'
api_hash = 'YOUR_API_HASH'
phone_number = '+1234567890'
target_channel = 'your_target_channel_username_or_id'
source_chats = ['source_channel1', 'source_channel2']
```

---

### 4️⃣ Set up LibreTranslate locally

This project requires you to **self-host LibreTranslate** for translation.

#### ➜ Install Docker

Download and install Docker Desktop from [https://www.docker.com/products/docker-desktop/](https://www.docker.com/products/docker-desktop/).

#### ➜ Run LibreTranslate

```bash
docker run -it -p 5555:5000 libretranslate/libretranslate
```

✅ This will:

* Download and run LibreTranslate, exposing it on `http://localhost:5555`.
* On first startup, download translation models (this takes a few minutes).

---

### 5️⃣ Run the bot!

```bash
python main.py
```

✅ The bot will:

* Connect to Telegram using Telethon
* Listen for new messages in your source chats
* Detect the source language and confidence
* Translate to English
* Forward the translated text (with detected language & confidence) to your target channel

---

Great point! Let’s add a section to the README about **stopping the LibreTranslate Docker container** to free up RAM and system resources when you’re not using it.

---

Here’s the updated section to add to your **README.md**:

---

### 🛑 Stopping LibreTranslate to save resources

LibreTranslate runs in a Docker container, and it can use a lot of RAM (because of language models).

✅ To **stop LibreTranslate** and free up resources:

1️⃣ List running containers:

```bash
docker ps
```

You’ll see something like:

```
CONTAINER ID   IMAGE                           PORTS                    NAMES
abc123456789   libretranslate/libretranslate   0.0.0.0:5555->5000/tcp   friendly_cartwright
```

2️⃣ Stop the container:

```bash
docker stop abc123456789
```

(replace `abc123456789` with your actual container ID)

---

### 📝 Notes

* Once stopped, LibreTranslate **won’t be available** for translations.
* You can **restart it anytime** by re-running:

  ```bash
  docker run -it -p 5555:5000 libretranslate/libretranslate
  ```
* Or use Docker Desktop’s GUI to stop/start containers!

---

✅ This ensures your computer isn’t using extra RAM when you’re not actively translating.


