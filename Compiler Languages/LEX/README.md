# LEX

Scanner Language

## Build

```.sh
flex -o outputFileName.lex.yy.c lexFileName.l
gcc -o compiledFileName.out outputFileName.lex.yy.c -lfl
```

## Run

```.sh
./compiledFileName.out < input.txt
```

## Explanation .l File

```.l
// sample code
%{                  // this scope is the definition using C/C++ type
    unsigned int charCount=0, wordCount=0, lineCount=0;
%}
word    [^ \t\n]+   // this is the definition of token type in LEX
eol     \n
%%                  // when come up to a token, program do what in the right scope
                    // this is "if(word)...else if(eol)...else if(.)", which "." means all the word
                    // "{}" means nothing which you can use or not use
{word}  { wordCount++; charCount += yyleng; }
{eol}   { charCount++; lineCount++; }
.       charCount++;
%%
int main(int argc, char *argv[]) {
    yylex();        // the upon 2 scope rules use here
    printf("%u %u %u\n", lineCount, wordCount, charCount);
    return(0);
}
```

## Rules (Matching Patterns)

### [ ]

`[a-z]` is a scope means from `a` to `z`

### +

`[a-z]+` means at least 1 [a-z]

### \.

`.` means all the symbols except `\n`

### \*

`Bo*` are `B`, `Bo`, `Boo` ...etc, which means 0 or above numbers of `o` after `B`

### ?

`Bo?` are `B`, `Bo`, which means having or not having one `o`

### ^ at beginning of line

`^o` means at the beginning of a line has a symbol `o`

> etc. cannot use it alone

### [^ ]

`[^abc]` means the symbols in the `[ ]` scope is not the selected

### $

`$` means the end of a line

> etc. cannot use it alone

### |

`|` means "or", which `a|b` is equal to [ab]

### \

`\` is special, which is an escape character among these rules

> etc. `\.` means reading a `.` character

### { }

###### {times}

###### {least_times, most_times}

Usage: ok{2} === okok

###### containing definition of token type in LEX

```.l
// count which is digit
digit       [0-9]
digits      [1-9]{digit}*|0
%%
{digits}    { ECHO; printf(" is digits\n"); }
.           ;
%%
int main(int argc, char* argv[]) {
    yylex();
    return 0;
}
```

### " "

`" "`("abc") needs this string as a token, whereas `\`(\\.) just needs a character

### ( )

`( )` has the same meaning as in Math

## State

Use `BEGIN(_state_name_);` or `BEGIN _state_name_;` as a jump to any state label

```.l
//          define COMMENT state
%x COMMENT
//          define COMMENTS state
%x COMMENTS
%%
"//"                { BEGIN COMMENT; }
<COMMENT>[^\n]*     { ECHO; printf("\n--- above is comment.\n"); }
<COMMENT>\n         { BEGIN INITIAL; }
"/*"                { BEGIN COMMENTS; }
<COMMENTS>[^"*/"]*  { ECHO; printf("--- above are comments.\n"); }
<COMMENTS>"*/"      { BEGIN INITIAL; }
.                   ;
%%
int main(int argc, char* argv[]) {
    yylex();
    return 0;
}
```

### Initial State

Normal mode

### Any User Defined State

ex. COMMENT state, COMMENTS state
