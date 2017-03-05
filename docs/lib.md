
# Lib 

Standard background stuff.


```c 

BEGIN { FS=OFS="," }

function lt(a,b) { return a < b }
function gt(a,b) { return a > b }

function have(lst,key,fun)   { 
  lst[key][1];    
  delete lst[key][1]
  if (fun)
    @fun(lst[key])
}
function empty(a) { split("",a,"") }
function push(i,v) { i[length(i) + 1] = v }

function isnum(x) { 
  return x=="" ? 0 : x == (0+strtonum(x)) 
}

function median(lst,
                n,p,q,lst1) {
  n = asort(lst,lst1)
  p = q = int(n/2+0.5)
  if (n <= 3) { 
    p = 1; q = n
  } else 
    if (!(n % 2) )
      q = p + 1;
  return p==q ? lst1[p] : (lst1[p]+lst1[q])/2
}
```


## o

Print nested array.


```c 

function o(l,prefix,order,
           indent,
           old,i) {
  if(! isarray(l)) {
    print "not array",prefix,l
    return 0}
  if(!order)
    for(i in l) { 
      if (isnum(i))
        order = "@ind_num_asc" 
      else 
        order = "@ind_str_asc"
      break
    }     
   old = PROCINFO["sorted_in"] 
   PROCINFO["sorted_in"]= order
   for(i in l) 
     if (isarray(l[i])) {
       print indent prefix "[" i "]"
       o(l[i],"",order, indent "|   ")
     } else
       print indent prefix "["i"] = (" l[i] ")"
   PROCINFO["sorted_in"]  = old 
}

```

