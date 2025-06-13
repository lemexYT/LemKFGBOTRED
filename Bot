import logging
import asyncio
from telegram import Update
from telegram.ext import Application, MessageHandler, filters, ContextTypes
from telegram.constants import ParseMode

# Настройка логирования
logging.basicConfig(
    format='%(asctime)s - %(name)s - %(levelname)s - %(message)s',
    level=logging.INFO
)
logger = logging.getLogger(__name__)

# Конфигурация
TOKEN = "7213101151:AAEyVSf3frBiz2mK4iTCg2TndR2cosh0p8k"
CHANNEL_ID = -1002834472692  # Ваш канал

# Кликабельные ссылки с красивым оформлением
LINKS = {
    "💬 Чат": "https://t.me/+j5_rzMShre5hMTQy",
    "📢 Канал": "https://t.me/LemexCFG",
    "📚 Гайды": "https://t.me/+ea_glVhWUaA3Y2Y6"
}

# Красивое оформление
FOOTER = """
━━━━━━━━━━━━
<b>🔗 Полезные ссылки:</b>
{links}
━━━━━━━━━━━━
"""

async def check_old_messages(app: Application):
    """Проверяет старые сообщения при запуске"""
    try:
        logger.info("⏳ Начинаем проверку старых сообщений...")
        count = 0
        
        async for message in app.bot.get_chat_history(CHANNEL_ID, limit=100):
            try:
                original_text = message.text or message.caption or ""
                
                # Пропускаем если ссылки уже есть или пустое сообщение
                if any(link in original_text for link in LINKS.keys()) or not original_text:
                    continue
                
                # Формируем красивые ссылки
                links_text = "\n".join(f"• <a href='{url}'>{text}</a>" for text, url in LINKS.items())
                footer = FOOTER.format(links=links_text)
                new_text = f"{original_text}\n{footer}"
                
                # Редактируем сообщение
                if message.text:
                    await message.edit_text(new_text, parse_mode=ParseMode.HTML)
                elif message.caption:
                    await message.edit_caption(caption=new_text, parse_mode=ParseMode.HTML)
                
                count += 1
                await asyncio.sleep(0.5)  # Задержка чтобы не получить лимит API
                
            except Exception as e:
                logger.error(f"⚠️ Ошибка в сообщении {message.message_id}: {e}")
        
        logger.info(f"✅ Проверено сообщений: {count}")
        
    except Exception as e:
        logger.error(f"❌ Ошибка при проверке старых сообщений: {e}")

async def edit_new_message(update: Update, context: ContextTypes.DEFAULT_TYPE):
    """Обрабатывает новые сообщения"""
    try:
        message = update.effective_message
        original_text = message.text or message.caption or ""
        
        # Пропускаем если ссылки уже есть
        if any(link in original_text for link in LINKS.keys()):
            return
        
        # Формируем красивые ссылки
        links_text = "\n".join(f"• <a href='{url}'>{text}</a>" for text, url in LINKS.items())
        footer = FOOTER.format(links=links_text)
        new_text = f"{original_text}\n{footer}"
        
        # Редактируем сообщение
        if message.text:
            await message.edit_text(new_text, parse_mode=ParseMode.HTML)
        elif message.caption:
            await message.edit_caption(caption=new_text, parse_mode=ParseMode.HTML)
            
        logger.info(f"✨ Добавлены ссылки к сообщению {message.message_id}")
        
    except Exception as e:
        logger.error(f"❌ Ошибка при редактировании: {e}")

async def post_init(app: Application):
    """Действия после запуска бота"""
    await check_old_messages(app)

def main():
    application = Application.builder().token(TOKEN).post_init(post_init).build()
    
    # Обработчик новых сообщений
    application.add_handler(MessageHandler(
        filters.Chat(chat_id=CHANNEL_ID),
        edit_new_message
    ))
    
    application.run_polling(
        allowed_updates=Update.ALL_TYPES,
        close_loop=False,
        drop_pending_updates=True
    )
    logger.info("🤖 Бот запущен и работает")

if __name__ == "__main__":
    main()
