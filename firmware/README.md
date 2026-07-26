# Distribución OTA del firmware

Las placas consultan `manifest.json` para saber si hay una versión nueva, descargan el `.bin`
y **verifican su firma antes de instalarlo**.

## Formato del manifiesto

| Campo | Qué es |
|---|---|
| `version` | Versión semántica del firmware (`1.2.0`) |
| `build` | Fecha de compilación |
| `url` | URL absoluta del binario |
| `sha256` | Hash del binario — detecta descargas corruptas |
| `signature` | **Firma del binario** (base64) — detecta binarios maliciosos |
| `min_upgradable_from` | Versión mínima desde la que se puede saltar a esta |

## ⚠️ Por qué la firma no es opcional

Este repositorio es **público**. El `sha256` por sí solo **no protege**: quien pudiera publicar
un binario malicioso publicaría también su hash correspondiente.

Lo que protege es la **firma**: cada placa lleva **embebida la clave pública** y rechaza
cualquier binario cuya firma no valide. **La clave privada nunca sale del repositorio privado**
y no debe existir ninguna copia en este repo ni en sus secretos.

Sin esa verificación, comprometer este repositorio equivaldría a instalar firmware arbitrario en
**todas las placas desplegadas**.

## Publicación

No se sube nada a mano aquí. El workflow del repositorio privado compila, firma y publica el
binario junto con el manifiesto actualizado.
