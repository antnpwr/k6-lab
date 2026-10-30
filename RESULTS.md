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