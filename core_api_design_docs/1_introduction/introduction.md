!SLIDE
# The Core API / Design Documents #

!SLIDE bullets incremental
# CouchDB #
* schema-free document model
* query engine
* design

!SLIDE bullets incremental transition=fade
# Eventual Consistency #

* thin wrapper around the database core
* taking a closer look at the structure, to better understanding the API

!SLIDE bullets incremental
# The Key to Your Data #

* B-tree storage engine
* allows searches, insertions, and deletions in logarithmic time
* MapReduce produce key/value pairs, inserted into the B-tree, sorted by key
* documents and view results accessed by key or key range

!SLIDE
# Running a Query Using MapReduce #

### couchdb book ###

.notes Map functions are called once with each document as the argument. The function can choose to skip the document altogether or emit one or more view rows as key/value pairs. Map functions may not depend on any information outside of the document. This independence is what allows CouchDB views to be generated incrementally and in parallel.
CouchDB views are stored as rows that are kept sorted by key. This makes retrieving data from a range of keys efficient even when there are thousands or millions of rows. When writing CouchDB map functions, your primary goal is to build an index that stores related data under nearby keys.

!SLIDE bullets incremental
# No Locking #

* Multi-Version Concurrency Control (MVCC)
* documents are versioned
* create entire new versions of documents
* no need to wait for read requests to finish

!SLIDE bullets incremental
# Incremental Replication #

* document changes periodically copied between servers
* automatic conflict detection and resolution
* winning version is saved as the most recent version
* previous version in the document’s history
* both databases will make exactly the same choice