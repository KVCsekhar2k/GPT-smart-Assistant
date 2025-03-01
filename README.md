# GPT-smart-Assistant
GPT smart assistance with voice commanding
from openai import OpenAI  
import openai  

from chatGptApi import api_data
import os
import speech_recognition as sr
import pyttsx3
import webbrowser

Model = "gpt-4o"
client = OpenAI(api_key=api_data)  # Adjust this for openai v0 if needed

def Reply(question):
    """
    Generate a response to a given question using OpenAI's chat completion API.
    """
    completion = client.chat.completions.create(
        model=Model,
        messages=[
            {"role": "system", "content": "You are a helpful assistant."},
            {"role": "user", "content": question},
        ],
        max_tokens=200,
    )
    answer = completion.choices[0].message.content
    return answer

# Text-to-Speech
engine = pyttsx3.init("sapi5")  # Fixed engine type
voices = engine.getProperty("voices")  # Fixed property name
engine.setProperty("voice", voices[0].id)  # Fixed variable reference

def speak(text):
    engine.say(text)
    engine.runAndWait()

speak("Hello, I'm Raaju, your personal assistant. How can I help you?")

def takecommand():
    r = sr.Recognizer()
    with sr.Microphone() as source:
        print("Listening....")
        r.pause_threshold = 1
        audio = r.listen(source)
    try:
        print("Recognizing....")
        query = r.recognize_google(audio, language="en-in")
        print(f"User Said: {query} \n")
    except Exception:
        print("Say That Again....")
        return "none"
    return query.lower()  # Ensure consistency in string comparison

if __name__ == "__main__":
    while True:
        query = takecommand()
        
        if query == "none":
            continue
        
        ans = Reply(query)
        print(ans)
        speak(ans)

        if "open youtube" in query:
            webbrowser.open("http://www.youtube.com")
        elif "open google" in query:
            webbrowser.open("http://www.google.com")
        elif "bye" in query:  # Fixed 'break' logic
            break

