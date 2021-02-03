# Handy command line scripts
A series of handy command line/bash/shell scripts I have found/munged in order to do stuff. 

## Convert all pdf files in a folder to txt
```
find ./ -name "*.pdf" -exec pdftotext {} \;
```

or try
```
find ./ -name "*.pdf" | xargs -L1 -I% pdftotext % /mnt/c/dest/%.txt
```

or try
```
for f in *.pdf; do pdftotext "$f" "/mnt/c/dest/${f%.*}.txt"; done
```

## Iterate over subdirectories with for loop and output pdftotext results to a single directory
```
for f in *.pdf **/*.pdf; do pdftotext -raw "`basename $f`" "/mnt/c/dest/${f%.*}.txt"; done
```

### Delete white space, tabs, newlines from txt file
```
for f in *.txt; do tr -d " \t\n\r" < "$f" > "${f%.txt}"--clean.txt; done
```

## Search for and highlight search term, output in html
```
ack [TERM HERE] -C3 --group --color --color-match='rgb555 on_rgb505' --color-filename='bold underline rgb000 on_rgb550' --sort-files --max-count=10 --column | aha > output.html -w
```

## Convert all html files in a folder into txt using html2text
```
for file in *.html; do html2text -utf8 -nobs -style pretty "$file" > "$file.txt"; done
```

## Convert a particular file to txt and put in a folder
```
for f in "`echo $(wslpath 'C:\Windows\Path\to\file.ext')`"; do pdftotext "$f" "/mnt/c/to/dest/ `echo ${f##*/}.txt`"; done
```

## Extract annotations from pdf files (requires pdfannots.py); run from Anaconda prompt
```
python C:\Users\dest\to\pdfannots-master\pdfannots.py "C:\dest\to\file.pdf" > output.txt
```


