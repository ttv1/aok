# /* vim: set filetype=awk ts=2 sw=2 sts=2 et : */

function array2(a,b)   { a[b][1];    delete a[b][1]; }
function array3(a,b,c) { a[b][c][1]; delete a[b][c][1]; }
function array4(a,b,c,d) { a[b][c][d][1]; delete a[b][c][d][1]; }

function push(x,y,z,   n) {
  n = length(x[y])
  x[y][n+1] = z
}
function test(   x) {
  split("",x,"")
  array4(x,100,22,10)
  print(length(x[100][22][10))
  push(x[100][22],10,"u")
  print(x[100][22][10][1])
}

BEGIN { test() }

