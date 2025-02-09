---
layout: default
---
[<- _Back_](./)

## The Birthday Paradox

_In a set of N randomly chosen people, what's the probability of at least two of them sharing the same birthday?_

Before proceeding, let's make the definition of "birthday" for this problem. I'm talking about the days in a Gregorian calendar.
A birthday is a valid combination of a day and a month in such calendar. For example, 2 people share the same birthday if one of them was born on January 26th, 1975 and the other one was born on January 26th, 2005, i.e. birth years aren't taken into account for this problem. 

__Remark__: For simplicity you can consider only birthdays on a common year, i.e. you can ignore February 29th that's available in the leap years. Thus, you can assume the sets of people includes no leaplings for the following calculations. When they are taken into account, the changes in those calculations are negligible. Also assume that any person's birthday is equally likely to be any of 365 days.

For N=23, the probability of at least two people sharing the same birthday is more than 50%. A set of 23 people seems too small to hit such a high probability and that's why this is referred as the birthday paradox. But how would someone calculate the probability of at least 2 people sharing the same birthday for a set of N people?

Let's ask a complementary question, what's the probability of no two people sharing the same birthday in the selected set?
* Let the probability of at least two people sharing the same birthday in a set of N people be P(A).
* Let the probability of no two people sharing the same birthday in the same set be P(A').

It can be said that those events are mutually exclusive because in any set of people 
either there are at least two people sharing the same birthday or all of them have different birthdays. Thus P(A) + P(A') = 1.
So, if P(A') is calculated, then P(A) can be calculated easily since P(A) = 1 - P(A')
[__Pigeonhole principle__](https://en.wikipedia.org/wiki/Pigeonhole_principle) can be utilized in order to calculate P(A').

First of all, it should be denoted that for a set of more than 365 people, it's impossible that each of them having different birthdays. For sets of size N<=365, the following statements can be generalized.
If all the people in a given set would have different birthdays, then the first person might be born on any of the 365 days.
The second person could be born on any of the remaining 364 days. The third person could be born on any of the remaining 363 days, so on and so forth. For N people the number of selections is

365 x 364 x 363 x ... (365 - N + 1)

If there wouldn't be any restrictions on the distribution of the birthdays, the number of selections is

365^N

The ratio of these 2 terms would give us the probability of all N people having different birthdays. Subtracting that ratio from 1 would give us the probability of at least two people sharing the same birthday. Here's the Java implementation of the calculations I've described.

```java
/**
 * Author: Fakih Karademir
 */
import java.math.BigDecimal;
import java.math.MathContext;

public class BirthdayParadox {
    private static final int DAY_COUNT_IN_GREGORIAN_YEAR = 365;

    public static void main(String[] args) {
        int[] personCounts = {1, 2, 3, 4, 10, 20, 21, 22, 23, 24, 50, 100};
        for (int personCount : personCounts) {
            String formattedPersonCount = String.format("%3d", personCount);
            String formattedProbability = String.format("%.20f", probabilityOfAtLeastTwoPeopleSharingTheSameBirthday(personCount));
            System.out.println(formattedPersonCount + " -> " + formattedProbability);
        }
    }

    private static BigDecimal probabilityOfAtLeastTwoPeopleSharingTheSameBirthday(int personCount) {
        if (personCount < 2) {
            return BigDecimal.ZERO;
        }
        if (personCount > DAY_COUNT_IN_GREGORIAN_YEAR) {
            return BigDecimal.ONE;
        }
        BigDecimal availableBirthdayCount = BigDecimal.valueOf(DAY_COUNT_IN_GREGORIAN_YEAR);
        BigDecimal birthdaySelectionCountWithoutRestriction = availableBirthdayCount.pow(personCount);
        BigDecimal birthdaySelectionCountWithoutCollision = BigDecimal.ONE;
        for (int i = 0; i < personCount; i++) {
            birthdaySelectionCountWithoutCollision = birthdaySelectionCountWithoutCollision.multiply(availableBirthdayCount);
            availableBirthdayCount = availableBirthdayCount.subtract(BigDecimal.ONE);
        }
        BigDecimal probabilityOfNoTwoPeopleSharingTheSameBirthday =
                birthdaySelectionCountWithoutCollision.divide(birthdaySelectionCountWithoutRestriction, new MathContext(20));
        return BigDecimal.ONE.subtract(probabilityOfNoTwoPeopleSharingTheSameBirthday);
    }
}
```
The output of the code above will look like the following:
```
  1 -> 0.00000000000000000000
  2 -> 0.00273972602739726027
  3 -> 0.00820416588478138488
  4 -> 0.01635591246655030500
 10 -> 0.11694817771107765187
 20 -> 0.41143838358057998762
 21 -> 0.44368833516520574172
 22 -> 0.47569530766255006891
 23 -> 0.50729723432398540723
 24 -> 0.53834425791452879252
 50 -> 0.97037357957798839992
100 -> 0.99999969275107214842
```
Let's interpret those values. For a set of 23 people, the probability of at least two people sharing the same birthday is more than 50%. The probability jumps to more than 97% when the set size is 50. For a set of size >= 100 it is almost certain that there are at least two people sharing the same birthday.

Here's an excerpt from Wikipedia's [Small business](https://en.wikipedia.org/wiki/Small_business) page.

| Business size category | Size according to European Union |
|------------------------|----------------------------------|
| Minute/Micro           | <10                              |
| Small                  | <50                              |
| Medium                 | <250                             |
| Large                  | <1000                            |
| Enterprise             | >1000                            |

In any medium business, there is an employee highly likely sharing their birthday with some of their colleague(s).
In any enterprise, there's certainly some employee sharing their birthday with some of their colleague(s).
