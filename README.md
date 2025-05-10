import logging
from telegram import Update
from telegram.ext import ApplicationBuilder, CommandHandler, ContextTypes

# তোমার Bot Token এবং Owner ID (শুধু এই আইডি বট চালাতে পারবে)
BOT_TOKEN = "7886329150:AAG4O7oJG-z8CkwkCZzunPLF-vtzHIpFHU0"
OWNER_ID = 6949188304

# কিছু গ্লোবাল ভেরিয়েবল
broker = None
market = None
bot_active = False

# লগ সেটআপ
logging.basicConfig(
    format='%(asctime)s - %(name)s - %(levelname)s - %(message)s',
    level=logging.INFO
)

# /start কমান্ড হ্যান্ডলার
async def start(update: Update, context: ContextTypes.DEFAULT_TYPE):
    if update.effective_user.id != OWNER_ID:
        await update.message.reply_text("দুঃখিত, এই বটটি প্রাইভেট।")
        return
    await update.message.reply_text("বট প্রস্তুত। ব্যবহার করুন: /setbroker, /set, /on, /off")

# /setbroker কমান্ড (ব্রোকার সেট করতে)
async def set_broker(update: Update, context: ContextTypes.DEFAULT_TYPE):
    global broker
    if update.effective_user.id != OWNER_ID:
        return
    if not context.args:
        await update.message.reply_text("ব্যবহার: /setbroker <quotex|pocket|iqoption>")
        return
    broker = context.args[0].lower()
    await update.message.reply_text(f"ব্রোকার সেট হয়েছে: {broker}")

# /set কমান্ড (মার্কেট সেট করতে)
async def set_market(update: Update, context: ContextTypes.DEFAULT_TYPE):
    global market
    if update.effective_user.id != OWNER_ID:
        return
    if not context.args:
        await update.message.reply_text("ব্যবহার: /set <মার্কেট নাম>")
        return
    market = context.args[0].upper()
    await update.message.reply_text(f"মার্কেট সেট হয়েছে: {market}")

# /on কমান্ড (বট চালু করতে)
async def bot_on(update: Update, context: ContextTypes.DEFAULT_TYPE):
    global bot_active
    if update.effective_user.id != OWNER_ID:
        return
    bot_active = True
    await update.message.reply_text("বট চালু হয়েছে। প্রতি ১ মিনিটে সিগনাল পাঠানো হবে (ডেমো)।")

# /off কমান্ড (বট বন্ধ করতে)
async def bot_off(update: Update, context: ContextTypes.DEFAULT_TYPE):
    global bot_active
    if update.effective_user.id != OWNER_ID:
        return
    bot_active = False
    await update.message.reply_text("বট বন্ধ করা হয়েছে।")

# মূল বট চালানো
if __name__ == '__main__':
    app = ApplicationBuilder().token(BOT_TOKEN).build()

    app.add_handler(CommandHandler("start", start))
    app.add_handler(CommandHandler("setbroker", set_broker))
    app.add_handler(CommandHandler("set", set_market))
    app.add_handler(CommandHandler("on", bot_on))
    app.add_handler(CommandHandler("off", bot_off))

    print("বট চালু হয়েছে...")
    app.run_polling() Taligrambot
