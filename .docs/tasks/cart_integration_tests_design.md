LOAD-TEST:

- seed/clean data before/after: using admin product api
- simulate concurrent users: randomly increase/decrease items quantity or remove items.

- verify:
	+ reserved total amounts <= the stocks
	+ all reservation entries are in correct state (reserved amounts, desired amounts, expiriations are match with statuses)
	+ no zero amount reservations
	+ GET user cart api must work correctly: cart items match reservation entries

- summary:
	+ load balancing: total requests that each server has handled	
	+ total runtime (includes verification duration)

SENERIOS TESTS:

- verify special cases for some user cart:
  + expiration of entries will automatically be reset on the next request
  + insufficient-stock entries will eventually be reconciled when the other reservations expire and the availabilities increase