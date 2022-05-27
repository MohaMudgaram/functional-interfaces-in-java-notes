# 01 Functional Interfaces
- In Java, an interface specifies one or more methods. The interface is **a contract which must be honored by all implementing classes.** 
- The interface defined in Listing 1-1 specifies methods `method1` and `method2`.
	```
	interface I1
	{
		void method1();
		String method2(String x);
	}
	```
	Listing 1-1
- Any class that implements an interface **must provide implementations for all the methods declared in the interface (or the class must be declared as abstract).**
- Since the class defined in Listing 1-2 provides implementations for both `method1` and `method2`, objects of class `C1` can be instantiated.
	```
	class C1 implements I1
	{
		@Override
		public void method1() {}
		@Override
		public String method2(String x) { return x; }
	}
	```
	Listing 1-2
## Enhancements to Interfaces in Java 8 and Java 9
* * *
- In Java 8, interfaces were allowed to have **static and default methods.**
- A static method has **a single instance associated with the interface. A static method can be called without creation of an object.**
- A default method is **an implementation provided by the interface that does not have to be overridden by an implementing class. Default methods help in the compilation of legacy code.**
- The program in Listing 1-3 defines interface `I2` that declares field `s` which contains the string “I2”. If a constant is common to all classes which implement an interface, it should be defined as a field in the interface. The interface also defines a static and a default method.
- Since `method1` is static, it can be called directly from interface `I2`, without creating an object. When called, it prints “I2”, which is the value of field `s`. Since class `C2` overrides `method2`, the call to `method2` from `objC2` executes the `C2` implementation, which returns the string argument and prints “Hello”. Since class `C3` does not override method2, the call to `method2` from `objC3` executes the default implementation provided in interface `I2`, which concatenates the string argument to field s and prints “I2World”.
	```
	interface I2
	{
		String s = "I2";
		static void method1()
		{
			System.out.println(s);
		}
		default String method2(String x)
		{
			return s + x;
		}
	}
	class C2 implements I2
	{
		@Override
		public String method2(String x) { return x; }
	}
	class C3 implements I2 {}
	class TestI2
	{
		public static void main(String[] args)
		{
			I2.method1();
			I2 objC2 = new C2();
			I2 objC3 = new C3();
			System.out.println(objC2.method2("Hello"));
			System.out.println(objC3.method2("World"));
		}
	}
	```
	Listing 1-3
	PROGRAM OUTPUT:
	```
	I2
	Hello
	I2World
	```
- In Java 9, interfaces were allowed to have **private methods.** **Private methods are useful to call from default methods.** The program in Listing 1-4 concatenates a random integer between 0 and 99 to the string “Hello”. Interface `I3` contains private method `getNumber` which generates the integer. This method is called by default method `M1`.
	```
	import java.util.Random;
	interface I3
	{
		private int getNumber() { return (new Random()).nextInt(100); }
		default String M1(String s)
		{
			return s + getNumber();
		}
	}
	class C4 implements I3
	{
	}
	class TestI3
	{
		public static void main(String[] args)
		{
			I3 objC4 = new C4();
			System.out.println(objC4.M1("Hello"));
		}
	}
	```
	Listing 1-4
	PROGRAM OUTPUT:
	`Hello21`
- In Java 9, interfaces can also have **private static methods**. Since the **static methods of an interface can be called without creation of an implementing object, these methods can only be called by public static methods defined in the interface.**
- Interface `I4` in the following program defines public static method `getName` which calls private static method `getPrefix` to add the prefix “Mr. ” or “Ms. ” based on gender. The `getName` method can be called from the main method of class `TestI4`. The `getPrefix` method can be called only from inside method `getName`.
	```
	interface I4
	{
		private static String getPrefix(String p)
		{
			return p.equals("male")? "Mr. " : "Ms. ";
		}
		public static String getName(String n, String p)
		{
			return getPrefix(p) + n;
		}
	}
	class TestI4
	{
		public static void main(String[] args)
		{
			System.out.println(I4.getName("Smith", "female"));
			System.out.println(I4.getName("Jones", "male"));
		}
	}
	```
	Listing 1-5
	PROGRAM OUTPUT:
	```
	Ms. Smith
	Mr. Jones
	```
## Functional Interfaces Defined
* * *
- A functional interface is **an interface with a single abstract method, called its functional method.**
- The code in Listing 1-6 defines functional interface `StringProcessor`, which specifies functional method process that takes a string argument and returns a string. The `@FunctionalInterface` annotation instructs the compiler to verify that the interface has only one abstract method.
	```
	@FunctionalInterface
	interface StringProcessor
	{
		   String process(String x);
	}
	```
	Listing 1-6
- If `StringProcessor` contained more than one abstract method, the `@FunctionalInterface` annotation would cause a compilation error to be generated. The code in Listing 1-7 demonstrates.
	```
	@FunctionalInterface
	interface StringProcessor
	{
		   String process(String x);
		   String process2(String x);
	}
	```
	Listing 1-7
	PROGRAM OUTPUT:
	```
	ERROR: Not a functional interface.
							   Contains more than one abstract
							   method.
	```
- Functional interfaces are ideal for defining a single problem or operation.
- In Java 8, the API was enhanced to utilize functional interfaces. Many of the functional interfaces can contain static and default methods, making them extendable by the user.
## Implementing Functional Interfaces with Pre-Java 8 Constructs
* * *
- A functional interface can be **implemented by defining a named class that provides the functional method.**
- The class in Listing 1-8 implements functional interface `StringProcessor` by providing an implementation of functional method `process`.
	```
	class NamedStringProcessor implements StringProcessor
	{
		@Override
		public String process(String s) { return s; }
	}
	```
	Listing 1-8
- A functional interface can also be **implemented by an anonymous class that provides the functional method.**
- The following code defines an anonymous class that implements `StringProcessor`. The anonymous implementation is referenced by variable `anonSP`.
	```
	StringProcessor anonSP = new StringProcessor() {
		@Override
		public String process(String x)
		{
			return x.toUpperCase();
		}
	};
	```
- The program in Listing 1-9 provides both named class and anonymous class implementations for functional interface `StringProcessor`.
	```
	public class TestStringProcessor
	{
		public static void main(String[] args)
		{
			NamedStringProcessor namedSP = new NamedStringProcessor();
			StringProcessor anonSP = new StringProcessor() {
				@Override
				public String process(String x)
				{
					return x.toUpperCase();
				}
			};
			System.out.println(namedSP.process("hello"));
			System.out.println(anonSP.process("hello"));
		}
	}
	```
	Listing 1-9
	PROGRAM OUTPUT:
	```
	Hello
	HELLO
	```
## Providing Default Methods in Functional Interfaces
* * *
- A functional interface can provide **default methods**. An implementing class **can use the default methods or provide its own versions**.
- Suppose a program needs to print different receipts based on item, price, discount, and tax. Listing 1-10 defines the `Receipt` class that contains the corresponding fields.
	```
	class Receipt
	{
		String item;
		double price;
		double discount;
		double tax;
		public Receipt(String i, double a, double d, double s)
		{
			item     = i;
			price    = a;
			discount = d;
			tax      = s;
		}
		public Receipt(Receipt r)
		{
			item     = r.item;
			price    = r.price;
			discount = r.discount;
			tax      = r.tax;
		}
	}
	```
	Listing 1-10
- In order to define a framework for receipt printing, a functional interface with a functional method that accepts a `Receipt` argument can be utilized.
- Listing 1-11 defines functional interface `ReceiptPrinter` whose functional method `print` accepts a `Receipt`.
- `ReceiptPrinter` also provides a default method that calculates the total amount based on price, discount, and tax, but allows implementing classes to override the calculation.
- The default method calls private method `getDiscounterPrice` which applies the discount to the price.
	```
	@FunctionalInterface
	interface ReceiptPrinter
	{
		void print(Receipt receipt);
		private double getDiscountedPrice(Receipt receipt)
		{
			return receipt.price
				- (receipt.price * receipt.discount);
		}
		default double computeTotal(Receipt receipt)
		{
			double discountedPrice = getDiscountedPrice(receipt);
			return discountedPrice + (discountedPrice * receipt.tax);
		}
	}
	```
	Listing 1-11
- The following `ReceiptPrinter` implementation prints each `Receipt` field on a separate line followed by the total. It uses the default total calculation.
	```
	ReceiptPrinter simpleReceiptPrinter = new ReceiptPrinter() {
		@Override
		public void print(Receipt receipt)
		{
			System.out.println("\nItem :\t" + receipt.item);
			System.out.println("Price:\t"   + receipt.price);
			System.out.println("Disc:\t"    + receipt.discount);
			System.out.println("Tax:\t"     + receipt.tax);
			System.out.println("Total:\t"   + computeTotal(receipt));
		}
	};
	```
- The following `ReceiptPrinter` implementation is for merchants who are exempt from tax. It provides its own implementation of the total calculation which does not include tax.
	```
	ReceiptPrinter exemptReceiptPrinter = new ReceiptPrinter() {
		@Override
		public void print(Receipt receipt)
		{
			System.out.println("\nItem :\t" + receipt.item);
			System.out.println("Price:\t"   + receipt.price);
			System.out.println("Disc:\t"    + receipt.discount);
			System.out.println("Total:\t"   + computeTotal(receipt));
		}
		@Override
		public double computeTotal(Receipt receipt)
		{
			return receipt.price - (receipt.price * receipt.discount);
		}
	};
	```
- The `simpleReceiptPrinter` and `exemptReceiptPrinter` implementations can be used to print the same receipt. The following code prints one copy of the receipt whose total is based on tax and a second copy of the receipt whose total is not based on tax:
	```
	Receipt receipt = new Receipt("shirt", 20.00, 0.05, 0.07);
	simpleReceiptPrinter.print(receipt);
	exemptReceiptPrinter.print(receipt);
	```
	OUTPUT:
	```
	Item :    shirt
	Amount:   20.0
	Disc:     0.05
	Tax:      0.07
	Total:    20.33
	Item :    shirt
	Amount:   20.0
	Disc:     0.05
	Total:    19.0
	```
## Providing Static Methods in Functional Interfaces
* * *
- A functional interface can also have **static methods**. Static methods are **useful to define helper methods that are not meant to be overridden by implementing classes.**
- Listing 1-12 enhances the `StringProcessor` interface by adding helper methods to check if a string is uppercase or lowercase.
	```
	@FunctionalInterface
	interface StringProcessor
	{
		String process(String s);
		static boolean isLowerCase(String s)
		{
			boolean result = true;
			for (int i = 0; i < s.length() && result; ++i)
				result &= Character.isLowerCase(s.charAt(i));
			return result;
		}
		static boolean isUpperCase(String s)
		{
			boolean result = true;
			for (int i = 0; i < s.length() && result; ++i)
				result &= Character.isUpperCase(s.charAt(i));
			return result;
		}
	}
	```
	Listing 1-12
- In the following example, one `StringProcessor` implementation is provided whose functional method converts a string to lowercase, and another implementation is provided that converts a string to uppercase:
	```
	StringProcessor toLowerCase = new StringProcessor() {
		@Override
		public String process(String s)
		{
			return s.toLowerCase();
		}
	};
	StringProcessor toUpperCase = new StringProcessor() {
		@Override
		public String process(String s)
		{
			return s.toUpperCase();
		}
	};
	```
- The static methods can be used to verify the case of the processed string.
	```
	String s = toLowerCase.process("FUNCTIONALINTERFACES");
	System.out.println(s);
	System.out.println("Lower case: " + StringProcessor.isLowerCase(s));
	System.out.println("Upper case: " + StringProcessor.isUpperCase(s));
	String t = toUpperCase.process(s);
	System.out.println("\n" + t);
	System.out.println("Lower case: " + StringProcessor.isLowerCase(t));
	System.out.println("Upper case: " + StringProcessor.isUpperCase(t));
	```
	OUTPUT:
	```
	functionalinterfaces
	Lower case: true
	Upper case: false
	FUNCTIONALINTERFACES
	Lower case: false
	Upper case: true
	```
## Generic Functional Interfaces
* * *
- Many of the functional interfaces defined in the Java API are generic for one or more types. Suppose a program needs methods that take two arguments of a type and returns a result of the same type, but both the type and the operation performed can vary.
- A functional interface can be defined that is **generic for type X and has a functional method that accepts two arguments of type X and returns a value of type X.**
	```
	@FunctionalInterface
	interface TwoArgsProcessor<X> {
		X process(X arg1, X arg2);
	}
	```
	Listing 1-13
- The program simply needs to provide different implementations of TwoArgsProcessor and call the process method of each.
- The program in Listing 1-14 declares a `TwoArgsProcessor<Integer>` implementation that multiplies two Integers. It also declares a `TwoArgsProcessor<Double>` implementation that adds two Doubles. Lastly, it declares a `TwoArgsProcessor<String>` implementation that compares two strings and returns the largest.
	```
	class TestTwoArgsProcessor {
		public static void main(String[] args)
		{
			TwoArgsProcessor<Integer> multiplyInts
				= new TwoArgsProcessor<>() {
					@Override
					public Integer process(Integer arg1, Integer arg2)
					{
						return arg1 * arg2;
					}
				};
			TwoArgsProcessor<Double> addDoubles
				= new TwoArgsProcessor<>() {
					@Override
					public Double process(Double arg1, Double arg2)
					{
						return arg1 + arg2;
					}
				};
			TwoArgsProcessor<String> compareStrings
				= new TwoArgsProcessor<>() {
					@Override
					public String process(String arg1, String arg2)
					{
						return arg1.compareTo(arg2) > 0? arg1: arg2;
					}
				};
			System.out.println(multiplyInts.process(2,3));
			System.out.println(addDoubles.process(4.1,5.2));
			System.out.println(compareStrings.process("ace","age"));
		}
	}
	```
	Listing 1-14
	PROGRAM OUTPUT:
	```
	6
	9.3
	age
	```
### Generic Functional Interfaces with Type Restrictions
* * *
- Generic functional interfaces can be restricted to certain types.
- The class in Listing 1-15 extends the Receipt class to include county tax.
	```
	public class CountyReceipt extends Receipt
	{
		double countyTax;
		public CountyReceipt(Receipt r, double c)
		{
			super(r);
			countyTax = c;
		}
	}
	```
	Listing 1-15
- As a result, the version of `ReceiptPrinter` given in Listing 1-11 is no longer adequate. It should be rewritten as in Listing 1-16 to be generic for classes that extend `Receipt`.
	```
	@FunctionalInterface
	public interface ReceiptPrinter<X extends Receipt>
	{
		void print(X receipt);
		private double getDiscountedPrice(X receipt)
		{
			return receipt.price
				- (receipt.price * receipt.discount);
		}
		default double computeTotal(X receipt)
		{
			double discountedPrice = getDiscountedPrice(receipt);
			return discountedPrice + (discountedPrice * receipt.tax);
		}
	}
	```
	Listing 1-16
- `simpleReceiptPrint` also needs to be rewritten as an implementation of `ReceiptPrinter<Receipt>`.
	```
	ReceiptPrinter<Receipt> simpleReceiptPrinter
		= new ReceiptPrinter<> () {
			@Override
			public void print(Receipt receipt)
			{
				System.out.println("\nItem :\t" + receipt.item);
				System.out.println("Price:\t"   + receipt.price);
				System.out.println("Disc:\t"    + receipt.discount);
				System.out.println("Tax:\t"     + receipt.tax);
				System.out.println("Total:\t"   + computeTotal(receipt));
			}
	};
	```
- The following code provides an implementation of `ReceiptPrinter<CountyReceipt>` that includes the county tax. The total calculation is overridden to include county tax.
	```
	ReceiptPrinter<CountyReceipt> countyReceiptPrinter
		= new ReceiptPrinter<> () {
			@Override
			public void print(CountyReceipt receipt)
			{
				System.out.println("\nItem :\t" + receipt.item);
				System.out.println("Price:\t"   + receipt.price);
				System.out.println("Disc:\t"    + receipt.discount);
				System.out.println("Tax:\t"     + receipt.tax);
				System.out.println("CnTax:\t"   + receipt.countyTax);
				System.out.println("Total:\t"   + computeTotal(receipt));
			}
			@Override
			public double computeTotal(CountyReceipt receipt)
			{
				double discountedPrice = receipt.price
									- (receipt.price * receipt.discount);
				return discountedPrice
					+ (discountedPrice * receipt.tax)
					+ (discountedPrice * receipt.countyTax);
			}
	};
	```
- The following code uses the two implementations defined previously to print two receipts from the same transaction. Only the second receipt contains county tax.
	```
	Receipt receipt = new Receipt("shirt", 20.00, 0.05, 0.07);
	simpleReceiptPrinter.print(receipt);
	CountyReceipt countyReceipt = new CountyReceipt(receipt, 0.04);
	countyReceiptPrinter.print(countyReceipt);
	```
	OUTPUT:
	```
	Item :    shirt
	Price:    20.0
	Disc:     0.05
	Tax:      0.07
	Total:    20.33
	Item :    shirt
	Price:    20.0
	Disc:     0.05
	Tax:      0.07
	CnTax:    0.04
	Total:    21.09
	```
## Specializing a Generic Functional Interface
* * *
- If the generic functional interface of a particular type is used frequently, it is convenient to specialize it for that type. Many examples exist in the Java API.
- Specialization is accomplished by **extending or implementing the generic functional interface of one type.** The resulting interface or class is not generic for that type.
- Functional interface `TwoArgsProcessor` can be specialized for type Integer by defining a new functional interface that only processes Integers.
	```
	@FunctionalInterface
	public interface TwoIntsProcessor
		extends TwoArgsProcessor<Integer>
	{
	}
	```
- `TwoArgsProcessor` can also be specialized by creating an abstract class that processes Integers.
	```
	abstract class TwoIntsProcessorAbstract
		implements TwoArgsProcessor<Integer>
	{
	}
	```
- The program in Listing 1-17 demonstrates that the functional interface `TwoIntsProcessor` is implemented by `multiplyInts` and `subtractInts` and abstract class `TwoIntsProcessorAbstract` is extended by `divideInts`.
	```
	@FunctionalInterface
	public interface TwoIntsProcessor
		extends TwoArgsProcessor<Integer>
	{
	}
	abstract class TwoIntsProcessorAbstract
		implements TwoArgsProcessor<Integer>
	{
	}
	class TestTwoIntsProcessor {
		public static void main(String[] args)
		{
			TwoIntsProcessor multiplyInts = new TwoIntsProcessor() {
					@Override public Integer process(Integer arg1,
													 Integer arg2)
					{
						return arg1 * arg2;
					}
				};
			TwoIntsProcessor subtractInts = new TwoIntsProcessor() {
					@Override public Integer process(Integer arg1,
													 Integer arg2)
					{
						return arg1 - arg2;
					}
				};
			TwoIntsProcessorAbstract divideInts
				= new TwoIntsProcessorAbstract() {
					@Override public Integer process(Integer arg1,
													 Integer arg2)
					{
						return arg1 / arg2;
					}
				};
			System.out.println(multiplyInts.process(2,3));
			System.out.println(subtractInts.process(5,2));
			System.out.println(divideInts.process(10,2));
		}
	}
	```
	Listing 1-17
	PROGRAM OUTPUT:
	```
	6
	3
	5
	```
## PROJECT 1: Playful Pets
* * *
### Problem Statement
* * *
- The Playful Pets pet store wants a program to search for pets currently in the store.
- The information stored for each pet is name, animal, breed, color, and price.
- When performing a search, the program should list the first pet that matches the criterion and then list all the pets that match the criterion.
- The most common search criteria are search by breed and search by price.
- Use the program to search for poodles and to search for pets for $800 or less.
### Solution
* * *
- `Pet.java`
	```
	import java.util.*;
	public class Pet
	{
		String name;
		String animal;
		String breed;
		String color;
		double price;
		static List<Pet> pets = new ArrayList<>();
		public Pet(String n, String a, String b, String c, double p)
		{
			name   = n;
			animal = a;
			breed  = b;
			color  = c;
			price  = p;
		}
		@Override
		public String toString()
		{
			return name + ":" + " a " + color + " " + breed
				 + " " + animal + " for $" + price;
		}
		@Override
		public boolean equals(Object o)
		{
			Pet p = (Pet)o;
			return animal.equals(p.animal) && breed.equals(p.breed);
		}
	}
	```
	Listing 1-18
- A functional interface whose functional method matches a search criterion can be written.
- `PetMatcher`'s `match` method returns a list of pets.
- `PetMatcher` should also have a default method called `first` that uses the `Pet` class’s `equals` method to find the first match, therefore matching by animal and breed.
	```
	@FunctionalInterface
	interface PetMatcher
	{
		List<Pet> match(Pet pet);
		default Pet first(Pet pet)
		{
			int index = Pet.pets.indexOf(pet);
			return index > -1? Pet.pets.get(index) : null;
		}
	}
	```
- An implementation of `PetMatcher` that searches by animal and breed can be provided. This implementation will use the default implementation of the first method.
	```
	PetMatcher breedMatcher = new PetMatcher() {
		@Override
		public List<Pet> match(Pet pet)
		{
			List<Pet> matches = new ArrayList<>();
			for (Pet p : Pet.pets)
				if (p.equals(pet))
					matches.add(p);
			return matches;
		}
	};
	```
- An implementation of `PetMatcher` that searches by price can also be provided. This implementation defines its own implementation of the first method.
	```
	PetMatcher priceMatcher = new PetMatcher() {
		@Override
		public List<Pet> match(Pet pet)
		{
			List<Pet> matches = new ArrayList<>();
			for (Pet p : Pet.pets)
				if (p.price <= pet.price)
					matches.add(p);
			return matches;
		}
		@Override
		public Pet first(Pet pet)
		{
			int index = -1;
			for(Pet p : Pet.pets)
				if (p.price <= pet.price)
					return p;
			return null;
		}
	};
	```
- The complete program is shown and demonstrated as follows. The `breedMatcher` implementation is used to find the poodles in the store. This returns the first poodle, Scruffy, followed by a list containing Scruffy and Max.
- Then, the `priceMatcher` implementation is used to find pets available in the store for $800 or less. This returns the first match, Meow the cat, followed by a list containing Meow the cat, Max the poodle, and Slider the snake.
	```
	import java.util.*;
	@FunctionalInterface
	interface PetMatcher
	{
		List<Pet> match(Pet pet);
		default Pet first(Pet pet)
		{
			int index = Pet.pets.indexOf(pet);
			return index > -1? Pet.pets.get(index) : null;
		}
	}
	class PlayfulPets
	{
		private static void matchPet(String criteria,
									 PetMatcher matcher, Pet pet)
		{
			System.out.println("\n" + criteria + ":");
			System.out.println("First: " + matcher.first(pet));
			System.out.println("All matches:");
			List<Pet> matches = matcher.match(pet);
			for (Pet p : matches)
				System.out.println(p);
		}
		public static void main(String[] args)
		{
			Pet.pets.add(new Pet("Scruffy","dog","poodle",
								 "white",895.00));
			Pet.pets.add(new Pet("Meow","cat","siamese","white",740.25));
			Pet.pets.add(new Pet("Max","dog","poodle","black",540.50));
			Pet.pets.add(new Pet("Cuddles","dog","pug","black",1282.75));
			Pet.pets.add(new Pet("Slider","snake","garden",
								 "green",320.00));
			PetMatcher breedMatcher = new PetMatcher() {
				@Override
				public List<Pet> match(Pet pet)
				{
					List<Pet> matches = new ArrayList<>();
					for (Pet p : Pet.pets)
						if (p.equals(pet))
							matches.add(p);
					return matches;
				}
			};
			PetMatcher priceMatcher = new PetMatcher() {
				@Override
				public List<Pet> match(Pet pet)
				{
					List<Pet> matches = new ArrayList<>();
					for (Pet p : Pet.pets)
						if (p.price <= pet.price)
							matches.add(p);
					return matches;
				}
				@Override
				public Pet first(Pet pet)
				{
					int index = -1;
					for(Pet p : Pet.pets)
						if (p.price <= pet.price)
							return p;
					return null;
				}
			};
			matchPet("Poodles",breedMatcher,
					 new Pet(null, "dog", "poodle", null, 0.0));
			matchPet("Pets for $800 or less",priceMatcher,
					 new Pet(null, null, null, null, 800.0));
		}
	}
	```
	Listing 1-19
	PROGRAM OUTPUT:
	```
	Poodles:
	First: Scruffy: a white poodle dog for $895.0
	All matches:
	Scruffy: a white poodle dog for $895.0
	Max: a black poodle dog for $540.5
	Pets for $800 or less:
	First: Meow: a white siamese cat for $740.25
	All matches:
	Meow: a white siamese cat for $740.25
	Max: a black poodle dog for $540.5
	Slider: a green garden snake for $320.0
	```
## Short Problems
* * *
- Write a functional interface named `InputStreamOpener` whose functional method, named `open`, accepts a String `argument` and returns an `InputStream`. Write an implementation that opens a `DataInputStream`. Write a second implementation that opens an `ObjectInputStream`. Write a third implementation that opens a `BufferedInputStream`. Use anonymous classes for all implementation. Demonstrate the implementation in a main program.
- Write a generic functional interface named `Summer`, and use it to compute the sum of two `Integers`, the sum of two `Doubles`, and the sum of two `Longs`.
- Write a functional interface named `ListManipulator` that is generic for type `T`. Its functional method, named `manipulate`, accepts a `List<T>` and a `T` as arguments and returns void. `ListManipulator` has a default method named `create` that creates an `ArrayList<T>`. The default method takes no arguments and returns a `List<T>`.
	- Write three implementations of `ListManipulator`:
		- `sListAdd` which implements `ListManipulator<String>` and whose functional method adds an element of type `T` to the end of the list
		- `iListAdd` which implements `ListManipulator<Integer>` and whose functional method adds an element of type `T` to the end of the list
		- `iListRmv` which implements `ListManipulator<Integer>` and whose functional method removes the first occurrence of an element of type `T` from the list
		- Implementations `iListAdd` and `iListRmv` override default method `create` with an implementation that sets the initial `ArrayList` capacity to 50 elements.
	- Demonstrate the three implementations in a main program.
## Long Problems
* * *
- Write a functional interface named `ToString` that is generic for type `T`. Its functional method, named `convert`, converts a `T` to a `String`.
	- Write two implementations of `ToString`:
		- `l2s` which converts a `List<String>` by creating a comma-separated string
		- `m2s` which converts a `Map<String,Integer>` by creating a comma-separated field for each map entry with a colon between the key and the value
	- Demonstrate the two implementations in a main program.
- Write a functional interface named `Area`, and use it to compute the area of a circle, a rectangle, and an isosceles right triangle. Include a `numberOfSides` method that by default returns 4.
- A typical used car’s list value is its original price minus $1000 per year since its manufacturing minus $500 for every 10,000 miles driven. A used sports car’s list value adds back a $250 vintage factor for every year since its manufacturing.
	- Write a functional interface named `ListValue` which computes the list value for both typical cars and sports cars.