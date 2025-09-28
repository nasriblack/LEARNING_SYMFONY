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
