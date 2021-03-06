# Node.js-Best-Practices
### Node Best Practices 

#### 1. Table of Contents 

#### 2 Introduction 

#### 3. Project Structure Practices 

#### 4.Code Style 

#### 5.Error Handling 

#### 6. Testing and Overall Quality 

#### 7.Security Practices 

#### 8.Performance Practices 

  

  

 

 

 

 

# 2.Introduction 

This document contains latest Node.js practices list to be followed 

( 21st September 2018) 

 

# 3.Project Structure Practices 

3.1 Structure your solution by components 

Partition your code into components, each gets its own folder or a dedicated codebase, and ensure that each unit is kept small and simple. Visit 'Read More' below to see examples of correct project structure 


 

 

## 3.2 Layer your components, keep Express within its boundaries 

 Do's 

Each component should contain 'layers' - a dedicated object for the web, logic and data access code. This not only draws a clean separation of concerns but also significantly eases mocking and testing the system. 

Don'ts  

Don't mix layers by passing the web layer objects (Express req, res) to business logic and data layers - this makes your application dependant on and accessible by Express only 

  

Otherwise: App that mixes web objects with other layers can not be accessed by testing code, CRON jobs and other non-Express callers 


## 3.3 Separate Express 'app' and 'server' 

Separate your 'Express' definition to at least two files: 

 a)The API declaration (app.js)  

b) Networking concerns (WWW).  

For even better structure, locate your API declaration within components 

Otherwise: Your API will be accessible for testing via HTTP calls only (slower and much harder to generate coverage reports). It probably won't be a big pleasure to maintain hundreds of lines of code in a single file 


 

## 3.4 Use environment aware, secure and hierarchical config 

A perfect and flawless configuration setup should ensure 

 (a) keys can be read from file AND from environment variable 

 (b) secrets are kept outside committed code  

(c) config is hierarchical for easier findability.  

There are a few packages that can help tick most of those boxes like rc, nconf and config 


 

 

# 4.Error Handling Practices 

 ## 4.1 Use Async-Await or promises for async error handling 

TL;DR: Handling async errors in callback style is probably the fastest way to hell (a.k.a the pyramid of doom). The best gift you can give to your code is using a reputable promise library or async-await instead which enables a much more compact and familiar code syntax like try-catch 

Otherwise: Node.js callback style, function(err, response), is a promising way to un-maintainable code due to the mix of error handling with casual code, excessive nesting and awkward coding patterns 


 

 ## 4.2 Use only the built-in Error object 

TL;DR: Many throws errors as a string or as some custom type – this complicates the error handling logic and the interoperability between modules. Whether you reject a promise, throw an exception or an emit error – using only the built-in Error object will increase uniformity and prevent loss of information 

Otherwise: When invoking some component, being uncertain which type of errors come in return – it makes proper error handling much harder. Even worse, using custom types to describe errors might lead to loss of critical error information like the stack trace! 


 
 
 

 ## 4.3 Distinguish operational vs programmer errors 

TL;DR: Operational errors (e.g. API received an invalid input) refer to known cases where the error impact is fully understood and can be handled thoughtfully. On the other hand, programmer error (e.g. trying to read undefined variable) refers to unknown code failures that dictate to gracefully restart the application 

Otherwise: You may always restart the application when an error appears, but why let ~5000 online users down because of a minor, predicted, operational error? the opposite is also not ideal – keeping the application up when an unknown issue (programmer error) occurred might lead to an unpredicted behavior. Differentiating the two allows acting tactfully and applying a balanced approach based on the given context 


 
 

 ## 4.4 Handle errors centrally, not within an Express middleware 

TL;DR: Error handling logic such as mail to admin and logging should be encapsulated in a dedicated and centralized object that all endpoints (e.g. Express middleware, cron jobs, unit-testing) call when an error comes in 

Otherwise: Not handling errors within a single place will lead to code duplication and probably to improperly handled errors 


 
 

 ## 4.5 Document API errors using Swagger 

TL;DR: Let your API callers know which errors might come in return so they can handle these thoughtfully without crashing. This is usually done with REST API documentation frameworks like Swagger 

Otherwise: An API client might decide to crash and restart only because he received back an error he couldn’t understand. Note: the caller of your API might be you (very typical in a microservice environment) 


 
 
 

 ## 4.6 Shut the process gracefully when a stranger comes to town 

TL;DR: When an unknown error occurs (a developer error, see best practice number #3)- there is uncertainty about the application healthiness. A common practice suggests restarting the process carefully using a ‘restarter’ tool like Forever and PM2 

Otherwise: When an unfamiliar exception is caught, some object might be in a faulty state (e.g an event emitter which is used globally and not firing events anymore due to some internal failure) and all future requests might fail or behave crazily 


 
 
 

 ## 4.7 Use a mature logger to increase error visibility 

TL;DR: A set of mature logging tools like Winston, Bunyan or Log4J, will speed-up error discovery and understanding. So forget about console.log 

Otherwise: Skimming through console.logs or manually through messy text file without querying tools or a decent log viewer might keep you busy at work until late 

 

 ## 4.8 Test error flows using your favorite test framework 

TL;DR: Whether professional automated QA or plain manual developer testing – Ensure that your code not only satisfies positive scenario but also handle and return the right errors. Testing frameworks like Mocha & Chai and jest can handle this easily  

Otherwise: Without testing, whether automatically or manually, you can’t rely on our code to return the right errors. Without meaningful errors – there’s no error handling 


 
 
 

 ## 4.9 Discover errors and downtime using APM products 

TL;DR: Monitoring and performance products (a.k.a APM) proactively gauge your codebase or API so they can automagically highlight errors, crashes and slow parts that you were missing 

Otherwise: You might spend great effort on measuring API performance and downtimes, probably you’ll never be aware which are your slowest code parts under real-world scenario and how these affect the UX 


 
 
 

 ## 4.10 Catch unhandled promise rejections 

TL;DR: Any exception thrown within a promise will get swallowed and discarded unless a developer didn’t forget to explicitly handle. Even if your code is subscribed to process.uncaughtException! Overcome this by registering to the event process.unhandledRejection 

Otherwise: Your errors will get swallowed and leave no trace. Nothing to worry about 


 

 ## 4.11 Fail fast, validate arguments using a dedicated library 

TL;DR: This should be part of your Express best practices – Assert API input to avoid nasty bugs that are much harder to track later. The validation code is usually tedious unless you are using a very cool helper library like Joi 

Otherwise: Consider this – your function expects a numeric argument “Discount” which the caller forgets to pass, later on, your code checks if Discount!=0 (amount of allowed discount is greater than zero), then it will allow the user to enjoy a discount. OMG, what a nasty bug. Can you see it? 

 

 

 

 

 

 

# 5. Code Style Practices 

 ## 5.1 Use ESLint 

TL;DR: ESLint is the de-facto standard for checking possible code errors and fixing code style, not only to identify nitty-gritty spacing issues but also to detect serious code anti-patterns like developers throwing errors without classification. Though ESLint can automatically fix code styles, other tools like prettier and beautify are more powerful in formatting the fix and work in conjunction with ESLint 

Otherwise: Developers will focus on tedious spacing and line-width concerns and time might be wasted overthinking about the project's code style 

 

 ## 5.2 Node.js Specific Plugins 

TL;DR: On top of ESLint standard rules that cover vanilla JS only, add Node-specific plugins like eslint-plugin-node, eslint-plugin-mocha and eslint-plugin-node-security 

Otherwise: Many faulty Node.js code patterns might escape under the radar. For example, developers might require(variableAsPath) files with a variable given as path which allows attackers to execute any JS script. Node.js linters can detect such patterns and complain early 

 
 
 

## 5.3 Start a Codeblock's Curly Braces on the Same Line 

TL;DR: The opening curly braces of a code block should be in the same line of the opening statement 

Code Example 

// Dofunction someFunction() {  // code block}// Avoidfunction someFunction(){  // code block} 

Otherwise: Deferring from this best practice might lead to unexpected results, as seen in the StackOverflow thread below: 


 
 
 

## 5.4 Don't Forget the Semicolon 

TL;DR: While not unanimously agreed upon, it is still recommended to put a semicolon at the end of each statement. This will make your code more readable and explicit to other developers who read it 

Otherwise: As seen in the previous section, JavaScript's interpreter automatically adds a semicolon at the end of a statement if there isn't one, or considers a statement as not ended where it should, which might lead to some undesired results 

Code example 

// Doconst count = 2;(function doSomething() {  // do something amazing}());// Avoid — throws exceptionconst count = 2 // it tries to run 2(), but 2 is not a function(function doSomething() {  // do something amazing}()) 

 
 
 

 ## 5.5 Name Your Functions 

TL;DR: Name all functions, including closures and callbacks. Avoid anonymous functions. This is especially useful when profiling a node app. Naming all functions will allow you to easily understand what you're looking at when checking a memory snapshot 

Otherwise: Debugging production issues using a core dump (memory snapshot) might become challenging as you notice significant memory consumption from anonymous functions 

 
 
 

## 5.6 Naming conventions for variables, constants, functions and classes 

TL;DR: Use lowerCamelCase when naming constants, variables and functions and UpperCamelCase (capital first letter as well) when naming classes. This will help you to easily distinguish between plain variables/functions, and classes that require instantiation. Use descriptive names, but try to keep them short 

Otherwise: Javascript is the only language in the world which allows invoking a constructor ("Class") directly without instantiating it first. Consequently, Classes and function-constructors are differentiated by starting with UpperCamelCase 

Code Example 

// for class name we use UpperCamelCaseclass SomeClassExample {}// for const names we use the const keyword and lowerCamelCaseconst config = {  key: 'value'};// for variables and functions names we use lowerCamelCaselet someVariableExample = 'value';function doSomething() {} 

 
 
 

## 5.7 Prefer const over let. Ditch the var 

TL;DR: Using const means that once a variable is assigned, it cannot be reassigned. Preferring const will help you to not be tempted to use the same variable for different uses, and make your code clearer. If a variable needs to be reassigned, in a for loop, for example, use let to declare it. Another important aspect of let is that a variable declared using it is only available in the block scope in which it was defined. var is function scoped, not block scoped, and shouldn't be used in ES6 now that you have const and let at your disposal 

Otherwise: Debugging becomes way more cumbersome when following a variable that frequently changes 


 
 

## 5.8 Requires come first, and not inside functions 

TL;DR: Require modules at the beginning of each file, before and outside of any functions. This simple best practice will not only help you easily and quickly tell the dependencies of a file right at the top but also avoids a couple of potential problems 

Otherwise: Requires are run synchronously by Node.js. If they are called from within a function, it may block other requests from being handled at a more critical time. Also, if a required module or any of its own dependencies throw an error and crash the server, it is best to find out about it as soon as possible, which might not be the case if that module is required from within a function 

 

## 5.9 Do Require on the folders, not directly on the files 

TL;DR: When developing a module/library in a folder, place an index.js file that exposes the module's internals so every consumer will pass through it. This serves as an 'interface' to your module and eases future changes without breaking the contract 

Otherwise: Changing the internal structure of files or the signature may break the interface with clients 

Code example 

// Domodule.exports.SMSProvider = require('./SMSProvider');module.exports.SMSNumberResolver = require('./SMSNumberResolver');// Avoidmodule.exports.SMSProvider = require('./SMSProvider/SMSProvider.js');module.exports.SMSNumberResolver = require('./SMSNumberResolver/SMSNumberResolver.js'); 

 
 
 

## 5.10 Use the === operator 

TL;DR: Prefer the strict equality operator === over the weaker abstract equality operator ==. == will compare two variables after converting them to a common type. There is no type conversion in ===, and both variables must be of the same type to be equal 

Otherwise: Unequal variables might return true when compared with the == operator 

Code example 

'' == '0'           // false0 == ''             // true0 == '0'            // truefalse == 'false'    // falsefalse == '0'        // truefalse == undefined  // falsefalse == null       // falsenull == undefined   // true' \t\r\n ' == 0     // true 

All statements above will return false if used with === 

 
 
 

## 5.11 Use Async Await, avoid callbacks 

TL;DR: Node 8 LTS now has full support for Async-await. This is a new way of dealing with asynchronous code which supersedes callbacks and promises. Async-await is non-blocking, and it makes asynchronous code look synchronous. The best gift you can give to your code is using async-await which provides a much more compact and familiar code syntax like try-catch 

Otherwise: Handling async errors in callback style is probably the fastest way to hell - this style forces to check errors all over, deal with awkward code nesting and make it difficult to reason about the code flow 


 
 
 

## 5.12 Use Fat (=>) Arrow Functions 

TL;DR: Though it's recommended to use async-await and avoid function parameters when dealing with older API that accept promises or callbacks - arrow functions make the code structure more compact and keep the lexical context of the root function (i.e. 'this') 

Otherwise: Longer code (in ES5 functions) is more prone to bugs and cumbersome to read 


 
## 5.13 Use Util And Config File Separately 

Always make it a habit of using util.js file. The functions which are to be used frequently must be written in util.js file. Use this file as a variable in your module. Doing this will reduce the number of global variables and code length. 

Make sure you make a config file which stores your constant parameters. This can be well understood that if you want to show the details of top five students. There are chances that it can be increased to ten tomorrow, so don’t hard code the limit value. Do keep it in config file. 

 
## 5.14 Setup .npmrc 

The .npmrc file assures npm install always updates the package.json and imposes the version of installed dependency to be an exact match. 

 

## 5.15 Use environment variables 

Don’t clutter your project with environment-specific config files. Rather, take advantage of environment variables. Utilize environment variables even for the early stages of a project to ensure there’s no leakage of sensitive info, and basically to build the code properly from the beginning. Configuration management is always a matter of a discussion. How does one decouple their code from the databases, services, etc. which it has to use during node.js development, production, and QA? The method advised in Node.js is to use environment variables and look up the values from process.env in your code. 

 

## 5.16 Add scripts to your package.json 

If there’s one thing all applications need, it’s a launch script. Knowing which file to call first and with what arguments can be an epic adventure of discovery on some projects. Good thing NPM has a standard way to start all node applications. 

Simply add a scripts property and object to your package.json with a start key. It’s value should be the command to launch your app. 

 

# 6. Testing And Overall Quality Practices 

 ## 6.1 At the very least, write API (component) testing 

TL;DR: Most projects just don't have any automated testing due to short timetables or often the 'testing project' run out of control and being abandoned. For that reason, prioritize and start with API testing which is the easiest to write and provide more coverage than unit testing (you may even craft API tests without code using tools like Postman. Afterward, should you have more resources and time, continue with advanced test types like unit testing, DB testing, performance testing, etc 

Otherwise: You may spend long days on writing unit tests to find out that you got only 20% system coverage 
 

## 6.2 Detect code issues with a linter 

TL;DR: Use a code linter to check basic quality and detect anti-patterns early. Run it before any test and add it as a pre-commit git-hook to minimize the time needed to review and correct any issue. Also check Section 3 on Code Style Practices 

 

Otherwise: You may let pass some anti-pattern and possible vulnerable code to your production environment. 

 

## 6.3 Carefully choose your CI platform (Jenkins vs CircleCI vs Travis vs Rest of the world) 

TL;DR: Your continuous integration platform (CICD) will host all the quality tools (e.g test, lint) so it should come with a vibrant ecosystem of plugins. Jenkins used to be the default for many projects as it has the biggest community along with a very powerful platform at the price of complex setup that demands a steep learning curve. Nowadays, it became much easier to set up a CI solution using SaaS tools like CircleCI and others. These tools allow crafting a flexible CI pipeline without the burden of managing the whole infrastructure. Eventually, it's a trade-off between robustness and speed - choose your side carefully 

Otherwise: Choosing some niche vendor might get you blocked once you need some advanced customization. On the other hand, going with Jenkins might burn precious time on infrastructure setup 


## 6.4 Constantly inspect for vulnerable dependencies 

TL;DR: Even the most reputable dependencies such as Express have known vulnerabilities. This can get easily tamed using community and commercial tools such as 🔗 nsp that can be invoked from your CI on every build 

Otherwise: Keeping your code clean from vulnerabilities without dedicated tools will require to constantly follow online publications about new threats. Quite tedious 

 

## 6.5 Tag your tests 

TL;DR: Different tests must run on different scenarios: quick smoke, IO-less, tests should run when a developer saves or commits a file, full end-to-end tests usually run when a new pull request is submitted, etc. This can be achieved by tagging tests with keywords like #cold #api #sanity so you can grep with your testing harness and invoke the desired subset. For example, this is how you would invoke only the sanity test group with Mocha: mocha --grep 'sanity' 

Otherwise: Running all the tests, including tests that perform dozens of DB queries, any time a developer makes a small change can be extremely slow and keeps developers away from running tests 

 

## 6.6 Check your test coverage, it helps to identify wrong test patterns 

TL;DR: Code coverage tools like Istanbul/NYC are great for 3 reasons: it comes for free (no effort is required to benefit this reports), it helps to identify a decrease in testing coverage, and last but not least it highlights testing mismatches: by looking at colored code coverage reports you may notice, for example, code areas that are never tested like catch clauses (meaning that tests only invoke the happy paths and not how the app behaves on errors). Set it to fail builds if the coverage falls under a certain threshold 

Otherwise: There won't be any automated metric telling you when a large portion of your code is not covered by testing 
 

## 6.7 Inspect for outdated packages 

TL;DR: Use your preferred tool (e.g. 'npm outdated' or npm-check-updates to detect installed packages which are outdated, inject this check into your CI pipeline and even make a build fail in a severe scenario. For example, a severe scenario might be when an installed package is 5 patch commits behind (e.g. local version is 1.3.1 and repository version is 1.3.8) or it is tagged as deprecated by its author - kill the build and prevent deploying this version 

Otherwise: Your production will run packages that have been explicitly tagged by their author as risky 
 

## 6.8 Use docker-compose for e2e testing 

TL;DR: End to end (e2e) testing which includes live data used to be the weakest link of the CI process as it depends on multiple heavy services like DB. Docker-compose turns this problem into a breeze by crafting production-like environment using a simple text file and easy commands. It allows crafting all the dependent services, DB and isolated network for e2e testing. Last but not least, it can keep a stateless environment that is invoked before each test suite and dies right after 

Otherwise: Without docker-compose teams must maintain a testing DB for each testing environment including developers machines, keep all those DBs in sync so test results won't vary across environments 

 
 
 

## 6.9 Refactor regularly using static analysis tools 

TL;DR: Using static analysis tools helps by giving objective ways to improve code quality and keep your code maintainable. You can add static analysis tools to your CI build to fail when it finds code smells. Its main selling points over plain linting are the ability to inspect quality in the context of multiple files (e.g. detect duplications), perform advanced analysis (e.g. code complexity) and follow the history and progress of code issues. Two examples of tools you can use are Sonarqube (2,600+ stars) and Code Climate (1,500+ stars). 

Otherwise: With poor code quality, bugs and performance will always be an issue that no shiny new library or state of the art features can fix. 

🔗 Read More: Refactoring! 

 

# 7. Security Best Practices 

 

## 7.1. Embrace linter security rules 

TL;DR: Make use of security-related linter plugins such as eslint-plugin-security to catch security vulnerabilities and issues as early as possible , at best  while they're being coded. This can help catching security weaknesses like using eval, invoking a child process or importing a module with a string literal (e.g. user input). Click 'Read more' below to see code examples that will get caught by a security linter 

Otherwise: What could have been a straightforward security weakness during development becomes a major issue in production. Also, the project may not follow consistent code security practices, leading to vulnerabilities being introduced, or sensitive secrets committed into remote repositories 


 
 
 

## 7.2. Limit concurrent requests using a middleware 

 

TL;DR: DOS attacks are very popular and relatively easy to conduct. Implement rate limiting using an external service such as cloud load balancers, cloud firewalls, nginx, or (for smaller and less critical apps) a rate limiting middleware (e.g. express-rate-limit) 

Otherwise: An application could be subject to an attack resulting in a denial of service where real users receive a degraded or unavailable service. 


 
 

## 7.3 Extract secrets from config files or use packages to encrypt them 

TL;DR: Never store plain-text secrets in configuration files or source code. Instead, make use of secret-management systems like Vault products, Kubernetes/Docker Secrets, or using environment variables. As a last result, secrets stored in source control must be encrypted and managed (rolling keys, expiring, auditing, etc). Make use of pre-commit/push hooks to prevent committing secrets accidentally 

Otherwise: Source control, even for private repositories, can mistakenly be made public, at which point all secrets are exposed. Access to source control for an external party will inadvertently provide access to related systems (databases, apis, services, etc). 


 
 
 

## 7.4. Prevent query injection vulnerabilities with ORM/ODM libraries 

TL;DR: To prevent SQL/NoSQL injection and other malicious attacks, always make use of an ORM/ODM or a database library that escapes data or supports named or indexed parameterized queries, and takes care of validating user input for expected types. Never just use JavaScript template strings or string concatenation to inject values into queries as this opens your application to a wide spectrum of vulnerabilities. All the reputable Node.js data access libraries (e.g. Sequelize, Knex, mongoose) have built-in protection agains injection attacks 

Otherwise: Unvalidated or unsanitized user input could lead to operator injection when working with MongoDB for NoSQL, and not using a proper sanitization system or ORM will easily allow SQL injection attacks, creating a giant vulnerability. 


 
 
 

## 7.5. Collection of generic security best practices 

TL;DR: These is a collection of security advice that are not related directly to Node.js - the Node implementation is not much different than any other language. Click read more to skim through. 


 

## 7.6. Adjust the HTTP response headers for enhanced security 

TL;DR: Your application should be using secure headers to prevent attackers from using common attacks like cross-site scripting (XSS), clickjacking and other malicious attacks. These can be configured easily using modules like helmet. 

Otherwise: Attackers could perform direct attacks on your application's users, leading huge security vulnerabilities 


 
 
 

## 7.7. Constantly and automatically inspect for vulnerable dependencies 

TL;DR: With the npm ecosystem it is common to have many dependencies for a project. Dependencies should always be kept in check as new vulnerabilities are found. Use tools like npm audit or snyk to track, monitor and patch vulnerable dependencies. Integrate these tools with your CI setup so you catch a vulnerable dependency before it makes it to production. 

Otherwise: An attacker could detect your web framework and attack all its known vulnerabilities. 


## 7.8. Avoid using the Node.js crypto library for handling passwords, use Bcrypt 

TL;DR: Passwords or secrets (API keys) should be stored using a secure hash + salt function like bcrypt, that should be a preferred choice over its JavaScript implementation due to performance and security reasons. 

Otherwise: Passwords or secrets that are persisted without using a secure function are vulnerable to brute forcing and dictionary attacks that will lead to their disclosure eventually. 


 
 
 

## 7.9. Escape HTML, JS and CSS output 

TL;DR: Untrusted data that is sent down to the browser might get executed instead of just being displayed, this is commonly being referred as a cross-site-scripting (XSS) attack. Mitigate this by using dedicated libraries that explicitly mark the data as pure content that should never get executed (i.e. encoding, escaping) 

Otherwise: An attacker might store a malicious JavaScript code in your DB which will then be sent as-is to the poor clients 


 
 
## 7.10. Validate incoming JSON schemas 

TL;DR: Validate the incoming requests' body payload and ensure it qualifies the expectations, fail fast if it doesn't. To avoid tedious validation coding within each route you may use lightweight JSON-based validation schemas such as jsonschema or joi 

Otherwise: Your generosity and permissive approach greatly increases the attack surface and encourages the attacker to try out many inputs until they find some combination to crash the application 


 
 

## 7.11. Support blacklisting JWTs 

TL;DR: When using JSON Web Tokens (for example, with Passport.js), by default there's no mechanism to revoke access from issued tokens. Once you discover some malicious user activity, there's no way to stop them from accessing the system as long as they hold a valid token. Mitigate this by implementing a blacklist of untrusted tokens that are validated on each request. 

Otherwise: Expired, or misplaced tokens could be used maliciously by a third party to access an application and impersonate the owner of the token. 


 
 

 ## 7.12. Limit the allowed login requests of each user 

TL;DR: A brute force protection middleware such as express-brute should be used inside an express application to prevent brute force/dictionary attacks on sensitive routes such as /admin or /login based on request properties such as the user name, or other identifiers such as body parameters 

Otherwise: An attacker can issue unlimited automated password attempts to gain access to privileged accounts on an application 


 
 

## 7.13. Run Node.js as non-root user 

TL;DR: There is a common scenario where Node.js runs as a root user with unlimited permissions. For example, this is the default behaviour in Docker containers. It's recommended to create a non-root user and either bake it into the Docker image (examples given below) or run the process on this users' behalf by invoking the container with the flag "-u username" 

Otherwise: An attacker who manages to run a script on the server gets unlimited power over the local machine (e.g. change iptable and re-route traffic to his server) 


 
 
 

## 7.14. Limit payload size using a reverse-proxy or a middleware 

TL;DR: The bigger the body payload is, the harder your single thread works in processing it. This is an opportunity for attackers to bring servers to their knees without tremendous amount of requests (DOS/DDOS attacks). Mitigate this limiting the body size of incoming requests on the edge (e.g. firewall, ELB) or by configuring express body parser to accept only small-size payloads 

Otherwise: Your application will have to deal with large requests, unable to process the other important work it has to accomplish, leading to performance implications and vulnerability towards DOS attacks 


 
 
 

## 7.15. Avoid JavaScript eval statements 

TL;DR: eval is evil as it allows executing a custom JavaScript code during run time. This is not just a performance concern but also an important security concern due to malicious JavaScript code that may be sourced from user input. Another language feature that should be avoided is new Function constructor. setTimeout and setInterval should never be passed dynamic JavaScript code either. 

Otherwise: Malicious JavaScript code finds a way into a text passed into eval or other real-time evaluating JavaScript language functions, and will gain complete access to JavaScript permissions on the page. This vulnerability is often manifested as an XSS attack. 


 
 
## 7.16. Prevent evil RegEx from overloading your single thread execution 

TL;DR: Regular Expressions, while being handy, pose a real threat to JavaScript applications at large, and the Node.js platform in particular. A user input for text to match might require an outstanding amount of CPU cycles to process. RegEx processing might be inefficient to an extent that a single request that validates 10 words can block the entire event loop for 6 seconds and set the CPU on 🔥. For that reason, prefer third-party validation packages like validator.js instead of writing your own Regex patterns, or make use of safe-regex to detect vulnerable regex patterns 

Otherwise: Poorly written regexes could be susceptible to Regular Expression DoS attacks that will block the event loop completely. For example, the popular moment package was found vulnerable with malicious RegEx usage in November of 2017 


 
 
 

 ## 7.17. Avoid module loading using a variable 

TL;DR: Avoid requiring/importing another file with a path that was given as parameter due to the concern that it could have originated from user input. This rule can be extended for accessing files in general (i.e. fs.readFile()) or other sensitive resource access with dynamic variables originating from user input. Eslint-plugin-security linter can catch such patterns and warn early enough 

Otherwise: Malicious user input could find its way to a parameter that is used to require tampered files, for example a previously uploaded file on the filesystem, or access already existing system files. 


 
 
 

## 7.18. Run unsafe code in a sandbox 

TL;DR: When tasked to run external code that is given at run-time (e.g. plugin), use any sort of 'sandbox' execution environment that isolates and guards the main code against the plugin. This can be achieved using a dedicated process (e.g. cluster.fork()), serverless environment or dedicated npm packages that acting as a sandbox 

Otherwise: A plugin can attack through an endless variety of options like infinite loops, memory overloading, and access to sensitive process environment variables 


 
 
 

## 7.19. Take extra care when working with child processes 

TL;DR: Avoid using child processes when possible and validate and sanitize input to mitigate shell injection attacks if you still have to. Prefer using child_process.execFile which by definition will only execute a single command with a set of attributes and will not allow shell parameter expansion. 

Otherwise: Naive use of child processes could result in remote command execution or shell injection attacks due to malicious user input passed to an unsanitized system command. 


 
 
 

## 7.20. Hide error details from clients 

TL;DR: An integrated express error handler hides the error details by default. However, great are the chances that you implement your own error handling logic with custom Error objects (considered by many as a best practice). If you do so, ensure not to return the entire Error object to the client, which might contain some sensitive application details 

Otherwise: Sensitive application details such as server file paths, third party modules in use, and other internal workflows of the application which could be exploited by an attacker, could be leaked from information found in a stack trace 


 
 
 

## 7.21. Configure 2FA for npm or Yarn 

TL;DR: Any step in the development chain should be protected with MFA (multi-factor authentication), npm/Yarn are a sweet opportunity for attackers who can get their hands on some developer's password. Using developer credentials, attackers can inject malicious code into libraries that are widely installed across projects and services. Maybe even across the web if published in public. Enabling 2-factor-authentication in npm leaves almost zero chances for attackers to alter your package code. 

Otherwise: Have you heard about the eslint developer who's password was hijacked? 

 
 
 

## 7.22. Modify session middleware settings 

TL;DR: Each web framework and technology has its known weaknesses - telling an attacker which web framework we use is a great help for them. Using the default settings for session middlewares can expose your app to module- and framework-specific hijacking attacks in a similar way to the X-Powered-By header. Try hiding anything that identifies and reveals your tech stack (E.g. Node.js, express) 

Otherwise: Cookies could be sent over insecure connections, and an attacker might use session identification to identify the underlying framework of the web application, as well as module-specific vulnerabilities 


 
 
 

## 7.23. Avoid DOS attacks by explicitly setting when a process should crash 

TL;DR: The Node process will crash when errors are not handled. Many best practices even recommend to exit even though an error was caught and got handled. Express, for example, will crash on any asynchronous error - unless you wrap routes with a catch clause. This opens a very sweet attack spot for attackers who recognize what input makes the process crash and repeatedly send the same request. There's no instant remedy for this but a few techniques can mitigate the pain: Alert with critical severity anytime a process crashes due to an unhandled error, validate the input and avoid crashing the process due to invalid user input, wrap all routes with a catch and consider not to crash when an error originated within a request (as opposed to what happens globally) 

Otherwise: This is just an educated guess: given many Node.js applications, if we try passing an empty JSON body to all POST requests - a handful of applications will crash. At that point, we can just repeat sending the same request to take down the applications with ease 

 

 
 

References :  https://github.com/i0natan/nodebestpractices 

https://github.com/airbnb/javascript 

https://goldbergyoni.com/checklist-best-practice-of-node-js-in-production/ 

https://medium.com/front-end-hacking/error-handling-in-node-javascript-suck-unless-you-know-this-2018-aa0a14cfdd9d 

view sourceprint? 

https://www.codementor.io/mattgoldspink/nodejs-best-practices-du1086jja 

https://medium.com/@zurfyx/building-a-scalable-node-js-express-app-1be1a7134cfd 

https://medium.mybridge.co/learn-node-js-we-created-a-directory-of-top-articles-from-2017-783e809452dd 
https://medium.com/@nodepractices/were-under-attack-23-node-js-security-best-practices-e33c146cb87d 

https://hackernoon.com/restful-api-design-with-node-js-26ccf66eab09 
