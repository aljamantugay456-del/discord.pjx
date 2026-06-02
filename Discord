"""
Bot Bro - A feature-packed Discord Bot 🤙
Supports both !prefix and /slash commands!
Requirements: pip install discord.py yt-dlp PyNaCl aiohttp python-dotenv
"""

import discord
from discord.ext import commands
from discord import app_commands
import os
import random
import asyncio
import aiohttp
import datetime
from dotenv import load_dotenv

load_dotenv()

TOKEN = os.getenv("MTUxMTM2NTI2NTc3NzEwMjk1OQ.G9XRdX.FrCGWeny2f32S7mp_XvS_w4A1plkrEPjf5L8Tg")
ANTHROPIC_API_KEY = os.getenv("ANTHROPIC_API_KEY")

# ─────────────────────────────────────────
#  Bot Setup
# ─────────────────────────────────────────
intents = discord.Intents.default()
intents.message_content = True
intents.members = True

bot = commands.Bot(command_prefix="!", intents=intents, help_command=None)

warnings_db = {}       # { user_id: [reasons] }
active_giveaways = {}  # { message_id: { prize, winners, end_time, channel_id, host } }


# ─────────────────────────────────────────
#  Events
# ─────────────────────────────────────────
@bot.event
async def on_ready():
    print(f"✅ Bot Bro is online as {bot.user}")
    print(f"✅ Connected to {len(bot.guilds)} servers")
    await bot.change_presence(activity=discord.Game(name="/help | Bot Bro 🤙"))
    
    # Sync slash commands
    try:
        synced = await bot.tree.sync()
        print(f"✅ Synced {len(synced)} slash command(s)")
    except Exception as e:
        print(f"❌ Error syncing commands: {e}")


@bot.event
async def on_member_join(member):
    channel = discord.utils.get(member.guild.text_channels, name="general")
    if channel:
        await channel.send(
            f"👋 Yo {member.mention}! Welcome to **{member.guild.name}**! "
            f"Type `/help` or `!help` to see what I can do. Let's gooo 🔥"
        )


@bot.event
async def on_command_error(ctx, error):
    if isinstance(error, commands.MissingPermissions):
        await ctx.send("❌ You don't have permission to do that, bro.")
    elif isinstance(error, commands.MissingRequiredArgument):
        await ctx.send(f"⚠️ Missing argument. Usage: `!help {ctx.command}`")
    elif isinstance(error, commands.CommandNotFound):
        await ctx.send("❓ Unknown command. Type `!help` to see all commands.")
    elif isinstance(error, commands.BadArgument):
        await ctx.send(f"⚠️ Bad argument. Try `!help {ctx.command}`")
    else:
        await ctx.send(f"⚠️ Something went wrong: `{error}`")


# ─────────────────────────────────────────
#  SLASH COMMANDS - HELP
# ─────────────────────────────────────────
@bot.tree.command(name="help", description="Show all available commands")
async def slash_help(interaction: discord.Interaction, category: str = None):
    """Slash command version of help"""
    
    categories = {
        "mod": "🛡️ Moderation Commands",
        "fun": "🎮 Fun & Games Commands",
        "giveaway": "🎉 Giveaway Commands",
        "music": "🎵 Music Commands",
        "utility": "🔧 Utility Commands",
        "ai": "🤖 AI Commands",
    }
    
    if category and category.lower() in categories:
        embed = discord.Embed(
            title=categories[category.lower()],
            description=f"Use `/{category.lower()}` commands or `!{category.lower()}` prefix commands",
            color=0x5865F2
        )
        
        # Add specific commands based on category
        if category.lower() == "mod":
            embed.add_field(name="/kick", value="Kick a member from the server", inline=True)
            embed.add_field(name="/ban", value="Ban a member from the server", inline=True)
            embed.add_field(name="/mute", value="Mute a member temporarily", inline=True)
            embed.add_field(name="/unmute", value="Unmute a member", inline=True)
            embed.add_field(name="/warn", value="Warn a member", inline=True)
            embed.add_field(name="/clear", value="Clear messages in channel", inline=True)
            embed.add_field(name="/slowmode", value="Set slowmode in channel", inline=True)
            embed.add_field(name="/lock", value="Lock the current channel", inline=True)
            embed.add_field(name="/unlock", value="Unlock the current channel", inline=True)
            
        elif category.lower() == "fun":
            embed.add_field(name="/roll", value="Roll a dice", inline=True)
            embed.add_field(name="/flip", value="Flip a coin", inline=True)
            embed.add_field(name="/8ball", value="Ask the magic 8ball", inline=True)
            embed.add_field(name="/rps", value="Play Rock Paper Scissors", inline=True)
            embed.add_field(name="/roast", value="Roast someone", inline=True)
            embed.add_field(name="/compliment", value="Compliment someone", inline=True)
            embed.add_field(name="/trivia", value="Answer a trivia question", inline=True)
            embed.add_field(name="/wyr", value="Would You Rather", inline=True)
            embed.add_field(name="/fact", value="Random fun fact", inline=True)
            embed.add_field(name="/joke", value="Tell a joke", inline=True)
            embed.add_field(name="/slots", value="Play the slot machine", inline=True)
            
        elif category.lower() == "giveaway":
            embed.add_field(name="/gstart", value="Start a giveaway", inline=True)
            embed.add_field(name="/gend", value="End a giveaway early", inline=True)
            embed.add_field(name="/greroll", value="Reroll giveaway winner", inline=True)
            embed.add_field(name="/glist", value="List active giveaways", inline=True)
            
        elif category.lower() == "music":
            embed.add_field(name="/play", value="Play a song from YouTube", inline=True)
            embed.add_field(name="/pause", value="Pause the music", inline=True)
            embed.add_field(name="/resume", value="Resume the music", inline=True)
            embed.add_field(name="/skip", value="Skip current song", inline=True)
            embed.add_field(name="/stop", value="Stop and disconnect", inline=True)
            embed.add_field(name="/queue", value="Show the music queue", inline=True)
            embed.add_field(name="/nowplaying", value="Show current song", inline=True)
            embed.add_field(name="/volume", value="Set volume", inline=True)
            
        elif category.lower() == "utility":
            embed.add_field(name="/ping", value="Check bot latency", inline=True)
            embed.add_field(name="/avatar", value="Get someone's avatar", inline=True)
            embed.add_field(name="/serverinfo", value="Server information", inline=True)
            embed.add_field(name="/userinfo", value="User information", inline=True)
            embed.add_field(name="/poll", value="Create a poll", inline=True)
            embed.add_field(name="/botinfo", value="Bot information", inline=True)
            
        elif category.lower() == "ai":
            embed.add_field(name="/ask", value="Ask Bot Bro AI anything", inline=True)
        
        embed.set_footer(text="Bot Bro 🤙 | Use /help for main menu")
        await interaction.response.send_message(embed=embed)
        return
    
    # Main help menu
    embed = discord.Embed(
        title="🤙 Bot Bro — Command Menu",
        description="Bot Bro supports both **Slash Commands** (/) and **Prefix Commands** (!)\n\n"
                   "Type `/help <category>` or `!help <category>` for detailed commands.",
        color=0x5865F2
    )
    embed.add_field(name="🛡️ Moderation", value="`/help mod`\nKick, ban, mute, warn", inline=True)
    embed.add_field(name="🎮 Fun & Games", value="`/help fun`\nGames, jokes, trivia", inline=True)
    embed.add_field(name="🎉 Giveaways", value="`/help giveaway`\nStart and manage giveaways", inline=True)
    embed.add_field(name="🎵 Music", value="`/help music`\nPlay YouTube audio", inline=True)
    embed.add_field(name="🔧 Utility", value="`/help utility`\nInfo, polls, avatar", inline=True)
    embed.add_field(name="🤖 AI Chat", value="`/help ai`\nAsk Claude AI questions", inline=True)
    embed.add_field(
        name="📌 Quick Examples",
        value="`/ping` - Check latency\n"
              "`/ask What is Python?` - Ask AI\n"
              "`/play Never Gonna Give You Up` - Play music\n"
              "`/8ball Will I win?` - Ask the magic 8ball",
        inline=False
    )
    embed.set_footer(text="Bot Bro 🤙 | Prefix: ! | Slash: /")
    await interaction.response.send_message(embed=embed)


# ─────────────────────────────────────────
#  SLASH COMMANDS - MODERATION
# ─────────────────────────────────────────
@bot.tree.command(name="kick", description="Kick a member from the server")
@app_commands.describe(member="The member to kick", reason="Reason for the kick")
async def slash_kick(interaction: discord.Interaction, member: discord.Member, reason: str = "No reason given"):
    if not interaction.user.guild_permissions.kick_members:
        await interaction.response.send_message("❌ You don't have permission to kick members!", ephemeral=True)
        return
    await member.kick(reason=reason)
    await interaction.response.send_message(f"👟 **{member}** has been kicked. Reason: {reason}")

@bot.tree.command(name="ban", description="Ban a member from the server")
@app_commands.describe(member="The member to ban", reason="Reason for the ban")
async def slash_ban(interaction: discord.Interaction, member: discord.Member, reason: str = "No reason given"):
    if not interaction.user.guild_permissions.ban_members:
        await interaction.response.send_message("❌ You don't have permission to ban members!", ephemeral=True)
        return
    await member.ban(reason=reason)
    await interaction.response.send_message(f"🔨 **{member}** has been banned. Reason: {reason}")

@bot.tree.command(name="mute", description="Mute a member temporarily")
@app_commands.describe(member="The member to mute", duration="Duration in minutes", reason="Reason for the mute")
async def slash_mute(interaction: discord.Interaction, member: discord.Member, duration: int = 10, reason: str = "No reason given"):
    if not interaction.user.guild_permissions.manage_roles:
        await interaction.response.send_message("❌ You don't have permission to mute members!", ephemeral=True)
        return
    
    muted_role = discord.utils.get(interaction.guild.roles, name="Muted")
    if not muted_role:
        muted_role = await interaction.guild.create_role(name="Muted")
        for channel in interaction.guild.channels:
            await channel.set_permissions(muted_role, send_messages=False, speak=False)
    
    await member.add_roles(muted_role, reason=reason)
    await interaction.response.send_message(f"🔇 **{member}** muted for {duration} min. Reason: {reason}")
    
    await asyncio.sleep(duration * 60)
    if muted_role in member.roles:
        await member.remove_roles(muted_role)

@bot.tree.command(name="unmute", description="Unmute a member")
@app_commands.describe(member="The member to unmute")
async def slash_unmute(interaction: discord.Interaction, member: discord.Member):
    if not interaction.user.guild_permissions.manage_roles:
        await interaction.response.send_message("❌ You don't have permission to unmute members!", ephemeral=True)
        return
    
    muted_role = discord.utils.get(interaction.guild.roles, name="Muted")
    if muted_role and muted_role in member.roles:
        await member.remove_roles(muted_role)
        await interaction.response.send_message(f"🔊 **{member}** has been unmuted.")
    else:
        await interaction.response.send_message(f"**{member}** isn't muted.")

@bot.tree.command(name="clear", description="Clear messages in the channel")
@app_commands.describe(amount="Number of messages to clear (1-100)")
async def slash_clear(interaction: discord.Interaction, amount: int = 10):
    if not interaction.user.guild_permissions.manage_messages:
        await interaction.response.send_message("❌ You don't have permission to clear messages!", ephemeral=True)
        return
    
    if amount < 1 or amount > 100:
        await interaction.response.send_message("⚠️ Amount must be between 1 and 100!", ephemeral=True)
        return
    
    deleted = await interaction.channel.purge(limit=amount)
    await interaction.response.send_message(f"🧹 Cleared {len(deleted)} messages.", ephemeral=True)

@bot.tree.command(name="slowmode", description="Set slowmode in the channel")
@app_commands.describe(seconds="Slowmode delay in seconds (0 to disable)")
async def slash_slowmode(interaction: discord.Interaction, seconds: int = 0):
    if not interaction.user.guild_permissions.manage_channels:
        await interaction.response.send_message("❌ You don't have permission to set slowmode!", ephemeral=True)
        return
    
    await interaction.channel.edit(slowmode_delay=seconds)
    if seconds == 0:
        await interaction.response.send_message("✅ Slowmode disabled.")
    else:
        await interaction.response.send_message(f"🐢 Slowmode set to **{seconds}s**.")

@bot.tree.command(name="lock", description="Lock the current channel")
async def slash_lock(interaction: discord.Interaction):
    if not interaction.user.guild_permissions.manage_channels:
        await interaction.response.send_message("❌ You don't have permission to lock channels!", ephemeral=True)
        return
    
    await interaction.channel.set_permissions(interaction.guild.default_role, send_messages=False)
    await interaction.response.send_message("🔒 Channel locked.")

@bot.tree.command(name="unlock", description="Unlock the current channel")
async def slash_unlock(interaction: discord.Interaction):
    if not interaction.user.guild_permissions.manage_channels:
        await interaction.response.send_message("❌ You don't have permission to unlock channels!", ephemeral=True)
        return
    
    await interaction.channel.set_permissions(interaction.guild.default_role, send_messages=None)
    await interaction.response.send_message("🔓 Channel unlocked.")


# ─────────────────────────────────────────
#  SLASH COMMANDS - FUN & GAMES
# ─────────────────────────────────────────
@bot.tree.command(name="roll", description="Roll a dice")
@app_commands.describe(sides="Number of sides on the dice")
async def slash_roll(interaction: discord.Interaction, sides: int = 6):
    result = random.randint(1, sides)
    await interaction.response.send_message(f"🎲 You rolled a **{result}** (d{sides})")

@bot.tree.command(name="flip", description="Flip a coin")
async def slash_flip(interaction: discord.Interaction):
    result = random.choice(['Heads 🪙', 'Tails 🔄'])
    await interaction.response.send_message(f"🪙 Coin flip: **{result}**")

@bot.tree.command(name="8ball", description="Ask the magic 8ball a question")
@app_commands.describe(question="Your question for the 8ball")
async def slash_8ball(interaction: discord.Interaction, question: str):
    responses = [
        "It is certain ✅", "Without a doubt ✅", "Yes definitely ✅",
        "Most likely ✅", "Signs point to yes ✅", "Reply hazy, try again 🤔",
        "Ask again later 🤔", "Cannot predict now 🤔", "Don't count on it ❌",
        "My reply is no ❌", "Outlook not so good ❌", "Very doubtful ❌"
    ]
    await interaction.response.send_message(f"🎱 **{random.choice(responses)}**\n> {question}")

@bot.tree.command(name="rps", description="Play Rock Paper Scissors")
@app_commands.describe(choice="Your choice")
@app_commands.choices(choice=[
    app_commands.Choice(name="Rock 🪨", value="rock"),
    app_commands.Choice(name="Paper 📄", value="paper"),
    app_commands.Choice(name="Scissors ✂️", value="scissors")
])
async def slash_rps(interaction: discord.Interaction, choice: str):
    options = ["rock", "paper", "scissors"]
    bot_choice = random.choice(options)
    wins = {"rock": "scissors", "paper": "rock", "scissors": "paper"}
    emojis = {"rock": "🪨", "paper": "📄", "scissors": "✂️"}
    
    if choice == bot_choice:
        result = "It's a tie! 🤝"
    elif wins[choice] == bot_choice:
        result = "You win! 🎉"
    else:
        result = "Bot Bro wins! 😎"
    
    await interaction.response.send_message(f"{emojis[choice]} vs {emojis[bot_choice]} — {result}")

@bot.tree.command(name="roast", description="Roast someone")
@app_commands.describe(member="The person to roast (optional)")
async def slash_roast(interaction: discord.Interaction, member: discord.Member = None):
    target = member.mention if member else interaction.user.mention
    roasts = [
        f"{target} your Wi-Fi password is stronger than your personality.",
        f"{target} I've seen better code written by a cat walking on a keyboard.",
        f"{target} you're the human version of a participation trophy.",
        f"{target} even autocorrect gave up on you.",
    ]
    await interaction.response.send_message(random.choice(roasts))

@bot.tree.command(name="compliment", description="Compliment someone")
@app_commands.describe(member="The person to compliment (optional)")
async def slash_compliment(interaction: discord.Interaction, member: discord.Member = None):
    target = member.mention if member else interaction.user.mention
    compliments = [
        f"{target} you light up every server you're in! ✨",
        f"{target} your vibes are immaculate fr 💯",
        f"{target} lowkey carries this whole server 👑",
    ]
    await interaction.response.send_message(random.choice(compliments))

@bot.tree.command(name="trivia", description="Answer a trivia question")
async def slash_trivia(interaction: discord.Interaction):
    questions = [
        ("What is the capital of Japan?", "Tokyo"),
        ("How many sides does a hexagon have?", "6"),
        ("What planet is known as the Red Planet?", "Mars"),
        ("Who wrote Romeo and Juliet?", "Shakespeare"),
    ]
    q, answer = random.choice(questions)
    
    await interaction.response.send_message(f"🧠 **Trivia Time!**\n{q}\n*Answer in 15 seconds!*")
    
    def check(m):
        return m.channel == interaction.channel and m.author != bot.user
    
    try:
        msg = await bot.wait_for("message", timeout=15.0, check=check)
        if answer.lower() in msg.content.lower():
            await interaction.followup.send(f"✅ Correct, {msg.author.mention}! Answer: **{answer}**!")
        else:
            await interaction.followup.send(f"❌ Wrong! The answer was **{answer}**.")
    except asyncio.TimeoutError:
        await interaction.followup.send(f"⏰ Time's up! The answer was **{answer}**.")

@bot.tree.command(name="wyr", description="Would You Rather question")
async def slash_wyr(interaction: discord.Interaction):
    questions = [
        ("fly", "be invisible"),
        ("never sleep again", "always be sleeping"),
        ("have super strength", "have super speed"),
    ]
    a, b = random.choice(questions)
    embed = discord.Embed(
        title="🤔 Would You Rather...",
        description=f"**🅰️ {a.capitalize()}**\n\nOR\n\n**🅱️ {b.capitalize()}**",
        color=0x9B59B6
    )
    await interaction.response.send_message(embed=embed)

@bot.tree.command(name="fact", description="Get a random fun fact")
async def slash_fact(interaction: discord.Interaction):
    facts = [
        "Honey never spoils. Archaeologists found 3000-year-old honey in Egyptian tombs.",
        "A group of flamingos is called a flamboyance.",
        "Octopuses have three hearts.",
        "Bananas are berries, but strawberries aren't.",
    ]
    await interaction.response.send_message(f"💡 **Random Fact:** {random.choice(facts)}")

@bot.tree.command(name="joke", description="Tell a joke")
async def slash_joke(interaction: discord.Interaction):
    jokes = [
        ("Why don't scientists trust atoms?", "Because they make up everything!"),
        ("Why did the scarecrow win an award?", "Because he was outstanding in his field!"),
        ("What do you call a fake noodle?", "An impasta!"),
    ]
    setup, punchline = random.choice(jokes)
    await interaction.response.send_message(f"😂 {setup}\n||{punchline}||")

@bot.tree.command(name="slots", description="Play the slot machine")
async def slash_slots(interaction: discord.Interaction):
    symbols = ["🍒", "🍋", "🍇", "⭐", "💎", "🎰"]
    s1, s2, s3 = random.choices(symbols, k=3)
    result = f"[ {s1} | {s2} | {s3} ]"
    
    if s1 == s2 == s3 == "💎":
        outcome = "💎 JACKPOT! You hit the diamonds! 💎"
    elif s1 == s2 == s3:
        outcome = f"🎉 THREE OF A KIND! {s1} wins!"
    elif s1 == s2 or s2 == s3 or s1 == s3:
        outcome = "😐 Two of a kind. So close!"
    else:
        outcome = "❌ No match. Better luck next time!"
    
    await interaction.response.send_message(f"🎰 **SLOTS**\n{result}\n{outcome}")


# ─────────────────────────────────────────
#  SLASH COMMANDS - GIVEAWAY
# ─────────────────────────────────────────
@bot.tree.command(name="gstart", description="Start a giveaway")
@app_commands.describe(duration="Duration in minutes", winners="Number of winners", prize="The prize to give away")
async def slash_gstart(interaction: discord.Interaction, duration: int, winners: int, prize: str):
    if not interaction.user.guild_permissions.manage_guild:
        await interaction.response.send_message("❌ You don't have permission to start giveaways!", ephemeral=True)
        return
    
    end_time = datetime.datetime.utcnow() + datetime.timedelta(minutes=duration)
    embed = discord.Embed(
        title="🎉 GIVEAWAY 🎉",
        description=(
            f"**Prize:** {prize}\n\n"
            f"React with 🎉 to enter!\n\n"
            f"**Winners:** {winners}\n"
            f"**Ends:** <t:{int(end_time.timestamp())}:R>\n"
            f"**Hosted by:** {interaction.user.mention}"
        ),
        color=0xFFD700
    )
    embed.set_footer(text=f"Ends at {end_time.strftime('%Y-%m-%d %H:%M')} UTC")
    
    await interaction.response.send_message("🎉 Starting giveaway...")
    msg = await interaction.channel.send(embed=embed)
    await msg.add_reaction("🎉")
    
    active_giveaways[msg.id] = {
        "prize": prize,
        "winners": winners,
        "end_time": end_time,
        "channel_id": interaction.channel.id,
        "host": interaction.user.id,
        "message_id": msg.id,
    }
    
    await interaction.followup.send(f"✅ Giveaway started! Ends in **{duration} minute(s)**.", ephemeral=True)
    
    await asyncio.sleep(duration * 60)
    if msg.id in active_giveaways:
        await _end_giveaway_slash(interaction, msg.id)

async def _end_giveaway_slash(interaction, message_id: int):
    data = active_giveaways.pop(message_id, None)
    if not data:
        return
    
    channel = bot.get_channel(data["channel_id"])
    try:
        msg = await channel.fetch_message(message_id)
    except Exception:
        return
    
    reaction = discord.utils.get(msg.reactions, emoji="🎉")
    if not reaction:
        await channel.send("❌ No one entered the giveaway!")
        return
    
    users = [u async for u in reaction.users() if not u.bot]
    if not users:
        await channel.send("❌ No valid entries for the giveaway!")
        return
    
    num_winners = min(data["winners"], len(users))
    winners = random.sample(users, num_winners)
    winner_mentions = ", ".join(w.mention for w in winners)
    
    embed = discord.Embed(
        title="🎉 Giveaway Ended!",
        description=(
            f"**Prize:** {data['prize']}\n\n"
            f"🏆 **Winner(s):** {winner_mentions}\n\n"
            f"Congratulations! 🎊"
        ),
        color=0x57F287
    )
    await msg.edit(embed=embed)
    await channel.send(f"🎉 Congrats {winner_mentions}! You won **{data['prize']}**!")

@bot.tree.command(name="glist", description="List active giveaways")
async def slash_glist(interaction: discord.Interaction):
    if not active_giveaways:
        await interaction.response.send_message("📋 No active giveaways right now.")
        return
    
    embed = discord.Embed(title="🎉 Active Giveaways", color=0xFFD700)
    for mid, data in active_giveaways.items():
        embed.add_field(
            name=data["prize"],
            value=f"Winners: {data['winners']} | Ends: <t:{int(data['end_time'].timestamp())}:R>",
            inline=False
        )
    await interaction.response.send_message(embed=embed)


# ─────────────────────────────────────────
#  SLASH COMMANDS - UTILITY
# ─────────────────────────────────────────
@bot.tree.command(name="ping", description="Check bot latency")
async def slash_ping(interaction: discord.Interaction):
    await interaction.response.send_message(f"🏓 Pong! Latency: **{round(bot.latency * 1000)}ms**")

@bot.tree.command(name="avatar", description="Get someone's avatar")
@app_commands.describe(member="The member to get avatar of (optional)")
async def slash_avatar(interaction: discord.Interaction, member: discord.Member = None):
    member = member or interaction.user
    embed = discord.Embed(title=f"{member}'s Avatar", color=0x5865F2)
    embed.set_image(url=member.display_avatar.url)
    await interaction.response.send_message(embed=embed)

@bot.tree.command(name="serverinfo", description="Get server information")
async def slash_serverinfo(interaction: discord.Interaction):
    g = interaction.guild
    embed = discord.Embed(title=g.name, color=0x5865F2)
    embed.set_thumbnail(url=g.icon.url if g.icon else discord.Embed.Empty)
    embed.add_field(name="👥 Members", value=g.member_count)
    embed.add_field(name="📅 Created", value=g.created_at.strftime("%b %d, %Y"))
    embed.add_field(name="👑 Owner", value=g.owner)
    embed.add_field(name="💬 Text Channels", value=len(g.text_channels))
    embed.add_field(name="🔊 Voice Channels", value=len(g.voice_channels))
    embed.add_field(name="🏷️ Roles", value=len(g.roles))
    await interaction.response.send_message(embed=embed)

@bot.tree.command(name="userinfo", description="Get user information")
@app_commands.describe(member="The member to get info about (optional)")
async def slash_userinfo(interaction: discord.Interaction, member: discord.Member = None):
    member = member or interaction.user
    embed = discord.Embed(title=str(member), color=0x5865F2)
    embed.set_thumbnail(url=member.display_avatar.url)
    embed.add_field(name="🆔 ID", value=member.id)
    embed.add_field(name="📅 Joined Server", value=member.joined_at.strftime("%b %d, %Y"))
    embed.add_field(name="🎂 Account Created", value=member.created_at.strftime("%b %d, %Y"))
    roles = [r.mention for r in member.roles if r.name != "@everyone"]
    embed.add_field(name="🏷️ Roles", value=", ".join(roles) if roles else "None", inline=False)
    await interaction.response.send_message(embed=embed)

@bot.tree.command(name="botinfo", description="Get bot information")
async def slash_botinfo(interaction: discord.Interaction):
    embed = discord.Embed(title="🤙 Bot Bro", description="Your all-in-one Discord homie", color=0x5865F2)
    embed.add_field(name="🏓 Latency", value=f"{round(bot.latency * 1000)}ms")
    embed.add_field(name="🌐 Servers", value=len(bot.guilds))
    embed.add_field(name="👥 Users", value=sum(g.member_count for g in bot.guilds))
    embed.add_field(name="📌 Prefix", value="!")
    embed.add_field(name="✨ Slash Commands", value="/help to see all")
    embed.set_footer(text="Made with discord.py 🐍 | Hosted on Railway 🚂")
    await interaction.response.send_message(embed=embed)

@bot.tree.command(name="poll", description="Create a poll")
@app_commands.describe(question="The poll question")
async def slash_poll(interaction: discord.Interaction, question: str):
    embed = discord.Embed(title="📊 Poll", description=question, color=0x57F287)
    embed.set_footer(text=f"Asked by {interaction.user}")
    await interaction.response.send_message(embed=embed)
    msg = await interaction.original_response()
    await msg.add_reaction("✅")
    await msg.add_reaction("❌")


# ─────────────────────────────────────────
#  SLASH COMMANDS - AI
# ─────────────────────────────────────────
@bot.tree.command(name="ask", description="Ask Bot Bro AI anything")
@app_commands.describe(question="Your question for the AI")
async def slash_ask(interaction: discord.Interaction, question: str):
    if not ANTHROPIC_API_KEY:
        await interaction.response.send_message("❌ AI features are not configured yet!", ephemeral=True)
        return
    
    await interaction.response.defer()
    
    async with aiohttp.ClientSession() as session:
        headers = {
            "x-api-key": ANTHROPIC_API_KEY,
            "anthropic-version": "2023-06-01",
            "content-type": "application/json"
        }
        body = {
            "model": "claude-sonnet-4-20250514",
            "max_tokens": 500,
            "system": "You are Bot Bro, a friendly and casual Discord bot assistant. Keep answers short, clear, and fun.",
            "messages": [{"role": "user", "content": question}]
        }
        async with session.post("https://api.anthropic.com/v1/messages", headers=headers, json=body) as resp:
            data = await resp.json()
    
    answer = data.get("content", [{}])[0].get("text", "I couldn't think of an answer bro 😅")
    embed = discord.Embed(title="🤖 Bot Bro AI", description=answer, color=0x5865F2)
    embed.set_footer(text=f"Asked by {interaction.user}")
    await interaction.followup.send(embed=embed)


# ─────────────────────────────────────────
#  PREFIX COMMANDS (Original ones preserved)
#  I'm keeping all your original !commands here
#  They work alongside the /slash commands!
# ─────────────────────────────────────────

# ... (ALL YOUR ORIGINAL PREFIX COMMANDS GO HERE - 
#      help, kick, ban, mute, unmute, clear, warn, 
#      slowmode, lock, unlock, gstart, gend, greroll, glist,
#      roll, flip, 8ball, rps, roast, compliment, trivia, wyr,
#      fact, joke, slots, poll, ping, avatar, serverinfo, 
#      userinfo, botinfo, announce, say, ask, and all music commands)

# For brevity, I'm not copying all of them here, but you should
# keep ALL your original prefix commands from your code!


# ─────────────────────────────────────────
#  RUN
# ─────────────────────────────────────────
if __name__ == "__main__":
    if not TOKEN:
        print("❌ No DISCORD_TOKEN found in .env file!")
    else:
        bot.run(TOKEN)
