
# Table (nums and sys and stuff)


```c 

@include "lib"
@include "things"

function Columns(i) {
  have(i,"all")
  have(i,"more")
  have(i,"less")
  have(i,"sym")
  have(i,"num")
  have(i,"indep")
  have(i,"klass")
}
function Table(i) {
  have(i,"rows")
  have(i,"cols","Columns")
}
function closest(i,a,   zero,bt,
                        b,out,tmp,best) {
  best= zero=="" ? 1e32 : zero 
  bt  = bt ? bt : "lt"
  out = a
  for (b in i.rows)
    if (b != a) {
      tmp = TableDist(i, i.rows[a].raw, i.rows[b].raw )
      if ( @bt(tmp,best) ) {
        best = tmp 
        out  = b
  }}
  return out
}
function furthest(i,a) {
  return closest(i,a, -1, "gt")
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
function TableRow(i,a,   j,str,sep) {
  for(j in i.rows[a].raw) {
    str = str sep i.rows[a].raw[j]
    sep = ","
  }
  return str
}
function TableDist(i,a,b,  col,d,n) {
  Distance(d)
  for(col in i.cols.indep) 
    _TableDist(i.cols.sym, 
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

