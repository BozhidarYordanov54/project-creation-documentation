# Entity Framework Project Creation Documentation

## What is EF Core?

Entity Framework Core (EF Core) is a modern, lightweight, and extensible version of Entity Framework. It is an Object-Relational Mapper (ORM) for .NET, allowing developers to work with a database using .NET objects, eliminating the need for most data-access code.

### Key Features:
- **Cross-Platform**: Works on Windows, macOS, and Linux.
- **LINQ Support**: Enables querying the database using LINQ (Language Integrated Query).
- **Migrations**: Helps manage database schema changes over time.
- **Multiple Database Providers**: Supports SQL Server, SQLite, PostgreSQL, MySQL, and more.

# Preparation

## Installing Dotnet Entity Framework

This installs dotnet entity framework globally to use.

```bash
dotnet tool install --global dotnet-ef
```

And with the flag --version we can specify what version of the dotnet to install.
```bash
dotnet tool install --global dotnet-ef --version 9.0.0
```

You can verify if its installed with
```bash
dotnet ef
```

The expected output should look something like this:
```output
                     _/\__
               ---==/    \\
         ___  ___   |.    \|\
        | __|| __|  |  )   \\\
        | _| | _|   \_/ |  //|\\
        |___||_|       /   \\\/\\

	Entity Framework Core .NET Command-line Tools 9.0.0

	Usage: dotnet ef [options] [command]

	Options:
	  --version        Show version information
	  -h|--help        Show help information
	  -v|--verbose     Show verbose output.
	  --no-color       Don't colorize output.
	  --prefix-output  Prefix output with level.

	Commands:
	  database    Commands to manage the database.
	  dbcontext   Commands to manage DbContext types.
	  migrations  Commands to manage migrations.

	Use "dotnet ef [command] --help" for more information about a command.
```

# Project Structure

We will be using the monolithic structure.

## What is a Monolithic Structure?

Monolithic architecture is a software design methodology that combines all of an application's components into a single, inseparable unit. Under this architecture, the user interface, business logic, and data access layers are all created, put into use, and maintained as one, unified unit.

- A traditional approach in system design, which contains all application components into a single codebase.
- It was preferred for its simplicity and ease of initial setup.
- In contrast, alternative architectural approaches, like microservices, divide the application into smaller, separately deployable services.
- Because of its rigidity, it is difficult to scale and maintain, which makes it difficult to adjust to changing needs.

## Implementing Monolithic Structure

For a monolithic structure with Entity Framework, we will use:

1. **ProjectName** (Console Application) - Main entry point
2. **ProjectName.Domain** (Class Library) - Entity models
3. **ProjectName.Infrastructure** (Class Library) - DbContext and migrations

### Folder Structure

Your folder structure should look like this:

```
ProjectName/
├── ProjectName/                    (Console Application)
│   ├── Program.cs
│   ├── ProjectName.csproj
│   └── ...
├── ProjectName.Domain/             (Class Library)
│   ├── ProjectName.Domain.csproj
│   └── ...
├── ProjectName.Infrastructure/     (Class Library)
│   ├── Data/
│   │   ├── ApplicationDbContext.cs
│   │   └── Migrations/
│   ├── ProjectName.Infrastructure.csproj
│   └── ...
└── ProjectName.sln
```

---

# Creating Projects

## Create a Solution Folder

Open your terminal and navigate to where you want to create your project:

```bash
mkdir ProjectName
cd ProjectName
```

## Create the Solution

```bash
dotnet new sln -n ProjectName
```

## Create the Console Application

```bash
dotnet new console -n ProjectName
dotnet sln add ProjectName/ProjectName.csproj
```

## Create the Domain Class Library

```bash
dotnet new classlib -n ProjectName.Domain
dotnet sln add ProjectName.Domain/ProjectName.Domain.csproj
```

## Create the Infrastructure Class Library

```bash
dotnet new classlib -n ProjectName.Infrastructure
dotnet sln add ProjectName.Infrastructure/ProjectName.Infrastructure.csproj
```

## Add Project References

From the Console Application, reference the Domain and Infrastructure projects:

```bash
cd ProjectName
dotnet add reference ../ProjectName.Domain/ProjectName.Domain.csproj
dotnet add reference ../ProjectName.Infrastructure/ProjectName.Infrastructure.csproj
cd ..
```

From the Infrastructure project, reference the Domain project:

```bash
cd ProjectName.Infrastructure
dotnet add reference ../ProjectName.Domain/ProjectName.Domain.csproj
cd ..
```

---

# Adding Libraries to Projects

## What Libraries to Install

For the project we will be using SQL Server for the database, though you can use an in-memory database but that's not required.

### Core Library (Required for all projects):

- **Microsoft.EntityFrameworkCore** - The main Entity Framework Core package containing the ORM functionality

### For Infrastructure Project:

- **Microsoft.EntityFrameworkCore.SqlServer** - Database provider for Microsoft SQL Server
- **Microsoft.EntityFrameworkCore.Design** - Design-time tools and utilities for Entity Framework Core
- **Microsoft.EntityFrameworkCore.Tools** - NuGet CLI tools for Entity Framework

### Optional:

- **Microsoft.EntityFrameworkCore.InMemory** - This uses an in-memory database that is stored in the RAM (not required for SQL Server)

## Installing Libraries - Visual Studio Code

Use dotnet CLI commands to install packages:

```bash
dotnet add package Microsoft.EntityFrameworkCore
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

Or specify the version:

```bash
dotnet add package Microsoft.EntityFrameworkCore --version 9.0.0
dotnet add package Microsoft.EntityFrameworkCore.<Specific-ef-Tool-Name> --version 9.0.0
```

---

# Working with Migrations

## What are Migrations?

Migrations are a way to manage database schema changes over time. They track database structure modifications (creating tables, adding columns, etc.) and allow you to version your database alongside your code.

## Creating a Migration

After defining your entities in the DbContext, create a migration with:

```bash
dotnet ef migrations add <MigrationName>
```

For projects that use the split monolithic structure:

```bash
dotnet ef migrations add <MigrationName> --project ProjectName.Infrastructure --startup-project ProjectName
```

Example:
```bash
dotnet ef migrations add InitialCreate --project ProjectName.Infrastructure --startup-project ProjectName
```

This generates a migration file in the `Migrations` folder containing the `Up()` and `Down()` methods.

## Removing a Migration

In order to remove/delete migrations, the migration shouldn't have been applied to the database:

```bash
dotnet ef migrations remove --project ProjectName.Infrastructure --startup-project ProjectName
```

## Applying Migrations

To apply all or the latest migration to the database:

```bash
dotnet ef database update --project ProjectName.Infrastructure --startup-project ProjectName
```

## Reverting Migrations

To revert to a previous migration:

```bash
dotnet ef database update <MigrationName> --project ProjectName.Infrastructure --startup-project ProjectName
```