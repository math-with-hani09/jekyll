# hani_site.py
# نسخهٔ پیشرفته: گرادینت متحرک، منوی کشویی، انیمیشن‌ها، ترنزیشن‌ها، ناوبری بالا با زیرمنو
# کاملاً راست‌چین و فارسی

import os
import math
from typing import List, Tuple
import streamlit as st
from PIL import Image

# ---------------- پیکربندی صفحه ----------------
st.set_page_config(
    page_title="ریاضی با هانی — نسخهٔ پیشرفته",
    page_icon="✨",
    layout="wide"
)

# ---------------- داده‌های نمایشی ----------------
PRODUCTS = [
    {
        "title": "جزوهٔ «ریاضی باحال‌تر از فیزیک کوانتومی»",
        "desc": "۷ فصل چکیده با تمرین‌های میمی و نکات ناب؛ مخصوص نابغه‌های خندان.",
        "price": "۱۹٬۰۰۰",
        "tags": ["جزوه", "تمرین", "نابغه‌پسند"],
        "tone": "cream"
    },
    {
        "title": "آزمون‌های «نابغه‌سنج» با امتیاز جهانی",
        "desc": "آزمون‌های زمان‌دار با جدول امتیاز؛ هر هفته قهرمان جدید!",
        "price": "۲۹٬۰۰۰",
        "tags": ["آزمون", "چالش", "امتیاز"],
        "tone": "green"
    },
    {
        "title": "پک استیکرهای ریاضی",
        "desc": "π خندان، ∞ کنجکاو، √ عاشق—هرجا که بخندی یاد ریاضی بیفتی.",
        "price": "۹٬۰۰۰",
        "tags": ["استیکر", "میم", "تزئینی"],
        "tone": "pink"
    },
    {
        "title": "دورهٔ ویدئویی «ریاضی با میم»",
        "desc": "مفاهیم سخت با مثال‌های وایرال و کپشن‌های بامزه.",
        "price": "۴۹٬۰۰۰",
        "tags": ["دوره", "ویدئو", "میم"],
        "tone": "blue"
    },
]

POSTS = [
    {
        "title": "چرا عدد π پایان ندارد؟",
        "date": "چهارشنبه ۱۴ شهریور",
        "read": "۴ دقیقه",
        "summary": "از دایره‌های باستان تا کامپیوترهای مدرن—سفر بی‌پایان π و راز محبوبیتش."
    },
    {
        "title": "ریاضی‌دان‌هایی که دنیا را نجات دادند",
        "date": "سه‌شنبه ۱۳ شهریور",
        "read": "۶ دقیقه",
        "summary": "از رامانوجان تا یک دانش‌آموز خلاق؛ ایده‌هایی که جهان را آرام‌تر کردند."
    },
    {
        "title": "با یک کسر ساده، معلمت را شگفت‌زده کن",
        "date": "دوشنبه ۱۲ شهریور",
        "read": "۳ دقیقه",
        "summary": "ترفند تبدیل کسر به فرم مصری؛ کم‌حرف، اثرگذار، و بامزه."
    },
]

WONDERS = [
    {
        "title": "عدد کاپریکار 6174",
        "items": [
            "یک عدد ۴ رقمی با حداقل دو رقم متفاوت انتخاب کن.",
            "رقم‌ها را نزولی و صعودی بچین؛ سپس بزرگ‌تر را از کوچک‌تر کم کن.",
            "این کار را تکرار کن؛ همیشه به 6174 می‌رسی!"
        ],
        "note": "چرخه‌ای شگفت‌انگیز که حس می‌کنی پشتش رازی پنهان شده.",
        "tone": "green"
    },
    {
        "title": "چرخهٔ 142857",
        "items": [
            "این عدد را در ۲ تا ۶ ضرب کن؛ فقط جای رقم‌ها جابه‌جا می‌شود!",
            "در ۷ که ضرب کنی، می‌شود ۹۹۹۹۹۹—و این تازه شروع بازی است."
        ],
        "note": "اثر شگفت‌انگیز اعداد تکرارشونده و جدول ضرب ۷.",
        "tone": "pink"
    },
    {
        "title": "آیا 0.999... = 1 ؟",
        "items": [
            "به نظر می‌رسد کمتر از ۱ باشد، اما دقیقاً برابر ۱ است.",
            "اثبات با حد، سری هندسی و حتی شهود ساده."
        ],
        "note": "گاهی شهود ما با واقعیت یکی نیست—و همین جذابش می‌کند.",
        "tone": "cream"
    },
]

# ---------------- کمکی: ریاضی کسر مصری ----------------
def _gcd(a, b):
    a, b = abs(a), abs(b)
    while b:
        a, b = b, a % b
    return a or 1

def simplify(a: int, b: int) -> Tuple[int, int]:
    g = _gcd(a, b)
    return a // g, b // g

def egyptian(a: int, b: int) -> Tuple[List[int], List[str]]:
    steps, dens = [], []
    a, b = simplify(a, b)
    while a > 1:
        x = math.ceil(b / a)
        dens.append(x)
        a, b = a * x - b, b * x
        a, b = simplify(a, b)
        steps.append(f"انتخاب: 1/{x} → باقی‌مانده: {a}/{b}")
    dens.append(b)
    steps.append(f"پایان: 1/{b}")
    return dens, steps

# ---------------- استایل پیشرفته ----------------
CSS = """
<style>
:root{
  --bg1: #1e293b;        /* آبی تیره برای عمق */
  --bg2: #312e81;        /* بنفش تیره */
  --grad1: #6b21a8;      /* بنفش */
  --grad2: #4ba3f5;      /* آبی نئونی ملایم */
  --grad3: #f472b6;      /* صورتی ملایم */
  --ink: #101418;
  --ink-soft: #5b6470;
  --card: #ffffff;
  --border: #E7EBF0;
  --blue: #3A6EA5;
  --purple: #6B21A8;
  --cream: #FFF6E8;
  --green: #A7F3D0;
  --pink: #FADADD;
  --blueSoft: #ECF5FF;
  --orange: #FFD89A;

  --shadow: 0 12px 28px rgba(0,0,0,.08);
  --radius: 16px;
}

/* پس‌زمینهٔ گرادینت متحرک با الگوی نرم */
html, body, [class*="css"]{
  direction: rtl;
  font-family: "Vazirmatn", "Vazir", Tahoma, sans-serif;
  background:
    radial-gradient(1200px 600px at 10% -10%, rgba(107,33,168,.12), transparent 60%),
    radial-gradient(900px 500px at 110% 10%, rgba(75,163,245,.10), transparent 60%),
    linear-gradient(135deg, var(--grad1), var(--grad2), var(--grad3));
  background-size: 120% 120%;
  animation: bgflow 22s ease-in-out infinite;
  color: var(--ink);
}
@keyframes bgflow {
  0% { background-position: 0% 30%, 120% 20%, 0% 0%; }
  50% { background-position: 50% 50%, 60% 40%, 100% 100%; }
  100% { background-position: 0% 30%, 120% 20%, 0% 0%; }
}

.container{ max-width: 1200px; margin: 0 auto; }

/* پوشش محتوایی برای خوانایی روی گرادینت */
.layer{
  background: rgba(255,255,255,.55);
  backdrop-filter: blur(8px);
  border: 1px solid rgba(255,255,255,.5);
  border-radius: var(--radius);
  box-shadow: var(--shadow);
  padding: 14px;
}

/* نوار ناوبری چسبان با منوهای کشویی و همبرگری موبایل */
.nav-wrap{
  position: sticky; top: 0; z-index: 9999;
  margin-bottom: 12px;
}
.nav{
  display: flex; align-items: center; justify-content: space-between;
}
.brand{
  display: flex; align-items: center; gap: 10px; font-weight: 900; color: #1f2937;
}
.brand .pi{ font-size: 26px; color: var(--purple); }
.menu{
  display: flex; align-items: center; gap: 4px;
}
.menu > li{
  list-style: none; position: relative;
}
.menu > li > a, .menu > li > button{
  display: inline-flex; align-items: center; gap: 6px;
  border: 1px solid rgba(0,0,0,.06);
  background: rgba(255,255,255,.9);
  border-radius: 999px;
  padding: 8px 12px;
  color: #111827; text-decoration: none; font-weight: 700;
  transition: transform .2s ease, box-shadow .2s ease, background .2s ease;
}
.menu > li > a:hover, .menu > li > button:hover{
  transform: translateY(-1px);
  box-shadow: 0 10px 24px rgba(0,0,0,.08);
}

/* زیرمنو */
.dropdown{
  position: absolute; right: 0; top: 46px; min-width: 220px;
  background: rgba(255,255,255,.96);
  border: 1px solid rgba(0,0,0,.06);
  border-radius: 12px; padding: 8px;
  box-shadow: var(--shadow);
  transform-origin: top right;
  transform: scale(.98) translateY(-6px);
  opacity: 0; pointer-events: none;
  transition: transform .18s ease, opacity .18s ease;
}
.menu > li:hover .dropdown{
  opacity: 1; pointer-events: auto; transform: scale(1) translateY(0);
}
.dropdown a{
  display: block; padding: 8px 10px; border-radius: 8px;
  color: #111827; text-decoration: none; font-weight: 600;
}
.dropdown a:hover{ background: #f4f6fb; }

/* همبرگری موبایل با هکِ چک‌باکس */
#nav-toggle { display: none; }
.burger{
  display: none; cursor: pointer;
  background: rgba(255,255,255,.9);
  border: 1px solid rgba(0,0,0,.06);
  border-radius: 10px; padding: 8px 10px; font-weight: 800;
}
@media (max-width: 900px){
  .menu{ display: none; }
  .burger{ display: inline-block; }
  #nav-toggle:checked ~ .menu{
    display: flex; flex-direction: column; gap: 8px; position: absolute;
    right: 16px; left: 16px; top: 64px; padding: 10px;
    background: rgba(255,255,255,.95); border: 1px solid rgba(0,0,0,.06); border-radius: 14px;
  }
  .menu > li { width: 100%; }
  .menu > li > a, .menu > li > button{ width: 100%; justify-content: space-between; }
  .menu > li:hover .dropdown{ position: static; transform: none; opacity: 1; pointer-events: auto; box-shadow: none; }
}

/* هدر قهرمان با نمادهای ریاضی متحرک */
.hero{
  position: relative; overflow: hidden;
  border-radius: var(--radius);
  padding: 16px;
  background:
    linear-gradient(145deg, rgba(255,255,255,.75), rgba(255,255,255,.55));
  border: 1px solid rgba(255,255,255,.6);
}
.hero h1{ margin: 0; color: #1f2937; font-weight: 900; }
.hero p{ margin: 8px 0 0 0; color: #475569; }

.float-math{ position: absolute; opacity: .08; font-weight: 900; user-select: none; }
.f1{ top: -10px; left: 20px; font-size: 120px; color: #6b21a8; animation: drift1 14s ease-in-out infinite; }
.f2{ bottom: -20px; right: 40px; font-size: 150px; color: #4ba3f5; animation: drift2 18s ease-in-out infinite; }
.f3{ top: 20px; right: 30%; font-size: 80px; color: #14b8a6; animation: drift3 12s ease-in-out infinite; }
.f4{ bottom: 10px; left: 35%; font-size: 90px; color: #ffd89a; animation: drift1 20s ease-in-out infinite reverse; }
@keyframes drift1 { 0%{transform: translateY(0)} 50%{transform: translateY(10px)} 100%{transform: translateY(0)} }
@keyframes drift2 { 0%{transform: translateX(0)} 50%{transform: translateX(-12px)} 100%{transform: translateX(0)} }
@keyframes drift3 { 0%{transform: rotate(0)} 50%{transform: rotate(6deg)} 100%{transform: rotate(0)} }

/* کارت‌ها و ترنزیشن‌ها */
.card{
  background: var(--card);
  border: 1px solid var(--border);
  border-radius: var(--radius);
  padding: 16px;
  box-shadow: var(--shadow);
  transition: transform .18s ease, box-shadow .18s ease, background .18s ease;
}
.card:hover{
  transform: translateY(-4px);
  box-shadow: 0 18px 40px rgba(0,0,0,.12);
}

/* برچسب‌ها */
.badge{
  display: inline-block; border: 1px solid rgba(0,0,0,.06); background: rgba(255,255,255,.9);
  border-radius: 999px; padding: 6px 10px; margin: 8px 6px 0 0; font-size: 13px; color: #374151; font-weight: 700;
}

/* دکمه‌ها */
.stButton button{
  background: linear-gradient(135deg, #3A6EA5, #4BA3F5) !important;
  color: #fff !important; border: none !important; border-radius: 12px !important; padding: 10px 14px !important;
  box-shadow: 0 10px 24px rgba(59,130,246,.3);
  transition: transform .16s ease, filter .16s ease, box-shadow .16s ease;
}
.stButton button:hover{ transform: translateY(-2px); filter: brightness(1.05); }

/* تُن‌های پس‌زمینه برای بخش‌ها */
.tone-cream { background: var(--cream); }
.tone-green { background: var(--green); }
.tone-pink  { background: var(--pink); }
.tone-blue  { background: var(--blueSoft); }

/* پیام‌ها */
.msg{ border-radius: 12px; padding: 10px 12px; margin-top: 10px; font-size: 14px; }
.msg.success{ background: #EAF7EE; border: 1px solid #CDE6D5; }
.msg.error{ background: #FDECEC; border: 1px solid #F7CACA; }
.msg.info{ background: #EEF5FF; border: 1px solid #D6E6FF; }

/* فرم‌ها راست‌چین */
input, textarea, select { direction: rtl !important; }

/* انیمیشن ورود */
.reveal{ opacity: 0; transform: translateY(8px); animation: reveal .5s ease forwards; }
@keyframes reveal { to{ opacity: 1; transform: none; } }

/* لینک‌ها */
a.x-link{ color: #4ba3f5; text-decoration: none; font-weight: 800; }
a.x-link:hover{ text-decoration: underline; }
</style>
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Vazirmatn:wght@300;400;600;700;900&display=swap" rel="stylesheet">
"""
st.markdown(CSS, unsafe_allow_html=True)

# ---------------- بارگذار کوتاه (تزئینی) ----------------
st.markdown("""
<style>
.loader {
  position: fixed; inset: 0; z-index: 9998; display: grid; place-items: center;
  background: linear-gradient(135deg, rgba(255,255,255,.85), rgba(255,255,255,.6));
  animation: fadeOut 1s ease 1.2s forwards;
}
.spinner {
  width: 54px; height: 54px; border-radius: 50%;
  border: 4px solid rgba(107,33,168,.2); border-top-color: #6B21A8; animation: spin .8s linear infinite;
}
@keyframes spin { to{ transform: rotate(360deg);} }
@keyframes fadeOut { to{ opacity: 0; visibility: hidden; } }
</style>
<div class="loader"><div class="spinner"></div></div>
""", unsafe_allow_html=True)

# ---------------- کمک‌تابع‌ها ----------------
def load_logo():
    path = "logo.png"
    if os.path.exists(path):
        try:
            return Image.open(path)
        except:
            return None
    return None

def set_page(name: str):
    st.experimental_set_query_params(page=name)

def get_page() -> str:
    params = st.experimental_get_query_params()
    return params.get("page", ["خانه"])[0]

# ---------------- نوار ناوبری بالا ----------------
def nav_bar():
    current = get_page()
    st.markdown('<div class="nav-wrap">', unsafe_allow_html=True)
    with st.container():
        st.markdown('<div class="container">', unsafe_allow_html=True)
        st.markdown('<div class="layer">', unsafe_allow_html=True)
        st.markdown(
            f"""
            <nav class="nav">
              <div class="brand">
                <span class="pi">π+</span>
                <span>ریاضی با هانی</span>
              </div>

              <label class="burger" for="nav-toggle">منو</label>
              <input type="checkbox" id="nav-toggle" />

              <ul class="menu">
                <li>
                  <a href="?page=خانه">خانه</a>
                </li>

                <li>
                  <a href="?page=عجایب ریاضی">عجایب</a>
                  <div class="dropdown">
                    <a href="?page=عجایب ریاضی">مسأله‌ها و الگوها</a>
                    <a href="?page=عجایب ریاضی">ایده‌های شگفت‌انگیز</a>
                  </div>
                </li>

                <li>
                  <a href="?page=ابزار کسر مصری">ابزارها</a>
                  <div class="dropdown">
                    <a href="?page=ابزار کسر مصری">تبدیل کسر به فرم مصری</a>
                    <a href="?page=ابزار کسر مصری">جُزر و جذر (به‌زودی)</a>
                    <a href="?page=ابزار کسر مصری">بازی با سری‌ها (به‌زودی)</a>
                  </div>
                </li>

                <li>
                  <a href="?page=فروشگاه">فروشگاه</a>
                  <div class="dropdown">
                    <a href="?page=فروشگاه">جزوه‌ها</a>
                    <a href="?page=فروشگاه">آزمون‌ها</a>
                    <a href="?page=فروشگاه">استیکرها</a>
                  </div>
                </li>

                <li>
                  <a href="?page=نوشته‌ها">نوشته‌ها</a>
                  <div class="dropdown">
                    <a href="?page=نوشته‌ها">یادداشت‌های کوتاه</a>
                    <a href="?page=نوشته‌ها">پشت‌صحنهٔ ابزار</a>
                  </div>
                </li>

                <li><a href="?page=درباره">درباره</a></li>
                <li><a href="?page=تماس">تماس</a></li>
              </ul>
            </nav>
            """, unsafe_allow_html=True
        )
        st.markdown('</div>', unsafe_allow_html=True)
        st.markdown('</div>', unsafe_allow_html=True)
    st.markdown('</div>', unsafe_allow_html=True)

nav_bar()

# ---------------- هدر قهرمان ----------------
with st.container():
    st.markdown('<div class="container reveal">', unsafe_allow_html=True)
    st.markdown('<div class="hero">', unsafe_allow_html=True)
    logo = load_logo()
    cols = st.columns([1, 3])
    with cols[0]:
        if logo is not None:
            st.image(logo, use_column_width=True)
        else:
            st.markdown(
                """
                <div class="card" style="text-align:center;">
                    <div style="font-size:46px; color:#6B21A8;">π+</div>
                    <div style="color:#556; font-size:13px;">لوگو یافت نشد (logo.png را کنار فایل بگذار)</div>
                </div>
                """, unsafe_allow_html=True
            )
    with cols[1]:
        st.markdown("""
            <div class="float-math f1">π</div>
            <div class="float-math f2">∞</div>
            <div class="float-math f3">√</div>
            <div class="float-math f4">Σ</div>
            <h1>ساده، پخته، مدرن — دنیای شگفتی‌های ریاضی</h1>
            <p>خانهٔ ابزارهای تعاملی، نوشته‌های الهام‌بخش، فروشگاه نابغه‌ها و «عجایب ریاضی».</p>
            <span class="badge">راست‌چین و فارسی</span>
            <span class="badge">گرادینت زنده</span>
            <span class="badge">منوی کشویی</span>
        """, unsafe_allow_html=True)
    st.markdown('</div>', unsafe_allow_html=True)
    st.markdown('</div>', unsafe_allow_html=True)

st.write("")

# ---------------- صفحات ----------------
def page_home():
    st.markdown('<div class="container reveal">', unsafe_allow_html=True)
    c1, c2 = st.columns([2,1])
    with c1:
        st.markdown('<div class="card">', unsafe_allow_html=True)
        st.markdown("### از اینجا شروع کن")
        st.markdown(
            "- فروشگاه: محصولات آموزشی نمایشی برای تست تجربهٔ خرید\n"
            "- نوشته‌ها: یادداشت‌های کوتاه و الهام‌بخش\n"
            "- عجایب: مسأله‌ها و الگوهای حیرت‌آور\n"
            "- ابزار: تبدیل کسر به فرم مصری"
        )
        st.markdown('</div>', unsafe_allow_html=True)

        st.markdown("")
        st.markdown('<div class="card">', unsafe_allow_html=True)
        st.markdown("### رنگ و حرکت")
        st.write("گرادینت زنده، کارت‌های چندلایه، و انیمیشن‌های ملایم برای حس حرفه‌ای و مدرن.")
        st.markdown('</div>', unsafe_allow_html=True)

    with c2:
        st.markdown('<div class="card">', unsafe_allow_html=True)
        st.markdown("### وضعیت نسخه")
        st.write("۰.۳ — پیشرفته")
        st.markdown(
            '<span class="badge">منوی کشویی</span>'
            '<span class="badge">گرادینت پویا</span>'
            '<span class="badge">انیمیشن‌ها</span>'
            '<span class="badge">عجایب ریاضی</span>', unsafe_allow_html=True
        )
        st.markdown('</div>', unsafe_allow_html=True)
    st.markdown('</div>', unsafe_allow_html=True)

def page_wonders():
    st.markdown('<div class="container reveal">', unsafe_allow_html=True)
    st.markdown('<div class="card">', unsafe_allow_html=True)
    st.markdown("### عجایب ریاضی")
    st.write("مسأله‌ها و الگوهای شگفت‌انگیز که ذهن را قلقلک می‌دهند.")

    cols = st.columns(3)
    for i, w in enumerate(WONDERS):
        with cols[i % 3]:
            tone = {
                "green": "tone-green",
                "pink": "tone-pink",
                "cream": "tone-cream"
            }.get(w["tone"], "")
            st.markdown(f'<div class="card {tone}">', unsafe_allow_html=True)
            st.markdown(f"**{w['title']}**")
            for it in w["items"]:
                st.write("• " + it)
            st.markdown(f'<div class="msg info">{w["note"]}</div>', unsafe_allow_html=True)
            st.markdown('</div>', unsafe_allow_html=True)
    st.markdown('</div>', unsafe_allow_html=True)
    st.markdown('</div>', unsafe_allow_html=True)

def page_egypt():
    st.markdown('<div class="container reveal">', unsafe_allow_html=True)
    st.markdown('<div class="card tone-blue">', unsafe_allow_html=True)
    st.markdown("### ابزار کسر مصری")
    st.write("کسرت را وارد کن تا به مجموع کسرهای واحد تبدیل کنیم.")

    with st.form("egypt_form"):
        c1, c2, c3 = st.columns([1,1,1])
        with c1:
            numer = st.number_input("صورت", min_value=1, value=4, step=1, format="%d")
        with c2:
            denom = st.number_input("مخرج", min_value=2, value=13, step=1, format="%d")
        with c3:
            ok = st.form_submit_button("تبدیل کن")
    if ok:
        if numer >= denom:
            st.markdown('<div class="msg error">فقط کسرهای حقیقی مجاز هستند (صورت باید کوچکتر از مخرج باشد).</div>', unsafe_allow_html=True)
        else:
            a, b = simplify(int(numer), int(denom))
            dens, steps = egyptian(a, b)
            sum_txt = " + ".join([f"1/{d}" for d in dens])
            st.markdown(f'<div class="msg success">{a}/{b} = {sum_txt}</div>', unsafe_allow_html=True)
            with st.expander("نمایش مراحل"):
                for s in steps:
                    st.write("• " + s)
    st.markdown('</div>', unsafe_allow_html=True)
    st.markdown('</div>', unsafe_allow_html=True)

def page_store():
    st.markdown('<div class="container reveal">', unsafe_allow_html=True)
    st.markdown('<div class="card">', unsafe_allow_html=True)
    st.markdown("### فروشگاه نابغه‌ها")
    st.write("محصولات آموزشی نمایشی برای تست تجربهٔ خرید و نمایش بصری.")

    rows = [st.columns(2), st.columns(2)]
    i = 0
    for prod in PRODUCTS:
        col = rows[i//2][i%2]
        with col:
            tone = {
                "cream": "tone-cream",
                "green": "tone-green",
                "pink": "tone-pink",
                "blue": "tone-blue"
            }.get(prod["tone"], "")
            st.markdown(f'<div class="card {tone}">', unsafe_allow_html=True)
            st.markdown(f"**{prod['title']}**")
            st.write(prod["desc"])
            st.markdown(f'<span class="badge">قیمت: {prod["price"]} تومان</span>', unsafe_allow_html=True)
            st.markdown("<div style='height:8px'></div>", unsafe_allow_html=True)
            for t in prod["tags"]:
                st.markdown(f'<span class="badge">{t}</span>', unsafe_allow_html=True)
            st.markdown('</div>', unsafe_allow_html=True)
        i += 1

    st.markdown('<div class="msg info">این‌ها نمونهٔ نمایشی هستند. در نسخهٔ اصلی، سبد خرید و درگاه بومی افزوده می‌شود.</div>', unsafe_allow_html=True)
    st.markdown('</div>', unsafe_allow_html=True)
    st.markdown('</div>', unsafe_allow_html=True)

def page_posts():
    st.markdown('<div class="container reveal">', unsafe_allow_html=True)
    st.markdown('<div class="card">', unsafe_allow_html=True)
    st.markdown("### یادداشت‌های هانی")
    st.write("خلاصه‌نویسی‌های الهام‌بخش و میمی برای عاشقان ریاضی.")

    for p in POSTS:
        st.markdown('<div class="card">', unsafe_allow_html=True)
        st.markdown(f"**{p['title']}**")
        st.markdown(f"<span style='color:#5b6470; font-size:13px;'>{p['date']} • {p['read']} مطالعه</span>", unsafe_allow_html=True)
        st.write(p["summary"])
        st.markdown('</div>', unsafe_allow_html=True)
    st.markdown('</div>', unsafe_allow_html=True)
    st.markdown('</div>', unsafe_allow_html=True)

def page_about():
    st.markdown('<div class="container reveal">', unsafe_allow_html=True)
    st.markdown('<div class="card">', unsafe_allow_html=True)
    st.markdown("### دربارهٔ من")
    st.write("من هانی هستم؛ عاشق ساختن ابزارهای خلاقانه برای ریاضی. این نسخه برای تست تجربهٔ بصری و ساختار توسعه‌پذیر ساخته شده.")
    st.write("هدف: مسیر ساده تا نسخهٔ اصلی روی سرور داخلی با دامنهٔ دات‌آی‌آر، فروشگاه واقعی، و محتوای غنی.")
    st.markdown('</div>', unsafe_allow_html=True)
    st.markdown('</div>', unsafe_allow_html=True)

def page_contact():
    st.markdown('<div class="container reveal">', unsafe_allow_html=True)
    st.markdown('<div class="card">', unsafe_allow_html=True)
    st.markdown("### تماس")
    st.write("نظرت را بگو—هر پیشنهاد می‌تواند یک ویژگی جدید بسازد.")
    with st.form("contact_form"):
        name = st.text_input("نام")
        email = st.text_input("ایمیل (اختیاری)")
        msg = st.text_area("پیام")
        ok = st.form_submit_button("ارسال")
    if ok:
        if not name or not msg:
            st.markdown('<div class="msg error">نام و پیام را وارد کن.</div>', unsafe_allow_html=True)
        else:
            st.markdown('<div class="msg success">پیامت ثبت شد (نمایشی).</div>', unsafe_allow_html=True)
    st.markdown('</div>', unsafe_allow_html=True)
    st.markdown('</div>', unsafe_allow_html=True)

# ---------------- مسیریابی بر اساس پارامتر آدرس ----------------
page = get_page()
if page == "خانه":
    page_home()
elif page == "عجایب ریاضی":
    page_wonders()
elif page == "ابزار کسر مصری":
    page_egypt()
elif page == "فروشگاه":
    page_store()
elif page == "نوشته‌ها":
    page_posts()
elif page == "درباره":
    page_about()
elif page == "تماس":
    page_contact()
else:
    page_home()
