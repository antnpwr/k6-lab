# K6 lab results

## Part 0

### Step 0.1
- http_req_duration = 308.43ms

### Step 0.2
- 75 requests at 3.7 req/s with a p(95) of 336.5 ms
- The rate stays below 5 requests per second because we see in the execution output that one iteration takes about 1.35s (about 0.3 for req, 1 for sleep), so 1 vus makes 1/1.35 req/s, and 5 vus makes 5*0.74 = 3.7 req/s. So it is impossible to reach 5 req/s when each iteration is more than 1 second.

### Step 0.3
- 20 requests at 3.3 req/s with a p(95) of 296.5ms

### Step 0.4
- 3 requests at 1.1 req/s with a p(95) of 405.2ms

### Step 0.5
- 1 requests at 0.4 req/s with a p(95) of 289.8ms

### Step 0.6
- Default run: 
    - checks = passed 'rate>0.99' rate=100.00%
    - http_req_duration = passed 'p(95)<1500' p(95)=418.24ms
    - http_req_failed = passed 'rate<0.01' rate=0.00%
    - exit code = 0
- Stricter rule (-e P95=300): 
    - checks = passed 'rate>0.99' rate=100.00%
    - http_req_duration = failed 'p(95)<300' p(95)=393.84ms
    - http_req_failed = passed 'rate<0.01' rate=0.00%
    - exit code = 99

### Step 0.7 before
- http_req_duration 
    - passed 'p(95)<1000' p(95)=589.46ms
    - passed 'p(99)<1500' p(99)=831.49ms
- http_req_failed
    - passed 'rate<0.01' rate=0.00%
- exit code = 0

### Step 0.7 after
- http_req_duration
    - failed 'p(95)<300' p(95)=582.21ms
    - passed 'p(99)<1500' p(99)=626.59ms
- http_req_failed
    - passed 'rate<0.01' rate=0.00%
- exit code = 99

### Step 0.8 before fix
- passed status is 200
- failed no pepperoni -> 85% — ✓ 61 / ✗ 10
- failed no knife needed -> 64% — ✓ 46 / ✗ 25

### Step 0.8 after fix
- passed status is 200
- passed no pepperoni
- passed no knife needed

- The reason why it failed is because the "pepperoni" and "knife" is not exactly the same as the one from the list we explore. The explore list shows that it is "Pepperoni" and "Knife". The part that is not the same is the capital letters. Since computer compare 'P' and 'p' or "K" and "k" as different, it did not find the exact matched word to exclude (it probably random the pizzas), therefore the result did not exclude as we want making it failed.
- Manual request might not have found the bug since there is a higher chance that manual might get the correct pizza than the wrong one. However if we check under load then we have many sample to look at and we can see the sample that gets the wrong pizza. Manual might be lucky, but if we do it many times then the bugs is more easy to be seen with that many samples. 


## Part A results 
| Step | Test type    | Users | Req/s | p(95)     | Max       | Failed | Checks  |
| ---- | ------------ | ----- | ----- | --------- | --------- | ------ | ------- |
| A1   | Smoke        | 1     | 0.48  | 665.72 ms | 713.84 ms | 0.00%  | 100.00% |
| A2   | Average load | 3     | 1.28  | 554.88 ms | 648.11 ms | 0.00%  | 100.00% |
| A3   | Stress       | 5     | 1.95  | 614.84 ms | 7.75 s    | 0.00%  | 100.00% |
| A4   | Spike        | 5     | 1.02  | 396.89 ms | 922.85 ms | 0.00%  | 100.00% |
| A5   | Soak         | 2     | 0.97  | 389.88 ms | 464.29 ms | 0.00%  | 100.00% |

- Comparing p(95) of A2 with A3: I actually run A3 twice, the first time it failed 2 thresholds with exit code 99, but the second/rerun passed all thresholds (it may be my network connection). But from the table, we can see that the p(95) is a little higher in A3 compared to A2
- A4 spike: The spike happen around 21:04:09 to 21:04:59. During the spike, the response time rise to the highest at around 21:04:49 but then quickly drop back to normal in 10s, right where the user drop to 1.
- A5 soak: No, in my case it does not worsen for both the response time and the error rate. It is still in the same range of around 320-420 all throughout the whole time.

## Part B
1. Done, see the table above.

2. The rate stays below 5 requests per second because we see in the execution output that one iteration takes about 1.35s (about 0.3 for req, 1 for sleep), so 1 vus makes 1/1.35 req/s, and 5 vus makes 5*0.74 = 3.7 req/s. So it is impossible to reach 5 req/s when each iteration is more than 1 second.

3. No, not much. It did grow a little bit but not much. P(95) went from 554.88 ms (A2) to 614.84 ms (A3). This is because QuickPizza is build to be able to handle a lot more users, so it still workes well inside the operational envelope. Adding just a few more users didn't really affect much, it is still far inside the envelope, we didn't see where it might be slow.

4. It takes around 10 seconds in A4 to return back to normal after the spike.

5. No, in my case it does not worsen for both the response time and the error rate. It is still in the same range of around 320-420 all throughout the whole time. 10 minutes prabably cannot show bug compare to a real soak test that last an hour, so a real one could show some problem like memory leak and being slow.

6. P(95) means 95th percentile of response time, so 95% of req are as fast as or faster than the number. And the average may not be accurate due to some extreme number and makes it not so real. 

7. First is because there could be noises or internet issues that can occur randomly and messed up with the output (i have that multiple times but i rerun them). Second is probably the amount of public users using it at the same time, while in the lab there may be a limit amount of users where the result from the test could be more consistent.

8. Because it might overload the site, as it is a public site, there will be other people that use it and testing with hundreds of users will affect other people trying to use the site at the same time. And it might also affect the owner of the site for having to handle the crash too.

9. Vague example: message in Line should send quickly. Testable requirement: with 100 users sending messages at the same time for 10 minutes, the send-message API answers 95% of requests within 500 ms and 99% within 1 second, and fewer than 1% of requests fail.
