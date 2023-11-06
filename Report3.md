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
With the args:
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

```ruby
fati2@LAPTOP-COMJ3VDF MINGW64 ~/Desktop/15l/GitHub/Week-5/technical/911report (main)
$ grep -i "preface" preface.txt
            PREFACE
```
2. `-v or --invert-match: Invert the match, i.e., select non-matching lines.`
```ruby
fati2@LAPTOP-COMJ3VDF MINGW64 ~/Desktop/15l/GitHub/Week-5/technical/911report (main)
$ grep -iv "We" preface.txt

    

            PREFACE
                the President of the United States, the United States Congress, and the American
                people for their consideration. Ten Commissioners-five Republicans and five
                Democrats chosen by elected leaders from our nation's capital at a time of great
                partisan division-have come together to present this report without dissent.
                September 11, 2001, was a day of unprecedented shock and suffering in the history of
                avoid such tragedy again?
                Commission on Terrorist Attacks Upon the United States (Public Law 107-306, November
                27, 2002).
                relating to the terrorist attacks of September 11, 2001," including those relating
                to intelligence agencies, law enforcement agencies, diplomacy, immigration issues
                and border control, the flow of assets to terrorist organizations, commercial
                aviation, the role of congressional oversight and resource allocation, and other
                individuals in ten countries. This included nearly every senior official from the
                current and previous administrations who had responsibility for topics covered in
                public testimony from 160 witnesses.
            Our aim has not been to assign individual blame. Our aim has been to provide the
                fullest possible account of the events surrounding 9/11 and to identify lessons
                learned.
                enemy rallies broad support in the Arab and Muslim world by demanding redress of
                political grievances, but its hostility toward us and our values is limitless. Its
                purpose is to rid the world of religious and political pluralism, the plebiscite,
                targets. Collateral damage is not in its lexicon.
                and national security did not understand how grave this threat could be, and did not
                sharing information across a large and unwieldy government that had been built in a
                different era to confront different dangers.
                together as a nation. The test before us is to sustain that unity of purpose and
                think about the way our government is organized. The massive departments and
                accountability.
```
```ruby
fati2@LAPTOP-COMJ3VDF MINGW64 ~/Desktop/15l/GitHub/Week-5/technical/911report (main)
$ grep -v "and" preface.txt

    

            PREFACE
                Democrats chosen by elected leaders from our nation's capital at a time of great
                partisan division-have come together to present this report without dissent.
                avoid such tragedy again?
                Commission on Terrorist Attacks Upon the United States (Public Law 107-306, November
                27, 2002).
                relating to the terrorist attacks of September 11, 2001," including those relating
                to intelligence agencies, law enforcement agencies, diplomacy, immigration issues
                individuals in ten countries. This included nearly every senior official from the
                From the outset, we have been committed to share as much of our investigation as we
                public testimony from 160 witnesses.
            Our aim has not been to assign individual blame. Our aim has been to provide the
                learned.
                targets. Collateral damage is not in its lexicon.
            We learned that the institutions charged with protecting our borders, civil aviation,
                different era to confront different dangers.
            At the outset of our work, we said we were looking backward in order to look forward.
                We hope that the terrible losses chronicled in this report can create something
                meet the challenges now confronting us. We need to design a balanced strategy for
                the same time protecting our country against future attacks. We have been forced to
                agencies that prevailed in the great struggles of the twentieth century must work
                together in new ways, so that all the instruments of national power can be combined.
                accountability.
            As we complete our final report, we want to begin by thanking our fellow
                Commissioners, whose dedication to this task has been profound. We have reasoned
                of our colleagues, as well as our great affection for them.
            We want to thank the Commission staff. The dedicated professional staff, headed by
                Philip Zelikow, has contributed innumerable hours to the completion of this report,
                setting aside other important endeavors to take on this all-consuming assignment.
                They have conducted the exacting investigative work upon which the Commission has
                assistance. We owe a huge debt to their investigative labors, painstaking attention
                fine work helped us get started. We thank the City of New York for assistance with
                & Company for helping to get this report to the broad public.
            We conclude this list of thanks by coming full circle: We thank the families of 9/11,
                the importance of the work we have undertaken.
                This final report is only a summary of what we have done, citing only a fraction of
                the sources we have consulted. But in an event of this scale, touching so many
                every knowledgeable person or found every relevant piece of paper. New information
                inevitably will come to light. We present this report as a foundation for a better
                have admired their determination to do their best to prevent another tragedy while
                preparing to respond if it becomes necessary. We emerge from this investigation with
                for the American people. We recognize the formidable challenges that lie ahead.
            We also approach the task of recommendations with humility. We have made a limited
                number of them. We decided consciously to focus on recommendations we believe to be
                most important, whose implementation can make the greatest difference. We came into
                this process with strong opinions about what would work. All of us have had to
                considered the views of others. We hope our report will encourage our fellow
            Thomas H. Kean, chair
            Lee H. Hamilton, vice chair
```
3. `-l or --files-with-matches: Only list the names of files containing the pattern, not the actual matched lines.`
```ruby
fati2@LAPTOP-COMJ3VDF MINGW64 ~/Desktop/15l/GitHub/Week-5/technical/911report (main)
$ grep -l "and" *.txt
chapter-1.txt
chapter-10.txt
chapter-11.txt
chapter-12.txt
chapter-13.1.txt
chapter-13.2.txt
chapter-13.3.txt
chapter-13.4.txt
chapter-13.5.txt
chapter-2.txt
chapter-3.txt
chapter-5.txt
chapter-6.txt
chapter-7.txt
chapter-8.txt
chapter-9.txt
preface.txt
```
```ruby
fati2@LAPTOP-COMJ3VDF MINGW64 ~/Desktop/15l/GitHub/Week-5/technical/911report (main)
$ grep -il "preface" *.txt
chapter-13.3.txt
chapter-3.txt
preface.txt
```
4. `-n or --line-number: Show line numbers for matched lines.`
```ruby
fati2@LAPTOP-COMJ3VDF MINGW64 ~/Desktop/15l/GitHub/Week-5/technical/911report (main)
$ grep -in "preface" *.txt
chapter-13.3.txt:1699:                For its preface, the Plan quoted a memo Tenet had sent to the CIA's senior
chapter-3.txt:2650:                prefaced the directions with a message:"From the American President down to the
preface.txt:4:            PREFACE
```
```ruby
fati2@LAPTOP-COMJ3VDF MINGW64 ~/Desktop/15l/GitHub/Week-5/technical/911report (main)
$ grep -n "Yale" *.txt
chapter-13.3.txt:17:                ed.(Yale Univ. Press, 1990).
chapter-13.3.txt:251:                (Yale Univ. Press, 2000), p. 133; Steve Coll, Ghost Wars: The Secret History of the
chapter-13.3.txt:258:            65. See Owen Bennet Jones, Pakistan: Eye of the Storm (Yale Univ. Press, 2002);
chapter-13.3.txt:1275:                of Yugoslavia (Yale Univ. Press, 2000).
chapter-13.3.txt:1635:                Islam, Oil and Fundamentalism in Central Asia (Yale Univ. Press, 2000).
```
