# Scala as a calculator
All the commands in this tutorial will assume that you started the Scala REPL via Ammonite as explained in the [Installation](0-installation.md) section.

```sh
$ amm
Loading...
Welcome to the Ammonite Repl 1.0.0
(Scala 2.12.2 Java 1.8.0_131)
If you like Ammonite, please support our development at www.patreon.com/lihaoyi
@
```

> Note: The default Ammonite prompt is just `@` whereas the one shown in the examples is `scala>`. This should not have any effect in the code execution.

## Numbers and arithmetic {#arithmetic}

```scala
scala> 2 + 2
res0: Int = 4

scala> 50 - 5*6
res1: Int = 20

scala> (50 - 5*6) / 4
res2: Int = 5

scala> 8 / 5
res3: Int = 1
```
As you can see, each result has the type indicated to the left.

> Note: `8/5` performs integer division. In order to get a floating point operation you need to convert one of the arguments:

```scala
scala> 8.toDouble / 5
res4: Double = 1.6
```

There's no predefined operator to calculate powers, but you can use `Math.pow`

```scala
scala> Math.pow(5,2)
res5: Double = 25.0
```

To save a value for later use you can use the keyword `val` and the equal sign `(=)`:

```scala
scala> val width = 20
width: Int = 20

scala> val height = 5 * 9
height: Int = 45

scala> width * height
res6: Int = 900
```


> Note: names created with `val` cannot be reassigned. For such cases you need to use `var`:

```scala
scala> var x = 1
x: Int = 1

scala> x = x + 1
x: Int = 2
```

## Numeric types {#numeric-types}
Python has 4 main numeric types. All have a corresponding type in Scala except `complex`.

| Python | Scala |
| ---    |---    |
| `bool` | `Boolean`
| `int` | `BigInt`
| `float` | `Double`
| `complex` | NA


From the Scala point of view these are the numeric types:


| Type | T.MinValue | T.MaxValue | Example
| --- | --- | --- | ---
|`Int` | `-2147483648` | `2147483647` | `100`
|`Long` | `-9223372036854775808` | `9223372036854775807` | `2147483648L`
|`Float` | `-3.4028235E38` | `3.4028235E38` | `1.23F`
|`Double` | `-1.7976931348623157E308` | `1.7976931348623157E308` | `1.23`
|`BigInt` | Unbounded (limited by memory) | Unbounded | `BigInt(123)`
| `BigDecimal` | Unbounded (limited by memory) | Unbounded | `BigDecimal(1.23)`

For all bounded types you can obtain the min/max values using the `MinValue` / `MaxValue` functions.

```scala
scala> Int.MaxValue
res7: Int = 2147483647
```

## Strings {#strings}
There are 3 common ways to create literal strings:

### Double quotes

```scala
scala> "spam eggs"
res8: String = spam eggs

scala> "doesn't"
res9: String = doesn't

scala> "\"abc\""
res10: String = "abc"

scala> "La niña comía rábanos"
res11: String = La niña comía rábanos

scala> "The 🐱 ate the 🍎" 
res12: String = The 🐱 ate the 🍎
```

### Triple quotes

```scala
scala> """"Isn't," she said."""
res13: String = "Isn't," she said.

scala> """a
     | multiline
     | word
     | """
res14: String =
"a
multiline
word
"

scala> """c:\some\name"""
res15: String = c:\some\name

scala> """{"x":1, "y":"2"}"""
res16: String = {"x":1, "y":"2"}
```

Translation: `print(s)` becomes `println(s)`

### String interpolation
```scala
scala> val x = 1 + 2 + 3
x: Int = 6

scala> println(s"1 + 2 + 3 = $x")
1 + 2 + 3 = 6
```

The `s"..."` syntax allows to embed code directly within a string.

### Characters
Characters have a special type in scala `Char` and are constructed using single quotes

```scala
scala> 'a'
res18: Char = a
```

### String operations
A String is conceptually a sequence of characters. 

<table>
<tbody>
<tr>
<td></td>
<td>Python</td>
<td>Scala</td>
<td>Notes</td>
</tr>

<tr>
<td>Concatenate</td>
<td><pre>"a" + "b"</pre></td>
<td><pre>"a" + "b"</pre></td>
</tr>

<tr>

<td>Indexing</td>

<td>
<pre>word = "Scala"
word[0] == "S"
word[4] == "a"</pre>
</td>

<td>
<pre>val word = "Scala"
word(0) == 'S'
word(4) == 'a'</pre>
</td>

<td>
<ul>
<li> Insted of square brackets `seq[i]` Scala uses parenthesis `seq(i)` to access the `i-th` element of a sequence.
<li> The result is a `Char`, not a `String`
<li> Indices cannot be negative.
<ul>
</td>
</tr>

<tr>
<td>Slices</td>
<td><pre>"Python"[2:6] == "thon"</pre></td>
<td><pre>"Python".slice(2,6) == "thon"</pre></td>
<td>There is no syntax for slices (you have to use the `.slice` method)<td>
</tr>

<tr>
<td>Length</td>
<td><pre>len("Python") == 6</pre></td>
<td><pre>"Python".length == 6</pre></td>
<td>There's no global `len` function as in Python but rather each collection has its own `length` method.<td>
</tr>


</tbody>
</table>


## Collections {#collections}
Scala has a rich library of data structures. A few highlights:

* There's no special syntax for building lists. (e.g. `[1,2,3]`)
* Scala collections are *homogeneous*. They can only hold values of a single type.
* Most of the collections have a *immutable* version and a *mutable* version. In general only the immutable variant is imported automatically (`Array` is an exception).
* The immutable versions are probably more performant than what you'd expect.
* When in doubt, is better to use the immutable variant and only switch to the mutable variant when you need it for reasons like convenience, performance, etc.


Let's start with one of the most popular immutable collections.


### `List`

Lists are in fact **linked lists**, which means that they are very fast at inserting elements at the beginning and iterating sequentially, but accessing element *n* requires scanning the whole list up to the *n-th* element.

Pro tip: `List` is *not* like Python's `list`. Use `Vector` (immutable) or `Array` (mutable) if you need fast access to an arbitrary index.

```scala
scala> val lst = List(1, 2, 3, 4, 5, 6)
lst: List[Int] = List(1, 2, 3, 4, 5, 6)

scala> lst.length
res19: Int = 6
```

List is particularly good if you need to get hold of the first element (the *head*) and everything but the head (the *tail*)

```scala
scala> val h = lst.head
h: Int = 1

scala> val t = lst.tail
t: List[Int] = List(2, 3, 4, 5, 6)
```
In most data structures getting a copy of the tail is a relatively expensive operation that requires copying n - 1 elements. In a linked list this just involves following a pointer so it is super fast.

Another fast operation on Lists is prepending an element via the `(::)` operator

```scala
scala> val lst2 = 0 :: lst
lst2: List[Int] = List(0, 1, 2, 3, 4, 5, 6)

scala> lst2.head
res20: Int = 0
```

### mutable.ArrayBuffer

Probably the most similar structure to Python's `list` is `collection.mutable.ArrayBuffer`.

The "Buffer" part refers to the fact that the size can be changed, as opposed to the `Array` which has a fixed size (see below).


```scala
scala> import collection.mutable.ArrayBuffer // required import
import collection.mutable.ArrayBuffer

scala> val b = ArrayBuffer(1, 2, 3, 4, 5)
b: scala.collection.mutable.ArrayBuffer[Int] = ArrayBuffer(1, 2, 3, 4, 5)
```

All of the operations that we've seen on Strings and Vectors work for `ArrayBuffer`:

```scala
scala> b(0)
res21: Int = 1

scala> b(1)
res22: Int = 2

scala> b.slice(1,3)
res23: scala.collection.mutable.ArrayBuffer[Int] = ArrayBuffer(2, 3)

scala> b.take(2)
res24: scala.collection.mutable.ArrayBuffer[Int] = ArrayBuffer(1, 2)

scala> b.drop(2)
res25: scala.collection.mutable.ArrayBuffer[Int] = ArrayBuffer(3, 4, 5)
```

Since they are mutable they support a few extra operations to modify / append / preppend / extend the Arraybuffer

```scala
scala> b(0) = 2 // modify in place

scala> b
res27: scala.collection.mutable.ArrayBuffer[Int] = ArrayBuffer(2, 2, 3, 4, 5)

scala> b(0) += 1 // increment operator

scala> b
res29: scala.collection.mutable.ArrayBuffer[Int] = ArrayBuffer(3, 2, 3, 4, 5)

scala> b.append(6)

scala> b
res31: scala.collection.mutable.ArrayBuffer[Int] = ArrayBuffer(3, 2, 3, 4, 5, 6)

scala> b.prepend(0)

scala> b
res33: scala.collection.mutable.ArrayBuffer[Int] = ArrayBuffer(0, 3, 2, 3, 4, 5, 6)

scala> b.appendAll(ArrayBuffer(7,8,9))

scala> b
res35: scala.collection.mutable.ArrayBuffer[Int] = ArrayBuffer(0, 3, 2, 3, 4, 5, 6, 7, 8, 9)
```

Since a collection is completely agnostic towards its contents (i.e. "generic") we can have nested buffers

```scala
scala> val b1 = ArrayBuffer(1, 2, 3)
b1: scala.collection.mutable.ArrayBuffer[Int] = ArrayBuffer(1, 2, 3)

scala> val b2 = ArrayBuffer(4, 5, 6)
b2: scala.collection.mutable.ArrayBuffer[Int] = ArrayBuffer(4, 5, 6)

scala> val c = ArrayBuffer(b1, b2)
c: scala.collection.mutable.ArrayBuffer[scala.collection.mutable.ArrayBuffer[Int]] = ArrayBuffer(ArrayBuffer(1, 2, 3), ArrayBuffer(4, 5, 6))
```

let's update the element (0,0)

```scala
scala> c(0)(0) += 1

scala> c
res37: scala.collection.mutable.ArrayBuffer[scala.collection.mutable.ArrayBuffer[Int]] = ArrayBuffer(ArrayBuffer(2, 2, 3), ArrayBuffer(4, 5, 6))
```

As we saw, `ArrayBuffer` allow not only updating its contents in-place but also modifing the length of the array. This imposes a small overhead; if you need maximum performance it's better to use an `Array` which allows in place modification but cannot change its size.

You can build an ArrayBuffer and the transform it into an Array
```scala
scala> b.toArray
res38: Array[Int] = Array(0, 3, 2, 3, 4, 5, 6, 7, 8, 9)
```

or if you know the size beforehand it's better to create the Array directly

```scala
scala> val arr = Array(1,2,3)
arr: Array[Int] = Array(1, 2, 3)

scala> arr(0) += 1

scala> arr
res40: Array[Int] = Array(2, 2, 3)

scala> Array.ofDim[Int](5)
res41: Array[Int] = Array(0, 0, 0, 0, 0)
```

## First Steps Towards Programming {#first-steps}

Let's generate the classic fibonacci series

```python
a, b = 0, 1
while b < 10:
    print(b)
    a, b = b, a+b
```

becomes

```scala
var a = 0
// a: Int = 0

var b = 1
// b: Int = 1

var tmp = 0
// tmp: Int = 0

while (b < 10) {
  println(b)
  tmp = a + b
  a = b
  b = tmp
}
// 1
// 1
// 2
// 3
// 5
// 8
```

We can observe a few differences: 

* The Python version doesn't need the temporal variable because multiple assignment: `a, b = b, a + b` works element by element.

 In contrast, Scala has a special version that works like this

 ```tut
scala>  val x, y, z = Math.random 
x: Double = 0.3073121756281484
y: Double = 0.09127578061809982
z: Double = 0.8283702233050622
 ```

 The expression on the right is evaluated as many times as variables on the left.

* Scala does not use indentation as part of the syntax. The body of the `while` loop consists of the block immediately next to it; curly brackets `{}` are required if there is more than one line (as in this case).



