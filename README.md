# CSE 11 Fall 2022 PA4 - Airline Reservation System
**Due date: Thursday, Oct 27 @ 11:59PM PDT**

There is an [FAQ post](https://piazza.com/class/l871flmwsqy8i/post/449) on Piazza. Please read that post first if you have any questions.

**Download the starter code from GitHub by clicking Code-> Download ZIP**
> __Warning__ 
> 
> *Do NOT add any additional import statements. Doing so will result in a **0** for your submission.*
## Provided Files
- AirlineReservation.java
- sample.csv
- otherSample.csv

## Goal
Programming Assignment 4 is an introduction to one dimensional arrays in Java. You will need to use arrays, method overloading, and many of the other programming techniques you learned from previous PAs to complete the assignment.

## Overview
- Survey [Google Form, 1 point]
- Airline Reservations System [Gradescope, 99 points]
    - Implementation [94 points]
    - Style [5 points]

## Survey [1 Point]
Please fill out [this survey](https://forms.gle/VX3AEsadFW9Yy4iL8) regarding your experience in this course.

## Airline Reservation System [99 points]
Airline reservation systems are systems that allow an airline to sell their seats on a flight. In this programming assignment, we will implement a simple airline reservation system that allows the user to book, cancel, and upgrade tickets. They can also lookup a passenger's seat (row) number, print the number available tickets in each travel class, and print a view of the plane with passenger names.


### 1D Airplane
To represent our airplane, we will be utilizing a 1D `String` array of length `n` named `passengers` to represent an airplane that has `n` rows. This airplane has one seat per row. For convenience, this plane will start with row `0` and end with row `n-1`.
```
passengers = ["Cao", "Miranda", "Niema", ..., "Minnes"] 
Row # =        0        1         2            n - 1
```
Additionally, this airplane will have three travel classes: First class, Business Class, and Economy Class. Each seat (row) is associated with one of these classes, where all seats of a given class are in a single contiguous (adjacent) section. The plane will always have its First Class in front (row 0), then Business Class, then Economy Class.
```
passengers = ["Cao", "Miranda", "Niema", "Chin", "Minnes"]
Class =        F        F         B        E        E
```
The above example is valid because each travel class is in a single contiguous section and the ordering is correct.
```
passengers = ["Cao", "Miranda", "Niema", "Chin", "Minnes"]
Class =        F        F         B        E        B
```
The above example is invalid because there are two clusters (non-adjacent sections) for Business Class.

### Input
The input will be a [CSV](https://en.wikipedia.org/wiki/Comma-separated_values) file which contains information on the number of rows on the plane, the number of rows per travel classes, and the initial seat bookings of passengers. The CSV input file will always follow the below format:

```
Total number of rows, number of first class rows, number of business class rows
Row number, passenger name,
Row number, passenger name,
Row number, passenger name,
..
```
*The additonal comma after passenger name is necessary for the alignment of the CSV file.*

Contents of `sample.csv` (provided to you):
```
10,1,2
0,Paul Cao,
2,Mia Minnes,
7,Joe Politz,
8,Niema Moshiri,
```
The first line of `sample.csv` corresponds to 10 rows on this plane with 1 row of first class and 2 rows of business class. The remaining 7 rows are economy class.
The following lines populates the plane with existing passengers. Here is a visualization of how the plane is initialized:
```
Row #   |Class| Passenger name
0        | F | Paul Cao 
1        | B |
2        | B | Mia Minnes
3        | E |
4        | E |
5        | E |
6        | E |
7        | E | Joe Politz
8        | E | Niema Moshiri
9        | E |
```


We will pass the CSV's file name through command line arguments and utilize Java's `Scanner` class to read from the CSV file.

### Static Variables
> __Warning__ 
> 
> *Do NOT modify these variable names.*
> *Do NOT add any additional static variables. (Unless they are private static final variables.)*
- `static String[] passengers`: Array used to store passenger names in their assigned seat row. If there is no passenger in a row, the corresponding index will contain `null`. Duplicate names are not allowed.
- `static int planeRows`: The number of total rows (seats) on this plane.
- `static int firstClassRows`: The number of rows in first class.
- `static int businessClassRows`: The number of rows in business class.

### Methods
#### Helper Methods
- `private static int findClass(int row)`
    - Return the travel class corresponding to the given `row`.
    - `FIRST_CLASS`, `BUSINESS_CLASS`, and `ECONOMY_CLASS` correspond to `0`, `1`, and `2` respectively. ***These constants are already defined for you in the starter code***.
    - Return`-1` if `row` does not exist on the plane.
- `private static int findFirstRow(int travelClass)`
    - Return the first row of the given `travelClass`.
    - Return `-1` if `travelClass` is not `FIRST_CLASS`, `BUSINESS_CLASS`, or `ECONOMY_CLASS`.
    - Ex: `findFirstRow(ECONOMY_CLASS)` using `sample.csv` returns 3.
- `private static int findLastRow(int travelClass)`
    - Return the last row of a given `travelClass`.
    - Return `-1` if `travelClass` is not `FIRST_CLASS`, `BUSINESS_CLASS`, or `ECONOMY_CLASS`.
    - Ex: `findLastRow(ECONOMY_CLASS)` using `sample.csv` returns 9.
- `private static void initPassengers(String fileName) throws FileNotFoundException`
    - Initializes the static variables `passengers`, `planeRows`, `firstClassRows`, and `businessClassRows` using the contents of the CSV file named `fileName`.
    - You may assume the following:
        - The file named fileName exists and is a valid, properly formatted CSV file.
        - There are no duplicate passenger names.
        - firstClassRows $\geq$ 1
        - businessClassRows $\geq$ 1
        - (planeRows - firstClassRows - businessClassRows) $\geq$ 1 *(The number of economy rows is* $\geq$ *1)*
    - In other words, you may assume that there is *at least one row* in each travel class.
    - *Note: It is possible that the CSV file only has 1 line, in which case `passengers` should still be initialized with all empty seats.*


#### Public Methods
- `public static boolean book(String passengerName, int travelClass)`
    - Book a ticket for `passengerName` in the `travelClass` section. Place `passengerName` in the first available row of the selected `travelClass`. A row is available if there is no other passenger that booked that row.
    - Return `false` if no seats are available for the selected `travelClass`, otherwise return `true`.
    - If `passengerName` is `null`, return `false`.
    - You may assume that `travelClass` is `FIRST_CLASS`, `BUSINESS_CLASS`, or `ECONOMY_CLASS`.
    - You may assume that `passengerName` does not already have a booking.
- `public static boolean book(int row, String passengerName)`
    - Book a ticket for `passengerName` at the seat number `row`. If the `row` is available, place `passengerName` in the `row`. If the `row` is not available, place `passengerName` in the first available row in the same travel class as `row`.
    - Return `false` if no seats are available in the travel class of `row`, otherwise return `true`.
    - Return `false` if `passengerName` is `null`.
    - You may assume that `row` is valid.
    - You may assume that `passengerName` does not already have a booking.

- `public static boolean cancel(String passengerName)`
    - Cancel the booking for `passengerName`. Remove `passengerName` from the `passengers` array.
    - Return `true` upon successful removal, `false` otherwise.
    - If `passengerName` is `null`, return `false`.
- `public static int lookUp(String passengerName)`
    - Look up the row number of `passengerName`. Return the row number of `passengerName`, or return `-1` if not found.
    - If `passengerName` is `null`, return `-1`.
- `public static int[] availableTickets()`
    - Find and return the number of available tickets in each travel class.
    - The return value should be in the format of `[first class, business class, economy class]`.
- `public static boolean upgrade(String passengerName, int upgradeClass)`
    - Upgrade `passengerName` to a seat in the `upgradeClass`. 
    - If `passengerName` is `null` or `passengerName` is not found in `passengers`, return `false`.
    - If `upgradeClass` is lower or equal to `passengerName`'s existing class, return `false` and do not modify `passengers`. *(i.e. a First Class passenger trying to 'upgrade' to Business Class)*    
    - Otherise, upgrade (move) `passengerName` to the first available row in `upgradeClass`.
    - Return `true` if the upgrade is successful, `false` otherwise.
    - You may assume that `travelClass` is `FIRST_CLASS`, `BUSINESS_CLASS`, or `ECONOMY_CLASS`.
    - *Note: if the upgrade failed, the passenger should stay in the same row.*

#### Provided Methods

- `public static void main (String[] args) throws FileNotFoundException`
    - Provides a command line interface where the user can utilize the Airline Reservation System.
    - The following commands are available: 
        - book \<travelClass(F/B/E)\> \<passengerName\>
        - book \<rowNumber\> \<passengerName\>
        - cancel \<passengerName\> 
        - lookup \<passengerName\>
        - availabletickets 
        - upgrade \<travelClass(F/B)\> \<passengerName\>
        - print
        - exit
    - You do not need to understand how this method was implemented in order to do this PA.
    - The commands and `travelClass` are case-insensitive. But `passengerName` is case-sensitive.
- `public static void printPlane()`
    - Prints out a visualization of the current state of the plane.

    
### Examples
#### Example 1:
```
$ java AirlineReservation sample.csv 
Available commands:
- book <travelClass(F/B/E)> <passengerName>
- book <rowNumber> <passengerName>
- cancel <passengerName>
- lookup <passengerName>
- availabletickets
- upgrade <travelClass(F/B)> <passengerName>
- print
- exit
> print
0        | F | Paul Cao 
1        | B |
2        | B | Mia Minnes
3        | E |
4        | E |
5        | E |
6        | E |
7        | E | Joe Politz
8        | E | Niema Moshiri
9        | E |
> book 6 CSE11 Student
Booked passenger CSE11 Student successfully.
> print
0        | F | Paul Cao 
1        | B |
2        | B | Mia Minnes
3        | E |
4        | E |
5        | E |
6        | E | CSE11 Student
7        | E | Joe Politz
8        | E | Niema Moshiri
9        | E |
> cancel Joe Politz
Canceled passenger Joe Politz's booking successfully.
> availabletickets
First Class: 0
Business Class: 1
Economy Class: 5
> upgrade F CSE11 Student
Could not upgrade passenger CSE11 Student to First Class.
> upgrade B CSE11 Student
Upgraded passenger CSE11 Student to Business Class successfully.
> print
0        | F | Paul Cao 
1        | B | CSE11 Student
2        | B | Mia Minnes
3        | E |
4        | E |
5        | E |
6        | E |
7        | E |
8        | E | Niema Moshiri
9        | E |
> exit
```
#### Example 2:
```
$ java AirlineReservation otherSample.csv 
Available commands:
- book <travelClass(F/B/E)> <passengerName>
- book <rowNumber> <passengerName>
- cancel <passengerName>
- lookup <passengerName>
- availabletickets
- upgrade <travelClass(F/B)> <passengerName>
- print
- exit
> print
0        | F | Paul 
1        | F |
2        | F |
3        | B |
4        | B | Joe
5        | B |
6        | B |
7        | B | Bob
8        | B |
9        | B |
10       | E | Alice
11       | E |
12       | E |
13       | E |
14       | E |
> book F Katie
Booked passenger Katie successfully.
> print
0        | F | Paul 
1        | F | Katie
2        | F |
3        | B |
4        | B | Joe
5        | B |
6        | B |
7        | B | Bob
8        | B |
9        | B |
10       | E | Alice
11       | E |
12       | E |
13       | E |
14       | E |
> book F Jimmy
Booked passenger Jimmy successfully.
> print
0        | F | Paul 
1        | F | Katie
2        | F | Jimmy
3        | B |
4        | B | Joe
5        | B |
6        | B |
7        | B | Bob
8        | B |
9        | B |
10       | E | Alice
11       | E |
12       | E |
13       | E |
14       | E |
> book F Greg
Could not book passenger Greg.
> upgrade F Paul 
Could not upgrade passenger Paul to First Class.
> exit
```


### Style [5 Points]
Coding style is an important part of ensuring readability and maintainability of your code. We will grade your code style in all submitted code files according to the style guidelines. Namely, there are a few things you must have in each file/class/method:

1. File header
2. Class header
3. Method header(s)
4. Inline comments
5. Proper indentation
6. Descriptive variable names
7. No magic numbers
8. Reasonably short methods (if you have implemented each method according to specification in this write-up, you’re fine). This is not enforced as strictly.
9. Lines shorter than 80 characters
10. Javadoc conventions (@param, @return tags, /** comments */, etc.)

A full style guide can be found [here](https://github.com/CaoAssignments/style-guide/blob/main/README.md) and a sample styled file can be found [here](https://github.com/CaoAssignments/style-guide/blob/main/SampleFile.java). If you need any clarifications, feel free to ask on Piazza.

## Testing
The Airline Reservation System can be run using the following command:
```
java AirlineReservation <filename>
```

The program takes in **exactly one** command-line argument (`argv[0]`), which is expected to be the filename (more specifically, the file path) to your input CSV file. This CSV file is used to initalize passenger bookings in the plane. For example, if you made a new CSV file called `customLoad.csv`, then you would run `java AirlineReservation customLoad.csv` in your terminal.

You are highly encouraged to make your own CSV files to test your functions. Please use the format as described under the [Input](https://github.com/CaoAssignments/cse11-fa22-pa4-airline-reservation-starter#Input) section when creating your files. Simply create a new file, then save it as `<filename>.csv` under the same folder as your `AirlineReservation.java` file.

***For this programming assignment, all test cases are visible on Gradescope.***

Start early, start often, and good luck on your midterms!
## Submission
Submit the following file(s) to Gradescope by **Thursday, Oct 27 @ 11:59PM PDT**.
 - AirlineReservation.java


Even if your code does not pass all the tests, you will still be able to submit your work to receive partial points for the tests that you passed. Make sure your code compiles in order to receive partial credit.


