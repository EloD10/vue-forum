# vue-forum

My training forum with Laravel 5.6 VueJS 2.5.13.


It's still in alpha stage, there is nothing very interesting. :punch:

## Setup

```
git clone 
cd laravelforum
```

Setup your connection database in `.env`.
And don't forget to generate a new encryption key with this command: php artisan key:generate Copy and paste this new key to APP_KEY= in line 3 to the env. file.

Follow these commands :

```
php artisan migrate
php artisan db:seed
```

You can launch the server with `php artisan serve`. You can see existing routes in the routes folder, try for example to go on the `/forum` route. 

Now, you need to run the frontend : 
```
npm run watch # or yarn watch
```

Enjoy!  :sunglasses: