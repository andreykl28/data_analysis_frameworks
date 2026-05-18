# Лабораторная работа №2 — Временные ряды

Лабораторная работа по анализу и прогнозированию многомерных временных рядов погодных данных.

Используется Excel-файл `data/have_fun.xlsx` с данными температуры для нескольких городов. Файл содержит скрытые листы, ошибки ручного ввода, пропуски, выбросы и проблемы форматирования — всё это требуется восстановить.

## Что сделано

Загрузка данных: через openpyxl получен доступ ко всем листам (включая скрытые), данные объединены в единый DataFrame.

Очистка: исправлена кодировка кириллицы, удалены мусорные столбцы, обработаны пропуски (интерполяция + ffill/bfill), удалены выбросы по физическим диапазонам и дубликаты.

Анализ временного ряда: визуализация на разных частотах, STL-декомпозиция (тренд + сезонность + остаток), ACF и PACF, проверка стационарности (ADF + KPSS), спектральный анализ (FFT).

Feature engineering: календарные признаки (sin/cos кодирование часа и дня года), one-hot кодирование городов, лаги температуры (от 1ч до 168ч), rolling-статистики (mean, std, min, max), лаги погодных параметров, разности температуры.

Модели: DecisionTree, RandomForest, CatBoost. Гиперпараметры подобраны через Optuna.

Стратегии прогноза:
- **Recursive** — модель предсказывает +1 час, результат подаётся на следующий шаг (168 итераций)
- **Direct** — отдельная модель для каждого горизонта (1, 24, 72, 120, 168 часов)

Оценка: MAE, MAPE, WAPE, Directional Accuracy, Directional R² на тестовом отрезке (2025 год).

## Файлы

- `notebook/notebook.ipynb` — основной ноутбук с полным пайплайном.
- `data/have_fun.xlsx` — исходный Excel-файл с данными.

## Требования

- Python 3.10 или новее.
- Установленные библиотеки: pandas, numpy, matplotlib, seaborn, scipy, statsmodels, scikit-learn, openpyxl, optuna, catboost, jupyter.

## Запуск

1. Перейти в папку проекта:

```bash
cd lab_2
```

2. Создать и активировать виртуальное окружение:

```bash
python -m venv .venv
source .venv/bin/activate
```

Для Windows:

```bash
.venv\Scripts\activate
```

3. Установить зависимости:

```bash
python -m pip install --upgrade pip
python -m pip install pandas numpy matplotlib seaborn scipy statsmodels scikit-learn openpyxl optuna catboost jupyter ipykernel
```

4. Добавить виртуальное окружение как kernel для Jupyter:

```bash
python -m ipykernel install --user --name lab_2 --display-name "Python (lab_2)"
```

5. Запустить Jupyter Notebook:

```bash
jupyter notebook
```

6. Открыть файл `notebook/notebook.ipynb`, выбрать kernel `Python (lab_2)` и выполнить все ячейки через `Run All`.

## Примечания

- Первый запуск может быть долгим (5-15 минут) из-за подбора гиперпараметров через Optuna (85 trial'ов) и recursive-прогноза на 168 часов для каждого города.
- Если появляется ошибка `ModuleNotFoundError`, убедитесь, что выбран kernel `Python (lab_2)`, созданный из виртуального окружения `.venv`.