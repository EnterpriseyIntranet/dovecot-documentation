.. _quota_clone_plugin:

==================
Quota Clone Plugin
==================

.. versionadded:: v2.2.17

Quota clone plugin is useful when you want to store everybody's current quota
usage to a database, but you don't want to use the database as the
authoritative quota database. For example you might want to access everybody's
quota via Redis (or with SQL database, Redis is example here), but you don't
store the Redis database permanently so it could become empty once in a while.
In this case you can use e.g. `dict-file
<https://wiki.dovecot.org/Quota/Dict>`_ or `count
<https://wiki.dovecot.org/Quota/Count>`_ as the authoritative quota database
and make a copy of the quota usage to Redis. From Redis you could then once in
a while gather everybody's current quota usage and send it to yet another place
(e.g. for statistics handling).

Every time quota is updated, the value is updated to the cloned dict. There are
race conditions with it so the quota may not always be 100% correct. The old
value is always replaced with the new one though (not just
incremented/decremented) so the cloned quota is never too much wrong.

The keys that are written to:

* priv/quota/storage - storage usage in bytes
* priv/quota/messages - count of messages

Configuration
=============

.. code-block:: none

  mail_plugins = $mail_plugins quota quota_clone
  plugin {
    quota_clone_dict = redis:host=127.0.0.1:port=6379
  }


More complex example using SQL:

.. code-block:: none

   dict {
     mysql = mysql:/etc/dovecot/dovecot-dict-sql.conf.ext
   }

   plugin {
      quota_clone_dict = proxy::mysql
   }

