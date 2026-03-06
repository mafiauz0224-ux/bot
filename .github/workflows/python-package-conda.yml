import os
import asyncio
from telebot.async_telebot import AsyncTeleBot
from telebot import types
from shazamio import Shazam

TOKEN = "8010820433:AAFjV1kqpqn_2K0fa92SkjPSAJXNwPsIr_o"
bot = AsyncTeleBot(TOKEN)
shazam = Shazam()

# Inline keyboard
def video_keyboard():
    kb = types.InlineKeyboardMarkup()
    kb.row(
        types.InlineKeyboardButton("🎧 Audio ajratish", callback_data="audio"),
        types.InlineKeyboardButton("🎵 Musiqani topish (Shazam)", callback_data="music")
    )
    return kb

@bot.message_handler(commands=["start"])
async def start(message):
    await bot.send_message(
        message.chat.id,
        "👋 Salom!\nVideo yuboring yoki Instagram/TikTok link tashlang."
    )

# Video qabul qilish
@bot.message_handler(content_types=["video"])
async def handle_video(message):
    file_info = await bot.get_file(message.video.file_id)
    downloaded = await bot.download_file(file_info.file_path)

    video_path = f"{message.chat.id}_video.mp4"
    with open(video_path, "wb") as f:
        f.write(downloaded)

    await bot.send_message(
        message.chat.id,
        "Video qabul qilindi ✅",
        reply_markup=video_keyboard()
    )

# Inline tugmalar
@bot.callback_query_handler(func=lambda c: True)
async def callbacks(call):
    chat_id = call.message.chat.id
    video = f"{chat_id}_video.mp4"
    audio = f"{chat_id}_audio.mp3"

    # 🎧 Audio ajratish
    if call.data == "audio":
        await bot.send_message(chat_id, "🎧 Audio ajratilmoqda...")
        os.system(f'ffmpeg -i "{video}" "{audio}" -y -loglevel quiet')
        await bot.send_audio(chat_id, open(audio, "rb"))

    # 🎵 Shazam
    elif call.data == "music":
        await bot.send_message(chat_id, "🎵 Musiqa aniqlanmoqda (Shazam)...")

        os.system(f'ffmpeg -i "{video}" "{audio}" -y -loglevel quiet')

        result = await shazam.recognize_song(audio)

        try:
            track = result["track"]
            title = track["title"]
            artist = track["subtitle"]

            await bot.send_message(
                chat_id,
                f"🎶 TOPILDI:\n\n"
                f"🎵 Nomi: {title}\n"
                f"👤 Ijrochi: {artist}"
            )
        except:
            await bot.send_message(chat_id, "❌ Musiqa topilmadi")

    # Tozalash
    for f in [video, audio]:
        if os.path.exists(f):
            os.remove(f)

# Botni ishga tushirish
asyncio.run(bot.polling())
