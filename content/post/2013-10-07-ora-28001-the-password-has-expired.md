+++
title = "ORA-28001: the password has expired"
date = "2013-10-07T08:44:00+08:00"
+++
That is such a trivial task for an Oracle DBA to periodically change an application user's password. Consider a situation that we forgot to set up an no-expire user profile for an application user, and it was just expired, how can we *change* the user's password to be *unchanged* as well as set its profile to no-expire?

We can have a look on this qeury to check all expired users.
```sql
SELECT username, account_status, expiry_date FROM dba_users;
```
And then set the profile of our user to be password no-expire, in this case we just edit the default user profile, but should be careful.
```sql
ALTER PROFILE DEFAULT LIMIT PASSWORD_LIFE_TIME UNLIMITED;
```
The statement below will generates SQLs to *change* the users' password to be *unchanged*.
```sql
SELECT    'alter user "'
       || d.username
       || '" identified by values '''
       || u.password
       || ''';'
          c
  FROM dba_users d, sys.user$ u
 WHERE d.username = UPPER ('THE_USER_NAME') AND u.user# = d.user_id;
```