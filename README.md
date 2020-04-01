# Computer Product Database - Case Study
source: Exercises 2.3.1, 2.4.1, 6.1.13, 6.3.8, 6.4.6 and 6.5.1 in A First Course in Database Systems

## Relational Schema
Consider the following database schema:
* Product(maker, model, type)
* PC(model, speed, ram, hd, price)
* Laptop(model, speed, ram, hd, screen, price)
* Printer(model, color, type, price)

The Product relation gives the manufacturer, model number and type (PC, laptop, or printer) of various products. We assume for convenience that model numbers are unique over all manufacturers and product types; that assumption is not realistic, and a real database would include a code for the manufacturer as part of the model number. The PC relation gives for each model number that is a PC the speed (of the processor,
in gigahertz), the amount of RAM (in megabytes), the size of the hard disk (in gigabytes), and the price. The Laptop relation is similar, except that the screen size (in inches) is also included. The Printer relation records for each printer model whether the printer produces color output (true, if so), the process type (laser or ink-jet, typically), and the price.

## Schema Declarations
### Write declarations
Write the following declarations:

a) A suitable schema for relation Product.

b) A suitable schema for relation PC.

c) A suitable schema for relation Laptop.

d) A suitable schema for relation Printer.

e) An alteration to your Printer schema from (d) to delete the attribute color.

f) An alteration to your Laptop schema from (c) to add the attribute od (optical-disk type, e.g., cd or dvd). Let the default value for this attribute be 'none' if the laptop does not have an optical disk.

The schema declarations (CREATE TABLE statements) can be written and the database can be populated with data as shown in the [script file](script.txt).

## Queries
### Write queries
Write the following queries:

a) What PC models have a speed of at least 3.00?

b) Which manufacturers make laptops with a hard disk of at least 100GB?

c) Find the model number and price of all products (of any type) made by manufacturer B.

d) Find the model numbers of all color laser printers.

e) Find those manufacturers that sell Laptops, but not PC's.

!f) Find those hard-disk sizes that occur in two or more PC's.

!g) Find those pairs of PC modeis that have both the same speed and RAM.
A pair should be listed only once; e.g., list (i, j) but not (j, i).

!!h) Find those manufacturers of at least two different computers (PC's or
laptops) with speeds of at least 2.80.

!!i) Find the manufacturer(s) of the computer (PC or laptop) with the highest available speed.

!!j) Find the manufacturers of PC's with at least three different speeds.

!!k) Find the manufacturers who sell exactly three different models of PC.

### Show results
Show the result of your queries using the given data set in the [script file](script.txt).

a) Find the model number, speed, and hard-disk size for all PC's whose price is under $1000.

b) Do the same as (a), but rename the speed column gigahertz and the hd column gigabytes.

c) Find the manufacturers of printers.

d) Find the model number, memory size, and screen size for laptops costing more than $1500.

e) Find all the tuples in the Printer relation for color printers. Remember that color is a boolean-valued attribute.

f) Find the model number and hard-disk size for those PC's that have a speed of 3.2 and a price less than $2000.

### Write queries
Write the following queries. You should use at least one subquery in each of your answers and write each query in two significantly different ways (e.g., using different sets of the operators `EXISTS`, `IN`, `ALL`, and `ANY`).

a) Find the makers of PC's with a speed of at least 3.0.

b) Find the printers with the highest price.

!c) Find the laptops whose speed is slower than that of any PC.

!d) Find the model number of the item (PC, laptop, or printer) with the highest price.

!e) Find the maker of the color printer with the lowest price.

!!f) Find the maker(s) of the PC(s) with the fastest processor among all those
PC's that have the smallest amount of RAM.

### Write queries
Write the following queries and evaluate your queries using the given data set in the [script file](script.txt).

a) Find the average speed of PC's.

b) Find the average speed of laptops costing over $1000.

c) Find the average price of PC's made by manufacturer "A."

!d) Find the average price of PC's and laptops made by manufacturer "D."

e) Find, for each different speed, the average price of a PC.

!f) Find for each manufacturer, the average screen size of its laptops.

!g) Find the manufacturers that make at least three different models of PC.

!h) Find for each manufacturer who sells PC's the maximum price of a PC.

!i) Find, for each speed of PC above 2.0, the average price.

!!j) Find the average hard disk size of a PC for all those manufacturers that
make printers.

## Data Modifications
## Write modifications
Write the following database modifications and describe the effect of the modifications on the given data set.

a) Using two INSERT statements, store in the database the fact that PC model 1100 is made by manufacturer C, has speed 3.2, RAM 1024, hard disk 180, and sells for $2499.
```
INSERT INTO PC VALUES ('1100', 3.2, 1024, 180, 2499);
INSERT INTO Product VALUES('C', '1100', 'pc');
```

!b) Insert the facts that for every PC there is a laptop with the same manufacturer, speed, RAM, and hard disk, a 17-inch screen, a model number 1100 greater, and a price $500 more.
```
INSERT INTO Laptop
SELECT model+1100, speed, ram, hd, 17, price+500
FROM PC;

INSERT INTO Product
SELECT maker, model+1100, 'Laptop'
FROM Product
WHERE type = 'pc';
```

c) Delete all PC's with less than 100 gigabytes of hard disk.
```
DELETE FROM PC
WHERE hd < 100;

```

d) Delete all laptops made by a manufacturer that doesn't make printers.
```
// find manufacturers that make printers
SELECT DISTINCT maker
FROM Product, Printer
WHERE Product.model = Printer.model;

// find laptops made by a manufacturer that doesn't make printers
SELECT Product.model
FROM Product, Laptop
WHERE Product.model = Laptop.model
AND maker NOT IN (
  SELECT DISTINCT maker
  FROM Product, Printer
  WHERE Product.model = Printer.model
);

// delete such laptops
DELETE FROM Laptop
WHERE model IN (
  SELECT Product.model
  FROM Product, Laptop
  WHERE Product.model = Laptop.model
  AND maker NOT IN (
    SELECT DISTINCT maker
    FROM Product, Printer
    WHERE Product.model = Printer.model
  )
);
```

e) Manufacturer A buys manufacturer B. Change all products made by B so they are now made by A.
```
UPDATE Product
SET maker = 'A'
WHERE maker = 'B';
```

f) For each PC, double the amount of RAM and add 60 gigabytes to the amount of hard disk. (Remember that several attributes can be changed by one UPDATE statement.)

!g) For each laptop made by manufacturer B, add one inch to the screen size and subtract $100 from the price.

## Constraints
## Write constraints
Write the following constraints. You may write your constraints either as containments or by equating an expression to the empty set. For the given data set indicate any violations to your constraints.

a) A PC with a processor speed less than 2.00 must not sell for more than $500.

b) A laptop with a screen size less than 15.4 inches must have at least a 100 gigabyte hard disk or sell for less than $1000.

!c) No manufacturer of PC's may also make laptops.

!d) A manufacturer of a PC must also make a laptop with at least as great a
processor speed.

!e) If a laptop has a larger main memory than a PC, then the laptop must also have a higher price than the PC.

## Testing
To setup the database, go to https://sqliteonline.com/ and run in the [script file](script.txt). Then, you can test your SQL statements.
