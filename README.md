# Promises - Promises #

### Step 1 ###
Simple promise resolving after 2 seconds.

	var data = {"a":"a", "b":"b"}
	function myEndpoint() {
		return new Promise(function(resolve, reject) {
			setTimeout(function() {
				resolve(data);
			}, 2000);
		});
	}
	
	myEndpoint().then(function(data) {
		alert(JSON.stringify(data));
	});

[https://jsfiddle.net/7ajp750s/](https://jsfiddle.net/7ajp750s/)

### Step 3 ###
Let's simulate a failure. 

	var endpointWillFail = false;
	var data = {"a":"a", "b":"b"}
	function myEndpoint() {
		return new Promise(function(resolve, reject) {
			setTimeout(function() {
				if(endpointWillFail)
					reject("Bohoo!");
				else
					resolve(data);
			}, 2000);
		});
	}
	
	myEndpoint()
		.then(data => {
			alert("SUCCESS: " + JSON.stringify(data));
		})
		.catch(err => {
			alert("FAILURE: " + err);
		});

[https://jsfiddle.net/1o30uy12/](https://jsfiddle.net/1o30uy12/ "https://jsfiddle.net/1o30uy12/")

Other option...

	var endpointWillFail = false;
	var data = {"a":"a", "b":"b"}
	function myEndpoint() {
		return new Promise(function(resolve, reject) {
			setTimeout(function() {
				if(endpointWillFail)
					reject("Bohoo!");
				else
					resolve(data);
			}, 2000);
		});
	}
	
	myEndpoint()
		.then(data => {
			alert("SUCCESS: " + JSON.stringify(data));
		}, err => {
			alert("FAILURE: " + err);
		});

[https://jsfiddle.net/356dy9z7/](https://jsfiddle.net/356dy9z7/ "https://jsfiddle.net/356dy9z7/")


### Step 4 ###
Simplification of endpoint...

	var endpointWillFail = false;
	var data = {"a":"a", "b":"b"}
	function myEndpoint() {
		return endpointWillFail ? Promise.reject("Bohoo!") : Promise.resolve(data);
	}
	
	myEndpoint()
		.then(data => {
			alert("SUCCESS: " + JSON.stringify(data));
		})
		.catch(err => {
			alert("FAILURE: " + err);
		});

[https://jsfiddle.net/jkrkjb88/](https://jsfiddle.net/jkrkjb88/ "https://jsfiddle.net/jkrkjb88/")

### Step 4 ###
Multiple `then`/`catch` handlers...

	var endpointWillFail = false;
	var data = {"a":"a", "b":"b"}
	function myEndpoint() {
		return endpointWillFail ? Promise.reject("Bohoo!") : Promise.resolve(data);
	}
	
	myEndpoint()
		.then(data => {
			alert("1st handler... SUCCESS: " + JSON.stringify(data));
			return data; // <-- COOL
		})
		.catch(err => {
			alert("1st handler... FAILURE: " + err);
			throw err; // <-- COOL
		})
		.then(data => {
			alert("2nd handler... SUCCESS: " + JSON.stringify(data));
			return data;
		})
		.catch(err => {
			alert("2nd handler... FAILURE: " + err);
			throw err;
		});

[https://jsfiddle.net/f9ufwpqc/](https://jsfiddle.net/f9ufwpqc/ "https://jsfiddle.net/f9ufwpqc/")

### Step 5 ###
Use the catch to create a fall-back...
Multiple `then`/`catch` handlers...

	var endpointWillFail = false;
	var data = {"a":"a", "b":"b"}
	function myEndpoint() {
		return endpointWillFail ? Promise.reject("Bohoo!") : Promise.resolve(data);
	}
	
	myEndpoint()
		.then(data => {
			alert("1st handler... SUCCESS: " + JSON.stringify(data));
			return data;
		})
		.catch(err => {
			alert("1st handler... FAILURE: " + err);
			return { "a": "fall-back" };// <--- EXTRA COOL
		})
		.then(data => {
			alert("2nd handler... SUCCESS: " + JSON.stringify(data));
			return data;
		})
		.catch(err => {
			alert("2nd handler... FAILURE: " + err);
			throw err;
		});

[https://jsfiddle.net/w8fbr3yp/](https://jsfiddle.net/w8fbr3yp/ "https://jsfiddle.net/w8fbr3yp/")

## Simple chain of `then` ##
(See previous example...)

	var endpointWillFail = false;
	var data = {"a":"a", "b":"b"}
	function myEndpoint() {
		return endpointWillFail ? Promise.reject("Bohoo!") : Promise.resolve(data);
	}
	
	myEndpoint()
		.then(data => {
			alert("1st handler... SUCCESS: " + JSON.stringify(data));
			return data;
		})
		.catch(err => {
			alert("1st handler... FAILURE: " + err);
			return { "a": "fall-back" };// <--- COOL
		})
		.then(data => {
			alert("2nd handler... SUCCESS: " + JSON.stringify(data));
			return data;
		})
		.catch(err => {
			alert("2nd handler... FAILURE: " + err);
			throw err;
		});

[https://jsfiddle.net/w8ffwpq8/](https://jsfiddle.net/w8ffwpq8/ "https://jsfiddle.net/w8ffwpq8/")

## Return a promise from a `then` function ##

	function myEndpoint1() {
		return Promise.resolve({"a":"a"});
	}

	function myEndpoint2(obj) {
		obj.b = "b";
		return Promise.resolve(obj);
	}
	
	myEndpoint1()
		.then(data => {
			alert("1st handler: " + JSON.stringify(data));
			return myEndpoint2(data); // <-- COOL
		})
		.then(data => {
			alert("2nd handler: " + JSON.stringify(data));
			return data;
		});

[https://jsfiddle.net/9tygL2f3/](https://jsfiddle.net/9tygL2f3/ "https://jsfiddle.net/9tygL2f3/")

# Parallel processing #

	var endpointWillFail = false;
	function myEndpoint1() {
		return endpointWillFail ? Promise.reject("Bohoo!") : Promise.resolve({"a":"a"});
	}

	function myEndpoint2() {
		return Promise.resolve({"b":"b"});
	}

	Promise.all([myEndpoint1(), myEndpoint2()]) // <-- COOL
		.then(values => {
			var data1 = values[0];
			var data2 = values[1];
			alert("SUCCESS... data1: " + JSON.stringify(data1) + "; data2: " + JSON.stringify(data2));
		})
		.catch(error => {
			alert("ERROR... " + error);
		});

[https://jsfiddle.net/p825722y/](https://jsfiddle.net/p825722y/ "https://jsfiddle.net/p825722y/")

# Convert anything to a promise #

	var x = {"a":"a"};
	var y = {"b":"b"};
	
	//var x = Promise.resolve({"a":"a"});
	//var y = Promise.resolve({"b":"b"});

	Promise.all([x, y])// <-- COOL; This will always work...
		.then(values => {
			var data1 = values[0];
			var data2 = values[1];
			alert("data1: " + JSON.stringify(data1) + "; data2: " + JSON.stringify(data2));
		});

[https://jsfiddle.net/6tosp25u/](https://jsfiddle.net/6tosp25u/ "https://jsfiddle.net/6tosp25u/")

Or...

	var x = {"a":"a"};
	
	//var x = Promise.resolve({"a":"a"});

	Promise.resolve(x)// <-- COOL; This will always work...
		.then(data => {
			alert("data: " + JSON.stringify(data));
		});

[https://jsfiddle.net/3jyhux8t/](https://jsfiddle.net/3jyhux8t/ "https://jsfiddle.net/3jyhux8t/")