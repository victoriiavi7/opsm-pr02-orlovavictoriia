# opsm-pr02-orlovavictoriia
# Практична робота № 2

**Дисципліна:** Основи побудови інформаційних систем та мереж (ОК-13)

**Тема:** Структура повідомлень прикладного протоколу HTTP. Формування запиту вручну

| Поле | Значення |
|---|---|
| Студент (прізвище, ім'я, по батькові) | Орлова Вікторія |
| Група | F5 2.02 |
| Номер варіанта | 18 |
| Індивідуальний домен | `ietf.org` |
| «Чужий» домен для завдання A.3.1 (варіант ± 15) | `haproxy.org` |
| Середовище виконання | Linux (Kali Linux) |
| Дата виконання | 02.10.2026 |

> Бланк заповнюють, не змінюючи структури розділів. Порожні заготовки блоків коду замінюють власними виводами. Позначки-підказки в кутових дужках вилучають.

---

## Частина A. Збір експериментальних даних

### Завдання A.1. Формування запиту вручну

**Команда:**

```text
nc -C ietf.org 80
```

**Набраний запит:**

```http
GET / HTTP/1.1
Host: ietf.org
Connection: close

```

**Відповідь:**

```text
HTTP/1.1 301 Moved Permanently
Date: Fri, 02 Oct 2026 18:46:53 GMT
Content-Type: text/html; charset=UTF-8
Transfer-Encoding: chunked
Connection: close
Location: https://www.ietf.org/
set-cookie: __cf_bm=5z9e4MMVK0K4FWfBcm_qkN9TGKM7tjzRvSW7ZjgkbnQ-1790966813.5954068-1.0.1.1-ePuM.oNvrD5IBvhde75M0nSiDBTU5ZAcSBOeq2BUgo9fxmdIzhGlViLrmfEv0weGMTLkoUDsnYS82284TJHlav9wEemYG2ODeorwJoQoBdnb08LO2uFK9CKUOVXXIA7K; HttpOnly; Path=/; Domain=ietf.org; Expires=Fri, 02 Oct 2026 19:16:53 GMT
Server: cloudflare
CF-RAY: a445df273f762494-KBP
alt-svc: h3=":443"; ma=86400

a7
<html>
<head><title>301 Moved Permanently</title></head>
<body>
<center><h1>301 Moved Permanently</h1></center>
<hr><center>cloudflare</center>
</body>
</html>
0
```

---

### Завдання A.2. Запит без поля `Host` у версії 1.1

**Команда:**

```bash
printf 'GET / HTTP/1.1\r\nConnection: close\r\n\r\n' | nc ietf.org 80
```

**Вивід:**

```text
HTTP/1.1 400 Bad Request
Server: cloudflare
Date: Fri, 02 Oct 2026 18:51:53 GMT
Content-Type: text/html
Content-Length: 155
Connection: close
CF-RAY: -

<html>
<head><title>400 Bad Request</title></head>
<body>
<center><h1>400 Bad Request</h1></center>
<hr><center>cloudflare</center>
</body>
</html>
```

---

### Завдання A.3. Вплив поля `Host` на відповідь сервера

#### A.3.1. Чуже доменне ім'я в полі `Host`

**Команда:**

```bash
printf 'GET / HTTP/1.1\r\nHost: haproxy.org\r\nConnection: close\r\n\r\n' | nc ietf.org 80
```

**Вивід:**

```text
HTTP/1.1 409 Conflict
Date: Fri, 02 Oct 2026 18:56:25 GMT
Content-Type: text/plain; charset=UTF-8
Content-Length: 16
Connection: close
X-Frame-Options: SAMEORIGIN
Referrer-Policy: same-origin
Cache-Control: private, max-age=0, no-store, no-cache, must-revalidate, post-check=0, pre-check=0
Expires: Thu, 01 Jan 1970 00:00:01 GMT
Server: cloudflare
CF-RAY: a445ed4e8ee177bb-KBP

error code: 1001
```

#### A.3.2. Неіснуюче ім'я в полі `Host`

**Команда:**

```bash
printf 'GET / HTTP/1.1\r\nHost: opism-pr02.invalid\r\nConnection: close\r\n\r\n' | nc ietf.org 80
```

**Вивід:**

```text
HTTP/1.1 409 Conflict
Date: Fri, 02 Oct 2026 18:58:01 GMT
Content-Type: text/plain; charset=UTF-8
Content-Length: 16
Connection: close
X-Frame-Options: SAMEORIGIN
Referrer-Policy: same-origin
Cache-Control: private, max-age=0, no-store, no-cache, must-revalidate, post-check=0, pre-check=0
Expires: Thu, 01 Jan 1970 00:00:01 GMT
Server: cloudflare
CF-RAY: a445efaa0d2e24ac-KBP

error code: 1001
```

#### A.3.3. Запит без поля `Host` у версії 1.0

**Команда:**

```bash
printf 'GET / HTTP/1.0\r\n\r\n' | nc ietf.org 80
```

**Вивід:**

```text
HTTP/1.1 403 Forbidden
Date: Fri, 02 Oct 2026 18:59:22 GMT
Content-Length: 57
Connection: close
Cache-Control: private, max-age=0, no-store, no-cache, must-revalidate, post-check=0, pre-check=0
Referrer-Policy: same-origin
Expires: Thu, 01 Jan 1970 00:00:01 GMT
CF-RAY: a445f1a0fe4a2313-KBP

Cloudflare encountered an error processing this request:
```

Зведення результатів наведено в **Додатку Д**.

---

### Завдання A.4. Два запити в одному з'єднанні

**Команда:**

```bash
printf 'GET /opism-pr02-12345 HTTP/1.1\r\nHost: ietf.org\r\n\r\nGET / HTTP/1.1\r\n Host: ietf.org\r\nConnection: close\r\n\r\n' | nc -C ietf.org 80
```

**Вивід:**

```text
HTTP/1.1 301 Moved Permanently
Date: Fri, 02 Oct 2026 19:02:37 GMT
Content-Type: text/html; charset=UTF-8
Transfer-Encoding: chunked
Connection: keep-alive
Location: https://www.ietf.org/opism-pr02-12345
set-cookie: __cf_bm=TFyJpl_FiXkrQJ_9wAVS9MRgrGvZv8.cG7hdj0.0S54-1790967757.111101-1.0.1.1-9ym8avfIyWOgP0ouJ5iqieK_bxveqYzRDCq6oha0KZvWfg1PKRnD00X5dFhS5raBKu175a9p0OSbF4KQF0BeuRHUk3lMqe9JB1W0sUOYDkiJWj4CjUNULTBPQMpN4HfL; HttpOnly; Path=/; Domain=ietf.org; Expires=Fri, 02 Oct 2026 19:32:37 GMT
Server: cloudflare
CF-RAY: a445f661ec3c2313-KBP
alt-svc: h3=":443"; ma=86400

a7
<html>
<head><title>301 Moved Permanently</title></head>
<body>
<center><h1>301 Moved Permanently</h1></center>
<hr><center>cloudflare</center>
</body>
</html>
0

HTTP/1.1 400 Bad Request
Server: cloudflare
Date: Fri, 02 Oct 2026 19:02:37 GMT
Content-Type: text/html
Content-Length: 155
Connection: close
CF-RAY: -

<html>
<head><title>400 Bad Request</title></head>
<body>
<center><h1>400 Bad Request</h1></center>
<hr><center>cloudflare</center>
</body>
</html>
```

**Кількість отриманих відповідей:** 2

**Коди стану отриманих відповідей:** `301 Moved Permanently`, `400 Bad Request`

---

### Завдання A.5. Запит за допомогою клієнтської програми

**Команда:**

```bash
curl -v --http1.1 http://ietf.org/ -o /dev/null
```

**Вивід:**

```text
* Host ietf.org:80 was resolved.
* IPv6: (none)
* IPv4: 104.16.45.99
*   Trying 104.16.45.99:80...
* Established connection to ietf.org (104.16.45.99 port 80) from 172.16.229.131 port 41716
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0* using HTTP/1.x
> GET / HTTP/1.1
> Host: ietf.org
> User-Agent: curl/8.21.0
> Accept: */*
>
* Request completely sent off
< HTTP/1.1 301 Moved Permanently
< Date: Fri, 02 Oct 2026 19:03:54 GMT
< Content-Type: text/html; charset=UTF-8
< Transfer-Encoding: chunked
< Connection: keep-alive
< Location: https://www.ietf.org/
< set-cookie: __cf_bm=5.tgTnHUxuFi8e6tMwsKwXloLPaNbVZuuxBcxGPbFlc-1790967834.2122405-1.0.1.1-gTPfiMt.w8wrmZyEZ_39W._hwDO_5BErfHYD0P_wlYv.XSoQ5iJIPynL8VRMU__i501uRQgl_4Qc01_JN8sQB0fSjmzStDwm9nTvxIoRvDSzSx7Z9001ZHVowTYU8H6u; HttpOnly; Path=/; Domain=ietf.org; Expires=Fri, 02 Oct 2026 19:33:54 GMT
< Server: cloudflare
< CF-RAY: a445f843cb57c918-KBP
< alt-svc: h3=":443"; ma=86400
<
{ [178 bytes data]
100   167    0   167    0     0   2750      0 --:--:-- --:--:-- --:--:--     0
* Connection #0 to host ietf.org:80 left intact
```

---

### Завдання A.6. Запит через захищене з'єднання

**Ресурс, на якому виконано завдання:** власний домен `ietf.org`

**Підстава для використання резервного ресурсу (заповнюють за потреби):** резервний ресурс не використовувався, оскільки у завданні A.1 отримано код класу `3xx` і TLS-з'єднання з `ietf.org:443` успішно встановилося.

**Команда:**

```bash
openssl s_client -connect ietf.org:443 -servername ietf.org -crlf -quiet
```

**Набраний запит:**

```http
GET / HTTP/1.1
Host: ietf.org
Connection: close

```

**Вивід:**

```text
Connecting to 104.16.44.99
depth=2 C=US, O=Google Trust Services LLC, CN=GTS Root R4
verify return:1
depth=1 C=US, O=Google Trust Services, CN=WE1
verify return:1
depth=0 CN=ietf.org
verify return:1
GET / HTTP/1.1
Host: ietf.org
Connection: close
HTTP/1.1 301 Moved Permanently
Date: Fri, 02 Oct 2026 19:09:06 GMT
Content-Type: text/html; charset=UTF-8
Transfer-Encoding: chunked
Connection: close
Location: https://www.ietf.org/
set-cookie: __cf_bm=Be.YdnNUNrCwtH9l.DhDx1I0hSFEIYg2IBmQfcBrpRQ-1790968146.5078392-1.0.1.1-LiE8NRYjAlOX5aJlBnUbC9_1fl6kK78v0h1RueoHqVew9Ra2RpsEl6eODSzsgvogPvD417Tn0JERTKR36GzZF7.SVaOsGQJMXnBY3.ZJuDdsPOAeVm3LAaUWkCzxaRFg; HttpOnly; Secure; Path=/; Domain=ietf.org; Expires=Fri, 02 Oct 2026 19:39:06 GMT
Server: cloudflare
CF-RAY: a445ffad2c448f2b-VIE
alt-svc: h3=":443"; ma=86400

a7
<html>
<head><title>301 Moved Permanently</title></head>
<body>
<center><h1>301 Moved Permanently</h1></center>
<hr><center>cloudflare</center>
</body>
</html>
0
```

---

## Частина B. Розбір полів заголовка

Розбирається відповідь, отримана в завданні A.1.

**Загальна кількість полів заголовка у відповіді:** 9

| № | Поле заголовка | Значення | Призначення (власне формулювання) | Походження: сервер / проміжний вузол / не визначено | Обґрунтування |
|---|---|---|---|---|---|
| 1 | `Date` | `Fri, 02 Oct 2026 18:46:53 GMT` | Вказує дату й час формування HTTP-відповіді. | не визначено | Із самого виводу неможливо однозначно встановити, чи це значення сформував кінцевий сервер, чи Cloudflare. |
| 2 | `Content-Type` | `text/html; charset=UTF-8` | Описує тип тіла відповіді та кодування тексту. | не визначено | Поле є стандартним і могло бути сформоване як кінцевим сервером, так і проміжним вузлом, що повернув відповідь. |
| 3 | `Transfer-Encoding` | `chunked` | Показує, що тіло передається частинами, а не з наперед заданим `Content-Length`. | не визначено | За одним виводом не можна достовірно визначити, на якому вузлі було обрано chunked-передавання. |
| 4 | `Connection` | `close` | Вказує, що TCP-з'єднання буде закрито після надсилання відповіді. | не визначено | Це поле могло бути сформоване або змінене HTTP-вузлом, який безпосередньо обробляв з'єднання. |
| 5 | `Location` | `https://www.ietf.org/` | Задає адресу, на яку клієнтові слід перейти після відповіді `301`. | не визначено | Перенаправлення могло бути налаштоване як на стороні IETF, так і на проміжному вузлі Cloudflare; вивід не дозволяє розрізнити ці варіанти. |
| 6 | `set-cookie` | `__cf_bm=...; HttpOnly; Path=/; Domain=ietf.org; Expires=Fri, 02 Oct 2026 19:16:53 GMT` | Передає клієнтові cookie, яке буде використовуватися в наступних запитах до домену. | проміжний вузол | Ім'я cookie `__cf_bm` має позначення `cf`, а в цій самій відповіді присутні `Server: cloudflare` і `CF-RAY`, що пов'язує його з Cloudflare. |
| 7 | `Server` | `cloudflare` | Повідомляє назву програмного або мережевого вузла, який повернув HTTP-відповідь клієнтові. | проміжний вузол | Значення поля прямо вказує `cloudflare`, а не сервер IETF. |
| 8 | `CF-RAY` | `a445df273f762494-KBP` | Містить ідентифікатор обробки запиту в мережі Cloudflare, корисний для трасування та діагностики. | проміжний вузол | Назва поля специфічна для Cloudflare та узгоджується з `Server: cloudflare`. |
| 9 | `alt-svc` | `h3=":443"; ma=86400` | Повідомляє клієнтові про альтернативний сервіс: у цьому випадку можливість використати HTTP/3 на порту 443 протягом указаного часу. | проміжний вузол | У відповіді явно присутній Cloudflare, який обслуговує клієнтське з'єднання; висновок зроблено за сукупністю полів `Server` і `CF-RAY`. |

---

## Частина D. Висновки

Обсяг — 150–300 слів. Висновки спираються на власні спостереження.

**D.1.** Найбільш неочевидною виявилася реакція вузла на зміну поля `Host`. Для правильного `Host: ietf.org` у A.1 було отримано рядок `HTTP/1.1 301 Moved Permanently`, тоді як у A.3.1 з `Host: haproxy.org` і в A.3.2 з `Host: opism-pr02.invalid` сервер в обох випадках повернув `HTTP/1.1 409 Conflict` та однакове тіло `error code: 1001`. Отже, встановлення TCP-з'єднання саме з `ietf.org` ще не означає, що будь-яке значення `Host` буде прийняте. Неочікуваним був і результат A.3.3: запит HTTP/1.0 без `Host` не дав `400`, як A.2, а завершився рядком `HTTP/1.1 403 Forbidden`. У A.4 перший запит до неіснуючого шляху все одно отримав `301`, що показує перенаправлення HTTP-трафіку на HTTPS незалежно від перевірки існування цього шляху на даному етапі.

**D.2.** Найбільше утруднення викликало поле `Location`. Із самого виводу видно лише значення `https://www.ietf.org/`, але не видно, де саме створено правило перенаправлення: на кінцевому сервері IETF чи на проміжному вузлі Cloudflare. Тому його походження позначено як «не визначено», на відміну від `CF-RAY` і `Server: cloudflare`, для яких ознака проміжного вузла явна.

**D.3.** Після роботи залишилося питання, на якому саме рівні інфраструктури налаштовано перенаправлення з `http://ietf.org/` на `https://www.ietf.org/`: безпосередньо на origin-сервері IETF чи в конфігурації Cloudflare. Наявні виводи показують результат, але не дають однозначно встановити місце цього правила.

---

## Контрольні питання

**1.** У завданні A.1 сервер не надсилав відповіді, доки не було введено порожній рядок. Чим це зумовлено?

Порожній рядок завершує секцію заголовків HTTP-запиту. До отримання послідовності `CRLF CRLF` сервер не може бути впевнений, що клієнт закінчив передавати поля заголовка і що запит уже можна обробляти. У A.1 після рядків `GET / HTTP/1.1`, `Host: ietf.org` і `Connection: close` був введений ще один порожній рядок, після чого надійшла відповідь `HTTP/1.1 301 Moved Permanently`.

**2.** Порівняйте результати завдань A.1, A.2 та A.3.1–A.3.3 (таблиця Додатка Д). За яких значень поля `Host` і за якої версії протоколу сервер обслуговує запит, а за яких — ні? Яку задачу розв'язує поле `Host`? Відповідь має посилатися на конкретні рядки ваших виводів.

У A.1 запит HTTP/1.1 з правильним `Host: ietf.org` був оброблений і дав `HTTP/1.1 301 Moved Permanently`. У A.2 HTTP/1.1 без `Host` дав `HTTP/1.1 400 Bad Request`. У A.3.1 значення `Host: haproxy.org` і в A.3.2 `Host: opism-pr02.invalid` дали однаковий результат `HTTP/1.1 409 Conflict` з рядком `error code: 1001`. У A.3.3 HTTP/1.0 без поля `Host` повернув `HTTP/1.1 403 Forbidden`, тобто запит також не привів до отримання цільового ресурсу. Отже, для перевіреного вузла правильне значення `Host` визначає, який логічний вебвузол потрібно обслуговувати на спільній мережевій адресі. Поле особливо важливе в HTTP/1.1, де його відсутність у цьому експерименті призвела до `400 Bad Request`.

**3.** Скільки відповідей надійшло у завданні A.4 і з якими кодами стану? Чи залежить відповідь сервера на порту 80 від запитаного шляху — і що це говорить про роль цього сервера? Якщо надійшла одна відповідь, знайдіть у ній поле заголовка, яке це пояснює, або зазначте, що такого поля немає. Якщо надійшло дві — що це означає для клієнтської програми, яка завантажує сторінку з великою кількістю вкладених ресурсів?

У A.4 надійшло дві відповіді: `301 Moved Permanently` і `400 Bad Request`. Перший запит був до неіснуючого шляху `/opism-pr02-12345`, але сервер все одно повернув `Location: https://www.ietf.org/opism-pr02-12345`. Це показує, що на порту 80 вузол насамперед виконує роль перенаправлення HTTP-запитів на HTTPS і на цьому етапі не перевіряє існування ресурсу так, як це робив би сервер контенту. Перша відповідь містить `Connection: keep-alive`, тому те саме TCP-з'єднання залишилося доступним для наступного запиту. Друга відповідь має `400 Bad Request`; у фактично виконаній команді перед другим `Host` є початковий пробіл (` Host: ietf.org`), тому другий запит відрізняється від коректної форми і це могло спричинити помилку. Дві відповіді в одному з'єднанні означають, що клієнт може послідовно передавати кілька HTTP/1.1-запитів без встановлення нового TCP-з'єднання для кожного ресурсу, доки з'єднання залишається відкритим.

**4.** Які поля заголовка програма `curl` додала самостійно (завдання A.5)? Ці поля не є обов'язковими — сервер відповів і без них у завданні A.1. З якою метою їх додано?

У A.5 `curl` сформувала рядки `Host: ietf.org`, `User-Agent: curl/8.21.0` і `Accept: */*`. Поле `Host` є необхідним для HTTP/1.1 і в A.1 було введене вручну, тому до додаткових необов'язкових полів належать `User-Agent` та `Accept`. `User-Agent` повідомляє серверу, якою клієнтською програмою зроблено запит, що може бути корисним для сумісності, статистики та діагностики. `Accept: */*` повідомляє, що клієнт готовий прийняти тіло відповіді будь-якого MIME-типу.

**5.** За якими ознаками у вашому виводі виявляється присутність проміжного вузла? Якщо таких ознак не виявлено, поясніть, що з цього випливає.

Присутність проміжного вузла видно за кількома рядками A.1: `Server: cloudflare`, `CF-RAY: a445df273f762494-KBP` і cookie з ім'ям `__cf_bm`. Крім того, HTML-тіло відповіді закінчується рядком `<hr><center>cloudflare</center>`. Сукупність цих ознак показує, що клієнт отримував відповідь через інфраструктуру Cloudflare, а не безпосередньо від origin-сервера IETF. Водночас сам факт наявності Cloudflare не дозволяє за одним виводом точно визначити джерело кожного стандартного поля заголовка, тому для частини полів у частині B походження позначено як «не визначено».

**6.** Три рядки, про які не йшлося на лекції, наведено в **Додатку В**.

---

## Додаток В. Відповіді на питання 6

| № | Рядок виводу | Джерело (номер завдання) |
|---|---|---|
| 1 | `CF-RAY: a445df273f762494-KBP` | A.1 |
| 2 | `alt-svc: h3=":443"; ma=86400` | A.1 |
| 3 | `set-cookie: __cf_bm=5z9e4MMVK0K4FWfBcm_qkN9TGKM7tjzRvSW7ZjgkbnQ-1790966813.5954068-1.0.1.1-ePuM.oNvrD5IBvhde75M0nSiDBTU5ZAcSBOeq2BUgo9fxmdIzhGlViLrmfEv0weGMTLkoUDsnYS82284TJHlav9wEemYG2ODeorwJoQoBdnb08LO2uFK9CKUOVXXIA7K; HttpOnly; Path=/; Domain=ietf.org; Expires=Fri, 02 Oct 2026 19:16:53 GMT` | A.1 |

---

## Додаток Д. Зведення результатів завдання A.3

**Вузол, з яким установлювалося з'єднання (у всіх пробах однаковий):** `ietf.org`

| Проба | Значення поля `Host` | Версія | Код стану | Обсяг тіла відповіді | Збігається з A.1 (так / ні) |
|---|---|---|---|---|---|
| A.1 (вихідна) | `ietf.org` | 1.1 | `301` | 167 байт (`0xA7`, chunked) | — |
| A.2 | поле відсутнє | 1.1 | `400` | 155 байт | ні |
| A.3.1 | `haproxy.org` | 1.1 | `409` | 16 байт | ні |
| A.3.2 | `opism-pr02.invalid` | 1.1 | `409` | 16 байт | ні |
| A.3.3 | поле відсутнє | 1.0 | `403` | 57 байт | ні |

**Висновок за таблицею (2–4 речення):** У пробах змінювалися значення або наявність поля `Host`, а в A.3.3 також версія протоколу з HTTP/1.1 на HTTP/1.0; з'єднання в усіх випадках встановлювалося з `ietf.org`. Правильний `Host: ietf.org` у HTTP/1.1 дав `301`, відсутність `Host` у HTTP/1.1 — `400`, а чуже та неіснуюче значення — `409`. У HTTP/1.0 без `Host` вузол повернув `403`, тому зміна версії протоколу змінила тип помилки, але не дала доступу до цільового ресурсу.

---

## Декларування використання технологій штучного інтелекту

Для цієї роботи встановлено **рівень Р3 — ШІ як співвиконавець**.

Виводи команд частини A не можуть бути згенеровані та мають бути отримані внаслідок фактичного виконання команд.

**Підтвердження:** усі виводи команд, наведені в частині A, отримано внаслідок фактичного виконання команд на зазначеному індивідуальному домені.

---

*ОПІСМ (ОК-13) · Практична робота № 2 · бланк звіту*
