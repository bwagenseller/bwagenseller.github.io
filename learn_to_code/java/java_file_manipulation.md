# Java File Manipulation

# Byte (Binary) Vs Char

In order to understand manipulating ifles, you must understand the difference between a <font color="green">byte</font> stream and a <font color="green">character</font> (a.k.a <font color="green">char</font>) stream.  

A <font color="green">character</font> (a.k.a <font color="green">char</font>) stream is useful when we want to process text (i.e. text or characters that we can read in something like notepad).  _Please note_ that Java _usually_ uses [unicode](https://en.wikipedia.org/wiki/Unicode#Unicode_in_use) for its character encoding.  

A <font color="green">byte</font> stream (or <font color="green">binary</font> stream) reads _raw data_ byte by byte (8 bits); these are not readable by humans and are used in everything that is not a text file (things like pictures, movies, audio, compiled code, etc are in this category).  

# Test Files

## Character Test File  

I used this as a test file for character streams.  I named the file `my.properties`:  
```
state=Pennsylvania
job=software engineer
school=Georgia Tech
#this is a test to check out comments!
favoriteFood=Chicken Wings
spaceTest1= please work
spaceTest2 =please work 2
spaceTest3 = please work 3
```

# Reading Text Files 

Here are some ways to read a text file.

## FileReader

> This section uses the [character test file](learn_to_code/java/java_file_manipulation?id=character-test-file).  

The class `FileReader` can be used to read data from a file. For example:  

**<font size="4">Main.java</font>**
```
package com.wagenseller;

import java.io.*;

public class Main {

    public static void main(String[] args) {

        String propFile = "my.properties";

        FileReader sourceStream = null;
        try
        {
            sourceStream = new FileReader(propFile);
            int characterCode;
            while ((characterCode = sourceStream.read()) != -1) System.out.println((char)characterCode);

            if (sourceStream != null) sourceStream.close();
        } catch (Exception e) {
            System.out.println("There was a problem reading the file");
        }
    }
}

```

This example reads each _individual_ character in as an integer which then must be converted to a `char` and manipulated. Sometimes this is what is desired, but in reality....this is a fairly laborious, and the class does not offer much in the way of manipulating the file. Most wrap `FileReader` in a [BufferedReader](learn_to_code/java/java_file_manipulation?id=bufferedreader-files-on-disk) instead.  

## BufferedReader (Files on Disk)

> This section uses the [character test file](learn_to_code/java/java_file_manipulation?id=character-test-file).  

This is a popular way to read in text from a file; it builds off of [FileReader](learn_to_code/java/java_file_manipulation?id=filereader) (actually _requiring_ its use) by adding some much needed functionality on top of `FileReader` (for more info on the differences between `BufferedReader` and `FileReader` please see [here](https://stackoverflow.com/questions/9648811/specific-difference-between-bufferedreader-and-filereader)).  

Here is an example:  

**<font size="4">Main.java</font>**  
```
package com.wagenseller;

import java.io.*;

public class Main {

    private static final int BUFFER_SIZE = 8192; // 8KB

    public static void main(String[] args) {

        String propFile = "my.properties";

        BufferedReader bufferedReader = null;

        try {
            String line = null;
            bufferedReader = new BufferedReader(new FileReader(propFile));

            while ((line = bufferedReader.readLine()) != null) {
                System.out.println("Line:    " + line + " ******");
            }

            bufferedReader.close();

        } catch (Exception e) {

        }
    }
}
```
* Note that instead of reading in character by character, `BufferedReader` can _read in an entire line_ and does so via `line = bufferedReader.readLine()` (and if this equals `null`, the while loop will end).  
* I print out '` ******`' after the line to see if it prints a newline or carriage return - it seems they are stripped.  

## BufferedReader (Files In Jar)

> This section uses the [character test file](learn_to_code/java/java_file_manipulation?id=character-test-file).  

Its possible to store files in [the jar file](learn_to_code/java/java_basics?id=jar-files); [Maven](learn_to_code/java/maven) does this by allowing you to store [files in the resource folder](learn_to_code/java/maven?id=resources-in-maven), which it then dumps to the root of the jar file once compiled.  

Its possible for Java code to read these files from its own jar file - this is ideal for things like default settings files, which should usually be found _outide of the jar on the disk_ somewhere, but if they do not exist for whatever reason, you can have Java get a default file from its own jar.  

There are two main differences between this implementation and how [BufferedReader for the file system](learn_to_code/java/java_file_manipulation?id=bufferedreader-files-on-disk) works; this first difference is instead of taking `new FileReader("fileLocation")` as a parameter, `BufferedReader` takes an `InputStreamReader` parameter a la `new InputStreamReader(InputStream.class.getClass().getResourceAsStream("fileLocation"))`.  It should be noted that the file is no longer a file on the file system, and this is why it has to be done in such a manner.  

The second main difference is how we reference the file itself - it used to be simply a directory location and file name, but now that it is in the jar file things change- you have to use the root of the _jar file itself_ as the root. For example, putting files in [the resource folder in Maven](learn_to_code/java/maven?id=resources-in-maven), Maven will transfer these files to the root of the jar file; so instead of referencing the file as it were on the file system:   
```
String propFile = "my.properties";
```  

We now how to _expressly_ use the directory structure of the jar file; since Maven dumped our file to the root of the jar file, we now have to reference it as:  
```
String propFile = "/my.properties";
```  

Here is an example that tries to use the file `my.properties` from the jar file _first_, and if that fails it tries the file on disk:  

```
package com.wagenseller;

import java.io.*;

public class Main {

    public static void main(String[] args) {

        String line = null;
        String propFile = "my.properties";
        String propFileInJar = "/my.properties";

        BufferedReader bufferedReader = null;

        try {
			//try to print the file from the jar first
		
            bufferedReader = new BufferedReader(new InputStreamReader(InputStream.class.getClass().getResourceAsStream(propFileInJar)));
            System.out.println("Found file in jar file! Continuing...");

            while ((line = bufferedReader.readLine()) != null) System.out.println("Line:    " + line + " @@@@@@@@@@FROM JAR@@@@@@@@@@");

            bufferedReader.close();

        } catch (Exception e) {
            System.out.println("Warning - could not find file in JAR file - trying to find on disk....");
            e.printStackTrace();
            try {
                FileReader fileReader=new FileReader(propFile);
                bufferedReader = new BufferedReader(fileReader);

                while ((line = bufferedReader.readLine()) != null) System.out.println("Line:    " + line + " $$$$$$$$$$FROM FILE$$$$$$$$$$");

                bufferedReader.close();
            } catch (Exception e2) {
                System.out.println("Error - could load file from jar file OR disk!");
            }
        }
    }
}
```
* From the perspective of `BufferedReader`, the only difference between a file on the file system and one in the jar is where the variable type is declared; after that, it works exactly the same.  
 * I have been told that `this.getClass().getResourceAsStream(propFileInJar)` may work as well as `InputStream.class.getClass().getResourceAsStream(propFileInJar)` but I did not test it.  

> I initially read about this [on stackoverflow](https://stackoverflow.com/questions/8258244/accessing-a-file-inside-a-jar-file).  

# Binary Stream Manipulation

## FileInputStream / FileOutputStream 

You may recall that [text files and all other files should be treated differently](learn_to_code/java/java_file_manipulation?id=byte-vs-char); that said, here is a quick section on manipulating data from a byte / binary stream (i.e. pictures, music, video, etc).  

Here is the example code I used to read / write a file in binary format:  

**<font size="4">Main.java</font>**  
```
package com.wagenseller;

import java.io.*;

public class Main {

    public static void main(String[] args) {

        FileInputStream sourceStream = null;
        FileOutputStream targetStream = null;

        try
        {
            sourceStream = new FileInputStream("welcometoParty.png");
            targetStream = new FileOutputStream("BruceWillis.png");

            int tempByte;
            while ((tempByte = sourceStream.read()) != -1) targetStream.write((byte)tempByte);

            if (sourceStream != null) sourceStream.close();
            if (targetStream != null) targetStream.close();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```
* I used a file of Bruce Willis in the hit movie 'Die Hard' as my picture - yours may vary. 
 * The `targetStream` pictuere does not exist, but will be created by this code (by copying the picture given to the `sourceStream` variable).  
* Each item comes in as an integer - this must be converted to a `byte` when writing to the putput file.
* Much like in [FileReader](learn_to_code/java/java_file_manipulation?id=filereader), `while ((tempByte = sourceStream.read()) != -1)` is used to check and see if there is more to read.  

> Usually [BufferedInputStream / BufferedOutputStream](learn_to_code/java/java_file_manipulation?id=bufferedinputstream-bufferedoutputstream) is used instead of FileInputStream / FileOutputStream.

## BufferedInputStream / BufferedOutputStream  

This is the standard set of classes to use for binary data manipulation. This is an improvement over [FileInputStream / FileOutputStream](learn_to_code/java/java_file_manipulation?id=fileinputstream-fileoutputstream) as there is an internal buffer that increases I/O performance.  

Here is an example:

**<font size="4">Main.java</font>**  
```
package com.wagenseller;

import java.io.*;

public class Main {

    private static final int BUFFER_SIZE = 8192; // 8KB

    public static void main(String[] args) {

		BufferedInputStream sourceStream = null;
        BufferedOutputStream targetStream = null;
        int bufferSize = 16384;	// 16KB buffer size for BufferedInputStream / BufferedOutputStream
        byte[] buffer = new byte[BUFFER_SIZE]; // buffer for our own while loop

		//attempt 1 - much slower than attempt 2 as it does it byte by byte
        try
        {
            sourceStream = new BufferedInputStream(new FileInputStream("welcometoParty.png"));
            targetStream = new BufferedOutputStream(new FileOutputStream ("BruceWillis.png"));

            int tempByte;
            while ((tempByte = sourceStream.read()) != -1) targetStream.write((byte)tempByte);
            if (sourceStream != null) sourceStream.close();
            if (targetStream != null) targetStream.close();
        } catch (Exception e) {
            e.printStackTrace();
        }

		//attempt 2 - much faster
        try
        {
            sourceStream = new BufferedInputStream(new FileInputStream("welcometoParty.png"), bufferSize);
            targetStream = new BufferedOutputStream(new FileOutputStream ("BruceWillis2.png"), bufferSize);

            while ((sourceStream.read(buffer)) != -1) targetStream.write(buffer);
            if (sourceStream != null) sourceStream.close();
            if (targetStream != null) targetStream.close();
        } catch (Exception e) {
            e.printStackTrace();
        }

    }
}
```
* I used a file of Bruce Willis in the hit movie 'Die Hard' as my picture - yours may vary. 
 * The `targetStream` picture does not exist, but will be created by this code (by copying the picture given to the `sourceStream` variable).  
* This can be used in almost the exact same way as [FileInputStream / FileOutputStream](learn_to_code/java/java_file_manipulation?id=fileinputstream-fileoutputstream), with the exception of it takes in a [FileInputStream / FileOutputStream](learn_to_code/java/java_file_manipulation?id=fileinputstream-fileoutputstream) as a parameter instead of simply the file name and location.  
* The default internal buffer for BufferedInputStream / BufferedOutputStream is 8 KB (8192 bytes), but you can set it yourself if you wish (as I did in attempt #2 above using the `bufferSize` parameter).  
* Also a bit different from the [FileInputStream / FileOutputStream](learn_to_code/java/java_file_manipulation?id=fileinputstream-fileoutputstream) example (even though this tactic could be used in [FileInputStream / FileOutputStream](learn_to_code/java/java_file_manipulation?id=fileinputstream-fileoutputstream) as well), 'attempt 2' creates a `byte[]` array that allows the 'chunking' of data into `BUFFER_SIZE` sizes - this _significantly_ increases performance and is suggested.  
 * This is different from `bufferSize` - that is specifically for the internals of BufferedInputStream / BufferedOutputStream, but using `byte[] buffer` speeds it up from out end (and is a good coding practice to do so).  



