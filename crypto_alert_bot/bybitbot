import requests
import time

TELEGRAM_BOT_TOKEN = "8281969942:AAGIA7zLVzS8OPV8Ij3K_LJsrjEZjfmr8Qs"
TELEGRAM_CHAT_ID = "1948054734" and "1964519156"

PERCENT_TRIGGER = 5            # порог роста в %
CHECK_INTERVAL = 60            # каждые 60 сек обновление

BYBIT_TICKERS_URL = "https://api.bybit.com/v5/market/tickers"


def send_telegram(text):
    url = f"https://api.telegram.org/bot{TELEGRAM_BOT_TOKEN}/sendMessage"
    data = {"chat_id": TELEGRAM_CHAT_ID, "text": text}
    try:
        requests.post(url, data=data)
    except Exception as e:
        print("Ошибка отправки телеграм:", e)


def get_all_prices():
    """Получить цены всех монет USDT"""
    params = {"category": "linear"}
    r = requests.get(BYBIT_TICKERS_URL, params=params)
    data = r.json().get("result", {}).get("list", [])

    prices = {}
    for symbol in data:
        name = symbol["symbol"]    # Например: BTCUSDT
        if name.endswith("USDT"):
            try:
                price = float(symbol["lastPrice"])
                prices[name] = price
            except:
                pass
    return prices


def percent_change(old, new):
    if old == 0:
        return 0
    return (new - old) / old * 100


def main():
    print("Загружаю цены с Bybit...")
    last_prices = get_all_prices()
    print("Старт мониторинга...")

    while True:
        try:
            current = get_all_prices()

            for coin, price in current.items():
                if coin in last_prices:
                    change = percent_change(last_prices[coin], price)

                    if change >= PERCENT_TRIGGER:
                        text = (
                            f"📈 Монета *{coin}* выросла на *{change:.2f}%*\n"
                            f"Цена: {price}"
                        )
                        send_telegram(text)
                        print(f"[ALERT] {coin}: +{change:.2f}%")

                last_prices[coin] = price

            time.sleep(CHECK_INTERVAL)

        except Exception as e:
            print("Ошибка:", e)
            time.sleep(5)


if __name__ == "__main__":
    main()
