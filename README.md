# Практическое задание для стажировки ВК

Задача: бинарная классификации временных рядов.
Цель проекта: обучить модель машинного обучения для предсказания меток классов на основе временных рядов.

Два основных скрипта:
1. Обучение модели.ipynb:  вычисляет признаки, необходимые для обучения; обучает модель на тренировачных данных и сохраняет обученную модель вместе с масштабировщиком.
2. Основной скрипт для решения.ipynb: вычисляет признаки уже по тестовой выборке и делает предсказания на основе модели из предыдущего скрипта.

Дополнительные файлы:
1.	Анализ датасета.ipynb – содержит общий анализ данных и некоторые выводы о датасете.
2.	submission.csv – ответ на задание – предсказания на тестовой выборке

Инструкция по запуску.
1.	Просмотреть файл Анализ датасета.ipynb
2.	Запустить файл Обучение модели.ipynb. После этого создадутся файлы trained_model.pkl – обученная модель и scaler.pkl-масштабировщик
3.	Запустить файл Основной скрипт для решения.ipynb. После этого создастся файл submission.csv с предсказаниями на тестовой выборке 


## Содержимое файлов

### Обучение модели.ipynb

1. Импорт необходимых библиотек:
   - pandas, numpy для работы с данными.
   - lightgbm для обучения модели.
   - joblib для сохранения модели и масштабировщика.
   - sklearn.model_selection для разделения данных на тренировочные и валидационные наборы.
   - sklearn.preprocessing для масштабирования признаков.
   - sklearn.metrics для оценки качества модели по метрике ROC AUC.
Также устанавливаем рандом, чтобы эксперимент можно было воспроизвести повторно и результаты совпадали.
3.	Загружаем тренировачные данные.
4.	Определение функцию для вычисления признаков:
 Функция compute_features вычисляет статистические характеристики временного ряда:
   - Среднее значение (mean).
   - Стандартное отклонение (std).
   - Максимум (max).
   - Минимум (min).
   - Медиана (median).
   - Коэффициент асимметрии (skew).
   - Эксцесс (kurtosis).
   - Длина ряда (length)
6.	Отделяем признаки от целевой переменной и масштабируем признаки
7.	Разделение данных на обучающую и валидационную выборки:
Используется train_test_split с параметром stratify для сохранения распределения классов.
8. Обучение модели:
   - Создание объектов lgb.Dataset для обучения и валидации.
   - Определение параметров модели (params), включая цель (objective), метрику (metric), тип бустинга (boosting_type), случайное состояние (random_state) и максимальную глубину(max_depth), учйт дисбаланса классов ('is_unbalance').
   - Обучение модели с помощью lgb.train.

8.	Оценка модели: Вычисление метрики ROC AUC
Сохранение объектов model и scaler в файлы trained_model.pkl и scaler.pkl соответственно.


### Основной скрипт для решения.ipynb

1. Импорт библиотек:
   - pandas для обработки данных.
   - numpy для численных вычислений.
   - joblib для загрузки сохраненных моделей и скейлеров.
   - lightgbm для работы с моделью LightGBM.

2. Загрузка тестовых данных:
   - Тестовые данные загружаются из файла test.parquet с помощью pd.read_parquet('test.parquet').

3. Определение функции compute_features:
   - Функция вычисляет ряд статистических признаков из массивов дат и значений:
     - Среднее (mean)
     - Стандартное отклонение (std)
     - Максимум (max)
     - Минимум (min)
     - Медиана (median)
     - Ассиметрия (skew)
     - Эксцесс (kurtosis)
     - Длина последовательности (length)

4. Вычисление признаков для тестовой выборки:
   - Признаки вычисляются для каждого примера в тестовом наборе данных с помощью цикла for и функции compute_features.
   - Результаты сохраняются в списке test_features_list, который затем преобразуется в DataFrame test_features_df.

5. Вывод первых строк вычисленных признаков:
   - С помощью print отображаются первые несколько строк DataFrame с вычисленными признаками.

6. Загрузка сохраненной модели и скейлера:
   - Используя joblib.load, загружаются:
   - Модель LightGBM из файла trained_model.pkl.
   - Скейлер (например, StandardScaler) из файла scaler.pkl.

7. Подготовка данных для предсказания:
   - Из DataFrame test_features_df удаляется столбец id.
   - Оставшиеся признаки масштабируются с помощью загруженного скейлера и сохраняются в X_test_scaled.

8. Предсказание на тестовой выборке:
   - С использованием метода predict модели LightGBM получаются предсказания вероятностей для тестовых данных.
   - Предсказания сохраняются в переменной test_pred_prob.



