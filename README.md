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
php bin/console make:migration
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

- Make Controller
```
# This command will genere a controller , give the name of controller after in terminal
php bin/console make:controller

```

- To retrieve data from the DB you need to install first the sertializer package
```
composer require symfony/serializer-pack

```

- To make the relation between 2 table , 
1. Create an entity 
1. add the type of the field as **relation** 
1. Select the type of the relation  

- To make an **ExceptionSubscriber** you need to run this command , subscriber ( from the tasks he do he will subscribe to every exception we have!)
```
php bin/console make:subscriber

```
1. We need ExceptionSubscriber as an event
1. We need to set the kernel.exception

## Notes

- Symfony is using the annotations a lots !
- **Fixture** => make a faker data
- EntityManagerInterface : is the service we use to talk to the database in Doctrine, it's responsible for the CRUD => we are using with the Dependecy injection
- ```return new JsonResponse($jsonBook, Response::HTTP_OK, [], true);``` => this represent a jsonResponse the empty array is for the header !

- ```$book = $serializer->deserialize($request->getContent(), Book::class, 'json');``` => to deserialize an object you need to get this from the serializer object , you just need to take the content from the request ( body ) , desiriaze it based on the class you need , with json.

- ```[AbstractNormalizer::OBJECT_TO_POPULATE => $currentBook]``` => this will tell Symfony you need to update not making a new object 



## References & Learning Resources

- https://openclassrooms.com/fr/courses/7709361-construisez-une-api-rest-avec-symfony-1/7795134-gerez-les-erreurs-et-ajoutez-la-validation => Tutorial iam watching

- https://www.youtube.com/watch?v=3K6oBiQK8aA => Symfony in 12h

- https://www.youtube.com/watch?v=TOa7JGbRwvk&list=PLQH1-k79HB3-xsVANTUV3yH6g9ubo_OxJ => TDD symfony

- https://www.youtube.com/watch?v=t81jwwMCwRU&list=PLQH1-k79HB38sxQrZ7OWalANJchHrP3AQ => Doctrine Relationships

- https://www.youtube.com/watch?v=pZv93AEJhS8&list=PLQH1-k79HB3_lsClhpW1svbukj6zgcupR => Microservice symfony 

## Errors & Solutions

- If you get a empty object in the response => install the serialzer package or import it 
- when getting this error 
A circular reference has been detected when serializing the object of class "App\Entity\Book" (configured limit: 1). => you will give a groups name ! for the att you want to receive!
=> the prb is the like an infinity loop of call ! 
**#[Groups(["getBooks"])]** => you will call this by adding this in serializer arg **['groups' => 'getBooks']**


## Tips & Tricks

- To debug the router and see all router
```
php bin/console debug:router
```
- To clear the cache 
```
php bin/console cache:clear
```


**DEBUG TOOLS**
- dd() => stop execution
- dump() => not stoping from the execution

## Prgoress & Task

- [ ] Understand the basics of symfony
    - [ ] Entity
    - [ ] Controller
    - [ ] Migration and Database
    - [ ] Repository and making custom request
    - [ ] Exception