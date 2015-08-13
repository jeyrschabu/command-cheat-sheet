# Commands Cheat Sheet
compilation of useful commands used on a daily basis

1. Perl One-liners:
- Search and Replace:
    - Flags:
        + -p : makes sure the code gets executed on everly line then gets printed out
        + -i : gets edited inline
        + -e : indicate this as commandline 
        
        - examples:
            a. perl -pi -e 's/search/replace/g' file
            b. perl -pi -e 's/you/me/g if /we/' file #if the line matches the word we
            c. perl -pi -e 's/you/me/g if /\d/' file # if the line matches numbers

- Find repeated lines in a file:
    - Flags:
        + -n : loops over the input but unlike -p doesn't print the lines automatically
        + -e : see Search and Replace section

        - examples:
            a. perl -ne 'print if $a{$_}++' file
                * %a is a hash and keeps the counter of how many times it has seen the same line
                * if the count is > 0 then the line gets printed
            b. perl -ne 'print unless $a{$_}++' file
            
- Numbering lines
    
        - examples:
            a. perl -pe '$_="$. $_"'
            b. perl -ne 'print "$. $_" if $a{$_}++' file1.txt #print duplicates with line numnber
            c. perl -ne 'print "$. $_"' file1.txt
            d. perl -pe '$_="$. $_"' file1.txt

2. AWK
- Double-Space A File:
    every awk program consists of pattern-action statements "pattern{action statements}"
    ORS - Output Record Separator (\n by default)
    
        - examples:
            a. awk '1;{print ""}'
            b. awk 'BEGIN {ORS="\n\n"}; 1' file1.txt
                * BEGIN is a special kind of pattern which is not tested against the input.executed before any input is read
            c. awk 'NF {print $0 "\n"}' file1.txt
                * NF (number of fields). it contains the number of fields the current line was split into. this cmd says print a line if NF is not zero
                
- Triple-Space A File:

        - examples:
            a. awk '1;{print "\n"}'
            
- Numbering and Calculations

        - examples:
            a. Number Lines in each file separately
                awk '{ print FNR "\t" $0 }' file1.txt
                    *FNR (File Line Number)
            b. Number lines for all files together
                awk '{ print NR "\t" $0}' file1.txt file2.txt
                    *NR (Line Number)
            c. Number lines in a fancy manner
                awk '{printf("%5d : %s\n", NR, $0)}' file1.txt
            d. Number only non-blank lines in files.
                awk 'NF {$0=++a " :" $0 }; {print}'
            e. Count lines in files (emulates WC -l)
                awk 'END {print NR}'
            f. Print the sum of fields in all lines
                awk '{s = 0; for (i = 1; i <= NF; i++) s = s+$1; print s}'
            g. Count the total number of fields (words) in a file
                awk '{ total = total + NF}; END {print total+0}'
            h. Print the total number of lines containing word "no"
                awk '/no/ { n++ }; END { print n+0 }'
            i. Print the number of fields in each line, followed by the line
                awk '{print NF ":" %0 } '
            j. Print the last field of earch line
                awk '{print $NF }'
            k. Print every line with more than 4 fields
                awk 'NF > 4'
            l. Print every lien where the value of the last field is greater than 4
                awk '$NF > 4'
- Text Conversion and Substitution
    a. Convert Windows/DOS new liens (CRLF) to Unix newlines (LF) from Unix
        awk '{sub(/\r$/, ""); print}'
    b. opposite of .a
        awk 1
    c.	Delete leading whitespace(spaces and tabs) from the beginning of each line
        awk '{sub(/[\t]+$/, ""); print }'
    d. Delete both leading and trailing whitespaces from each line
        awk '{gsub(/^[\t]+|[\t]+$/, ""); print }'
    e. Remove whitespace between fields
        awk '{$1=$1; print} '
    f. Insert 5 blank spaces at the beginning of each line
        awk '{sub(/^/, "     "); print}'

    g. Find and replace "foo " with "bar" on each line
        awk '{sub(/foo/, "bar"); print}'
        NOTE: only replace the first match. use gsub to replace globally

    h. Find and replace with regex
        awk '/baz/ {gsub(/foo/, "bar")}; {print}'
    i. More on search and replace:
        a. awk '{ gsub(/scarlet|ruby|puce/, "red"); print}'
        
- Search and Print
    a. AWK - print only matching field itself and not line
        awk 'match($0,/regexp/) {print substr($0,RSTART,RLENGTH)}' inputfile
        i.e Print anything starting with isc and ending with Item
        $ awk 'match($0, /isc[a-zA-Z]*Item/) {print substr($0, RSTART, RLENGTH)}' *


3. SED
4. Grep
5. VIM
6. Unix
7. Examples:
a. Renaming files:
    ls | awk '$1 ~/^file/ {print "mv "$1" "$1".new"}'| sh
b. Renaming within filename
    ls *new* | awk '{print "mv "$1" "$1}' | sed s/new/jeyrs/2 | sh
c. Remove duplicate and non consecutive lines 
    awk '!($0 in array){array[$0]; print}' aFile
d. Remove big archive files using find 
    find / -type f -name *.zip -size +100M -exec rm -i {} \;"
e. Find the top 5 small files
    find . -type f -exec ls -s {} \; | sort -n | head -5
f. Find & Replace recursively
    find -name '*.txt' -exec sed -i 's/foo/bar/g' {} +
g. batch renaming of extension for .wav.wav to .wav
    ls *.wav | awk '{s=substr($1,1,length($1)-4); print "mv " s ".wav " s ""}'| sh
h. Replace all '.txt' file extension to '.log' extension and display "rename" command in console:
    ls *.txt |awk ' {s=substr($1, 1, length($1) - 4); print "mv " s ".txt " s ".log"}'

    * Executable mode of bulk replace file extension from '.txt' to '.log':
        ls *.txt |awk ' {s=substr($0, 1, length($0) - 4); system("mv " s ".txt " s ".log")}'
