
# Treasure

```
My friend Beale passed me this chunk of text, with the following message:

14-13-58-60-13-190-187-41-38-13-107-2-70-4-9-107-127-38-37-49-60-60-49-9-59-35

He told me that this message will lead me to find a treasure.
```

Reading the challenge name and description, it seems that we're supposed to find a treasure. The description seems to be of little use for now, so let's move on to the included file! The file seems to be a .docx file, and running `file` on it seems to confirm it.

![Running file command](/CHF_2022/treasure/images/filecommand.png)

We could use either `LibreOffice` or upload the file to Google Docs to view it, but since I'm too lazy to do either let's use this script from StackOverflow to simply extract all the text from the document! (https://stackoverflow.com/a/25620447)

```bash
unzip -p document.docx word/document.xml | sed -e 's/<\/w:p>/\n/g; s/<[^>]\{1,\}>//g; s/[^[:print:]\n]\{1,\}//g'
```

After reading through and making sure it doesn't do anything sus, let's run it on our document.
