
# Test suite: How I Write AWK Code


```c 
@include "codingtips"

function _top1(a) { ok(0, 10 == 10) }
function _top2(a) { ok(1, 10 == 10) }

BEGIN {
  oks("_top1,_top2")
}
```

