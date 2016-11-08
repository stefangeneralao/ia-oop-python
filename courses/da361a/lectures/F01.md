---
layout: instructions
title: Repetition
code: da361a
---

# Repetition

<div class="video">
    <iframe src="//www.slideshare.net/slideshow/embed_code/key/JAjZh9fFjbMjSe" width="595" height="485" frameborder="0" marginwidth="0" marginheight="0" scrolling="no" style="border:1px solid #CCC; border-width:1px; margin-bottom:5px; max-width: 100%;" allowfullscreen> </iframe> <div style="margin-bottom:5px"> <strong> <a href="//www.slideshare.net/AntonTibblin/ht16-da361a-repetition" title="HT16 - DA361A - Repetition" target="_blank">HT16 - DA361A - Repetition</a> </strong> from <strong><a target="_blank" href="//www.slideshare.net/AntonTibblin">Anton Tibblin</a></strong> </div>
</div>

[Klicka här för att ladda ner PDF:n](/assets/pdf/f1.pdf)

[Länk till tjänsten vi byggde](http://fjortiz.pythonanywhere.com)

## Källkod

### 1. Fjortiz, orginal
{% highlight python linenos %}
import json
import random

fjortiz_dict = False

def app():
    global fjortiz_dict
    fjortiz_dict = read_dictionary("fjortiz.json")
    choice = False
    while choice != "0":
        print_menu()
        choice = input("Ange val: ")
        if choice == "1":
            translate_user_input()
        elif choice == "2":
            add_word()
        elif choice == "0":
            print("*"*40)
            print("Hejdå!")
            print("*"*40)
            exit()
        print("\n")

def translate_user_input():
    input_text = input("Vad vill du översätta? ").lower()

    # Kontrollerar om något av de nycklar som finns i lexikonet även finns i den sträng som användaren matar in
    for word in fjortiz_dict["words"]:
        input_text = input_text.replace(word, fjortiz_dict["words"][word])

    # Fixa särskrivning
    for word in fjortiz_dict["separate"]:
        input_text = input_text.replace(word, word+" ")

    input_text = input_text.replace(".", change_dot())   

    print("*"*40)
    print(input_text)
    print("*"*40)

def change_dot():
    dot_ending = fjortiz_dict["random"]
    return " {} ".format(random.choice(dot_ending))

def add_word():
    word = input("Vilket ord vill du byta ut? ")
    new_word = input("Vad ska det bytas ut mot? ")
    global fjortiz_dict
    fjortiz_dict["words"][word] = new_word
    save_dict()

def save_dict():
    try:
        the_file = open("fjortiz.json", "w")
        the_file.write(json.dumps(fjortiz_dict, indent=4))
    except:
        print("Kunde inte spara filen")

def read_dictionary(file_name):
    try:
        with open(file_name) as data_file:
            data = json.load(data_file)
            return data
    except:
        print("Det gick inte att läsa in lexikonet")
        exit()

def print_menu():
    print("*"*40)
    print("Meny")
    print("*"*40)
    print("1) Gör om till fjortizspråk")
    print("2) Lägg till nytt ord i ordlistan")
    print("0) Stäng programmet")

app()
{% endhighlight %}

### 2.a. Bottle (app_bottle.py)
{% highlight python linenos %}
from bottle import route, run, template, request
import fjortiz

@route('/')
def index():
    return template("index", text="")

@route('/translate', method="POST")
def translate():
    user_message = request.forms.message
    translated_message = fjortiz.translate_user_input(user_message)
    return template("index", text=translated_message)

run(host='localhost', port=8080, debug=True)
{% endhighlight %}

### 2.b. Fjortiz
{% highlight python linenos %}
import json
import random

fjortiz_dict = False

def translate_user_input(input_text):

    global fjortiz_dict
    fjortiz_dict = read_dictionary("fjortiz.json")

    input_text = input_text.lower()

    # Kontrollerar om något av de nycklar som finns i lexikonet även finns i den sträng som användaren matar in
    for word in fjortiz_dict["words"]:
        input_text = input_text.replace(word, fjortiz_dict["words"][word])

    # Fixa särskrivning
    for word in fjortiz_dict["separate"]:
        input_text = input_text.replace(word, word+" ")

    input_text = input_text.replace(".", change_dot())

    return input_text

def change_dot():
    dot_ending = fjortiz_dict["random"]
    return " {} ".format(random.choice(dot_ending))

def read_dictionary(file_name):
    try:
        with open(file_name) as data_file:
            data = json.load(data_file)
            return data
    except:
        print("Det gick inte att läsa in lexikonet")
        exit()
{% endhighlight %}

### 3. fjortiz.json
{% highlight json linenos %}
{
    "separate": [
        "j\u00e4tte",
        "super",
        "svin",
        "as"
    ],
    "words": {
        "hej": "tjeniz",
        "ok": "okii",
        "bara": "ba'",
        "!": " \u00e5 s\u00e5nt!",
        "vara": "va",
        "orka": "palla",
        "puss": "puzz",
        "h\u00e4ftigt": "abow",
        "jag": "ja",
        "taskig": "obror",
        "kram": "kjamizz",
        "nyb\u00c3\u00b6rjare": "n00b",
        "okej": "kej",
        "kul": "fr\u00e4scht",
        "hejd\u00e5": "kjamiz <3<3",
        "cool": "swag",
        "kompis": "chiefen",
        "?": " \u00e5 s\u00e5nt?",
        "tusen": "papp",
        ", ": ", typ"
    },
    "random": [
        "typ",
        "liksom",
        "ba'",
        "yolo"
    ]
}
{% endhighlight %}
