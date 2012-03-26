http://www.mongodb.org/display/DOCS/Replica+Set+Tutorial

	config = {
		_id: 'rs_default',
		members: [
			  {_id: 0, host: 'localhost:27017', priority : 2},
			  {_id: 1, host: 'localhost:27018'},
			  {_id: 2, host: 'localhost:27019', arbiterOnly : true}
		]
	};
	rs.initiate(config);
