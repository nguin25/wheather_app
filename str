import requests
import streamlit as st

# --- Page setup (equivalent to setWindowTitle + stylesheet) ---
st.set_page_config(page_title="Weather App", layout="centered")

st.markdown("""
<style>
/* mimic your Qt stylesheet */
html, body, [data-testid="stAppViewContainer"] { font-family: Calibri, sans-serif; }

/* labels */
.city_label{ font-size:40px; font-style:italic; text-align:center; margin-bottom:0.25rem; }

/* text input – center text & enlarge */
.stTextInput > div > div > input { text-align:center; font-size:40px; }

/* button – bold/large and centered */
.stButton > button { font-size:30px; font-weight:bold; display:block; margin: 0.5rem auto 1rem auto; }

/* outputs */
.temperature_label{ font-size:75px; text-align:center; }
.temperature_label.small{ font-size:30px; text-align:center; }
.emoji_label{ font-size:100px; font-family:"Segoe UI Emoji"; text-align:center; line-height:1.1; }
.description_label{ font-size:50px; text-align:center; }
</style>
""", unsafe_allow_html=True)

# ---------- UI (mirrors your widgets & order) ----------
st.title("Weather App")

st.markdown('<div class="city_label">Ingiza jina la eneo: </div>', unsafe_allow_html=True)
city = st.text_input("", key="city_input")

# Placeholders that behave like your QLabel widgets
temp_ph = st.empty()
emoji_ph = st.empty()
desc_ph = st.empty()

# ---------- Logic (1:1 with your methods) ----------
def get_weather_emoji(weather_id: int) -> str:
    if 200 <= weather_id <= 232:
        return "⛈"
    elif 300 <= weather_id <= 321:
        return "🌦"
    elif 500 <= weather_id <= 531:
        return "🌧"
    elif 600 <= weather_id <= 622:
        return "❄"
    elif 701 <= weather_id <= 741:
        return "🌫"
    elif weather_id == 762:
        return "🌋"
    elif weather_id == 771:
        return "💨"
    elif weather_id == 781:
        return "🌪"
    elif weather_id == 800:
        return "☀"
    elif 801 <= weather_id <= 804:
        return "☁"
    else:
        return ""

def display_error(message: str) -> None:
    # same behavior: show message in temperature label, clear others
    temp_ph.markdown(f'<div class="temperature_label small">{message.replace("\n","<br>")}</div>',
                     unsafe_allow_html=True)
    emoji_ph.empty()
    desc_ph.empty()

def display_weather(data: dict) -> None:
    # same math & fields as your original code
    temperature_k = data["main"]["temp"]
    temperature_c = temperature_k - 273.15
    weather_id = data["weather"][0]["id"]
    weather_description = data["weather"][0]["description"]

    temp_ph.markdown(f'<div class="temperature_label">{temperature_c:.0f}°c</div>', unsafe_allow_html=True)
    emoji_ph.markdown(f'<div class="emoji_label">{get_weather_emoji(weather_id)}</div>', unsafe_allow_html=True)
    desc_ph.markdown(f'<div class="description_label">{weather_description}</div>', unsafe_allow_html=True)

def get_weather(city_name: str) -> None:
    # Keep your API key behavior; use st.secrets if available (safer for Cloud)
    API_key = st.secrets.get("API_key", "b0b0f07943383e003c7c20ffbc89544e")
    url = f"https://api.openweathermap.org/data/2.5/weather?q={city_name}&appid={API_key}"

    try:
        response = requests.get(url, timeout=15)
        response.raise_for_status()
        data = response.json()
        if data.get("cod") == 200:
            display_weather(data)
        else:
            # If API returns JSON error with non-200 cod
            display_error(str(data))
    except requests.exceptions.HTTPError as http_error:
        status = getattr(response, "status_code", None)
        if status == 400:
            display_error("Bad request:\nPlease check your input")
        elif status == 401:
            display_error("Unauthorized:\nInvalid API key")
        elif status == 403:
            display_error("Forbidden:\nAccess is denied")
        elif status == 404:
            display_error("Not found:\nCity not found")
        elif status == 500:
            display_error("Internal Server Error:\nPlease try again later")
        elif status == 502:
            display_error("Bad Gateway:\nInvalid response from the server")
        elif status == 503:
            display_error("Service Unavailable:\nServer is down")
        elif status == 504:
            display_error("Gateway Timeout:\nNo response from the server")
        else:
            display_error(f"HTTP error occurred:\n{http_error}")
    except requests.exceptions.ConnectionError:
        display_error("Connection Error:\nCheck your internet connection")
    except requests.exceptions.Timeout:
        display_error("Timeout Error:\nThe request timed out")
    except requests.exceptions.TooManyRedirects:
        display_error("Too many Redirects:\nCheck the URL")
    except requests.exceptions.RequestException as req_error:
        display_error(f"Request Error:\n{req_error}")

# ---------- Button (same label) ----------
if st.button("Bofya hapa"):
    if city.strip():
        get_weather(city.strip())
    else:
        display_error("Bad request:\nPlease check your input")
