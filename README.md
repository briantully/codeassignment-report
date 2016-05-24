# Code Assignment Performance Report

This report summarizes my findings and recommended changes to improve site performance, as proposed in the following request:

> Using the following information, prepare a short report of recommended changes (code or config) to the site to improve its performance, while still meeting the business needs.

> A customer has run into performance issues with a simple link sharing site.

> The site aggregates content from popular websites and displays the content in a standard news site fashion. The site’s main feature is adjusting the text of the incoming feeds by replacing words or phrases. A voting feature is provided to help the community feature the best content. 

> The performance problem revolves around pages loading slowly when the site is under heavy load. Problems have been reported after cron runs, which is every 5 minutes to import new content, and after code deploys which clear all Drupal caches.




### Project Scope/Goals - Initial Questions and Responses

What is the ratio between anonymous and authenticated users, and which is the site’s primary focus?

> 99% anonymous users. The site is a traditional news sites, users can not register only editors.

Has an SLA been defined whereby there is a minimum/maximum amount of time before users can see “latest” content vs. cached content?

> SLA for latest content should be 5 minutes, inline with the imports.

Has an SLA been established that defines acceptance criteria for expected page load times and number of concurrent users?

> SLA for page load time should be less than 500ms. (Consider number of concurrent users out of scope for this, only looking at performance issues rather then scalability issues.)

Assuming that the latest, up-to-date content is a requirement, has there been any exploration/consideration of a reverse proxy like Varnish, which can help alleviate slow loading when the site is under heavy load and/or when Drupal cache has been cleared. 

> We'll assume the production site is hosted on Acquia Cloud, which has Varnish provided by default. The local instance you have access to is only code and database, so consider the actual Varnish configuration file out of scope (but Drupal settings that interact with Varnish can be in scope.)

You mentioned cron is running every 5 minutes to import new content. I’m hoping that cron is running as an external process (or through drush) vs. within Drupal (i.e., in Drupal admin config, make sure it’s turned off). Otherwise, the cron jobs run by Drupal wind up using valuable Apache resources and are actually triggered by a user page request — so that user gets a very delayed response while cron waits to finish all its jobs.

> Though not in the ZIP file, assume the 5 minute cron job is properly configured via drush.

Because of the frequent importing of new content, the site is very “write-heavy” on the database, and doesn’t lend itself to database optimization techniques to boost/promote query cache, especially with Drupal default InnoDB tables. Has there been any exploration/consideration of utilizing a cache backend in front of the database (e.g., Memcached, Redis) and/or moving some of the most frequent “writes” into a separate key/value store if appropriate?

>  No investigation has been done to use an external cache backend. (i.e. recommendations are welcome.)

Let me know if it’s outside the scope of this assignment, but will I have access to the configuration files for the deployment environment (php.ini, my.cnf, http.conf, etc.) and information on the CPU, specifically RAM?


> Though during normal load testing we do ensure the servers are sized correctly, consider it out of scope for this.
