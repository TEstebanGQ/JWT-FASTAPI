# Campus API segura

API de ejemplo creada con FastAPI. Permite registrar usuarios, iniciar sesión y consultar cursos y perfiles protegidos con tokens JWT.

## Requisitos

- Python instalado.
- PowerShell en Windows.

## Instalación

Abre una terminal en la carpeta del proyecto y ejecuta:

```powershell
py -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install -r requirements.txt
```

Si PowerShell bloquea la activación del entorno, permite scripts solo en la terminal actual y vuelve a activarlo:

```powershell
Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass
.\.venv\Scripts\Activate.ps1
```

## Configuración

Crea el archivo local `.env` a partir de la plantilla:

```powershell
Copy-Item .env.example .env
```

Genera una clave secreta:

```powershell
python -c "import secrets; print(secrets.token_urlsafe(32))"
```

Copia el valor generado en `.env`, reemplazando el texto de ejemplo:

```env
JWT_SECRET_KEY=pega_aqui_la_clave_generada
```

No compartas ni publiques `.env`. El archivo está excluido del control de versiones; usa `.env.example` como plantilla sin secretos.

## Ejecutar la API

Con el entorno virtual activado, desde la carpeta del proyecto:

```powershell
fastapi dev .\main.py
```

FastAPI muestra la dirección local al iniciar. La documentación interactiva está disponible en:

- Swagger UI: `http://127.0.0.1:8000/docs`
- ReDoc: `http://127.0.0.1:8000/redoc`

## Rutas disponibles

| Método | Ruta | Acceso | Descripción |
| --- | --- | --- | --- |
| `POST` | `/usuarios/registro` | Público | Registra un usuario. Recibe JSON con `username` (3–50 caracteres) y `password` (mínimo 8 caracteres). |
| `POST` | `/token` | Público | Verifica las credenciales y devuelve un token JWT. Recibe los campos de formulario `username` y `password`. |
| `GET` | `/publico` | Público | Devuelve un mensaje de bienvenida. |
| `GET` | `/perfil` | Requiere token | Devuelve el perfil del usuario autenticado. |
| `GET` | `/cursos` | Requiere token | Devuelve la lista de cursos para el usuario autenticado. |

### Probar el registro y el inicio de sesión

En `/docs`, abre `POST /usuarios/registro`, selecciona **Try it out** e introduce un cuerpo como:

```json
{
  "username": "ana",
  "password": "una-clave-de-ejemplo"
}
```

Después, abre `POST /token`, selecciona **Try it out** e introduce el mismo nombre y contraseña en los campos del formulario. La respuesta contiene `access_token` y `token_type` (`bearer`).

Para probar `/perfil` o `/cursos`, pulsa **Authorize** en Swagger UI e inicia sesión con esas credenciales. También puedes copiar el `access_token` de la respuesta de `/token` y usarlo como token Bearer.

## Detalles y limitaciones

- Las contraseñas se guardan como hashes, y no como texto plano.
- Los JWT se firman con HS256 y expiran después de 60 minutos.
- Los usuarios se guardan en memoria: se pierden al detener o reiniciar la aplicación. No hay una base de datos persistente.
- Este proyecto es una demostración; antes de usarlo en producción, añade almacenamiento persistente y configura HTTPS y la gestión de secretos adecuada.
