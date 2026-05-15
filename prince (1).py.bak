import telebot
import requests
import pytz

# ================= CONFIG =================
BOT_TOKEN = "8432813979:AAGHTnQOm4BMccevsZKyN0JQmftzL11vPs0"

OWNER_NAME = "@PRINCE_HCR"

API_URL = "https://prince-ob-53.vercel.app/like?uid={}&server_name=ind"
IST = pytz.timezone("Asia/Kolkata")

bot = telebot.TeleBot(BOT_TOKEN)

# ================= FONT =================
def sc(text):
    normal = "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789"
    small = "ᴀʙᴄᴅᴇғɢʜɪᴊᴋʟᴍɴᴏᴘǫʀsᴛᴜᴠᴡxʏᴢᴀʙᴄᴅᴇғɢʜɪᴊᴋʟᴍɴᴏᴘǫʀsᴛᴜᴠᴡxʏᴢ0123456789"
    return text.translate(str.maketrans(normal, small))

def footer():
    return sc(f"\n\n━━━━━━━━━━━━━━\n👑 owner : {OWNER_NAME}")

# ================= API CALL =================
def call_api(uid):
    try:
        url = API_URL.format(uid)
        r = requests.get(url, timeout=40)
        data = r.json()

        if "LikesbeforeCommand" not in data:
            return None, data.get("message", "api error")

        remaining = data.get("remaining", 1)
        if remaining <= 0:
            return None, "❌ daily limit over"

        return data, None

    except requests.exceptions.Timeout:
        return None, "⏳ api timeout"
    except Exception as e:
        return None, f"⚠️ server error: {str(e)}"

# ================= START =================
@bot.message_handler(commands=["start"])
def start(msg):
    bot.reply_to(
        msg,
        sc(
            "🔥 vip manual like bot\n\n"
            "📌 available command :\n\n"
            "⚡ /like uid\n\n"
            "📝 example :\n"
            "/like 123456789\n\n"
            "ℹ️ note :\n"
            "• private + group supported\n"
            "• wrong command pe error milega\n"
            "• daily api limit apply"
        ) + footer()
    )

# ================= MANUAL LIKE =================
@bot.message_handler(commands=["like"])
def manual_like(msg):
    parts = msg.text.split()

    if len(parts) != 2:
        bot.reply_to(
            msg,
            sc(
                "❌ wrong usage\n\n"
                "✅ correct format :\n"
                "/like uid\n\n"
                "📝 example :\n"
                "/like 123456789"
            ) + footer()
        )
        return

    uid = parts[1]

    if not uid.isdigit():
        bot.reply_to(
            msg,
            sc("❌ uid must be numbers only") + footer()
        )
        return

    m = bot.reply_to(
        msg,
        sc(
            f"🔥 manual like\n\n"
            f"⚡ uid : {uid}\n"
            f"⏳ sending likes..."
        ) + footer()
    )

    data, err = call_api(uid)

    if err:
        bot.edit_message_text(
            sc(f"❌ like failed\n\n{err}") + footer(),
            msg.chat.id,
            m.message_id
        )
        return

    likes = data["LikesafterCommand"] - data["LikesbeforeCommand"]

    bot.edit_message_text(
        sc(
            "✅ like successful\n\n"
            f"👤 playername : {data.get('PlayerNickname', 'N/A')}\n"
            f"🆔 uid : {uid}\n\n"
            f"👍 likes before : {data.get('LikesbeforeCommand')}\n"
            f"🔥 likes after : {data.get('LikesafterCommand')}\n"
            f"🎯 likes given : {likes}\n\n"
            f"📈 remaining : {data.get('remaining', '-')}\n"
            f"🌍 region : IND\n"
            f"⚙️ status : success"
        ) + footer(),
        msg.chat.id,
        m.message_id
    )

# ================= UNKNOWN COMMAND =================
@bot.message_handler(func=lambda m: m.text.startswith("/"))
def unknown(msg):
    bot.reply_to(
        msg,
        sc(
            "❌ unknown command\n\n"
            "ℹ️ use /start to see commands"
        ) + footer()
    )

# ================= RUN =================
print("✅ manual like bot running successfully...")
bot.infinity_polling()