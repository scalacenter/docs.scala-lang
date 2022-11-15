---
title: How to use clues to get better test report?
type: section
description: ???
num: 7
previous-page: munit-single-test
next-page: munit-asynchronous-tests
---

{% include markdown.html path="_markdown/install-munit.md" %}


In MUnit tests outputs are presented to the user in different ways depending on used assertion.
For example `assertEquals` failure returns not only stack trace, 
but also by default includes reason of the failure: `values are not the same` along with differences between two values.

{% tabs munit-assert-equals %}
{% tab 'Scala CLI' %}
```scala
//> using toolkit

class MyTests extends munit.FunSuite:
  test("string comparison") {
    assertEquals("JohnSmith", "John Smith")
  }
```
{% endtab %}
{% tab 'sbt and Mill' %}
```scala
class MyTests extends munit.FunSuite:
  test("string comparison") {
    assertEquals("JohnSmith", "John Smith")
  }
```
{% endtab %}
{% endtabs %}

Which will result in:
```
# ==> X MyTests.string comparison munit.ComparisonFailException: 
#   ./example/MyTests.test.scala:7
# 4:  test("string comparison") {
# 5:    assertEquals("JohnSmith", "John Smith")
# 6:  }
# values are not the same
# => Obtained
# JohnSmith
# => Diff (- obtained, + expected)
# -JohnSmith
# +John Smith
```

Sometimes we may want to change it to better suit our needs.
In MUnit there are two ways to achieve it. First one allows us to directly provide
custom message to assertion functions as an additional parameter, for example:


{% tabs munit-assert-clue-string %}
{% tab 'Scala CLI' %}
```scala
//> using toolkit

class MyTests extends munit.FunSuite:
  test("price after discount can't be lower than 0") {
    val price = 10.0
    val discount = 15.0
    assert(
      price - discount >= 0,
      s"price cannot have negative value: ${price - discount}"
    )
  }
```
{% endtab %}
{% tab 'sbt and Mill' %}
```scala
class MyTests extends munit.FunSuite:
  test("price after discount can't be lower than 0") {
    val price = 10.0
    val discount = 15.0
    assert(
      price - discount >= 0,
      s"price cannot have negative value: ${price - discount}"
    )
  }
```
{% endtab %}
{% endtabs %}

Now the test output contains a message when test fails:
```
# MyTests:
# ==> X MyTests.price after discount can't be lower than 0  munit.FailException: 
#   ./example/MyTests.test.scala:7 price cannot have negative value: -5.0
# 9:       s"price cannot have negative value: ${price - discount}"
# 10:    )
# 11:  }
```

The other way to change output messages is to use `clue` method for those arguments which values we want to be printed
in case of failure:


{% tabs munit-assert-clue %}
{% tab 'Scala CLI' %}
```scala
//> using toolkit

class MyTests extends munit.FunSuite:
  test("price after discount can't be lower than 0") {
    val price = 10.0
    val discount = 15.0
    assert(clue(price) - clue(discount) >= 0)
  }
```
{% endtab %}
{% tab 'sbt and Mill' %}
```scala
class MyTests extends munit.FunSuite:
  test("price after discount can't be lower than 0") {
    val price = 10.0
    val discount = 15.0
    assert(clue(price) - clue(discount) >= 0)
  }
```
{% endtab %}
{% endtabs %}

Resulting with:

```
# ==> X MyTests.price after discount can't be lower than 0  munit.FailException: 
#   ./example/MyTests.test.scala:7 assertion failed
# 6:    val discount = 15.0
# 7:    assert(clue(price) - clue(discount) >= 0)
# 8:  }
# Clues {
#   price: Double = 10.0
#   discount: Double = 15.0
# }
```

It is worth stating that both custom messages and clues can be combined with each other.

