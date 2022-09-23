---
description: A template for calcaulate dates, including leap month, year etc.
---

# Date

### Determine the day of week given date (year, month, day)

```cpp
int dayofweek(int d, int m, int y) {
    static int t[] = {0, 3, 2, 5, 0, 3, 5, 1, 4, 6, 2, 4};
    y -= m < 3;
    return (y + y / 4 - y / 100 + y / 400 + t[m - 1] + d) % 7;
}
```

Reference:

[How to determine the day of the week, given the month, day and year](https://cs.uwaterloo.ca/\~alopez-o/math-faq/node73.html)

### Find difference of days between two dates (DD-MM-YYYY)

1. A const array containing number of days in a month
2. A function to check leap year
3. A function to get days in a certain month in a certain year
4. A function to get how many days from 1971.1.1 till now
5. Main function takes two dates as parameters

```cpp
class Solution {
public:
    // 1
    const int months[13] = {
        0, 31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31
    };
    
    // 2. return 1 if leap year, return 0 if not
    int isLeap(int year) {
        if (year % 4 == 0 && year % 100 != 0 || year % 400 == 0)
            return 1;
        return 0;
    }
    
    // 3. get days in month in a certain year
    int getDaysInAMonth(int month, int year) {
        if (month != 2) return months[month];
        return isLeap(year) + 28;
    }
    
    // 4. get how many days from 1971.1.1 till now
    int getDays(string date) {
        int year, month, day;
        sscanf(date.c_str(), "%d-%d-%d", &year, &month, &day);
        
        int days = 0;
        
        // year count
        for (int i = 1971;  i < year; i++) {
            days += 365 + isLeap(i);
        }
        
        // month count
        for (int i = 1; i < month; i++) {
            days += getDaysInAMonth(i, year);
        }
        
        // day count
        days += day;
        
        return days;        
    }
    
    // 5. main function to get difference of two dates
    int daysBetweenDates(string date1, string date2) {
        return abs(getDays(date1) - getDays(date2));
    }
};
```
