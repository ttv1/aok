
# Table (nums and sys and stuff)


```awk 

@include "lib"
@include "things"

function Columns(i) {
  has(i,"all")
  has(i,"more")
  has(i,"less")
  has(i,"sym")
  has(i,"num")
  has(i,"indep")
  has(i,"klass")
}
function Table(i) {
  has(i,"rows")
  has(i,"cols","Columns")
}
function Distance(i,p) {
  i.d= 0
  i.n= 1e-32
  i.p= p ? p : 2
}
function Distance1(i,inc) {
  i.n++
  i.d += inc^i.p
}
function Distanced(i) {
  return i.d**(1/i.p) / i.n**(1/i.p)
}
function TableDist(i,a,b,  col,d,n) {
  Distance(d)
  for(col in i.cols.indep) 
    _TableDist(i.cols.syms, 
               i.cols.all[col], 
               a,b,d)
  return Distanced(d)
}
function _TableDist(syms,col,a,b,d,v1,v2) {
  v1 = a[col.pos]
  v2 = b[col.pos]
  if (v1 == "?" && v2 == "?") 
    return
  if (col.pos in syms) 
    return Distance1(d, v1 != v2 )
  if (v1=="?") {
    v2 = NumberNorm(col.my.num, v2)
    v1 = v2 > 0.5 ? 0 : 1
  } else if (v2=="?") {
    v1 = NumberNorm(col.my.num, v1)
    v2 = v1 > 0.5 ? 0 : 1
  } else {
    v1 = NumberNorm(col.my.num, v1)
    v2 = NumberNorm(col.my.num, v2)
  }
  return Distance1(d, v1-v2)
}
```

