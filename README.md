# Easy_Recycle_Telegram_Bot

https://t.me/CleanSanBot

![video_2021-10-28_09-47-35 (online-video-cutter com)](https://user-images.githubusercontent.com/88197584/139203581-8459bed5-8be6-47a2-ac12-d8cc23ae9fac.gif)

## Задача

Реализовать сервис по грамотной и быстрой сортировки отходов для их утилизации или переработки.

## План решения задачи

1) **Формирование датасета классов отходов и пунктов раздельного сбора отходов**
2) **Разметка фотографий и их аугментация**
3) **Выбор и обучение модели на нейронных сетях**
4) **Создание сервиса**

## Решение задачи

### Формирование датасета классов отходов и пунктов раздельного сбора отходов
Были определены 6 классов отходов (пластик, бумага, металл, органические материалы природного происхождения, стекло, композитные материалы) и сделан датасет из 1400 фотографий. Так же в ручную был сделан датасет из пунктов раздельного сбора отходо по г.Москва для выбранных классов (около 400 точек)
### Разметка фотографий и их аугментация
С помощью сайта https://roboflow.com/ была проведена аугрентация фотографий. В конечном итоге количество фотографий возросло до 5000. Они были разделены на train, valid и test.
### Выбор и обучение модели на нейронных сетях
В качестве модели была выбрана модель Yolov4 DarkNet и с помощью Google Colab были обучены две данные модели. Вторая модель определяет является ли пластик перерабатываемым или нет. Обучение заняло 15 и 10 часов соответственно. Метрикой в моделях Yolov4 DarkNet служило mean Average Precision. На валидационных выбораках оценка точности составила 72% и 71%.
### Создание сервиса
В качестве сервиса был выбран телеграм Бот [@CleaSanBot](https://t.me/CleanSanBot). При создании бота была использвана библиотека aiogram, которая помогла реализовать State запроты, запросы пользователя, клавиауры ReplyKeyboardMarkup и InlineKeyboardMarkup и отправку сообщениия пользователю.
## Принцип работы сервиса
- Пользователь запускает сервис и присылает фотографию со значком переработки отходов
- Фотография поступат на проверку отправленного сообщения (проверяет, что на вход пришла фотография, а не текст)
- Пользователю предлагается использовать его местоположение
- Если пользователь соглашается:
  - Eго кординаты записываются в датасет (все данные конфиденциальные);
  - Далее фотография послупает на вход модели Yolov4 DarkNet и определяется тип отходов, присутствующих на фотографии;
  - Если классов несколько, то пользователю предлагается выбрать, какай класс отходов от хочет сдать на переработку;
  - Если выбранный класс является Пластиком, то фотография поступает на еще одну модель Yolov4 DarkNet, которая определяет является ли пластик перерабатываемым или нет;
  - С помощью координат пользователя и координат местоположения пунктов раздельного сбора отходов высчитываются 3 ближайши точки, куда можно сдать данный тип отходов и выводятся пользователю сообщением;
  - Если обнаружен только один класс, то пользователю сразу выводится в виде сообщения 3 адреса ближайших унктов переработки данного типа отхода.
- Если пользователь отказался:
  - Его фотография послупает на вход модели Yolov4 DarkNet и определяется тип отходов, присутствующих на фотографии;
  - Если среди обнаруженных классов есть Пластик то фотография поступает на еще одну модель Yolov4 DarkNet, которая определяет является ли пластик перерабатываемым или нет;
  - Выводится сайт, где пользователь может посмотреть все точки на карте, куда можно сдать отходы.
## Способ проверки работоспособности кода
1) Установить все библиотеки из requirements.txt (for windows - pip install - r requirements.txt)
2) В bot/api/ создать папку database
3) Из [Googve drive](https://drive.google.com/drive/folders/1_K7dKHxCFKUMlBCmlu7Rq7LuF9AnW5LM?usp=sharing) скачать датасет и поместить в database. Веса поместить в папку bot/api/core/darknet-master/
4) По примеру файла .env.dist создать в корневой папке файл .env с TOKEN_TELEGRAM и ключевыми словами с кодами.
5) Запустить скрипт bot/_bot.py .
