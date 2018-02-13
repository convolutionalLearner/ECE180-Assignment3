# ECE-180 Assignment #3
Due: Feb 28, 11:15p (PST)

## Preface

We can all remember the simpler days (last week) when homework assignments involved building one simple class. That was fun, right?  Sadly, very few real-world software problems can be implemented so easily. Therefore, this assignment involves multiples classes that have to work together to accomplish the given task. 

## JobCo Plans to Go PUBLIC!

IPO! WOOT WOOT!  It's very exciting when your cool little startup grows up, and is ready to go public. Sure, lots of early employees stand to make a lot of money on their stock grants. But it's not all fun and games you know. The process of "going public" takes a tremendous amount of work, making sure that all of your internal processes are up to the standards necessary to meet public scrutiny. As it turns out, JobCo has more than a few problems, and you've been placed on the "going public" software team. Good luck!

### Calendrical Computations

"Ok everyone, settle down", Chloe says, smiling softly. "As you've all heard, JobCo is going public...", she begins, then stops abruptly as the room explodes into cheers and shouting. Waiting for nearly a minute, she finally continues, "Ok, ok, before you all rush out and buy yourself a Tesla, we have some work to do." 

## Assignment Details

In this assignment, you are going to build a series of classes that support calendrical computations. Dates, times, timezones, and intervals. Customer of your solution will use your classes to do things like creating timers in their own code, or performing timezone conversions within their application. All of your classes will be declared within a namespace called "SoftwareFoundations".

At a minimum, you will implement five classes:

```
class SFDate;
class SFTime;
class SFDateTime;
class SFTimezone;
class SFInterval;
```

Note that you are free to create any other classes you need in order to support your solution. Presumably, if you add other classes it is to provide functionality to your solution that you have deemed "worthy" to be part of your solution. Totally up to you. Our testing framework will only interact with the five classes we have outlined above.

### About Your Classes

You are free to implement your `Date` class any way you see fit. It must, however, fulfill certain requirements:

#### The SFDate Class 

- You must be able to construct a `SFDate` class without any arguments, in which case it refers to the current date
- You must be able to construct a `SFDate` class using 3 integers (int month, int day, int year)
- You must be able to construct a `SFDate` class using a well-formed string ("Jan 4, 1961")
- You must be able to copy construct a `SFDate` from another `SFDate` class, or convert/construct from a `SFDateTime` class
- You must provide a conversion operator from your `SFDate` class to a const char*. 

Your `Date` class must also support basic operations to change the date incrementally, relational operator, as well as methods to get/set various properties of the date. Here's a summary of your interface:

```

class SFDate {
  SFDate();                             //default to today, in GMT timezone
  SFDate(const char *aDateTimeString);  //must parse the given (well-formed) string  -- (more details below)
  SFDate(int month, int day, int year); //build date from individual parts
  SFDate(const SFDate &aCopy);  
  SFDate(const SFDateTime &aCopy);

  operator ++(); //advance by one day
  operator --(); //back up by one day...
  
  Interval operator-(const Date& aCopy); //returns Interval object (difference between two Date objects)

  SFDate& adjustByDays(int n) -- to add or subtract N days from the given date
  SFDate& adjustByWeeks(int n) -- to add or subtract N weeks from the given date
  SFDate& adjustByMonths(int n) -- to add or subtract N months from the given date
  SFDate& adjustByYears(int n) -- to add or subtract N years from the given date
  
  SFDate& setDay(int aDay)
  SFDate& setMonth(int aMonth)
  SFDate& setYear(int aYear)
  
  int   getDay()
  int   getMonth()
  int   getYear()  
  int   getWeekOfYear() //1..52
  int   getDayOfYear() //1..365
  in    getDayOfweek() //0..6 {0==sunday}
  int   daysInMonth() //based on month/year, return # of days...
  
    //ADD RELATIONAL OPERATORS HERE... >, <, <=, >=, !=, ==

  SFDate& startOfMonth(); (02/01/2018) //depends on month...
  SFDate& endOfMonth();  (02/28/2018)  //depends on month
  SFDate& startOfYear(); (01/01/2018)
  SFDate& endOfYear();   (12/31/2018)

  std::string toDateString();  
  
  ... more members here as necessary...

}
```

#### The SFTime Class 

This class is much simpler, as shown below:

```
class SFTime {
  SFTime();                             //default to now(HH:MM:SS) 
  SFTime(const char *aTimeString);      //must parse fro the given (well-formed) string  
  SFTime(int anHour, int aMinutes, int aSeconds); //build time from individual parts
  SFTime(const SFTime &aCopy);  
  SFTime(const SFDateTime &aCopy);

  Interval operator-(const SFTime& aCopy); //returns Interval object (difference between two Time objects)

  int   getHour()
  int   getMinutes()
  int   getSeconds()  

  SFTime& startOfDay(); (00:00:00)
  SFTime& endOfDay();   (23:59:59)
  
  std::string toTimeString();  

    //ADD RELATIONAL OPERATORS HERE... >, <, <=, >=, !=, ==

  ... more members here as necessary...

}
```

#### The SFDateTime Class 

You are free to implement your `SFDateTime` class any way you see fit. In general, the `SFDateTime` class combines features of the `SFDate` class with features of the `SFTime` class. In addition, the `DateTime` class must also support timezones and relational operators.  

```
class SFDateTime {

  SFDateTime(Timezone *aTimezone=nullptr); //init a new datetime based on GMT, unless a valid timezone is provided
  SFDateTime(const SFDateTime &aCopy); //copy construct
  SFDateTime(int aMonth, int aDay, int aYear, int anHour=0, int aMinutes=0, int aSeconds=0, Timezone *aTimezone=nullptr); 
  SFDateTime(const char* aString, SFTimezone *aTimezone=nullptr); //parse the given string (details below)
  SFDateTime(const SFDate &aDate, const SFTime &aTime, SFTimezone *aTimezone=nullptr); 
             
  Interval operator-(const SFDateTime& aCopy); //returns Interval object (difference between two DateTime objects)
             
  SFTimezone&  getTimezone();
  SFDateTime&  setTimezone(SFTimezone &aTimezone);

    //ADD RELATIONAL OPERATORS HERE... >, <, <=, >=, !=, ==

  operator const char*();
  operator SFDate(); 
  operator SFTime();
  operator SFTimezone();             
             
  std::string toDateTimeString();   //Jan 4, 1961 09:15:00 PST 
             
  ... more members here as necessary...

}
```

#### The SFTimezone Class 

Timezones can be tricky, so we're keeping the requirements to a minimum. The baseline timezone for your solution is "GMT" -- which refers to Greenwich Mean Time, clock time at the Royal Observatory in Greenwich, London. It is the same all year round and is not affected by Summer Time or Daylight Saving Time.

Although there a more than 20 _actual_ timezones, we will only ever ask you to support four, using an abbreviation:

1. GMT (GMT as described above)
2. EST (US Eastern standard time)
3. CST (US Central standard time)
4. PST (US Pacific standard time)

Your timezone class must support default construction, copy construction, and construction from one of the four abbreviations listed above. We will also ask your Timezone class to provide a const char* conversion operator in case a caller wants to be able to print out the string value of the current timezone.

```
class SFTimezone {
  SFTimezone(const char* aTimezoneAbbrev);
  SFTimezone(const SFTimezone &aTimezone);
  
  operator const char*();
  
  ...other members as needed...
};
```

#### The SFInterval Class 

Date intervals are used to determine the calendrical distance bewteen two calendrical classes. SFTime, SFDate and SFDateTime all offer a conversion operator that returns an SFInterval. For `SFTime` classes, only time relevant properties are set (h,min,sec). For `SFDate` class, only date-relevant properties are set (m,d,y). For `SFDateTime` instances, date and time properties are all set. 

```
class SFInterval {
  int years;
  int months;
  int days;
  int hours;
  int minutes;
  int seconds;  
}
```

### The Testing Interface

In our last assignment, we provided you with a specific class interface. In this assignment, the interface is up to you. Instead, we are providing you with a "boilerplate" test-harness that you'll use to test your implementation. Once you turn in your work, Vlad-the-compiler will use your testing harness to perform operations that we can use to grade your work.  

The testing harness looks like this:

```
class SFTestHarness {
public:
  static int runDateTests();
  static int runTimeTests();
  static int runDateTimeTests();
  static int runIntervalTests();
  static int runTimezoneTests();
  static int runTests() {
    runDateTests();
    runTimeTests();
    runDateTimeTests();
    runIntervaltests();
    runTimezoneTests();
  }
};
```

### Submitting Your Assignment

more details here...
