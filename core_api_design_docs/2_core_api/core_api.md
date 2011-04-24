!SLIDE subsection transition=fade

# The Core API #

!SLIDE bullets

# The Core API #

* Server
* Databases
* Documents
* Replication


!SLIDE commandline incremental

# Server #

	$ curl http://127.0.0.1:5984/

	{"couchdb":"Welcome","version":"1.0.1"}

!SLIDE commandline incremental

# Databases #

	$ curl -X PUT http://127.0.0.1:5984/albums

	{"ok":true}

	$ curl -X PUT http://127.0.0.1:5984/albums

	{"error":"file_exists","reason":"The database could not be
		created, the file already exists."}

	< HTTP/1.1 201 Created

	$ curl -vX DELETE http://127.0.0.1:5984/albums-backup

!SLIDE bullets incremental

# Documents #

* real-world document
* JSON format to store documents
* each document has an ID (unique per database)

!SLIDE smaller commandline incremental

# Documents #

	$ curl -X GET http://127.0.0.1:5984/_uuids

	{"uuids":["6e1295ed6c29495e54cc05947f18c8af"]}

	$ curl -X PUT http://127.0.0.1:5984/albums/6e1295ed6c29495e54cc05947f18c8af \
	 -d '{"title":"There is Nothing Left to Lose","artist":"Foo Fighters"}'

	{"ok":true,"id":"6e1295ed6c29495e54cc05947f18c8af","rev":"1-2902191555"}

!SLIDE commandline incremental smaller

# Documents #

	$ curl -X GET http://127.0.0.1:5984/albums/6e1295ed6c29495e54cc05947f18c8af

	{"_id":"6e1295ed6c29495e54cc05947f18c8af",
			"_rev":"1-2902191555",
			"title":"There is Nothing Left to Lose",
			"artist":"Foo Fighters"}

!SLIDE bullets incremental

# Documents / Revisions #

* load the full document, save the entire new revision (_rev)
* should include _rev to update or delete a document
* overwrite data you didn’t know existed

!SLIDE commandline incremental smaller

# Documents / Revision #

	$ curl -X PUT http://127.0.0.1:5984/albums/6e1295ed6c29495e54cc05947f18c8af \
		-d '{"title":"There is Nothing Left to Lose","artist":"Foo Fighters","year":"1997"}'

	{"error":"conflict","reason":"Document update conflict."}

	$ curl -X PUT http://127.0.0.1:5984/albums/6e1295ed6c29495e54cc05947f18c8af \
		-d '{"_rev":"1-2902191555","title":"There is Nothing Left to Lose",
		"artist":"Foo Fighters","year":"1997"}'

	{"ok":true,"id":"6e1295ed6c29495e54cc05947f18c8af","rev":"2-2739352689"}

!SLIDE commandline incremental smaller

# Documents / Attachments #

	$ curl -vX PUT http://127.0.0.1:5984/albums/6e1295ed6c29495e54cc05947f18c8af/ \
	 artwork.jpg?rev=2-2739352689 --data-binary @artwork.jpg -H "Content-Type: image/jpg"

	$curl http://127.0.0.1:5984/albums/6e1295ed6c29495e54cc05947f18c8af

	{"_id":"6e1295ed6c29495e54cc05947f18c8af","_rev":"3-131533518","title":
	"There is Nothing Left to Lose","artist":"Foo Fighters","year":"1997","_attachments":
	{"artwork.jpg":{"stub":true,"content_type":"image/jpg","length":52450}}}

	?attachments=true HTTP option

!SLIDE commandline incremental smaller

# Replication #

	$ curl -vX POST http://127.0.0.1:5984/_replicate \
		-d '{"source":"albums","target":"albums-replica"}'

	push replication
	$ curl -vX POST http://127.0.0.1:5984/_replicate \
		-d '{"source":"albums","target":"http://127.0.0.1:5984/albums-replica"}'

	pull replication
	$ curl -vX POST http://127.0.0.1:5984/_replicate \
		-d '{"source":"http://127.0.0.1:5984/albums-replica","target":"albums"}'

	remote replication
	$curl -vX POST http://127.0.0.1:5984/_replicate \
		-d '{"source":"http://127.0.0.1:5984/albums",
		"target":"http://127.0.0.1:5984/albums-replica"}'

!SLIDE bullets incremental

# Replication #

* "create_target":true implicitly creates the target database if it doesn’t exist
* CouchDB maintains a session history of replications
* request for replication will stay open until replication closes
* replicates the database only as it was at the point in time when replication was started
* replication API not RESTful