# ND4J

# Resources 

* [Scala for Machine LEarning ND4J Post](http://www.scalaformachinelearning.com/2019/06/multivariate-normal-sampling-with-scala.html) 
* nd4j.org  
 * [Getting Started](https://nd4j.org/getstarted#)  
 * [Introduction to Code](https://nd4j.org/introduction.html)  
 * [Java Docs](http://nd4j.org/doc/)  
 
# Maven Dependencies

> I assume your pom.xml looks something like [this](/learn_to_code/java/maven?id=sample-pomxml); at the very least, make sure the `maven-shade-plugin` plugin and the `maven-jar-plugin` plugins are in your pom.xml file.  

Maven is the preferred way of loading nd4j, and that is what I will recommend as well.  You will need to place at least this dependency in your [pom dependency section](learn_to_code/java/maven?id=adding-dependencies-to-maven):  
```
		<dependency>
			<groupId>org.nd4j</groupId>
			<artifactId>nd4j-native-platform</artifactId>
			<version>0.9.1</version>
		</dependency> 
```  
* Right now, `0.9.1` is the latest official non-beta, but the most popular beta seems to be `<version>1.0.0-beta3</version>`; check [here](https://mvnrepository.com/artifact/org.nd4j/nd4j-native-platform) for the latest version.  

That will work, but nd4j will complain if you do not have [logback](learn_to_code/java/logback) on the classpath as well - to get that, please also include these dependencies:  
```
		<dependency>
			<groupId>ch.qos.logback</groupId>
			<artifactId>logback-classic</artifactId>
			<version>1.2.3</version>
			<scope>runtime</scope>
		</dependency>
		<dependency>
			<groupId>org.slf4j</groupId>
			<artifactId>slf4j-api</artifactId>
			<version>1.7.28</version>
		</dependency>
```  

# Basic ND4J Example

```
package com.wagenseller;

import org.nd4j.linalg.api.ndarray.INDArray;
import org.nd4j.linalg.factory.Nd4j;

public class Main {

    public static void main(String[] args) {

        //INDArray nd = Nd4j.create(new float[]{1,2,3,4,5,6},new int[]{3,2});
        INDArray nd = Nd4j.create(new float[]{1,2,3,4},new int[]{2,2});
        INDArray nd2 = Nd4j.create(new float[]{5,6},new int[]{2,1}); //vector as column
        INDArray nd3 = Nd4j.create(new float[]{5,6},new int[]{2}); //vector as row
        System.out.println(nd);
        System.out.println(nd2);
        System.out.println(nd3);

    }
}
```