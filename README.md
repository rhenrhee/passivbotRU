![Passivbot](docs/images/pbot_logo_full.svg)

# Trading bot running on Bybit, OKX, Bitget, GateIO, Binance and Hyperliquid

:warning: **Вы используете данный инструмент на свой страх и риск** :warning:

v7.3.1


## Вводная часть

<p align="justify">PassivBot (далее сокр. PB) – полностью автоматизированный торговый робот, построенный с использованием языка «python» и «RUST» (одна из реализаций языка С++). Имплементация на RUST была добавлена с седьмой версии продукта. Основной целью разработки данного инструмента являлось создание стабильного инструмента, сочетающего в себе низкорисковую торговлю в сочетании с возможностью накопления прибыли в течение кратного времени (час, день, неделя, месяц и т.д.) без ручного воздействия на рынок.

<p align="justify">Торговый робот PB торгует на фьючерсных[1] и спотовых[2] рынках, используя продвинутую форму сеточной (grid)[3] торговли, когда ордеры размещаются как на закрытие, так и на открытие.</p>

<p align="justify">[1] Фьючерс — это когда две стороны договариваются по поводу стоимости какого-то актива в будущем. Продавец надеется, что актив будет стоить дешевле, а покупатель ждёт высоких цен. Другими словами, фьючерс — это почти что пари. Стороны спорят, вырастет цена актива или упадёт. При этом, в отличие от опциона, исполнить контракт должны обе стороны — и продавец, и покупатель.</p>
<p align="justify">[2] Спотовая торговля — форма, при которой покупка и продажа актива происходит немедленно на основе спотовой цены, которая определяется на момент сделки. Спотовая торговля является одним из самых распространенных видов торговли на рынках ценных бумаг, валютных пар, сырьевых товаров и других активов. Оплата и поставка актива происходят немедленно или в течение нескольких рабочих дней, в зависимости от условий.</p>
<p align="justify">[3] Сеточная торговля — это бот для автоматизации торговли фьючерсными контрактами. Она предназначена для размещения ордеров через заданные интервалы в заданном ценовом диапазоне. Во время использования данной стратегии ордера размещаются выше и ниже установленной цены, создавая сетку ордеров с постепенным увеличением и уменьшением цен. В результате создается торговая сетка.</p>

## О стратегии работы

<p align="justify">Автоматизированный робот представляет собой python-скрипт, которому для осуществления торговли требуется предоставить доступ к учётной записи пользователя на бирже. PB «прослушивает» поток текущих сделок через websocket на выбранном рынке (пара), создавая автоматически и/или отменяя автоматически лимитные ордера на покупку и/или продажу.</p>

<p align="justify">PB работает на фьючерских и спотовых рынках. Используя режим хеджирования, можно открывать одновременно длинные и короткие позиции. На спотовых рынках робот имитирует поведение фьючерсов, открывая только длинные позиции с максимальным кредитным плечом в 1Х. </p>

<p align="justify">Торговый робот основывает свои решения на позициях по счёту, открытых ордерах, балансе, а также на нескольких EMA[1]различного диапазона. EMA используется как правило для сглаживания Take-Profit и Stop-Loss позиций. </p>

<p align="justify">Режим работы PB в реальном времени детерминировано и может быть смоделировано в том числе и на исторических данных с помощью инструментов так называемого бэк-тестера. Изменить режим работы можно в настройках в соответствии с конкретным рынком и предпочтением пользователя. </p>

<p align="justify">Параметры конфигурации возможно оптимизировать для конкретного периода исторических данных путём обратного тестирования, что позволит прийти к той конфигурации, которая покажет наиболее лучшие результаты торговли. </p>

<p align="justify">[1] EMA — отличный инструмент для экспоненциального сглаживания цен и устранения краткосрочных колебаний (шума). Из-за высокой волатильности цен такие колебания в криптоиндустрии происходят постоянно.</p>

### Trailing Orders
In addition to grid-based entries and closes, Passivbot may be configured to utilize trailing entries and trailing closes.

For trailing entries, the bot waits for the price to move beyond a specified threshold and then retrace by a defined percentage before placing a re-entry order. Similarly, for trailing closes, the bot waits before placing its closing orders until after the price has moved favorably by a threshold percentage and then retraced by a specified percentage. This may result in the bot locking in profits more effectively by exiting positions when the market shows signs of reversing instead of at a fixed distance from average entry price.

Grid and trailing orders may be combined, such that the robot enters or closes a whole or a part of the position as grid orders and/or as trailing orders.

### Forager
The Forager feature dynamically chooses the most volatile markets on which to open positions. Volatility is defined as the mean of the normalized relative range for the most recent 1m candles, i.e. `mean((ohlcv.high - ohlcv.low) / ohlcv.close)`.

### Unstucking Mechanism
Passivbot manages underperforming, or "stuck", positions by realizing small losses over time. If multiple positions are stuck, the bot prioritizes positions with the smallest gap between the entry price and current market price for "unstucking". Losses are limited by ensuring that the account balance does not fall under a set percentage below the past peak balance.  

## Установка

To install Passivbot and its dependencies, follow the steps below.

### Step 1: Копирование репозитория

First, clone the Passivbot repository to the local machine:

```sh
git clone https://github.com/enarjord/passivbot.git
cd passivbot
```


### Step 2: Установка Rust
Passivbot uses Rust for some of its components. Install Rust by following these steps:

Visit https://www.rust-lang.org/tools/install
Follow the instructions to install Rustup, the Rust installer and version management tool.
After installation, restart the terminal or command prompt.

### Step 3: Создание и активация виртуального окружения (среды)

Create a virtual environment to manage dependencies:

```sh
python3 -m venv venv
```

Activate the virtual environment:

```sh
source venv/bin/activate
```

### Step 4: Установка зависимостей Python

Install all the required Python dependencies listed in the `requirements.txt` file:

```sh
pip install -r requirements.txt
```

### Step 5 (optional): Сборка (компиляция) библиотек Rust

Passivbot will attempt to build the necessary Rust extensions automatically, but they can also be built manually by navigating to the `passivbot-rust` directory and using `maturin`:

```sh
cd passivbot-rust
maturin develop --release
cd ..
```

If changes in the Rust source are detected, recompilation is needed, which Passivbot will attempt to do automatically when starting. To manually recompile, use the commands given above.

### Step 6: Добавление API keys

Make a copy of the api-keys template file:

```sh
cp api-keys.json.example api-keys.json
```

Add your keys to api-keys.json.

### Step 7: Запуск PassivBot

To start the bot with the default settings, run:

```sh
python3 src/main.py -u {account_name_from_api-keys.json}
```

or make a new configuration file, using `configs/template.json` as a template, and start the bot with:


```sh
python3 src/main.py path/to/config.json
```

## Jupyter Lab

Jupyter lab needs to be run in the same virtual environment as the bot. Activate venv (see installation instructions above, step 3), and launch Jupyter lab from the Passivbot root dir with:
```shell
python3 -m jupyter lab
```

## Системные требования

- Python >= 3.8
- [requirements.txt](requirements.txt) dependencies

## Pre-optimized configurations

Coming soon...

See also https://pbconfigdb.scud.dedyn.io/

## Документация:

For more detailed information about Passivbot, see documentation files here: https://quanttrading.ru/passivbot/

## Support

[![Discord](https://img.shields.io/badge/Discord-7289DA?style=for-the-badge&logo=discord&logoColor=white)](https://discord.gg/QAF2H2UmzZ)

[![Telegram](https://img.shields.io/badge/Telegram-2CA5E0?style=for-the-badge&logo=telegram&logoColor=white)](https://t.me/passivbot_futures)

## Third Party Links, Referrals and Tip Jar

**Passivbot Manager Service:**  
There is a paid manager service to run Passivbot on the user's behalf:  
www.passivbotmanager.com  

**Passivbot GUI**
A graphical user interface for Passivbot:  
https://github.com/msei99/pbgui

**Referrals:**  
Signing up using these referrals is appreciated:  
https://accounts.binance.com/register?ref=TII4B07C  
https://partner.bybit.com/b/passivbot  
https://partner.bitget.com/bg/Y8FU1W  
https://www.okx.com/join/PASSIVBOT  (20% rebate)  
https://app.hyperliquid.xyz/join/PASSIVBOT  

**Note on Binance**  
To support continued Passivbot development, please use a Binance account which  
1) was created after 2024-09-21 and  
2) either:  
  a) was created without a referral link, or  
  b) was created with referral ID: "TII4B07C".  
                                                                                      
Passivbot receives commissions from trades only for accounts meeting these criteria.  


**BuyMeACoffee:**  
https://www.buymeacoffee.com/enarjord  

**Donations:**  
If the robot is profitable, consider donating as showing gratitude for its development:  

- USDT or USDC Binance Smart Chain BEP20:  
0x4b7b5bf6bea228052b775c052843fde1c63ec530  
- USDT or USDC Arbitrum One:  
0x4b7b5bf6bea228052b775c052843fde1c63ec530  

Bitcoin (BTC) via Strike:  
enarjord@strike.me

## License

Released freely without conditions.
Anybody may copy, distribute, modify, use or misuse for commercial, non-commercial, educational or non-educational purposes, censor, claim as one's own or otherwise do whatever without permission from anybody.
