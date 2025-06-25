# Last-Project
projek terakhir kodland python pro

import discord
import os
print(os.listdir('images'))
from discord.ext import commands
from bagian_awal import gen_pass  # Impor fungsi gen_pass
import random  # Untuk fitur tambahan, jika diperlukan
import requests
import json

# Konfigurasi intents
intents = discord.Intents.default()
intents.message_content = True

# Prefix command untuk bot
bot = commands.Bot(command_prefix='$', intents=intents)

emoji_list = [
    "😀", "😂", "🤣", "😍", "🥰", "😎", "🤔", 
    "😴", "🤖", "👻", "🦄", "🌟", "🔥", "🎉",
    "🍕", "☕", "🏆", "🎮", "🚀"
]

kategori_sampah = {
    "organik": ["sisa makanan", "daun", "kulit buah", "nasi basi", "ampas kopi", "tulang ayam"],
    "anorganik": ["plastik", "kaca", "kaleng", "botol", "kertas", "besi berkarat"],
    "berbahaya": ["baterai", "lampu neon", "obat kadaluarsa", "cat bekas", "pecahan kaca"]
}

aksi_sah = [
    "Menanam pohon",
    "Membersihkan taman",
    "Membuang sampah pada tempatnya",
    "Mematikan lampu tidak terpakai",
    "Hemat listrik",
    "Hemat air",
    "Daur ulang",
    "Pakai transportasi umum",
    "Mengurangi plastik sekali pakai",
    "Membuat kompos",
    "Mengumpulkan minyak jelantah",
    "Membawa tas belanja sendiri"
]


# File untuk simpan poin
POIN_FILE = "poin_hijau.json"

# Cek & load data
def muat_poin():
    if os.path.exists(POIN_FILE):
        with open(POIN_FILE, "r") as f:
            return json.load(f)
    return {}

# Simpan data
def simpan_poin(data):
    with open(POIN_FILE, "w") as f:
        json.dump(data, f)

# Tambah poin ke user
def tambah_poin(user_id, jumlah=1):
    poin_data = muat_poin()
    user_id = str(user_id)
    poin_data[user_id] = poin_data.get(user_id, 0) + jumlah
    simpan_poin(poin_data)

# Ambil poin user
def ambil_poin(user_id):
    poin_data = muat_poin()
    return poin_data.get(str(user_id), 0)

green_tips = [
    "Matikan lampu jika tidak digunakan 💡",
    "Bawa botol minum sendiri untuk mengurangi plastik 🍼",
    "Gunakan transportasi umum atau bersepeda 🚴",
    "Kurangi makan daging untuk menghemat emisi karbon 🥦",
    "Tanam pohon atau rawat tanaman di rumah 🌳"
    "Transisi energi fosil ke energi terbarukan agar menghindari polusi 🌻"
]

@bot.event
async def on_ready():
    print(f'We have logged in as {bot.user}')

@bot.command()
async def Fungsi(ctx):
    await ctx.send(
        """📌 **Daftar Perintah Bot** 📌

0. `$Halo` - Menyapa bot.
1. `$Goodbye` - Balasan emot 😊.
2. `$Apalah` - Mengulang "he" sesuai jumlah yang diberikan.
3. `$Passgen <jumlah>` - Membuat password acak dengan simbol.
4. `$Menambahkan <angka1> <angka2>` - Menjumlahkan dua angka.
5. `$Dadu` - Mengocok dadu 1-6 dan beri respons acak.
6. `$Ulang <jumlah> <kata>` - Mengulang kata beberapa kali.
7. `$Emoji` - Memberi emoji acak.
8. `$Koin` - Melempar koin (Kepala/Ekor).
9. `$Bebek` - Mengirimkan gambar bebek random 🦆.
10. `$Rubah` - Mengirimkan gambar rubah random 🦊.
11. `$Pilah <nama_sampah>` - Mengetahui kategori sampah (organik/anorganik/berbahaya).
12. `$Kategori` - Melihat isi kategori sampah.
13. `$Tambah_Kategori <kategori> <nama_sampah>` - Menambahkan sampah baru ke kategori.

♻️ **Fitur Aksi Hijau:**
14. `$Green_Action` - Menampilkan daftar Rekomendasi aksi ramah lingkungan.
15. `$Aksi <nama aktivitas>` - Melakukan aksi ramah lingkungan (misal: menanam pohon) dan dapat poin hijau. (Hanya aksi yang terdaftar akan diterima!)
16. `$Poinku` - Melihat jumlah poin hijau kamu.
17. `$Leaderboard` - Melihat pengguna dengan poin tertinggi dalam aksi hijau.
18. `$Usul_Aksi <nama aksi>` - Mengusulkan aksi hijau baru ke daftar aksi yang sah.

📝 Catatan:
- Untuk `$Ulang`, contoh: `$Ulang 3 Halo` → Maka akan mengulang "Halo" sebanyak 3 kali.
- Untuk `$Tambah_Kategori`, contoh: `$Tambah_Kategori organik pisang` → Menambahkan "pisang" ke kategori "organik".
"""
    )

@bot.command()
async def Halo(ctx):
    await ctx.send(f'Hi! Aku bot dari ciptaan kak Raffasya yaituu: {bot.user}!')

@bot.command()
async def Goodbye(ctx):
    await ctx.send("\U0001f642")

@bot.command()
async def Apalah(ctx, count_heh: int = 5):
    await ctx.send("he" * count_heh)

@bot.command()
async def Passgen(ctx, count_heh = 10):
    await ctx.send(gen_pass(count_heh))

@bot.command()
async def Menambahkan(ctx, left: int, right: int):
    """Menambahkan 2 angka."""
    await ctx.send(left + right)

@bot.command()
async def Dadu(ctx):
    """Mengocok dadu."""
    dice = random.randint(1, 6)
    if dice >= 4:
        await ctx.send(f"Kamu beruntung SEKALI mendapatkan angka dadu: {dice}")
    elif dice <=3:
        await ctx.send(f"Kamu KURANG beruntung mendapatkan angka dadu: {dice}")

@bot.command()
async def Ulang(ctx, times: int, content: str = "Mengulang...."):
    """Mengulang kata tertentu sejumlah times."""
    for i in range(times):
        await ctx.send(content)

@bot.command()
async def Emoji(ctx):
    """Mengirimkan emoji acak."""
    chosen_emoji = random.choice(emoji_list)
    await ctx.send(f"Kamu mendapatkan emoji: {chosen_emoji}")

@bot.command()
async def Koin(ctx):
    hasil = random.choice(["Kepala", "Ekor"])
    await ctx.send(f"Hasil lemparan koin: ***{hasil}***")

def get_duck_image_url():    
    url = 'https://random-d.uk/api/random'
    res = requests.get(url)
    data = res.json()
    return data['url']


@bot.command('Bebek')
async def Bebek(ctx):
    '''Setelah kita memanggil perintah bebek (duck), program akan memanggil fungsi get_duck_image_url'''
    image_url = get_duck_image_url()
    await ctx.send(image_url)

def get_fox_image_image():
    image = 'https://randomfox.ca/floof'
    res = requests.get(image)
    data = res.json()
    return data['image']


@bot.command('Rubah')
async def Rubah(ctx):
    images_url = get_fox_image_image()
    await ctx.send(images_url)

@bot.command()
async def Pilah(ctx, *, sampah: str):
    sampah = sampah.lower()
    for kategori, daftar_sampah in kategori_sampah.items():
        if sampah in daftar_sampah:
            await ctx.send(f'Sampah "{sampah}" termasuk dalam kategori: **{kategori.capitalize()}**.')
            return
    await ctx.send(f'Sampah "{sampah}" tidak dikenali. Coba sampah lain atau tambahkan dengan Fungsi `$TambahKategori`.')

@bot.command()
async def Kategori(ctx):
    pesan = "Kategori sampah yang tersedia:\n"
    for kategori, daftar_sampah in kategori_sampah.items():
        pesan += f"- **{kategori.capitalize()}**: {', '.join(daftar_sampah)}\n"
    await ctx.send(pesan)

@bot.command()
async def Tambah_Kategori(ctx, kategori: str, *, sampah_baru: str):
    kategori = kategori.lower()
    if kategori not in kategori_sampah:
        kategori_sampah[kategori] = []
    kategori_sampah[kategori].append(sampah_baru.lower())
    await ctx.send(f"Sampah yang kamu masukkan yaitu:`{sampah_baru}` telah ditambahkan ke kategori **{kategori.capitalize()}**.")

@bot.command()
async def Green_Action(ctx):
    tip = random.choice(green_tips)
    tambah_poin(ctx.author.id, 2) 
    await ctx.send(f"🌱 Tips Hijau Hari Ini:\n{tip}\n\nKamu mendapatkan **+2 poin hijau**! 🌿")

@bot.command()
async def Aksi(ctx, *, aktivitas: str):
    aktivitas = aktivitas.lower()
    cocok = None
    for aksi in aksi_sah:
        if aksi.lower() in aktivitas:
            cocok = aksi
            break

    if cocok:
        tambah_poin(ctx.author.id, 5)
        await ctx.send(f"✅ Aksi tercatat: _{aktivitas}_\n(Kecocokan: **{cocok}**)\nKamu mendapat **+5 poin hijau**! 🌿")
    else:
        await ctx.send(f"⚠️ Maaf, aksi _{aktivitas}_ belum dikenali sebagai aksi hijau sah.\n")
        
@bot.command()
async def Poinku(ctx):
    poin = ambil_poin(ctx.author.id)
    await ctx.send(f"🌟 Kamu punya **{poin} poin hijau**.")

@bot.command()
async def Leaderboard(ctx):
    data = muat_poin()
    if not data:
        await ctx.send("Belum ada aksi hijau tercatat 🌱")
        return
    sorted_users = sorted(data.items(), key=lambda x: x[1], reverse=True)
    top = sorted_users[:5]
    pesan = "**🌿 Green Leaderboard**\n"
    for i, (user_id, poin) in enumerate(top, start=1):
        user = await bot.fetch_user(int(user_id))
        pesan += f"{i}. {user.name} - {poin} poin\n"
    await ctx.send(pesan)

# -- Usulan aksi baru dari pengguna --
@bot.command()
async def Usul_Aksi(ctx, *, usulan: str):
    usulan = usulan.lower()
    if usulan in aksi_sah:
        await ctx.send("✅ Aksi itu sudah ada di daftar.")
    else:
        aksi_sah.append(usulan)
        await ctx.send(f"🌱 Aksi '{usulan}' telah ditambahkan ke daftar aksi hijau! Terima kasih atas inisiatifmu!")

bot.run(TOKEN)
