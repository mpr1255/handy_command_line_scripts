# Handy command line scripts
A series of handy command line/bash/shell scripts I have found/munged in order to do stuff. 

## delete files of a certain type and size
find . -name "*.tif" -type 'f' -size -160k -delete
(-160k for less than, 160k for exactly; +160k for greater than. -type 'f' for only touching files, not recursing into folders)

find . -name "*.html" -type 'f' -size -3k -delete

# get all github shortlinks of a text file
for f in `cat links.txt`; do curl -i https://git.io -F url=${f} >> "output.txt"; done

# get one github shortlink
curl -i https://git.io -F url=https://github.com/mpr1255/robin/raw/main/munro_bibliography.docx


## Cat files with the filename
find *.txt -type f -print0 | xargs -0 -I % sh -c 'echo %; cat %'

## Convert all pdf files in a folder to txt
```
find ./ -name "*.pdf" -exec pdftotext {} \;
```

or try
```
find ./ -name "*.pdf" | xargs -L1 -I% pdftotext % /mnt/c/dest/%.txt
```
(`find` because the Stack commenters say that passing arguments to `xargs` using `ls` is bad form because it won't handle spaces or some other characters...)

or try (puts it in another dir)
```
for f in *.pdf; do pdftotext "$f" "/mnt/c/dest/${f%.*}.txt"; done
```

### But use this! Because it makes a log and puts std errors in it!
```
for f in *.pdf; do pdftotext -raw "$f" "/mnt/c/dest/${f%.*}.txt" > >(tee -a stdout.log) 2> >(tee -a stderr.log >&2); done
```

## Iterate over subdirectories with for loop and output pdftotext results to a single directory
```
for f in *.pdf **/*.pdf; do pdftotext "$f" "/mnt/c/dest/`basename ${f%.*}`.txt"; done
```

### Delete white space, tabs, newlines from txt file
```
for f in *.txt; do tr -d " \t\n\r" < "$f" > "/path/to/dest/${f%.txt}"--clean.txt; done
```

## ack Search for and highlight search term, output in html
```
ack [TERM HERE] -C3 --group --color --color-match='rgb555 on_rgb505' --color-filename='bold underline rgb000 on_rgb550' --sort-files --max-count=10 --column | aha > output.html -w
```

## rga search for a term, then another, highlight and output in html
```
rga [TERM HERE] -C10 --color always --heading --column | aha > output.html -w

rga [TERM] -C10 --color always --heading --column --colors 'match:fg:blue' --colors 'match:bg:yellow' | rg ".pdf|[OTHER TERM]" -C10 --color always --heading --column --colors 'match:fg:blue' --colors 'match:bg:yellow' | aha > output.html -w
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

## Delete all files of an extension in subdirectories
```
 find . -type f -name '*.ext' -delete
```

## Grab top few lines from a folder of pdfs; output in terminal

```
for f in *.pdf; do echo `pdftotext ${f} - | head -n 3 | tr -d '\n' |tr -d ':'`; done
```

# Dump an elastic db
for f in `cat target_text.txt`; do elasticdump --input="https://elastic:blablah/${f}" --output="${f}.json" --limit 500 --concurrency 5 --sourceOnly --noRefresh --fileSize 100 MB --fsCompress --type=data > >(tee -a stdout.log) 2> >(tee -a stderr.log >&2); done


# wget a single page 
wget -l 0 --mirror --convert-links --adjust-extension --page-requisites --no-parent http://news.sohu.com/20150306/n409390334.shtml

# curl multiple URLS, save them with the html files, and dump into txt
curl http://news.sohu.com/20170301/n482044089.shtml http://news.sohu.com/20161222/n476586079.shtml -O - -- >> curl_output.txt


# delete pages from a pdf
pdftk "input.pdf" cat 2-end output "input1.pdf"

# combine a pdf together
go to a folder where the pdf is and make sure ls displays it in order you want combined. then 
pdftk `ls *.pdf` cat output 'all_to_print_20210722_1504.pdf'

## rg search inside files of a certain type recursively 
rg "term" -g *.Rmd

rg "邪教" -g "邪教*.html"

## rg ripgrep files for a regex string, match only that string, and dump in a doc
rg -o "[0-9a-z]{32}"  -I | sort -u  > docids.txt

## rg return exact string match when searching
cat file.ext | rg -o -e '[a-z0-9]{32}' 

## rg return exact string match when searching then unique them
cat file.ext | rg -o -e '[a-z0-9]{32}' | sort -u

## rg delete files with a certain string using rg 
grep -l email@domain.com * | xargs rm

or safer:

find . | xargs grep -l email@domain.com | awk '{print "rm "$1}' > doit.sh
vi doit.sh // check for murphy and his law
source doit.sh

# loop over links in a text file and curl them to their own names
for f in `cat links.txt`; do curl -c cookiejar.txt -g -O -J -L "${f}"   -H 'Upgrade-Insecure-Requests: 1'   -H 'User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.164 Safari/537.36'   -H 'Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9'   -H 'Sec-GPC: 1'   --compressed   --insecure -o fileout.pdf; done
