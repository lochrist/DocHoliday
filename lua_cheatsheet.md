#Lua Cheatsheet

## Basic syntax
### Comments
```lua
-- single line comment

--[[ this is a
multi line
comment that spans on
4 lines --]]

```

### String literal
```lua
s = 'walternate'  -- Immutable strings like Python.
t = "double-quotes are also fine"
u = [[ Double brackets
       start and end
       multi-line strings.]]

-- string concatenation
s = 'test' .. "me"
```

### Assigments
```lua
a, b, c = 1, 2, 3
a, b = b, a

-- "6" is evaluated but ignored
a, b = 4, 5, "6"

-- b is set to nil
a, b = "there"

-- destroys a; its contents are eligible for garbage collection if unreferenced.
a = nil

-- if z is not defined it is nil, so nil is assigned to a (destroying it)
a = z

```


### Loops
```lua
while num < 50 do
  num = num + 1  -- No ++ or += type operators.
end

karlSum = 0
for i = 1, 100 do  -- The range includes both ends.
  karlSum = karlSum + i
end

-- Use "100, 1, -1" as the range to count down:
fredSum = 0
for j = 100, 1, -1 do
	fredSum = fredSum + j
end

for var = start, limit, step do
  -- code here
end

-- Another loop construct:
repeat
  print('the way of the future')
  num = num - 1
until num == 0
```

### Generic For
```lua
-- Will loop until exp-list returns nil
for <var-list> in <exp-list> do
  <body>
end

-- Ex:
function list_iter (t)
  local i = 0
  local n = table.getn(t)
  return function ()
   i = i + 1
   if i <= n then return t[i] end
 end
end

t = {10, 20, 30}
for element in list_iter(t) do
  print(element)
end

```


### Conditional
```lua
if num > 40 then
  print('over 40')
elseif s ~= 'walternate' then  -- ~= is not equals.
  -- Equality check is == like Python; ok for strs.
  io.write('not over 40\n')  -- Defaults to stdout.
else
  -- Variables are global by default.
  thisIsGlobal = 5  -- Camel case is common.

  -- How to make a variable local:
  local line = io.read()  -- Reads next stdin line.

  -- String concatenation uses the .. operator:
  print('Winter is coming, ' .. line)
end

-- Only nil and false are falsy; 0 and '' are true!
if not aBoolValue then print('twas false') end

```

### Operators precedence (from higher to lower)
```lua
^
not  - (unary)
*   /
+   -
..
<   >   <=  >=  ~=  ==
and
or
```

### Logical operators
```lua
-- The logical operators are and, or, and not. Like control structures,
-- all logical operators consider false and nil as false and anything else as true

-- The operator and returns its first argument if it is false; otherwise,
-- it returns its second argument
print(4 and 5)         --> 5
print(nil and 13)      --> nil
print(false and 13)    --> false

-- The operator or returns its first argument if it is not false;
-- otherwise, it returns its second argument:
print(4 or 5)          --> 4
print(false or 5)      --> 5

-- Both and and or use short-cut evaluation, that is,
-- they evaluate their second operand only when necessary.

-- Idioms with shortcuts evaluation:

x = x or v -- equivalent to => if not x then x = v end

(a and b) or c -- equivalent to =>  a ? b : c (assuming b is not false)
a and b or c -- equivalent to =>a ? b : c (assuming b is not false)

-- The operator not always returns true or false
print(not nil)      --> true
print(not false)    --> true
print(not 0)        --> false
print(not not nil)  --> false
```

### Functions
```lua

-- defines function and assigns to global variable name
function name ( args )
	[[body]]
    [return values]
end

-- defines function as local to chunk
local function name ( args )
	[[body]]
    [return values]
end

-- anonymous function assigned to variable f
f = function ( args )
	[[body]]
    [return values]
end

-- Named arguments
function foo(t)
  return t[1] * t.x + t[2] * t.y
end

foo{3, 4, x = 5, y = 6} -- 39
-- That function call is equivalent to:
foo({ 3, 4, x = 5, y = 6 })

-- variable argument list, acces them by putting them ina table using {...}
function sum(...)
  local ret = 0
  for i, v in ipairs{...} do ret = ret + v end
  return ret
end

sum(3, 4, 5, 6) -- 18

-- Another variable argument list example using select
-- select returns all values after the specified index, or the length of the list if "#" is provided instead
function sum(...)
  local ret = 0
  for i = 1, select("#", ...) do ret = ret + select(i, ...) end
  return ret
end

-- Default argument paradigm uising or operator (each unused value is passed nil)
function func(x, y, z)
  y = y or 0
  z = z or 1
end

-- Non global functions
Lib = {}
Lib.foo = function (x,y) return x + y end
Lib.goo = function (x,y) return x - y end

-- Or:
Lib = {
  foo = function (x,y) return x + y end,
  goo = function (x,y) return x - y end
}

-- Or with more syntactic sugar:
Lib = {}
function Lib.foo (x,y)
  return x + y
end
function Lib.goo (x,y)
  return x - y
end

-- object function, gets obj as additional first argument self
function obj:name ( args )
	[[body]]
    [return values]
end

-- object call: shortcut for x.move(x, 2, -3)
x:move (2, -3)
```


##Tables

### Table Constructors
```lua
t = {} -- creates an empty table and assigns it to t
t = {"yes", "no", "?"} -- simple array; elements are t[1], t[2], t[3].
t = { [1] = "yes", [2] = "no", [3] = "?" } -- same as above, but with explicit fields
t = {[-900] = 3, [900] = 4} -- sparse array with just two elements (no space wasted)
t = {x=5, y=10} -- hash table, fields are t["x"], t["y"] (or t.x, t.y)
t = {x=5, y=10; "yes", "no"} -- mixed, fields/elements are t.x, t.y, t[1], t[2]
t = {msg = "choice", {"yes", "no", "?"}} -- tables can contain others tables as fields

-- When the keys of a table are strings you can use them as property:
t = { foo = 1, bar = 2 }
print(t.foo) -- 1
t.bar = 3

-- Get the size of a table (# operator)
a = { 11, 22, "foo", "bar" }
print(#a) -- 4
```

### Table Iteration
```lua
-- Iterate over a table in arbitrary order
for k, v in pairs(t) do
  print(k, v)
end

-- pairs implementation
function pairs (t)
  return next, t, nil
end

-- next is a primitive: where k is a key of the table t
-- returns a next key in the table, in an arbitrary order
next(t, k)

-- returns the first pair
next(t, nil)

-- next returns nil when there are no more pairs
for k, v in ipairs(t) do
  print(k, v)
end

-- ipairs implementation
function iter (a, i)
  i = i + 1
  local v = a[i]
  if v then
    return i, v
  end
end

function ipairs (a)
  return iter, a, 0
end
```

### Table library
```lua
-- inserts v at numerical index i [default: after the end] in table t
table.insert (t, [i,] v)

 -- removes element at numerical index i [default: last element] from table t;
 -- returns the removed element or nil on empty table.
table.remove (t [, i])

-- size of the table if table act as an array
table.getn (table)

-- returns the largest positive numerical index of table t or zero if t has no positive indices (similar as table.getn)
table.maxn (t)

-- sorts (in place) elements from t[1] to #t, using compare function cf(e1, e2) (default is <)
table.sort (t [, cf])

-- Sort example:
function comp(w1,w2)
    if w1 > w2 then
        return true
    end
end

table.sort(myTable,comp)

-- returns a single string made by concatenating table elements t[i] to t[j] [default: i =1, j = #t]
-- separated by string s; returns empty string if no elements exist or i > j.
table.concat (t [, s [, i [, j]]])

-- concat example:
myTable = {
  19,
  "iron",
  10,
  "clay",
}
result = table.concat(myTable)
print(result) --> 19iron10clay

result = table.concat(myTable, " space ")
print(result) --> 19 space iron space 10 space clay
```

### Table as array
```lua
a = {}    -- new array
for i=1, 1000 do
  a[i] = 0
end

-- creates an array with indices from -5 to 5
a = {}
for i=-5, 5 do
  a[i] = 0
end

squares = {1, 4, 9, 16, 25, 36, 49, 64, 81}
```
## Libraries

### Global functions
```lua
-- prints each of the passed args to stdout using tostring() (see below)
print (args)

-- terminates the program or the last protected call (e.g. pcall()) with error message msg
-- quoting level n [default: 1, current function]
error (msg [, n])
assert (v [, msg]) -- calls error(msg) if v is nil or false [default msg: "assertion failed!"]

-- returns the type of x as a string (e.g. "nil", "string");
-- type can be: "nil" "boolean" "number" "string" "table" "function" "thread" "userdata"
type (x)

-- converts x to a string, using t's metatable's __tostring if available
tostring (x)

-- converts string x representing a number in base b [2..36, default: 10] to a number, or nil if
-- invalid; for base 10 accepts full format (e.g. "1.5e6").
tonumber (x [, b])

-- returns t[1]..t[n] (n = #t) as separate values
unpack (t)
```

### Math library
```lua
-- Basic functions
math.abs (x) -- returns the absolute value of x
math.mod (x, y) -- returns the remainder of x / y as a rounded-down integer, for y ~= 0
math.floor (x) -- returns x rounded down to the nearest integer
math.ceil (x) -- returns x rounded up to the nearest integer
math.min (args) -- returns the minimum value from the args received
math.max (args) -- returns the maximum value from the args received

-- Exponential and logarithmic
math.sqrt (x) -- returns the square root of x, for x >= 0
math.pow (x, y) -- returns x raised to the power of y, i.e. x^y; if x < 0, y must be integer.
__pow (x, y) -- global function added by the math library to make operator '^' work
math.exp (x) -- returns e (base of natural logs) raised to the power of x, i.e. e^x
math.log (x) -- returns the natural logarithm of x, for x >= 0
math.log10 (x) -- returns the base-10 logarithm of x, for x >= 0

-- Trigonometrical
math.deg (a) -- converts angle a from radians to degrees
math.rad (a) -- converts angle a from degrees to radians
math.pi -- constant containing the value of pi
math.sin (a) -- returns the sine of angle a (measured in radians)
math.cos (a) -- returns the cosine of angle a (measured in radians)
math.tan (a) -- returns the tangent of angle a (measured in radians)
math.asin (x) -- returns the arc sine of x in radians, for x in [-1, 1]
math.acos (x) -- returns the arc cosine of x in radians, for x in [-1, 1]
math.atan (x) -- returns the arc tangent of x in radians
math.atan2 (y, x) -- similar to math.atan(y / x) but with quadrant and allowing x = 0

-- Splitting on powers of 2
math.frexp (x) -- splits x into normalized fraction and exponent of 2 and returns both
math.ldexp (x, y) --returns x * (2 ^ y) with x = normalized fraction, y = exponent of 2

-- returns a pseudo-random number in range [0, 1] if no arguments given; in range [1, n] if n is
-- given, in range [n, m] if both n and
m are passed.
math.random ([n [, m])

-- sets a seed n for random sequence (same seed = same sequence)
math.randomseed (n)
```

## String library

### Basic operations
```lua
string.len (s) -- returns the length of string s, including embedded zeros (see also # operator)
string.sub (s, i [, j]) -- returns the substring of s from position i to j [default: -1] inclusive
string.rep (s, n) -- returns a string made of n concatenated copies of string s
string.upper (s) -- returns a copy of s converted to uppercase according to locale
string.lower (s) -- returns a copy of s converted to lowercase according to locale
```

### String search and regex
```lua
-- returns first and last position of pattern p in string s, or nil if not found, starting search at
-- position i [default: 1]; returns captures as extra results. If d is true, treat pattern as plain string.
string.find (s, p [, i [, d]])

-- returns an iterator getting next occurrence of pattern p (or its captures) in string s as
substring(s) matching the pattern.
string.gmatch (s, p)

-- returns a copy of s with up to n [default: all] occurrences of pattern p (or its captures) replaced
-- by r if r is a string (r can include references to captures in the form %n). If r is a function r() is
-- called for each match and receives captured substrings; it should return the replacement string.
-- If r is a table, the captures are used as fields into the table. The function returns the number of
-- substitutions made as second result.
string.gsub (s, p, r [, n])

-- returns captures of pattern p in string s (or the whole match if p specifies no captures) or nil if
-- p does not match s; starts search at position i [default: 1].
string.match (s, p [, i])
```

### Regex Patterns:
```
General pattern format: pattern_item [ pattern_items ]

cc   matches a single character in the class cc (see Pattern character classes below)
cc*  matches zero or more characters in the class cc; matchest longest sequence (greedy).
cc-  matches zero or more characters in the class cc; matchest shortest sequence (non-greedy).
cc+  matches one or more characters in the class cc; matchest longest sequence (greedy).
cc?  matches zero or one character in the class cc
%n   matches the n-th captured string (n = 1..9, see Pattern captures)
%bxy matches the balanced string from character x to character y (e.g. %b() for nested parentheses)
^    anchors pattern to start of string, must be the first item in the pattern
$    anchors pattern to end of string, must be the last item in the pattern

Captures:

(pattern)   stores substring matching pattern as capture %1..%9, in order of opening parentheses
()          stores current string position as capture
```

### Pattern character class:
```
 any character
%a any letter %A any non-letter
%c any control character
%C any non-control character
%d any digit %D any non-digit
%l any lowercase letter
%L any non-(lowercase letter)
%p any punctuation character
%P any non-punctuation character
%s any whitespace character
%S any non-whitespace character
%u any uppercase letter
%U any non-(uppercase letter)
%w any alphanumeric character
%W any non-alphanumeric character
%x any hexadecimal digit
%X any non-(hexadecimal digit)
%z the byte value zero %Z any non-zero character
%x if x is a symbol the symbol itself x if x not in ^$()%.[]*+-? the character itself
[ set ] any character in any of the given classes; can also be a range [c1-c2], e.g. [a-z].
[ ^set ] any character not in set
```

### Regex example
```lua
string.find("Lua is great!", "is") -- (5, 6)
string.find("Lua is great!", "%s") -- (4, 4)

string.gsub("Lua is great!", "%s", "-") -- ("Lua-is-great!", 2)
string.gsub("Lua is great!", "[%s%l]", "*") -- ("L***********!", 11)
string.gsub("Lua is great!", "%a+", "*") -- ("* * *!", 3)
string.gsub("Lua is great!", "(.)", "%1%1") -- ("LLuuaa iiss ggrreeaatt!!", 13)
string.gsub("Lua is great!", "%but", "") -- ("L!", 1)
string.gsub("Lua is great!", "^.-a", "LUA") -- ("LUA is great!", 1)
string.gsub("Lua is great!", "^.-a", function(s) return string.upper(s) end) -- ("LUA is great!", 1)
```

### String format
```lua
 -- returns a copy of s where formatting directives beginning with '
-- %' are replaced by the value of arguments args, in the given order (see Formatting directives below)
string.format (s [, args])
```

```
Formatting directives for string.format:
% [flags] [field_width] [.precision] type

Formatting field types:
%d decimal integer
%o octal integer
%x hexadecimal integer, uppercase if %X
%f floating-point in the form [-]nnnn.nnnn
%e floating-point in exp. Form [-]n.nnnn e [+|-]nnn, uppercase if %E
%g floating-point as %e if exp. < -4 or >= precision, else as %f; uppercase if %G.
%c character having the (system-dependent) code passed as integer
%s string with no embedded zeros
%q string between double quotes, with all special characters escaped
%% '%' character

Formatting flags:
- left-justifies within field_width [default: right-justify]
+ prepends sign (only applies to numbers)
(space) prepends sign if negative, else blank space
# adds "0x" before %x, force decimal point for %e, %f, leaves trailing zeros for %g
Formatting field width and precision
n puts at least n (<100) characters, pad with blanks
0n puts at least n (<100) characters, left-pad with zeros
.n puts at least n (<100) digits for integers; rounds to n decimals for floating-point; puts no more than n
(<100) characters for strings.
```

```lua
-- Formatting examples
string.format("results: %d, %d", 13, 27) results: 13, 27
string.format("<%5d>", 13) < 13>
string.format("<%-5d>", 13) <13 >
string.format("<%05d>", 13) <00013>
string.format("<%06.3d>", 13) < 013>
string.format("<%f>", math.pi) <3.141593>
string.format("<%e>", math.pi) <3.141593e+00>
string.format("<%.4f>", math.pi) <3.1416>
string.format("<%9.4f>", math.pi) < 3.1416>
string.format("<%c>", 64) <@>
string.format("<%.4s>", "goodbye") <good>
string.format("%q", [[she said "hi"]]) "she said \"hi\""
```

## Metatable

### Metatable library
```lua
setmetatable (t, mt) -- sets mt as metatable for t, unless t's metatable has a __metatable field, and returns t
getmetatable (t) -- returns __metatable field of t's metatable or t's metatable or nil
rawget (t, i) -- gets t[i] of a table without invoking metamethods
rawset (t, i, v) -- sets t[i] = v on a table without invoking metamethods
rawequal (t1, t2) -- returns boolean (t1 == t2) without invoking metamethods
```

### Metatable keys
```lua
-- sets handler h(a, b) for '+' and for binary '-' __mul, __div sets handler h(a, b) for '*' and for '/'
__add, __sub
local x = {value = 5} -- creating local table x containing one key,value of value,5

local mt = {
  __add = function (lhs, rhs) -- "add" event handler
    return { value = lhs.value + rhs.value }
  end
}
setmetatable(x, mt) -- use "mt" as the metatable for "x"

-- set handler h(a, b) for '%' __pow sets handler h(a, b) for '^'
__mod

-- sets handler h(a) for unary '-' __len sets handler h(a) for the # operator (userdata)
__unm

-- sets handler h(a, b) for '..'
__concat

-- sets handler h(a, b) for '==', '~='
__eq

-- sets handler h(a, b) for '<', '>' and possibly '<=', '>=' (if no __le)
__lt

-- sets handler h(a, b) for '<=', '>='
__le

-- example on comparators:
local mt
mt = {
  __add = function (lhs, rhs)
    return setmetatable({value = lhs.value + rhs.value}, mt)
  end,
  __eq = function (lhs, rhs)
    return lhs.value == rhs.value
  end,
  __lt = function (lhs, rhs)
    return lhs.value < rhs.value
  end,
  __le = function (lhs, rhs) -- not really necessary, just improves "<=" and ">" performance
    return lhs.value <= rhs.value
  end,
}

-- sets handler h(t, k) for access to non-existing field
__index

-- {} an empty table, and after the comma, a custom function failsafe
local func_example = setmetatable({}, {__index = function (t, k)
  return "key doesn't exist"
end})
print(func_example[1]) --> key doesn't exist

-- sets handler h(t, k, v) for assignment to nonexisting field
__newindex
t = setmetatable({}, {
  __newindex = function(t, key, value)
    if type(value) == "number" then
      rawset(t, key, value * value)
    else
      rawset(t, key, value)
    end
  end
})

t.foo = "foo"
t.bar = 4
t.la = 10
t.foo -- "foo"
t.bar -- 16
t.la -- 100

-- sets handler h(f, ...) for function call (using the object as a function)
__call
t = setmetatable({}, {
  __call = function(t, a, b, c, whatever)
    return (a + b + c) * whatever
  end
})

t(1, 2, 3, 4) -- 24

-- sets handler h(a) to convert to string, e.g. for print()
__tostring

t = setmetatable({ 1, 2, 3 }, {
  __tostring = function(t)
    sum = 0
    for _, v in pairs(t) do sum = sum + v end
    return "Sum: " .. sum
  end
})

print(t) -- prints out "Sum: 6"

-- sets finalizer h(ud) for userdata (has to be set from C)
__gc

--  table mode: 'k' = weak keys; 'v' = weak values; 'kv' = both.
__mode

--  sets value to be returned by getmetatable()
__metatable

```

### Metable Vector class example:
```lua
Vector = {}
Vector.__index = Vector

function Vector.new(x, y)
  return setmetatable({ x = x or 0, y = y or 0 }, Vector)
end

function Vector:len()
  return math.sqrt(self.x * self.x + self.y * self.y)
end

function Vector.__add(a, b)
  if type(a) == "number" then
    return Vector.new(b.x + a, b.y + a)
  elseif type(b) == "number" then
    return Vector.new(a.x + b, a.y + b)
  else
    return Vector.new(a.x + b.x, a.y + b.y)
  end
end

function Vector.__eq(a, b)
  return a.x == b.x and a.y == b.y
end

function Vector.__lt(a, b)
  return a.x < b.x or (a.x == b.x and a.y < b.y)
end

function Vector.__le(a, b)
  return a.x <= b.x and a.y <= b.y
end

function Vector.__tostring(a)
  return "(" .. a.x .. ", " .. a.y .. ")"
end

setmetatable(Vector, { __call = function(_, ...) return Vector.new(...) end })

a = Vector.new(10, 10)
b = Vector(20, 11)
c = a + b
print(a:len()) -- 14.142135623731
print(a) -- (10, 10)
print(c) -- (30, 21)
print(a < c) -- true
print(a == b) -- false
```

### Printing the callstack:
```
debug.traceback()

Example :
function a()
    print(debug.traceback())
end 

function b()
    a() 
end 

Calling the function b would print :

stack traceback:
    ./test.lua:45: in function 'a'
    ./test.lua:50: in function 'b'
    ./test.lua:53: in main chunk
    [C]: in ?
```

This cheatsheet has been created from multiple sources:
- [The Original Lua Cheatsheet](http://thomaslauer.com/download/luarefv51.pdf)
- [Lua for beginners](http://lua.gts-stolberg.de/en/table.php)
- [Lua in 15 minutes](http://tylerneylon.com/a/learn-lua/)
- [Lua tutorials](http://nova-fusion.com/2012/08/27/lua-for-programmers-part-1/)
- [Lua Reference Manual](http://www.lua.org/manual/5.1/)
- [Programming Lua First Edition](http://www.lua.org/pil/contents.html)
