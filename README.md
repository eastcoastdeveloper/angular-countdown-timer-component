# Angular Countdown Timer Comonent

Angular Countdown Timer — Technical Documentation
Overview

This document explains the architecture and implementation details of the Angular Countdown Timer component, including:

Template structure

DOM access via ViewChild

Time calculations

Update loop and lifecycle timing

Data flow between component logic and the DOM

```
<div class="count-down-timer">
  <p>Countdown to {{ currentTime }}</p>

  <div class="wrapper">
    <div class="description">
      <p>Days</p>
      <p>Hours</p>
      <p>Minutes</p>
      <p>Seconds</p>
    </div>

    <div class="times">
      <p #days></p>
      <p #hours></p>
      <p #minutes></p>
      <p #seconds></p>
    </div>
  </div>
</div>
```

Key Points

```
{{ currentTime }} displays the human-readable target date.
```

Template reference variables (#days, #hours, etc.) enable direct DOM manipulation using @ViewChild.

2. Component Logic (app.component.ts):
```
import { AfterViewInit, Component, ElementRef, ViewChild } from '@angular/core';

@Component({
  selector: 'my-app',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.scss'],
})

export class AppComponent implements AfterViewInit {
  date: any;
  now: any;
  targetDate: any = new Date(2026, 11, 31);
  targetTime: any = this.targetDate.getTime();
  difference: number;

  months: Array<string> = [
    'January','February','March','April','May','June',
    'July','August','September','October','November','December',
  ];

  currentTime: any = `${
    this.months[this.targetDate.getMonth()]
  } ${this.targetDate.getDate()}, ${this.targetDate.getFullYear()}`;

  @ViewChild('days', { static: true }) days: ElementRef;
  @ViewChild('hours', { static: true }) hours: ElementRef;
  @ViewChild('minutes', { static: true }) minutes: ElementRef;
  @ViewChild('seconds', { static: true }) seconds: ElementRef;

  ngAfterViewInit() {
    setInterval(() => {
      this.tickTock();
      this.difference = this.targetTime - this.now;
      this.difference = this.difference / (1000 * 60 * 60 * 24);

      !isNaN(this.days.nativeElement.innerText)
        ? (this.days.nativeElement.innerText = Math.floor(this.difference))
        : (this.days.nativeElement.innerHTML = `<img src="https://i.gifer.com/VAyR.gif" />`);
    }, 1000);
  }

  tickTock() {
    this.date = new Date();
    this.now = this.date.getTime();

    this.days.nativeElement.innerText = Math.floor(this.difference);
    this.hours.nativeElement.innerText = 23 - this.date.getHours();
    this.minutes.nativeElement.innerText = 60 - this.date.getMinutes();
    this.seconds.nativeElement.innerText = 60 - this.date.getSeconds();
  }
}
```

3. Target Date and Display Formatting
Target Date Initialization
```
targetDate = new Date(2026, 11, 31);
targetTime = this.targetDate.getTime();
```
JavaScript months are 0-indexed (11 = December).

targetTime stores the timestamp in milliseconds.

Human-Readable Date

currentTime = "December 31, 2026";

This value is rendered in the template.

4. DOM Access Using @ViewChild
```
@ViewChild('days') days: ElementRef;
@ViewChild('hours') hours: ElementRef;
@ViewChild('minutes') minutes: ElementRef;
@ViewChild('seconds') seconds: ElementRef;
```

| Template Ref | DOM Updates      | Purpose                  |
| ------------ | ---------------- | ------------------------ |
| `#days`      | `innerText`      | Shows remaining days     |
| `#hours`     | `innerText`      | Hours left today         |
| `#minutes`   | `innerText`      | Minutes left this hour   |
| `#seconds`   | `innerText`      | Seconds left this minute |

5. Update Cycle (ngAfterViewInit())

Executed after the view initializes:
```
setInterval(() => {
  this.tickTock();
  this.difference = (this.targetTime - this.now) / (1000 * 60 * 60 * 24);
  this.days.nativeElement.innerText = Math.floor(this.difference);
}, 1000);
```

Calls tickTock() every second.

Computes total days remaining.

Updates the DOM with new values.

6. Time Calculation Logic (tickTock())
```
tickTock() {
  this.date = new Date();
  this.now = this.date.getTime();

  this.days.nativeElement.innerText = Math.floor(this.difference);
  this.hours.nativeElement.innerText = 23 - this.date.getHours();
  this.minutes.nativeElement.innerText = 60 - this.date.getMinutes();
  this.seconds.nativeElement.innerText = 60 - this.date.getSeconds();
}
```

Breakdown
| Unit    | Calculation              | Explanation                                |
| ------- | ------------------------ | ------------------------------------------ |
| Days    | `Math.floor(difference)` | Derived from total milliseconds difference |
| Hours   | `23 - currentHour`       | Remaining hours today                      |
| Minutes | `60 - currentMinutes`    | Remaining minutes this hour                |
| Seconds | `60 - currentSeconds`    | Remaining seconds this minute              |


7. Summary

The countdown timer calculates the difference between the current time and a future target date.

It updates DOM values every second via setInterval inside ngAfterViewInit().

Uses @ViewChild to directly modify DOM elements.

Displays days, hours, minutes, and seconds remaining with a clean UI.
