# ESP DIV CYD Web Flasher

## Структура репозитория

```
index.html
versions.json
firmware/
  1.0/
    esp-div-cyd-1.0.bin   <- ваш merged-бинарник
  1.1/
    esp-div-cyd-1.1.bin
```

## Как собрать один merged .bin (адрес 0x0)

Web-флешер пишет один файл с нулевого адреса, поэтому загрузчик, разделы и
приложение надо склеить. Для PlatformIO / Arduino-сборки:

```
esptool.py --chip esp32 merge_bin -o esp-div-cyd-1.0.bin \
  --flash_mode dio --flash_freq 40m --flash_size 4MB \
  0x1000 bootloader.bin \
  0x8000 partitions.bin \
  0xe000 boot_app0.bin \
  0x10000 firmware.bin
```

Если ваш `.bin` уже merged (в PlatformIO: `pio run -t mergebin`), используйте его как есть.

## Как выложить, чтобы им могли пользоваться все

Нужен HTTPS, иначе браузер не даст доступ к USB-порту.

### Вариант 1. GitHub Pages (бесплатно, проще всего)

1. Создайте публичный репозиторий, залейте туда эти файлы и папку `firmware/`.
2. Settings -> Pages -> Source: `main` / `root`.
3. Сайт откроется на `https://ВАШ_НИК.github.io/ИМЯ_РЕПО/`.

### Вариант 2. Cloudflare Pages / Netlify

Перетащите папку в панель, получите HTTPS-адрес.

## Как добавить новую версию

1. Положите файл в `firmware/1.1/esp-div-cyd-1.1.bin`.
2. Добавьте блок в `versions.json`:

```json
{
  "version": "1.1",
  "date": "2026-12-01",
  "file": "firmware/1.1/esp-div-cyd-1.1.bin",
  "offset": 0,
  "notes": "Что изменилось."
}
```

3. Закоммитьте. Версия появится в выпадающем списке сразу.

Версию можно открыть напрямую по ссылке: `...?v=1.1`.

## Проверка локально

Web Serial работает на `localhost`:

```
python -m http.server 8000
```

Откройте `http://localhost:8000` в Chrome.

## Если файл лежит не на вашем домене

Другой домен должен отдавать заголовок `Access-Control-Allow-Origin: *`.
GitHub Pages и Releases-ссылки через raw.githubusercontent.com это делают.
Проще всего хранить бинарники рядом с сайтом.
