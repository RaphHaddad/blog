---
title: 'Migrations VS DACPACs'
date: 2019-01-18T00:00:00.000+11:00
draft: true
---

Dealing with SQL Database changes on production should always be automated. There are various techniques we can use and today I will compare two popular approaches. A migrations approach and a DACPAC approach.

## The two approaches
Below is an explanation of the two approaches. Feel free to skip this section and go straight to the next section if you are aware of the two approaches

### Migrations
The migrations approach involves running a set of scripts against a target database in a predetermined order. The target database will have a meta table with the scripts that have already been run, so that they are not run again. Popular .NET tools include [DbUp](https://dbup.github.io/) and [Entity Framework Migrations](https://docs.microsoft.com/en-us/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application). 

### DACPAC
A DACPAC (Data-tier Application Component Package) is a package that contains the _state_ of your database. It is generated from tools such as SQL Projects that you can create from Visual Studio 2017. From a code perspective, this means a set of `CREATE` scripts for all your entities. Occasionally, when you modify an object the `CREATE` scripts may not accurately reflect the state of the database (for example: when you rename a column) in this case a _refactor log_ is kept in the DACPAC.

![Which route to take?](/images/migrations-vs-dacpacs-which-steps.jpg) "Which route to take? Credit: [Greg Jeanneau](https://unsplash.com/@gregjeanneau)"

## Criteria 1: Ease of Setting Up
You can easily setup migrations to run on your application's entry point, they are included in ORM (object relational mapper) frameworks (such as Entity Framework) and you'll be able to execute your migrations with one method call.

On the other hand, generating a DACPAC file means creating a new SQL Project that is difficult to reference from your existing application. You'll need to create a step in your release pipeline to ensure that it is applied and this may mean an added overhead when trying to initially get setup.

Here, __migrations__ win. Simply include DbUp or Entity Framework in your solution, modify your entry point to execute migrations and that's it!

## Criteria 2: Code Maintainability
Migrations work by having a set of scripts in a folder that may grow over time. When modifying an existing SQL Database object, you'll need to create a new file for each set of changes that get deployed. If you're frequently modifying a few lines of code on programmability objects (like stored procedures or functions) you will need to commit multiple `SQL` files. These files will have almost identical code, as you'll need a separate `ALTER` statement for each set of changes. On the other hand, SQL Projects that generate DACPACs will have all stored procedures, functions, or tables as `CREATE` statements, so if you need to modify them, you'll modify the `CREATE` statement on the original file. This gives you a git history of the stored procedure or function just like any code file.

Over time, you could have hundreds of SQL Scripts in a migrations folder with almost identical code. This is why in terms of maintainability __DACPACs__ win!

## Criteria 3: Integration with Release Pipelines
Migrations are easy to integrate into a release pipeline. If you use Entity Framework migrations, you automatically get the `dotnet ef database` command. If you use DbUp, an executable is built that is used to run migrations against a target database.

Likewise, DACPACs are easy to integrate into a release pipeline. The tool `SqlPackage.exe` is used to deploy a DACPAC to a target database. This tool comes with SQL Server 2017 or Visual Studio 2017.

It was hard to decide a winner here, but I'll choose __Migrations__ only because it's a bit more difficult getting `SqlPackage.exe` on a build agent. Migrations though, are usually self-contained or come with the standard `dotnet` command line tool.
 
## Criteria 4: Development Experience
The development experience with migrations is great, all you do is add migrations to your migration folder! For example: you can modify a field on your ORM classes and create a migration script for it. If you're using Entity Framework migrations the  `dotnet ef database` command scaffolds the migrations for you, even easier! 

Using a SQL Project to generate a DACPAC is a great experience as well. The tooling allows you to create Pre and Post deployment scripts, rename fields, write unit tests, add fields, and user permissions. Basically, anything you can do on a database, you'll be able to do with SQL Projects. In this case, the development experience may not always be tightly-coupled to your application code, but that's not a bad thing. In fact, it can be a positive thing, especially if your database is used by multiple applications.

Migrations definitely provide an easy development experience. Easy, does not necessarily mean better though and there are problems associated with the development experience using migrations. Code is read a lot more than it is written, and the underlying structure of a database is better understood using SQL Projects than they are using migrations. 

For example, if you've chosen a migration approach and a field is introduced into a previously created table, you will be reading two separate migrations in isolation to each other, however, using SQL Projects everything is declared as `CREATE` statements so you will read the definition of the table from one file.

Another example is modifying programmability objects, every time this type of object needs modification a separate `ALTER` statement needs to be created that has almost the same code as the previous declaration of it. This means the experience of code reviewing and checking differences between versions of the same object is greatly diminished.

Initially, I though the development experience of using migrations was better because it was simpler. After several months of using SQL Projects though, I would say that __DACPACs__ win on this criteria.

## Criteria 5: Testability
Modern day tooling has made writing tests against in-memory databases easy. With Entity Framework you can use the [In-Memory Database Provider](https://docs.microsoft.com/en-us/ef/core/providers/in-memory/) for your tests and even run your migrations against it! This means that you can write automated tests in the same language as your application code while hitting a database that behaves close to the one you have deployed in production.

SQL Projects give you the ability to write tests against any valid database. The tests themselves are written in `T-SQL` and the asserts can be configured to (non-exhaustive):

- Result count
- Scalar Value
- Execution time

__DACPACs__ win here. Writing automated tests against a database isolated away from application code, means that the unit-under-test is the database. This achieves a better separation of concerns and more granular tests as the tests themselves are hitting the database directly not the application code that hits the database.

## Conclusion
A count of each criteria will show that a migrations approach won on two occasions, while a DACPAC approach won on three occasions. This does not mean that using a DACPAC approach is the best solution for all scenarios.

Each approach has its advantages and to help you pick which approach is right for you. It's important to ask yourself the right questions

- Is my application the only one accessing the database?
- Am I using my SQL Database only as a datastore?
- Will I only be using an ORM to access to the database?

If you answered 'yes' to most of the above, then you should consider taking a migration approach.

- Am I reliant on stored procedures?
- Is the database used by several applications?
- Will I need to tune database indexes?
- Do I want greater control of data types and relationships?

If you answered 'yes' to most of the above, then you should consider using DACPACs.

You can always opt for a migrations approach while your database is simple and easily convert it to a SQL Project (which builds a DACPAC) when your database becomes more complex. Just reverse engineer your database by generating `CREATE` scripts using [SQL Server Management Studio](https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017) and add them to a SQL Project.

A definite answer to which approach is best would probably not do every scenario justice, as such, I would encourage you to learn both approaches to properly understand which approach to choose. Whatever decision you make, it's easy to migrate from one to the other. What's important is that you are aware of the value that both approaches provide.
