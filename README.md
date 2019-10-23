# Concurrency
Implement threads & parallel array

    Author: Luc Nguyen
    Class: CEN 3024C
    Profesor: Gossai

    import java.util.Random;

    //Extending thread class
    public class Concurrency extends Thread {

	//Create array list
	private int[] array;
	//Integer values
	private int low, high, partial;

	//Create threads
	public Concurrency(int[] array, int low, int high) {
		this.array = array;
		this.low = low;
		this.high = Math.min(high, array.length);
	}

	public int getPartial() {
		return partial;
	}
	//Runnable interface
	public void run() {
		partial = sum(array, low, high);
	}
	//Create array sum
	public static int sum(int[] arr) {
		return sum(arr, 0, arr.length);
	}

	//Both private integer methods
	public static int sum(int[] arr, int low, int high) {
		
		int total = 0;
		for (int i = low; i < high; i++) {
			total += arr[i];
		}
		return total;
	}
	//Parallel array sum
	public static int parallelSum(int[] arr) {
		return parallelSum(arr, Runtime.getRuntime().availableProcessors());
	}
	//Parallel methods
	public static int parallelSum(int[] arr, int threads) {
		int size = (int) Math.ceil(arr.length * 1.0 / threads);

		Concurrency[] sums = new Concurrency[threads];

		for (int i = 0; i < threads; i++) {
			sums[i] = new Concurrency(arr, i * size, (i + 1) * size);
			sums[i].start();
		//Create try catch for error
		}
		try {
			for (Concurrency sum : sums) {
				sum.join();
				}
		} catch (InterruptedException e) {
			
		}
		//Get total and return total
		int total = 0;
		for (Concurrency sum : sums) {
			total += sum.getPartial();
			}
		return total;
	}
	
	//Create random number
	public static void main(String[] args) {
		Random rand = new Random();

		int[] arr = new int[200000000];

		for (int i = 0; i < arr.length; i++) {
			arr[i] = rand.nextInt() + 1;
		}
		//Display the time and sum from the random array list
		long start = System.currentTimeMillis();

		Concurrency.sum(arr);
		System.out.println("Time: " + (System.currentTimeMillis() - start));
		start = System.currentTimeMillis();

		Concurrency.parallelSum(arr);
		System.out.println("Sum: " + (System.currentTimeMillis() - start));
    	}
    }
