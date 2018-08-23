# Using Migrations
Referência: https://docs.microsoft.com/pt-br/ef/core/managing-schemas/migrations/

# PowerShell
Add-Migration InitialCreate

# Console:
dotnet ef migrations add InitialCreate



# PowerShell
Update-Database

# Console
dotnet ef database update



# PowerShell
Add-Migration AddProductReviews

# Console
dotnet ef migrations add AddProductReviews



# PowerShell
Update-Database

# Console
dotnet ef database update



# PowerShell
Remove-Migration

# Console
dotnet ef migrations remove



# PowerShell
Update-Database LastGoodMigration

# Console
dotnet ef database update LastGoodMigration



# PowerShell
Script-Migration

# Console
dotnet ef migrations script



# Como executar em runtime:
myDbContext.Database.Migrate();
