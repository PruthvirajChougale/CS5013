What the stack trace is saying
This is a normal JUnit failure, not a build problem.

The project compiled successfully.
JUnit ran all tests.
9 tests passed.
1 test failed: “quotesDiscountForLoyalCustomer()”
The failing assertion is in PriceEngineTest.java:79-93
The important part is:

Expected: 90.0
Actual: 95.0
That means the code applied a 5% loyalty discount instead of the required 10% discount for a customer with 5 years of loyalty.
Most likely root cause
The likely bug is in the loyalty-tier logic in PriceEngine.java:39-59.

The code is effectively doing this:

if years > 5 → 10%
else if years >= 3 → 5%
else if years >= 1 → 2%
For a customer with 5 years:

5 > 5 is false
5 >= 3 is true
So the method incorrectly takes the 5% tier instead of the 10% tier. That is an off-by-one boundary bug: the check for the top loyalty level is wrong at exactly 5 years.
In plain English: the program treats 5 years as “middle loyalty,” not “top loyalty,” which is why the test expected 90 but got 95.

This is a logic bug in the business rule, not a setup or JUnit issue.