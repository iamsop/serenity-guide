# Allowing Multiple Genre Selection

It happens. Requirements change. Now we want to allow selecting multiple genres for a movie.

For this, we need a M-N mapping table that will let us link any movie to multiple genres.


### Creating MovieGenres Table

As usual, we start with a migration:

**Modules/Common/Migrations/DefaultDB/ DefaultDB_20160528_115400_MovieGenres.cs:**

```
using FluentMigrator;

namespace MovieTutorial.Migrations.DefaultDB
{
    [Migration(20160528115400)]
    public class DefaultDB_20160528_115400_MovieGenres : Migration
    {
        public override void Up()
        {
            Create.Table("MovieGenres").InSchema("mov")
                .WithColumn("MovieGenreId").AsInt32()
                    .Identity().PrimaryKey().NotNullable()
                .WithColumn("MovieId").AsInt32().NotNullable()
                    .ForeignKey("FK_MovieGenres_MovieId", 
                        "mov", "Movie", "MovieId")
                .WithColumn("GenreId").AsInt32().NotNullable()
                    .ForeignKey("FK_MovieGenres_GenreId", 
                        "mov", "Genre", "GenreId");

            Execute.Sql(
              @"INSERT INTO mov.MovieGenres (MovieId, GenreId) 
                    SELECT m.MovieId, m.GenreId 
                    FROM mov.Movie m 
                    WHERE m.GenreId IS NOT NULL");

            Delete.ForeignKey("FK_Movie_GenreId")
                .OnTable("Movie").InSchema("mov");
            Delete.Column("GenreId")
                .FromTable("Movie").InSchema("mov");
        }

        public override void Down()
        {
        }
    }
}
```

I tried to save existing Genre declarations on Movie table, by copying them to our new MovieGenres table. The line above with *Execute.Sql* does this.

Then we should remove GenreId column, by first deleting the foreign key declaration *FK_Movie_GenreId* that we defined on it previously.


### Deleting Mapping for GenreId Column

As soon as you build and open the Movies page, you'll get this error:





