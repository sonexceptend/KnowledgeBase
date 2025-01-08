# FluentMigrator

(https://fluentmigrator.github.io/articles/fluent-interface.html)[https://fluentmigrator.github.io/articles/fluent-interface.html]

## Quickstart

Для создания миграции нужно добавить в проект следующие пакеты:

> dotnet add package FluentMigrator // библиотека классов

> dotnet add package FluentMigrator.Runner // для выполнения миграции

Далее зависит от базы назначения. Для мускула FluentMigrator.Runner.MySql, дял SQLite FluentMigrator.Runner.SQLite и т.д.

Создаем фай  20180430_AddLogTable.cs следующего содержания:

```c#
using FluentMigrator;

namespace test
{
    [Migration(20180430121800)]
    public class AddLogTable : Migration
    {
        public override void Up()
        {
            Create.Table("Log")
                .WithColumn("Id").AsInt64().PrimaryKey().Identity()
                .WithColumn("Text").AsString();
        }

        public override void Down()
        {
            Delete.Table("Log");
        }
    }
}
```

где:
- [Migration(20180430121800)] - номер миграции. Определяет порядок ее выолнения. Применяется шаблон yyyyMMddhhmmss
- public class AddLogTable : Migration - класс наследуется от Migration. Имя класс используется в качестве описания данных в миграции
- public override void Up() - метод вызывается для применения миграции
- public override void Down() - метод вызывается для отката миграции

Выполнение миграции:

```c#
using System;
using System.Linq;

using FluentMigrator.Runner;
using FluentMigrator.Runner.Initialization;

using Microsoft.Extensions.DependencyInjection;

namespace test
{
    class Program
    {
        static void Main(string[] args)
        {
            using (var serviceProvider = CreateServices())
            using (var scope = serviceProvider.CreateScope())
            {
                // Put the database update into a scope to ensure
                // that all resources will be disposed.
                UpdateDatabase(scope.ServiceProvider);
            }
         }

        /// <summary>
        /// Configure the dependency injection services
        /// </summary>
        private static ServiceProvider CreateServices()
        {
            return new ServiceCollection()
                // Add common FluentMigrator services
                .AddFluentMigratorCore()
                .ConfigureRunner(rb => rb
                    // Add SQLite support to FluentMigrator
                    .AddSQLite()
                    // Set the connection string
                    .WithGlobalConnectionString("Data Source=test.db")
                    // Define the assembly containing the migrations
                    .ScanIn(typeof(AddLogTable).Assembly).For.Migrations())
                // Enable logging to console in the FluentMigrator way
                .AddLogging(lb => lb.AddFluentMigratorConsole())
                // Build the service provider
                .BuildServiceProvider(false);
        }

        /// <summary>
        /// Update the database
        /// </summary>
        private static void UpdateDatabase(IServiceProvider serviceProvider)
        {
            // Instantiate the runner
            var runner = serviceProvider.GetRequiredService<IMigrationRunner>();

            // Execute the migrations
            runner.MigrateUp();
        }
    }
}
```

## Interface

Create.Table("Users") // создать таблицу
    .WithIdColumn()   // создать столбец id int с автоикрементом и первичным ключом. 
	// аналогично такому .WithColumn("Id").AsInt32().NotNullable().PrimaryKey().Identity()
    .WithColumn("Name").AsString(100).NotNullable()	// столбец Name строка длиной 100 символов, NOT NULL
	.WithDefaultValue("Bob") // значение по умолчанию
	;
	
Delete.Table("Users"); // удалить таблицу

Delete.Column("AllowSubscription").Column("SubscriptionDate").FromTable("Users"); // удалить столбцы AllowSubscription и SubscriptionDate в таблице Users

Alter.Table("Bar").AddColumn("SomeDate").AsDateTime().Nullable(); // добавить столбец SomeDate в таблицу Bar

Alter.Table("Bar").AlterColumn("SomeDate").AsDateTime().NotNullable(); // сменить тип столбца SomeDate на Not Null

Alter.Column("SomeDate").OnTable("Bar").AsDateTime().NotNullable();

Выполнить скрипт

```
Execute.Script("myscript.sql");
Execute.EmbeddedScript("UpdateLegacySP.sql");
Execute.Sql("DELETE TABLE Users");
```

Rename.Table("Users").To("UsersNew"); // переименовать таблицу

Rename.Column("LastName").OnTable("Users").To("Surname"); // переименовать столбец

Изменение данных

Insert.IntoTable("Users").Row(new { FirstName = "John", LastName = "Smith" }); // вставка

Delete.FromTable("Users").AllRows(); // delete all rows

Delete.FromTable("Users").Row(new { FirstName = "John" }); // delete all rows with FirstName==John

Delete.FromTable("Users").IsNull("Username"); //Delete all rows where Username is null

Update.Table("Users").Set(new { Name = "John" }).Where(new { Name = "Johnanna" }); // изменение данных

Проверка. Если в таблице Users нет столбца FirstName, то добавить его

```
if (!Schema.Table("Users").Column("FirstName").Exists())
{
    this.Create.Column("FirstName").OnTable("Users").AsAnsiString(128).Nullable();
}
```

Create.UniqueConstraint("users_unique").OnTable("users").Columns(new string[] { "guid", "town" }); // добавить уникальное ограничение

Create.ForeignKey("assets_clients_FK").FromTable("assets").ForeignColumn("clientId").ToTable("clients").PrimaryColumn("id"); // добавить внешний ключ

## Проблемы

В мускуле не создается тип Time:

.WithColumn("minHarvestPeriod").AsTime().NotNullable() // создаст тип DateTime