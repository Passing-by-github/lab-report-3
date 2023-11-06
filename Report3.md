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
Part 2
---
Bash commands are a very powerful tool, used to interact with the computer, manage files, run programs, and perform various tasks. And to utilize it efficiently, we'll look into command-line agruments to place with the command.  
I will be focusing on the command `grep`, which is like f+cntl, a way to find lines containing the words we want. I used chatgpt to find its arguments by asking it directly: What’s the command line options for grep?  
It gave me a fairshare of arguments and I'll go through some of them here:

1.  `-i or --ignore-case: Perform a case-insensitive search.`
Without the arg: 
```ruby
fati2@LAPTOP-COMJ3VDF MINGW64 ~/Desktop/15l/GitHub/Week-5/technical/911report (main)
$ grep "WE" chapter-1.txt
"WE HAVE SOME PLANES"
```
```ruby
fati2@LAPTOP-COMJ3VDF MINGW64 ~/Desktop/15l/GitHub/Week-5/technical/911report (main)
$ grep -i "WE" chapter-1.txt
"WE HAVE SOME PLANES"
    Tuesday, September 11, 2001, dawned temperate and nearly cloudless in the eastern United States. Millions of men and women readied themselves for work. Some made their way to the Twin Towers, the signature structures of the World Trade Center complex in New York City. Others went to Arlington, Virginia, to the Pentagon. Across the Potomac River, the United States Congress was back in session. At the other end of Pennsylvania Avenue, people began to line up for a White House tour. In Sarasota, Florida, President George W. Bush went for an early morning run.
    For those heading to an airport, weather conditions could not have been better for a safe and pleasant journey. Among the travelers were Mohamed 
Atta and Abdul Aziz al Omari, who arrived at the airport in Portland, Maine.
    When he checked in for his flight to Boston, Atta was selected by a computerized prescreening system known as CAPPS (Computer Assisted Passenger 
Prescreening System), created to identify passengers who should be subject to special security measures. Under security rules in place at the time, the only consequence of Atta's selection by CAPPS was that his checked bags were held off the plane until it was confirmed that he had boarded the aircraft. This did not hinder Atta's plans.
    Between 6:45 and 7:40, Atta and Omari, along with Satam al Suqami, Wail al Shehri, and Waleed al Shehri, checked in and boarded American Airlines Flight 11, bound for Los Angeles. The flight was scheduled to depart at 7:45.
    In another Logan terminal, Shehhi, joined by Fayez Banihammad, Mohand al Shehri, Ahmed al Ghamdi, and Hamza al Ghamdi, checked in for United Airlines Flight 175, also bound for Los Angeles. A couple of Shehhi's colleagues were obviously unused to travel; according to the United ticket agent, they had trouble understanding the standard security questions, and she had to go over them slowly until they gave the routine, reassuring answers.   
    The security checkpoints through which passengers, including Atta and his colleagues, gained access to the American 11 gate were operated by Globe Security under a contract with American Airlines. In a different terminal, the single checkpoint through which passengers for United 175 passed was controlled by United Airlines, which had contracted with Huntleigh USA to perform the screening.
...
```
2.  
