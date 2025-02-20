
# 📡 API REST en Laravel - Guía de Configuración

Este proyecto es una API REST en Laravel, creada como guía para entender el flujo de desarrollo de una API utilizando autenticación con tokens, migraciones, controladores y recursos en Laravel.




## 🛠 Tecnologías Utilizadas

- Laravel - Framework PHP
- Laravel Breeze - Sistema de autenticación
- Sanctum - Manejo de autenticación con API Tokens
- Eloquent ORM - Gestión de base de datos
- Laravel Resources - Transformación de datos




## 📥 Instalación

1️⃣ Requisitos Previos
- PHP (>= 8.0)
- Composer
- MySQL o XAMPP

2️⃣ Instalar Laravel y Breeze
```bash
composer require laravel/breeze --dev
php artisan breeze:install
```

3️⃣ Habilitar API Tokens en el Modelo User

En app/Models/User.php, añade:

```bash
use Laravel\Sanctum\HasApiTokens;

class User extends Authenticatable
{
    use HasApiTokens, HasFactory, Notifiable;
}
```

4️⃣ Instalar Sanctum y Configurar API

```bash
php artisan install:api
```

Luego, en routes/api.php, añade la ruta para la API de productos:

```bash
use App\Http\Controllers\Api\ProductController;
Route::apiResource('products', ProductController::class);
```

5️⃣ Crear la Migración para Productos
```bash
php artisan make:migration create_products_table
php artisan migrate
```

6️⃣ Crear el Modelo de Producto
```bash
php artisan make:model Product
```

En app/Models/Product.php, define los atributos:

```bash
namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;

class Product extends Model
{
    use HasFactory;

    protected $table = 'products';

    protected $fillable = ['name', 'description', 'price'];
}

```

7️⃣ Crear el Controlador de API

```bash
php artisan make:controller Api/ProductController --api
```
En app/Http/Controllers/Api/ProductController.php, añade los métodos:

```bash
    public function index()
    {
        $products = Product::all();
        if ($products->count() > 0) {
            return ProductResource::collection($products);
        } else {
            return response()->json(['message' => 'No products found'], 404);
        }
    }

    public function store(Request $request)
    {

        $validator = Validator::make($request->all(), [
            'name' => 'required',
            'description' => 'required',
            'price' => 'required',
        ]);

        if ($validator->fails()) {
            return response()->json([
                'message' => 'Validation failed',
                'error' => $validator->messages(),
            ], 422);
        }

        $pr = Product::create([
            'name' => $request->name,
            'description' => $request->description,
            'price' => $request->price,
        ]);

        return response()->json([
            'message' => 'Product created',
            'product' => new ProductResource($pr)
        ], 200);
    }

    public function show($id)
    {
        $product = Product::find($id);
        if ($product) {
            return new ProductResource($product);
        } else {
            return response()->json(['message' => 'Product not found'], 404);
        }
    }

    public function update(Product $product, Request $request)
    {
        $validator = Validator::make($request->all(), [
            'name' => 'required',
            'description' => 'required',
            'price' => 'required',
        ]);

        if ($validator->fails()) {
            return response()->json([
                'message' => 'Validation failed',
                'error' => $validator->messages(),
            ], 422);
        }

        $product->update([
            'name' => $request->name,
            'description' => $request->description,
            'price' => $request->price,
        ]);

        return response()->json([
            'message' => 'Product update successfully',
            'product' => new ProductResource($product)
        ], 200);
    }

    public function destroy($id)
    {
        $product = Product::find($id);
        if ($product) {
            $product->delete();
            return response()->json(['message' => 'Product deleted successfully'], 200);
        } else {
            return response()->json(['message' => 'Product not found'], 404);
        }
    }
```

8️⃣ Ver las Rutas de la API
```bash
php artisan route:list
```

Esto mostrará las rutas disponibles para interactuar con los productos.

9️⃣ Crear el Resource para Formatear Respuestas
```bash
php artisan make:resource ProductResource
```

En app/Http/Resources/ProductResource.php, define el formato de respuesta:
```bash
 public function toArray(Request $request): array
    {
        //return parent::toArray($request);
        return [
            'id' => $this->id,
            'name' => $this->name,
            'description' => $this->description,
            'price' => $this->price,
        ];
    }
```


## 📌 Endpoints de la API
🔹 Obtener todos los productos

```http
  GET /api/products
```

| Parameter | Type     | Description                |
| :-------- | :------- | :------------------------- |
| `api_key` | `string` | **Required**. Your API key |

#### Get un producto

```http
  GET /api/products/{id}
```

| Parameter | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `id`      | `int` | **Required**. ID del producto a obtener |


📌 Ejemplo de Respuesta:
```bash
{
    "data": {
        "id": 1,
        "name": "Producto 1",
        "description": "Descripción del producto 1",
        "price": 100,
        "created_at": "2024-02-12 10:00:00",
        "updated_at": "2024-02-12 10:00:00"
    }
}
```
🔹 Crear un nuevo producto
```bash
POST /api/products
```

| Parameter | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `name`      | `strig` | **Required**. nombre |
| `description`      | `string` | **Required**. description |
| `price`      | `float` | **Required**. price |

📌 Ejemplo body de la request
```bash
{
    "name": "Nuevo Producto",
    "description": "Descripción del nuevo producto",
    "price": 150
}
```

📌 Ejemplo de Respuesta:

```bash
{
    "data": {
        "id": 2,
        "name": "Nuevo Producto",
        "description": "Descripción del nuevo producto",
        "price": 150,
        "created_at": "2024-02-12 12:00:00",
        "updated_at": "2024-02-12 12:00:00"
    }
}
```

🔹 Actualizar un producto
```bash
PUT /api/products/{id}
```

| Parameter | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `id`      | `int` | **Required**. id |
| `name`      | `strig` | **Optional**. nombre |
| `description`      | `string` | **Optional**. description |
| `price`      | `float` | **Optional**. price |

📌 Ejemplo de Request Body:
```bash
{
    "name": "Producto Actualizado",
    "description": "Nueva descripción",
    "price": 200
}
```
📌 Ejemplo de Respuesta:
```bash
{
    "data": {
        "id": 2,
        "name": "Producto Actualizado",
        "description": "Nueva descripción",
        "price": 200,
        "created_at": "2024-02-12 12:00:00",
        "updated_at": "2024-02-12 12:30:00"
    }
}
```
🔹 Eliminar un producto
```bash
DELETE /api/products/{id}
```
| Parameter | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `id`      | `int` | **Required**. id del producto a aeliminar |

📌 Ejemplo de Respuesta:

```bash
{
    "message": "Producto eliminado"
}
```

## Authors

- [@alejandro14972](https://github.com/alejandro14972)


