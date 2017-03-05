 How I Write AWK Code

I first starting using AWK in 1985. After 30+ years
of using the language, here are some of the tricks 
I've learned.

# Test Suites

All my files `code.awk` have a campanion files `codeok.awk`
that tests that file:

-  The first line of that `ok` file is
   always an include to the other file
-  The last line is a `BEGIN` block that calls the
   test function.

For example, here's a very simple `ok` file:

```
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


```awk 
function oks(tests,   a,f,i,n) {
   n = split(tests,a,",")
   for(i=1;i<=n;i++) {
     f = a[i]
     printf("#TEST:\t" f "\t")
     @f()
}}
function ok(want,got) {
   print want "\t" got (want == got ?"" : "\tFAIL")
}
```

This all prints some triples

```
testName <tab> expected <tab> got  
```

and for triples where expected != got, there is an appended
"FAIL" file

# Less Globals

`N-1` globals is usually better than `N`. So I use many
tricks to avoid the need for global variables.

Firsly, I carry around my state as nested arrays, the
structure of which I initialize in constructors.


```awk 
function Table(i) {
   have(i,"rows")
   have(i,"cols","Cols")
}
```


The `have` function ensures that some variable is a nested array with certain keys (in this case,
`rows` and `cols`).



```awk 
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


```awk 
function Cols(i) {
  i["n"]   = 0
  i["sum"] = 0
}
```


# Less `/pattern/ {action}`

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
  



```awk 
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

```awk 
```
