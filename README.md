# Telegram-bot
import telebot
from telebot import types

token = '7398959957:AAFv2ljiIDXCSv8zij22K6Q_U1eV-uYLLQ8'
bot = telebot.TeleBot(token)

@bot.message_handler(commands=['start'])
def start_message(message):
    markup = types.ReplyKeyboardMarkup(resize_keyboard=True)
    markup.add('Калькулятор', 'Заметки', 'Мем дня', 'Я соскучился по тебе')
    bot.send_message(message.chat.id, 'Привет! Я бот, который может работать как калькулятор, умеет сохранять заметки и отправлять мем дня. Напиши /help, чтобы узнать больше.', reply_markup=markup)

@bot.message_handler(commands=['help'])
def help_message(message):
    bot.send_message(message.chat.id, 'Напиши /calculate для калькулятора.\nНапиши /notes для сохранения заметки.\nНапиши /mem для мема дня. \nНапиши /miss .')

@bot.message_handler(commands=['miss'])
def miss(message):
    bot.send_message(message.chat.id, 'Я тоже по тебе сокучился!!!')
    bot.send_photo(message.chat.id, 'https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcQYd8p0UKZoaqXRQLuJ8Y33ajpgrxymNR0O_g&s')

@bot.message_handler(commands=['Калькулятор'])
def calculate(message):
    bot.send_message(message.chat.id, 'Напиши выражение в формате: число 1 операция число 2')
    bot.register_next_step_handler(message, process_calculation)

@bot.message_handler(commands=['Мем'])
def mem(message):
    bot.send_message(message.chat.id, 'Вот мем дня:')
    bot.send_photo(message.chat.id, 'https://media.discordapp.net/attachments/1188889103102656513/1246062525771022357/IMG_3956.jpg?ex=665b05a8&is=6659b428&hm=f6b6af9e7b5a46783751a1725d4b50ccd71602df5728dd629d8dea64aeac48ff&=&format=webp&width=528&height=619')

@bot.message_handler(func=lambda message: message.text == 'Калькулятор')
def calculate_inline(message):
    bot.send_message(message.chat.id, 'Напиши выражение в формате: число 1 операция число 2')
    bot.register_next_step_handler(message, process_calculation)


@bot.message_handler(func=lambda message: message.text == 'Мем дня')
def mem_inline(message):
    bot.send_message(message.chat.id, 'Вот мем дня:')
    bot.send_photo(message.chat.id, 'https://media.discordapp.net/attachments/1188889103102656513/1246061665145847879/IMG_3953.jpg?ex=665b04db&is=6659b35b&hm=b0c6c5e651489fa57cbbc6c38e624fdd5818ba2c02dd33d641d92178f74d673c&=&format=webp&width=487&height=619')

notes_dict = {}

@bot.message_handler(func=lambda message: message.text == 'Заметки')
def notes_inline(message):
    bot.send_message(message.chat.id, 'Напиши заметку или отправь "Показать заметки" чтобы просмотреть сохраненные заметки.')
    bot.register_next_step_handler(message, save_or_show_notes)

def save_or_show_notes(message):
    global notes_dict
    if message.text.lower() == 'показать заметки':
        notes = notes_dict.get(message.from_user.id, [])
        if notes:
            bot.send_message(message.chat.id, 'Сохраненные заметки:')
            for i, note in enumerate(notes, 1):
                bot.send_message(message.chat.id, f'{i}. {note}')
        else:
            bot.send_message(message.chat.id, 'У вас нет сохраненных заметок.')
    else:
        note_text = message.text
        current_notes = notes_dict.get(message.from_user.id, [])
        current_notes.append(note_text)
        notes_dict[message.from_user.id] = current_notes
        bot.send_message(message.chat.id, 'Заметка сохранена.')
        
def process_calculation(message):
    try:
        expression = message.text
        result = eval(expression)
        bot.send_message(message.chat.id, f'Результат: {result}')
    except Exception as e:
        bot.send_message(message.chat.id, f'Error: {e}')

@bot.message_handler(func=lambda message: message.text == 'Я соскучился по тебе')
def icku_inline(message):
    bot.send_message(message.chat.id, 'Я тоже по тебе сокучился!!!')
    bot.send_photo(message.chat.id, 'https://media.discordapp.net/attachments/1188889103102656513/1246062342907891773/IMG_3954.jpg?ex=665b057d&is=6659b3fd&hm=3b54d5cce98eea58131a030f6f9e36a85adacd0c4630782891236faf91c9c35d&=&format=webp')

bot.infinity_polling()
