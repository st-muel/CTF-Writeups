
# Treasure

```
My friend Beale passed me this chunk of text, with the following message:

14-13-58-60-13-190-187-41-38-13-107-2-70-4-9-107-127-38-37-49-60-60-49-9-59-35

He told me that this message will lead me to find a treasure.
```

Reading the challenge name and description, it seems that we're supposed to find a treasure. The description seems to be of little use for now, so let's move on to the included file! The file seems to be a .docx file, and running `file` on it seems to confirm it.

![Running file command](/CHF_2022/treasure/images/filecommand.png)

We could use either `LibreOffice` or upload the file to Google Docs to view it, but since I'm too lazy to do either let's use this command from StackOverflow to simply extract all the text from the document! (https://stackoverflow.com/a/25620447)

```bash
unzip -p document.docx word/document.xml | sed -e 's/<\/w:p>/\n/g; s/<[^>]\{1,\}>//g; s/[^[:print:]\n]\{1,\}//g'
```

After reading through and making sure it doesn't do anything sus, let's run it on our document.

![Command output](/CHF_2022/treasure/images/docxoutput.png)

Perfect! Hmmm, it seems to be the movie script for The Imitation Game. Lets redirect it into a text file first for easy access later.

```bash
unzip -p document.docx word/document.xml | sed -e 's/<\/w:p>/\n/g; s/<[^>]\{1,\}>//g; s/[^[:print:]\n]\{1,\}//g' > imitation_game.txt
```

Looking back at the description, the message doesn't seem to be in the format of an Enigma Cipher though. Let's google to see if any other ciphers were mentioned in the movie.

![Google Result](/CHF_2022/treasure/images/googleresult.png)

By googling "the imitation game ciphers" and scrolling down, there appears to be a very interesting result. A "Beale Cipher" seems to be mentioned in the movie, although erroneously it seems. It can't be a coincidence that our dear friend just so happens to have the same name! Going to the Wikipedia page for Beale ciphers, it seems that it is a book cipher that states the location of buried treasure, which matches the challenge name and description.

All the pieces seem to be falling into place now. A book cipher is being used with the script for "The Imitation Game" as the book, and the numbers in the message correspond to the position of a word in the book.

We could theoretically just start counting, but let's save ourselves and make a Python script instead.
```python
message = "14-13-58-60-13-190-187-41-38-13-107-2-70-4-9-107-127-38-37-49-60-60-49-9-59-35"
message = message.split("-")
message = map(int, message)

with open("imitation_game.txt", "r") as f:
  text = f.read()

print(text)
```

![Python Output 1](/CHF_2022/treasure/images/pythonoutput1.png)

Next, let's clean up the text by replacing newlines with a space and then seperating the individual words. Additionally, we need to remove any empty elements in the list.
```python
message = "14-13-58-60-13-190-187-41-38-13-107-2-70-4-9-107-127-38-37-49-60-60-49-9-59-35"
message = message.split("-")
message = map(int, message)

with open("imitation_game.txt", "r") as f:
  text = f.read()

text = text.replace("\n", " ")
text = text.split(" ")
text = filter(lambda x: x, text)

print(list(text))
```

![Python Output 2](/CHF_2022/treasure/images/pythonoutput2.png)

Perfect! That's exactly what we want! However, there seems to be some elements that are not words in the list, i.e. punctuations like `-`. This will crop up in situations like in the case of `ALAN TURING'S HOUSE - DAY - 1951`, where `-` will become an element even though it is not a word since it is seperated by a space.
We can account for this by simply removing any element that is a punctuation.

```python
text = list(filter(lambda x: x and x not in string.punctuation, text))
```

Now, let's get the respective words according to the indexes in the message. Don't forget to account for the fact that the indexes in the message start from 1!

```python
import string

message = "14-13-58-60-13-190-187-41-38-13-107-2-70-4-9-107-127-38-37-49-60-60-49-9-59-35"
message = message.split("-")
message = map(int, message)

with open("imitation_game.txt", "r") as f:
  text = f.read()

text = text.replace("\n", " ")
text = text.split(" ")
text = list(filter(lambda x: x and x not in string.punctuation, text))

for i in message:
  print(text[i-1], end="")
```

![Python Output 3](/CHF_2022/treasure/images/pythonoutput3.png)

Okay this makes no sense at all and is way too long in the first place. Could it be the first letter of each word then?

```
BealeCipherIsWorthMillions
```

Bingo! The final Python code:
```python
import string

message = "14-13-58-60-13-190-187-41-38-13-107-2-70-4-9-107-127-38-37-49-60-60-49-9-59-35"
message = message.split("-")
message = map(int, message)

with open("imitation_game.txt", "r") as f:
  text = f.read()

text = text.replace("\n", " ")
text = text.split(" ")
text = list(filter(lambda x: x and x not in string.punctuation, text))

for i in message:
  print(text[i-1][0], end="")
```

All in all, this was a really fun challenge and I enjoyed solving it after banging my head on it for a while!
