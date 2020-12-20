---
layout: post
title:  "Oracle Database on a Mac"
date:   2020-10-31
categories: MacOS Oracle PL/SQL
---
I am facing a task of installing Oracle Database locally on my Mac, so I am doing a bit of research.

It looks like the only supported operating systems are Linux and Windows.

However one way to do it on a Mac would be to install it as a virtual machine or a Docker container.

Just google "Oracle Container Registry" (I am not providing the link because it seems it could be changing), from there select "Database" - and you will get the list of official Docker containers provided by Oracle with various editions.

It seems like the best one for the developer's machine would be the "standard" edition. It does have limitations like no more than one database, etc. - but this is still plenty for developer purposes. Also Oracle explicitly states on this page that this **is** intended to be used by developers.

I didn't install it yet, and I will share my experience once I do.

However I do want to point out some useful queries for Oracle once it is installed, for a developer who just needs to use the database and is not a database administrator.

Get list of all tables in user's schema:

```sql
SELECT * FROM user_tables;
```

Get list of indexes in user's schema:

```sql
SELECT * FROM user_indexes;
```

Get list of all sequences in user's schema:

```sql
SELECT * FROM user_sequences;
```

Get list of all views in user's schema:

```sql
SELECT * FROM user_views;
```

The next one I found very useful, and attribution goes to Rafael Eyng. Delete all user objects from user's schema:

```sql
BEGIN
   FOR cur_rec IN (SELECT object_name, object_type
                     FROM user_objects
                    WHERE object_type IN
                             ('TABLE',
                              'VIEW',
                              'PACKAGE',
                              'PROCEDURE',
                              'FUNCTION',
                              'SEQUENCE',
                              'SYNONYM'
                             ))
   LOOP
      BEGIN
         IF cur_rec.object_type = 'TABLE'
         THEN
            EXECUTE IMMEDIATE    'DROP '
                              || cur_rec.object_type
                              || ' "'
                              || cur_rec.object_name
                              || '" CASCADE CONSTRAINTS';
         ELSE
            EXECUTE IMMEDIATE    'DROP '
                              || cur_rec.object_type
                              || ' "'
                              || cur_rec.object_name
                              || '"';
         END IF;
      EXCEPTION
         WHEN OTHERS
         THEN
            DBMS_OUTPUT.put_line (   'FAILED: DROP '
                                  || cur_rec.object_type
                                  || ' "'
                                  || cur_rec.object_name
                                  || '"'
                                 );
      END;
   END LOOP;
END;
/
```