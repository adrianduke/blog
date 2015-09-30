---
Description: "The basics of getting your head around TDD, what it is and how to do it"
Tags: [tdd]
date: 2015-09-30T10:00:00+01:00
title: "Test Driven Development - A Guided Tour"
---

<p class="lead">This is an article I wrote a while back, but hadn't published. It will take you through what TDD is, why you'd want to do it and how to do so.</p>

## What

Test-Driven Development is a discipline for writing software. The developer works in a short repetitive cycle known commonly as: Red, Green, Refactor. Each phase of the cycle requiring different action to be undertaken:

1. [**Red**: Write a failing test](#red)
2. [**Green**: Write just enough code to pass the test](#green)
3. [**Refactor**: Refactor both tests and production code](#refactor)

As TDD is a discipline, it like other disciplines requires practise. Practising TDD on a particular problem is commonly called a 'Code Kata'. Code Katas should be practised regularly when an active project employing TDD is not being undertaken, this will ensure the developer doesn't lose the discipline.

## Why

Writing the minimum amount of bug free code to satisfy requirements is hard, really hard.

Every line of code written by a developer has the potential to introduce new bugs into the system, every method composed has the potential to veer away from the requirements and every design pattern introduced has the potential to add un-needed complexity. Add to this that there are many solutions to any given software problem and that developers come with their own set of flaws and you quickly see the cocktail of complexity emerging. No wonder then why ~65% of software projects undertaken fail (note that the developer isn't the sole reason for this failure rate, but is definitely apart of it).

It is for these reasons I use TDD. TDD provides an assurance that bare back development does not, and its not just as developers that we see the benefits. The benefits that I have found from practising TDD are as follows:

#### Beneficiaries:
- Code
- Developer
- Business
- Process (Software development process)

#### Benefits:
- **Facilitating change**: Being able to make large changes to your existing system is much less daunting and risky when you know you have tests to cover your back. [_Developer, Business_]
- **Better design**: You naturally develop with modularisation in mind, otherwise you wouldn't be able to test the code you are writing. [_Code, Developer_]
- **Living documentation**: Provided you name your tests well, all the documentation required for another developer to understand your code is in the tests. [_Code, Developer_]
- **Ease integrations**: Connecting distant services becomes much less of an ordeal when you practise TDD as both ends have already been tested with mocks. [_Business, Developer_]
- **Flow and productivity**: Spending less time debugging (which naturally reduces with TDD) means you spend more time developing, and moving forwards is much more productive. [_Process, Developer_]
- **Stronger guarantees**: When you practise TDD you have larger guarantee that the code you deploy works. [_Business, Developer, Code_]
- **Regression**: Regression tests are baked into the TDD process, thus you get regression tests for free. [_Business, Developer, Code_]

## How

### <a name="red"></a>RED: Writing Failing Tests

#### Naming Tests

Naming tests is the first step of the Red phase and sometimes seen as the hardest part of TDD, and rightly so, as everyone knows the 2 hardest problems in computing sciences is cache invalidation and naming things (Phil Karlton). The aim is to summerise in a sentance the specific behaviour you require, and call your test that. Here are a few examples:

<a name="fizzbuzz-naming"></a>Lets imagine we are implementing FizzBuzz. The first and simplest test case we know is that when given the number 1, it prints the number 1, so lets call it just that: '**TestItPrints1WhenGiven1**'. Next test case is that when given the number 2 it prints the number 2, so lets keep consistent and call it: '**TestItPrints2WhenGiven2**'

Lets now imagine we are implementing a javascript powered menu that converts a UL into an interactive menu. Our first behaviour here is likely that given an ID it grabs the DOM element so lets call our first test case: '**TestItGetsDomElementById**'. Now we can get DOM elements our second behaviour is that it will display a warning if the DOM element got by ID is not an UL, so lets call that: '**TestItDisplaysWarningIfDomElementIsNotUL**'

As you can see, each test name is an apt summary of exactly the behaviour we require, there is no ambiguity about what the production code should be doing.

#### Writing The Test Case

Writing tests at an abstract level is about 2 things:

1. Scaffolding how you intend your code to work. E.g. The method name you intend to call and what parameters (if any) it will accept, and what it may or may not return.
2. Defining the acceptance criterea that signals if the code is working or not. E.g. In our FizzBuzz example above, when our method receives a '1' it is only considered working if it returns a '1' and not if it returned a '2'.

Once you have your behaviour and name hopefully its fairly evident exactly what you need to do. To help solidify this, follow along the FizzBuzz example below:

##### Red: FizzBuzz Step 1

Following on from the [FizzBuzz test name](#fizzbuzz-naming) examples above, here is the first test for '**TestItPrints1WhenGiven1**':

{{< highlight php "style=friendly" >}}
<?
function TestItPrints1WhenGiven1() {
	if (Translate(1) != 1) {
		print("FAIL! ". Translate(1) . " != 1");
	}
}
{{< /highlight >}}

I have elected to call the method under test `Transalate()` and it takes a single argument, the int that we want to translate (or not). Notice that `Translate()` doesn't exist yet, another benefit of TDD is that you get to decide on your public interfaces before you write them, not after!

If I were to run the above, I should get an error along the lines of no method `Translate()` exists. This is sufficient for the Red phase, we can now move onto the [>>> **Green phase**](#green).

##### <a name="fizzbuzz-red-2"></a>Red: FizzBuzz Step 2

Now that we have 1 passing test and nothing to refactor, we can move onto writing our second test '**TestItPrints2WhenGiven2**':

{{< highlight php "style=friendly" >}}
<?
function TestItPrints2WhenGiven2() {
	if (Translate(2) != 2) {
		print("FAIL! ". Translate(2) . " != 2");
	}
}
{{< /highlight >}}

Running the above produces:
```
FAIL! 1 != 2
```

This looks rather similar to our first test, and that has been noted. We will get to it in the next Refactor phase, but for now onto [>>> **Green phase**](#fizzbuzz-green-2).

##### <a name="fizzbuzz-red-3"></a>Red: FizzBuzz Step 3

We now have 2 passing tests, albeit very simple, our next test however is where things get interesting. We now hit the case where by we pass '3' to our `Translate()` method and require it to return 'Fizz'. I think we should call our test '**TestItPrintsFizzWhenGiven3**', and heres what it looks like:

{{< highlight php "style=friendly" >}}
<?
function TestItPrintsFizzWhenGiven3() {
	assertItTranslatesArgIntoExpected(3, "Fizz");
}
{{< /highlight >}}

Running the above produces:
```
FAIL! 3 != Fizz
```

Luckily as we refactored our code, implementing this test was just a 1 liner. Onto the [>>> **Green phase**](#fizzbuzz-green-3).

##### <a name="fizzbuzz-red-4"></a>Red: FizzBuzz Step 4

We are going to skip testing arg value of '4' for the sakes of page length, and move straight onto '5'. Now we need our code to return 'Buzz' when it receives a '5' so I think '**TestItReturnsBuzzWhenGiven5**' is suitable:

{{< highlight php "style=friendly" >}}
<?
function TestItPrintsBuzzWhenGiven5() {
	assertItTranslatesArgIntoExpected(5, "Buzz");
}
{{< /highlight >}}

Running the above produces:
```
FAIL! 5 != Buzz
```

Onto the [>>> **Green phase**](#fizzbuzz-green-4).

##### <a name="fizzbuzz-red-5"></a>Red: FizzBuzz Step 5

We now move onto an arg value of '6', continuing in the same vein I am going to choose the test name '**TestItPrintsFizzWhenGiven6**'. My refactoring sense are tingling, we appear to have 2 tests that take an argument and return 'Fizz', but we will leave that until the next Refactoring phase:

{{< highlight php "style=friendly" >}}
<?
function TestItPrintsFizzWhenGiven6() {
	assertItTranslatesArgIntoExpected(6, "Fizz");
}
{{< /highlight >}}

Running the above produces:
```
FAIL! 6 != Fizz
```

Onto the [>>> **Green phase**](#fizzbuzz-green-5).

##### <a name="fizzbuzz-red-6"></a>Red: FizzBuzz Step 6

Again for the sakes of page length we are going to skip onto '10' and whilst we are at it add '9' to our '**TestItPrintsFizzWhenGivenANumberDivisableBy3**' test. Very much like the previous Red phase we are going to add another test called '**TestItPrintsBuzzWhenGiven10**'. Again my refactoring sense are tingling for the same reasons as before, we have 2 very similar tests, but we will get to it in the Refactoring phase:

{{< highlight php "style=friendly" >}}
<?
function TestItPrintsBuzzWhenGiven10() {
	assertItTranslatesArgIntoExpected(10, "Buzz");
}
{{< /highlight >}}

Running the above produces:
```
FAIL! 10 != Buzz
```

Onto the [>>> **Green phase**](#fizzbuzz-green-6).

##### <a name="fizzbuzz-red-7"></a>Red: FizzBuzz Step 7

Ok, we are now onto our final functional requirement, which is when we have a number divisible by both 3 and 5, we should print 'FizzBuzz', lets call it '**TestItPrintsFizzBuzzWhenGivenANumberDivisibleByBoth3and5**':

{{< highlight php "style=friendly" >}}
<?
function TestItPrintsFizzBuzzWhenGivenANumberDivisibleByBoth3and5() {
	$testCases = array(
		15,
	);

	assertTestCasesTranslateIntoExpected($testCases, "FizzBuzz");
}
{{< /highlight >}}

Running the above produces:
```
FAIL! Fizz != FizzBuzz
```

Thats an interesting output, its because the code returns first by checking divisibility by 3 and because 15 is divisible by 3 we return 'Fizz'. No matter though, its still a failure, onto [>>> **Green phase**](#fizzbuzz-green-7).

###  <a name="green"></a>GREEN: Writing just enough code

Writing just enough code to pass your test is probably the next hardest part of TDD. Its very easy to get carried away implementing more functionality into your target method without writing more tests, especially in the beginning. Your target is to satisfy the current failing test with the smallest amount of change to your production code. That may sometimes require you just to return a constant only to know in the next test you are going to replace that with a variable, but in the beginning its best to be more pedantic to really ingrain the discipline. Over time you will start to notice patterns and will be able to save your self the pedanticism.

##### Green: FizzBuzz Step 1

Here is our code to pass our first test:

{{< highlight php "style=friendly" >}}
<?
function Translate($i) {
	return 1;
}
{{< /highlight >}}

Notice how we are literally returning just a constant '1'. This is perfectably acceptable as our test runs with no failures, and is the smallest amount of change required to pass.

We now move into our [>>> **Refactoring phase**](#refactor).

##### <a name="fizzbuzz-green-2"></a>Green: FizzBuzz Step 2

Keeping in mind the least amount of change required to implement the next failing test, here is our passing production code:

{{< highlight php "style=friendly" >}}
<?
function Translate($i) {
	return $i;
}
{{< /highlight >}}

We have turned our constant '1' into the arg varibale `$i`. Arguably we could of added an if statement to check the value of `$i` and return the appropriate constant, but I believe this to be a simpler code change.

Tests are passing, so now onto the [>>> **Refactoring phase**](#fizzbuzz-refactor-2).

##### <a name="fizzbuzz-green-3"></a>Green: FizzBuzz Step 3

We now have our first interesting test case, we need to get our function to return 'Fizz' when it receives a '3'. I believe the easiest way to do that would be to add a condition to check the `$i` variable. Here is what I have chosen to implement:

{{< highlight php "style=friendly" >}}
<?
function Translate($i) {
	if ($i == 3){
		return "Fizz";
	}
	return $i;
}
{{< /highlight >}}

Our tests are passing so we can move onto the next [>>> **Refactoring phase**](#fizzbuzz-refactor-3).

##### <a name="fizzbuzz-green-4"></a>Green: FizzBuzz Step 4

Another interesting test case, I think the quickest and simplest solution would be another conditional as follows:

{{< highlight php "style=friendly" >}}
<?
function Translate($i) {
	if ($i == 3){
		return "Fizz";
	}
	if ($i == 5){
		return "Buzz";
	}
	return $i;
}
{{< /highlight >}}

All tests are now passing, onto the next [>>> **Refactoring phase**](#fizzbuzz-refactor-4)

##### <a name="fizzbuzz-green-5"></a>Green: FizzBuzz Step 5

We now come to a fork in the road. We have both '3' and '6' which require the same outcome, and there are different approaches we could take. One approach could be to just add another condition like so:

{{< highlight php "style=friendly" >}}
<?
	if ($i == 3){
		return "Fizz";
	}
	if ($i == 5){
		return "Buzz";
	}
	if ($i == 6){
		return "Fizz";
	}
{{< /highlight >}}

But it doesn't take much forethought to realise that we could be repeating that pattern a lot (9, 10, 12, 15, 18, 20, 21... etc), also this doesn't appear to be a simple change, we have added a fair amount of code, is there a shorter way? Well yes, we could condense the new conditional into a chanined OR expression on the first if like so:

{{< highlight php "style=friendly" >}}
<?
	if ($i == 3 || $i == 6){
		return "Fizz";
	}
	if ($i == 5){
		return "Buzz";
	}
{{< /highlight >}}

But again, we could be repeating this pattern A LOT. So we are really only left with searching for an algroithm that saves us from having to chain OR's. Luckily modulus comes to mind and would solve this problem in less characters. Here is my implementation with modulus:

{{< highlight php "style=friendly" >}}
<?
function Translate($i) {
	if ($i % 3 == 0) {
		return "Fizz";
	}
	if ($i == 5) {
		return "Buzz";
	}
	return $i;
}
{{< /highlight >}}

Thats pretty neat, tests are passing. Onto the next [>>> **Refactoring phase**](#fizzbuzz-refactor-5)

##### <a name="fizzbuzz-green-6"></a>Green: FizzBuzz Step 6

Very much like the Green phase before we come to a fork, but we have learned from our previous encounter and know that modulus will be the neatest and simplest answer to our problem:

{{< highlight php "style=friendly" >}}
<?
function Translate($i) {
	if ($i % 3 == 0) {
		return "Fizz";
	}
	if ($i % 5 == 0) {
		return "Buzz";
	}
	return $i;
}
{{< /highlight >}}

Excellent. Onto the next [>>> **Refactoring phase**](#fizzbuzz-refactor-6)

##### <a name="fizzbuzz-green-7"></a>Green: FizzBuzz Step 7

We now have our final requirement and a failing test, great. How should we approach this problem, we currently have the following:

{{< highlight php "style=friendly" >}}
<?
function Translate($i) {
	if ($i % 3 == 0) {
		return "Fizz";
	}
	if ($i % 5 == 0) {
		return "Buzz";
	}
	return $i;
}
{{< /highlight >}}

We could use a preceeding conditional to check both divisibility by 3 and 5 and return like so:

{{< highlight php "style=friendly" >}}
<?
function Translate($i) {
	if ($i % 3 == 0 && $i % 5 == 0) {
		return "FizzBuzz";
	}
	if ($i % 3 == 0) {
		return "Fizz";
	}
	if ($i % 5 == 0) {
		return "Buzz";
	}
	return $i;
}
{{< /highlight >}}

But that seems a little clunky and is quite a big change, it does work though. Or how about string concatination with a little ternary return statement:

{{< highlight php "style=friendly" >}}
<?
function Translate($i) {
	$translation = "";
	if ($i % 3 == 0) {
		$translation .= "Fizz";
	}
	if ($i % 5 == 0) {
		$translation .= "Buzz";
	}

	return ($translation) ? $translation : $i;
}
{{< /highlight >}}

Arguably this change is as big as the last, we have added a new conditional, but it does seem a little more elegant making more use of what was already there (not repeating conditional statements). I am happy to settle on this, but its really a matter of preference.

Onto the final [>>> **Refactoring phase**](#fizzbuzz-refactor-7)

### <a name="refactor"></a>REFACTORING: Refactor both tests and production code

The refactoring phase is akin to tidying up as you go along. This is where we enforce our over-arching ideologies such as DRY (Don't repeat yourself) and YAGNI (You ain't gunna need it). We must refactor BOTH production and test code as arguably both are as important as the other. It is vital to note that during this phase no new behaviour should be introduced, the definition of refactoring for the purposes of TDD is: _Changing the structure of the code without changing the behaviour_. All tests must still pass at the end of this phase to be able to continue back into Red.

##### Refactor: FizzBuzz Step 1

Following our FizzBuzz example there isn't anything to be refactored so we would skip back into the [>>> **Red phase**](#fizzbuzz-red-2).

##### <a name="fizzbuzz-refactor-2"></a>Refactor: FizzBuzz Step 2

Our current code base looks as follows:

{{< highlight php "style=friendly" >}}
<?
function TestItPrints1WhenGiven1() {
	if (Translate(1) != 1) {
		print("FAIL! ". Translate(1) . " != 1");
	}
}

function TestItPrints2WhenGiven2() {
	if (Translate(2) != 2) {
		print("FAIL! ". Translate(1) . " != 2");
	}
}
{{< /highlight >}}

{{< highlight php "style=friendly" >}}
<?
function Translate($i) {
	return $i;
}
{{< /highlight >}}

I believe we have a DRY violation in our test code base, the 2 test contents look rather similar, and I'd like to refactor into a helper function as follows:

{{< highlight php "style=friendly" >}}
<?
function assertItTranslatesArgIntoExpected($arg, $expected) {
	if (Translate($arg) != $expected) {
		print("FAIL! ". Translate($arg) ." != ". $expected);
	}
}
{{< /highlight >}}

This leaves us with in our test code:

{{< highlight php "style=friendly" >}}
<?
function TestItPrints1WhenGiven1() {
	assertItTranslatesArgIntoExpected(1, 1);
}

function TestItPrints2WhenGiven2() {
	assertItTranslatesArgIntoExpected(2, 2);
}

function assertItTranslatesArgIntoExpected($arg, $expected) {
	if (Translate($arg) != $expected) {
		print("FAIL! ". Translate($arg) ." != ". $expected);
	}
}
{{< /highlight >}}

There is still nothing to refactor for the production code, and that is ok. Back into the [>>> **Red phase**](#fizzbuzz-red-3).

##### <a name="fizzbuzz-refactor-3"></a>Refactor: FizzBuzz Step 3

Our current code base looks as follows:

{{< highlight php "style=friendly" >}}
<?
function TestItPrints1WhenGiven1() {
	assertItTranslatesArgIntoExpected(1, 1);
}

function TestItPrints2WhenGiven2() {
	assertItTranslatesArgIntoExpected(2, 2);
}

function TestItPrintsFizzWhenGiven3() {
	assertItTranslatesArgIntoExpected(3, "Fizz");
}

function assertItTranslatesArgIntoExpected($arg, $expected) {
	if (Translate($arg) != $expected) {
		print("FAIL! ". Translate($arg) ." != ". $expected);
	}
}
{{< /highlight >}}

{{< highlight php "style=friendly" >}}
<?
function Translate($i) {
	if ($i == 3){
		return "Fizz";
	}
	return $i;
}
{{< /highlight >}}

Doesn't look like there is much to refactor right now. Onto the next [>>> **Red phase**](#fizzbuzz-red-4).

##### <a name="fizzbuzz-refactor-4"></a>Refactor: FizzBuzz Step 4

Like before there doesn't appear to be much to refactor, so we shall skip straight on to our next [>>> **Red phase**](#fizzbuzz-red-5).

##### <a name="fizzbuzz-refactor-5"></a>Refactor: FizzBuzz Step 5

As mentioned in the previous Red phase, we now have 2 tests that look awfully similar:

{{< highlight php "style=friendly" >}}
<?
function TestItPrints1WhenGiven1() {
	assertItTranslatesArgIntoExpected(1, 1);
}

function TestItPrints2WhenGiven2() {
	assertItTranslatesArgIntoExpected(2, 2);
}

function TestItPrintsFizzWhenGiven3() {
	assertItTranslatesArgIntoExpected(3, "Fizz");
}

function TestItPrintsBuzzWhenGiven5() {
	assertItTranslatesArgIntoExpected(5, "Buzz");
}

function TestItPrintsFizzWhenGiven6() {
	assertItTranslatesArgIntoExpected(6, "Fizz");
}

function assertItTranslatesArgIntoExpected($arg, $expected) {
	if (Translate($arg) != $expected) {
		print("FAIL! ". Translate($arg) ." != ". $expected);
	}
}
{{< /highlight >}}

Infact its not a coincidence that they look similar, there is an underlying business rule that connects them (the fact that they are divisible by 3 and should return 'Fizz') and with that realisation our understanding of this program has expanded, we now know that args that are divisable by 3 should always return 'Fizz' and our tests should reflect our new understanding by refactoring them into something with a more appropriate name... I propose '**TestItPrintsFizzWhenGivenANumberDivisableBy3**':

{{< highlight php "style=friendly" >}}
<?
function TestItPrints1WhenGiven1() {
	assertItTranslatesArgIntoExpected(1, 1);
}

function TestItPrints2WhenGiven2() {
	assertItTranslatesArgIntoExpected(2, 2);
}

function TestItPrintsBuzzWhenGiven5() {
	assertItTranslatesArgIntoExpected(5, "Buzz");
}

function TestItPrintsFizzWhenGivenANumberDivisableBy3() {
	$testCases = array(
		3,
		6,
	);

	foreach ($testCases as $testCase) {
		assertItTranslatesArgIntoExpected($testCase, "Fizz");
	}
}

function assertItTranslatesArgIntoExpected($arg, $expected) {
	if (Translate($arg) != $expected) {
		print("FAIL! ". Translate($arg) ." != ". $expected);
	}
}
{{< /highlight >}}

We can now move onto our next [>>> **Red phase**](#fizzbuzz-red-6).

##### <a name="fizzbuzz-refactor-6"></a>Refactor: FizzBuzz Step 6

We are back to where we were last refactoring phase, we have 2 tests that are intriniscally linked by a business rule:

{{< highlight php "style=friendly" >}}
<?
function TestItPrints1WhenGiven1() {
	assertItTranslatesArgIntoExpected(1, 1);
}

function TestItPrints2WhenGiven2() {
	assertItTranslatesArgIntoExpected(2, 2);
}

function TestItPrintsBuzzWhenGiven5() {
	assertItTranslatesArgIntoExpected(5, "Buzz");
}

function TestItPrintsFizzWhenGivenANumberDivisableBy3() {
	$testCases = array(
		3,
		6,
        9,
	);

	foreach ($testCases as $testCase) {
		assertItTranslatesArgIntoExpected($testCase, "Fizz");
	}
}

function TestItPrintsBuzzWhenGiven10() {
	assertItTranslatesArgIntoExpected(10, "Buzz");
}

function assertItTranslatesArgIntoExpected($arg, $expected) {
	if (Translate($arg) != $expected) {
		print("FAIL! ". Translate($arg) ." != ". $expected);
	}
}
{{< /highlight >}}

So to reflect our new understanding of the domain we shall condense the 2 tests into a single, and we can call it '**TestItPrintsBuzzWhenGivenANumberDivisibleBy5**':

{{< highlight php "style=friendly" >}}
<?
function TestItPrints1WhenGiven1() {
	assertItTranslatesArgIntoExpected(1, 1);
}

function TestItPrints2WhenGiven2() {
	assertItTranslatesArgIntoExpected(2, 2);
}

function TestItPrintsFizzWhenGivenANumberDivisableBy3() {
	$testCases = array(
		3,
		6,
		9,
	);

	foreach ($testCases as $testCase) {
		assertItTranslatesArgIntoExpected($testCase, "Fizz");
	}
}

function TestItPrintsBuzzWhenGivenANumberDivisibleBy5() {
	$testCases = array(
		5,
		10,
	);

	foreach ($testCases as $testCase) {
		assertItTranslatesArgIntoExpected($testCase, "Buzz");
	}
}

function assertItTranslatesArgIntoExpected($arg, $expected) {
	if (Translate($arg) != $expected) {
		print("FAIL! ". Translate($arg) ." != ". $expected);
	}
}
{{< /highlight >}}

Brilliant, but we still have our original 2 tests '**TestItPrints1WhenGiven1**' and '**TestItPrints2WhenGiven2**' which with our new understanding of the system can actually be refactored into their own condensed test, because we know that any number that is NOT divisible by 3 or 5 should return the original number, so lets call that test '**TestItPrintsNumberWhenGivenANumberNotDivisibleBy3or5**':

{{< highlight php "style=friendly" >}}
<?
function TestItPrintsNumberWhenGivenANumberNotDivisibleBy3or5() {
	$testCases = array(
		1,
		2,
		4,
		7,
		8,
	);

	foreach ($testCases as $testCase) {
		assertItTranslatesArgIntoExpected($testCase, $testCase);
	}
}

function TestItPrintsFizzWhenGivenANumberDivisableBy3() {
	$testCases = array(
		3,
		6,
		9,
	);

	foreach ($testCases as $testCase) {
		assertItTranslatesArgIntoExpected($testCase, "Fizz");
	}
}

function TestItPrintsBuzzWhenGivenANumberDivisibleBy5() {
	$testCases = array(
		5,
		10,
	);

	foreach ($testCases as $testCase) {
		assertItTranslatesArgIntoExpected($testCase, "Buzz");
	}
}

function assertItTranslatesArgIntoExpected($arg, $expected) {
	if (Translate($arg) != $expected) {
		print("FAIL! ". Translate($arg) ." != ". $expected);
	}
}
{{< /highlight >}}

And whilst we are at it, lets refactor all that looping that looks rather similar:

{{< highlight php "style=friendly" >}}
<?
function TestItPrintsNumberWhenGivenANumberNotDivisibleBy3or5() {
	$testCases = array(
		1,
		2,
		4,
		7,
		8,
	);

	assertTestCasesTranslateIntoExpected($testCases, null);
}

function TestItPrintsFizzWhenGivenANumberDivisableBy3() {
	$testCases = array(
		3,
		6,
		9,
	);

	assertTestCasesTranslateIntoExpected($testCases, "Fizz");
}

function TestItPrintsBuzzWhenGivenANumberDivisibleBy5() {
	$testCases = array(
		5,
		10,
	);

	assertTestCasesTranslateIntoExpected($testCases, "Buzz");
}

function assertTestCasesTranslateIntoExpected($testCases, $expectedValue) {
	foreach ($testCases as $testCase) {
		$expected = ($expectedValue == null ? $testCase : $expectedValue);
		assertItTranslatesArgIntoExpected($testCase, $expected);
	}
}

function assertItTranslatesArgIntoExpected($arg, $expected) {
	if (Translate($arg) != $expected) {
		print("FAIL! ". Translate($arg) ." != ". $expected);
	}
}
{{< /highlight >}}

Much better... [>>> **Red phase**](#fizzbuzz-red-7).

##### <a name="fizzbuzz-refactor-7"></a>Refactor: FizzBuzz Step 7

Well after all that we have ended up with:

{{< highlight php "style=friendly" >}}
<?
function TestItPrintsNumberWhenGivenANumberNotDivisibleBy3or5() {
	$testCases = array(
		1,
		2,
		4,
		7,
		8,
	);

	assertTestCasesTranslateIntoExpected($testCases, null);
}

function TestItPrintsFizzWhenGivenANumberDivisableBy3() {
	$testCases = array(
		3,
		6,
		9,
	);

	assertTestCasesTranslateIntoExpected($testCases, "Fizz");
}

function TestItPrintsBuzzWhenGivenANumberDivisibleBy5() {
	$testCases = array(
		5,
		10,
	);

	assertTestCasesTranslateIntoExpected($testCases, "Buzz");
}

function TestItPrintsFizzBuzzWhenGivenANumberDivisibleByBoth3and5() {
	$testCases = array(
		15,
	);

	assertTestCasesTranslateIntoExpected($testCases, "FizzBuzz");
}

function assertTestCasesTranslateIntoExpected($testCases, $expectedValue) {
	foreach ($testCases as $testCase) {
		$expected = ($expectedValue == null ? $testCase : $expectedValue);
		assertItTranslatesArgIntoExpected($testCase, $expected);
	}
}

function assertItTranslatesArgIntoExpected($arg, $expected) {
	if (Translate($arg) != $expected) {
		print("FAIL! ". Translate($arg) ." != ". $expected);
	}
}
{{< /highlight >}}

{{< highlight php "style=friendly" >}}
<?
function Translate($i) {
	$translation = "";
	if ($i % 3 == 0) {
		$translation .= "Fizz";
	}
	if ($i % 5 == 0) {
		$translation .= "Buzz";
	}

	return ($translation) ? $translation : $i;
}
{{< /highlight >}}

Doesn't look like there is much to refactor. Looks like we are done!