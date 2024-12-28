import telebot
from telebot import types
import random
import json


# Токен вашего бота
API_TOKEN = 'your_token'

# Создаем объект бота
bot = telebot.TeleBot(API_TOKEN)

# Список преподавателей Программистов
teachers_prog = {
    'Потякина Валерия Андреевна🐈': 'Преподаватель математики, ОИТ, информатики.',
    'Харченко Никита Леонидович😎': 'Преподаватель введения в специальность и программирования на Python.',
    'Пташинский Игорь Андреевич🧔🏻‍♂️': 'Преподаватель истории и обществознания.',
    'Чихачёв Артем Аркадьевич🧔🏻‍♂️☁️': 'Преподаватель Интернет-маркетинга.',
    'Чиндяйкина  Марина Сергеевна🦅': 'Преподаватель иностранного языка',
    'Фофанова Александра Владимировна': 'Преподаватель биологии',
}

# Список преподавателей Дизайнеров
teachers_diz = {
    'Потякина Валерия Андреевна🐈': 'Преподаватель математики, ОИТ, информатики.',
    'Игнатенко Екатерина Алексеевна✝️': 'Преподаватель истории искусств',
    'Пташинский Игорь Андреевич🛐🤠': 'Преподаватель истории, общества и основ Photoshop adapt',
    'Клименко Наталья Викторовна🦪🚷📵': 'Преподаватель географии',
    'Мамедова Наргиз Мехмановна👩‍🔬': 'Преподаватель химии',
    'Чиндяйкина  Марина Сергеевна🦅': 'Преподаватель иностранного языка',
}

# Имя файла для хранения отзывов
REVIEW_FILE = 'review.json'

# Главное меню
def main_menu():
    markup = types.ReplyKeyboardMarkup(resize_keyboard=True, one_time_keyboard=False)
    markup.row('/review оставить отзыв', 'Сайт академии')
    markup.row('список преподавателей', 'Учебные материалы')
    markup.row('отзывы о преподавателях')
    return markup





# Команда /start
@bot.message_handler(commands=['start', 'main', 'старт'])
def start_command(message):
    bot.send_message(message.chat.id,
                   f'Добро пожаловать, в GuruBot {message.from_user.first_name}!\nЗдесь вы сможете посмотреть отзывы о преподавателях, их рейтинг и учебные пособия!',
                     reply_markup=main_menu())




# Команда /teacher
@bot.message_handler(regexp=r'^список преподавателей$')
def list_teachers(message):
    markup = types.InlineKeyboardMarkup(row_width=2)
    prog_button = types.InlineKeyboardButton(text="Программисты", callback_data="prog")
    diz_button = types.InlineKeyboardButton(text="Дизайнеры", callback_data="diz")
    markup.add(prog_button, diz_button)

    bot.send_message(message.chat.id, 'Выберите категорию:', reply_markup=markup)

# Обработчик нажатия кнопки преподавателя
@bot.callback_query_handler(func=lambda call: True)
def handle_teacher_button(call):
    for teacher, data in [(t, str(hash(t))) for t in teachers_prog | teachers_diz]:
        if call.data == data:
            info = teachers_prog.get(teacher, teachers_diz.get(teacher))
            break
    else:
        info = None

    if info:
        bot.edit_message_text(f"{info}", chat_id=call.message.chat.id, message_id=call.message.message_id,
                            reply_markup=go_back())

    if call.data == "go_back":
        bot.clear_step_handler_by_chat_id(call.message.chat.id)
        bot.send_message(call.message.chat.id, 'Вы вернулись в главное меню.', reply_markup=main_menu())





# Команда /main
@bot.message_handler(commands=['main'])
def go_to_main_menu(message):
    bot.send_message(message.chat.id, 'Вы вернулись в главное меню.', reply_markup=main_menu())




# Команда /review
@bot.message_handler(commands=['review', 'оставить отзыв' ])
def review_command(message):
    markup = types.ReplyKeyboardMarkup(one_time_keyboard=True, resize_keyboard=True)
    buttons = [types.KeyboardButton(teacher) for teacher in teachers_prog | teachers_diz]
    markup.add(*buttons)

    bot.send_message(message.chat.id, 'Оставьте отзыв о преподавателе:', reply_markup=markup)
# Обработка отзыва
@bot.message_handler(func=lambda m: m.text in teachers_prog or m.text in teachers_diz and m.content_type == 'text')
def handle_review(message):
    teacher = message.text
    bot.send_message(message.chat.id, f'Напишите свой отзыв о {teacher}:')
    bot.register_next_step_handler(message, save_review, teacher)

import json

def save_review(message, teacher):
    review = message.text
    user_fullname = f'{message.from_user.first_name} {message.from_user.last_name}'
    with open(REVIEW_FILE, 'a', encoding='utf-8') as file:
        file.write(f'{{"teacher": "{teacher}", "user_fullname": "{user_fullname}", "review": "{review}"}}\n')

    bot.send_message(message.chat.id, f'Ваш отзыв о {teacher}:\n{review}\nбыл успешно сохранен.')
    bot.send_message(message.chat.id, 'Спасибо за ваш отзыв!\n/main - вернуться в меню', reply_markup=types.ReplyKeyboardRemove())



# Обработчик для кнопки "Главное меню"
@bot.message_handler(func=lambda message: message.text == "Главное меню")
def go_to_main_menu_from_button(message):
    bot.send_message(message.chat.id, 'Вы вернулись в главное меню.', reply_markup=main_menu())
# Функция для возврата в главное меню
def go_back():
    markup = types.InlineKeyboardMarkup()
    back_button = types.InlineKeyboardButton(text="Назад 🔙", callback_data="go_back")
    markup.add(back_button)
    return markup


# Команда /site
@bot.message_handler(regexp=r'^Сайт академии$')
def academy_site(message):
    markup = types.InlineKeyboardMarkup()
    button = types.InlineKeyboardButton(text="Перейти на сайт (/site)", url='https://journal.top-academy.ru')
    markup.add(button)

    bot.send_message(chat_id=message.chat.id, text="Нажмите на кнопку ниже, чтобы перейти на сайт:",
                     reply_markup=markup)


# Обработчик нажатия кнопки категории
@bot.callback_query_handler(func=lambda call: call.data in ['prog', 'diz'])
def handle_category_button(call):
    global teachers_dict
    if call.data == 'prog':
        teachers_dict = teachers_prog
    elif call.data == 'diz':
        teachers_dict = teachers_diz

    markup = types.InlineKeyboardMarkup(row_width=1)

    for teacher in teachers_dict.keys():
        callback_data = str(hash(teacher))
        button = types.InlineKeyboardButton(text=teacher, callback_data=callback_data)
        markup.add(button)

    bot.edit_message_text("Выберите преподавателя:", chat_id=call.message.chat.id, message_id=call.message.message_id,
                         reply_markup=markup)




# Команда /book
@bot.message_handler(regexp=r'^Учебные материалы$')
def materials_command(message):
    markup = types.InlineKeyboardMarkup()
    markup.add(types.InlineKeyboardButton('GitHub', url='https://github.com'))
    markup.add(types.InlineKeyboardButton('Tilda', url='https://tilda.cc/ru/'))
    markup.add(types.InlineKeyboardButton('Microsoft 365', url='https://www.microsoft365.com/'))
    markup.add(types.InlineKeyboardButton('Python Online (Programiz.pro)', url='https://programiz.pro/ide/python'))

    bot.send_message(
        chat_id=message.chat.id,
        text=
        "<b>Список полезных материалов в учебе:</b>\n"
        "<em>GitHub — интерпретатор</em>\n"
        "<em>Tilda (тильда) — веб-сайт для разработки сайтов</em>\n"
        "<em>Microsoft 365 — онлайн сайт для работы с Word, Excel и т.п.</em>\n"
        "<em>Python Online от Programiz — это онлайн интерпретатор на Python!</em>",
        reply_markup=go_back(),
        parse_mode='html'
    )

# Обработка получения фото от пользователя
@bot.message_handler(content_types=['photo'])
def handle_photo(message):
    bot.send_message(message.chat.id, 'Ваууууууу очень красивая фотография! Хотите посмотреть мою фотографию? 😊', reply_markup=get_yes_no_keyboard())



# Список путей к фотографиям и их шансы
photos = {
    './photo_1.jpeg': 0.25,
    './photo_2.jpeg': 0.50,
    './photo_3.jpeg': 0.75,
}


def get_yes_no_keyboard():
    markup = telebot.types.ReplyKeyboardMarkup(resize_keyboard=True)
    yes_button = telebot.types.KeyboardButton('Да❤️‍🔥   ')
    no_button = telebot.types.KeyboardButton('Нет😭')
    markup.add(yes_button, no_button)
    return markup


def send_welcome(message):
    # Создаем клавиатуру с кнопкой
    markup = telebot.types.ReplyKeyboardMarkup(resize_keyboard=True)
    btn1 = telebot.types.KeyboardButton('Отправить фото')
    markup.add(btn1)

    bot.send_message(message.chat.id, 'Привет!', reply_markup=markup)


@bot.message_handler(commands=['start'])
def start_command(message):
    send_welcome(message)

@bot.message_handler(func=lambda message: message.text == 'Отправить фото')
def handle_send_photo_request(message):
    bot.send_message(message.chat.id, 'Ожидаю ваше фото', reply_markup=None)


@bot.message_handler(content_types=['photo'])
def handle_photo(message):
    bot.reply_to(message, 'Ваууууууу очень красиво! Хотите посмотреть мой рисунок🤩?',
                 reply_markup=get_yes_no_keyboard())

def choose_photo_with_chance():
    """Функция выбирает фотографию с учетом шансов"""
    rand_value = random.random()  # Случайное число от 0 до 1

    for path, chance in photos.items():
        if rand_value <= chance:
            return path


@bot.message_handler(func=lambda message: message.text.startswith('Да❤️‍🔥'))
def handle_da_response(message):
    chosen_path = choose_photo_with_chance()

    if chosen_path is not None:
        with open(chosen_path, 'rb') as photo_file:
            bot.send_photo(message.chat.id, photo_file)
    else:
        bot.send_message(message.chat.id, 'Удача обошла вас стороной ,попробуйте в следующий раз😘!')


@bot.message_handler(func=lambda message: message.text.startswith('Нет😭'))
def handle_net_response(message):
    bot.send_message(message.chat.id, '😭 Вы меня расстроили -Rep 😭\n/main - вернуться в главное меню ')


# Команды для получения отзывов
@bot.message_handler(commands=['all_reviews'])
def get_all_reviews(message):
    with open(REVIEW_FILE, 'r', encoding='utf-8') as file:
        all_reviews = json.load(file)
        for review in all_reviews:
            teacher = review['teacher']
            user_fullname = review['user_fullname']
            review_text = review['review']

            formatted_review = f'Преподаватель: {teacher}\n' \
                               f'Пользователь: {user_fullname}\n' \
                               f'Отзыв: {review_text}\n\n'

            bot.send_message(message.chat.id, formatted_review)
# Запуск бесконечного цикла обработки сообщений
if __name__ == "__main__":
        try:
            bot.polling(none_stop=True)
        except Exception as e:
            print(e)
