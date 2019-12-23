.. _configuration:

#############
Configuration
#############

As part of the installation of XTools, ``composer install`` or ``composer update`` may prompt you for configuration options.

Databases
=========

XTools' own database:

- **database_host** - Hostname for the server with the XTools database.
- **database_port** - Port for the server with the XTools database.
- **database_name** - Database name of the XTools database.
- **database_user** - Username for the XTools database.
- **database_password** - Password for the user for the XTools database

The projects' databases:

- **database_replica_host** - Hostname for the server with the MediaWiki databases. If you are :ref:`developing against the WMF replicas <development_wmf_replicas>` through an SSH tunnel, the value should probably be ``127.0.0.1``.
- **database_replica_port** - Port for the server with the MediaWiki databases. If you are developing against the WMF replicas through an SSH tunnel, the value should the MySQL port that was forwarded.
- **database_replica_name** - Database name of any one of the MediaWiki databases (usually the default, or the 'meta'; it doesn't matter which). For installations connecting to the WMF replicas, this could for example be ``metawiki_p``.
- **database_replica_user** - Username for the MediaWiki databases. If you are developing against the WMF replicas, you should use the credentials specified in the ``replica.my.cnf`` file, located in the home directory of your Toolforge account.
- **database_replica_password** - Password for the user for the MediaWiki databases.

The 'meta' database:

- **database_meta_name** - Database name for the server with the ``wiki`` table (this is not required if ``app.single_wiki`` is set). If connecting to the WMF replicas, the value should be ``meta_p``.
- **database_meta_table** - Name of the ``wiki`` table.  Defaults to ``wiki``.

For WMF installations, you should also specify credentials for the ``tools-db`` database server, which contains data from other tools (e.g. checkwiki_):

- **database_toolsdb_host** - MySQL host name (``127.0.0.1`` if connecting to the replicas via SSH tunnel).
- **database_toolsdb_port** - MySQL port number.
- **database_toolsdb_name** - Username to connect as (should be the same as ``database_replica_user``).
- **database_toolsdb_password** - Password to use for the user (should be the same as ``database_replica_password``).

.. _checkwiki: https://tools.wmflabs.org/checkwiki/

Authentication and Email
========================

OAuth is used to allow users to make unlimited requests, and to allow them to see :ref:`detailed statistics <optin>` of their own account. The credentials need to be requested from ``Special:OAuthConsumerRegistration`` on your default wiki. This requires the `OAuth extension <https://www.mediawiki.org/wiki/Extension:OAuth>`_. If this extension is not installed or you are developing locally, you can leave these options blank.

- **oauth_key** - Oauth consumer key.
- **oauth_secret** - Oauth consumer secret.

Email is only used to send error reports to the maintainers. If you don't care about this (such as in development installations),
you can use the defaults for all mailer options and no emails will be sent. Note the 'from' and 'to' emails must have valid addresses.

- **mailer.transport** - Software for the mailer.
- **mailer.host** - Hostname for the mailer.
- **mailer.user** - Username for the mailer software.
- **mailer.password** - Password for the mailer software.
- **mailer.from_email** - The 'from' email.
- **mailer.to_email** - The 'to' email.

Caching
=======

These options are available if you wish to use a cache provider such as Redis. However, XTools will function well using only the file system for caching.

- **cache.adapter** A cache adapter supported by `DoctrineCacheBundle <https://symfony.com/doc/current/bundles/DoctrineCacheBundle/reference.html>`_. `file_system` is the default and should work well, but we recommend `apcu` for better performance.
- **cache.redis_dsn** The DSN of the Redis server, if ``redis`` is used as the ``cache.adapter``. If you're not using Redis, this parameter can be ignored.

Wiki configuration
==================

The parameters ensures paths to your wiki(s) are properly constructed, and for communicating with the API.

- **wiki_url** - Full URL of the wiki, used only if ``app.single_wiki`` is set to ``true``. The title of pages are attached to the end.
- **api_path** - The API path for the projects, usually ``/w/api.php``.
- **default_project** - The base URL of whatever wiki you consider to be the "default". This will be the default value for the "Project" field in the forms. On the Wikimedia installation, ``en.wikipedia.org`` is used because it is the most popular wiki. For single-wiki installations, the "Project" field in the forms are hidden, but you still need to provide this value for ``default_project``.
- **central_auth_project** - Which wiki should be used for CentralAuth (if applicable), and also for OAuth authentication. You may want this to be the same as the ``default_project``.
- **opted_in** - A list of database names of projects that will display :ref:`restricted statistics <optin>` regardless of individual users' preferences. For developers working off of the replicas, use ``enwiki_p``.

Application
===========

- **secret** - A secret key that's used to generate certain security-related tokens, and as the secret for the internal API. This can be any non-blank value. If you are using a separate API server (as explained in the :ref:`administration <offload_api>` section), this parameter must have the same value on both the app server and API server. `nux.net/secret <http://nux.net/secret>`_ can auto-generate a secure token for you.
- **app.noticeDisplay** - This is used to broadcast a notice at the top of XTools. Set to ``1`` to turn this feature on.
- **app.noticeStyle** - Style of the notice banner, correlating to the `Bootstrap contextual classes <https://getbootstrap.com/docs/3.3/css/#tables-contextual-classes>`_. Available options include ``danger``, ``warning``, ``info`` and ``success``.
- **app.noticeText** - Message shown to the user. If you provide a valid i18n message key, it will display that message instead.
- **app.single_wiki** - Point XTools to a single wiki, instead of using a meta database. This ignores ``database_meta_name`` above.
- **app.is_labs** - Whether XTools lives on the Wikimedia Foundation Cloud Services environment. If you are developing against the WMF replicas through an SSH tunnel, set this to ``true``.
- **app.replag_threshold** - Number of seconds to consider the replicas as "lagged", and show a warning to the user that the data may be out of date. For WMF installations, this parameter is obsolete and can be left blank, as the new replicas do not suffer from noticeable lag.
- **app.query_timeout** Maximum allowed time for queries to run. This is to ensure database quota is not exceeded.
- **app.rate_limit_time** - Used for :ref:`rate limiting <rate_limiting>`. This parameter is the number of minutes during which ``app.rate_limit_count`` requests from the same user are allowed. Set this to ``0`` to disable rate limiting.
- **app.rate_limit_count** - Number of requests from the same user that are allowed during the time frame specified by ``app.rate_limit_time``. Set this to ``0`` to disable rate limiting.
- **app.max_page_revisions** - Set a maximum number of revisions to process for pages. This is to safeguard against unnecessarily consuming too many resources for queries that will most surely timeout. Set this to `0` to disable all limitations.
- **app.max_user_edits** - Querying a user that has more edits than this will be rejected. This is to safeguard against unnecessarily consuming too many resources for queries that will most surely timeout. Set this to `0` to disable all limitations.
- **app.wikiwho.username** - Used to get around rate-limiting of the WikiWho API, which is used by the Authorship feature of the ArticleInfo tool. Leave blank if you're working in a development environment, or are using a non-WMF installation of XTools.
- **app.wikiwho.password** - Used to get around rate-limiting of the WikiWho API, which is used by the Authorship feature of the ArticleInfo tool. Leave blank if you're working in a development environment, or are using a non-WMF installation of XTools.
- **multilingual_wikis** - This should be left blank for any non-WMF installation. This lists the database names of wikis where there is no specific language associated with them, for use in the AutoEdits tool.
- **www_wikis** - This should be left blank for any non-WMF installation. It includes root the domain name for wikis that have noting but 'www' as a possible subdomain (use 'mediawiki' instead of 'www.mediawiki.org'). This is used for converting legacy lang= and wiki= URL parameters.

Tools
=====

Selectively choose which tools to enable within XTools.

- **enable.AdminScore** - Enable "Admin Score" tool.
- **enable.AdminStats** - Enable "Admin Statistics" tool.
- **enable.ArticleInfo** - Enable "Article Information" tool.
- **enable.Authorship** - Enable "Authorship" tool (WMF-only).
- **enable.AutoEdits** - Enable "Automated Edits" tool.
- **enable.Blame** - Enable "Blame" tool (WMF-only).
- **enable.CategoryEdits** - Enable "Category Edits" tool.
- **enable.EditCounter** - Enable "Edit Counter" tool.
- **enable.EditSummary** - Enable "Edit Summaries" tool.
- **enable.GlobalContribs** - Enable the "Global Contributions" tool.
- **enable.Meta** - Enable the Meta tool, which tracks usage of the XTools installation.
- **enable.Pages** - Enable "Pages Created" tool.
- **enable.Quote** - Enable "Quote Database" tool.
- **enable.RfXAnalysis** - Enable "RfX Analysis" tool.
- **enable.RfXVoteCalculator** - Enable "RfX Vote Calculator" tool.
- **enable.SimpleEditCounter** - Enable "Plain, Dirty, Simple Edit Counter" tool.
- **enable.TopEdits** - Enable "Top Edits" tool.
