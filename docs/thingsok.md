
# Thingsok

Checking things.


```c 

@include "things"

function _remedian1(max,   a,i) {
  Remedian(a); 
  for(i=1;i<=max;i++)  
    Remedian1(a,rand()**2)
  print Remedians(a)
}

BEGIN {
      _remedian1(1e1) 
      _remedian1(1e3) 
      _remedian1(1e5) 
      _remedian1(1e7) 
}
```

