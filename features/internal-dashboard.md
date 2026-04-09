1. For the sparklines they should look better now, they'll match the data and how it's aggregated for the chart, so if you do 90 days it should be a lot smoother now.

2. I've set the access token expiry to now be 15 minutes, so you can do a refresh less frequently now. The main token will expire after 1 day, for production it will be 30 days.

3. I've implemented more API endpoints, the main one being the performance comparison chart using route `admin/account/performance/chart` that will now work and return live data from staging database.




---

mobile menu height
