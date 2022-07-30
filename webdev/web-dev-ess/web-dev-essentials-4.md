# Web Development Essentials - Topic 4: JavaScript Execution and Syntax

## Lesson 4-1 JavaScript Execution and Syntax

Web pages are developed using three standard technologies: HTML, CSS, and JavaScript. JavaScript is a programming language that lets the browser dynamically update website content. The JavaScript is usually executed by the same browser used to view a webpage. This means that, similar to CSS and HTML, the exact behavior of any code you write might differ between browsers. But most common browsers adhere to the ECMAScript specification. This is a standard that unifies JavaScript usage in the web, and will be the basis for the lesson, together with the HTML5 specification, which specifies how JavaScript needs to be put into a webpage for a browser to execute it.

### Running JavaScript in the Browser
To execute JavaScript, the browser needs to obtain the code either directly, as part of the HTML that makes up the web page, or as a URL that indicates a location for a script to be executed.

The following example shows how to include code directly in the HTML file:

```
<html>
  <head>
  </head>
  <body>
    <h1>Website Headline</h1>
    <p>Content</p>

    <script>
      console.log('test');
    </script>

  </body>
</html>
```

The code is wrapped between `<script>` and `</script>` tags. Everything included in these tags will be executed by the browser directly when loading the page.

The position of the `<script>` element within the page dictates when it will be executed. An HTML document is parsed from top to bottom, with the browser deciding when to display elements on the screen. In the example just shown, the `<h1>` and `<p>` tags of the website are parsed, and likely displayed, before the script runs. If the JavaScript code within the `<script>` tag would take very long to execute, the page would still display without any problem. If, however, the script had been placed above the other tags, the web page visitor would have to wait until the script finishes executing before seeing the page. For this reason, `<script>` tags are usually placed in one of two places:

- At the very end of the HTML body, so that the script is the last thing that gets executed. Do this when the code adds something to the page that would not be useful without the content. An example would be adding functionality to a button, as the button has to exist for the functionality to make sense.

- Inside the `<head>` element of the HTML. This ensures that the script is executed before the HTML body is parsed. If you want to change the loading behavior of the page, or have something that needs to be executed while the page is still not fully loaded, you can put the script here. Also, if you have multiple scripts that depend on a particular script, you can put that shared script in the head to make sure it’s executed before other scripts.

For a variety of reasons, including manageability, it is useful to put your JavaScript code into separate files that exist outside of your HTML code. External JavaScript files are included using a `<script>` tag with a `src` attribute, as follows:

```
<html>
  <head>
    <script src="/button-interaction.js"></script>
  </head>
  <body>
  </body>
</html>
```

The `src` tag tells the browser the location of the source, meaning the file containing the JavaScript code. The location can be a file on the same server, as in the example above, or any web-accessible URL such as `https://www.lpi.org/example.js`. The value of the `src` attribute follows the same convention as the import of CSS or image files, in that it can be relative or absolute. Upon encountering a script tag with the `src` attribute, the browser will then attempt to obtain the source file by using an HTTP `GET` request, so external files need to be accessible.

When you use the `src` attribute, any code or text that is placed between the `<script>`…​`</script>` tags is ignored, according to the HTML specification.

```
<html>
  <head>
    <script src="/button-interaction.js">
      console.log("test"); // <-- This is ignored
    </script>
  </head>
  <body>
  </body>
</html>
```

There are other attributes you can add to the `script` tag to further specify how the browser should get the files, and how it should handle them afterwards. The following list goes into detail about the more important attributes:

`async`
- Can be used on `script` tags and instructs the browser to fetching the script in the background, so as not to block the page load process. The page load will still be interrupted while after the browser obtains the script, because the browser has to parse it, which is done immediately once the script has been fetched completely. This attribute is Boolean, so writing the tag like `<script async src="/script.js"></script>` is sufficient and no value has to be provided.

`defer`
- Similar to `async`, this instructs the browser not to block the page load process while fetching the script. Rather than that, the browser will defer parsing the script. The browser will wait until the entire HTML document has been parsed and only then it will parse the script, before announcing that the document has been to be completely loaded. Like `async`, `defer` is a Boolean attribute and is used in the same way. Since `defer` implies `async`, it is not useful to specify both tags together.

```
Note
When a page is completely parsed, the browser indicates that it is ready to display by triggering a DOMContentLoaded event, when the visitor will be able to see the document. Thus, the JavaScript included in a <head> event always has a chance to act on the page before it is displayed, even if you include the defer attribute.
```

`type`
Denotes what type of script the browser should expect within the tag. The default is JavaScript (`type="application/javascript"`), so this attribute isn’t necessary when including JavaScript code or pointing to a JavaScript resource with the `src` tag. Generally, all MIME types can be specified, but only scripts that are denoted as JavaScript will be executed by the browser. There are two realistic use cases for this attribute: telling the browser not to execute the script by setting `type` to an arbitrary value like `template` or other, or telling the browser that the script is an ES6 module. We don’t cover ES6 modules in this lesson.

```
Warning
When multiple scripts have the async attribute, they will be executed in the order they finish downloading, not in the order of the script tags in the document. The defer attribute, on the other hand, maintains the order of the script tags.
```

### Browser Console
While usually executed as part of a website, there is another way of executing JavaScript: through the browser console. All modern desktop browsers provide a menu through which you can execute JavaScript code in the browser’s JavaScript engine. This is usually done for testing new code or to debug existing websites.

There are multiple ways to access the browser console, depending on the browser. The easiest way is through keyboard shortcuts. The following are the keyboard shorcuts for some of the browsers currently in use:

Chrome
- `Ctrl`+`Shift`+`J` (`Cmd`+`Option`+`J` on Mac)

Firefox
- `Ctrl`+`Shift`+`K` (`Cmd`+`Option`+`K` on Mac)

Safari
- `Ctrl`+`Shift`+`?` (`Cmd`+`Option`+`?` on Mac)

You can also right-click on a webpage and select the “Inspect” or “Inspect Element” option to open the inspector, which is another browser tool. When the inspector opens, a new panel will appear. In this panel, you can select the “Console” tab, which will bring up the browser console.

Once you pull up the console, you can execute JavaScript on the page by typing the JavaScript directly into the input field. The result of any executed code will be shown in a separate line.

### JavaScript Statements
Now that we know how to start executing a script, we will cover the basics of how a script is actually executed. A JavaScript script is a collection of statements and blocks. An example statement is `console.log('test')`. This instruction tells the browser to output the word `test` to the browser console.

Every statement in JavaScript is terminated by a semicolon (`;`). This tells the browser that the statement is done and a new one can be started. Consider the following script:

```
var message = "test"; console.log(message);
```

We have written two statements. Every statement is terminated either by a semicolon or by the end of the script. For readability purposes, we can put the statements on separate lines. This way, the script could also be written as:

```
var message = "test";
console.log(message);
```

This is possible because all whitespace between statements, such as a space, a newline, or a tab, is ignored. Whitespace can also often be put between individual keywords within statements, but this will be further explained in an upcoming lesson. Statements can also be empty, or comprised only of whitespace.

If a statement is invalid because it has not been terminated by a semicolon, ECMAScript makes an attempt to automatically insert the proper semicolons, based on a complex set of rules. The most important rule is: If an invalid statement is composed of two valid statements separated by a newline, insert a semicolon at the newline. For instance, the following code does not form a valid statement:

```
console.log("hello")
console.log("world")
```

But a modern browser will automatically execute it as if it were written with the proper semicolons:

```
console.log("hello");
console.log("world");
```

Thus, it is possible to omit semicolons in certain cases. However, as the rules for automatic semicolon insertion are complex, we advise you always to properly terminate your statements to avoid unwanted errors.

### JavaScript Commenting
Big scripts can get quite complicated. You might want to comment on what you are writing, to make the script easier to read for other people, or for yourself in the future. Alternatively, you might want to include meta information in the script, such as copyright information, or information about when the script was written and why.

To make it possible to include such meta information, JavaScript supports comments. A developer can include special characters in a script that denote certain parts of it as a comment, which will be skipped on execution. The following is a heavily commented version of the script we saw earlier.

```
/*
 This script was written by the author of this lesson in May, 2020.
 It has exactly the same effect as the previous script, but includes comments.
*/

// First, we define a message.
var message = "test";

console.log(message); // Then, we output the message to the console.
```

Comments are not statements and do not need to be terminated by a semicolon. Instead, they follow their own rules for termination, depending on the way the comment is written. There are two ways to write comments in JavaScript:

Multi-line comment
Use `/*` and `*/` to signal the start and end of a multi-line comment. Everything after `/*`, until the first occurrence of `*/` is ignored. This kind of comment is generally used to span multiple lines, but it can also be used for single lines, or even within a line, like so:

```
console.log(/* what we want to log: */ "hello world")
```

Because the goal of comments is generally to increase the readability of a script, you should avoid using this comment style within a line.

Single-line comment
- Use `//` (two forward slashes) to comment out a line. Everything after the double-slash on the same line is ignored. In the example shown earlier, this pattern is first used to comment an entire line. After the `console.log(message);` statement, it is used to write a comment on the rest of the line.

In general, single-line comments should be used for single lines, and multi-line comments for multiple lines, even if it possible to use them in other ways. Comments within a statement should be avoided.

Comments can also be used to temporarily remove lines of actual code, as follows:

```
// We temporarily want to use a different message
// var message = "test";
var message = "something else";
```

___

## Lesson 4.2 JavaScript Data Structures

Programming languages, like natural languages, represent reality through symbols that are combined into meaningful statements. The reality represented by a programming language is the machine’s resources, such as processor operations, devices, and memory.

Each of the myriad programming languages adopts a paradigm for representing information. JavaScript adopts conventions typical of high-level languages, where most of details such as memory allocation are implicit, allowing the programmer to focus on the script’s purpose in the context of the application.

### High-Level Languages
High-level languages provide abstract rules so that the programmer needs to write less code to express an idea. JavaScript offers convenient ways to make use of computer memory, using programming concepts that simplify the writing of recurring practices and that are generally sufficient for the web developer’s purpose.

```
Note
Although it’s possible to use specialized mechanisms for meticulous memory access, the simpler types of data we’ll look at are more general in use.
```

Typical operations in a web application consist of requesting data through some JavaScript instruction and storing it to be processed and eventually presented to the user. This storage is quite flexible in JavaScript, with suitable storage formats for each purpose.

### Declaration of Constants and Variables
The declaration of constants and variables to hold data is the cornerstone of any programming language. JavaScript adopts the convention of most programming languages, assigning values to constants or variables with the `name = value` syntax. The constant or variable on the left takes the value on the right. The constant or variable name must start with a letter or underscore.

The type of data stored in the variable does not need to be indicated, because JavaScript is a dynamic typing language. The type of the variable is inferred from the value assigned to it. However, it is convenient to designate certain attributes in the declaration to guarantee the expected result.

```
Note
TypeScript is a JavaScript-inspired language that, like lower-level languages, allows you to declare variables for specific types of data.
```

### Constants
A constant is a symbol that is assigned once when the program starts and never changes. Constants are useful to specify fixed values such as defining the constant `PI` to be `3.14159265`, or `COMPANY_NAME` to hold the name of your company.

In a web application, for example, take a client that receives weather information from a remote server. The programmer may decide that the address to the server must be constant, because it will not change during the application’s execution. The temperature information, however, can change with every new data arrival from the server.

The interval between queries made to the server can also be defined as a constant, which can be queried from any part of the program:

```
const update_interval = 10;

function setup_app(){
  console.log("Update every " + update_interval + "minutes");
}
```

When invoked, the `setup_app()` function displays the message `Update every 10 minutes` message on the console. The term `const` placed before the name `update_interval` makes sure that its value will remain the same throughout the entire execution of the script. If an attempt is made to reset the value of a constant, a `TypeError: Assignment to constant variable` error is issued.

### Variables
Without the term `const`, JavaScript automatically assumes that `update_interval` is a variable and that its value can be modified. This is equivalent to declaring the variable explicitly with `var`:

```
var update_interval;
update_interval = 10;

function setup_app(){
  console.log("Update every " + update_interval + "minutes");
}
```

Note that although the `update_interval` variable was defined outside the function, it was accessed from within the function. Any constant or variable declared outside of functions or code blocks defined by braces `({})` has global scope; that is, it can be accessed from any part of the code. The opposite is not true: a constant or variable declared inside a function has local scope, so it is accessible only from inside the function itself. Bracket-delimited code blocks, such as those placed in `if` decision structures or `for` loops, delimit the scope of constants, but not variables declared as `var`. The following code, for example, is valid:

```
var success = true;
if ( success == true )
{
  var message = "Transaction succeeded";
  var retry = 0;
}
else
{
  var message = "Transaction failed";
  var retry = 1;
}

console.log(message);
```

The `console.log(message)` statement is able to access the `message` variable, even though it has been declared within the code block of the `if` structure. The same would not happen if `message` were constant, as exemplified in the following example:

```
var success = true;
if ( success == true )
{
  const message = "Transaction succeeded";
  var retry = 0;
}
else
{
  const message = "Transaction failed";
  var retry = 1;
}

console.log(message);
```

In this case, an error message of type `ReferenceError: message is not defined` would be issued and the script would be stopped. While it may seem like a limitation, restricting the scope of variables and constants helps to avoid confusion between the information processed in the script body and in its different code blocks. For this reason, variables declared with `let` instead of `var` are also restricted in scope by the blocks delimited by braces. There are other subtle differences between declaring a variable with `var` or with `let`, but the most significant of these concerns the scope of the variable, as discussed here.

### Types of Values
Most of the time, the programmer doesn’t need to worry about the type of data stored in a variable, because JavaScript automatically identifies it with one of its primitive types during the first assignment of a value to the variable. Some operations, however, can be specific to one data type or another and can result in errors when used without discretion. In addition, JavaScript offers structured types that allow you to combine more than one primitive type into a single object.

### Primitive Types
Primitive type instances correspond to traditional variables, which store only one value. Types are defined implicitly, so the `typeof` operator can be used to identify what type of value is stored in a variable:

```
console.log("Undefined variables are of type", typeof variable);

{
  let variable = true;
  console.log("Value `true` is of type " + typeof variable);
}

{
  let variable = 3.14159265;
  console.log("Value `3.14159265` is of type " + typeof variable);
}

{
  let variable = "Text content";
  console.log("Value `Text content` is of type " + typeof variable);
}

{
  let variable = Symbol();
  console.log("A symbol is of type " + typeof variable);
}
```

This script will display on the console what type of variable was used in each case:

```
undefined variables are of type undefined
Value `true` is of type boolean
Value `3.114159265` is of type number
Value `Text content` is of type string
A symbol is of type symbol
```

Notice that the first line tries to find the type of an undeclared variable. This causes the given variable to be identified as undefined. The symbol type is the least intuitive primitive. Its purpose is to provide a unique attribute name within an object when there is no need to define a specific attribute name. An object is one of the data structures we’ll look at next.

### Structured Types
While primitive types are sufficient for writing simple routines, there are drawbacks to using them exclusively in more complex applications. An e-commerce application, for example, would be much more difficult to write, because the programmer would need to find ways to store lists of items and corresponding values using only variables with primitive types.

Structured types simplify the task of grouping information of the same nature into a single variable. A list of items in a shopping cart, for instance, can be stored in a single variable of type array:

```
let cart = ['Milk', 'Bread', 'Eggs'];
```

As demonstrated in the example, an array of items is designated with square brackets. The example has populated the array with three literal string values, hence the use of single quotes. Variables can also be used as items in an array, but in that case they must be designated without quotes. The number of items in an array can be queried with the `length` property:

```
let cart = ['Milk', 'Bread', 'Eggs'];
console.log(cart.length);
```

The number `3` will be displayed in the console output. New items can be added to the array with the `push()` method:

```
cart.push('Candy');
console.log(cart.length);
```

This time, the amount displayed will be `4`. Each item in the list can be accessed by its numeric index, starting with `0`:

```
console.log(cart[0]);
console.log(cart[3]);
```

The output displayed on the console will be:

```
Milk
Candy
```

Just as you can use `push()` to add an element, you can use `pop()` to remove the last element from an array.

Values stored in an array do not need be of the same type. It is possible, for example, to store the quantity of each item beside it. A shopping list like the one in the previous example could be constructed as follows:

```
let cart = ['Milk', 1, 'Bread', 4, 'Eggs', 12, 'Candy', 2];

// Item indexes are even
let item = 2;

// Quantities indexes are odd
let quantity = 3;

console.log("Item: " + cart[item]);
console.log("Quantity: " + cart[quantity]);
```

The output displayed on the console after running this code is:

```
Item: Bread
Quantity: 4
```

As you may have already noticed, combining the names of items with their respective quantities in a single array may not be a good idea, because the relationship between them is not explicit in the data structure and is very susceptible to (human) errors. Even if an array were used for names and another array for quantities, maintaining the integrity of the list would require the same care and would not be very productive. In these situations, the best alternative is to use a more appropriate data structure: an object.

In JavaScript, an object-type data structure lets you bind properties to a variable. Also, unlike an array, the elements that make up an object do not have a fixed order. A shopping list item, for example, can be an object with the properties name and `quantity`:

```
let item = { name: 'Milk', quantity: 1 };
console.log("Item: " + item.name);
console.log("Quantity: " + item.quantity);
```

This example shows that an object can be defined using braces (`{}`), where each property/value pair is separated by a colon and the properties are separated by commas. The property is accessible in the format variable.property, as in `item.name`, both for reading and for assigning new values. The output displayed on the console after running this code is:

```
Item: Milk
Quantity: 1
```

Finally, each object representing an item can be included in the shopping list array. This can be done directly when creating the list:

```
let cart = [{ name: 'Milk', quantity: 1 }, { name: 'Bread', quantity: 4 }];
```

As before, a new object representing an item can be added later to the array:

```
cart.push({ name: 'Eggs', quantity: 12 });
```

Items in the list are now accessed by their index and their property name:

```
console.log("Third item: " + cart[2].name);
console.log(cart[2].name + " quantity: " + cart[2].quantity);
```

The output displayed on the console after running this code is:

```
third item: eggs
Eggs quantity: 12
```

Data structures allow the programmer to keep their code much more organized and easier to maintain, whether by the original author or by other programmers on the team. Also, many outputs from JavaScript functions are in structured types, which need to be handled properly by the programmer.

### Operators
So far, we’ve pretty much seen only how to assign values to newly created variables. As simple as it is, any program will perform several other manipulations on the values of variables. JavaScript offers several types of operators that can act directly on the value of a variable or store the result of the operation in a new variable.

Most operators are geared toward arithmetic operations. To increase the quantity of an item in the shopping list, for example, just use the `+` addition operator:

```
item.quantity = item.quantity + 1;
```

The following snippet prints the value of `item.quantity` before and after the addition. Do not mix up the roles of the plus sign in the snippet. The `console.log` statements use a plus sign to combine two strings.

```
let item = { name: 'Milk', quantity: 1 };
console.log("Item: " + item.name);
console.log("Quantity: " + item.quantity);

item.quantity = item.quantity + 1;
console.log("New quantity: " + item.quantity);
```

The output displayed on the console after running this code is:

```
Item: Milk
Quantity: 1
New quantity: 2
```

Note that the value previously stored in `item.quantity` is used as the operand of the addition operation: `item.quantity = item.quantity + 1`. Only after the operation is complete the value in `item.quantity` is updated with the result of the operation. This kind of arithmetic operation involving the current value of the target variable is quite common, so there are shorthand operators that allow you to write the same operation in a reduced format:

```
item.quantity += 1;
```

The other basic operations also have equivalent shorthand operators:

- `a = a - b` is equivalent to `a -= b`.

- `a = a * b` is equivalent to `a *= b`.

- `a = a / b` is equivalent to `a /= b`.

For addition and subtraction, there is a third format available when the second operand is only one unit:

- `a = a + 1` is equivalent to `a++`.

- `a = a - 1` is equivalent to `a--`.

More than one operator can be combined in the same operation and the result can be stored in a new variable. For example, the following statement calculates the total price of an item plus a shipping cost:

```
let total = item.quantity * 9.99 + 3.15;
```

The order in which the operations are carried out follows the traditional order of precedence: first the multiplication and division operations are carried out, and only then are the addition and subtraction operations carried out. Operators with the same precedence are executed in the order they appear in the expression, from left to right. To override the default precedence order, you can use parentheses, as in `a * (b + c)`.

In some situations, the result of an operation doesn’t even need to be stored in a variable. This is the case when you want to evaluate the result of an expression within an `if` statement:

```
if ( item.quantiy % 2 == 0 )
{
  console.log("Quantity for the item is even");
}
else
{
  console.log("Quantity for the item is odd");
}
```

The `%` (modulo) operator returns the remainder of the division of the first operand by the second operand. In the example, the `if` statement checks whether the remainder of the division of `item.quantity` by `2` is equal to zero, that is, whether `item.quantity` is a multiple of 2.

When one of the operands of the `+` operator is a string, the other operators are coerced into strings and the result is a concatenation of strings. In the previous examples, this type of operation was used to concatenate strings and variables into the argument of the `console.log` statement.

This automatic conversion might not be the desired behavior. A user-supplied value in a form field, for example, might be identified as a string, but it is actually a numeric value. In cases like this, the variable must first be converted to a number with the `Number()` function:

```
sum = Number(value1) + value2;
```

Moreover, it is important to verify that the user has provided a valid value before proceeding with the operation. In JavaScript, a variable without an assigned value contains the value `null`. This allows the programmer to use a decision statement such as `if ( value1 == null )` to check whether a variable was assigned a value, regardless of the type of the value assigned to the variable.

___

## Lesson 4.3 JavaScript Control Structures and Functions

Like any other programming language, JavaScript code is a collection of statements that tells an instruction interpreter what to do in sequential order. However, this does not mean that every statement should be executed only once or that they should execute at all. Most statements should execute only when specific conditions are met. Even when a script is triggered asynchronously by independent events, it often has to check a number of control variables to find out the right portion of code to run.

### If Statements
The simplest control structure is given by the `if` instruction, which will execute the statement immediately after it if the specified condition is true. JavaScript considers conditions to be true if the evaluated value is non-zero. Anything inside the parenthesis after the `if` word (spaces are ignored) will be interpreted as a condition. In the following example, the literal number `1` is the condition:

```
if ( 1 ) console.log("1 is always true");
```

The number `1` is explicitly written in this example condition, so it is treated as a constant value (it remains the same throughout the execution of the script) and it will always yield true when used as a conditional expression. The word `true` (without the double quotes) could also be used instead of `1`, as it is also treated as a literal true value by the language. The `console.log` instruction prints its arguments in the browser’s console window.

```
Tip
The browser console shows errors, warnings, and informational messages sent with the console.log JavaScript instruction. On Chrome, the kbd:[Ctrl+Shift+J] (kbd:[Cmd+Option+J] on Mac) key combination opens the console. On Firefox, the kbd:[Ctrl+Shift+K] (kbd:[Cmd+Option+K] on Mac) key combination opens the console tab in the developer’s tools.
```

Although syntactically correct, using constant expressions in conditions is not very useful. In an actual application, you’ll probably want to test the trueness of a variable:

```
let my_number = 3;
if ( my_number ) console.log("The value of my_number is", my_number, "and it yields true");
```

The value assigned to the variable `my_number` (`3`) is non-zero, so it yields true. But this example is not common usage, because you rarely need to test whether a number equals zero. It is much more common to compare one value to another and test whether the result is true:

```
let my_number = 3;
if ( my_number == 3 ) console.log("The value of my_number is", my_number, "indeed");
```

The double equal comparison operator is used because the single equal operator is already defined as the assignment operator. The value on each side of the operator is called an operand. The ordering of the operands does not matter and any expression that returns a value can be an operand. Here a list of other available comparison operators:

`value1 == value2`
- True if `value1` is equal to `value2`.

`value1 != value2`
- True if `value1` is not equal to `value2`.

`value1 < value2`
- True if `value1` is less than `value2`.

`value1 > value2`
- True if `value1` is greater than `value2`.

`value1 <= value2`
- True if `value1` is less than or equal to `value2`.

`value1 >= value2`
- True if `value1` is grater than or equal to `value2`.

Usually, it does not matter whether the operand to the left of the operator is a string and the operand to the right is a number, as long as JavaScript is able to convert the expression to a meaningful comparison. So the string containing the character `1` will be treated as the number 1 when compared to a number variable. To make sure the expression yields true only if both operands are of the exact same type and value, the strict identity operator `===` should be used instead of `==`. Likewise, the strict non-identity operator `!==` evaluates as true if the first operand is not of the exact same type and value as the second operator.

Optionally, the `if` control structure can execute an alternate statement when the expression evaluates as false:

```
let my_number = 4;
if ( my_number == 3 ) console.log("The value of my_number is 3");
else console.log("The value of my_number is not 3");
```

The `else` instruction should immediately follow the `if` instruction. So far, we are executing only one statement when the condition is met. To execute more than one statement, you must enclose them in curly braces:

```
let my_number = 4;
if ( my_number == 3 )
{
  console.log("The value of my_number is 3");
  console.log("and this is the second statement in the block");
}
else
{
  console.log("The value of my_number is not 3");
  console.log("and this is the second statement in the block");
}
```

A group of one or more statements delimited by a pair of curly braces is known as a block statement. It is common to use block statements even when there is only one instruction to execute, in order to make the coding style consistent throughout the script. Moreover, JavaScript does not require the curly braces or any statements to be on separate lines, but to do so improves readability and makes code maintenance easier.

Control structures can be nested inside each other, but it is important not to mix up the opening and closing braces of each block statement:

```
let my_number = 4;

if ( my_number > 0 )
{
  console.log("The value of my_number is positive");

  if ( my_number % 2 == 0 )
  {
    console.log("and it is an even number");
  }
  else
  {
    console.log("and it is an odd number");
  }
} // end of if ( my_number > 0 )
else
{
  console.log("The value of my_number is less than or equal to 0");
  console.log("and I decided to ignore it");
}
```

The expressions evaluated by the `if` instruction can be more elaborate than simple comparisons. This is the case in the previous example, where the arithmetical expression `my_number % 2` was employed inside the parentheses in the nested `if`. The `%` operator returns the remainder after dividing the number on its left by the number on its right. Arithmetical operators like `%` take precedence over comparison operators like `==`, so the comparison will use the result of the arithmetical expression as its left operand.

In many situations, nested conditional structures can be combined into a single structure using logical operators. If we were interested only in positive even numbers, for example, a single `if` structure could be used:

```
let my_number = 4;

if ( my_number > 0 && my_number % 2 == 0 )
{
  console.log("The value of my_number is positive");
  console.log("and it is an even number");
}
else
{
  console.log("The value of my_number either 0, negative");
  console.log("or it is a negative number");
}
```

The double ampersand operator `&&` in the evaluated expression is the logical AND operator. It evaluates as true only if the expression to its left and the expression to its right evaluate as true. If you want to match numbers that are either positive or even, the `||` operator should be used instead, which stands for the OR logical operator:

```
let my_number = -4;

if ( my_number > 0 || my_number % 2 == 0 )
{
  console.log("The value of my_number is positive");
  console.log("or it is a even negative number");
}
```

In this example, only negative odd numbers will fail to match the criteria imposed by the composite expression. If you have the opposite intent, that is, to match only the negative odd numbers, add the logical NOT operator `!` to the beginning of the expression:

```
let my_number = -5;

if ( ! ( my_number > 0 || my_number % 2 == 0 ) )
{
  console.log("The value of my_number is an odd negative number");
}
```

Adding the parenthesis in the composite expression forces the expression enclosed by them to be evaluated first. Without these parentheses, the NOT operator would apply only to `my_number > 0` and then the OR expression would be evaluated. The `&&` and `||` operators are known as binary logical operators, because they require two operands. `!` is known as a unary logical operator, because it requires only one operand.

# Switch Structures
Although the `if` structure is quite versatile and sufficient to control the flow of the program, the `switch` control structure may be more appropriate when results other than true or false need to be evaluated. For example, if we want to take a distinct action for each item chosen from a list, it will be necessary to write an `if` structure for each assessment:

```
// Available languages: en (English), es (Spanish), pt (Portuguese)
let language = "pt";

// Variable to register whether the language was found in the list
let found = 0;

if ( language == "en" )
{
  found = 1;
  console.log("English");
}

if ( found == 0 && language == "es" )
{
  found = 1;
  console.log("Spanish");
}

if ( found == 0 && language == "pt" )
{
  found = 1;
  console.log("Portuguese");
}

if ( found == 0 )
{
  console.log(language, " is unknown to me");
}

```

In this example, an auxiliary variable `found` is used by all if structures to find out whether a match has occurred. In a case such as this one, the `switch` structure will perform the same task, but in a more succinct manner:

```
switch ( language )
{
  case "en":
    console.log("English");
    break;
  case "es":
    console.log("Spanish");
    break;
  case "pt":
    console.log("Portuguese");
    break;
  default:
    console.log(language, " not found");
}
```

Each nested `case` is called a clause. When a clause matches the evaluated expression, it executes the statements following the colon until the `break` statement. The last clause does not need a `break` statement and is often used to set the default action when no other matches occur. As seen in the example, the auxiliary variable is not needed in the `switch` structure.

```
Warning
switch uses strict comparison to match expressions against its case clauses.
```

If more than one clause triggers the same action, you can combine two or more `case` conditions:

```
switch ( language )
{
  case "en":
  case "en_US":
  case "en_GB":
    console.log("English");
    break;
  case "es":
    console.log("Spanish");
    break;
  case "pt":
  case "pt_BR":
    console.log("Portuguese");
    break;
  default:
    console.log(language, " not found");
}
```

### Loops
In previous examples, the `if` and `switch` structures were well suited for tasks that need to run only once after passing one or more conditional tests. However, there are situations when a task must execute repeatedly — in a so-called loop — as long as its conditional expression keeps testing as true. If you need to know whether a number is prime, for example, you will need to check whether dividing this number by any integer greater than 1 and lower than itself has a remainder equal to 0. If so, the number has an integer factor and it is not prime. (This is not a rigorous or efficient method to find prime numbers, but it works as a simple example.) Loop controls structures are more suitable for such cases, in particular the `while` statement:

```
// A naive prime number tester

// The number we want to evaluate
let candidate = 231;

// Auxiliary variable
let is_prime = true;

// The first factor to try
let factor = 2;

// Execute the block statement if factor is
// less than candidate and keep doing it
// while factor is less than candidate
while ( factor < candidate )
{

  if ( candidate % factor == 0 )
  {
    // The remainder is zero, so the candidate is not prime
    is_prime = false;
    break;
  }

  // The next factor to try. Simply
  // increment the current factor by one
  factor++;
}

// Display the result in the console window.
// If candidate has no integer factor, then
// the auxiliary variable is_prime still true
if ( is_prime )
{
  console.log(candidate, "is prime");
}
else
{
  console.log(candidate, "is not prime");
}
```

The block statement following the `while` instruction will execute repeatedly as long as the condition `factor < candidate` is true. It will execute at least once, as long as we initialize the `factor` variable with a value lower than `candidate`. The `if` structure nested in the `while` structure will evaluate whether the remainder of `candidate` divided by `factor` is zero. If so, the candidate number is not prime and the loop can finish. The `break` statement will finish the loop and the execution will jump to the first instruction after the `while` block.

Note that the outcome of the condition used by the `while` statement must change at every loop, otherwise the block statement will loop “forever”. In the example, we increment the `factor` variable‒the next divisor we want to try‒and it guarantees the loop will end at some point.

This simple implementation of a prime number tester works as expected. However, we know that a number that is not divisible by two will not be divisible by any other even number. Therefore, we could just skip the even numbers by adding another `if` instruction:

```
while ( factor < candidate )
{

  // Skip even factors bigger than two
  if ( factor > 2 && factor % 2 == 0 )
  {
    factor++;
    continue;
  }

  if ( candidate % factor == 0 )
  {
    // The remainder is zero, so the candidate is not prime
    is_prime = false;
    break;
  }

  // The next number that will divide the candidate
  factor++;
}
```

The `continue` statement is similar to the `break` statement, but instead of finishing this iteration of the loop, it will ignore the rest of the loop’s block and start a new iteration. Note that the `factor` variable was modified before the `continue` statement, otherwise the loop would have the same outcome again in the next iteration. This example is too simple and skipping part of the loop will not really improve its performance, but skipping redundant instructions is very important when writing efficient applications.

Loops are so commonly used that they exist in many different variants. The `for` loop is specially suited to iterate through sequential values, because it lets us define the loop rules in a single line:

```
for ( let factor = 2; factor < candidate; factor++ )
{
  // Skip even factors bigger than two
  if ( factor > 2 && factor % 2 == 0 )
  {
    continue;
  }

  if ( candidate % factor == 0 )
  {
    // The remainder is zero, so the candidate is not prime
    is_prime = false;
    break;
  }
}
```

This example produces the exact same result as the previous `while` example, but its parenthetic expression includes three parts, separated by semicolons: the initialization (`let factor = 2`), the loop condition (`factor < candidate`), and the final expression to be evaluated at the end of each loop iteration (`factor++`). The `continue` and `break` statements also apply to `for` loops. The final expression in the parentheses (`factor++`) will be evaluated after the `continue` statement, so it should not be inside the block statement, otherwise it will be incremented two times before the next iteration.

JavaScript has special types of `for` loops to work with array-like objects. We could, for example, check an array of candidate variables instead of just one:

```
// A naive prime number tester

// The array of numbers we want to evaluate
let candidates = [111, 139, 293, 327];

// Evaluates every candidate in the array
for (candidate of candidates)
{
  // Auxiliary variable
  let is_prime = true;

  for ( let factor = 2; factor < candidate; factor++ )
  {
    // Skip even factors bigger than two
    if ( factor > 2 && factor % 2 == 0 )
    {
      continue;
    }

    if ( candidate % factor == 0 )
    {
      // The remainder is zero, so the candidate is not prime
      is_prime = false;
      break;
    }
  }

  // Display the result in the console window
  if ( is_prime )
  {
    console.log(candidate, "is prime");
  }
  else
  {
    console.log(candidate, "is not prime");
  }
}
```

The `for (candidate of candidates)` statement assigns one element of the `candidates` array to the `candidate` variable and uses it in the block statement, repeating the process for every element in the array. You don’t need to declare `candidate` separately, because the `for` loop defines it. Finally, the same code from the previous example was nested in this new block statement, but this time testing each candidate in the array.


In addition to the standard set of builtin functions provided by the JavaScript language, developers can write their own custom functions to map an input to an output suitable to the application’s needs. Custom functions are basically a set of statements encapsulated to be used elsewhere as part of an expression.

Using functions is a good way to avoid writing duplicate code, because they can be called from different locations throughout the program. Moreover, grouping statements in functions facilitates the binding of custom actions to events, which is a central aspect of JavaScript programming.

### Defining a Function
As a program grows, it becomes harder to organize what it does without using functions. Each function has its own private variable scope, so the variables defined inside a function will be available only inside that same function. Thus, they won’t get mixed up with variables from other functions. Global variables are still accessible from within functions, but the preferable way to send input values to a function is through function parameters. As an example, we are going to build on the prime number validator from the previous lesson:

```
// A naive prime number tester

// The number we want to evaluate
let candidate = 231;

// Auxiliary variable
let is_prime = true;

// Start with the lowest prime number after 1
let factor = 2;

// Keeps evaluating while factor is less than the candidate
while ( factor < candidate )
{

  if ( candidate % factor == 0 )
  {
    // The remainder is zero, so the candidate is not prime
    is_prime = false;
    break;
  }

  // The next number that will divide the candidate
  factor++;
}

// Display the result in the console window
if ( is_prime )
{
  console.log(candidate, "is prime");
}
else
{
  console.log(candidate, "is not prime");
}
```

If later in the code you need to check if a number is prime, it would be necessary to repeat the code that has already been written. This practice is not recommended, because any corrections or improvements to the original code would need to be replicated manually everywhere the code was copied to. Moreover, repeating code places a burden on the browser and network, possibly slowing down the display of the web page. Instead of doing this, move the appropriate statements to a function:

```
// A naive prime number tester function
function test_prime(candidate)
{
  // Auxiliary variable
  let is_prime = true;

  // Start with the lowest prime number after 1
  let factor = 2;

  // Keeps evaluating while factor is less than the candidate
  while ( factor < candidate )
  {

    if ( candidate % factor == 0 )
    {
      // The remainder is zero, so the candidate is not prime
      is_prime = false;
      break;
    }

    // The next number that will divide the candidate
    factor++;
  }

  // Send the answer back
  return is_prime;
}
```

The function declaration starts with a `function` statement, followed by the name of the function and its parameters. The name of the function must follow the same rules as the names for variables. The parameters of the function, also known as the function arguments, are separated by commas and enclosed by parenthesis.

```
Tip
Listing the arguments in the function declaration is not mandatory. The arguments passed to a function can be retrieved from an array-like arguments object inside that function. The index of the arguments starts at 0, so the first argument is arguments[0], the second argument is arguments[1], and so on.
```

In the example, the `test_prime` function has only one argument: the `candidate` argument, which is the prime number candidate to be tested. Function arguments work like variables, but their values are assigned by the statement calling the function. For example, the statement `test_prime(231)` will call the `test_prime` function and assign the value 231 to the candidate argument, which will then be available inside the function’s body like an ordinary variable.

If the calling statement uses simple variables for the function’s parameters, their values will be copied to the function arguments. This procedure — copying the values of the parameters used in the calling statement to the parameters used inside the function —- is called passing arguments by value. Any modifications made to the argument by the function does not affect the original variable used in the calling statement. However, if the calling statement uses complex objects as arguments (that is, an object with properties and methods attached to it) for the function’s parameters, they will be passed as reference and the function will be able to modify the original object used in the calling statement.

The arguments that are passed by value, as well as the variables declared within the function, are not visible outside it. That is, their scope is restricted to the body of the function where they were declared. Nonetheless, functions are usually employed to create some output visible outside the function. To share a value with its calling function, a function defines a `return` statement.

For instance, the `test_prime` function in the previous example returns the value of the `is_prime` variable. Therefore, the function can replace the variable anywhere it would be used in the original example:

```
// The number we want to evaluate
let candidate = 231;

// Display the result in the console window
if ( test_prime(candidate) )
{
  console.log(candidate, "is prime");
}
else
{
  console.log(candidate, "is not prime");
}
```

The `return` statement, as its name indicates, returns control to the calling function. Therefore, wherever the `return` statement is placed in the function, nothing following it is executed. A function can contain multiple `return` statements. This practice can be useful if some are within conditional blocks of statements, so that the function might or might not execute a particular `return` statement on each run.

Some functions may not return a value, so the `return` statement is not mandatory. The function’s internal statements are executed regardless of its presence, so functions can also be used to change the values of global variables or the contents of objects passed by reference, for example. Notwithstanding, if the function does not have a `return` statement, its default return value is set to `undefined`: a reserved variable that does not have a value and cannot be written.

### Function Expressions
In JavaScript, functions are just another type of object. Thus, functions can be employed in the script like variables. This characteristic becomes explicit when the function is declared using an alternative syntax, called function expressions:

```
let test_prime = function(candidate)
{
  // Auxiliary variable
  let is_prime = true;

  // Start with the lowest prime number after 1
  let factor = 2;

  // Keeps evaluating while factor is less than the candidate
  while ( factor < candidate )
  {

    if ( candidate % factor == 0 )
    {
      // The remainder is zero, so the candidate is not prime
      is_prime = false;
      break;
    }

    // The next number that will divide the candidate
    factor++;
  }

  // Send the answer back
  return is_prime;
}
```

The only difference between this example and the function declaration in the previous example is in the first line: `let test_prime = function(candidate)` instead of `function test_prime(candidate)`. In a function expression, the `test_prime` name is used for the object containing the function and not to name the function itself. Functions defined in function expressions are called in the same way as functions defined using the declaration syntax. However, whereas declared functions can be called before or after their declaration, function expressions can be called only after their initialization. Like with variables, calling a function defined in an expression before its initialization will cause a reference error.

### Function Recursion
In addition to executing statements and calling builtin functions, custom functions can also call other custom functions, including themselves. To call a function from itself is called function recursion. Depending on the type of problem you are trying to solve, using recursive functions can be more straightforward than using nested loops to perform repetitive tasks.

So far, we know how to use a function to test whether a given number is prime. Now suppose you want to find the next prime following a given number. You could employ a while loop to increment the candidate number and write a nested loop which will look for integer factors for that candidate:

```
// This function returns the next prime number
// after the number given as its only argument
function next_prime(from)
{
  // We are only interested in the positive primes,
  // so we will consider the number 2 as the next
  // prime after any number less than two.
  if ( from < 2 )
  {
    return 2;
  }

  // The number 2 is the only even positive prime,
  // so it will be easier to treat it separately.
  if ( from == 2 )
  {
    return 3;
  }

  // Decrement "from" if it is an even number
  if ( from % 2 == 0 )
  {
    from--;
  }

  // Start searching for primes greater then 3.

  // The prime candidate is the next odd number
  let candidate = from + 2;

  // "true" keeps the loop going until a prime is found
  while ( true )
  {
    // Auxiliary control variable
    let is_prime = true;

    // "candidate" is an odd number, so the loop will
    // try only the odd factors, starting with 3
    for ( let factor = 3; factor < candidate; factor = factor + 2 )
    {
      if ( candidate % factor == 0 )
      {
        // The remainder is zero, so the candidate is not prime.
        // Test the next candidate
        is_prime = false;
        break;
      }
    }
    // End loop and return candidate if it is prime
    if ( is_prime )
    {
      return candidate;
    }
    // If prime not found yet, try the next odd number
    candidate = candidate + 2;
  }
}

let from = 1024;
console.log("The next prime after", from, "is", next_prime(from));
```

Note that we need to use a constant condition for the `while` loop (the `true` expression inside the parenthesis) and the auxiliary variable `is_prime` to know when to stop the loop. Athough this solution is correct, using nested loops is not as elegant as using recursion to perform the same task:

```
// This function returns the next prime number
// after the number given as its only argument
function next_prime(from)
{
  // We are only interested in the positive primes,
  // so we will consider the number 2 as the next
  // prime after any number less than two.
  if ( from < 2 )
  {
    return 2;
  }

  // The number 2 is the only even positive prime,
  // so it will be easier to treat it separately.
  if ( from == 2 )
  {
    return 3;
  }

  // Decrement "from" if it is an even number
  if ( from % 2 == 0 )
  {
    from--;
  }

  // Start searching for primes greater then 3.

  // The prime candidate is the next odd number
  let candidate = from + 2;

  // "candidate" is an odd number, so the loop will
  // try only the odd factors, starting with 3
  for ( let factor = 3; factor < candidate; factor = factor + 2 )
  {
    if ( candidate % factor == 0 )
    {
      // The remainder is zero, so the candidate is not prime.
      // Call the next_prime function recursively, this time
      // using the failed candidate as the argument.
      return next_prime(candidate);
    }
  }

  // "candidate" is not divisible by any integer factor other
  // than 1 and itself, therefore it is a prime number.
  return candidate;
}

let from = 1024;
console.log("The next prime after", from, "is", next_prime(from));
```

Both versions of `next_prime` return the next prime number after the number given as its only argument (`from`). The recursive version, like the previous version, starts by checking the special cases (i.e. numbers less or equal to two). Then it increments the candidate and starts looking for any integer factors with the `for` loop (notice that the `while` loop is not there anymore). At that point, the only even prime number has already been tested, so the candidate and its possible factors are incremented by two. (An odd number plus two is the next odd number.)

There are only two ways out of the `for` loop in the example. If all the possible factors are tested and none of them has a remainder equal to zero when dividing the candidate, the `for` loop completes and the function returns the candidate as the next prime number after `from`. Otherwise, if `factor` is an integer factor of `candidate (candidate % factor == 0)`, the returned value comes from the `next_prime` function called recursively, this time with the incremented candidate as its from parameter. The calls for `next_prime` will be stacked on top of one another, until one candidate finally turns up no integer factors. Then the last `next_prime` instance containing the prime number will return it to the previous `next_prime` instance, and thus successively down to the first next_prime instance. Even though each invocation of the function uses the same names for variables, the invocations are isolated from each other, so their variables are kept separated in memory.

___

## Lesson 4.4 JavaScript Manipulation of Website Content and Styling

HTML, CSS, and JavaScript are three distinct technologies that come together on the Web. In order to make truly dynamic and interactive pages, the JavaScript programmer must combine components from HTML and CSS at run time, a task that is greatly facilitated by using the Document Object Model (DOM).

Interacting with the DOM
The DOM is a data structure that works as a programming interface to the document, where every aspect of the document is represented as a node in the DOM and every change made to the DOM will immediately reverberate in the document. To show how to use the DOM in JavaScript, save the following HTML code in a file called `example.html`:

```
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8" />
  <title>HTML Manipulation with JavaScript</title>
</head>
<body>

<div class="content" id="content_first">
<p>The dynamic content goes here</p>
</div><!-- #content_first -->

<div class="content" id="content_second" hidden>
<p>Second section</p>
</div><!-- #content_second -->

</body>
</html>
```

The DOM will be available only after the HTML is loaded, so write the following JavaScript at the end of the page body (before the ending `</body>` tag):

```
<script>
let body = document.getElementsByTagName("body")[0];
console.log(body.innerHTML);
</script>
```

The `document` object is the top DOM element, all other elements branch off from it. The `getElementsByTagName()` method lists all the elements descending from `document` that have the given tag name. Even though the `body` tag is used only once in the document, the `getElementsByTagName()` method always return an array-like collection of found elements, hence the use of the `[0]` index to return the first (and only) element found.

### HTML Content
As shown in the previous example, the DOM element returned by the `document.getElementsByTagName("body")[0]` was assigned to the `body` variable. The `body` variable can then be used to manipulate the page’s body element, because it inherits all the DOM methods and attributes from that element. For instance, the `innerHTML` property contains the entire HTML markup code written inside the corresponding element, so it can be used to read the inner markup. Our `console.log(body.innerHTML)` call prints the content inside `<body></body>` to the web console. The variable can also be used to replace that content, as in `body.innerHTML = "<p>Content erased</p>`".

Rather than changing entire portions of HTML markup, it is more practical to keep the document structure unaltered and just interact with its elements. Once the document is rendered by the browser, all the elements are accessible by DOM methods. It is possible, for example, to list and access all the HTML elements using the special string `*` in the `getElementsByTagName()` method of the document object:

```
let elements = document.getElementsByTagName("*");
for ( element of elements )
{
  if ( element.id == "content_first" )
  {
    element.innerHTML = "<p>New content</p>";
  }
}
```

This code will place all the elements found in `document` in the `elements` variable. The `elements` variable is an array-like object, so we can iterate through each of its items with a `for` loop. If the HTML page where this code runs has an element with an `id` attribute set to `content_first` (see the sample HTML page shown at the start of the lesson), the `if` statement matches that element and its markup contents will be changed to `<p>New content</p>`. Note that the attributes of an HTML element in the DOM are accessible using the dot notation of JavaScript object properties: therefore, `element.id` refers to the `id` attribute of the current element of the `for` loop. The `getAttribute()` method could also be used, as in `element.getAttribute("id")`.

It is not necessary to iterate through all the elements if you want to inspect only a subset of them. For example, the `document.getElementsByClassName()` method limits the matched elements to those having a specific class:

```
let elements = document.getElementsByClassName("content");
for ( element of elements )
{
  if ( element.id == "content_first" )
  {
    element.innerHTML = "<p>New content</p>";
  }
}
```

However, iterating through many document elements using a loop is not the best strategy when you have to change a specific element in the page.

### Selecting Specific Elements
JavaScript provides optimized methods to select the exact element you want to work on. The previous loop could be entirely replaced by the `document.getElementById()` method:

```
let element = document.getElementById("content_first");
element.innerHTML = "<p>New content</p>";
```

Every `id` attribute in the document must be unique, so the `document.getElementById()` method returns only a single DOM object. Even the declaration of the `element` variable can be omitted, because JavaScript lets us chain methods directly:

```
document.getElementById("content_first").innerHTML = "<p>New content</p>";
```

The `getElementById()` method is the preferable method to locate elements in the DOM, because its performance is much better than iterative methods when working with complex documents. However, not all elements have an explicit ID, and the method returns a null value if no element matches with the provided ID (this also prevents the use of chained attributes or functions, like the `innerHTML` used in the example above). Moreover, it is more practical to assign ID attributes only to the main components of the page and then use CSS selectors to locate their child elements.

Selectors, introduced in an earlier lesson on CSS, are patterns that match elements in the DOM. The `querySelector()` method returns the first matching element in the DOM’s tree, while `querySelectorAll()` returns all elements that match the specified selector.

In the previous example, the `getElementById()` method retrieves the element bearing the `content_first` ID. The `querySelector()` method can perform the same task:

```
document.querySelector("#content_first").innerHTML = "<p>New content</p>";
```

Because the `querySelector()` method uses selector syntax, the provided ID must begin with a hash character. If no matching element is found, the querySelector() method returns null.

In the previous example, the entire content of the `content_first` div is replaced by the text string provided. The string has HTML code in it, which is not considered a best practice. You must be careful when adding hard-coded HTML markup to JavaScript code, because tracking elements can become difficult when changes to the overall document structure are required.

Selectors are not restricted to the ID of the element. The internal `p` element can be addressed directly:

```
document.querySelector("#content_first p").innerHTML = "New content";
```

The `#content_first p` selector will match only the first `p` element inside the `#content_first` div. It works fine if we want to manipulate the first element. However, we may want to change the second paragraph:

```
<div class="content" id="content_first">
<p>Don't change this paragraph.</p>
<p>The dynamic content goes here.</p>
</div><!-- #content_first -->
```

In this case, we can use the `:nth-child(2)` pseudo-class to match the second `p` element:

```
document.querySelector("#content_first p:nth-child(2)").innerHTML = "New content";
```

The number `2` in `p:nth-child(2)` indicates the second paragraph that matches the selector. See the CSS selectors lesson to know more about selectors and how to use them.

### Working with Attributes
JavaScript’s ability to interact with the DOM is not restricted to content manipulation. Indeed, the most pervasive use of JavaScript in the browser is to modify the attributes of the existing HTML elements.

Let’s say our original HTML example page has now three sections of content:

```
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8" />
  <title>HTML Manipulation with JavaScript</title>
</head>
<body>

<div class="content" id="content_first" hidden>
<p>First section.</p>
</div><!-- #content_first -->

<div class="content" id="content_second" hidden>
<p>Second section.</p>
</div><!-- #content_second -->

<div class="content" id="content_third" hidden>
<p>Third section.</p>
</div><!-- #content_third -->

</body>
</html>
```

You may want to make only one of them visible at a time, hence the `hidden` attribute in all `div` tags. This is useful, for example, to show only one image from a gallery of images. To make one of them visible when the page loads, add the following JavaScript code to the page:

```
// Which content to show
let content_visible;

switch ( Math.floor(Math.random() * 3) )
{
  case 0:
    content_visible = "#content_first";
  break;
  case 1:
    content_visible = "#content_second";
  break;
  case 2:
    content_visible = "#content_third";
  break;
}
document.querySelector(content_visible).removeAttribute("hidden");
```

The expression evaluated by the `switch` statement randomly returns the number 0, 1, or 2. The corresponding ID selector is then be assigned to the `content_visible` variable, which is used by the `querySelector(content_visible)` method. The chained `removeAttribute("hidden")` call removes the `hidden` attribute from the element.

The opposite approach is also possible: All sections could be initially visible (without the `hidden` attribute) and the JavaScript program can then assign the `hidden` attribute to every section except the one in `content_visible`. To do so, you must iterate through all the content div elements that are different from the chosen one, which can be done by using the `querySelectorAll()` method:

```
// Which content to show
let content_visible;

switch ( Math.floor(Math.random() * 3) )
{
  case 0:
    content_visible = "#content_first";
  break;
  case 1:
    content_visible = "#content_second";
  break;
  case 2:
    content_visible = "#content_third";
  break;
}

// Hide all content divs, except content_visible
for ( element of document.querySelectorAll(".content:not("+content_visible+")") )
{
  // Hidden is a boolean attribute, so any value will enable it
  element.setAttribute("hidden", "");
}
```

If the `content_visible` variable was set to `#content_first`, the selector will be `.content:not(#content_first)`, which reads as all elements having the content class except those having the `content_first` ID. The `setAttribute()` method adds or changes attributes of HTML elements. Its first parameter is the name of the attribute and the second is the value of the attribute.

However, the proper way to change the appearance of elements is with CSS. In this case, we can set the `display` CSS property to `hidden` and then change it to `block` using JavaScript:

```
<style>
div.content { display: none }
</style>

<div class="content" id="content_first">
<p>First section.</p>
</div><!-- #content_first -->

<div class="content" id="content_second">
<p>Second section.</p>
</div><!-- #content_second -->

<div class="content" id="content_third">
<p>Third section.</p>
</div><!-- #content_third -->

<script>
// Which content to show
let content_visible;

switch ( Math.floor(Math.random() * 3) )
{
  case 0:
    content_visible = "#content_first";
  break;
  case 1:
    content_visible = "#content_second";
  break;
  case 2:
    content_visible = "#content_third";
  break;
}
document.querySelector(content_visible).style.display = "block";
</script>
```

The same good practices that apply to mixing HTML tags with JavaScript apply also to CSS. Thus, writing CSS properties directly in JavaScript code is not recommended. Instead, CSS rules should be written apart from JavaScript code. The proper way to alternate visual styling is to select a pre-defined CSS class for the element.

### Working with Classes
Elements may have more than one associated class, making it easier to write styles that can be added or removed when necessary. It would be exhausting to change many CSS attributes directly in JavaScript, so you can create a new CSS class with those attributes and then add the class to the element. DOM elements have the `classList` property, which can be used to view and manipulate the classes assigned to the corresponding element.

For example, instead of changing the visibility of the element, we can create an additional CSS class to highlight our `content div`:

```
div.content {
  border: 1px solid black;
  opacity: 0.25;
}
div.content.highlight {
  border: 1px solid red;
  opacity: 1;
}
```

This stylesheet will add a thin black border and semi-transparency to all elements having the `content` class. Only the elements that also have the `highlight` class will be fully opaque and have the thin red border. Then, instead of changing the CSS properties directly as we did before, we can use the `classList.add("highlight")` method in the selected element:

```
// Which content to highlight
let content_highlight;

switch ( Math.floor(Math.random() * 3) )
{
  case 0:
    content_highlight = "#content_first";
  break;
  case 1:
    content_highlight = "#content_second";
  break;
  case 2:
    content_highlight = "#content_third";
  break;
}

// Highlight the selected div
document.querySelector(content_highlight).classList.add("highlight");
```

All the techniques and examples we have seen so far were performed at the end of the page loading process, but they are not restricted to this stage. In fact, what makes JavaScript so useful to Web developers is its ability to react to events on the page, which we will see next.

### Event Handlers
All visible page elements are susceptible to interactive events, such as the click or the movement of the mouse itself. We can associate custom actions to these events, which greatly expands what an HTML document can do.

Probably the most obvious HTML element that benefits from an associated action is the `button` element. To show how it works, add three buttons above the first `div` element of the example page:

```
<p>
<button>First</button>
<button>Second</button>
<button>Third</button>
</p>

<div class="content" id="content_first">
<p>First section.</p>
</div><!-- #content_first -->

<div class="content" id="content_second">
<p>Second section.</p>
</div><!-- #content_second -->

<div class="content" id="content_third">
<p>Third section.</p>
</div><!-- #content_third -->
```

The buttons do nothing on their own, but suppose you want to highlight the `div` corresponding to the pressed button. We can use the `onClick` attribute to associate an action to each button:

```
<p>
<button onClick="document.getElementById('content_first').classList.toggle('highlight')">First</button>
<button onClick="document.getElementById('content_second').classList.toggle('highlight')">Second</button>
<button onClick="document.getElementById('content_third').classList.toggle('highlight')">Third</button>
</p>
```

The `classList.toggle()` method adds the specified class to the element if it is not present, and removes that class if it is already present. If you run the example, you will note that more than one `div` can be highlighted at the same time. To highlight only the `div` corresponding to the pressed button, it is necessary to remove the `highlight` class from the other `div` elements. Nonetheless, if the custom action is too long or involves more than one line of code, it’s more practical to write a function apart from the element tag:

```
function highlight(id)
{
  // Remove the "highlight" class from all content elements
  for ( element of document.querySelectorAll(".content") )
  {
    element.classList.remove('highlight');
  }

  // Add the "highlight" class to the corresponding element
  document.getElementById(id).classList.add('highlight');
}
```

Like the previous examples, this function can be placed inside a `<script>` tag or in an external JavaScript file associated with the document. The `highlight` function first removes the `highlight` class from all the `div` elements associated with the `content` class, then adds the `highlight` class to the chosen element. Each button should then call this function from its `onClick` attribute, using the corresponding ID as the function’s argument:

```
<p>
<button onClick="highlight('content_first')">First</button>
<button onClick="highlight('content_second')">Second</button>
<button onClick="highlight('content_third')">Third</button>
</p>
```

In addition to the `onClick` attribute, we could use the `onMouseOver` attribute (triggered when the pointing device is used to move the cursor onto the element), the `onMouseOut` attribute (triggered when the pointing device is no longer contained within the element), etc. Moreover, event handlers are not restricted to buttons, so you can assign custom actions to these event handlers for all visible HTML elements.

___
This documentation is provided by the Linux Professional Institute

[Attribution-NonCommercial-NoDerivatives 4.0 International (CC BY-NC-ND 4.0)](https://creativecommons.org/licenses/by-nc-nd/4.0/)

[Get the Full PDF](https://learning.lpi.org/en/learning-materials/030-100/)