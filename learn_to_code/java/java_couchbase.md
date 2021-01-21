# Couchbase In Java


# Couchbase API 3.0.0+ (A Note)

Java Couchbase API versions 3.0.x and later (Couchbase 6.5+) made _considerable_ changes to the API; This guide is focused on the pre-3.0.0 API. If comments / examples use 3.0.0+, it will be explicitly noted!

# Couchbase - Maven Dependency  

The Maven dependency required in pom.xml is:  
```
        <!-- https://mvnrepository.com/artifact/com.couchbase.client/java-client -->
        <dependency>
            <groupId>com.couchbase.client</groupId>
            <artifactId>java-client</artifactId>
            <version>2.7.18</version>
        </dependency>
```  

# Couchbase - Basic Example  

Here is a basic example: 

```
package com.wagenseller;

import java.util.Arrays;
import java.util.List;
import com.couchbase.client.java.Bucket;
import com.couchbase.client.java.Cluster;
import com.couchbase.client.java.CouchbaseCluster;
import com.couchbase.client.java.env.CouchbaseEnvironment;
import com.couchbase.client.java.env.DefaultCouchbaseEnvironment;

/**
 * This object maintains basic Couchbase cluster connection(s).
 * ear
 * Assumptions:
 * * The supplied username has access to requested buckets - if this is not the case, open another 
 *   CouchbaseConnection object for that specific connection.
 *
 */
public class CouchbaseConnection {

    public static final long DEFAULT_CONNECT_TIMEOUT = 30000L;
    private long DEFAULT_QUERY_TIMEOUT = 900000L;
    private long DEFAULT_KV_TIMEOUT = 60000L;

    private static Cluster cluster;
    private static CouchbaseEnvironment env;


	private String username;
    private String password;
    private List<String> clusterNodes;

    /**
     * The constructor, which takes a (comma separated list of) hosts, a username, and a password login to a couchbase cluster. Once these three are in-hand, an 
	 * attempt to connect to - and then set the internal - cluster is made.
     *
     * @param hosts
     * @param username
     * @param password
     */
    public CouchbaseConnection(String hosts, String username, String password) {

		this.username = username;
		this.password = password;

		// turn the comma separated list of hosts to a Java list
		clusterNodes = Arrays.asList(hosts.split("\\s*,\\s*"));

		// set the cluster
		cluster = inititializeCluster();
    }

    /**
     * This initializes the cluster.
     *
     * @return The cluster, if properly initialized - null otherwise.
     */
    public Cluster inititializeCluster() {

        // Set the couchbase environment variable
		env = DefaultCouchbaseEnvironment.builder()
				.connectTimeout(25000)
				.queryTimeout(1000000)
				.kvTimeout(60000) // key-value timeout. According to the notes in the docs, the Key/Value default timeout is used on operations which are performed on a 
									// specific key if not overridden by a custom timeout. This includes all commands like get(), getFromReplica() 
				.build();

		cluster = CouchbaseCluster.create(env, clusterNodes);
		cluster.authenticate(this.username, this.password);


        return cluster;
    }

    /**
     * This method returns a Bucket object, which can be used to storing JSON documents to said bucket later on.
     *
     * @param bucketName The name of the bucket requested.
     * @return The bucket requested, IF the cluster is not null; otherwise, this will return a null.
     */
    public Bucket getBucket(String bucketName) {
        Bucket retVal = null;

        if(cluster != null) { retVal = cluster.openBucket(bucketName); }
        return retVal;
    }

    /**
     * This method simply disconnects the cluster.
     */
    public void disconnectCluster() {
        if (cluster != null) cluster.disconnect();
    }

    /**
     *
     * This method helps in finding the correct document expiry time for couchbase documents.
     *
     * Couchbase is weird with its expiration set on couchbase documents (i.e. when they are auto-deleted); its unit is seconds, and for times up to 30 days (60*60*24*30)
     * the number is considered an offset; HOWEVER, numbers larger than this are an absolute timestamp (seconds after the epoch).  Since we want to use > 30 days, we are going to have to
     *  develop the offset as an absolute, I do this by figuring out the seconds since the epoch and then adding it to the offset to get the absolute offset.
     *
     *  Note this is an int and _not_ a long - this is because Couchbase's expiry uses an int and not a long.
     *
     *  As for the offset itself, I like to use 457 days (basically a year and 3 months, which is good for trending); in seconds, that number is 39484800.
     *
     * @param offsetInSeconds The offset, in seconds. I like to use 457 days (basically a year and 3 months, which is good for trending); in seconds, that number is 39484800.
     * @return The absolute offset - the time (in seconds) since the epoch plus our offset.
     */
    public static int getCbExpiryWithBase(int offsetInSeconds) {
        long timeNow = System.currentTimeMillis();

        int cbExpirationBase = (int)Math.floor((double)timeNow/1000.0);

        return (cbExpirationBase + offsetInSeconds);
    }
}

```

Then, somewhere else in the code, you can create a bucket like so:
```
...

	CouchbaseConnection someCouchbaseConnection;
    Bucket someBucket;
	someCouchbaseConnection = new CouchbaseConnection("COMMA_SEPARATED_HOSTS", "USERNAME", "PASSWORD");
	someBucket = someCouchbaseConnection.getBucket("SOME_BUCKET_NAME");
```  
* Requires imports of:  
 * `com.couchbase.client.java.Bucket`  

And then, if you want to insert / update a document, it would be:  
```
RawJsonDocument someResult = someBucket.upsert(RawJsonDocument.create("DOCID_GOES_HERE", CouchbaseConnection.getCbExpiryWithBase(39484800), "SOME_VALID_JSON_STRING_HERE"));
```  
* Requires import of `com.couchbase.client.java.document.RawJsonDocument`  
