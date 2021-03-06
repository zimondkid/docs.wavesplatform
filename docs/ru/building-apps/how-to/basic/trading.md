---
sidebarDepth: 2
---

# Как купить или продать токены

Любой ассет, выпущенный на блокчейне Waves (кроме [NFT](/ru/blockchain/token/non-fungible-token)), можно продать или купить на бирже [Waves.Exchange](https://waves.exchange/). Приложение Waves.Exchange, разработанное командой Waves.Exchange, является частью экосистемы Waves и включает пользовательский кошелек и децентрализованную биржу, которая выполняет сделки быстро и надежно.

Чтобы продать или купить токены, нужно отправить ордер (биржевую заявку) на матчер (движок биржи). При этом вы не передаете токены на биржу — они остаются на вашем аккаунте до момента, когда матчер выполнит заявку и создаст транзакцию обмена. Блокчейн гарантирует, что условия обмена будут не хуже, чем указаны в заявке.

Подробнее см. в разделе [Биржевая заявка](/ru/blockchain/order).

## Создание ордера

### С помощью Waves.Exchange

Используйте online/desktop- или мобильное приложение. См. разделы [Торговля на бирже (online/desktop-приложение)](https://docs.waves.exchange/ru/waves-exchange/waves-exchange-online-desktop/online-desktop-trading) и [Торговля на бирже (мобильные приложения)](https://docs.waves.exchange/ru/waves-exchange/waves-exchange-mobile/mobile-trading/mobile-start-trading) документации Waves.Exchange.

### С помощью JavaScript

#### Установка параметров матчера

Используйте следующий адрес матчера:

* Для Testnet: <https://matcher.testnet.wavesnodes.com>
* Для Mainnet: <https://matcher.waves.exchange>

Чтобы получить публичный ключ матчера, используйте метод `GET /matcher` API матчера.

#### Установка ассетной пары

Ассетную пару образуют два ассета, которые вы хотите обменять: amount-ассет и price-ассет. Который из двух ассетов является price-ассетом, не зависит от того, какой ассет вы отдаете и какой получаете.

На Mainnet вы можете посмотреть ассетные пары и идентификаторы ассетов в Waves.Exchange на странице **Торговля**. Первый ассет в паре — это amount-ассет, второй — price-ассет.

![](./_assets/asset-pair.png)

Как на Mainnet, так и на Testnet вы можете получить ассетные пары с помощью метода `GET /matcher/orderbook` или `GET /matcher/settings`. Подробнее см. раздел [Matcher API](https://docs.waves.exchange/ru/waves-matcher/matcher-api) документации Waves.Exchange.

> :warning: Идентификаторы ассетов отличаются на Mainnet и на Testnet.

WAVES — главный токен платформы Waves — не имеет идентификатора, вместо ID нужно указывать значение 'WAVES'.

#### Заполнение параметров ордера, подписание и отправка на матчер

Используйте функции библиотеки `waves-transactions`:

* функция `order` создает ордер и генерирует подпись. Для генерации подписи используется секретная фраза (seed) аккаунта. По умолчанию комиссия матчера рассчитывается автоматически.
* функция `submitOrder` отправляет подписанный ордер на матчер.

Описание функций приведено в [документации библиотеки](https://wavesplatform.github.io/waves-transactions/index.html) на Github.

```javascript
import { order, submitOrder } from "@waves/waves-transactions";

const matcherUrl = 'https://matcher.testnet.wavesnodes.com';
const matcherPublicKey: '8QUAqtTckM5B8gvcuP7mMswat9SjKUuafJMusEoSn1Gy';

const amountAssetId: 'BrmjyAWT5jjr3Wpsiyivyvg5vDuzoX2s93WgiexXetB3'; // Идентификатор ETH на Testnet
const priceAssetId: 'WAVES';

const seed = 'insert your seed here';

const orderParams = {
    amount: 100000000, // 1 ETH: фактическое количество amount-ассета нужно умножить на 10^amountAssetDecimals
    price: 19900000000, // 199 WAVES за один 1 ETH: цену, выраженную в price-ассете, нужно умножить на 10^(8 + priceAssetDecimals – amountAssetDecimals)                                             
    amountAsset: amountAssetId,
    priceAsset: priceAssetId,
    matcherPublicKey: matcherPublicKey,
    orderType: 'buy'
}

const signedOrder = order(orderParams, seed);
await submitOrder(signedOrder, matcherUrl);

let orderId = signedOrder.id;
console.log('ID ордера: '+ orderId);
```

### С помощью Python

```python
import pywaves as pw

matcher_url = 'https://matcher.testnet.wavesnodes.com'
pw.setMatcher(matcher_url)

# ETH на Testnet
amount_asset = pw.Asset('BrmjyAWT5jjr3Wpsiyivyvg5vDuzoX2s93WgiexXetB3')

# WAVES
price_asset = pw.Asset('')

asset_pair = pw.AssetPair(amount_asset, price_asset)

my_address = pw.Address(privateKey='some_private_key')

buy_order = my_address.buy(asset_pair=asset_pair, amount=1e8, price=50e8)

print(f'Buy order ID: {buy_order.orderId}')
```

## Проверка статуса ордера

### С помощью Waves.Exchange

Размещенный ордер отображается на вкладке **Мои открытые ордера** в online/desktop-приложении или на вкладке **Мои ордера** в мобильном приложении. См. разделы [Торговля на бирже (online/desktop-приложение)](https://docs.waves.exchange/ru/waves-exchange/waves-exchange-online-desktop/online-desktop-trading) и [Торговля на бирже (мобильные приложения)](https://docs.waves.exchange/ru/waves-exchange/waves-exchange-mobile/mobile-trading/mobile-start-trading) документации Waves.Exchange.

### С помощью API матчера

Чтобы получить статус ордера, достаточно значть его идентификатор и ассетную пару. Используйте метод `GET /matcher/orderbook/{amountAsset}/{priceAsset}/{orderId}`. Получение статуса доступно для ордеров, размещенный не более 30 дней назад. Для частично выполненных ордеров метод также возвращает сумму выполненной части.

Описание метода приведено в разделе [Matcher API](https://docs.waves.exchange/ru/waves-matcher/matcher-api) документации Waves.Exchange.

**Пример запроса:**

```
curl 'https://matcher.testnet.wavesnodes.com/matcher/orderbook/BrmjyAWT5jjr3Wpsiyivyvg5vDuzoX2s93WgiexXetB3/WAVES/6hgoJMKAMPVZb11epd2vCjqk47dGcr9eT8cJQ2HpYnHp'
```

Приведенный пример подходит для утилиты `cURL`. Вы можете адаптировать запрос для своего языка программирования.

### С помощью JavaScript

```javascript
const matcherUrl = 'https://matcher.testnet.wavesnodes.com';

const amountAssetId: 'BrmjyAWT5jjr3Wpsiyivyvg5vDuzoX2s93WgiexXetB3'; // Идентификатор ETH на Testnet
const priceAssetId: 'WAVES';

const orderId= '6hgoJMKAMPVZb11epd2vCjqk47dGcr9eT8cJQ2HpYnHp';

let response = await fetch(matcherUrl + '/matcher/orderbook/' + amountAsset + '/' + priceAsset + '/' + orderId);
let json = await response.json();
console.log('Статус ордера: ' + json.status);
```

### С помощью Python

```python
# используем ордер из предыдущего примера
print(buy_order.status())
```

## Отмена ордера

Вы можете отменить ордер, если он еще не выполнен полностью.

### С помощью Waves.Exchange

Вы можете отменить ордер:

* В online/desktop-приложении: нажмите **Отмена** на вкладке **Мои открытые ордера**.
* В мобильном приложении: Note: нажмите **X** на вкладке **Мои ордера**.

### С помощью JavaScript

Запрос на отмену ордера должен быть подписан отправителем ордера.

Используйте функции библиотеки `waves-transactions`:

* функция `cancelOrder` создает и подписывает запрос на отмену ордера.
* функция `cancelSubmittedOrder` отправляет подписанный запрос на матчер.

Описание функций см. в [документации библиотеки](https://wavesplatform.github.io/waves-transactions/index.html) на Github.

**Пример:**

```javascript
import {cancelOrder, cancelSubmittedOrder } from "@waves/waves-transactions";

const matcherUrl = 'https://matcher.testnet.wavesnodes.com';

const amountAssetId: 'BrmjyAWT5jjr3Wpsiyivyvg5vDuzoX2s93WgiexXetB3'; // Идентификатор ETH на Testnet
const priceAssetId: 'WAVES';

const seed = 'insert your seed here';
const orderId= '6hgoJMKAMPVZb11epd2vCjqk47dGcr9eT8cJQ2HpYnHp';

const co = cancelOrder({ orderId: orderId }, seed);
const cancelledOrder = await cancelSubmittedOrder(co, amountAsset, priceAsset, matcherUrl);

console.log(cancelledOrder.status);
```

### С помощью Python

```python
# используем ордер из предыдущего примера
buy_order.cancel()
```
