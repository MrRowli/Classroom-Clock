# Classroom-Clock
a DIY digital clock designed for and by teachers


[jdeboi.com](http://jdeboi.com/)  
[Instructable](http://www.instructables.com/editInstructable/edit/E8J84XOION6POZY/)


## Editing Schedule

This clock is currently set up for 12-hour, [rotating block schedule] (https://github.com/jdeboi/Classroom-Clock/blob/master/US%20Schedule%20grid.pdf). If you develop a 24-hour clock, please let me know! Would love to include a link!

To add your own schedule, there are a number of changes. Look for the following section of code:

```c++
/////////////////////////////////////////////////////////
/////////////////////////////////////////////////////////
// EDIT THESE
/////////////////////////////////////////////////////////
uint8_t currentBlock = H_BLOCK;   // setup for a rotating block schedule

const uint8_t numPeriods = 7;     // this number should match the number
                                  // of entries in classPeriods[]

uint8_t classPeriods[numPeriods][4] = {
  // use 24 hour clock numbers even though this clock is a 12 hour clock
  // {starting hour, start min, end hour, end min}
  {8, 0, 9, 0},       // Period 0: 8 - 9
  {9, 3, 9, 48},      // Period 1: 9:03 - 9:48
                      // Assembly: 9:48 - 10:18
  {10, 18, 11, 3},    // Period 2: 10:18 - 11:03
  {11, 6, 11, 51},    // Period 3: 11:06 - 11:51
  {11, 54, 12, 39},   // Period 4: 11:54 - 12:39
                      // Lunch: 12:39 - 1:24
  {13, 27, 14, 12},   // Period 5: 1:27 - 2:12
  {14, 15, 15, 0}     // Period 6: 2:15 - 3:00
};
```

Edit the classPeriods array to match your schedule. For example, if your first period starts at 7:45AM and ends at 8:45AM:

```c++

uint8_t classPeriods[numPeriods][4] = {
  // use 24 hour clock numbers even though this clock is a 12 hour clock
  // {starting hour, start min, end hour, end min}
  {7, 45, 8, 45},       // Period 0: 7:45AM - 8:45AM

```

classPeriods[] should only include periods for academic classes- not homeroom, lunch, advisory, club, etc. Those time blocks should be entered into the "otherBlocks" which we'll talk about in a second. Separating academic classes from other time blocks is important in order to ensure the rotating block letter updates appropriately.

Once you've entered all of your relevant classPeriods[], count up the number of entries (should be the number of class periods), and set "numPeriods" to this value:

```c++
const uint8_t numPeriods = 7;
```

All other time blocks should be entered into numOtherBlocks array:

```c++
const uint8_t numOtherBlocks = 2;   // this number should match the number
                                    // of entries in otherBlocks[]

uint8_t otherBlocks[numOtherBlocks][4] = {
  {9, 48, 10, 18},   // assembly: 9:48 - 10:18
  {12, 39, 13, 24}  // Lunch: 12:39 - 1:24
};

```

If you'd like to add another "other block", such as a homeroom:

```c++
const uint8_t numOtherBlocks = 3;   // this number should match the number
                                    // of entries in otherBlocks[]

uint8_t otherBlocks[numOtherBlocks][4] = {
  {9, 48, 10, 18},   // assembly: 9:48 - 10:18
  {12, 39, 13, 24},  // Lunch: 12:39 - 1:24
  {8, 15, 8, 20}     // homeroom: 8:15 - 8:20AM
};

const uint8_t assemblyBlock = 0;
const uint8_t lunchBlock = 1; 
const uint8_t homeroomBlock = 2;       // adding this
```
If your school doesn't begin with an academic class (like a homeroom), you will need to change the "boolean isBeforeSchool()" function. IF it starts with the homeroom block from the previous example:

```c++
boolean isBeforeSchool() {
  // You may need to edit these values if school begins with a homeroom or
  // otherBlock[] that isn't a classperiod[]
  return (now.hour() <= otherBlocks[homeroomBlock][0] && now.minute() < otherBlocks[homeroomBlock][1]);
}
```
There's probably other code editing you'll have to do for these types of schedule modifications. Please let me know (or submit pull requests) as your run into problems specific to your use case.


## Editing Clock Display
The other main section of code that you'll have to edit is the display clock section:

```c++
void displayClock() {
  if(isEndOfDay()) nextDay();
  else if (isWeekend()) pulseClock(Wheel(250),10);
  else if (isBeforeSchool()) colorClock(Wheel(240));
  else if (isAfterSchool()) pulseClock(Wheel(100),5);
  else if (isEndFlash()) countdownClock(); 
  else if (isLunch()) pulseClock(Wheel(100),5);
  else if (isAssembly()) rainbowClock(5);
  else if (isDuringClass()) gradientClock();
  else colorClock(Wheel(20));
}
```

To get rid of the countdown timer triggered at the end of the class, comment out ("//") the following line:

```c++
//else if (isEndFlash()) countdownClock(); 
```

