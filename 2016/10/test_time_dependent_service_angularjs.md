# Test time-dependent service in AngularJS

## Context

Let's say we have an AngularJS service to test, and that service is time dependent.
The use case here is having heart beat messages sent by a backend to ensure the communication to a frontend is alive.
If the frontend does not receive heart beats anymore, it should close the application because of whatever reason.

The trick here is to control the flow of time in our tests.
Creating timeouts and interval in the tests is just not an option.
It would slow down the execution of tests, and the syntax would be too overwhelming.

In order to control the flow of time, we will call a doctor to the rescue!

## Doctor Who?

The `ngMock` module comes with handy functions on `$timeout` and `$interval`.
They are called `flush` on both services.
`flush` takes an optional argument representing a number of milliseconds to move forward.

When we call `flush`, we "jump" into the future.
If no parameter is given to `flush`, we basically say to `$timeout` or `$interval` to go as far as they can.
If a parameter is given, `flush` will execute functions up to the given number of milliseconds.

## Usage

Let's consider the following scenario:

`cb1` is a `$timeout` callback that will be called after 1,000 ms.
`cb2` is an `$interval` callback that will be called three times, each 1,500 ms.

Here is a representation of the above scenario in a time scale:

```
Time (s)        0         1         2         3         4         5
                ├─────────┼────┬────┴─────────┼─────────┴────┬────┴──>
Callback calls            cb1  cb2            cb2            cb2
```

We can see the execution of the callbacks though time.
During our tests, the goal is to control how much the time "flows" to the right direction.
All being done with a sequential set of instructions, without the need of delaying Acts and Asserts within out tests.

In the above example, we use both `$timeout` and `$interval`.
That means we will use both flushes to control the execution of our callbacks.
Since `cb1` and `cb2` are independent (i.e. one is not declared within the other), we can trigger their execution in any order.

Consider the following pseudo test code:

```javascript
$timeout.flush(1000);
$interval.flush(2000);
```

The `cb1` has been called because we moved the `$timeout` clock one second in the future.
It was enough to trigger the first callback.
By moving the `$interval` clock two seconds in the future, the second callback has been called once.

To trigger the second callback another time, the `$interval` clock must be at three seconds or more.
The thing is that when we flush, we move forward in time for the given amount of milliseconds.
At the moment, `$interval` is at 2,000 ms, to trigger the second callback once more we need to go a 1,000 ms forward.

```javascript
$interval.flush(1000);
```

Here we go! `cb2` has been called a second time.
And if we wanted to trigger it once again, we would need to flush an amount of 1,500 ms.

# Conclusion

AngularJS provides a useful interface to control the execution order of both timeouts and intervals.
It improves speed and allow for a cleaner and shorter tests.

One thing to note is that it might be necessary to mock `Date.now()` function to fake some timestamps.
Flushes sequentially trigger callbacks in the same cycle of the event loop.
So, if time calculation happens relative to current execution time,
it is preferred to use `Date.now()` instead of `new Date.getTime()`.
