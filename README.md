instalacion:
composer requiere laravel/breeze --dev
php artisan breeze:install -----> en el modelo user añadir: HasApiTokens
php artisan install:api  -----> añadir en routes/api: Route::apiResource('products', ProductController::class);
php artisan make:migration create_products_table
php artisan migrate
php artisan make:model Product ----> añadir los fillable y table
php artisan make:controller Api/ProductController --->añadir los métodos
php artisan route list  ---->comando que muestra los request de productos
php artisan make:resource ProductResource
