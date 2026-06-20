import streamlit as st
from google import genai
from google.genai import types

# Page setup - Ekdum Gaming aur Dark Theme Look
st.set_page_config(page_title="CHILL-CHAT AI ⚡", page_icon="🎮", layout="centered")

# Custom CSS for Neon Cyberpunk Interface
st.markdown("""
<style>
    body { background-color: #0d0f12; color: #e0e0e0; }
    .stApp { background-color: #0d0f12; }
    .main-title { text-align: center; color: #00ffcc; font-size: 40px; font-weight: bold; text-shadow: 0 0 10px #00ffcc; margin-bottom: 20px; }
    .zone-box { border-radius: 15px; padding: 15px; margin-bottom: 15px; border: 2px solid; }
    .gaming-box { border-color: #ff007f; background-color: #1a0a15; box-shadow: 0 0 10px #ff007f; }
    .study-box { border-color: #00ffff; background-color: #0a151a; box-shadow: 0 0 10px #00ffff; }
    .chat-box { border-color: #ffaa00; background-color: #1a150a; box-shadow: 0 0 10px #ffaa00; }
    .box-title { font-size: 20px; font-weight: bold; margin-bottom: 5px; }
</style>
""", unsafe_allowed_code_html=True)

st.markdown('<div class="main-title">CHILL-CHAT AI ⚡</div>', unsafe_allowed_code_html=True)

# API Key setup (Direct input via sidebar for ease on mobile)
with st.sidebar:
    st.markdown("### ⚙️ Setup Zone")
    api_key = st.text_input("Gemini API Key Daalo:", type="password")
    st.info("💡 Google AI Studio se free key le kar yahan paste karo.")

# Persona Setup - Jo sirf 'Fact' baatein karega aur bore nahi hone dega
personality_prompt = """
You are CHILL-CHAT AI, a modern, highly energetic, and ultra-smart assistant for a young Indian user. 
Your tone is like a gaming buddy (Bhai/Dost vibe) using cool Hinglish, but you never talk generic or boring stuff. 
When asked about facts, gaming, or studies, give concrete, shocking, or high-value FACTUAL insights. No textbook definitions!

Strict Guidelines:
1. GAMING HUB: Provide pro-strategies (BGMI, GTA, Valorant), patch analysis, or upcoming game leaks.
2. STUDY ZONE: Explain with logic, memory tricks, or real-world application. No boring essays.
3. CHAT/FACTS: Never let the user get bored. If they say they are bored, immediately drop a mind-blowing, unexpected real-world fact or a challenge.
"""

if api_key:
    # Initialize Client & Session
    if "chat" not in st.session_state:
        try:
            client = genai.Client(api_key=api_key)
            st.session_state.chat = client.chats.create(
                model="gemini-2.5-flash",
                config=types.GenerateContentConfig(system_instruction=personality_prompt)
            )
            st.session_state.messages = []
        except Exception as e:
            st.error(f"Initialization fail ho gaya: {e}")

    # Top Dashboard Sections
    col1, col2 = st.columns(2)
    with col1:
        st.markdown("""
        <div class="zone-box gaming-box">
            <div class="box-title">🎮 GAMING HUB</div>
            <p style="font-size:13px; color:#ff007f;">• BGMI Rush Strats Live<br>• GTA 6 Maps & Leaks</p>
        </div>
        """, unsafe_allowed_code_html=True)
    with col2:
        st.markdown("""
        <div class="zone-box study-box">
            <div class="box-title">📚 STUDY ZONE</div>
            <p style="font-size:13px; color:#00ffff;">• Coding Roadmaps<br>• Maths & Science Shortcuts</p>
        </div>
        """, unsafe_allowed_code_html=True)

    # Chat Area Design
    st.markdown('<div class="zone-box chat-box"><div class="box-title">💬 CHILL CHAT & FACTS</div></div>', unsafe_allowed_code_html=True)
    
    # Display previous chat messages
    for msg in st.session_state.messages:
        with st.chat_message(msg["role"]):
            st.write(msg["text"])

    # User Input
    if user_query := st.chat_input("Bhai se baat kar... (Ask anything or say 'Bore ho raha hu')"):
        # Append User Msg
        st.session_state.messages.append({"role": "user", "text": user_query})
        with st.chat_message("user"):
            st.write(user_query)
            
        # Get AI Response
        with st.chat_message("assistant"):
            try:
                response = st.session_state.chat.send_message(user_query)
                st.write(response.text)
                st.session_state.messages.append({"role": "assistant", "text": response.text})
            except Exception as e:
                st.error(f"Arre yaar errror aaya: {e}")
else:
    st.warning("👈 Pehle left side ke sidebar mein apni API Key daal do bhai, tabhi dashboard chalu hoga!")
