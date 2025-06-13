# short_alert_bot.py
from binance.client import Client
from telegram import Bot
import time
import os

API_KEY = os.getenv('API_KEY')
API_SECRET = os.getenv('API_SECRET')
TELEGRAM_TOKEN = os.getenv('TELEGRAM_TOKEN')
CHAT_ID = os.getenv('CHAT_ID')

client = Client(API_KEY, API_SECRET)
bot = Bot(token=TELEGRAM_TOKEN)

SYMBOL = 'MASKUSDT'
VOLUME_MINIMO = 200000

def verificar_short():
    candles = client.futures_klines(symbol=SYMBOL, interval='1m', limit=2)
    ultimo = candles[-1]
    open_, close = float(ultimo[1]), float(ultimo[4])
    volume = float(ultimo[5])

    if close < open_ and volume > VOLUME_MINIMO:
        alvo1 = round(close * 0.98, 4)
        alvo2 = round(close * 0.97, 4)
        alvo3 = round(close * 0.95, 4)
        msg = f"🚨 SHORT: {SYMBOL}\\nEntrada: {close}\\n🎯 Alvos:\\n1️⃣ {alvo1}\\n2️⃣ {alvo2}\\n3️⃣ {alvo3}"
        bot.send_message(chat_id=CHAT_ID, text=msg)

while True:
    verificar_short()
    time.sleep(60)
