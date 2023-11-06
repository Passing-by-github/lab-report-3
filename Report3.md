# Lab Report 3
In this lab, we explore Bugs within code and Command-line agruments.

Part 1
---
Errors within code are tricky to navigate, that's why we build tests to help us catch and diagnose those errors more effectively.  

Here, we created a test on a buggy program, reversed, that takes an array and save its reversed version in a new array:  


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
 public void testReverseInPlace() {
    int[] input1 = { 3 };
    ArrayExamples.reverseInPlace(input1);
    assertArrayEquals(new int[]{ 3 }, input1);
	}
```

The Reversed method code didn't change, which our test failed to detect its error:  
