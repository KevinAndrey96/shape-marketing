# Shape Marketing: API de envío de SMS en Laravel 8

[![PHP](https://img.shields.io/badge/PHP-%5E7.3%20%7C%20%5E8.0-777BB4?logo=php&logoColor=white)](https://www.php.net/)
[![Laravel](https://img.shields.io/badge/Laravel-8-FF2D20?logo=laravel&logoColor=white)](https://laravel.com/)
[![Sanctum](https://img.shields.io/badge/Auth-Laravel%20Sanctum-FF2D20?logo=laravel&logoColor=white)](https://laravel.com/docs/8.x/sanctum)
[![Vue](https://img.shields.io/badge/Vue-2-4FC08D?logo=vuedotjs&logoColor=white)](https://v2.vuejs.org/)
[![Laravel Mix](https://img.shields.io/badge/Laravel%20Mix-5-F9645D)](https://laravel-mix.com/)
[![Estado](https://img.shields.io/badge/estado-archivo%20hist%C3%B3rico-lightgrey)](#estado-del-proyecto)

API REST para enviar mensajes de texto (SMS) de campañas de marketing a través de una pasarela externa. Autentica a los usuarios con tokens de Laravel Sanctum y solo permite enviar a los usuarios con estado `ACTIVE`.

## Qué problema resuelve

Expone un único punto de entrada autenticado para que otras aplicaciones envíen SMS a una lista de destinatarios, sin compartir las credenciales de la pasarela con cada cliente. Cada envío queda etiquetado con el usuario que lo solicitó.

## Endpoints

| Método | Ruta | Autenticación | Descripción |
| :--- | :--- | :--- | :--- |
| `POST` | `/api/auth/login` | No | Valida credenciales y devuelve un token de acceso |
| `POST` | `/api/v1/send-message` | Bearer (Sanctum) | Envía un SMS a una lista de destinatarios |
| `POST` | `/api/auth/logout` | Bearer (Sanctum) | Revoca el token actual |

Cuerpo de `send-message`:

```json
{
  "textMessage": "Texto del mensaje",
  "isTest": true,
  "recipients": [{ "phone": "573001112233" }]
}
```

Si el usuario autenticado no tiene estado `ACTIVE`, la API responde `"NOVALIDO"` y no envía nada.

## Estado del proyecto

Es la base de una API de 2021: el flujo de autenticación y el envío de SMS funcionan como se describe arriba. Las tablas `text_messages`, `customers`, `urls`, `status_codes`, `balances` y `packages` están creadas como andamios (solo `id` y marcas de tiempo) y la interfaz Vue es un componente de ejemplo. No hay un panel web terminado.

## Estructura

```
.
├── app/
│   ├── Http/Controllers/
│   │   ├── AuthController.php         # Registro, login y logout con Sanctum
│   │   └── api/SMSController.php      # Envío de SMS a la pasarela externa
│   ├── Models/User.php                # Usuario con username y status (INACTIVE por defecto)
│   └── Traits/ApiResponser.php        # Respuestas JSON uniformes
├── config/external.php                # URL y credenciales de la pasarela (desde el entorno)
├── database/migrations/               # Usuarios, tokens y tablas base del dominio
├── resources/js/                      # Componente Vue de ejemplo (Laravel Mix)
└── routes/api.php                     # Rutas de la API
```

## Requisitos

- PHP 7.3 u 8.0 o superior y Composer 2.
- MySQL (u otra base de datos compatible con Laravel).
- Node.js y npm solo si vas a compilar los recursos de Vue.
- Una cuenta en la pasarela de SMS y sus credenciales.

## Ejecución local

```bash
git clone git@github.com:KevinAndrey96/shape-marketing.git
cd shape-marketing
composer install
cp .env.example .env
php artisan key:generate
```

Completa en `.env` los datos de la base de datos y de la pasarela:

```
DB_DATABASE=***REMOVED***
DB_USERNAME=<usuario>
DB_PASSWORD=<clave>
URL_SMS_EXTERNAL_API=<url de la pasarela>
SMS_EXTERNAL_API_USER=<usuario de la pasarela>
SMS_EXTERNAL_API_PASSWORD=<clave de la pasarela>
```

Luego ejecuta las migraciones y levanta el servidor:

```bash
php artisan migrate
php artisan serve
```

## Seguridad

Las credenciales de la base de datos, la clave de la aplicación y las de la pasarela de SMS se leen del entorno y ya no están en el código. El historial de Git de versiones anteriores puede contener valores antiguos, por lo que deben considerarse comprometidos y rotarse antes de reutilizar el proyecto.

## Licencia

Sin licencia de código abierto. Todos los derechos reservados.

El código no se distribuye bajo ninguna licencia abierta; su uso o redistribución requiere autorización expresa del titular.
