
# Readcsv


```c 

@include "lib"
@include "table"

function readcsv(f,t,   str,a,n) {
  Table(t)
  str = _readcsvLine(f)
  while(str != -1) {
    split(str,a,FS)
    if(n++) # true after line1
      _readcsvRow(t,a)
    else    # line 1
      _readcsvHead(t,a)
    str = _readcsvLine(f)
  }
  close(f)
}
function _readcsvLine(f,   str) {
  if ((getline str < f) > 0) {
    gsub(/[ \t\r]*/,"",str)
    gsub(/#.*$/,"",str)
    if ( str ~ /,$/ )
      return str _readcsvLine(f)
    else
      return str
  }
  return -1
}
function _readcsvHead(t,lst,i,
                      klassed,n,txt) {
  for(n in lst) {
    txt = lst[n]
	 	have(t.cols.all,n)
    Column(t.cols.all[n],txt,n)
    if (txt ~ /^</)  {
       klassed++
       t.cols.less[n]
    } else if (txt ~ /^>/)  {
       klassed++
       t.cols.more[n]
    } else if (txt ~ /^!/)  {
       klassed++
		   t.cols.klass[n]
	  } else
       t.cols.indep[n]
  }
}
function _readcsvRow(t,lst,   j) {
   j=length(t.rows)+1
   have(t.rows,j,"Row")
   Row1(t.rows[j], t, lst)
}
```

