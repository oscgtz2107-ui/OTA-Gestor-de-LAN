# Distribución OTA del firmware

> ⚠️ **El manifiesto ya no vive aquí.** Está en [`../fw/manifest.json`](../fw/manifest.json).
>
> Este directorio está **excluido del hosting** en `firebase.json`, así que nada de lo
> que se ponga aquí llega a servirse. El manifiesto estuvo aquí y por eso las placas
> recibían un 404 al buscar su actualización.

Las placas piden `https://gestor-de-lan.web.app/fw/manifest.json`, que es lo que lleva
compilado el firmware (`FIRMWARE_MANIFEST_URL`). Descargan el `.bin` y **verifican su
firma antes de instalarlo**.

## Formato

**Plano**, tal y como lo lee `fw_updater.cpp`. Nada de anidarlo bajo `latest`.

| Campo | Qué es |
|---|---|
| `version` | Versión semántica (`2.4.1`) |
| `versionCode` | Entero monótono; la placa rechaza bajar de versión |
| `url` | URL absoluta del binario |
| `sha256` | Hash del `.bin`, en hex |
| `signature` | Firma ECDSA P-256 del manifiesto, **en hex** (no base64) |

## Quién lo escribe

`04_Herramientas/scripts/publicar_firmware_ota.py`, en el repositorio privado, que es
donde vive la clave de firma. No se edita a mano.

Cada versión se publica junto al **kit de reenlazado** que exige la LGPL; ver
[/licencias/](https://gestor-de-lan.web.app/licencias/).
