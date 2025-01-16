import pyttsx3
import speech_recognition as sr
import wikipedia
import pyjokes
import datetime
import os
import webbrowser

# Initialize the pyttsx3 engine (Text-to-Speech)
engine = pyttsx3.init()
engine.setProperty('rate', 150)  # Speed of speech
engine.setProperty('volume', 1)  # Volume (0.0 to 1.0)

def speak(text):
    engine.say(text)
    engine.runAndWait()

def listen():
    recognizer = sr.Recognizer()
    with sr.Microphone() as source:
        print("Listening...")
        recognizer.adjust_for_ambient_noise(source)
        audio = recognizer.listen(source)
    
    try:
        print("Recognizing...")
        query = recognizer.recognize_google(audio, language="en-US")
        print(f"You said: {query}")
        return query.lower()
    except sr.UnknownValueError:
        print("Sorry, I did not understand that.")
        return None
    except sr.RequestError:
        print("Could not request results, check your network connection.")
        return None

def greet():
    hour = datetime.datetime.now().hour
    if hour < 12:
        speak("Good morning!")
    elif 12 <= hour < 18:
        speak("Good afternoon!")
    else:
        speak("Good evening!")
    speak("I am Jarvis. How can I assist you today?")

def tell_joke():
    joke = pyjokes.get_joke()
    speak(joke)

def search_wikipedia(query):
    try:
        result = wikipedia.summary(query, sentences=2)
        speak(result)
    except wikipedia.exceptions.DisambiguationError as e:
        speak(f"There are multiple results for {query}. Could you be more specific?")
    except wikipedia.exceptions.HTTPTimeoutError:
        speak("Sorry, I couldn't connect to Wikipedia. Please check your internet connection.")

def open_website(url):
    webbrowser.open(url)
    speak(f"Opening {url}")

def get_time():
    current_time = datetime.datetime.now().strftime("%H:%M")
    speak(f"The current time is {current_time}")

def main():
    greet()
    
    while True:
        query = listen()

        if query is None:
            continue

        if 'hello' in query or 'hi' in query:
            greet()

        elif 'what is' in query or 'who is' in query:
            query = query.replace('what is', '').replace('who is', '')
            search_wikipedia(query)

        elif 'joke' in query:
            tell_joke()

        elif 'time' in query:
            get_time()

        elif 'open' in query:
            if 'youtube' in query:
                open_website("https://www.youtube.com")
            elif 'google' in query:
                open_website("https://www.google.com")
            else:
                speak("Which website do you want me to open?")

        elif 'exit' in query or 'quit' in query:
            speak("Goodbye! Have a great day!")
            break

if __name__ == "__main__":
    main()
