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

## Notes

- Symfony is using the annotations a lots !

## References & Learning Resources

## Errors & Solutions

## Tips & Tricks

## Prgoress & Task