   import telegram
   from telegram.ext import Updater, CommandHandler, MessageHandler, Filters
   from pytube import YouTube

   # Botingizning tokenini o'zgartiring
   BOT_TOKEN = "7511188076:AAH6nFarBKvn8FIOTA5tM2ElgLkXG3DY9Ho"

   # Botni ishga tushirish
   updater = Updater(BOT_TOKEN, use_context=True)
   dispatcher = updater.dispatcher

   # /start buyrug'i
   def start(update, context):
       update.message.reply_text("Salom! Men musiqa botman. Musiqa eshiting!")

   # Musiqa qidirish va yuklab olish
   def search_music(update, context):
       query = update.message.text.split(" ", 1)[1]  # Musiqa nomi
       try:
           youtube = YouTube(query)
           video = youtube.streams.filter(progressive=True, file_extension='mp4').order_by('resolution').desc().first()
           video.download()
           audio_file = video.default_filename
           update.message.reply_audio(audio=open(audio_file, 'rb'))
       except Exception as e:
           update.message.reply_text(f"Xatolik: {e}")

   # Buyruqlarni ro'yxatga olish
   dispatcher.add_handler(CommandHandler('start', start))
   dispatcher.add_handler(MessageHandler(Filters.text & ~Filters.command, search_music))

   # Botni ishga tushirish
   updater.start_polling()
   updater.idle()
