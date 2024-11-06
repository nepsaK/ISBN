import requests
import pyttsx3  # For text-to-speech functionality
import json

def fetch_book_summary(isbn):
    url = f"https://www.googleapis.com/books/v1/volumes?q=isbn:{isbn}"
    response = requests.get(url)
    if response.status_code == 200:
        data = response.json()
        if 'items' in data:
            return data['items'][0]['volumeInfo']
    return None

def translate_text(text, target_language, api_key):
    url = f"https://translation.googleapis.com/language/translate/v2"
    params = {
        'q': text,
        'target': target_language,
        'key': api_key
    }
    response = requests.post(url, data=params)
    if response.status_code == 200:
        translation = response.json()
        return translation['data']['translations'][0]['translatedText']
    return None

def text_to_speech(text, language_code):
    engine = pyttsx3.init()
    engine.setProperty('voice', language_code)
    engine.say(text)
    engine.runAndWait()

def main():
    isbn = input("Enter the ISBN number of the book: ")
    target_language = input("Enter the target language code (e.g., 'es' for Spanish): ")
    api_key = input("Enter your Google Translate API key: ")

    book_info = fetch_book_summary(isbn)
    
    if book_info:
        title = book_info.get('title', 'No title found')
        authors = ', '.join(book_info.get('authors', ['No authors found']))
        description = book_info.get('description', 'No summary found')

        print(f"Title: {title}\nAuthors: {authors}\nDescription: {description}\n")

        translated_summary = translate_text(description, target_language, api_key)
        print("Translated Summary:")
        print(translated_summary)

        text_to_speech(translated_summary, 'com.apple.speech.synthesis.voice.samantha')  # Adjust based on your OS

        chapters = book_info.get('tableOfContents', [])
        if chapters:
            print("\nAvailable Chapters:")
            for chapter in chapters:
                print(chapter)
            chapter_number = int(input("Enter chapter number to summarize: "))
            chapter_summary = f"Summary of Chapter {chapter_number}..."  # Placeholder for chapter summary
            translated_chapter_summary = translate_text(chapter_summary, target_language, api_key)
            print(f"Translated Chapter Summary:\n{translated_chapter_summary}")

        vocabulary = ["word1", "word2"]  # This would ideally come from the text
        print("\nVocabulary Builder:")
        for word in vocabulary:
            translation = translate_text(word, target_language, api_key)
            print(f"{word} -> {translation}")

    else:
        print("Book not found or no summary available.")

if __name__ == "__main__":
    main()
