# Lab Report 3
In this lab, we explore Bugs within code and Command-line agruments.

Part 1
---
Errors within code are tricky to navigate, that's why we build tests to help us catch and diagnose those errors more effectively.  

Here, we created a test on a buggy program, reversed, that takes an array and save its reversed version in a new array:  
```SAS
	@Test
  	public void testReversed() {
    		int[] input1 = { 1,2 };
    		assertArrayEquals(new int[]{ 2,1 }, ArrayExamples.reversed(input1));
  	}
```

Our input for the test is a standard array and the expected output, but it caused a failure due to the code's buggy nature.  
The Reversed method code can be seen here:  
```ruby
// Returns a *new* array with all the elements of the input array in reversed
// order
  static int[] reversed(int[] arr) {
    int[] newArray = new int[arr.length];
    for(int i = 0; i < arr.length; i += 1) {
      arr[i] = newArray[arr.length - i - 1];
    }
    return arr;
  }
```

However, the tricky part of testing is our input must also cover all the ways the code could break. As seen with this input that didn't cause the test to fail:  
``` javascript
@Test
  public void testReversed() {
    int[] input1 = { };
    assertArrayEquals(new int[]{ }, ArrayExamples.reversed(input1));
  }
```

The Reversed method code didn't change, which means our test failed to detect its error:  
```ruby
// Returns a *new* array with all the elements of the input array in reversed
// order
  static int[] reversed(int[] arr) {
    int[] newArray = new int[arr.length];
    for(int i = 0; i < arr.length; i += 1) {
      arr[i] = newArray[arr.length - i - 1];
    }
    return arr;
  }
```

Now, how did we know that the code is faulty? That's due to the printed outputs of the tests. 

In the first test, this was the output:
```Q
OK (2 tests)

[user@sahara ~/lab3]$ javac -cp .:lib/hamcrest-core-1.3.jar:lib/junit-4.13.2.jar *.java
[user@sahara ~/lab3]$  java -cp .:lib/hamcrest-core-1.3.jar:lib/junit-4.13.2.jar org.junit.runner.JUnitCore ArrayTests
JUnit version 4.13.2
..E
Time: 0.008
There was 1 failure:
1) testReversed(ArrayTests)
arrays first differed at element [0]; expected:<2> but was:<0>
at org.junit.internal.ComparisonCriteria.arrayEquals(Comp
arisonCriteria.java:78)
1) testReversed(ArrayTests)
arrays first differed at element [0]; expected:<2> but was:<0>
at org.junit.internal.ComparisonCriteria.arrayEquals(ComparisonCriteria.java:78)
at org.junit.internal.ComparisonCriteria.arrayEquals(ComparisonCriteria.java:28)
at org.junit.Assert.internalArrayEquals(Assert.java:534)
at org.junit.Assert.assertArrayEquals(Assert.java:418)
at org.junit.Assert.assertArrayEquals(Assert.java:429)
at ArrayTests.testReversed(ArrayTests.java:16)
... 30 trimmed
Caused by: java.lang.AssertionError: expected:<2> but was:<0>
at org.junit.Assert.fail(Assert.java:89)
at org.junit.Assert.failNotEquals(Assert.java:835)
at org.junit.Assert.assertEquals(Assert.java:120)
at org.junit.Assert.assertEquals(Assert.java:146)
at org.junit.internal.ExactComparisonCriteria.assertElementsEqual(ExactComparisonCriteria.java:8)
at org.junit.internal.ComparisonCriteria.arrayEquals(ComparisonCriteria.java:76)
... 36 more

FAILURES!!!
Tests run: 2,  Failures: 1
```

The reason we know why the code has errors is because the tests showcased **Symptoms**, evience that the code has a problem. 

And the second test didn't have any **symptoms**:
```Q
JUnit version 4.13.2
..
Time: 0.007

OK (2 tests)
```

The underlying issue in the software is called a **Bug**. To fix this issue, we observe our code to find where we went wrong, and the tests help us in this area:
```ruby
// Returns a *new* array with all the elements of the input array in reversed
// order
  static int[] reversed(int[] arr) {
    int[] newArray = new int[arr.length];
    for(int i = 0; i < arr.length; i += 1) {
      arr[i] = newArray[arr.length - i - 1];
    }
    return arr;
  }
```

In the test output, we see that the first element of the array was expected to be `<1>`, that means the bug exists somewhere within the array. Another hint within the test output is the line of code that broke the test `(ExactComparisonCriteria.java:8)` which corresponds to this line in reversed method:  
`arr[i] = newArray[arr.length - i - 1];`  
Eureka! Looking over this line of code, we notice that it's updating the argument array `arr` using the element of `newArray` instead of the opposite.  
Also, without what should be returned is `newArray`, not `arr`, so that's a bug we've obseved by looking at the code itself.
Here's the final version of the method, bug-free:
```ruby
  // Returns a *new* array with all the elements of the input array in reversed
  // order
  static int[] reversed(int[] arr) {
    int[] newArray = new int[arr.length];
    for(int i = 0; i < arr.length; i += 1) {
      newArray[i] = arr[arr.length - i - 1];
    }
    return newArray;
  }
```
