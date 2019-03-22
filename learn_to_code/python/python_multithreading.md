# Learning To Code: 

# Before We Begin

# LearnMultithreading.py

```

#!/usr/bin/anaconda/python2.7/bin/python
import multiprocessing
import urllib
import threading
import time
import random
import os

from concurrent.futures import ThreadPoolExecutor
from concurrent.futures import ProcessPoolExecutor
import concurrent.futures

myCounter = 1
myLock = threading.Lock()
myTicketsAvailable = 200 # Our Ticket Allocation

def downloadImage(imagePath, fileName):
	print "downloading Image from {}".format(imagePath)
	urllib.urlretrieve(imagePath, fileName)
	
def linearDownload():
	for i in range(10):
		imageName = "image-" + str(i) + ".jpg"
		print imageName
		#myURL = "http://lorempixel.com/400/200/sports"
		myURL = "https://placekitten.com/g/200/400"
		downloadImage(myURL, imageName)

def executeFirstThread(i):
	imageName = "image-" + str(i) + ".jpg"
	print imageName
	myURL = "http://lorempixel.com/400/200/sports"
	#myURL = "https://placekitten.com/g/200/400"
	downloadImage(myURL, imageName)	
	
		 
def firstThreadExample():
	#This is an example of how to make a mulithreaded process
	#This is known as 'concurrent' programming (as opposed to parallel); in concurrent programming, one CPU is shared by multiple threads.
	#Threads of the same process only ever interact with one CPU - so all threads use one and only one CPU.
	#Threads are good to use when most of the wall clock time is used waiting on blocked I/O resources
	startTime = time.time()
	#create an array of references to all threads
	threads = []
	
	#make 10 threads
	for i in range(10):
		thread = threading.Thread(target=executeFirstThread, name='threadWAG-' + str(i), args=(i,))
		threads.append(thread)
		thread.start()

	#This prints all information on the thread
	print "Thread information on all threads: {}".format(threading.enumerate())
	
	#This prints the number of threads.  NOTE: THIS SHOULD ALWAYS BE AT LEAST 1, as it counts the 'main' thread too; if this is 1 it means all sub-threads are completed
	print "Active thread count: {}".format(threading.active_count())
		
	#ensure that all threads in our array have completed their execution
	#this acts as a 'loop' until all are done
	#the join() method stops progress until the thread on deck is finished running.
	#	its possible that, running 10 threads, threads 1-9 are done and we are waiting on 0; when 0 finishes, it will check all proceeding threads (which will be finished) 
	#	and we will then just move on
	for i in threads:
		i.join()
	
	#this is also a loop that waits for the threads to all be complete
	#this may be preferable as you can actively interact with the threads by replacing dead threads with living ones via launching them in the WHILE loop
	while (threading.active_count() > 1):
		time.sleep(2)
		print "Waiting for threads to stop..."
	
	print "Active thread count: {}".format(threading.active_count())
		
	endTime = time.time()
	totalTime = endTime - startTime
	print "Total execution time: {}".format(totalTime)
	
	

def firstProcessExample():
	#This example launches processes instead of threads
	#This is known as 'parallel' programming (as opposed to concurrent); in parallel programming, multiple processes do not share a single CPU but rather
	#use distinct CPUs at the same time,
	#Processes are good to use (instead of threads) for CPU-intensive tasks
	startTime = time.time()
	#create an array of references to all processes
	procs = []
	
	#make 10 processes
	for i in range(10):
		proc = multiprocessing.Process(target=executeFirstThread,args=(i,))
		procs.append(proc)
		proc.start()
		
	#ensure that all threads in our array have completed their execution
	for i in procs:
		i.join()
		
	endTime = time.time()
	totalTime = endTime - startTime
	print "Total execution time: {}".format(totalTime)		
	
def forkedChild():
	print "Ware are in the child process with PID= %d"%os.getpid()
	
def forkParent():
	myVar = 369
	print "We are in the parent process with PID= %d"%os.getpid()
	newRef = os.fork()
	print "PARENT AND CHILD WILL PRINT THIS: {}".format(myVar)
	if newRef == 0:
		forkedChild()
	else:
		print "Ware are in the parent process and our child process has PID= %d"%newRef
		
def forkExample():
	#This shows an example of a fork
	#a fork is an EXACT copy - same variable values, same code, EVEN THE SAME EXECUTION POINT (so the fork starts off where the parent is and then both child and 
	#parent run independently from the same place).
	
	forkParent()
	
def firstDaemon():
	while True:
		print "Sending out heartbeat...This will have to be killed with ctrl-c"
		time.sleep(2)
		
def launchFirstDaemon():
	myDaemon = threading.Thread(target=firstDaemon)
	myDaemon.setDaemon(True) #this is just for housekeeping purposes.....it may not even be necessary
	myDaemon.start()
	

class Producer(threading.Thread):
	"""
	Produces random integers to a list
	"""

	def __init__(self, integers, condition):
		"""
		Constructor.
		@param integers list of integers
		@param condition condition synchronization object
		"""
		threading.Thread.__init__(self)
		self.integers = integers
		self.condition = condition
	
	def run(self):
		"""
		Thread run method. Append random integers to the integers list
		at random time.
		"""
		while True:
			integer = random.randint(0, 256)
			self.condition.acquire()
			print 'condition acquired by %s' % self.name
			self.integers.append(integer) 
			print '%d appended to list by %s' % (integer, self.name)
			print 'condition notified by %s' % self.name
			self.condition.notify()
			print 'condition released by %s' % self.name
			self.condition.release()
			time.sleep(1)

class Consumer(threading.Thread):
	"""
	Consumes random integers from a list
	"""

	def __init__(self, integers, condition):
		"""
		Constructor.
		@param integers list of integers
		@param condition condition synchronization object
		"""
		threading.Thread.__init__(self)
		self.integers = integers
		self.condition = condition
	
	def run(self):
		"""
		Thread run method. Consumes integers from list
		"""
		while True:
			self.condition.acquire()
			print 'condition acquired by %s' % self.name
			while True:
				if self.integers:
					integer = self.integers.pop()
					print '%d popped from list by %s' % (integer, self.name)
					break
				print 'condition wait by %s' % self.name
				self.condition.wait()
			print 'condition released by %s' % self.name
			self.condition.release()

def firstConditionExample():
	integers = []
	condition = threading.Condition()
	t1 = Producer(integers, condition)
	t2 = Consumer(integers, condition)
	t1.start()
	t2.start()
	t1.join()
	t2.join()
	
def lockWorkerA():
	global myCounter
	myLock.acquire()
	print "Lock by 'A' acquired!"
	try:
		while myCounter < 500:
			myCounter += 1
			print("Worker A is incrementing counter to {}".format(myCounter))
			
	finally:
		myLock.release()

def lockWorkerB():
	global myCounter
	myLock.acquire()
	print "Lock by 'B' acquired!"
	try:
		while myCounter > -500:
			myCounter -= 1
			print("Worker B is decrementing counter to {}".format(myCounter))
			
	finally:
		myLock.release()

def firstLockExample():
	t0 = time.time()
	thread1 = threading.Thread(target=lockWorkerA)
	thread2 = threading.Thread(target=lockWorkerB)

	thread1.start()
	thread2.start()

	thread1.join()
	thread2.join()

	t1 = time.time()

	print("Execution Time {}".format(t1-t0))
	
class boundedSemaphore_TicketSeller(threading.Thread):
	ticketsSold = 0

	def __init__(self, semaphore):
		threading.Thread.__init__(self);
		self.sem = semaphore
		print("Ticket Seller Started Work")

	def run(self):
		global myTicketsAvailable
		running = True
		while running:
			self.randomDelay()
			
			self.sem.acquire()
			if(myTicketsAvailable <= 0):
				running = False
			else:
				self.ticketsSold = self.ticketsSold + 1
				myTicketsAvailable = myTicketsAvailable - 1
				print("{} Sold One ({} left)".format(self.getName(), myTicketsAvailable))
			self.sem.release()
		print("Ticket Seller {} Sold {} tickets in total".format(self.getName(), self.ticketsSold))

	def randomDelay(self):
		time.sleep(random.randint(0,4)/4)

def boundedSemaphoreExample():
	# our sempahore primitive
	semaphore = threading.BoundedSemaphore(2)
	
	# our array of sellers
	sellers = []
	for i in range(4):
		seller = boundedSemaphore_TicketSeller(semaphore)
		seller.start()
		sellers.append(seller)

	# joining all our sellers
	for seller in sellers:
		seller.join()
		
def multiplyByTwo(n, letter = ''):
	print letter + ' was the letter sent to me'
	time.sleep(5)
	return 2 * n

def CaptureThreadPoolExec_Results():
	"""
	This shows the use of thread pool executors; using pools cuts down on code, as you do not have to specify which thread needs to take the job - the next free one will.
	The 'max_workers' defines the maximum number of threads - all jobs are stored to the pool, but are only launched once a worker is available
	In the example below, the thread collection is launched with the 'submit' function (which accepts the function - as well as all parameters - to use).
	
	In addition, you can send unique values to the function and capture the return values with the 'as_completed' function. It seems that all values returned are stored to 
	the as_completed() function (so there arent just the same number of elements as defined by max_workers, it will equal the number of submits)
	It seems that 'as_completed' stalls while waiting for mall results - so you can launch all submits, and the 'for' loop associated with 'as_completed' will wait/stall
	until ALL submits have a result (that is to say, you will not see 'MOVE FORWARD - C' until ALL submits have returned a result)
	"""
	numValues = [2,3,4,5,6,7,8]
	letterValues = ['b', 'c', 'd', 'e', 'f', 'g', 'h']
	
	
	with ThreadPoolExecutor(max_workers=3) as executor:
		tasks = []
		print "MOVE FORWARD - A"
		#for numValue in numValues:
			#task = executor.submit(multiplyByTwo, (numValue))
			#tasks.append(task)
		
		for i in range(len(numValues)):
			task = executor.submit(multiplyByTwo, numValues[i], letterValues[i])
			tasks.append(task)
		print "MOVE FORWARD - B"
			
		for result in concurrent.futures.as_completed(tasks):
			print str(result.result()) + ' was the result'
		print "MOVE FORWARD - C"
	print "MOVE FORWARD - D"
			
def ThreadPoolExec_MapExample():
	"""
	Another example of thread pool executors; The 'map' function automatically launches the thread with the 'mapped' value in the provided list; in the example below, 
	the thread collection is launched with the 'map' function.  It seems you can send one variable to the mapped function; if you need to send multiple you will have 
	to use a tuple or a dictionary.
	In addition, you can send unique values to the function and capture the return values.
	"""
	
	numValues = [2,3,4,5,6,7,8]
	
	with ThreadPoolExecutor(max_workers=3) as executor:
		results = executor.map(multiplyByTwo, numValues)
	for result in results:
		print(result)

def ThreadPoolExec_Callback_task(n, aaa=0):
	print("Processing {} with pid {}".format(n, os.getpid()))
	time.sleep(1)
	return 55*n

def ThreadPoolExec_Callback_taskDone(fn, aaa = ''):
	if fn.cancelled():
		print "Our {} Future has been cancelled".format(fn.arg)
	elif fn.done():
		print "Our Task has completed and returned value " + str(fn.result())
		#fn.result()[1] = fn.result()[1]*2

def ThreadPoolExec_Callback():
	"""
	This introduces a 'callback' function to a thread pool. A callback is a function that, once the initial thread is finished, the callback is then called.
	In this example, 'ThreadPoolExec_Callback_task' is the threadded task, and once its done, it calls 'ThreadPoolExec_Callback_taskDone'
	Note only one parameter is sent to 'ThreadPoolExec_Callback_taskDone', and it seems to be the function results from 'ThreadPoolExec_Callback_task'.
	You can tease out if it terminated normally (.done()) or if it was cancelled (.cancelled()); you can also tease out the return value with .result() 
	"""
	myNumber = 17
	print "Starting ThreadPoolExecutor"
	with ThreadPoolExecutor(max_workers=3) as executor:
		future = executor.submit(ThreadPoolExec_Callback_task, 2, myNumber)
		future.add_done_callback(ThreadPoolExec_Callback_taskDone)
	print "All tasks complete"

def ProcessPoolExec_Example():
	"""
	You can use a process pool instead of a thread pool for pretty much everything a thread pool can do; just replace the word 'Process' in place of 'Thread'
	This is an example using callbacks with a process pool

	You can use os.getpid() to get the process ID of the current process in the task function itself, but be warned: the WORKER gets a process ID and it lasts until 
	the program terminates. This means that, for example, if you have max_workers = 3 and you submit 100 things, there will only be 3 unique pids working those
	100 items. 
	"""
	myNumber = 17
	numRuns = 0
	print "Starting ProcessPoolExecutor with pid {}".format(os.getpid())
	while numRuns < 4:
		#This will run this 'with' 4 times; note that EACH time the 'with' is called its a brand-new process pool, with different pids etc allocated for the pool
		with ProcessPoolExecutor(max_workers=3) as executor:
			future = executor.submit(ThreadPoolExec_Callback_task, 2, myNumber)
			future = executor.submit(ThreadPoolExec_Callback_task, 4, myNumber)
			future = executor.submit(ThreadPoolExec_Callback_task, 6, myNumber)
			future = executor.submit(ThreadPoolExec_Callback_task, 8, myNumber)
			future = executor.submit(ThreadPoolExec_Callback_task, 10, myNumber)
			
			#note that since we are constantly over-writing 'future' this callback will only work on the LAST one given! If we wanted it to work on all we would have
			#to do this after every submit()
			future.add_done_callback(ThreadPoolExec_Callback_taskDone)
		print "All processes complete"
		numRuns = numRuns + 1
	
	print "Ending ProcessPoolExecutor with pid {}".format(os.getpid())#this will be the same process ID as the initial one
	
if __name__ == "__main__":
	print "This machine has " + str(multiprocessing.cpu_count()) + " processors."
	#linearDownload()
	#firstThreadExample()
	#firstProcessExample()
	#forkExample()
	#launchFirstDaemon()
	#firstConditionExample()
	#firstLockExample()
	#boundedSemaphoreExample()
	#CaptureThreadPoolExec_Results()
	#ThreadPoolExec_MapExample()
	#ThreadPoolExec_Callback()
	ProcessPoolExec_Example()

```
