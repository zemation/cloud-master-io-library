# Web Development Essentials - Topic 1: Software Development and Web Technologies

## Lesson 1-1 Software Development Basic

The very first computers were programmed through the grueling process of plugging cables into sockets. Computer scientists soon started a never-ending search for easy ways to tell the computer what to do. This chapter introduces the tools of programming. It discusses the key ways that text instructions—​programming languages—​represent the tasks a programmer wants to accomplish, and the tools that change the program into a form called machine language that a computer can run.

```
Note
In this text, the terms program and application are used interchangeably.
```

### Source Code
A programmer normally develops an application by writing a textual description, called source code, of the desired task. The source code is in a carefully defined programming language that represents what the computer can do in a high-level abstraction humans can understand. Tools have also been developed to let programmers as well as non-programmers express their thoughts visually, but writing source code is still the predominant way to program.

In the same way that a natural language has nouns, verbs, and constructions to express ideas in a structured way, the words and punctuation in a programming language are symbolic representations of operations that will be performed on the machine.

In this sense, source code is not very different from any other text in which the author employs the well-established rules of a natural language to communicate with the reader. In the case of source code, the “reader” is the machine, so the text cannot contain ambiguities or inconsistencies—​even subtle ones.

And like any text that discusses some topic in depth, the source code also needs to be well structured and logically organized when developing complex applications. Very simple programs and didactic examples can be stored in the few lines of a single text file, which contains all the program’s source code. More complex programs can be subdivided into thousands of files, each with thousands of lines.

The source code of professional applications should be organized into different folders, usually associated with a particular purpose. A chat program, for example, can be organized into two folders: one that contains the code files that handle the transmission and reception of messages over the network, and another folder that contains the files that build the interface and react to user actions. Indeed, it is common to have many folders and subfolders with source code files dedicated to very specific tasks within the application.

Moreover, the source code is not always isolated in its own files, with everything written in a single language. In web applications, for example, an HTML document can embed JavaScript code to supplement the document with extra functionality.

### Code Editors and IDE
The variety of ways in which source code can be written can be intimidating. Therefore, many developers take advantage of tools that help with writing and testing the program.

The source code file is just a plain text file. As such, it can be edited by any text editor, no matter how simple. To make it easier to distinguish between source code and plain text, each language adopts a self-explanatory filename extension: `.c` for the C language, `.py` for Python, `.js` for JavaScript, etc. General-purpose editors often understand the source code of popular languages well enough to add italics, colors, and indentation to make the code understandable.

Not every developer chooses to edit source code in a general-purpose editor. An integrated development environment (IDE) provides a text editor along with tools to help the programmer avoid syntactic errors and obvious inconsistencies. These editors are particularly recommended for less experienced programmers, but experienced programmers use them as well.

Popular IDEs such Visual Studio, Eclipse, and Xcode intelligently watch what the programmer types, frequently suggesting words to use (autocompletion) and verifying code in real-time. The IDEs can even offer automated debugging and testing to identify issues whenever the source code changes.

Some more experienced programmers opt for less intuitive editors such as Vim, which offer greater flexibility and do not require installation of additional packages. These programmers use external, standalone tools to add the features that are built-in when you use an IDE.

### Code Maintenance
Whether in an IDE or using standalone tools, it’s important to employ some kind of version control system (VCS). Source code is constantly evolving because unforeseen flaws need to be fixed and enhancements need to be incorporated. An inevitable consequence of this evolution is that fixes and enhancements can interfere with other parts of applications in a large code base. Version control tools such as Git, Subversion, and Mercurial record all changes made to the code and who made the change, allowing you to trace and eventually recover from an unsuccessful modification.

Furthermore, version control tools allow each developer on the team to work on a copy of the source code files without interfering with the work of other programmers. Once the new versions of source code are ready and tested, corrections or improvements made to one copy can be incorporated by other team members.

Git, the most popular version control system nowadays, allows many independent copies of a repository to be maintained by different people, who share their changes as they desire. However, whether using a decentralized or centralized version control system, most teams maintain one trusted repository whose source code and resources can be relied on. Several online services offer storage for repositories of source code. The most popular of these services are GitHub and GitLab, but the GNU project’s Savannah is also worth mentioning.

### Programming Languages
A wide variety of programming languages exist; each decade sees the invention of new ones. Each programming language has its own rules and is recommended for particular purposes. Although the languages show superficial differences in syntax and keywords, what really distinguishes the languages are the deep conceptual approaches they represent, known as paradigms.

### Paradigms
Paradigms define the premises on which a programming language is based, especially concerning how the source code should be structured.

The developer starts from the language paradigm to formulate the tasks to be performed by the machine. These tasks, in turn, are symbolically expressed with the words and syntactic constructions offered by the language.

The programming language is procedural when the instructions presented in the source code are executed in sequential order, like a movie script. If the source code is segmented into functions or subroutines, a main routine takes care of calling the functions in sequence.

The following code is an example of a procedural language. Written in C, it defines variables to represent the side, area and volume of geographical shapes. The value of the `side` variable is assigned in `main()`, which is the function invoked when the program is executed. The `area` and `volume` variables are calculated in the `square()` and `cube()` subroutines that precede the main function:

```
#include <stdio.h>

float side;
float area;
float volume;

void square(){ area = side * side; }

void cube(){ volume = area * side; }

int main(){
  side = 2;
  square();
  cube();
  printf("Volume: %f\n", volume);
  return 0;
}
```

The order of actions defined in `main()` determines the sequence of program states, characterized by the value of the `side`, `area`, and `volume` variables. The example ends after displaying the value of `volume` with the `printf` statement.

On the other hand, the paradigm of object-oriented programming (OOP) has as its main characteristic the separation of the program state into independent sub-states. These sub-states and associated operations are the objects, so called because they have a more or less independent existence within the program and because they have specific purposes.

Distinct paradigms do not necessarily restrict the type of task that can be performed by a program. The code from the previous example can be rewritten according to the OOP paradigm using the C++ language:

```
#include <iostream>

class Cube {
  float side;
  public:
  Cube(float s){ side = s; }
  float volume() { return side * side * side; }
};

int main(){
  float side = 2;
  Cube cube(side);
  std::cout << "Volume: " << cube.volume() << std::endl;
  return 0;
}
```

The `main()` function is still present. But now there is a new word, `class`, that introduces the definition of an object. The defined class, named `Cube`, contains its own variables and subroutines. In OOP, a variable is also called an attribute and a subroutine is called a method.

It’s beyond the scope of this chapter to explain all the C++ code in the example. What’s important to us here is that `Cube` contains the `side` attribute and two methods. The `volume()` method calculates the cube’s volume.

It is possible to create several independent objects from the same class, and classes can be composed of other classes.

Keep in mind that these same features can be written differently and that the examples in this chapter are oversimplified. C and C++ have much more sophisticated features that allow much more complex and practical constructions.

Most programming languages do not rigorously impose one paradigm, but allow programmers to choose various aspects of one paradigm or another. JavaScript, for example, incorporates aspects of different paradigms. The programmer can decompose the entire program into functions that do not share a common state with each other:

```
function cube(side){
  return side*side*side;
}

console.log("Volume: " + cube(2));
```

Although this example is similar to procedural programming, note that the function receives a copy of all the information necessary for its execution and always produces the same result for the same parameter, regardless of changes that happen outside the function’s scope. This paradigm, called functional, is strongly influenced by mathematical formalism, where every operation is self-sufficient.

Another paradigm covers declarative languages, which describe the states you want the system to be in. A declarative language can figure out how to achieve the specified states. SQL, the universal language for querying databases, is sometimes called a declarative language, although it really occupies a unique niche in the programming pantheon.

There is no universal paradigm that can be adopted to any context. The choice of language may also be restricted by which languages are supported on the platform or execution environment where the program will be used.

A web application that will be used by the browser, for example, will need to be written in JavaScript, which is a language universally supported by browsers. (A few other languages can be used because they provide converters to create JavaScript.) So for the web browser—​sometimes called the client side or front end of the web application—​the developer will have to use the paradigms allowed in JavaScript. The server side or back end of the application, which handles requests from the browser, is normally programmed in a different language; PHP is most popular for this purpose.

Regardless of paradigm, every language has pre-built libraries of functions that can be incorporated into code. Mathematical functions—​like the ones illustrated in the example code—​don’t need to be implemented from scratch, as the language already has the function ready to use. JavaScript, for example, provides the Math object with the most common math operations.

Even more specialized functions are usually available from the language vendor or third-party developers. These extra resource libraries can be in source code form; i.e., in extra files that are incorporated into the file where they will be used. In JavaScript, embedding is done with `import from`:
```
import { OrbitControls } from 'modules/OrbitControls.js';
```

This type of import, where the embedded resource is also a source code file, is most often used in so-called interpreted languages. Compiled languages allow, among other things, the incorporation of pre-compiled features in machine language, that is, compiled libraries. The next section explains the differences between these types of languages.

### Compilers and Interpreters
As we already know, source code is a symbolic representation of a program that needs to be translated into machine language in order to run.

Roughly speaking, there are two possible ways to do the translation: converting the source code beforehand for future execution, or converting the code at the moment of its execution. Languages of the first modality are called compiled languages and languages of the second modality are called interpreted languages. Some interpreted languages provide compilation as an option, so that the program can start faster.

In compiled languages, there is a clear distinction between the source code of the program and the program itself, which will be executed by the computer. Once compiled, the program will usually work only on the operating system and platform for which it was compiled.

In an interpreted language, the source code itself is treated as the program, and the process of converting to machine language is transparent to the programmer. For an interpreted language, it is common to call the source code a script. The interpreter translates the script into the machine language for the system it’s running on.

### Compilation and Compilers
The C programming language is one of the best-known examples of a compiled language. The C language’s greatest strengths are its flexibility and performance. Both high-performance supercomputers and microcontrollers in home appliances can be programmed in the C language. Other examples of popular compiled languages are C++ and C# (C sharp). As their names suggest, these languages are inspired by C, but include features that support the object-oriented paradigm.

The same program written in C or C++ can be compiled for different platforms, requiring little or no change to the source code. It is the compiler that defines the target platform of the program. There are platform-specific compilers as well as cross-platform compilers such as GCC (which stands for GNU Compiler Collection) that can produce binary programs for many distinct architectures.

```
Note
There are also tools that automate the compilation process. Instead of invoking the compiler directly, the programmer creates a file indicating the different compilation steps to be performed automatically. The traditional tool used for this purpose is make, but a number of newer tools such as Maven and Gradle are also in widespread use. The entire build process is automated when you use an IDE.
```

The compilation process does not always generate a binary program in machine language. There are compiled languages that produce a program in a format generically called bytecode. Like a script, bytecode is not in a platform-specific language, so it requires an interpreter program that translates it into machine language. In this case, the interpreter program is simply called a runtime.

The Java language takes this approach, so compiled programs written in Java can be used on different operating systems. Despite its name, Java is unrelated to JavaScript.

Bytecode is closer to machine language than source code, so its execution tends to be comparatively faster. Because there is still a conversion process during the execution of the bytecode, it is difficult to obtain the same performance as an equivalent program compiled into machine language.

### Interpretation and Interpreters
In interpreted languages such as JavaScript, Python, and PHP, the program does not need to be precompiled, making it easier to develop and modify it. Instead of compiling it, the script is executed by another program called an interpreter. Usually, the interpreter of a language is named after the language itself. The interpreter of a Python script, for example, is a program called `python`. The JavaScript interpreter is most often the web browser, but scripts can also be executed by the `node` program outside a browser. Because it is converted to binary instructions every time it is executed, an interpreted language program tends to be slower than a compiled language equivalent.

Nothing prevents the same application from having components written in different languages. If necessary, these components can communicate through a mutually understandable application programming interface (API).

The Python language, for example, has very sophisticated data mining and data tabulation capabilities. The developer can choose Python to write the parts of the program that deal with these aspects and another language, such as C++, to perform the heavier numeric processing. It is possible to adopt this strategy even when there is no API that allows direct communication between the two components. Code written in Python can generate a file in the proper format to be used by a program written in C++, for example.

Although it is possible to write almost any program in any language, the developer should adopt the one that is most in line with the application’s purpose. In doing so, you benefit from the reuse of already tested and well-documented components.

___

## Lesson 1-2 Web Application Architecture

The word application has a broad meaning in technological jargon. When the application is a traditional program, executed locally and self-sufficient in its purpose, both the application’s operating interface and the data processing components are integrated in a single “package”. A web application is different because it adopts the client/server model and its client portion is based on HTML, which is obtained from the server and, in general, rendered by a browser.

### Clients and Servers
In the client/server model, part of the work is done locally on the client side and part of the work is done remotely, on the server side. Which tasks are performed by each party varies according to the purpose of the application, but in general it’s up to the client to provide an interface to the user and to layout the content in an attractive manner. It’s up to the server to run the business end of the application, processing and responding to requests made by the client. In a shopping application, for example, the client application displays an interface for the user to choose and pay for the products, but the data source and the transaction records are kept on the remote server, accessed via the network. Web applications perform this communication over the Internet, usually via the Hypertext Transfer Protocol (HTTP).

Once loaded by the browser, the client side of the application initiates interaction with the server whenever necessary or convenient. Web application servers offer an application programming interface (API) that defines the available requests and how those requests should be made. Thus, the client constructs a request in the format defined by the API and sends it to the server, which checks for any prerequisites for the request and sends back the appropriate response.

While the client, in the form of a mobile application or desktop browser, is a self-contained program with regard to the user interface and instructions for communicating with the server, the browser must obtain the HTML page and associated components—​such as images, CSS, and JavaScript—that define the interface and instructions for communicating with the server.

The programming languages and platforms used by client and server are independent, but use a mutually understandable communication protocol. The server portion is almost always performed by a program without a graphical interface, running in highly available computing environments so that it is always ready to responde to requests. In contrast, the client portion runs on any device that is capable of rendering an HTML interface, such as smartphones.

In addition to being essential for certain purposes, the adoption of the client/server model allows an application to optimize several aspects of development and maintenance, since each part can be designed for its specific purpose. An application that displays maps and routes, for example, does not need to have all maps stored locally. Only maps relating to the location of the users’s interest are required, so only those maps are requested from the central server.

The developers have direct control over the server, so they can also modify the client that is provided by it. This allows developers to improve the application, to a greater or lesser extent, without the need the user to explicitly install new versions.

### The Client Side
A web application should run the same way on any of the most popular browsers, as long as the browser is up to date. Some browsers may be incompatible with recent innovations, but only experimental applications use features not yet widely adopted.

Incompatibility issues were more common in the past, when different browsers had their own rendering engine and there was less cooperation in formulating and adopting standards. The rendering engine is the main component of the browser, as it is responsible for transforming HTML and other associated components into the visual and interactive elements of the interface. Some browsers, notably Internet Explorer, needed special treatment in the code so as not to break the pages expected functioning.

Today, there are minimal differences between the main browsers, and incompatibilities are rare. In fact, the Chrome and Edge browsers use the same rendering engine (called Blink). The Safari browser, and other browsers offered on the iOS App Store, use the WebKit engine. Firefox uses an engine called Gecko. These three engines account for virtually all browsers used today. Although developed separately, the three engines are open source projects and there is cooperation between their developers, which facilitates compatibility, upkeep, and the adoption of standards.

Because browser developers have taken so much effort to stay compatible, the server is not normally linked to a single type of client. In principle, an HTTP server can communicate with any client that is also capable of communicating via HTTP. In a map application, for example, the client can be a mobile application or a browser that loads the HTML interface from the server.

### Varieties of Web Clients
There are mobile and desktop applications whose interface is rendered from HTML and, like browsers, can use JavaScript as a programming language. However, unlike the client loaded in the browser, the HTML and the necessary components for the native client to function are locally present since the installation of the application. In fact, an application that works this way is virtually identical to an HTML page (both are even likely to be rendered by the same engine). There are also progressive web apps (PWA), a mechanism that allows you to package web application clients for offline use—​limited to functions that do not require immediate communication with the server. Regarding what the application can do, there is no difference between running the browser or packaged in a PWA, except that in the latter the developer has more control over what is stored locally.

Rendering interfaces from HTML is such a recurring activity that the engine is usually a separate software component, present in the operating system. Its presence as a separate component allows different applications to incorporate it without having to embed it in the application package. This model also delegates the maintenance of the rendering engine to the operating system, facilitating updates. It is very important to keep such a crucial component up to date in order to avoid possible failures.

Regardless of their delivery method, applications written in HTML run on an abstraction layer created by the engine, which functions as an isolated execution environment. In particular, in the case of a client that runs on the browser, the application has at its disposal only those resources offered by the browser. Basic features, such as interacting with page elements and requesting files over HTTP, are always available. Resources that may contain sensitive information, such as access to local files, geographic location, camera, and microphone, require explicit user authorization before the application is able to use them.

### Languages of a Web Client
The central element of a web application client that runs on the server is the HTML document. In addition to presenting the interface elements that the browser displays in a structured way, the HTML document contains the addresses for all files required for the correct presentation and operation of the client.

HTML alone does not have much versatility to build more elaborate interfaces and does not have general-purpose programming features. For this reason, an HTML document that should function as a client application is always accompanied by one or more sets of CSS and JavaScript.

The CSS can be provided as a separate file or directly in the HTML file itself. The main purpose of CSS is to adjust the appearance and layout of the elements of the HTML interface. Although not strictly necessary, more sophisticated interfaces usually require modifications to the CSS properties of the elements to suit their needs.

JavaScript is a practically indispensable component. Procedures written in JavaScript respond to events in the browser. These events can be caused by the user or non-interactive. Without JavaScript, an HTML document is practically limited to text and images. Using JavaScript in HTML documents allows you to extend interactivity beyond hyperlinks and forms, making the page displayed by the browser like a conventional application interface.

JavaScript is a general-purpose programming language, but its main use is in web applications. The features of the browser execution environment are accessible through JavaScript keywords, used in a script to perform the desired operation. The term `document`, for example, is used in JavaScript code to refer to the HTML document associated with the JavaScript code. In the context of the JavaScript language, `document` is a global object with properties and methods that can be used to obtain information from any element on the HTML document. More importantly, you can use the `document` object to modify its elements and to associate them with custom actions written in JavaScript.

A client application based on web technologies is multiplatform, because it can run on any device that has a compatible web browser.

Being confined to the browser, however, imposes limitations on web applications compared to native applications. The intermediation performed by the browser allows higher level programming and increases security, but also increases the processing and memory consumption.

Developers are continually working on browsers to provide more features and improve the performance of JavaScript applications, but there are intrinsic aspects to the execution of scripts such as JavaScript that impose a disadvantage on them compared to native programs for the same hardware.

A feature that significantly improves the performance of JavaScript applications running on the browser is WebAssembly. WebAssembly is a kind of compiled JavaScript that produces source code written in a more efficient, lower-level language, such as the C language. WebAssembly can accelerate mainly processor-intensive activities, because it avoids much of the translation performed by the browser when running a program written in conventional JavaScript.

Regardless of the implementation details of the application, all HTML code, CSS, JavaScript, and multimedia files must first be obtained from the server. The browser obtains these files just like an Internet page, that is, with an address accessed by the browser.

A web page that acts as an interface to a web application is like a plain HTML document, but adds additional behaviors. On conventional pages, the user is directed to another page after clicking on a link. Web applications can present their interface and respond to user events without loading new pages in the browser’s window. The modification of this standard behavior in HTML pages is done via JavaScript programming.

A webmail client, for example, displays messages and switches between message folders without leaving the page. This is possible because the client uses JavaScript to react to user actions and make appropriate requests to the server. If the user clicks on the subject of a message in the inbox, a JavaScript code associated with this event requests the content of that message from the server (using the corresponding API call). As soon as the client receives the response, the browser displays the message in the appropriate portion of the same page. Different webmail clients may adopt different strategies, but they all use this same principle.

Therefore, in addition to providing the files that make up the client to the browser, the server must also be able to handle requests such as that of the webmail client, when it asks for the content of a specific message. Every request that the client can make has a predefined procedure to respond on the server, whose API can define different methods to identify which procedure the request refers to. The most common methods are:

- Addresses, through a Uniform Resource Locator (URL)
- Fields in the HTTP header
- GET/POST methods
- WebSockets

One method may be more suitable than another, depending on the purpose of the request and other criteria taken into account by the developer. In general, web applications use a combination of methods, each in a specific circumstance.

The Representational State Transfer (REST) paradigm is widely used for communication in web applications, because it is based on the basic methods available in HTTP. The header of an HTTP request starts with a keyword that defines the basic operation to be performed: `GET`,`POST`, `PUT`,`DELETE`, etc., accompanied by a corresponding URL where the action will be applied. If the application requires more specific operations, with a more detailed description of the requested operation, the GraphQL protocol may be a more appropriate choice.

Applications developed using the client/server model are subject to instabilities in communication. For this reason, the client application must always adopt efficient data transfer strategies to favor its consistency and not harm the user experience.

### The Server Side
Despite being the main actor in a web application, the server is the passive side of the communication, just responding to requests made by the client. In web jargon, server can refer to the machine that receives the requests, the program that specifically handles HTTP requests, or the recipient script that produces a response to the request. This latter definition is the most relevant in the context of web application architecture, but they are all closely related. Although they are only partially in the scope of the application server developer, the machine, operating system, and HTTP server cannot be ignored, because they are fundamental to running the application server and often intersect.

### Handling Paths from Requests
HTTP servers, such as Apache and NGINX, usually require specific configuration changes to meet the needs of the application. By default, traditional HTTP servers directly associate the path indicated in the request to a file on the local file system. If a website’s HTTP server keeps its HTML files in the `/srv/www` directory, for example, a request with the path `/en/about.html` will receive the content of the file `/srv/www/en/about.html` as a response, if the file exists. More sophisticated websites, and especially web applications, demand customized treatments for different types of requests. In this scenario, part of the application implementation is modifying HTTP server settings to meet application requirements.

Alternatively, there are frameworks that allow you to integrate the management of HTTP requests and the implementation of the application code in one place, allowing the developer to focus more on the application’s purpose than on platform details. In Node.js Express, for example, all request mapping and corresponding programming are implemented using JavaScript. As the programming of clients is usually done in JavaScript, many developers consider it a good idea from the perspective of code maintenance to use the same language for client and server. Other languages commonly used to implement the server side, either in frameworks or in traditional HTTP servers, are PHP, Python, Ruby, Java, and C#.

### Database Management Systems
It is up to the discretion of the development team how the data received or requested by the client is stored on the server, but there are general guidelines that apply to most cases. It is convenient to keep static content—images, JavaScript and CSS code that do not change in the short term—​as conventional files, either on the server’s own file system or distributed across a content delivery network (CDN). Other kinds of content, such as email messages in a webmail application, product details in a shopping application, and transaction logs, are more conveniently stored in a database management system (DBMS).

The most traditional type of database management system is the relational database. In it, the application designer defines data tables and the input format accepted by each table. The set of tables in the database contains all the dynamic data consumed and produced by the application. A shopping app, for example, may have a table that contains an entry with the details of each product in the store and a table that records items purchased by a user. The table of purchased items contains references to entries in the product table, creating relationships between the tables. This approach can optimize storage and access to the data, as well as allow queries in combined tables using the language adopted by the database management system. The most popular relational database language is the Structured Query Language (SQL, pronounced “sequel”), adopted by the open source databases SQLite, MySQL, MariaDB, and PostgreSQL.

An alternative to relational databases is a form of database that does not require a rigid structure for the data. These databases are called non-relational databases or simply NoSQL. Although they may incorporate some features similar to those found in relational databases, the focus is on allowing greater flexibility in storage and access to stored data, passing the task of processing that data to the application itself. MongoDB, CouchDB, and Redis are common non-relational database management systems.

### Content Maintenance
Regardless of the database model adopted, applications have to add data and probably update it over the life span of the applications. In some applications, such as webmail, users themselves provide data to the database when using the client to send and receive messages. In other cases, such as in the shopping application, it’s important to allow the application’s maintainers to modify the database without having to resort to programming. Many organizations therefore adopt some kind of content management system (CMS), which allows non-technical users to administer the application. Therefore, for most web applications, it’s necessary to implement at least two types of clients: a non-privileged client, used by ordinary users, and privileged clients, used by special users to maintain and update the information presented by the application.

___

## Lesson 1-3 HTTP Basics

The HyperText Transfer Protocol (HTTP) defines how a client asks the server for a specific resource. Its working principle is quite simple: the client creates a request message identifying the resource it needs and forwards that message to the server via the network. In turn, the HTTP server evaluates where to extract the requested resource and sends a response message back to the client. The reply message contains details about the requested resource, followed by the resource itself.

More specifically, HTTP is the set of rules that define how the client application should format request messages that will be sent to the server. The server then follows HTTP rules to interpret the request and format reply messages. In addition to requesting or transferring requested content, HTTP messages contain extra information about the client and server involved, about the content itself, and even about its unavailability. If a resource cannot be sent, a code in the response explains the reason for the unavailability and, if possible, indicates where the resource was moved.

The part of the message that defines the resource details and other context information is called the header of the message. The part following the header, which contains the content of the corresponding resource, is called the payload of the message. Both request messages and response messages can have a payload, but in most cases, only the response message has one.

### The Client’s Request
The first stage of an HTTP data exchange between the client and the server is initiated by the client, when it writes a request message to the server. Take, for example, a common browser task: to load an HTML page from a server hosting a website, such as `https://learning.lpi.org/en/`. The address, or URL, provides several pieces of relevant information. Three pieces of information appear in this particular example:

- The protocol: HyperText Transfer Protocol Secure (https), an encrypted version of HTTP.
- The web host’s network name (learning.lpi.org)
- The location of the requested resource on the server (the /en/ directory—​in this case, the English version of the home page).

```
Note
A Uniform Resource Locator (URL) is an address that points to a resource on the Internet. This resource is usually a file that can be copied from a remote server, but URLs can also indicate dynamically generated content and data streams.
```

### How the Client Handles the URL
Before contacting the server, the client needs to convert `learning.lpi.org` to its corresponding IP address. The client uses another Internet service, the Domain Name System (DNS), to request the IP address of a host name from one or more predefined DNS servers (DNS servers are usually automatically defined by the Internet Service Provider, ISP).

With the server’s IP address, the client tries to connect to the HTTP or HTTPS port. Network ports are identification numbers defined by the Transmission Control Protocol (TCP) to intertwine and identify distinct communication channels within a client/server connection. By default, HTTP servers receive requests on TCP ports 80 (HTTP) and 443 (HTTPS).

```
Note
There are other protocols used by web applications to implement client/server communication. For audio and video calls, for example, it is more appropriate to use WebSockets, a lower level protocol that is more efficient than HTTP for transferring data streams in both directions.
```

The format of the request message that the client sends to the server is the same in HTTP and HTTPS. HTTPS is already more widely used than HTTP, because all data exchanges between client and server are encrypted, which is an indispensable feature to promote privacy and security on public networks. The encrypted connection is established between client and server even before any HTTP message is exchanged, using the Transport Layer Security (TLS) cryptographic protocol. By doing this, all HTTPS communication is encapsulated by TLS. Once decrypted, the request or response transmitted over HTTPS is no different from a request or response made exclusively over HTTP.

The third element of our URL, `/en/`, will be interpreted by the server as the location or path for the resource being requested. If the path is not provided in the URL, the default location `/` will be used. The simplest implementation of an HTTP server associates paths in URLs with files on the file system where the server is running, but this is just one of the many options available on more sophisticated HTTP servers.

### The Request Message
HTTP operates through a connection already established between client and server, usually implemented in TCP and encrypted with TLS. In fact, once a connection meeting the requirements imposed by the server is ready, an HTTP request typed by hand in plain text could generate the response from the server. In practice, however, programmers rarely need to implement routines to compose HTTP messages, as most programming languages provide mechanisms that automate the making of the HTTP message. In the case of the example URL, `https://learning.lpi.org/en/`, the simplest possible request message would have the following content:

```
GET /en/ HTTP/1.1
Host: learning.lpi.org
User-Agent: Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:87.0) Gecko/20100101 Firefox/87.0
Accept: text/html
```

The first word of the first line identifies the HTTP method. It defines which operation the client wants to perform on the server. The GET method informs the server that the client requests the resource that follows it: `/en/`. Both client and server may support more than one version of the HTTP protocol, so the version to be adopted in the data exchange is also provided in the first line: `HTTP/1.1`.

```
Note
The most recent version of the HTTP protocol is HTTP/2. Among other differences, messages written in HTTP/2 are encoded in a binary structure, whereas messages written in HTTP/1.1 are sent in plain text. This change optimizes data transmission rates, but the content of the messages is basically the same.
```

The header can contain more lines after the first one to contextualize and help identify the request to the server. The `Host` header field, for example, may appear redundant, because the server’s host has obviously been identified by the client in order to establish the connection and it’s reasonable to assume that the server knows its own identity. Nonetheless, it’s important to inform the host of the expected host name in the request header, because it is common practice to use the same HTTP server to host more than one website. (In this scenario, each specific host is called virtual host.) Therefore, the `Host` field is used by the HTTP server to identify which one the request refers to.

The `User-Agent` header field contains details about the client program making the request. This field can be used by the server to adapt the response to the needs of a specific client, but it is more often used to produce statistics about the clients using the server.

The `Accept` field is of more immediate value, because it informs the server about the format for the requested resource. If the client is indifferent about the resource format, the `Accept` field can specify `*/*` as the format in.

There are many other header fields that can be used in an HTTP message, but the fields shown in the example are enough to request a resource from the server.

In addition to the fields in the request header, the client can include other complementary data in the HTTP request that will be sent to the server. If this data consists only of simple text parameters, in the format `name=value`, they can be added to the path of the GET method. The parameters are embedded in the path after a question mark and are separated by ampersand (`&`) characters:

```
GET /cgi-bin/receive.cgi?name=LPI&email=info@lpi.org HTTP/1.1
```

In this example, `/cgi-bin/receive.cgi` is the path to the script on the server that will process and possibly use the parameters `name` and `email`, obtained from the request path. The string that corresponds to the fields, in the format `name=LPI&email=info@lpi.org`, is called query string and is supplied to the `receive.cgi` script by the HTTP server that receives the request.

When the data is made up of more than short text fields, it’s more appropriate to send it in the payload of the message. In this case, the HTTP POST method must be used so that the server receives and processes the message’s payload, according to the specifications indicated in the request header. When the POST method is used, the request header must provide the size of the payload that will be sent next and how the body is formatted:

```
POST /cgi-bin/receive.cgi HTTP/1.1
Host: learning.lpi.org
Content-Length: 1503
Content-Type: multipart/form-data; boundary=------------------------405f7edfd646a37d
```

The `Content-Length` field indicates the size in bytes of the payload and the `Content-Type` field indicates its format. The `multipart/form-data` format is the one most commonly used in traditional HTML forms that use the POST method. In this format, each field inserted in the request’s payload is separated by the code indicated by the `boundary` keyword. The POST method should be used only when appropriate, as it uses a slightly larger amount of data than an equivalent request made with the GET method. Because the GET method sends the parameters directly in the request’s message header, the total data exchange has a lower latency, because an additional connection stage to transmit the message body will not be necessary.

### The Response Header
After the HTTP server receives the request message header, the server returns a response message back to the client. An HTML file request typically has a response header like this:

```
HTTP/1.1 200 OK
Accept-Ranges: bytes
Content-Length: 18170
Content-Type: text/html
Date: Mon, 05 Apr 2021 13:44:25 GMT
Etag: "606adcd4-46fa"
Last-Modified: Mon, 05 Apr 2021 09:48:04 GMT
Server: nginx/1.17.10
```

The first line provides the version of the HTTP protocol used in the response message, which must correspond to the version used in the request header. Then, still in the first line, the status code of the response appears, indicating how the server interpreted and generated the response for the request.

The status code is a three-digit number, where the left-most digit defines the response class. There are five classes of status codes, numbered from 1 to 5, each indicating a type of action taken by the server:

1xx (Informational)
- The request was received, continuing the process.

2xx (Successful)
- The request was successfully received, understood, and accepted.

3xx (Redirection)
- Further action needs to be taken in order to complete the request.

4xx (Client Error)
- The request contains bad syntax or cannot be fulfilled.

5xx (Server Error)
- The server failed to fulfill an apparently valid request.

The second and third digits are used to indicate additional details. Code 200, for example, indicates that the request could be answered without any problems. As shown in the example, a brief text description following the response code (`OK`) can also be provided. Some specific codes are of particular interest to ensure that the HTTP client can access the resource in adverse situations or to help to identify the reason for failure in the event of an unsuccessful request:

301 Moved Permanently
- The target resource has been assigned a new permanent URL, provided by the `Location` header field in the response.

302 Found
- The target resource resides temporarily under a different URL.

401 Unauthorized
- The request has not been applied because it lacks valid authentication credentials for the target resource.

403 Forbidden
- The Forbidden reponse indicates that, although the request is valid, the server is configured to not provide it.

404 Not Found
- The origin server did not find a current representation for the target resource or is not willing to disclose that one exists.

500 Internal Server Error
- The server encountered an unexpected condition that prevented it from fulfilling the request.

502 Bad Gateway
- The server, while acting as a gateway or proxy, received an invalid response from an inbound server it accessed while attempting to fulfill the request.

Although they indicate that it was not possible to fulfill the request, status codes `4xx` and `5xx` at least indicate that the HTTP server is running and is capable of receiving requests. The `4xx` codes require an action to be taken on the client, because its URL or credentials are wrong. In contrast, `5xx` codes indicate something wrong on the server side. Therefore, in the context of web applications, these two classes of status codes indicate that the source of the error lies in the application itself, either client or server, not in the underlying infrastructure.

### Static and Dynamic Content
HTTP servers use two basic mechanisms to fulfill the content requested by the client. The first mechanism provides static content: that is, the path indicated in the request message corresponds to a file on the server’s local file system. The second mechanism provides dynamic content: that is, the HTTP server forwards the request to another program—​probably a script‒to build the response from different sources, such as databases and other files.

Although there are different HTTP servers, they all use the same HTTP communication protocol and adopt more or less the same conventions. An application that does not have a specific need can be implemented with any traditional server, such as Apache or NGINX. Both are capable of generating dynamic content and providing static content, but there are subtle differences in the configuration of each.

The location of static files to be served up, for example, is defined in different ways in Apache and NGINX. The convention is to keep these files in a specific directory for this purpose, having a name associated with the host, for example `/var/www/learning.lpi.org/`. In Apache, this path is defined by the configuration directive `DocumentRoot /var/www/learning.lpi.org`, in a section that defines a virtual host. In NGINX, the directive used is `root /var/www/learning.lpi.org` in a `server` section of the configuration file.

Whichever server you choose, the files at `/var/www/learning.lpi.org/` will be served via HTTP in almost the same way. Some fields in the response header and their contents may vary between the two servers, but fields like `Content-Type` must be present in the response header and must be consistent across any server.

### Caching
HTTP was designed to work on any type of Internet connection, fast or slow. Furthermore, most HTTP exchanges have to traverse many network nodes due to the distributed architecture of the Internet. As a result, it is important to adopt some content caching strategy to avoid the redundant transfer of previously downloaded content. HTTP transfers can work with two basic types of cache: shared and private.

A shared cache is used by more than a single client. For example, a large content provider might use caches on geographically distributed servers, so that clients get the data from their nearest server. Once a client has made a request and its response was stored in a shared cache, other clients making that same request in that same area will received the cached response.

A private cache is created by the client itself for its exclusive use. It is the type of caching the web browser does for images, CSS files, JavaScript, or the HTML document itself, so they don’t need to be downloaded again if requested in the near future.

```
Note
Not all HTTP requests must be cached. A request using the POST method, for example, implies a response associated exclusively with that particular request, so its response content should not be reused. By default, only responses to requests made using the GET method are cached. Furthermore, only responses with conclusive status codes such as 200 (OK), 206 (Partial Content), 301 (Moved Permanently), and 404 (Not Found) are suitable for caching.
```

Both the shared and private cache strategy use HTTP headers to control how the downloaded content should be cached. For the private cache, the client consults the response header and verifies whether the content in the local cache still corresponds to the current remote content. If it does, the client waives the transfer of the response payload and uses the local version.

The validity of the cached resource can be assessed in several ways. The server can provide an expiration date in the response header for the first request, so that the client discards the cached resource at the end of the term and requests it again to obtain the updated version. However, the server is not always able to determine the expiration date of a resource, so it is common to use the `ETag` response header field to identify the version of the resource, for example `Etag: "606adcd4-46fa"`.

To verify that a cached resource needs updating, the client requests only its response header from the server. If the `ETag` field matches the one in the locally stored version, the client reuses the cached content. Otherwise, the updated content of the resource is downloaded from the server.

### HTTP Sessions
In a conventional website or web application, the features that handle session control are based on HTTP headers. The server cannot assume, for example, that all requests coming from the same IP address are from the same client. The most traditional method that allows the server to associate different requests to a single client is the use of cookies, an identification tag that is given to the client by the server and that is provided in the HTTP header.

Cookies allow the server to preserve information about a specific client, even if the person running the client does not identify himself or herself explicitly. With cookies, it is possible to implement sessions where logins, shopping carts, preferences, etc., are preserved in between different requests made to the same server that provided them. Cookies are also used to track user browsing, so it is important to ask for consent before sending them.

The server sets the cookie in the response header using the `Set-Cookie` field. The field value is a `name=value` pair chosen to represent some attribute associated with a specific client. The server can, for example, create an identification number for a client that requests a resource for the first time and pass it on to the client in the response header:

```
HTTP/1.1 200 OK
Accept-Ranges: bytes
Set-Cookie: client_id=62b5b719-fcbf
```

If the client allows the use of cookies, new requests to this same server have the cookie field in the header:

```
GET /en/ HTTP/1.1
Host: learning.lpi.org
Cookie: client_id=62b5b719-fcbf
```

With this identification number, the server can retrieve specific definitions for the client and generate a customized response. It is also possible to use more than one `Set-Cookie` field to deliver different cookies to the same customer. In this way, more than one definition can be preserved on the client side.

Cookies raise both privacy issues and potential security holes, because there is a possibility that they can be transferred to another client, who will be identified by the server as the original client. Cookies used to preserve sessions can give access to sensitive information from the original client. Therefore, it’s very important for clients to adopt local protection mechanisms to prevent their cookies from being extracted and reused without authorization.


___
This documentation is provided by the Linux Professional Institute

[Attribution-NonCommercial-NoDerivatives 4.0 International (CC BY-NC-ND 4.0)](https://creativecommons.org/licenses/by-nc-nd/4.0/)

[Get the Full PDF](https://learning.lpi.org/en/learning-materials/010-160/)