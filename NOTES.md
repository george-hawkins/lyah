Learn you a Haskell
===================

Install Stack:

```
$ curl -sSL https://get.haskellstack.org/ | sh
```

Note: it only actually downloads and installs GHC itself when you first use `stack`.

Stack REPL:

```
$ stack ghci
```

VS Code seems to be the Haskell IDE, to get set up:

<https://github.com/haskell/haskell-language-server#visual-studio-code>

Not operator is `not`:

```
not True
```

Not equals operator is `/=`:

```
5 /= 4
```

Function application has the highest precedence of all the operations:

```
succ 9 + max 5 4 + 1
```

`succ` and `max` are applied before the `+` operations.

So be careful, the parentheses here aren't optional:

```
succ (9 * 10)
```

If a function takes two parameters you can make it infix:

```
div 92 10
92 `div` 10
```

Load:

```
> :l baby.hs
```

Haskell's `if` is an expression and must have an `else`:

```
if x > 100 then x else x * 2
```

The apostrophe doesn’t have any special meaning in Haskell. It usually denotes a _strict_ version of a function (i.e., one that isn’t lazy), or a slightly modified version of a function or variable with a similar name.

A function with an argument looks like this:

```
doubleMe x = x + x
```

A function with no arguments looks like this:

```
foo = "foo"
```

Is this a function? It's usually called a _definition_ or a _name_. Should you think of this as assignment? Maybe. See:

<http://www.haskellforall.com/2017/07/demistifying-haskell-assignment.html>

You can write:

```
alpha = "b"

bar = do
  let alpha = "a"
  putStrLn alpha
```

The `let` in the function `bar` isn't optional.

Why do we need the `let` in one situation and not in the other? Because at the top-level you're already in an implicit block where you can introduce names:

<https://stackoverflow.com/questions/28422817/the-usage-of-let-in-haskell>

In older version of `ghci` (like that used in LYAH), `let` seems to have been necessary when working interactively, even at the top-level, but not if loading the same thing from a file. There doesn't seem to be any such distinction anymore.

We'll get to `let` and `<-` later.

To enable multiline mode in `ghci`:

```
$ echo ':set +m' >> ~/.ghci
$ chmod go-w ~/.ghci
```

Oddly, `ghci` doesn't like `.ghci` being group writable (even if you're the only person in that group).

Think of `++` as concatenation, not addition:

```
[1,2,3,4] ++ [9,10,11,12]
"hello" ++ " " ++ "world"
```

Interesting:

```
> ['h','e','l','l','o'] == "hello"
True
> ['h','e','l','l','o']
"hello"
```

Use `:type` to ask about types:

```
> :type "hello"
"hello" :: [Char]
> :type 'h'
'h' :: Char
```

The _cons_ operator is `:`:

```
'a':"bcde"
1:[2, 3, 4]
```

Cf concatenation:

```
[1] ++ [2, 3, 4]
```

_Cons_ can only take a single item as its LHS.

The _list index_ operator looks a little strange, it's `!!`:

```
> [1, 2, 3, 4] !! 2
3
```

If you have lists of lists, the subelements must all be of the same type. So this is legal:

```
[[1], [2, 3, 4], [5, 6]]
```

But this is not:

```
[[1], ['a']]
```

The comparison operators, i.e. `<`, `>`, `<=` and `>=` can be used on lists if the elements of the lists are themselves comparable with these operators:

```
> [] < [0]
True
> [0] < [0, 1]
True
> [0, 1] < [1, 1]
True
```

If an empty list is always less than a non-empty list then it should be obvious why `[0]` is less than `[0, 1]`.

More list operators:

```
> head [5,4,3,2,1]
5
> tail [5,4,3,2,1]
[4,3,2,1]
> last [5,4,3,2,1]
1
> init [5,4,3,2,1]
[5,4,3,2]
```

`last` and `init` are kind of the mirror of `head` and `tail`.

Yet more:

```
> length [5,4,3,2,1]
5
> reverse [5,4,3,2,1]
[1,2,3,4,5]
> maximum [1,9,2,3,4]
9
> minimum [8,4,2,1,5,6]
1
> sum [5,2,1,6,3,2,5,7]
31
> product [6,2,1,2]
24
```

`take` and `drop`:

```
> take 3 [5,4,3,2,1]
[5,4,3]
> take 0 [6,6,6]
[]
> take 5 [1,2]
[1,2]
```

See what happens when we `take` 0 elements or when we `take` more than are in the list?

`take` takes _at most_ the specified number of elements. Similarly `drop` drops _at most_ the specified number of elements.

Cf with `head` etc. which generate an exception if given an empty list.

Checking if a list is empty:

```
> null []
True
```

Oh dear - `null` is such a confusing word. Actually, one rarely uses the `null` function - generally this kind of thing is handled with pattern matching.

The _is-contained-in_ operator is `elem`:

```
> 4 `elem` [1, 2, 3, 4]
True
```

Used, as I am, to _contains_, the argument ordering here seems rather odd.

If it can be _enumerated_, you can create a range:

```
> [1..4]
[1,2,3,4]
```

Specifying a _step_ look a little odd (to me):

```
> [4,6..20]
[4,6,8,10,12,14,16,18,20]
```

See what's going on here? `4` is _not_ the step. Instead, you're giving the first two elements, i.e. `4` and `6`, and from these two it has deduced the step to use for the range.

Note that e.g. `[2,4,6..20]` doesn't parse.

For negative ranges, it turns out you need parentheses that you might not expect:

```
> [(-4)..(-1)]
[-4,-3,-2,-1]
> [-4..(-1)]
[-4,-3,-2,-1]
> [-4..-1]
error: Variable not in scope: (..-)
```

Update: you can do without the parentheses if you introduce spaces:

```
> [-4 .. -1]
[-4,-3,-2,-1]
Prelude> [-4.. -1]
[-4,-3,-2,-1]
```

You must use a _step_ for a range that counts down:

```
> [10..1]
[]
> [10,9..1]
[10,9,8,7,6,5,4,3,2,1]
```

You can implement the same behavior in Kotlin like so:

```Kotlin
fun range(first: Int, second: Int, last:Int): List<Int> {
    val step = second - first
    val dir = last - first

    // If the signs are different then `step` can never get us to `last`.
    return if (step * dir < 0) {
        listOf()
    } else {
        listOf(first) + range(second, second + step, last)
    }
}

fun range(first: Int, last: Int) = range(first, first + 1, last)
```

You can create infinite ranges by not specifying the upper limit:

```
> take 4 [2,4..]
[2,4,6,8]
```

Functions that produce infinite - `cycle` and `repeat`:

```
> take 4 (cycle [1,2])
[1,2,1,2]
> take 4 (repeat 2)
[2,2,2,2]
```

`repeat` takes an single element, while `cycle` takes a list.

Aside: `take 4 (repeat 2)` is better achieved with `replicate 4 2`.

Note that floating-point numbers are as funky in Haskell as elsewhere:

```
> [0.1,0.2..0.4]
[0.1,0.2,0.30000000000000004,0.4]
```

Rewritting `take 10 [2,4..]` as a _list comprehension_:

```
[x * 2 | x <- [1..10]]
```

Obviously, a list comprehension_allows for much more flexibility beyond the _step_ of a range.

Aside: LYAH seems to imply `[x <- [1..10]]` would be legal but it's not, you'd need to write this as `[x | x <- [1..10]]` (or more sensibly as just `[1..10]`).

Let's describe the parts of list comprehension in words:

* We _draw_ our elements from the list `[1..10]`.
* `x <- [1..10]` means that `x` takes on the value of each element that is drawn from `[1..10]`. In other words, we _bind_ each element from `[1..10]` to `x`.
* The part before the `|` is the _output_ of the list comprehension.

We can add a condition (also called a _predicate_):

```
[ x | x <- [50..100], x `mod` 7 == 3]
```

Weeding out parts of lists using predicates is also called _filtering_.

You can include as many conditions as you want:

```
> [ x | x <- [10..20], x /= 13, x /= 15, x /= 19]
[10,11,12,14,16,17,18,20]
```

You can also specify as many lists as you want:

```
> [x+y | x <- [1,2,3], y <- [10,100,1000]]
[11,101,1001,12,102,1002,13,103,1003]
```

Important: see what's happening here, it's not doing something like zipping the two lists. Instead you're getting the cartesian product of the two. In Kotlin, you'd write this as:

```
x.flatMap { xEl -> y.map { yEl -> xEl + yEl }
```

The length of your resulting list will be the product of the lengths of the lists used.

The output doesn't even have to refer to anything to the right of the the `|`. E.g. we can define our own `length` so:

```
length' xs = sum [1 | _ <- xs]
```

Note: I couldn't find a good definition of what `_` is, you can bind to it, as seen above, but you can't use it:

```
> _ = 42
> _
error: Found hole: _ :: t
```

Remember strings are just lists:

```
> removeNonUppercase st = [ c | c <- st, c `elem` ['A'..'Z']]
> removeNonUppercase "Hahaha! Ahahaha!"
"HA"
```

There's no issue with list comprehension within list comprehensions:

```
> xxs = [[1,3,5], [1,2,3], [1,2,4]]
> [ [ x | x <- xs, even x ] | xs <- xxs]
[[],[2],[2,4]]
```

_Tuples_ store several heterogeneous elements as a single value. Tuples have a fixed size

Lists etc. are immutable so is this not true of all such things? I guess the type of a list doesn't give you any information about the number of elements it contains whereas the type of a tuple does.

So while the following is legal:

```
[[1,2], [1,2,3]]
```

Haskell infers that the type is `[[Int]]` and that imposes no restriction on the length of the elements.

But the following is illegal:

```
[(1,2), (1,2,3)]
```

Haskell would initially infer a type of `[(Int, Int)]` and then choke on seeing a tuple with three elements.

Interestingly, you can't write a general function to append an element to a tuple, you'd have to write a function that takes a pair and returns a triple, another to take a triple and return a 4-tuple and so on.

Like lists, you can compare tuples if the elements are comparable:

```
> ('a', 1) < ('a', 1.2)
True
```

Note that the types here aren't identical but the relevant elements are comparable. Unlike lists, length matters:

```
> ('a', 1) < ('a')
error: Couldn't match expected type ‘(Char, Integer)’ with actual type ‘Char’
```

Look at the type of the RHS, this surprised me. Turns out there's no easy way to specify a 0 or 1-tuple in Haskell. In the above situation the parentheses are just (pointlessly) grouping, it's no more a tuple than `(1 + 2)`. In Python you can write `(1,)` but there's nothing similar in Haskell. You can't take or return arbitrary lengthed tuples in a Haskell function so there's no situation where a 1-tuple might be usefully used rather than the value contained.

Cf with lists where `[1]` and `[]` are completely normal.

As noted, there aren't general tuple functions. But pairs are so common that there are various functions defined for them:

```
> fst (1, 2)
1
> snd (1, 2)
2
```

`fst (1, 2, 3)` will fail - it can only handle pairs.

And there are functions that produce pairs:

```
> zip [1..] ["apple", "orange", "cherry", "mango"]
[(1,"apple"),(2,"orange"),(3,"cherry"),(4,"mango")]
```

Watch out when (like above) the lists aren't the same size.

An exercise - consider a right-angled triangle, i.e. one where `a^2 + b^2 = c^2`. For such a triangle `c`, the hypotenuse, is always the longest side. We're interested in triangles whose sides are all integer values between 1 and 10 and whose perimeter is 24. And we want to exclude triangles where just the legs are reversed, i.e. exclude `a=2, b=1`, if `a=1, b=2` is a valid triange.

We can generate all candidate triples like so:

```
[ (a,b,c) | c <- [1..10], a <- [1..c], b <- [1..a] ]
```

`a <- [1..c]` excludes values where `a` is bigger than the hypotenuse. And `b <- [1..a]` excludes flipped legs (try things out with `c <- [1..2]` and varying between `b <- [1..a]` and `b <- [1..c]` and you'll see it does work out even if it's not immediatelly obvious).

Now you can filter for valid right-angle triangles:

```
[ (a,b,c) | c <- [1..10], a <- [1..c], b <- [1..a], a^2 + b^2 == c^2 ]
```

And now for ones whose perimeter equals 24:

```
> [ (a,b,c) | c <- [1..10], a <- [1..c], b <- [1..a], a^2 + b^2 == c^2, a + b + c == 24 ]
[(8,6,10)] 
```

Read the `::` operator as "has type of":

```
> :t 'a'
'a' :: Char
> :t "alpha"
"alpha" :: [Char]
```

Except when writing short trivial functions, it's good practice to give functions an explicit type declaration:

```
> :{
removeNonUppercase :: [Char] -> [Char]
removeNonUppercase st = [ c | c <- st, c `elem` ['A'..'Z']]
:}
```

When using the REPL you have to use `:{` and `:}` even if multi-line mode in enabled.

Haskell can work out the declaration without your help. If you look at the definition you can see that Haskell's type inference is fairly sophisticated, it would have to work out the type of `st` via the expression involving `c`.

For a mult-argument function the types of the arguments are separated with `->` and they in turn are separated from the return type with an `->`. You can guess this will seem more obvious when we get to currying.

```
addThree :: Int -> Int -> Int -> Int
addThree x y z = x + y + z
```

`:t` can take an expression:

```
> :t False == True
True == False :: Bool
```

However, a function definition isn't an expression, so you have to define it first and then ask for its type:

```
> foo x y z = x + y + z
> :t foo
foo :: Num a => a -> a -> a -> a
```

Note that above, I get something quite different back compared if I declare the function type explicitly:

```
> :{
bar :: Int -> Int -> Int -> Int
bar x y z = x + y + z
:}
> :t bar
bar :: Int -> Int -> Int -> Int
```

In one case I got `Num a => a -> a -> a -> a` and in the other I got `Int -> Int -> Int -> Int`. The `=>` and everything before it is a type constraint and we'll get to them later.

Haskell has two integer types:

* `Int` - the classic bounded integer, i.e. -2^63 to (2^63 - 1) on 64-bit systems.
* `Integer` - the unbounded integer.

```
> :{
factorial :: Int -> Int
factorial n = product [1..n]
:}
> factorial 25
7034535277573963776
> factorial 26
-1569523520172457984
> :{
factorial' :: Integer -> Integer
factorial' n = product [1..n]
:}
> factorial' 50
30414093201713378043612608166064768844377641568960512000000000000
```

`factorial` blows out at 26 while `factorial'` has no problem with larger values.

Haskell also has `Float` and `Double` but these are entirely as you'd expect - neither is anything like `BigDecimal`.

Other basic types include `Bool`, `Char` and tuples, e.g. `(Char, Int)` or `()`.

Aside: I wonder if there's a reason why he mentions tuples here but not lists?

