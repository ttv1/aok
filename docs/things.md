
# Things 

Here's a set of `Thing`s to read a csv file
whose first row is  list of names (one for each
column header).

In the following, `x=y` means that `x` is initialized to `y`.
When `y` is an term in the left hand column, then `x` is
initialized to the structure of the term.

Object       |  
-------------|-----------|----------|---------|-------|-------|---------|----
Table        |  rows=[]  | cols=Columns
Columns      |  all=[]   | more=[]  | less=[] |sym=[] |num=[] |indep=[] |klass=[]
Row          |  id       | raw=[]   | cooked=[]
Column       |  my       | adder    | name pos
my           |  NumberFarcade=[] or Symbol=[]
NumberFarcade|  remedian=Remedian   | sample=Sample |num=Number
Remedian     |  all=[]   | more=[]  |k=128  | _median
Sample       |  most=64  | all=[]   |n=0
Symbol       |  count=[] | mode     |most=0
Distance     |  d=0      | n=1e-32  |p=2
Number       |  hi=-1e32 | lo=1e32  |bins=5 |n=0 |mu=0 |m2=0 |sd=0


```awk 
@include "lib"

function Row(i) {
  i.id = Id = Id+1
  have(i,"raw")
  have(i,"cooked")
}
function Row1(i,t,lst,     j,raw,cooked) {
  for (j in lst) 
    _Row1(i,t.cols.all[j],
            lst[j],
            j,t)
}
function _Row1(i,col,val,j,t) {
  i.cooked[j] = i.raw[j] = val
  Column1(col,val,j,t)
  if (j in t.cols.num) 
    i.cooked[j] = NumberBin(col.my.num, val)
}
function Column(i,     txt,pos) {
  have(i,"my")
  i.adder= ""
  i.name = txt
  i.pos  = pos
}
function Column1(i,v,pos,t,  tmp) {
  if (v=="?") return
  if ( ! length(i.my) ) {
    if (isnum(v)) {
      i.adder="NumberFarcade1"
      NumberFarcade(i.my)
      t.cols.num[pos]
    } else {
      i.adder="Symbol1"
      Symbol(i.my)
      t.cols.sym[pos]
  }}
  tmp=i.adder
  @tmp(i.my,v)
}
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
function Sample(i,     most) {
  i.most= most ? most : 64
  have(i,"all")
  i.n=0
}
function Sample1(i,v,    
                 added,len) {
  i.n++
  len=length(i.all)
  if (len < i.most) {
    push(i.all,v)
    added=1
  } else if (rand() < len/i.n) {  
    i.all[ int(len*rand()) + 1 ] = v
    added=1
  }
  return added
}
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
function NumberNorm(i,v) {
  if (v < i.lo) return 0
  if (v > i.hi) return 1
  return  (v - i.lo)/(i.hi - i.lo + 1e-31)
}
function NumberBin(i,v,   tmp) {
  tmp = NumberNorm(i,v)
  tmp = int(tmp*i.bins + 0.5) 
  return tmp == i.bins ? --tmp : tmp
}
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
```

