
# Readcsv



```awk 
BEGIN { FS=OFS="," }

function has(a,b,c)   { 
  a[b][1];    
  delete a[b][1]
  if (c)
    @c(a[b])
}
function push(i,v) { i[length(i) + 1] = v }

function isnum(x) { 
  return x=="" ? 0 : x == (0+strtonum(x)) 
}
function o(l,prefix,order,   indent,   old,i) {
  if(! isarray(l)) {
    print "not array",prefix,l
    return 0}
  if(!order)
    for(i in l) { 
      if (isnum(i))
        order = "@ind_num_asc" 
      else 
        order = "@ind_str_asc"
      break
    }     
   old = PROCINFO["sorted_in"] 
   PROCINFO["sorted_in"]= order
   for(i in l) 
     if (isarray(l[i])) {
       print indent prefix "[" i "]"
       o(l[i],"",order, indent "|   ")
     } else
       print indent prefix "["i"] = (" l[i] ")"
   PROCINFO["sorted_in"]  = old 
}
function line(f,   str) {
  if ((getline str < f) > 0) {
    gsub(/[ \t\r]*/,"",str) 
    gsub(/#.*$/,"",str)
    if ( str ~ /,[ \t]*$/ )
      return str line(f)
    else
      return str 
  } 
  return -1
}
##########################################
function readcsv(f,t,   str,a,n) {
  Table(t)
  str = line(f)
  while(str != -1) {
    split(str,a,FS) 
    ++n == 1 ? _readcsvHead(t,a) : _readcsvRow(t,a) 
    str = line(f)
  }
}
 row 
 raw
 cooked
function _readcsvHead(t,lst,i,     
                      klassed,n,txt) {
  klassed=0
  for(n in lst) {
    txt = lst[n]
	 	has(t.cols.all,n)
    Thing(t.cols.all[n],txt,n)
    if (txt ~ /^</)  {
       klassed=1
		   push(t.cols.less,n) 
    } else if (txt ~ /^>/)  {
       klassed=1
		   push(t.cols.more,n) 
    } else if (txt ~ /^!/)  {
       klassed=1
		   push(t.cols.klass,n) 
	}}
  if (!klassed)
	   push(t.cols.klass,length(lst))
}
function _readcsvRow(t,lst,   n,j) {
   j=length(t.rows)+1
   for(n in lst) {
	   t.rows[j][n] = lst[n]
     has(t.cols.all,n)
     thing1(t.cols.all[n],lst[n],n,t)
  }
}
function Sample(i,     most) {
  i.most= most ? most : 8
  has(i,"all")
  i.n=0
}
function sample1(i,v,    
                 len,doomed) {
  i.n++
  len=length(i.all)
  if (len < i.most)
    push(i.all,v)
  else if (rand() < len/i.n)  
    i.all[ int(len*rand()) + 1 ] = v
}
function Sym(i) {
  has(i,"count")
  i.mode = ""
  i.most = 0
}
function sym1(i,v,     n) {
  n = ++i.counts[v]
  if (n > i.most) {
    i.mode = v
    i.most = n
}}
function Num(i) {
  i.hi = -1e32
  i.lo =  1e32
  i.n  = i.mu = i.m2 = i.sd = 0
  has(i,"cache","Sample")
}
function num1(i,v,          delta) {
  v    += 0
  i.n  += 1
  i.lo  = v < i.lo ? v : i.lo 
  i.hi  = v > i.hi ? v : i.hi 
  delta = v - i.mu
  i.mu += delta/i.n
  i.m2 += delta*(v-i.mu)
  if (i.n > 1)
	  i.sd = (i.m2/(i.n-1))^0.5
  sample1(i.cache,v)
}
function Thing(i,     txt,pos) {
  has(i,"my")
  i.adder= ""
  i.name = txt
  i.pos  = pos
}
function thing1(i,v,pos,t,  tmp) {
  if (v=="?") return
  if ( ! length(i.my) ) {
    if (isnum(v)) {
      i.adder="num1"
      Num(i.my)
      push(t.cols.num,pos)
    } else {
      i.adder="sym1"
      Sym(i.my)
      push(t.cols.sym,pos)
  }}
  tmp=i.adder
  @tmp(i.my,v)
}
function Cols(i) {
  has(i,"all")
  has(i,"more")
  has(i,"less")
  has(i,"sym")
  has(i,"num")
  has(i,"klass")
}
function Table(i) {
  has(i,"rows") 
  has(i,"cols","Cols")
}

```
