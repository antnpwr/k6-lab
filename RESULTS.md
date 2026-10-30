# Step 0.2
- 75 requests at 3.7 req/s with a p(95) of 336.5 ms
- The rate stays below 5 requests per second because we see in the execution output that one iteration takes 1.35s, so 5 seconds with 5 vus in parallel where each take 1.35 will never be 5 but instead around 3.7 req/s. 1 / 1.35=0.74 iterations per second -> 5 vus * 0.74 = 3.7 req/s