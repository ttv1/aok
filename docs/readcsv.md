
# Readcsv



```awk 
BEGIN { srand(Seed ? Seed :1  ) }

function has(a,b,c)   { 
  a[b][1];    
  delete a[b][1]
  if (c)
    &c(a[b])
}
function push(i,v) { i[length(i) + 1] = v }

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
function readcsv(f,   str,a,t,n) {
  Table(t)
  f   = f ? f : "-"
  str = line(f)
  while(str != -1) {
    split(str,a,FS) 
    n++ ? tableRow1(t,a) : tableHeader1(t,a) 
    str = line(f)
}}
function Sample(i,     most) {
  i.most= most ? most : 256
  has(i,"all")
  i.n=0
}
function sample1(i,v,    
                 len,doomed) {
  i.n++
  len=length(i.all)
  if (len < i.most)
    push(v,i.all)
  else if (rand() < len/n)  
    i.all[ int(len*rand()) + 1 ] = v
}
function Sym(i) {
  i.is   = "Sym"
  has(i,"count")
  i.mode = ""
  i.most = 0
}
function sym1(i,v,     n) {
  n = i.counts[v]++
  if (n > i.most) {
    i.mode = v
    i.most = n
}}
function Num(i) {
  i.is = "Num"
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
}
function Thing(i,     txt,pos) {
  has(i,"my")
  i.adder= ""
  i.name = txt
  i.pos  = pos
}
function thing1(i,v) {
  if (v=="?") return
  if ( length(i,my) == 0 ) {
    if (v+0 == v) {
      i.adder="num1"
      Num(i.my)
    } else {
      i.adder="sym1"
      Sym(i.my)
  }}
  &i.adder(i.my,v)
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

