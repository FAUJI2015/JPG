import os
import io
import requests
import streamlit as st
import fal_client
from PIL import Image, ImageDraw, ImageFont

# ==========================================
# FAUJI FACTORY ব্র্যান্ডিং কনফিগারেশন (RED MAIN)
# ==========================================
CENTER_NAME = "FAUJI FACTORY"
CONTACT_NUMBER = "9564945545"
COLOR_PRIMARY_RED = "#D32F2F"    # সিগনেচার ডিফেন্স রেড
COLOR_TEXT_WHITE = "#FFFFFF"     # বোল্ড সাদা টেক্সট
COLOR_ACCENT_BLACK = "#111111"   # ডিপ ব্ল্যাক বর্ডার

st.set_page_config(page_title="Fauji Factory AI Studio", page_icon="🎖️", layout="centered")

# কাস্টম পেজ ডিজাইন
st.markdown(f"""
    <style>
    .main-title {{
        text-align: center;
        color: {COLOR_PRIMARY_RED};
        font-weight: 900;
        font-size: 34px;
        letter-spacing: 1px;
        margin-bottom: 2px;
    }}
    .sub-title {{
        text-align: center;
        color: #333333;
        font-size: 15px;
        font-weight: 600;
        margin-bottom: 25px;
    }}
    .stButton>button {{
        background-color: {COLOR_PRIMARY_RED} !important;
        color: white !important;
        font-size: 18px !important;
        font-weight: bold !important;
        border-radius: 8px !important;
        border: 2px solid {COLOR_ACCENT_BLACK} !important;
        width: 100%;
        padding: 10px;
    }}
    </style>
""", unsafe_allow_html=True)

st.markdown(f'<div class="main-title">🎖️ {CENTER_NAME} AI STUDIO</div>', unsafe_allow_html=True)
st.markdown('<div class="sub-title">Facebook Feed & Story Generator | Defense & Athletics Edition</div>', unsafe_allow_html=True)

# API Key ইনপুট
fal_key = st.secrets.get("FAL_KEY", None)
if not fal_key:
    fal_key = st.sidebar.text_input("fal.ai API Key এখানে দিন:", type="password")
    if fal_key:
        os.environ["FAL_KEY"] = fal_key
else:
    os.environ["FAL_KEY"] = fal_key

# ==========================================
# ব্র্যান্ডিং ব্যানার ফাংশন
# ==========================================
def apply_branding_banner(generated_img: Image.Image) -> Image.Image:
    img = generated_img.convert("RGBA")
    w, h = img.size
    
    banner_height = int(h * 0.12)
    banner = Image.new("RGBA", (w, banner_height), COLOR_PRIMARY_RED)
    draw = ImageDraw.Draw(banner)
    
    # ব্ল্যাক টপ বর্ডার
    draw.rectangle([(0, 0), (w, int(banner_height * 0.05))], fill=COLOR_ACCENT_BLACK)
    
    font_size_name = max(24, int(banner_height * 0.42))
    font_size_sub = max(18, int(banner_height * 0.28))
    
    try:
        font_name = ImageFont.truetype("DejaVuSans-Bold.ttf", font_size_name)
        font_sub = ImageFont.truetype("DejaVuSans.ttf", font_size_sub)
    except:
        font_name = ImageFont.load_default()
        font_sub = ImageFont.load_default()
        
    draw.text((int(w * 0.05), int(banner_height * 0.18)), CENTER_NAME, fill=COLOR_TEXT_WHITE, font=font_name)
    draw.text((int(w * 0.05), int(banner_height * 0.58)), f"Call / WhatsApp: {CONTACT_NUMBER}", fill=COLOR_TEXT_WHITE, font=font_sub)
    
    if os.path.exists("logo.png"):
        try:
            logo = Image.open("logo.png").convert("RGBA")
            logo_size = int(banner_height * 0.8)
            logo = logo.resize((logo_size, logo_size), Image.Resampling.LANCZOS)
            banner.paste(logo, (w - logo_size - int(w * 0.05), int(banner_height * 0.1)), logo)
        except Exception:
            pass

    final_canvas = Image.new("RGBA", (w, h + banner_height))
    final_canvas.paste(img, (0, 0))
    final_canvas.paste(banner, (0, h))
    return final_canvas.convert("RGB")

# ==========================================
# ইউজার ইন্টারফেস
# ==========================================
col1, col2 = st.columns(2)

with col1:
    post_format = st.selectbox(
        "পোস্ট সাইজ:",
        ["Facebook Feed (Square 1:1)", "Facebook/Insta Feed (Portrait 4:5)", "Story / Reel (Full 9:16)"]
    )

with col2:
    category = st.selectbox(
        "ক্যাটাগরি:",
        [
            "Indian Army Physical Training Drill",
            "State Police Physical Ground Practice",
            "Athletic Running Cadets on Track",
            "Obstacle Race & Extreme Endurance",
            "Cadet Celebration & Victory Pose"
        ]
    )

ref_file = st.file_uploader("রেফারেন্স ছবি আপলোড করুন (ঐচ্ছিক):", type=["jpg", "jpeg", "png"])
extra_prompt = st.text_input("অতিরিক্ত নির্দেশ (ঐচ্ছিক):", placeholder="যেমন: dramatic morning mist, intense focus")

ratio_map = {
    "Facebook Feed (Square 1:1)": {"image_size": "square_hd"},
    "Facebook/Insta Feed (Portrait 4:5)": {"image_size": "portrait_4_3"},
    "Story / Reel (Full 9:16)": {"image_size": "portrait_16_9"}
}

if st.button("🔥 Generate Photo Now"):
    if not os.environ.get("FAL_KEY"):
        st.error("দয়া করে সাইডবারে fal.ai API Key দিন।")
    else:
        with st.spinner("AI ছবি তৈরি করছে... অনুগ্রহ করে অপেক্ষা করুন..."):
            try:
                base_prompt = f"Professional candid photo of {category.lower()}, fit Indian candidates, authentic training ground, dramatic cinematic lighting, ultra-realistic, 8k resolution, raw documentary style."
                if extra_prompt:
                    base_prompt += f" {extra_prompt}"

                input_args = {
                    "prompt": base_prompt,
                    "image_size": ratio_map[post_format]["image_size"],
                    "num_images": 1
                }

                if ref_file:
                    ref_url = fal_client.upload(ref_file.getvalue())
                    input_args["image_url"] = ref_url
                    result = fal_client.subscribe("fal-ai/flux/dev/image-to-image", arguments=input_args)
                else:
                    result = fal_client.subscribe("fal-ai/flux/schnell", arguments=input_args)

                image_url = result["images"][0]["url"]
                res = requests.get(image_url)
                raw_image = Image.open(io.BytesIO(res.content))
                
                final_image = apply_branding_banner(raw_image)

                st.success("ছবি তৈরি সম্পন্ন হয়েছে!")
                st.image(final_image, caption=f"{CENTER_NAME} - Ready to Post", use_container_width=True)

                buf = io.BytesIO()
                final_image.save(buf, format="JPEG", quality=95)
                st.download_button(
                    label="📥 Download Photo",
                    data=buf.getvalue(),
                    file_name="fauji_factory_post.jpg",
                    mime="image/jpeg"
                )

            except Exception as e:
                st.error(f"সমস্যা হয়েছে: {str(e)}")
