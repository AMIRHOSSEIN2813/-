from pyrogram import Client, filters
import requests
import json
import re

API_ID = "683734"
API_HASH = "cd3c11f1f8b1368ef2d0623342fbe821"
BOT_TOKEN = "7778289671:AAHokiMqFGYk8SmXZ6a2yq7Ip_3K-ZRWLo8"
ADMIN_USER_ID = 575030674

app = Client("crypto_price_bot", api_id=API_ID, api_hash=API_HASH, bot_token=BOT_TOKEN)


crypto_dict = {
    "BTC": ["بیت کوین", "بیتکوین", "بیت‌کوین"],
    "PAXG": ["طلا", "نرخ طلا","قیمت طلا"],
    "SHIB": ["شیبا", "شیبا اینو"],
    "LTC": ["لایت کوین"],
    "XRP": ["ریپل"],
    "USDT": ["تتر"],
    "BNB": ["بایننس کوین"],
    "DOGE": ["دوج کوین"],
    "ADA": ["کارادانو"],
    "DOT": ["پولکادات"],
    "SOL": ["سولانا"],
    "LINK": ["چین لینک"],
    "BCH": ["بیت کوین کش", "بیتکوین کش"],
    "AVAX": ["آوالانچ", "اواکس"],
    "XMR": ["مونرو"],
    "ZEC": ["زی کش"],
    "ONE": ["هارمونی"],
    "MIOTA": ["آیوتا"],
    "MANA": ["دیسنترالند"],
    "AXS": ["اکسی اینفینیتی"],
    "XLM": ["استلار"],
    "FIL": ["فایل کوین"],
    "ONT": ["آنتولوژی"],
    "CRV": ["کرو فایننس", "کرو دائو"],
    "COMP": ["کامپوند"],
    "SUSHI": ["سوشی سواپ", "سوشی‌سواپ"],
    "MKR": ["میکر"],
    "HBAR": ["هیدرا هشگراف", "هدرا"],
    "DGB": ["دیجی بایت"],
    "CHZ": ["چیلیز"],
    "ALGO": ["آلگوراند"],
    "NEAR": ["نیر", "نیر پروتکل"],
    "XTZ": ["تزوس"],
    "KNC": ["کایبر نتورک"],
    "RENBTC": ["ریبون"],
    "GALA": ["گلکسی"],
    "SNX": ["سینتتیکس"],
    "VET": ["ورتیس"],
    "REN": ["رین"],
    "DAO": ["سازمان غیرمتمرکز", "داو"],
    "RON": ["رونین"],
    "QNT": ["کیوان", "کوانت"],
    "PDA": ["کیف پول"],
    "WIN": ["وینک"],
    "DASH": ["دش"],
    "LUNA": ["ترا"],
    "ARV": ["آروین"],
    "FET": ["فوتی", "فچ"],
    "BAND": ["بند پروتکل"],
    "TWT": ["تراست والت"],
    "USDC": ["یو اس دی کوین"],
    "DAI": ["دای"],
    "AAVE": ["آوه"],
    "SUI": ["سویی"],
    "OP": ["آپتیمیسم"],
    "ARB": ["آربیتریم", "آربیتروم"],
    "ENS": ["خدمات نام اتریوم", "اتریوم نیم سرویس"],
    "PEPE": ["پپه"],
    "HNT": ["هلیوم"],
    "KSM": ["کوزاما"],
    "SAND": ["ساند باکس", "سند باکس"],
    "BLUR": ["بلور"],
    "FET": ["فچ.ای آی"],
    "PUNDIX": ["پاندی ایکس"],
    "STMX": ["استورم ایکس"],
    "LDO": ["لیدو د آو", "لیدو دائو"],
    "KAVA": ["کاوا"],
    "SYN": ["سیناپس"],
    "BTT": ["بیت تورنت"],
    "QTUM": ["کوتوم"],
    "1INCH": ["یک اینچ", "1اینچ"],
    "XEM": ["نِم"],
    "XDC": ["شبکه ایکس دی سی"],
    "FLOKI": ["فلوکی اینو"],
    "ICP": ["کامپیوتر اینترنتی"],
    "WAVES": ["ویوز"],
    "AUDIO": ["آدیوس"],
    "CELO": ["سلو"],
    "BAT": ["توکن توجه اساسی", "بریو توکن"],
    "GRT": ["گراف"],
    "EGLD": ["الران"],
    "GAL": ["گالا"],
    "WBTC": ["رپد بیت‌کوین"],
    "IMX": ["ایموتبل ایکس"],
    "GLM": ["گولم"],
    "SUSHI": ["سوشی‌سواپ"],
    "ZRO": ["زیرو ایکس"],
    "STORJ": ["استورج"],
    "ANT": ["آراگون"],
    "AEVO": ["آوو"],
    "RSR": ["ریزر"],
    "API3": ["ای‌پی‌آی ۳"],
    "OM": ["مانتا دائو"],
    "RDNT": ["رادیانت"],
    "MAGIC": ["مجیک"],
    "T": ["ترا"],
    "CVX": ["کانویکس فایننس"],
    "UMA": ["یو‌ام‌ای"],
    "SSV": ["اس‌اس‌وی نتورک"],
    "FLOW": ["فلو"],
    "CVC": ["سیویک"],
    "NMR": ["نومریر"],
    "SKL": ["اسکیل نتورک"],
    "SNT": ["استاتوس"],
    "TRB": ["تلور"],
    "WLD": ["ورلد‌کوین"],
    "YFI": ["یرن فایننس"],
    "MATIC": ["ماتیک"],
    "FET": ["فچ","فت"],
    "AGIX": ["سینگولاریتی‌نت"],
    "LPT": ["لایوپییر"],
    "SLP": ["اسموث لاو پوشن"],
    "MEME": ["میم کوین"],
    "BAL": ["بلنسر"],
    "NOT": ["نات","نات‌کوین","نات کوین","ناتکوین"],
    "TON": ["تون‌کوین", "تون", "تون کوین"],
    "TRX": ["ترون"],
    "DOGS": ["داگز"],
    "CATS": ["کتز"],
    "HMSTR": ["همستر"],
    "CATI": ["کتیژن","کتیزن"],
    "THETA": ["تتا"],
    "ZIL": ["زیلیکا"],
    "OMG": ["اومیسه گو"],
    "ANKR": ["انکر"],
    "ACH": ["آلکمی پی"],
    "APE": ["ایپ کوین"],
    "APT": ["اپتوس"],
    "ASTR": ["استار"],
    "BICO": ["بیکونومی"],
    "BOSON": ["بوسون پروتکل"],
    "BTRST": ["برینتراست"],
    "CLV": ["کلوور فایننس"],
    "COTI": ["کوتی"],
    "FORTH": ["امپلفورث"],
    "LRC": ["لوپرینگ"],
    "NANO": ["نانو"],
    "OCEAN": ["اوشن پروتکل"],
    "WAT": ["وات","وات کوین","واتکوین"],
    "RUNE": ["تورچین"]
}


def format_toman_amount(amount):
    return f"{amount:,.0f}"


def get_crypto_symbol(input_name):
    for symbol, names in crypto_dict.items():
        if input_name in names:
            return symbol
    return None
    
def get_price_usdt_nobitex():
    try:
        url = "https://api.nobitex.ir/v3/orderbook/USDTIRT"
        response = requests.get(url)
        response.raise_for_status()
        data = response.json()


        if 'lastTradePrice' in data:
            return float(data['lastTradePrice']) / 10  
        else:
            return None
    except requests.exceptions.RequestException:
        return None


def get_price_shiba_nobitex():
    try:
        url = "https://api.nobitex.ir/v3/orderbook/SHIBUSDT"
        response = requests.get(url)
        response.raise_for_status()
        data = response.json()


        if 'lastTradePrice' in data:
            return float(data['lastTradePrice'])  
        else:
            return None
    except requests.exceptions.RequestException:
        return None

@app.on_message(filters.text & filters.regex(r"^(تتر|قیمت تتر)$", re.IGNORECASE))
def send_usdt_price(client, message):
    usdt_price = get_price_usdt_nobitex()
    if usdt_price:
        message.reply(f"🟢 قیمت تتر: {usdt_price} تومان ")
    else:
        message.reply("خطا در دریافت قیمت تتر ❌")


@app.on_message(filters.text & filters.regex(r"^(قیمت شیبا|شیبا|شیبا اینو)$", re.IGNORECASE))
def send_shiba_price(client, message):
    shiba_price_usdt = get_price_shiba_nobitex()  
    usdt_price_toman = get_price_usdt_nobitex()  
    
    if shiba_price_usdt and usdt_price_toman:
        shiba_price_toman = shiba_price_usdt * usdt_price_toman  
        message.reply(f"🟢 قیمت شیبا: {shiba_price_usdt} USDT ({shiba_price_toman:,.0f} تومان) ")
    else:
        message.reply("خطا در دریافت قیمت شیبا ❌")



def get_price_binance(symbol):
    try:
        url = f"https://api.binance.com/api/v3/ticker/price?symbol={symbol}USDT"
        response = requests.get(url)
        response.raise_for_status()
        data = response.json()
        return float(data['price']) if 'price' in data else None
    except requests.exceptions.RequestException:
        return None

def get_price_kucoin(symbol):
    try:
        url = f"https://api.kucoin.com/api/v1/market/orderbook/level1?symbol={symbol}-USDT"
        response = requests.get(url)
        response.raise_for_status()
        data = response.json()
        return float(data['data']['price']) if 'data' in data and 'price' in data['data'] else None
    except requests.exceptions.RequestException:
        return None

def get_price_gateio(symbol):
    try:
        url = f"https://api.gateio.ws/api/v4/spot/tickers?currency_pair={symbol}_USDT"
        response = requests.get(url)
        response.raise_for_status()
        data = response.json()
        return float(data[0]['last']) if len(data) > 0 and 'last' in data[0] else None
    except requests.exceptions.RequestException:
        return None

def get_price_okx(symbol):
    try:
        url = f"https://www.okx.com/api/v5/market/ticker?instId={symbol}-USDT"
        response = requests.get(url)
        response.raise_for_status()
        data = response.json()
        return float(data['data'][0]['last']) if 'data' in data and len(data['data']) > 0 else None
    except requests.exceptions.RequestException:
        return None


def get_exchange_rate_nobitex():
    try:
        url = "https://api.nobitex.ir/v3/orderbook/USDTIRT"
        response = requests.get(url)
        response.raise_for_status()
        data = response.json()
        return float(data['lastTradePrice']) if 'lastTradePrice' in data else None
    except requests.exceptions.RequestException:
        return None


def get_price_shiba_nobitex():
    try:
        url = "https://api.nobitex.ir/v3/orderbook/SHIBUSDT"
        response = requests.get(url)
        response.raise_for_status()
        data = response.json()
        return float(data['lastTradePrice']) if 'lastTradePrice' in data else None
    except requests.exceptions.RequestException:
        return None


def get_price(symbol):
    try:
        url = f"https://api.nobitex.ir/v3/orderbook/{symbol}"
        response = requests.get(url)
        response.raise_for_status()
        data = response.json()
        return float(data['lastTradePrice']) if 'lastTradePrice' in data else None
    except requests.exceptions.RequestException:
        return None

def calculate_gold_price_per_gram(final_price_toman, discount_percent=0.40):
    """محاسبه قیمت هر گرم طلای 24 عیار بر اساس قیمت نهایی و تبدیل از اونس به گرم."""
    price_per_gram_24 = final_price_toman / 31.1035  
    return price_per_gram_24 * (1 - discount_percent)  # اعمال درصد تخفیف

def calculate_price_by_carat(price_per_gram_24, carat, discount_percent=0.0):
    """محاسبه قیمت هر گرم طلا بر اساس عیار مشخص."""
    return price_per_gram_24 * (carat / 24) * (1 - discount_percent)  # اعمال درصد تخفیف

def calculate_second_hand_gold(price_per_gram_18, discount_percent=0.01):
    """محاسبه قیمت طلای دست دوم بر اساس قیمت هر گرم طلا 18 عیار و اعمال تخفیف."""
    second_hand_price = (price_per_gram_18 * 740) / 741
    return second_hand_price * (1 - discount_percent)  # اعمال تخفیف

def calculate_gold_22(price_per_gram_24, discount_percent=0.0):
    """محاسبه قیمت هر گرم طلای 22 عیار از 24 عیار با امکان تخفیف."""
    price_per_gram_22 = price_per_gram_24 * (22 / 24)
    return price_per_gram_22 * (1 - discount_percent)  # اعمال درصد تخفیف

def calculate_gold_21(price_per_gram_24, discount_percent=0.0):
    """محاسبه قیمت هر گرم طلای 21 عیار از 24 عیار با امکان تخفیف."""
    price_per_gram_21 = price_per_gram_24 * (21 / 24)
    return price_per_gram_21 * (1 - discount_percent)  # اعمال درصد تخفیف

def calculate_gold_18(price_per_gram_24):
    """محاسبه قیمت هر گرم طلای 18 عیار از قیمت طلای 24 عیار."""
    return price_per_gram_24 * 0.75  # ضربدر 0.75 برای تبدیل به 18 عیار
    

def calculate_gold_17(price_per_gram_24, discount_percent=0.0):
    """محاسبه قیمت هر گرم طلای 17 عیار از 24 عیار با امکان تخفیف."""
    price_per_gram_17 = price_per_gram_24 * (17 / 24)
    return price_per_gram_17 * (1 - discount_percent)  # اعمال درصد تخفیف

def calculate_bahar_azadi(price_per_gram_24):
    """محاسبه قیمت سکه بهار آزادی بر اساس قیمت هر گرم طلای 24 عیار."""
    weight_bahar_azadi = 8.431  # وزن سکه بهار آزادی (گرم)
    return price_per_gram_24 * weight_bahar_azadi  # قیمت سکه بهار آزادی

def calculate_seke_nim(price_per_gram_24):
    """محاسبه قیمت نیم سکه بر اساس قیمت هر گرم طلای 24 عیار."""
    weight_nim_seke = 5.15  # وزن نیم سکه (گرم)
    return price_per_gram_24 * weight_nim_seke  # قیمت نیم سکه

def calculate_seke_rub(price_per_gram_24):
    """محاسبه قیمت ربع سکه بر اساس قیمت هر گرم طلای 24 عیار."""
    weight_rub_seke = 3.362  # وزن ربع سکه (گرم)
    return price_per_gram_24 * weight_rub_seke  # قیمت ربع سکه

def calculate_seke_garmi(price_per_gram_24):
    """محاسبه قیمت سکه گرمی بر اساس قیمت هر گرم طلای 24 عیار."""
    weight_garmi_seke = 1.50  # وزن سکه گرمی (گرم)
    return price_per_gram_24 * weight_garmi_seke  # قیمت سکه گرمی

def format_toman_amount(amount):
    """کم کردن یک صفر از قیمت برای نمایش در تومان."""
    return f"{amount / 10:,.0f}"  # تقسیم قیمت بر 10 برای تبدیل به تومان

@app.on_message(filters.text & filters.regex(r"^(طلا|سکه|نرخ طلا|قیمت طلا)$", re.IGNORECASE))
def main_gold(client, message):
    # دریافت قیمت PAXG و XAUT از نوبیتکس
    paxg_price_toman = get_price('PAXGIRT') 
    xaut_price_toman = get_price('XAUTIRT')  
    
    # بررسی دریافت قیمت‌ها
    if paxg_price_toman is None and xaut_price_toman is None:
        message.reply("خطا در دریافت قیمت طلا ❌")
        return

    # پیدا کردن قیمت نهایی طلا
    final_price_toman = max(paxg_price_toman, xaut_price_toman) if paxg_price_toman and xaut_price_toman else paxg_price_toman or xaut_price_toman
    
    # محاسبه قیمت طلای 24 عیار با تخفیف
    discount_percent = 0.002
    price_per_gram_24 = calculate_gold_price_per_gram(final_price_toman, discount_percent)

    # محاسبه قیمت‌های مختلف طلا
    price_per_gram_17 = calculate_gold_17(price_per_gram_24)
    price_per_gram_18 = calculate_gold_18(price_per_gram_24)  # استفاده از تابع جدید
    price_per_gram_21 = calculate_gold_21(price_per_gram_24)
    price_per_gram_22 = calculate_gold_22(price_per_gram_24)

    # محاسبه قیمت سکه‌ها
    bahar_azadi_price = calculate_bahar_azadi(price_per_gram_24)  # سکه کامل
    nim_sekeh_price = calculate_seke_nim(price_per_gram_24)  # نیم سکه
    rub_e_sekeh_price = calculate_seke_rub(price_per_gram_24)  # ربع سکه
    sekkeh_germi_price = calculate_seke_garmi(price_per_gram_24)  # سکه گرمی

    # محاسبه قیمت طلای دست دوم
    second_hand_price = calculate_second_hand_gold(price_per_gram_18)

    # فرمت کردن پیام پاسخ
    response_message = (f"🟡 قیمت هر گرم طلای 24 عیار: {format_toman_amount(price_per_gram_24)} تومان\n"
                        "          🔸          • =========== •           🔸          \n"
                        f"🟡 قیمت هر گرم طلای 22 عیار: {format_toman_amount(price_per_gram_22)} تومان\n"
                        "          🔸          • =========== •           🔸          \n"
                        f"🟡 قیمت هر گرم طلای 21 عیار: {format_toman_amount(price_per_gram_21)} تومان\n"
                        "          🔸          • =========== •           🔸          \n"
                        f"🟡 قیمت هر گرم طلای 18 عیار: {format_toman_amount(price_per_gram_18)} تومان\n"
                        "          🔸          • =========== •           🔸          \n"
                        f"🟡 قیمت هر گرم طلای 17 عیار: {format_toman_amount(price_per_gram_17)} تومان\n"
                        "          🔸          • =========== •           🔸          \n"
                        f"🟢 قیمت طلای دست دوم: {format_toman_amount(second_hand_price)} تومان\n"
                        "          🔸          • =========== •           🔸          \n"
                        f"🔴 قیمت سکه بهار آزادی: {format_toman_amount(bahar_azadi_price)} تومان\n"
                        "          🔸          • =========== •           🔸          \n"
                        f"🔴 قیمت نیم سکه: {format_toman_amount(nim_sekeh_price)} تومان\n"
                        "          🔸          • =========== •           🔸          \n"
                        f"🔴 قیمت ربع سکه: {format_toman_amount(rub_e_sekeh_price)} تومان\n"
                        "          🔸          • =========== •           🔸          \n"
                        f"🔴 قیمت سکه گرمی: {format_toman_amount(sekkeh_germi_price)} تومان")

    message.reply(response_message)
    
    
@app.on_message(filters.text)
def crypto_price(client, message):
    user_input = message.text.strip()
    symbol = get_crypto_symbol(user_input) or user_input.upper()

    if symbol:
        exchange_rate = get_exchange_rate_nobitex()
        if exchange_rate is None:
            message.reply("خطا در دریافت نرخ ارز. ❌")
            return

        price_binance = get_price_binance(symbol)
        price_kucoin = get_price_kucoin(symbol)
        price_gateio = get_price_gateio(symbol)
        price_okx = get_price_okx(symbol)

        response_message = f"🟢 قیمت کنونی <{symbol}> :"

        if price_binance is not None:
            price_in_toman = price_binance * exchange_rate 
            response_message += f"\n🟡 - Binance: {price_binance} USDT ({format_toman_amount(price_in_toman)} تومان)"
        else:
            response_message += "\n❌ - Binance: قیمت موجود نیست"

        if price_kucoin is not None:
            price_in_toman = price_kucoin * exchange_rate 
            response_message += f"\n🟡 - KuCoin: {price_kucoin} USDT ({format_toman_amount(price_in_toman)} تومان)"
        else:
            response_message += "\n❌ - KuCoin: قیمت موجود نیست"

        if price_gateio is not None:
            price_in_toman = price_gateio * exchange_rate 
            response_message += f"\n🟡 - Gate.io: {price_gateio} USDT ({format_toman_amount(price_in_toman)} تومان)"
        else:
            response_message += "\n❌ - Gate.io: قیمت موجود نیست"

        if price_okx is not None:
            price_in_toman = price_okx * exchange_rate 
            response_message += f"\n🟡 - OKX: {price_okx} USDT ({format_toman_amount(price_in_toman)} تومان)"
        else:
            response_message += "\n❌ - OKX: قیمت موجود نیست"

        message.reply(response_message)
    else:
        message.reply("رمزارز شناسایی نشد. لطفاً نام صحیح را وارد کنید.")

@app.on_message(filters.text)
def handle_message(client, message):
    user_input = message.text.strip()

    # بررسی دستور طلا
@app.on_message(filters.text)
def crypto_price(client, message):
    user_input = message.text.strip()



if __name__ == "__main__":
    app.run()
    
