
# Discovery/Recommendations


## xhprof Profiling


The use of the xhprof profiler did not reveal anything obvious. There were significant calls to the database for cache getting/setting (even with cache disabled), so this promotes the idea of using an external cache like Memcached to offload these calls from the database and improve performance.

**RECOMMENDATION: Install/configure Memcached
**


## Percona Toolkit / MySQL Slow Query Log


The MySQL slow query log was enabled in order to highlight any potential troublesome queries. Originally run with the default 1s limit, this produced no entries -- even with repeated cache clears and cron runs and using apachebench and siege to send many requests to the website. We reduced the limit down to 50ms and then 5ms before we started seeing entries. All queries were run through MySQL's EXPLAIN function to ensure they were using proper indexes and were optimized. 

**RECOMMENDATION: No obvious optimizations discovered at this time
**

## Code Refactoring

All core and contributed code looked up to date and working reliably. However, the **ca_rewrite module** has a potential optimization that should improve performance and cachebility. Currently the module uses hook_node_view_alter() to modify a node's title based on custom rewrite patterns. It modifies the title only on the render side, i.e., as a filter that gets called after the node is loaded and before the node is displayed. The concern is that for each view containing a list of nodes, for each node the view loads it then needs to call the database to get a list of rewrite patterns and apply them to each node in the view. On pages like the home page this gets expensive when you consider there are multiple (3) views. 

<pre><code style="language-php">
function ca_rewrite_node_view_alter(&$build) {
  // Node title is not run through text formats, so replace contents here.
  $build['#node']->title = ca_rewrite_filter_process($build['#node']->title);
}
</code></pre>

I experimented with this and refactored the code to use hook_node_presave() instead. The rationale is that the title filtering would only happen once when the node is created via feed import, rather than being run on every node on EVERY page view.

<pre><code style="language-php">
function ca_rewrite_node_presave($node) {
  if ($node->type == 'feed_item') {
    // Alter title, run it through rewrite patterns:
    $node->title = ca_rewrite_filter_process($node->title);
  }
}
</code></pre>


While the performance gains weren't as obvious as I'd hoped, there was a noticeable improvement of about 500ms on average (see chart from WebPageTest comparison below).

![](ca_rewrite-changes.png)



