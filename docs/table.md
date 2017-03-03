
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
```

