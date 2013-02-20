http://wiki.apache.org/thrift/Tutorial

	/**
	 * The first thing to know about are types. The available types in Thrift are:
	 *
	 *  bool        Boolean, one byte
	 *  byte        Signed byte
	 *  i16         Signed 16-bit integer
	 *  i32         Signed 32-bit integer
	 *  i64         Signed 64-bit integer
	 *  double      64-bit floating point value
	 *  string      String
	 *  map<t1,t2>  Map from one type to another
	 *  list<t1>    Ordered list of one type
	 *  set<t1>     Set of unique elements of one type
	 *
	 * Did you also notice that Thrift supports C style comments?
	 */

struct Work {
  1: i32 num1 = 0,
  2: i32 num2,
  3: Operation op,
  4: optional string comment,
}

http://diwakergupta.github.io/thrift-missing-guide/

As you can see, each field in the message definition has a unique numbered tag. These tags are used to identify your fields in the wire format, and should not be changed once your message type is in use.

It is often useful to split up Thrift definitions in separate files to ease maintainance, enable reuse and improve modularity/organization. Thrift allows files to include other Thrift files. Included files are looked up in the current directory and by searching relative to any paths specified with the -I compiler flag.
Included objects are accessed using the name of the Thrift file as a prefix.

	include "../common/tweet.thrift"           // 1
	...
	struct TweetSearchResult {
		1: list<tweet.Tweet> tweets; // 2
	}
