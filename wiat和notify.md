#wait，notify
	对于wait，notify方法，是object中的方法，不是Thread类中的方法，所以在执行时需要有个对象。
	wait和notify必须在同步代码块中执行，因为wait方法会释放锁，将自己得到的synchronized独占锁释放掉，让自己等待，所以没有锁怎么去释放呢，对于notify来说，
	会唤醒某个等待的线程，但是，仅仅只是唤醒，还并没有释放自己的锁，也就是说执行完notify以后，代码会继续执行，直到代码执行结束，当前的锁释放出来，被唤醒的线程才能拿到这个锁，才能继续执行。
	这就是wait方法和notify方法需要在synchronized中才能进行的原因。
	
join方法其实底层调用的就是wait方法，只是为什么没有看到notify方法，将其唤醒呢？因为对于thread1.join（）来说，我们是主线程等待thread1线程执行完毕，也就是thread1线程加入我们，join us，那么就是我们的主线程进行了thread1.wait（），持有了这个thread1对象的锁，而对于线程对象来说，我们在执行完一个线程以后，在Thread类中就有native方法，对持有这个对象锁的线程进行notify。这就是join方法底层不需要notify，也可以唤醒的原理。