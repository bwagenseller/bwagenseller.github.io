# Java: Asynchronous Programming  

# Asynchronous Programming  

<font color="green">Asynchronous Programming</font> is the concept of being able to process different pieces of code concurrently. The advantage of this is the code does not have to be run sequentially - so the _overall_ runtime can be completed faster. In addition, sometimes the code is _required_ to be asynchronous: sometimes we rely on a remote application, and running the code sequentially would force us to block until the remote application responds.  <font color="green">Asynchronous programming</font> allows us to navigate these challenges.  

# Akka  

The [Akka](/learn_to_code/java/akka/) toolset is a powerful, expansive collection of packages that addresses <font color="purple">asynchronous programming</font>; the reality is Akka does <font color="purple">asynchronous programming</font> plus much more. I will not delve into Akka in this document, but you can check out my notes on it [here](/learn_to_code/java/akka/).  

# CompletableFuture  

> In addition to my own testing, I drew some knowledge from [baeldung](https://www.baeldung.com/java-completablefuture) and [Callicoder](https://www.callicoder.com/java-8-completablefuture-tutorial/) when digging into <font color="green">CompletableFutures</font>.  

The <font color="green">CompletableFuture</font> class is a framework that was created to address <font color="purple">asynchronous programming</font>. It avoids blocking in the code by using [lambda expressions](earn_to_code/java/java_basics?id=lambda-function) which are run in a separate thread; when the thread completes, the result (if any) is returned to the main application. 

## CompletableFuture - Before We Begin  

> We need to go over a few basic facts before we delve into <font color="purple">CompletableFutures</font>. You may not fully understand some of the methods mentioned here just yet, but do not worry - they will be covered later. For now, understand these concepts!  

### Lambda Expressions

Due to the fact that the entire point of a <font color="purple">CompletableFuture</font> is it facilitates code to run on a separate thread (so it does not block the main thread), we often need to define the function that will run on said remote thread and then return its results at some arbitrary point in the future. We usually create a [lambda expression](learn_to_code/java/java_basics?id=lambda-expression) (i.e. `() -> {}`) to accomplish this. An example of a lambda expression is: 

```
Supplier<String> myLambdaFunction = () -> {
	try {
		TimeUnit.SECONDS.sleep(1);
	} catch (InterruptedException e) {

	}
	return "One ring";
};
```  

This lambda expression is stored in the object `myLambdaFunction`; the `<String>` portion informs the code that it will return a String.  `myLambdaFunction` simply sleeps for one second and then returns the String "<font color="green">One ring</font>".  

The <font color="purple">CompletableFuture</font> class utilizes [Supplier](learn_to_code/java/java_basics?id=supplier) (in the [first lambda](learn_to_code/java/asynchronous_programming?id=runasync-supplyasync-and-get) of <font color="purple">CompletableFuture</font>), [Function](learn_to_code/java/java_basics?id=javautilfunctionfunction) (in [chaining operations](learn_to_code/java/asynchronous_programming?id=chaining-operations-thenapply-thenapplyasync)), and [Runnable](learn_to_code/java/java_basics?id=runnable) (in lambdas that do not return anything).


> We could also simply define the lambda expression inline (i.e. `() -> {}`), but sometimes its advantageous to store as an object if its needed in multiple spots / if you want the code to be a bit easier to read.  

### Methods are not Inline  

Its <font color="red">important</font> to note that a <font color="purple">CompletableFuture</font> does _not_ work inline / inplace (for most methods). If the method returns a <font color="purple">CompletableFuture</font> it means you cannot call it inplace. 

For example, consider the [basic CompletableFuture example](learn_to_code/java/asynchronous_programming?id=runasync-supplyasync-and-get); if we were to change two lines of code to the below it would *_not_* work: 
```
        CompletableFuture<String> completableFutureAsync = new CompletableFuture<>();
		...
        completableFutureAsync.supplyAsync(myLambdaFunction);
		...
```

The inline `completableFutureAsync.supplyAsync(myLambdaFunction);` would not work - this would hang indefinitely on `completableFutureAsync.get()`, as technically the `supplyAsync()` was never bound to `completableFutureAsync`; we *must* set it equal to the result a la `completableFutureAsync = completableFutureAsync.supplyAsync(myLambdaFunction)`.  

## CompletableFuture Code Skeleton  


Here is the basic skeleton of the code for all examples. For each example, you will have to replace the `process()` method:

```
package com.industries.wagenseller;

import java.util.concurrent.CompletableFuture;
import java.util.concurrent.TimeUnit;
import java.util.function.*;

public class Main {

    public void process() {

		// *************************************
		// *************************************
		// PLACE CODE HERE *********************
		// *************************************
		// *************************************
		
        System.out.println("Program exiting!");
    }

    public static void main(String[] args) {

        new Main().process();
    }
}
```  

## runAsync(), supplyAsync(), and get() 

Here is a basic example of a <font color="purple">CompletableFuture</font> (paste this in the `process()` section of the [CompletableFuture code skeleton](learn_to_code/java/asynchronous_programming?id=completablefuture-code-skeleton):  
```
        String theResult = "";
        CompletableFuture<String> completableFutureAsync;

        Supplier<String> myLambdaFunction = () -> {
            try {
                TimeUnit.SECONDS.sleep(1);
            } catch (InterruptedException e) {

            }
            return "One ring";
        };
		
        completableFutureAsync = CompletableFuture.supplyAsync(myLambdaFunction);

        try {
            //theResult = completableFutureAsync.get(); //completableFutureAsync
            theResult = completableFutureAsync.get(1500, TimeUnit.MILLISECONDS);
            System.out.println(theResult);
        } catch (java.util.concurrent.TimeoutException e) {
            System.out.println("'get()' took too long and we skipped its return.");
        } catch (java.lang.InterruptedException e) {

        } catch (java.util.concurrent.ExecutionException e) {

        }
```  

> We could have also created a new object `completableFutureAsync` a la '`CompletableFuture<String> completableFutureAsync = new CompletableFuture<>();`'; if we did this we could have substituted in '`completableFutureAsync = completableFutureAsync.supplyAsync(myLambdaFunction);`' instead, if we wanted.  

In this example, we make a <font color="purple">CompletableFuture</font> object `completableFutureAsync`.  

Due to the fact that the entire point of a <font color="purple">CompletableFuture</font> is it facilitates code to run on a separate thread (so it does not block the main thread), we often need to define the function that will run on said remote thread and then return its results at some arbitrary point in the future. We usually create a [lambda expression](learn_to_code/java/asynchronous_programming?id=lambda-expressions) to accomplish this (`supplyAsync()` uses [Supplier](learn_to_code/java/java_basics?id=supplier)); in this case, we made the object `myLambdaFunction` that simply sleeps for one second and then returns the String "<font color="green">One ring</font>".  

Now that we have the code that will run on a separate thread, we need to bind that to `completableFutureAsync`; we do that with the method `supplyAsync()`, which will _usually_ (unless there is an error) return the result (in our case a String) to this thread. Note that `supplyAsync()` takes our [lambda expression](learn_to_code/java/asynchronous_programming?id=lambda-expressions) `myLambdaFunction` as a parameter - this will run on a separate thread.  

We get the result of the lambda expression in the line `theResult = completableFutureAsync.get(500, TimeUnit.MILLISECONDS)`; The `.get(500, TimeUnit.MILLISECONDS)` method blocks for 1500 milliseconds, in the hopes that `myLambdaFunction` will return in that timeframe. If it does not, a `java.util.concurrent.TimeoutException` will be thrown and we will see "<font color="green">'get()' took too long and we skipped its return.</font>" (and yes, it is true that the entire point of the <font color="purple">CompletableFuture</font> is to _not_ block, but this is just an example; also, sometimes you will use an API that uses a <font color="purple">CompletableFuture</font> and you need it to block until the code returns).  

We could also block _until_ the result is returned if we simply used `.get()` (with no time parameters sent); this can get a bit dicey, though, because if the method never returns, this code will hang indefinitely. Its best practice to use the timeout with `.get()`.  

The above example covers times when you need an object returned. What if you do not need something returned? For those circumstances you can simply use `runAsync()`; a basic example of this is as follows (paste this in the `process()` section of the [CompletableFuture code skeleton](learn_to_code/java/asynchronous_programming?id=completablefuture-code-skeleton):  
```
        CompletableFuture<Void> voidFuture = new CompletableFuture<>();

        Runnable myVoidLambdaFunction = () -> {
            try {
                TimeUnit.SECONDS.sleep(1);
            } catch (InterruptedException e) {

            }
            System.out.println("An example of runAsync()");
        };

        voidFuture.runAsync(myVoidLambdaFunction);
```  

Note that this uses the [Runnable](learn_to_code/java/java_basics?id=runnable) class and _not_ [Supplier](learn_to_code/java/java_basics?id=supplier) (which is used by `.supplyAsync)`).  

> The code starts running as soon as `supplyAsync()` is called - this happens if no <font color="green">join()</font> or a <font color="green">get()</font> is in the code.  

## join()  

<font color="green">join()</font> is very similar to [get()](learn_to_code/java/asynchronous_programming?id=runasync-supplyasync-and-get) with a few caveats: 
* <font color="purple">get()</font> has _checked_ exceptions (meaning, your code _must_ handle them); <font color="green">join()</font> does not. If you fully use a form of [CompleteableFuture exception handling](learn_to_code/java/asynchronous_programming?id=completablefuture-exception-handling) this should not be an issue; in any event, make sure all errors are handled.  
* <font color="green">join()</font> does not seem to have the timeout functionality that [get()](learn_to_code/java/asynchronous_programming?id=runasync-supplyasync-and-get) has, so if a timeout is important, you will need to use [get()](learn_to_code/java/asynchronous_programming?id=runasync-supplyasync-and-get).  
* Apparently, according to [baeldung](https://www.baeldung.com/java-completablefuture), <font color="green">join()</font> allows for the use of `Stream.map()` (whereas <font color="purple">get()</font> does not). If that is needed, see [8. Running Multiple Futures in Parallel](https://www.baeldung.com/java-completablefuture) for an example.  

> The code starts running as soon as `supplyAsync()` is called - this happens if no <font color="green">join()</font> or a <font color="green">get()</font> is in the code.  

## CompleteableFuture With Void Return

In some cases, you will need to use a <font color="purple">CompletableFuture</font> that runs without returning anything; if that is the case, you can simply use <font color="green">runAsync()</font>, which only returns a `Void`; here is a basic example (paste this in the `process()` section of the [CompletableFuture code skeleton](learn_to_code/java/asynchronous_programming?id=completablefuture-code-skeleton):  
```
        CompletableFuture<Void> completableFutureAsync = new CompletableFuture<>();

        completableFutureAsync = completableFutureAsync.runAsync(() -> {
            System.out.println("One ring to rule them all!");
            try {
                TimeUnit.SECONDS.sleep(2);
            } catch (InterruptedException e) {

            }
        });

        completableFutureAsync.join();

        if (completableFutureAsync.isDone()) System.out.println("The CompleteableFuture was marked as done.");
```  

Note that the `join()` here _will_ block your main code, as it waits for the <font color="purple">CompletableFuture</font> to finish. The `join()` isnt necessary, but I wanted to show its use.  

## Chaining Operations - thenApply(), thenApplyAsync()  

Its quite possible you wish to chain asynchronous operations; so for example, operation `A` has to happen first, then once that is done, operation `B` has to happen, then finally operation `C` takes place. This can be acheived with `thenApply()`; here is a basic example (paste this in the `process()` section of the [CompletableFuture code skeleton](learn_to_code/java/asynchronous_programming?id=completablefuture-code-skeleton):  
```
        String theResult = "";
        CompletableFuture<String> completableFutureAsync = new CompletableFuture<>();

        Supplier<String> myLambdaFunction = () -> {
            try {
                TimeUnit.SECONDS.sleep(1);
            } catch (InterruptedException e) {

            }

            return "One ring";
        };

        completableFutureAsync = CompletableFuture.supplyAsync(myLambdaFunction);

		// First thenApplyAsync
        completableFutureAsync = completableFutureAsync.thenApplyAsync(sentenceFragment -> {
            return sentenceFragment + " to rule";
        });

		// Second thenApplyAsync. To change things around, we create the function that will be used in the thenApplyAsync beforehand.
        Function<String, String> myThenApplyFunction = (String inputSentenceFragment) -> {
            String myPart = " them all!";
            return inputSentenceFragment + myPart;
        };
        
        completableFutureAsync = completableFutureAsync.thenApplyAsync(myThenApplyFunction);

        try {
            theResult = completableFutureAsync.get(1500, TimeUnit.MILLISECONDS);
            System.out.println(theResult);
        } catch (java.util.concurrent.TimeoutException e) {
            System.out.println("'get()' took too long and we skipped its return.");
        } catch (java.lang.InterruptedException e) {

        } catch (java.util.concurrent.ExecutionException e) {

        }

```  
* Prints `One ring to rule them all!`  

We first used a [supplyAsync()](learn_to_code/java/asynchronous_programming?id=runasync-supplyasync-and-get); that will be carried out on a different thread, but when it returns we want to perform further operations on it. We can make further operations on the data (once it returns) with <font color="green">thenApplyAsync</font> / <font color="green">thenApply</font>. In the example above, there are two <font color="green">thenApplyAsync</font> applied: one that appends the words " to rule", and then, _once that is finished_, there is a _second_ <font color="green">thenApplyAsync</font> that will append the words " them all!" at the end of the string. We could chain <font color="green">thenApplyAsync</font> indefinitely, if we wanted to.  

A few notes about <font color="green">thenApplyAsync</font>: 
* Their lambda expressions utilize the [Function](learn_to_code/java/java_basics?id=javautilfunctionfunction) interface.  
* While they are asychronous, they happen sequentially, with one <font color="green">thenApplyAsync</font> only becoming active after the last one has completed.  
* The order in which the <font color="green">thenApplyAsync</font> is seen in the code will be kept.  
* <font color="green">thenApply</font> runs as soon as the chain before it is finished - it does _not_ need the `.get()` or `.join()` to run (but they may be needed to _extract_ the final value).  
* <font color="green">thenApply</font> and <font color="green">thenApplyAsync</font> are _almost_ interchangeable. 
 * <font color="green">thenApply</font> _may_ execute on the same thread if the result is already available. 
 * <font color="green">thenApplyAsync</font> is guaranteed to not block the current thread; in addition, you have the ability to set its `Executor` (i.e. its threadpool). 
 * You can read more on this [at stackoverflow](https://stackoverflow.com/questions/47489338/what-is-the-difference-between-thenapply-and-thenapplyasync-of-java-completablef).  

## CompletableFuture Exception Handling  

If you simply ignore all exception handling in a work chain and there is an exception, an exception will be thrown when accessing the [get()](learn_to_code/java/asynchronous_programming?id=runasync-supplyasync-and-get) method; that said, you _can_ intercept errors differently.  

### exceptionally()  

The `exceptionally()` method is designed to handle errors in a <font color="purple">CompletableFuture</font> work chain. `exceptionally()` utilizes the [Function](learn_to_code/java/java_basics?id=javautilfunctionfunction) interface, with a `Throwable` as an input and a second object type as the return object type. Here is an example (paste this in the `process()` section of the [CompletableFuture code skeleton](learn_to_code/java/asynchronous_programming?id=completablefuture-code-skeleton):   
```  
        String theResult = "";
        CompletableFuture<String> completableFutureAsync = new CompletableFuture<>();

        Supplier<String> myLambdaFunction = () -> {
            try {
                TimeUnit.SECONDS.sleep(1);
            } catch (InterruptedException e) {

            }

            throw new IllegalStateException("Illegal state exception example.");
            //return "One ring";
        };
		
        Function<Throwable, String> myExceptionLambda = (Throwable myException) -> {
            if (myException instanceof java.util.concurrent.CompletionException) {
                // This will always be the case
                System.out.println("Error message: " + myException.getMessage());
            } else {
                System.out.println("This should never be hit: " + myException.getMessage() + " " + myException.getClass());
            }

            // necessary. The function MUST return an object of the same type as defined in the CompletableFuture (in our case, a String)
            return "THERE WAS AN ERROR";
        };		

        completableFutureAsync = CompletableFuture.supplyAsync(myLambdaFunction);
		
        completableFutureAsync = completableFutureAsync.exceptionally(myExceptionLambda);		

        completableFutureAsync = completableFutureAsync.thenApplyAsync(sentenceFragment -> {
            try {
                TimeUnit.MILLISECONDS.sleep(500);
            } catch (InterruptedException e) {

            }
            return sentenceFragment + " to rule";
        });

        Function<String, String> myThenApplyFunction = (String inputSentenceFragment) -> {
            try {
                TimeUnit.MILLISECONDS.sleep(100);
            } catch (InterruptedException e) {

            }

            String myPart = " them all!";
            return inputSentenceFragment + myPart;
        };

        completableFutureAsync = completableFutureAsync.thenApplyAsync(myThenApplyFunction);

        try {
            theResult = completableFutureAsync.get(4500, TimeUnit.MILLISECONDS);
            System.out.println(theResult);
        } catch (java.util.concurrent.TimeoutException e) {
            System.out.println("'get()' took too long and we skipped its return.");
        } catch (java.lang.InterruptedException e) {
            System.out.println("Interrupted exception: " + e.getMessage());
        } catch (java.util.concurrent.ExecutionException e) {
            System.out.println("ExecutionException exception: " + e.getMessage());
        }

        if (completableFutureAsync.isCompletedExceptionally()) System.out.println("Errors were seen in the execution!");
```  

This prints:  
```
Error message: java.lang.IllegalStateException: Illegal state exception example.
THERE WAS AN ERROR to rule them all!
```  

!> It may be counterintuitive, but `.isCompletedExceptionally()` _only_ works if there is an _uncaught_ error - since `.exceptionally()` actually catches the error, `.isCompletedExceptionally()` will _always_ be false when `.exceptionally()` is used (properly, i.e. not inline).  

Take note of the chain order, as its important:  
1. `supplyAsync()`  
2. `exceptionally()`  
3. `thenApplyAsync()`  
4. `thenApplyAsync()`  

In this example, we forced an error in `supplyAsync()`, which was caught in `exceptionally()`. Since there _was_ an error, `exceptionally()` is activated. You can take different actions here, and the return type of `exceptionally()` is that of the completion stage / chain that caused the error (in our case, its always a String). 

Some notes:
* If there is no error, the code in `exceptionally()` will _never_ execute. 
* The `Throwable` type is _always_ `java.util.concurrent.CompletionException` in `exceptionally()`, no matter the actual exception. That said, the _actual_ exception will be encapsulated in the message of the exception.  
* _Do not try to perform the `exceptionally()` in an inline manner (i.e. always set the result to a <font color="purple">CompletableFuture</font> object). 
 * If you try to do this inline, The internal code will run, but the return will not matter, as the get() will simply throw an error itself (specifically, it will throw an java.util.concurrent.ExecutionException with the _original_ error's message). 
 * If you simply add this to the chain of <font color="purple">CompleteableFutures</font>, it will execute the code as given in the exception lambda in addition to returning the default object (i.e. for us, its a String, and that String is "THERE WAS AN ERROR") _for the leg that experienced the error_, and `get()` will not trigger an error.  

In addition, what is returned depends on _where_ you place the `.exceptionally()`; for example, if the chain order is as listed above, and the error happens in `supplyAsync()`, the object generated in the `exceptionally()` method will be passed to the first `thenApplyAsync()`; however, if `supplyAsync()` experiences the error and the `exceptionally()` comes after the last `thenApplyAsync()` in the chain, _the entire_ chain is considered to have errored out. It may be advantageous to place `exceptionally()` at the end of the chain, if this is what you desire. If you want to try to save the chain, read more on that [here](https://stackoverflow.com/questions/46049781/completablefuture-exceptionally-breaks-the-work-chain) - *or*, use [handle()](learn_to_code/java/asynchronous_programming?id=handle-handleasync) (see immediately below).   

### handle(), handleAsync()  

> Generally speaking, [exceptionally()](learn_to_code/java/asynchronous_programming?id=exceptionally) should be used when you do not care about a transformation, and <font color="green">handle()</font> should be used when the output needs to be transformed to a different type _regardless of errors being present_.  

If you want a sidegrade in exception handling (when compared to [exceptionally()](learn_to_code/java/asynchronous_programming?id=exceptionally)), you can use <font color="green">handle()</font> (or <font color="green">handleAsync()</font>, the differences are the same as between [thenApply() and thenApplyAsync()](learn_to_code/java/asynchronous_programming?id=chaining-operations-thenapply-thenapplyasync)).  The advantage <font color="green">handle()</font> has over [exceptionally()](learn_to_code/java/asynchronous_programming?id=exceptionally) is it is _always_ run, so if a transformation is necessary, <font color="green">handle()</font> is what should be used. Here is an example (paste this in the `process()` section of the [CompletableFuture code skeleton](learn_to_code/java/asynchronous_programming?id=completablefuture-code-skeleton):  
```
String theResult = "";
CompletableFuture<String> completableFutureAsync = new CompletableFuture<>();

BiFunction<String, Throwable, String> myBiFunctionLambda = (String resultsFromLastChain, Throwable myException) -> {
	String retVal = "";
	if (myException != null) {
		// if there is an error
		if (myException instanceof java.util.concurrent.CompletionException) {
			// This will always be the case
			System.out.println("Error message: " + myException.getMessage());
		} else {
			System.out.println("Unknown Error message: " + myException.getMessage() + " " + myException.getClass());
		}

		retVal = "THERE WAS AN ERROR";
	} else {
		// no error - this is just a passthrough
		retVal = resultsFromLastChain;
	}

	// necessary. The function MUST return an object of the same type as defined in the CompletableFuture (in our case, a String)
	return retVal;
};

Supplier<String> myLambdaFunction = () -> {
	try {
		TimeUnit.SECONDS.sleep(1);
	} catch (InterruptedException e) {

	}

	throw new IllegalStateException("Illegal state exception example.");
	//return "One ring";
};

completableFutureAsync = CompletableFuture.supplyAsync(myLambdaFunction);

completableFutureAsync = completableFutureAsync.handleAsync(myBiFunctionLambda);

completableFutureAsync = completableFutureAsync.thenApplyAsync(sentenceFragment -> {
	try {
		TimeUnit.MILLISECONDS.sleep(500);
	} catch (InterruptedException e) {

	}
	return sentenceFragment + " to rule";
});

Function<String, String> myThenApplyFunction = (String inputSentenceFragment) -> {
	try {
		TimeUnit.MILLISECONDS.sleep(100);
	} catch (InterruptedException e) {

	}

	//throw new IllegalStateException("Illegal state exception example.");
	String myPart = " them all!";
	return inputSentenceFragment + myPart;
};

completableFutureAsync = completableFutureAsync.thenApplyAsync(myThenApplyFunction);

try {
	theResult = completableFutureAsync.get(4500, TimeUnit.MILLISECONDS);
	System.out.println(theResult);
} catch (java.util.concurrent.TimeoutException e) {
	System.out.println("'get()' took too long and we skipped its return.");
} catch (java.lang.InterruptedException e) {
	System.out.println("Interrupted exception: " + e.getMessage());
} catch (java.util.concurrent.ExecutionException e) {
	System.out.println("ExecutionException exception: " + e.getMessage());
}
```  

This will return:  
```
Error message: java.lang.IllegalStateException: Illegal state exception example.
THERE WAS AN ERROR to rule them all!
```  

This uses a `BiFunction` interface instead of a [Function](learn_to_code/java/java_basics?id=javautilfunctionfunction) interface, and the object types expected are: 
1. Object type that is returned by a success in the previous chain.  
2. Throwable.  
1. Object type that is will be returned by this part of the chain.  

## Cancelling a CompleteableFuture  

Its possible to cancel a <font color="purple">CompletableFuture</font> if it has not finished. That said, cancelling a <font color="purple">CompletableFuture</font> is a bit wonky, as it seems its difficult (see a stackoverflow discussion on it [here](https://stackoverflow.com/questions/23320407/how-to-cancel-java-8-completable-future)). 

We can cancel a <font color="purple">CompletableFuture</font> with a call to the `cancel()` method. It does take one parameter (a boolean), but according to the docs, [it does nothing](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/CompletableFuture.html#cancel-boolean-). 

From as far as I can tell, its only possible to stop a <font color="purple">CompletableFuture</font> _chain_, _not_ an individual <font color="purple">CompletableFuture</font> element. For example, Lets say you have chained 3 <font color="purple">CompletableFutures</font>:
1. `supplyAsync()`  
2. `thenApplyAsync()`  
3. `thenApplyAsync()` 

Lets say you administred a <font color="green">.cancel()</font> to the above <font color="purple">CompletableFuture</font> chain, and the cancel came in as the `supplyAsync()` was mid-process. There is no way to actually stop the `supplyAsync()` and it will finish; however, the #2 / #3 `thenApplyAsync()` will never run.  

Here is an example (paste this in the `process()` section of the [CompletableFuture code skeleton](learn_to_code/java/asynchronous_programming?id=completablefuture-code-skeleton):  
```
        String theResult = "";
        CompletableFuture<String> completableFutureAsync = new CompletableFuture<>();

        Supplier<String> myLambdaFunction = () -> {
            try {
                TimeUnit.SECONDS.sleep(2);
            } catch (InterruptedException e) {

            }
            System.out.println("XX One ring XX");
            return "One ring";
        };
        completableFutureAsync = CompletableFuture.supplyAsync(myLambdaFunction);

        completableFutureAsync = completableFutureAsync.thenApplyAsync(sentenceFragment -> {
            System.out.println("XX to rule them all.XX");
            return sentenceFragment + " to rule them all.";
        });

        try {
            theResult = completableFutureAsync.get(100, TimeUnit.MILLISECONDS);
            System.out.println(theResult);
        } catch (java.util.concurrent.TimeoutException e) {
            System.out.println("'get()' took too long and we skipped its return.");
            try {
                if (!(completableFutureAsync.isDone() || completableFutureAsync.isCancelled() || completableFutureAsync.isCompletedExceptionally())) {

                    completableFutureAsync.cancel(true);
                    System.out.println("CompleteableFuture cancelled.");
                }
            } catch (java.util.concurrent.CancellationException innerException) {
                System.out.println("Cancellation exception: " + innerException.getMessage());
            } catch (Exception innerException) {
                System.out.println("Inner Exception: " + innerException.getMessage());
            }

        } catch (java.lang.InterruptedException e) {
            System.out.println("Interrupted exception: " + e.getMessage());
        } catch (java.util.concurrent.ExecutionException e) {
            System.out.println("ExecutionException exception: " + e.getMessage());
        }

        if (completableFutureAsync.isCancelled()) System.out.println("Work was cancelled!");

        try {
            TimeUnit.SECONDS.sleep(5);
        } catch (InterruptedException e) {

        }

        if (completableFutureAsync.isDone()) System.out.println("Program exiting!");
```  

The point of this is to launch a chain of <font color="purple">CompletableFutures</font> that will take longer to execute than the `.get()` timeout (the `.supplyAsync()` takes 2 secondds, but the `.get()` timeout is only 500 milliseconds). Once the timeout is hit, we attempt to cancel with `completableFutureAsync.cancel(true)`. To the unsuspecting, the following _should_ be printed:
```  
'get()' took too long and we skipped its return.
CompleteableFuture cancelled.
Work was cancelled!
Program exiting!
```  

However, this is what is printed:  
```
'get()' took too long and we skipped its return.
CompleteableFuture cancelled.
Work was cancelled!
XX One ring XX
Program exiting!
```  

It is apparent that even though the `.get()` timeout is well below the sleep timer in `.supplyAsync()`, the print line `XX One ring XX` is still printed; this is because that particular <font color="purple">CompletableFuture</font> piece was already running when we administered `completableFutureAsync.cancel(true)`; however, `XX to rule them all.XX` never printed (this `.thenApplyAsync()` never ran). 

Just note that if you try to use [.get()](learn_to_code/java/asynchronous_programming?id=runasync-supplyasync-and-get) or [.join()](learn_to_code/java/asynchronous_programming?id=join) _after_ `.cancel()` it _will_ error out.  

## Combining CompleteableFutures - thenCombine(), thenCombineAsync()  

Occasionally, you will have to run two (or more) separate <font color="purple">CompletableFutures</font> that will need to be merged; the code cannot proceed until both <font color="purple">CompletableFutures</font> are complete, but once they are complete and are merged, it can proceed as one <font color="purple">CompletableFuture</font> chain. This can be achieved with a <font color="green">thenCombine()</font> (or <font color="green">thenCombineAsync()</font>, the differences are the same as between [thenApply() and thenApplyAsync()](learn_to_code/java/asynchronous_programming?id=chaining-operations-thenapply-thenapplyasync)).  It should be noted that <font color="green">thenCombine()</font> utilizes a [BiFunction](learn_to_code/java/java_basics?id=javautilfunctionbifunction) lambda expression. 

Here is an example (paste this in the `process()` section of the [CompletableFuture code skeleton](learn_to_code/java/asynchronous_programming?id=completablefuture-code-skeleton):   
```
        String theResult = "";
        CompletableFuture<String> completableFutureAsync = new CompletableFuture<>();
        CompletableFuture<String> anotherCompletableFutureAsync = new CompletableFuture<>();

        Supplier<String> myAlphaLambdaFunction = () -> {
            try {
                TimeUnit.SECONDS.sleep(3);
            } catch (InterruptedException e) {

            }
            return "One ring";
        };
        Supplier<String> myBetaLambdaFunction = () -> {
            try {
                TimeUnit.SECONDS.sleep(3);
            } catch (InterruptedException e) {

            }
            return " to rule them all.";
        };

        BiFunction<String, String, String> myBiFunction = (String completionStringFromLeft, String completionStringFromRight) -> {
            return completionStringFromLeft + completionStringFromRight;
        };

        completableFutureAsync = CompletableFuture.supplyAsync(myAlphaLambdaFunction);
        anotherCompletableFutureAsync = CompletableFuture.supplyAsync(myBetaLambdaFunction);

        completableFutureAsync = completableFutureAsync.thenCombine(anotherCompletableFutureAsync, myBiFunction);

        try {
            theResult = completableFutureAsync.get(3500, TimeUnit.MILLISECONDS);
            System.out.println(theResult);
        } catch (java.util.concurrent.TimeoutException e) {
            System.out.println("'get()' took too long and we skipped its return.");
        } catch (java.lang.InterruptedException e) {
            System.out.println("Interrupted exception: " + e.getMessage());
        } catch (java.util.concurrent.ExecutionException e) {
            System.out.println("ExecutionException exception: " + e.getMessage());
        }
        if (completableFutureAsync.isDone()) System.out.println("Program exiting!");
```  

The above code makes two distinct <font color="purple">CompletableFutures</font> (`completableFutureAsync` and `anotherCompletableFutureAsync`); the way it works is you use one of them as a base (in our case thats `completableFutureAsync`), and from there you call `.thenCombine()` and use the other <font color="purple">CompletableFuture</font> (in our case `anotherCompletableFutureAsync`) as a parameter. This is done in the line:  
```
completableFutureAsync = completableFutureAsync.thenCombine(anotherCompletableFutureAsync, myBiFunction);
```  

This also requires the building of a [BiFunction](learn_to_code/java/java_basics?id=javautilfunctionbifunction) lambda expression.  

## Void Sink - thenAccept(), thenAcceptAsync()  

There are times when you do not need the output of the <font color="purple">CompletableFuture</font>, you just need it completed (with maybe some cleanup work performed in the last step); for these times, you can use a <font color="green">thenAccept()</font>, which acts as a [sink](learn_to_code/java/akka/streams?id=sink) / final chain in a <font color="purple">CompletableFuture</font> that does not return anything.  You could also use <font color="green">thenAcceptAsync()</font> - the differences are the same as between [thenApply() and thenApplyAsync()](learn_to_code/java/asynchronous_programming?id=chaining-operations-thenapply-thenapplyasync).  

<font color="green">thenAccept()</font> utilizes a [Consumer](learn_to_code/java/java_basics?id=consumer) lambda expression; here is an example (paste this in the `process()` section of the [CompletableFuture code skeleton](learn_to_code/java/asynchronous_programming?id=completablefuture-code-skeleton):  
```
String theResult = "";
CompletableFuture<String> completableFutureAsync = new CompletableFuture<>();
Supplier<String> myLambdaFunction = () -> {
	try {
		TimeUnit.SECONDS.sleep(1);
	} catch (InterruptedException e) {
	}
	return "One ring";
};

Function<String, String> myThenApplyFunction = (String inputSentenceFragment) -> {
	String myPart = " them all!";
	return inputSentenceFragment + myPart;
};

Consumer<String> myConsumer = (String theLastResult) -> {
	System.out.println("The last result was: " + theLastResult);
};

completableFutureAsync = CompletableFuture.supplyAsync(myLambdaFunction);

completableFutureAsync = completableFutureAsync.thenApplyAsync(sentenceFragment -> {
	return sentenceFragment + " to rule";
});

// The paths split - this is path A
completableFutureAsync = completableFutureAsync.thenApplyAsync(myThenApplyFunction);

// And this is path B
CompletableFuture<Void> completableFutureVoid = completableFutureAsync.thenAcceptAsync(myConsumer);

try {
	completableFutureVoid.get(4500, TimeUnit.MILLISECONDS);
	
	theResult = completableFutureAsync.get(4500, TimeUnit.MILLISECONDS);
	System.out.println(theResult);
} catch (java.util.concurrent.TimeoutException e) {
	System.out.println("'get()' took too long and we skipped its return.");
} catch (java.lang.InterruptedException e) {

} catch (java.util.concurrent.ExecutionException e) {

}
if (completableFutureAsync.isDone()) System.out.println("Program exiting!");
```  

The above code (more or less) mirrors the example code in [thenApply()](learn_to_code/java/asynchronous_programming?id=chaining-operations-thenapply-thenapplyasync), but it diverges at the line that says `The paths split - this is path A`; at that point, the path followed by `completableFutureAsync` (path `A`) will necessitate the usage of `.get()` / `.join()` to obtain the output value (as it did in the [thenApply()](learn_to_code/java/asynchronous_programming?id=chaining-operations-thenapply-thenapplyasync) example). The code above introduces a fork at that point - that code still executes, but now the result is forked into a new chain which terminates in the `.thenAcceptAsync()` (path `B`).   Note that this uses the same output sent from the final `.thenApplyAsync(myThenApplyFunction)`, but from here it is a _completely_ different chain (as evident from the declaration `CompletableFuture<Void>`, declaring it as using a `Void` output).  

Path `A` is still terminated with a `theResult = completableFutureAsync.get(4500, TimeUnit.MILLISECONDS)`. Path `B` _also_ needs a `.get()` / `.join()` to run - in fact, if a `.get()` / `.join()` is _not_ present, the code in `completableFutureAsync.thenAcceptAsync(myConsumer)` will _never_ run (this is the reason we still have to run the `.get()` in `completableFutureVoid.get(4500, TimeUnit.MILLISECONDS)`). 



