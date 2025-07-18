import speech_recognition as sr
import pyttsx3
import datetime
import webbrowser
import wikipedia
import pyjokes
import os
import smtplib

# Initialize engine
engine = pyttsx3.init()
engine.setProperty('rate', 175)  # speaking speed
voices = engine.getProperty('voices')
engine.setProperty('voice', voices[1].id)  # female voice

def speak(text):
    print("Assistant:", text)
    engine.say(text)
    engine.runAndWait()

def listen():
    r = sr.Recognizer()
    with sr.Microphone() as source:
        print("Listening...")
        r.pause_threshold = 1
        audio = r.listen(source)
    try:
        print("Recognizing...")
        query = r.recognize_google(audio, language='en-in')
        print(f"You said: {query}")
    except:
        speak("Sorry, could not understand.")
        return ""
    return query.lower()

def wish():
    hour = datetime.datetime.now().hour
    if hour < 12:
        speak("Good morning!")
    elif hour < 18:
        speak("Good afternoon!")
    else:
        speak("Good evening!")
    speak("I am your AI assistant. How can I help you today?")

def send_email(to, content):
    # You must enable "Less secure apps" or app password
    server = smtplib.SMTP('smtp.gmail.com', 587)
    server.starttls()
    server.login("your_email@gmail.com", "your_password")
    server.sendmail("your_email@gmail.com", to, content)
    server.quit()

def main():
    wish()
    while True:
        query = listen()

        if 'wikipedia' in query:
            speak("Searching Wikipedia...")
            query = query.replace("wikipedia", "")
            results = wikipedia.summary(query, sentences=2)
            speak("According to Wikipedia")
            speak(results)

        elif 'open youtube' in query:
            webbrowser.open("https://www.youtube.com")

        elif 'open google' in query:
            webbrowser.open("https://www.google.com")

        elif 'open notepad' in query:
            os.system("notepad")

        elif 'time' in query:
            strTime = datetime.datetime.now().strftime("%H:%M:%S")
            speak(f"The time is {strTime}")

        elif 'date' in query:
            strDate = datetime.datetime.now().strftime("%Y-%m-%d")
            speak(f"Today's date is {strDate}")

        elif 'joke' in query:
            joke = pyjokes.get_joke()
            speak(joke)

        elif 'email' in query:
            try:
                speak("What should I say?")
                content = listen()
                to = "receiver_email@example.com"
                send_email(to, content)
                speak("Email sent successfully.")
            except Exception as e:
                print(e)
                speak("Sorry, I was unable to send the email.")

        elif 'note' in query:
            speak("What should I write?")
            note = listen()
            with open("notes.txt", "a") as f:
                f.write(f"{datetime.datetime.now()}: {note}\n")
            speak("Note saved.")

        elif 'read note' in query:
            try:
                with open("notes.txt", "r") as f:
                    speak("Reading your notes:")
                    speak(f.read())
            except FileNotFoundError:
                speak("No notes found.")

        elif 'exit' in query or 'stop' in query:
            speak("Goodbye!")
            break

        else:
            speak("Sorry, I didn't get that. Try again.")

if __name__ == "__main__":
    main()
