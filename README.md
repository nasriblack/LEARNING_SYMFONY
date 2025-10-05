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


- Assets and validation
    - Install the package
```
composer require symfony/validator doctrine/annotations

```
    - Import the right use
```
use Symfony\Component\Validator\Constraints as Assert;
```
    - In controller you need to 
        - import **ValidatorInterface $validator** => make it DI in the params of the method 
        - $errors = $validator->validate($book); => we gonna check if there is any error basic of the entity we updatet
 
        ```php        
        if ($errors->count() > 0) {
            return new JsonResponse($serializer->serialize($errors, 'json'), JsonResponse::HTTP_BAD_REQUEST, [], true);
        }
        ```


- Security and Authentification
1. You need to install the security package
```
composer require security
```
2. You need to create a user 
```
php bin\console make:user
```
3. We need to add this getUserName method in the user entity
```
public function getUsername(): string {
        return $this->getUserIdentifier();
    }
```
4. As we update the entity file we need to make this , This will make the migration without creating a migratio file ! it update the database
```
php bin/console doctrine:schema:update --force
```
5. In Fixture , You need to inject the  UserPasswordHasherInterface $userPasswordHasher !
    - After we set the email and role the password must be like this
    ```php
            $userAdmin->setPassword($this->userPasswordHasher->hashPassword($userAdmin, "password"));

    ```


- To Active the JWT you need to
1. install this package
```
composer require lexik/jwt-authentication-bundle
```

2. Generate private and public key
```
php bin/console lexik:jwt:generate-keypair
```
3. The JWT_PASSPHRASE you will find it in .env file ! => this one to make our key **sensitive value**
4. You need to make update in the **config\packages\security.yaml** file !!
5. Add this to the routes.yaml
```yaml
# config/routes.yaml
api_login_check:
    path: /api/login_check

```

- To work with **role based**
1. you need to add this above the method of the controller 
```
    #[IsGranted('ROLE_ADMIN', message: 'A custom msg in case of SIMPLE_ROLE')]

```
=> This will check the role of the user based on the token !! 


- Start the project with Symfony CLI
```
symfony server:start
```




## Notes

- Symfony is using the annotations a lots !
- **Fixture** => make a faker data
- EntityManagerInterface : is the service we use to talk to the database in Doctrine, it's responsible for the CRUD => we are using with the Dependecy injection
- ```return new JsonResponse($jsonBook, Response::HTTP_OK, [], true);``` => this represent a jsonResponse the empty array is for the header !

- ```$book = $serializer->deserialize($request->getContent(), Book::class, 'json');``` => to deserialize an object you need to get this from the serializer object , you just need to take the content from the request ( body ) , desiriaze it based on the class you need , with json.

- ```[AbstractNormalizer::OBJECT_TO_POPULATE => $currentBook]``` => this will tell Symfony you need to update not making a new object

- The listener are implemented when we want to make an event listeners for example to User entity ! We want for example when we change or affect User an listener should be trigger
    - first you should update the the **service.yml** that exist in config folder
    - add a listner in same ligne as App\ => you note this App\EntityListener for example
    - In **resource** you should give the path of where this listenere exist
    - In **tags** it's array
    - In the Entity you make this #[ORM\EntityListener[(' Path of the file you write the class of the listener which get called)]]


- For the JWT we gonna use this package **LexikJWT**

- The __invoke lets you call an object like a function!
  ```
  class HelloWorld {
    public function __invoke($name) {
        return "Hello, $name!";
    }
}

$greeter = new HelloWorld();
echo $greeter("Nasereddine"); 
// Output: Hello, Nasereddine!
    ```
=> here we don't passe the arg in costructor because there is no constructor ! there is invook function


- **PAGINATION**
    - First we gonna make the pagionation we need to update the repostory first and add this query 
    ```php
    public function findAllWithPagination($page, $limit)
        {
            $qb = $this->createQueryBuilder('b')
                ->setFirstResult(($page - 1) * $limit)
                ->setMaxResults($limit);
            return $qb->getQuery()->getResult();
        }

    ```
    - We call the method we created in findAllWithPagination in our controller in place of findAll
    - We retreive the value first of the page and limit to passe it in the repo

- **CACHE**
    - We gonna implmeent the cache inside getAllBooks and the theory is to make an id of each cache ! for example we gonna make an in for the request based on the page and limit like this 
    ```php
            $idCache = "getAllBooks-" . $page . "-" . $limit;
            $bookList = $cachePool->get($idCache, function (ItemInterface $item) use ($bookRepository, $page, $limit) {
            $item->tag("booksCache");
            return $bookRepository->findAllWithPagination($page, $limit);
        });
    ```
    - Here we are making a cache and after we getting the cache if it found of couse !
    - We will return the $bookList of course !
    - But here we have a **prb** , the result of our relation with authour is getting **null**
    => **SOLUTION** => we will cache the entier json response ! which have the value and the result of the beginning , so we need to update our BookController and our BookRepository
    ```php
    # In Repository ( this is other solution)
        $query->setFetchMode(Book::class, "author", \Doctrine\ORM\Mapping\ClassMetadata::FETCH_EAGER);
    ```

    ```php
    # In controller we replace this code
     $jsonBookList = $cache->get($idCache, function (ItemInterface $item) use ($bookRepository, $page, $limit, $serializer) {
            $item->tag("booksCache");
            $bookList = $bookRepository->findAllWithPagination($page, $limit);
            return $serializer->serialize($bookList, 'json', ['groups' => 'getBooks']);
        });
    ```

    **YOU NEED TO CLEAR THE CACHE**

    - What if we delete an item in book list ? what will happend =>  and this represent a prb for us because we save the cache and then we returning as it was ! now for delete for example we can use this
    ```php
    # Import this $cachePool with this TagAwareCacheInterface $cachePool as DI
     $cachePool->invalidateTags(["booksCache"]);
    ```
    - In this way we can set the invalide cache of booksCache


- SWAGGER => NelmioApiDocBandle
    - you need to install this first 
    ```
    composer require nelmio/api-doc-bundle
    composer require twig asset
    ```
    - Then you need to update the doc that exist in the config/route
    ```yaml
        # config\routes\nelmio_api_doc.yaml

    # Expose your documentation as JSON swagger compliant
    app.swagger:
        path: /api/doc.json
        methods: GET
        defaults: { _controller: nelmio_api_doc.controller.swagger }

    
    ## Requires the Asset component and the Twig bundle
    ## $ composer require twig asset
    app.swagger_ui:
        path: /api/doc
        methods: GET
        defaults: { _controller: nelmio_api_doc.controller.swagger_ui }
    ```

    - You need to put this to acces_control in security yaml because it must be public access
    ```yaml
        - { path: ^/api/doc, roles: PUBLIC_ACCESS }
    ```

    - Check in this project this file for the configuration you need to make the login and take the JWT
    ```
     config\packages\nelmio_api_doc.yaml
    ```

- ELIMINIATE SOME ATT THAT U DON'T LIKE !
    ```php
  $json_content = $serializer->serialize($products, "json", [
            ObjectNormalizer::IGNORED_ATTRIBUTES => ["id"]
        ]);
    ```
    - This code will ignore the att id from the json response 

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
    - [X] Entity
    - [X] Controller
    - [ ] Migration and Database
    - [ ] Repository and making custom request
    - [X] Exception
    - [X] Add conditions to the entity ( asserts )
    - [ ] Add listener
    - [ ] Understand the Firewall
    - [ ] Security
        - [X] Authentification
        - [X] Role based
