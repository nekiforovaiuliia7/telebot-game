# telebot-game
import telebot
import random

bot = telebot.TeleBot('5528479668:AAEAZ0vKN8LWKc4tTkyRfMwBTvGWIr3qOJw')


# Обработчик команды /start
@bot.message_handler(commands=['start'])
def start_message(message):
    bot.reply_to(message, 'Привет, напиши "игра", чтобы начать игру')

# Обработчик текстовых сообщений и других типов сообщений
@bot.message_handler(content_types=['text', 'document', 'audio'])
def handle_message(message):
    if message.text.lower() == 'игра':
        bot.send_message(message.chat.id, 'Загадываю число от 0 до 10. Угадай!')
        num = random.randint(0, 10)
        tries_left = 3
        guess = bot.send_message(message.chat.id, 'Попытка № {}. Осталось {} попыток. Введи число:'.format(4-tries_left, tries_left-1))
        bot.register_next_step_handler(guess, check_guess, num, tries_left-1)

# Проверка угадали пользователь или нет
def check_guess(message, num, tries_left):
    try:
        guess = int(message.text)
        if guess == num:
            bot.send_message(message.chat.id, 'Поздравляю, ты угадал число {}!'.format(num))
            bot.send_message(message.chat.id, 'Напиши "игра", чтобы сыграть еще раз.')
        elif tries_left > 0:
            tries_left = tries_left - 1
            guess = bot.send_message(message.chat.id, 'Не угадал, осталось {} попыток.'.format(tries_left))
            bot.register_next_step_handler(guess, check_guess, num, tries_left)
        else:
            bot.send_message(message.chat.id, 'Не угадал, загаданное число было {}.'.format(num))
            bot.send_message(message.chat.id, 'Напиши "игра", чтобы сыграть еще раз.')
    except ValueError:
        bot.send_message(message.chat.id, 'Некорректный ввод, попробуй еще раз.')
        bot.register_next_step_handler(message, check_guess, num, tries_left)


bot.polling()


