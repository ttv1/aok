
# Readcsv


```awk 

@include "readcsv"

function _weather1(    t) {
  readcsv("data/weather__csv",t)
  o(t,"tw")
}

BEGIN { _weather1() }

```

