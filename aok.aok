# /* vim: set filetype=awk ts=2 sw=2 sts=2 et : */

function subray(a,b)   { a[b][1];    delete a[b][1]; }

function push(x,y,z,   n) {
  n = length(x[y])
  x[y][n+1] = z
}
function test(   x,k,j,l) {
  split("",x,"")
  subray(x,1)
  test1(x[1])
  for(k in x)
    for(j in x[k])
      for(l in x[k][j])
        print k,j,l
  print(length(x[1][2][3]))
}
function test1(x) {
  subray(x, 2)
  test2(x[2])
}
function test2(x) {
  subray(x,3)
  x[3][0]=22
  }

BEGIN { test() }

