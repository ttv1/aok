# How I Write AWK Code

I've been writing AWK
code for 
decades.
After all that time,
here are some of the tricks 
I've learned.

# Share Code, On-line

My code is on-line from day1 is some public Gitbub repo. This can be
very useful-- it there are ten people in the world who care about your
work, then that's the way they might find you.

Github now can make any repo a website,
serving up all the markdown files from a `docs` sub-directory.
So  to write a site, like the one you are reading
right now, is just a matter of adding markdown
files into the right directory.

## Literate Programming

Code needs commentary-- not too much-- that helps
other programmers to understand the code.
I use markdown interwoven with the code in multi-line 
comments (beginning and end marked with `===`).  Using 
a pre-prossor, I then pull the source apart
into an (1)executable awk code file and (2)a markdown files.

My pre-processor which I call 
[aok](https://github.com/ttv1/aok/blob/master/aok),
inputs `*.aok` files then generates
`*.awk` and `*.md` files.

```
                   /--- x.md
x.aok --> aok --> /
                  \ 
                   \--- x.awk
```
Note that I need to trick my editor into treating `*.aok`
as `*.awk` files so I add a modeline at the top:

     # /* vim: set filetype=awk ts=2 sw=2 sts=2 et : */

So my standard directory structure 
looks like this: 

```
manager.sh
file1.aok
file2.aok
...
docs
  file1.md
  file2.md
  ...
_var
  awk
    file1.awk
  tmp
    
```
Note that the code-as-markdown likes in `docs` and
the ready-to-run AWK
likes in `_var/awk/*.awk`.

## Change How I call AWK

To make all that work, I change how I call AWK such
that it knows to add `_var/awk` to its path:
```sh
AWKPATH="$Awk:$AWKPATH" gawk          \
       --dump-variables=$Tmp/awkvars.out \
       --profile=$Tmp/awkprof.out         \
       -f $1.awk
vars;
```

## Debugging tools

AWK's variables are global by default unless declared as 
extra arguments in function headers. After spending
too much of my life tracking down bugs from globals
thata have gone rogue, now I:

- Always define locals starting with a lower case letter
- Always define globals as MixedCase starting an Uppercase letter.
- Always call AWK with `--dump-variables`
- Check that dump afterwards for globals,
  ignoring the AWK built-ins.

```sh
if [ -f "$Tmp/awkvars.out" ]
then
  egrep -v '[A-Z][A-Z]' $Tmp/awkvars.out |
  sed 's/^/W> rogue local: /'
fi
```
## Test Suites

All my files `code.awk` have a campanion files `codeok.awk`
that tests that file:

-  The first line of that `ok` file is
   always an include to the other file
-  The last line is a `BEGIN` block that calls the
   test function.

For example, here's a very simple `ok` file:

```c
@include "codingtips"
function _top1(a) { ok(0, 10 == 10) }
BEGIN { oks("_top1") }
```

Note the two functions 

- `ok` is called once per test.
- `oks` is called once and is the top-level driver; e.g.

      oks("_test1,test2,..")

Here, `_test1` is a function that calls `ok` to
report if we `got` what we `want`ed.


```c 
function oks(tests,   a,f,i,n) {
   n = split(tests,a,",")
   for(i=1;i<=n;i++) {
     f = a[i]
     printf("#TEST:\t" f "\t")
     @f()
}}
function ok(want,got) {
   print want "\t" got (want == got ?"\tPASSED" : "\tFAILED")
}
```

This all prints some triples

```
testName <tab> expected <tab> got  
```

and for triples where expected != got, there is an appended
"FAIL" file. 

## Less Globals

`N-1` globals is usually better than `N`. So I use many
tricks to avoid the need for global variables.

Firsly, I carry around my state as nested arrays, the
structure of which I initialize in constructors.


```c 
function Table(i) {
   have(i,"rows")
   have(i,"cols","Cols")
}
```


The `have` function ensures that some variable is a nested array with certain keys (in this case,
`rows` and `cols`).



```c 
function have(lst,key,fun)   { 
  lst[key][1];    
  delete lst[key][1]
  if (fun)
    @fun(lst[key])
}
```


`Have` has an optional third argument which, is used,
defines some nested constructor. For example, in the above,
the `Cols` constructor was offered as the way to initialize
the `cols` key of a `Table`:


```c 
function Cols(i) {
  i["n"]   = 0
  i["sum"] = 0
}
```


## Much Less `/pattern/ {action}`

Most of my code makes no use of pattern-directed
programming.  Why? Well, you can't build libraries of
reusable code if every
line in the library wants to takeover the top-level control for itself. 
So when  I need to read from files or
  standard input, I to things like the following.
Here, `t` is the `Table` initialized above and

- `line` is the function used to read the next line;
- `f0,f1` are functions called to update the `Table`
       - `f0` processes the header of the file (on line 1);
       - `f1` processes all the all line
  



```c 
function readcsv(file,t,line,f0,f1,    str,a,n) {
  str = @line(file)
  while(str != -1) {
    split(str,a,FS)
    if(n++) 
      @f1(t,a)
    else    
      @f0(t,a)
    str = @line(file)
  }
  close(file)
}

function _readline(file,   str) {
  # if a line ends with ",", it is continues on next line
  if ((getline str < file) > 0) {
    gsub(/[ \t\r]*/,"",str)  # kill whitespace
    gsub(/#.*$/,"",str)      
    if ( str ~ /,$/ )
      return str _readcsvLine(file)
    else
      return str
  }
  return -1
}
function _readcsvHead(t,lst,i,
                      klassed,n,txt) {
  for(n in lst) {
    txt = lst[n]
	 	have(t.cols.all,n)
    Column(t.cols.all[n],txt,n)
    if (txt ~ /^</)  {
       klassed++
       t.cols.less[n]
    } else if (txt ~ /^>/)  {
       klassed++
       t.cols.more[n]
    } else if (txt ~ /^!/)  {
       klassed++
		   t.cols.klass[n]
	  } else
       t.cols.indep[n]
  }
}
function _readcsvRow(t,lst,   j) {
   j=length(t.rows)+1
   have(t.rows,j,"Row")
   Row1(t.rows[j], t, lst)
}

```

- Define all locals. Add extra arguments to function definitions so none of the temporaries
  used in 

AWK locals can be defined as extra function arguments.

```c 
```

