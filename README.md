# tf2-express
[![License](https://img.shields.io/github/license/offish/tf2-express.svg)](https://github.com/offish/tf2-express/blob/master/LICENSE)
[![Stars](https://img.shields.io/github/stars/offish/tf2-express.svg)](https://github.com/offish/tf2-express/stargazers)
[![Issues](https://img.shields.io/github/issues/offish/tf2-express.svg)](https://github.com/offish/tf2-express/issues)
[![Size](https://img.shields.io/github/repo-size/offish/tf2-express.svg)](https://github.com/offish/tf2-express)
[![Discord](https://img.shields.io/discord/467040686982692865?color=7289da&label=Discord&logo=discord)](https://discord.gg/t8nHSvA)
[![Code style](https://img.shields.io/badge/code%20style-black-000000.svg)](https://github.com/psf/black)
[![Ruff](https://img.shields.io/endpoint?url=https://raw.githubusercontent.com/astral-sh/ruff/main/assets/badge/v2.json)](https://github.com/astral-sh/ruff)

Automated TF2 trading bot with GUI support, built with Python. Prices are by default provided by [Prices.TF](https://prices.tf).

## Donate
Donations are not required, but greatly appericated.
- BTC: `bc1q9gmh5x2g9s0pw3282a5ypr6ms8qvuxh3fd7afh`
- [Steam Trade Offer](https://steamcommunity.com/tradeoffer/new/?partner=293059984&token=0-l_idZR)

## Features
* GUI for adding and changing items, prices, `max_stock` + browsing trades
* Supports automated price updates from [Prices.TF](https://prices.tf)
* Creates, modifies and deletes listings on [Backpack.TF](https://backpack.tf)
* Accepts incoming friend requests
* Supports buy/sell message commands (`sell_5021_6`)
* Sends counter offer when user is trying to take items for free
* Sends counter offer when values are incorrect
* Supports Random Craft Hats [[?]](#random-craft-hats)
* Bank as many items as you want
* Add items by either name or SKU
* Uses MongoDB for saving items, prices and trades
* Limited inventory fetching to mitigate rate-limits
* Supports arbitraging items from different trading sites [[?]](#arbitrage)
* Supports 3rd party inventory providers [[?]](#3rd-party-inventory-providers)

All available options can be found [here](express/options.py).

**Key dependencies:**
* [steam.py](https://github.com/gobot1234/steam.py)
* [backpack-tf](https://github.com/offish/backpack-tf)
* [tf2-sku](https://github.com/offish/tf2-sku)
* [tf2-data](https://github.com/offish/tf2-data)
* [tf2-utils](https://github.com/offish/tf2-utils)

## Showcase
![GUI Showcase](https://github.com/user-attachments/assets/06f61b55-06a2-4bd7-a575-9225d68d2396)

## Installation
You need to have Python 3.11 or above installed.
If you want to run the bot using Docker see [Using Docker](#using-docker).

```bash
git clone git@github.com:offish/tf2-express.git
cd tf2-express
pip install -r requirements.txt
```

> [!NOTE]
> You need to host a MongoDB server for the bot to work. Download the free community version [here](https://www.mongodb.com/try/download/community). You may also want to install [MongoDB Compass](https://www.mongodb.com/products/tools/compass) to access/modify/delete collections  manually.

## Setup
> [!NOTE]
> Make a copy of `config.example.json` and name it `config.json`. Make sure it is in the same folder as the example file. Update credentials and set your preferred `options`.

Example config:
```json
{
    "bots": [
        {
            "username": "username",
            "password": "password",
            "shared_secret": "Aa11aA1+1aa1aAa1a=",
            "identity_secret": "aA11aaaa/aa11a/aAAa1a1=",
            "options": {
                "use_backpack_tf": true,
                "backpack_tf_token": "token",
                "enable_arbitrage": false,
                "inventory_provider": "steamsupply",
                "inventory_api_key": "mySteamSupplyApiKey",
                "accept_donations": true,
                "decline_trade_hold": true,
                "enable_craft_hats": true,
                "save_trade_offers": true,
                "owners": [
                    "76511111111111111",
                    "76522222222222222"
                ]
            }
        }
    ]
}
```

> [!NOTE]
> As of v3.0.0 tf2-express only supports running one bot instance at a time. It will use the first entry in `bots` in the config.

### Options
- `username` is the username for the Steam account you want to use the bot with. This will also be the of the MongoDB database.
- `use_backpack_tf` whether to list items on Backpack.TF or not.
- `backpack_tf_token` can be found on [here](https://next.backpack.tf/account/api-access) (copy Access Token, not API key)
- `pricing_provider` is the provider for pricing. Prices.tf is the default.
- `inventory_provider` is the provider for inventory. The default is Steam Community, but you can use a third party provider like Steam.Supply or Express-Load [[?]](#3rd-party-inventory-providers)
- `inventory_api_key` is the API key for the inventory provider. You don't need to set this if you are using the default inventory provider.
- `backpack_tf_user_agent` user agent to show on next.backpack.tf.
- `accept_donations` whether to accept donations or not.
- `auto_counter_bad_offers` whether to counter offers with wrong values or not.
- `decline_trade_hold` whether to decline trade hold or not.
- `auto_cancel_sent_offers` whether to auto cancel our sent offers or not after some time.
- `cancel_sent_offers_after_seconds` how long to wait before auto cancelling sent offers.
- `max_price_age_seconds` how old a price can be before it is fetched again.
- `enable_arbitrage` whether to enable arbitrage or not [[?]](#arbitrage)
- `enable_craft_hats` whether to enable Random Craft Hats or not [[?]](#random-craft-hats)
- `save_trade_offers` whether to save trade offers in the MongoDB database or not.
- `groups` is a list of group IDs to join.
- `owners` is a list of owner Steam ID64s. The bot will accept offers from owners no matter what.

## Running
```bash
# tf2-express/
python main.py # start the bot
python panel.py # start the gui
```

Now you can visit the GUI at http://127.0.0.1:5000/ 

Logs will be available under `logs/express.log`. 
Level is set to DEBUG, so here you will be able to see every request etc. and more information than is shown in the terminal.

> [!WARNING]
> Do NOT share your logs or config files with anyone before removing sensitive information. This might leak your `API_KEY` and more.

## Updating
```bash
# tf2-express/
git pull
pip install --upgrade -r requirements.txt
# update packages like bptf, tf2-utils, tf2-data and tf2-sku
# which the bot is dependant on
```

## Using Docker
First configure the bot like shown in [Setup](#setup).
Then change the timezone in the `Dockerfile`, it is set to use Oslo time by default.

```bash
make build # will build the tf2-express docker image and install dependencies
make run # will start mongodb and tf2-express
```

The GUI does not start automatically. To start the GUI run this:

```bash
make gui
```

The GUI will then be available at http://127.0.0.1:5000/

## Explanation
### Random Craft Hats
If a craftable hat does not have a specific price in the database, it will be viewed as a Random Craft Hat (SKU: -100;6), if `enable_craft_hats` is enabled. 

> [!CAUTION]
> This applies to any craftable unique hat, which includes hats such as The Team Captain, Earbuds, Max Heads etc. If these do not have their own price in the database, they will be priced as a Random Craft Hat, if this option is enabled. Be careful when using this option, as it can lead to unwanted trades.

Simply open the GUI and add "Random Craft Hat" or `-100;6` to the pricelist. Set the buy and sell price to whatever you want. Random Craft Hats cannot get automatic price updates.

### Arbitrage
"Arbitraging is the process of taking advantage of a price difference between two or more markets". In this case, it is used to buy items from one trade site and sell to another for profit. `tf2-express` can act on deals from [`tf2-arbitrage`](https://github.com/offish/tf2-arbitrage) and send or receive offers.

> [!IMPORTANT]
> As of tf2-express v3.0.0 arbitrage is currently broken.

### 3rd Party Inventory Providers
Steam can rate-limit inventory fetch requests if they are called too often. This can be avoided using a third party provider like SteamApis, Steam.Supply, Express-Load or your own. This is especially useful if you are running multiple bots.

> [!NOTE]
> If you want to use [Express-Load](https://express-load.com/) you can use the promo code `offish` to receive free credits and try out their API for free.

## Testing
```bash
# tf2-express/
pytest
```

Every test should succeed except for the version check. The version needs to be incremented to pass this test.

## License
MIT License

Copyright (c) 2020-2025 offish ([confern](https://steamcommunity.com/id/confern))

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
