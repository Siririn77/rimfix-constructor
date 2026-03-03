# RimFix — Конфигуратор дисков

## Обзор проекта

**RimFix** — это веб-приложение для визуальной настройки автомобильных дисков. Позволяет пользователю выбрать тип кузова автомобиля, установить диски и изменить их цвет с помощью палитры预设 или пользовательского цвета.

### Основные возможности

- **Выбор кузова**: 8 типов (седан, хэтчбек, купе, кабриолет, кроссовер, минивэн, пикап, универсал)
- **Выбор дисков**: Несколько вариантов колёсных дисков
- **Настройка цвета**: 18预设 цветов + пользовательский цвет через color picker
- **Автоматическое обнаружение**: OpenCV.js использует template matching для поиска позиций колёс на изображении кузова
- **Адаптивный дизайн**: Полноценная работа на desktop и mobile устройствах

### Технологии

| Компонент | Технология |
|-----------|------------|
| Frontend | Vanilla HTML/CSS/JavaScript |
| Computer Vision | OpenCV.js |
| Шрифты | Google Fonts (Bebas Neue, Barlow Condensed, Barlow) |
| Сервер | Python HTTP Server |

### Структура проекта

```
V1.Qwen-Code/
├── wheel_configurator.html    # Основное приложение (HTML + CSS + JS)
├── opencv.js                  # Библиотека OpenCV.js (10MB+)
├── run_server.py              # Python-скрипт для запуска локального сервера
├── car_types/                 # Изображения типов кузова (8 PNG)
│   ├── sedan.png, hatchback.png, coupe.png, cabriolet.png
│   ├── crossover.png, minivan.png, pickup.png, wagon.png
├── wheel_types/               # Изображения дисков (2 PNG)
│   ├── wheel1.png, wheel2.png
├── other_assets/              # Дополнительные ассеты
│   └── replace_part.png       # Шаблон для template matching
└── README.md                  # Краткое описание проекта
```

## Запуск проекта

### Требования

- Python 3.x
- Современный браузер с поддержкой ES6+ и WebAssembly (для OpenCV.js)

### Команды

```bash
# Запуск локального сервера (порт 8000)
python run_server.py

# После запуска откройте в браузере:
# http://localhost:8000/wheel_configurator.html
```

### Особенности сервера

`run_server.py` настраивает:
- CORS заголовки (`Access-Control-Allow-Origin: *`)
- Отключение кэширования (для разработки)
- Статическую раздачу файлов из корня проекта

## Архитектура приложения

### JavaScript модули

1. **Data** — массивы конфигурации (`CAR_TYPES`, `WHEEL_TYPES`, `PALETTE`)
2. **State** — глобальное состояние (`selectedCar`, `selectedWheel`, `activeTintHex`)
3. **OpenCV** — инициализация и template matching для поиска колёс
4. **Tinting** — наложение цвета на диски с учётом освещённости (luminance-based)
5. **UI Sync** — синхронизация состояния цвета между desktop sidebar и mobile sheets

### Алгоритм наложения дисков

1. Загрузка изображения кузова и шаблона (`replace_part.png`)
2. Анализ шаблона для определения центра и радиуса
3. Template matching с масштабированием 0.5x для производительности
4. Поиск топ-2 совпадений (левое и правое колесо)
5. Отрисовка тонированного изображения диска на найденных позициях

### Адаптивность

- **Desktop (>740px)**: Sidebar слева + preview area справа
- **Mobile (≤740px)**: Canvas сверху + tab bar снизу + bottom sheets для выбора

## Переменные CSS

```css
--bg: #0c0d0f;          /* Основной фон */
--surface: #141518;     /* Фон панелей */
--accent: #18df61;      /* Акцентный зелёный */
--red: #e53935;         /* Ошибки */
--green: #22c55e;       /* Успех */
--f-display: 'Bebas Neue';
--f-ui: 'Barlow Condensed';
--f-body: 'Barlow';
```

## Расширение функционала

### Добавление нового типа кузова

1. Добавить PNG в `car_types/`
2. Добавить запись в `CAR_TYPES`:
```javascript
{ id:'new_type', name:'Название', src:'car_types/new_type.png' }
```
3. При необходимости настроить `CAR_ADJ` для коррекции позиции

### Добавление нового цвета

Добавить в массив `PALETTE`:
```javascript
{ id:'custom_id', name:'Название', hex:'#RRGGBB' }
```

### Добавление новых дисков

1. Добавить PNG в `wheel_types/`
2. Добавить запись в `WHEEL_TYPES`

## Известные ограничения

- OpenCV.js требует времени на инициализацию (~2-5 сек)
- Template matching может не найти колёса на нестандартных изображениях
- Требуется CORS-friendly сервер для загрузки изображений
