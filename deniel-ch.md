## ==Folder structure and principal explanation==

- This is the folder structure we want to achieve for our project. The file names are just examples:
```
YouTube
|- Program.cs


YouTube.Core
|- 📁 Service
|- 📁 Contracts


YouTube.Infrastructure
|- 📁 Data
    |- 📁 Constants
	    |- DbConstants.cs
	|- 📁 Models // here we hold all table models (video.cs is an example)
		|- Video.cs (namespace YouTube.Infrastructure.Data.Models)
	|- DbContext.cs
|- 📁 Common
	|- IRepository.cs
	|- Repository.cs
|- 📁 Migrations (automatically generated when you make a migration)
```

- We use monolith structure to divide the project into 3 layers where each layer has its own job but they are all connected between each other. The whole project runs as one. We connect them through dependencies. The connection is as follows (Console App -> Core -> Infrastructure) 
	- Console App - this is where the app starts and connects with all the other projects
	- Core - this is where we keep the business logic. Hold the service layer(this defines how the app should behave) and contracts layer(This has interfaces for services)
	- Infrastructure - this is where everything connected to the Db is. We talk to the database through it. It has the DB context, the repository pattern, Models and constants and migrations
```ad-important
The console app is an executable project.
Core and Infrastructure on the other hand are classLib
classLib - it has code which the whole project can use but is not buildable
```
- We use an ORM, which in this case means to use EF Core(Entity framework Core). It translates your C# classes into database tables and handles the SQL queries for you. It Creates the database, writes queries, keeps track of changes and makes migrations and saves the data
- We use Repository Pattern which is a way to keep all database code in one place instead of spreading it around the whole project. The IRepository interface holds all the operations your app needs for the database. The Repository.cs file implements the IRepository interface. It uses EF Core and DbContext to talk to the database. This is where we contain the logic for CRUD operations

## ==Visual Studio Code==

```ad-attention
You **need** .net SDK. If you dont have it run this in your bash console:
winget install Microsoft.DotNet.SDK.8
```

### 1.Creating folder structure and projects
**To create the folder structure run these commands in your bash console. The words marked with [] is where you have to name files however you like. The given names are just examples for a youTube project**
1. Create a root folder for your solution:
	`mkdir [YouTubeProject]
	`cd [YouTubeProject]`
2. Create a solution file
	`dotnet new sln -n [projectName]`
3. Create the projects
	`dotnet new console -n [YouTube]`
	`dotnet new classlib -n [YouTube].Core`
	`dotnet new classlib -n [YouTube].Infrastructure`
4. Add projects to the solution
	`dotnet sln add YouTube/YouTube.csproj
	`dotnet sln add YouTube.Core/YouTube.Core.csproj`
	`dotnet sln add YouTube.Infrastructure/YouTube.Infrastructure.csproj`
5. Add project references
	`cd [YouTube].Core`
	`dotnet add reference`
	`../YouTube.Infrastructure/YouTube.Infrastructure.csproj`
	`cd ..`

	`cd YouTube`
	`dotnet add reference ../YouTube.Core/YouTube.Core.csproj`
	`cd ..`
6. Create the folders inside the projects
	`cd [YouTube].Core`
	`mkdir Service`
	`mkdir Contracts`
	`cd ..`

	`cd [YouTube].Infrastructure`
	`mkdir Data`
	`mkdir Data/Constants`
	`mkdir Data/Models`
	`mkdir Common`
	`cd ..`
7. Create the files inside
	`cd YouTube.Infrastructure/Data/Constants`
	`type nul > DbConstants.cs`
	`cd ../Models`
	`type nul > Video.cs`
	`cd ../../..`
	`type nul > DbContext.cs`
	`cd Common`
	`type nul > IRepository.cs`
	`type nul > Repository.cs`
	`cd ../../..`
	`type nul > YouTube/Program.cs`
**With the commands in point 7 we create empty files. You should write the code in them**
```ad-note

**Command models**
`dotnet new <PROJECT_TYPE> -n? <PROJECT-NAME>` -> creates a new project
`dotnet run` -> runs the projects
`dotnet add reference <PATH_TO_PROJECT_2>` -> links the current project to `<PROJECT_2>`

```

### 2.Install the needed extensions and libraries

1. Install extensions
	1. C# - Open VS Code → Extensions tab → Search `C#` → Click Install
	2. NuGet - Extensions tab → Search `NuGet Package Manager` → Click Install
2. Install NuGet libraries
	`cd YouTube.Infrastructure`
	`dotnet add package Microsoft.EntityFrameworkCore`
	`dotnet add package Microsoft.EntityFrameworkCore.Tools`
	`dotnet add package Microsoft.EntityFrameworkCore.Design`
	`dotnet add package Microsoft.EntityFrameworkCore.SqlServer`

	`cd ../YouTube.Core`
	`dotnet add package Microsoft.EntityFrameworkCore`

	`cd ../YouTube`
	`dotnet add package Microsoft.EntityFrameworkCore`

### 3.Add migrations

```ad-important
You need to have code in your DbContext.cs file before you do the next steps.
Migrations reqire the connection string to exist.
Connection string model:
"Server=(address), (port); Database=(DbName); User Id=(userId); Password=(password); TrustServerCertificate = (true/false); Encrypt = (true/false)"
```

1. If you don't have dotnet-ef installed then install it:
	`dotnet tool install --global dotnet-ef`
2. Open a terminal in the infrastructure project:
	`cd YouTube.Infrastructure`
3. Add migration:
	`dotnet ef migrations add InitialCreate`
	
4. Update the database:
	`dotnet ef database update`
```ad-info
**Command models**
- `dotnet ef migrations add <MIGRATION_NAME>` -> generates a migration
- `dotnet ef migrations remove` - removes the last migration
- `dotnet ef database update` - updates the database with the latest migrations

```

## ==Visual Studio 2022==
### 1.Create Folder Structure
1. Create a solution
	1. Open Visual Studio → File → New → Project
	2.  Search for **Blank Solution** → Click Next
	3. Name it (e.g., `YouTubeProject`) → Choose location → Click Create
2. Add the projects
	1. Right-click on the solution → Add → New Project
	2. Add a **Console App (.NET 8)** → Name: `YouTube` → Click Create
	3. Add a **Class Library (.NET 8)** → Name: `YouTube.Core` → Click Create
	4. Add another **Class Library (.NET 8)** → Name: `YouTube.Infrastructure` → Click Create
3. Set dependencies
	1. Right-click `YouTube` (Console App) → Add → Project Reference → Check `YouTube.Core` → OK
	2. Right-click `YouTube.Core` → Add → Project Reference → Check `YouTube.Infrastructure` → OK
4. Create the folders inside
	1. Right-click on `YouTube.Core` → Add → New Folder → Name: `Service`
	2. Add another folder → Name: `Contracts`
	3. Right-click on `YouTube.Infrastructure` → Add → New Folder → Name: `Data`
	4. Inside `Data` → Add → New Folder → `Constants`
	5. Inside `Data` → Add → New Folder → `Models`
	6. Right-click on `YouTube.Infrastructure` → Add → New Folder → Name: `Common`
5. Create Files
	1. Right-click the folder → Add → Class → Name it appropriately:
		1. `DbConstants.cs` in `Constants`
		2. `Video.cs` in `Models`
		3. `DbContext.cs` in Data`
		4. `IRepository.cs` and `Repository.cs` in `Common`
		5. `Program.cs` already exists in Console App
### 2. Install the needed libraries
1. Click on `tools` -> NuGet Package Manager -> Manage NuGet packages for solution
2. Search for the libraries:
	1. `Microsoft.Entity.FrameworkCore`
	2. `Microsoft.EntityFrameworkCore.Design`
	3. `Microsoft.EntityFrameworkCore.SqlServer`
	4. `Microsoft.EntityFrameworkCore.Tools`
3. Select the projects that need the libraries
	1. `Microsoft.Entity.FrameworkCore` -> all
	2. `Microsoft.EntityFrameworkCore.Design` -> infrastructure
	3. `Microsoft.EntityFrameworkCore.SqlServer` -> infrastructure
	4. `Microsoft.EntityFrameworkCore.Tools` -> infrastructure
4. Select the version of the library which is compatible with the .Net version of your projects
5. Click Install
### 3. Add Migrations
1. Open **Package Manager Console** (Tools → NuGet Package Manager → Package Manager Console)
2. Make sure the **Default Project** in the package manager console is set to `YouTube.Infrastructure`
3. Add Migration command:
	`Add-Migration InitialCreate`
4. Update Database:
	`Update-Database`

```ad-info
**Migration command models**
Add-Migration [migrationName] - adds a new migration
Remove-Migration - removes the last migration
Update-Database - updates the database wiht the lates or all migrations
Update-Database [projectName of previous migration] - reverts to an older migration
```

```ad-note
**You don't make the folder "migrations". It is automatically generated when you add your first migration**
```

Creating ... roject.md
Външни
Показва се „Creating a new project.md“.