# Learnign Symfony
- First i am gonna install **Laragon** to give the server locally ro run php on my machine

## Commands

- I just Installed symfony by this command 
```
# This is fully usefull when you want create microservice or api
composer create-project symfony/skeleton:"7.3.x" Books

cd Books

# This when you want to use twig and web app
composer require webapp

# This command will tell symfony to create some elements ( controller , entity ... )
composer require symfony/maker-bundle --dev

# Install the ORM Doctrine inside our project
composer require orm
```

- Make Entity
```
# Enter the name of the entity and continue the option ( the same command to update a model or entity )
php bin/console make:entity
```

- There is 2 folder are created inside the src folder 
    - Entity => Which represent our model ( we can show the attributs)
    - Repository => Which represent basicly the connection between the entity and the DB

- Make the migration Then migrate the Database
```
# Make the migration ( create the sql code )
php bin/console make:migratio
# Migrate the schema into the DB
php bin/console doctrine:migrations:migrate
```

- Fixture => to make a fake data for testing
```
composer require orm-fixtures --dev
```
In Load function we need to set a code that make the fake data

```php
for ($i = 0; $i < 20; $i++) {
            $livre = new Book;
            $livre->setTitle('Livre ' . $i);
            $livre->setCoverText('Quatrième de couverture numéro : ' . $i);
            $manager->persist($livre);
        }
```
```
# This will trigger the function load 
php bin\console doctrine:fixture:load
```

## Notes

- Symfony is using the annotations a lots !
- **Fixture** => make a faker data

## References & Learning Resources

## Errors & Solutions

## Tips & Tricks

## Prgoress & Task