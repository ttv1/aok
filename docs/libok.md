
# Libok

Checking lib


```c 

@include "lib"

function _ltgt() {
  print "_ltgt", 1, gt(10,8)
  print "_ltgt", 0, gt(6,8)
  print "_ltgt", 1, lt(5,10)
  print "_ltgt", 0, lt(8,6)
}
function _med(    x,y,i,w,z,b) {
  for(i=1;i<=10;i++) x[i]=i; print "_med", 5.5, median(x)
  for(i=1;i<=8;i++)  b[i]=i; print "_med", 4.5, median(b)
  for(i=1;i<=3;i++)  y[i]=i; print "_med",   2, median(y)
  for(i=1;i<=7;i++)  w[i]=i; print "_med",   4, median(w)
}
BEGIN {
  OFS="\t"
  _ltgt() 
  _med() 
}
```

