
# Libok

Checking lib


```awk 

@include "lib"

function _median(    x,y,i,w,z,b) {
  for(i=1;i<=10;i++) x[i]=i; z=median(x); print "!",10,z,"\n"
  for(i=1;i<=8;i++)  b[i]=i; z=median(b); print "!",8,z,"\n"
  for(i=1;i<=3;i++)  y[i]=i; z=median(y); print "!",3, z,"\n"
  for(i=1;i<=7;i++)  w[i]=i; z=median(w); print "!",7, z,"\n"
}

BEGIN {
  print (! (10 % 2))
  _median() 
}
```
