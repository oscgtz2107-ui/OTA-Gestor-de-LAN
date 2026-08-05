# OTA — Gestor de LAN

Repositorio **público** de compilación y distribución del producto
[Gestor de LAN](https://gestor-de-lan.com).

Aquí **no vive el producto**: solo se compila la app y se publican las versiones.

## Qué contiene

| Ruta | Para qué |
|---|---|
| `.github/workflows/build-app.yml` | Compila la app Android en depuración (minutos gratis por ser público) |
| `index.html` | Página de descarga de la app, servida en `gestor-de-lan.com` |
| `app/manifest.json` | Manifiesto de versión de la app (lo consulta la app para avisar de actualizaciones) |
| `firmware/manifest.json` | Manifiesto OTA que consultan las placas |
| `firmware/*.bin` | Binarios firmados del firmware |
| `CNAME` | Sirve `gestor-de-lan.com` por GitHub Pages |

## Distribución de la app

La app se descarga desde `gestor-de-lan.com` (la página lee `app/manifest.json`), y el APK se
publica como **asset de un GitHub Release** de este repo (URL estable
`releases/latest/download/gestor-de-lan.apk`), no se commitea al git.

Quien publica una versión es el **workflow de release del repositorio privado** de la app: allí
vive el keystore, compila el APK **firmado**, crea el Release aquí, actualiza `app/manifest.json`
y envía el aviso por FCM. Aquí no se firma nada ni se sube ningún APK a mano.

A diferencia del firmware, el manifiesto de la app **no lleva firma propia**: la autenticidad la
garantiza la **firma Android del APK** — el sistema rechaza instalar encima una app firmada con
otra clave, así que un manifiesto o una página comprometidos no pueden reemplazar la app
instalada. El `sha256` del manifiesto es integridad/informativo.

## Dónde está cada cosa

| Repositorio | Visibilidad | Contenido |
|---|---|---|
| `Gestor-de-LAN` | Privado | Firmware ESP32-S3 y documentación |
| `APP-Gestor-de-LAN` | Privado | Código fuente de la app Android |
| **`OTA-Gestor-de-LAN`** | **Público** | **Este repo: compilación y distribución** |

## Secretos configurados aquí

| Secreto | Alcance | Por qué es aceptable en un repo público |
|---|---|---|
| `APP_REPO_DEPLOY_KEY` | **Solo lectura**, un único repositorio | Si se filtrase, el alcance es el código de la app; ni el firmware ni el resto de repos quedan expuestos |
| `GOOGLE_SERVICES_JSON` | Configuración de Firebase | Viaja dentro del APK igualmente; lo que protege de verdad son las reglas de seguridad de la base |

## Lo que NUNCA debe estar aquí

- 🔴 **Keystore de firma de Android.** Quien lo tenga puede publicar actualizaciones que Android
  acepta como auténticas, y si se pierde no se puede volver a actualizar la app en Play Store.
  Por eso este repo compila **APK de depuración**; la firma de release se hace fuera.
- 🔴 **Clave privada de firma del firmware.** Vive solo en el repositorio privado. Las placas
  llevan embebida la clave **pública** y rechazan cualquier binario que no valide — sin eso,
  comprometer este repositorio permitiría instalar firmware arbitrario en todas las placas.
- 🔴 Tokens con acceso amplio a la cuenta.

## Reglas para los workflows de este repo

Al ser público, dos patrones bastan para filtrar secretos:

1. **No usar `pull_request_target`** con checkout del código del PR.
2. **No interpolar entradas de usuario** (`${{ github.event.* }}`) dentro de bloques `run:`
   — se inyecta shell. Pasarlas por variables de entorno.
