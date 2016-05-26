# 3. Final Results/Summary

### Executive Summary (TL;DR)

As detailed in the previous section, we used a variety of strategies and tools to help identify bottlenecks and improve site performance -- both real and "perceived" performance. By employing all of the recommended additions and modifications, we were able to see a drastic decrease in page load time, and came very close to the business goal of "under 500ms for a page load", depending on which metric/tool is doing the reporting. 



### Siege

<pre><code>
Transactions:		          50 hits
Availability:		         100.00 %
Elapsed time:		           2.84 secs
Data transferred:	           0.44 MB
Response time:		          0.01 secs
Transaction rate:	          17.61 trans/sec
Throughput:		             0.15 MB/sec
Concurrency:		            0.20
Successful transactions:       50
Failed transactions:	        0
Longest transaction:	        0.04
Shortest transaction:	       0.00
</code></pre>

Note that siege is reporting that our longest transaction is 0.04s (40ms) -- a major improvement over our original longest transaction of 1.4s! (1400ms).

### ApacheBench



