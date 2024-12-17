# Weather
Импорт библиотек:

import logging
import requests
from aiogram import Bot, Dispatcher, executor, types
Импортируются необходимые библиотеки: logging для ведения логов, requests для выполнения HTTP-запросов и aiogram для работы с Telegram Bot API.
Константы:

TELEGRAM_TOKEN = 'YOUR_TELEGRAM_BOT_TOKEN'
API_KEY = 'YOUR_OPENWEATHERMAP_API_KEY'
CITY = 'Samara'
TELEGRAM_TOKEN — токен вашего бота в Telegram.
API_KEY — ключ API для доступа к OpenWeatherMap.
CITY — город, для которого будет запрашиваться погода.
Создание бота и диспетчера:

bot = Bot(token=TELEGRAM_TOKEN)
dp = Dispatcher(bot)
Создается объект бота и диспетчер для обработки входящих сообщений.
Функция получения погоды:

def get_weather(city):
    URL = f'http://api.openweathermap.org/data/2.5/weather?q={city}&appid={API_KEY}&units=metric&lang=ru'
    response = requests.get(URL)

    if response.status_code == 200:
        data = response.json()
        main = data['main']
        weather = data['weather'][0]

        temp = main['temp']
        pressure = main['pressure']
        humidity = main['humidity']
        description = weather['description']

        return f'Погода в городе {city}:\n' \
               f'Температура: {temp}°C\n' \
               f'Давление: {pressure} гПа\n' \
               f'Влажность: {humidity}%\n' \
               f'Описание: {description.capitalize()}'
    else:
        return 'Город не найден или возникла ошибка!'
Функция get_weather принимает название города и формирует URL для запроса к API OpenWeatherMap.
Если запрос успешен (код 200), функция извлекает данные о температуре, давлении, влажности и описании погоды, а затем формирует строку с этой информацией.
Если город не найден или произошла ошибка, возвращается сообщение об ошибке.
Обработчики команд:

@dp.message_handler(commands=['start'])
async def start_command(message: types.Message):
    await message.answer('Здравствуйте! Напишите /weather, чтобы узнать погоду в Самаре.')

@dp.message_handler(commands=['weather'])
async def weather_command(message: types.Message):
    weather_info = get_weather(CITY)
    await message.answer(weather_info)
start_command отвечает на команду /start, приветствуя пользователя и предлагая ему узнать погоду.
weather_command отвечает на команду /weather, вызывая функцию get_weather и отправляя пользователю информацию о погоде в Самаре.
Запуск бота:

if __name__ == '__main__':
    executor.start_polling(dp, skip_updates=True)
Этот блок запускает бота и начинает опрос обновлений. Параметр skip_updates=True позволяет игнорировать старые обновления, которые были получены, пока бот был отключен.
Возможные улучшения
Динамический ввод города:

Вместо того чтобы жестко задавать город, можно позволить пользователю вводить название города, и бот будет возвращать погоду для указанного города.
Обработка ошибок:

Улучшить обработку ошибок, чтобы бот мог более подробно сообщать пользователю о проблемах (например, неверный формат города).
Логирование:

Включить логирование для отслеживания работы бота и выявления возможных проблем.
Дополнительные команды:

Добавить больше команд, например, для получения прогноза погоды на несколько дней вперед.
Форматирование сообщений:

Использовать Markdown или HTML для форматирования сообщений, чтобы сделать их более читабельными.
