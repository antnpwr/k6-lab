## Step 0.2
- 75 requests at 3.7 req/s with a p(95) of 336.5 ms
- The rate stays below 5 requests per second because we see in the execution output that one iteration takes 1.35s, so 5 seconds with 5 vus in parallel where each take 1.35 will never be 5 but instead around 3.7 req/s. 1 / 1.35=0.74 iterations per second -> 5 vus * 0.74 = 3.7 req/s

## Step 0.3
- 20 requests at 3.3 req/s with a p(95) of 296.5ms

## Step 0.4
- 3 requests at 1.1 req/s with a p(95) of 405.2ms

## Step 0.5
- 1 requests at 0.4 req/s with a p(95) of 289.8ms

## Step 0.6
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

## Step 0.7 before
- http_req_duration 
    - passed 'p(95)<1000' p(95)=589.46ms
    - passed 'p(99)<1500' p(99)=831.49ms
- http_req_failed
    - passed 'rate<0.01' rate=0.00%
- exit code = 0

## Step 0.7 after
- http_req_duration
    - failed 'p(95)<300' p(95)=582.21ms
    - passed 'p(99)<1500' p(99)=626.59ms
- http_req_failed
    - passed 'rate<0.01' rate=0.00%
- exit code = 99

## Step 0.8 before fix
- passed status is 200
- failed no pepperoni -> 85% — ✓ 61 / ✗ 10
- failed no knife needed -> 64% — ✓ 46 / ✗ 25

## Step 0.8 after fix
- passed status is 200
- failed no pepperoni -> 85% — ✓ 61 / ✗ 10
- failed no knife needed -> 64% — ✓ 46 / ✗ 25

- The reason why it failed is because the "pepperoni" and "knife" is not exactly the same as the one from the list we explore. The explaore list shows that it is "Pepperoni" and "Knife". The part that is not the same is the capital letters. Since computer compare 'P' and 'p' or "K" and "k" as different, it did not find the exact matched word to exclude (it probably random the pizzas), therefore the result did not exclude as we want making it failed.
- Manual request might not have found the bug since there is a higher chance that manual might get the correct pizza than the wrong one. However if we check under load then we have many sample to look at and we can see the sample that gets the wrong pizza. Manual might be lucky, but if we do it many times then the bugs is more easy to be seen with that many samples. 

## Step A1
- passed home page is 200
- passed quotes are listed
- passed recommendation is 200
- passed pizza has a name