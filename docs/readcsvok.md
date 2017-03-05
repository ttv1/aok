
# Readcsv


```c 

@include "readcsv"

function _weather1(    t) {
  srand(SEED ? SEED : 1)
  readcsv("data/weather__csv",t)
  o(t,"tw")
}

BEGIN { _weather1() }

```

