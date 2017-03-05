# Tutorial

(For instalation notes, see end of file.)

AOK is a pre-processor to AWK code that adds:

- Abstract data types;
- Easy methods to list the  attributes and operations of that data;
- Object constructors;
- Nested object constructors.
- Multi-line quotes
- A _dot_ notation for easy reference to nested structures. 

As an example of the last point,

```
emp.name.lname
```

expands to

```
emp["name"]["lname"]
```

By the way, the magic text substitution  for implementing the above is:

```
gensub( /\.([a-zA-Z_])([a-zA-Z0-9_]*)/, 
        "[\"\\1\\2\"]",
	"g",
	$0
     )
```

The following magic function creates a nested list attached at position
`key` in array `lst`. 

```cpp
function have(lst,key,fun)   { 
  lst[key][1];    
  delete lst[key][1]
  if (fun)
    @fun(lst[key])
}
```

If the constructor function `fun` is supplied, then that constructor is
called to install nested structure to `lst[key]`.  If those constructors
also call `have` then arbitrary nested structures can be initialized.

For a simple example, the following  `Symbol` constructor initializes
something that tracks string frequency. Also, it keeps a handle
on the most frquent string seen so far (the `mode`).
The interface to `Symbol`s is the `Symbol1` function.

```awk
function Symbol(i) {
  have(i,"count")
  i.mode = ""
  i.most = 0
}
function Symbol1(i,str,     n) {
  n = ++i.counts[str]
  if (n > i.most) {
    i.mode = str
    i.most = n
}}
```

For an example of nested construction, consider the `NumberFarcade` class.
This object uses three nested objects that  can watch over a very long stream of data:

- A `Sample` object holds a random sample of the data see too date;
- A `Number` object incrementally maintains the standard deviation of the numbers seen so far;
- A `Remedian` object incrementally maintains the median of the numbers see so far.

The constructor function for `NumberFarcade` looks like this:

```awk
function NumberFarcade(i) {
  have(i,"remedian","Remedian")
  have(i,"sample",  "Sample")
  have(i,"num",     "Number")
}
function NumberFarcade1(i,v) {
  if (Sample1(i.sample, v)) {
    Remedian1(i.remedian,v)
    Number1(i.num, v)
}}
```

The interface to this object is the `NumberFarcade1` function which
throws values `v` to the `Sample` object. It it reports that this value was kept,
then 	we update the `Remedian` and `Number` object. 

```awk
function Sample(i,     most) {
  i.most= most ? most : 64 # keep up to 64 items
  have(i,"all")            # i.all holds the kept values
  i.n=0
}
function Sample1(i,v,    
                 added,len) {
  i.n++
  len=length(i.all)
  if (len < i.most) { # if the cache is not full, add in "v"
    push(i.all,v)
    added=1
  } else if (rand() < len/i.n) { # else, sometimes, add "v"
    i.all[ int(len*rand()) + 1 ] = v
    added=1
  }
  return added    # report if anything was added
}
```

The `Number` object uses an algorithm from Knuth to incrementally
maintans a value for

- standard deviation `sd` value ;
- the `hi`ghest and `lo`west value seen so far;
- As well as the mean value `mu`.

The inteface to `Number` objects is the `Number1` function:

```awk
function Number(i) {
  i.hi  = -1e32
  i.lo  =  1e32
  i.bins= 5
  i.n   = i.mu = i.m2 = i.sd = 0
}
function Number1(i,v,          delta) {
  v    += 0
  i.n  += 1
  i.lo  = v < i.lo ? v : i.lo 
  i.hi  = v > i.hi ? v : i.hi 
  delta = v - i.mu
  i.mu += delta/i.n
  i.m2 += delta*(v-i.mu)
  if (i.n > 1)
          i.sd = (i.m2/(i.n-1))^0.5
}
```

The `Remedian` object accumulates some numbers, works out their median,
then passes that to a recusive `Remedian` object.  This means that,
_n_ levels in, that object holds the median of the median of the median (_n_
levels in). Consquently, a linear list of size _n*k_ can hold the median of
an exponential set of _k<sup>n</sup>_ numbers.

```awk
function Remedian(i,   k) {
  have(i,"all")
  have(i,"more")
  i.k = k ? k : 128
  i._median = ""
}
function Remedian1(i, v)  {
  push(i.all,v)
  if (length(i.all) == i.k) {
    if (!length(i.more)) 
      Remedian(i.more,i.k)
    i._median = median(i.all)
    Remedian1(i.more, i._median)
    empty(i.all)
}}
function Remedians(i) {
  if (length(i.more))  
     return Remedians(i.more)
  if (i._median == "") 
    i._median = median(i.all)
  return i._median
}
function empty(a) { split("",a,"") }
```


## Install, configure, test

Get the code

- `git clone http://github.com/ttv1/aok`
- or download `https://github.com/ttv1/aok/archive/master.zip`

To configure, edit the file `aok.rc`. Here's my current file:

    MyName="'Tim Menzies'"
    MyEmail=""'tim@menzies.us'"
    Tmp=_var/tmp # where to write temporaroes
    Docs=docs    # where to write the generated markdown files
    Awk=_var/awk # where to keep the generated awk files

Ensure the file `aok` is executable

    chmod +a aok

To see if this runs, try

```
./aok libok
```

If that works, you should see some test output like:

    _ltgt	1	1
    _ltgt	0	0
    _ltgt	1	1
    _ltgt	0	0
    _med	5.5	5.5
    _med	4.5	4.5
    _med	2	2
    _med	4	4

To write your own code:

- Add a `x.aok` file
- Optionally, if you like `vim` add this line to the top of your file:<br>
  `# /* vim: set filetype=awk ts=2 sw=2 sts=2 et : */`
- Add in `@include lib.aok` to the top of your file.
- Add a description string to the top of your file. 

Your file should look like this:

```
# /* vim: set filetype=awk ts=2 sw=2 sts=2 et : */

"""

## My First File

How does this look.

"""

@include "lib"

BEGIN { yourcodeStartshere() }
```

Then see if it runs

```
./aok x.aok
```


