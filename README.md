# Prueba Técnica: Integración de Pagos con Khipu usando Postman y Python

Este repositorio documenta el desarrollo completo de una integración de pagos utilizando la API de Khipu. El proceso fue realizado como parte de una prueba técnica, donde se cumplieron los requisitos de generar un cobro de prueba, visualizarlo en la plataforma de Khipu, y verificar el flujo de pago simulado.

## Tecnologías utilizadas

* **Postman**: Para realizar y firmar solicitudes HTTP.
* **Python**: Para generar firmas HMAC SHA256 y comprender la lógica de integración.
* **GitHub**: Para documentar el proceso y subir evidencia.

---

## Paso a Paso de la Implementación

### 1. Registro y configuración en Khipu

* Se creó una cuenta en [https://khipu.com](https://khipu.com).
* Se accedió al panel de cobrador en modo desarrollador.
* Se obtuvo el **Receiver ID** y **Llave Secreta** desde la sección "Opciones de la cuenta".

### 2. Generación del cobro

* Se utilizó Postman para realizar una solicitud `POST` a `https://payment-api.khipu.com/v3/payments`.
* El cuerpo (`Body`) de la solicitud incluyó:

```json
{
  "subject": "Cobro desde Postman v3.0",
  "amount": 5000,
  "currency": "CLP",
  "transaction_id": "orden-roman-prueba-002",
  "return_url": "https://misitio.cl/retorno",
  "cancel_url": "https://misitio.cl/cancelado",
  "payer_email": "cliente@ejemplo.com"
}
```

### 3. Generación de la firma HMAC SHA256

Se creó el archivo `Generar_Firma_HMAC_SHA256.py` con el siguiente código:

```python
import hmac
import hashlib
import json

# Tu llave secreta aquí
secret = 'a288b445b536406379f72800b42f8887061b4109'

# Tu JSON exacto (sin saltos de línea ni espacios innecesarios)
body = {
    "subject": "Cobro desde Postman v3.0",
    "amount": 5000,
    "currency": "CLP",
    "transaction_id": "orden-roman-prueba-002",
    "return_url": "https://misitio.cl/retorno",
    "cancel_url": "https://misitio.cl/cancelado",
    "payer_email": "cliente@ejemplo.com"
}

# Serializar el body (asegúrate que se vea igual al enviado en Postman)
serialized_body = json.dumps(body, separators=(',', ':'))

# Crear firma HMAC SHA256
signature = hmac.new(
    key=bytes(secret, 'utf-8'),
    msg=bytes(serialized_body, 'utf-8'),
    digestmod=hashlib.sha256
).hexdigest()

print("x-signature:", signature)
```

**Explicación**: Este script genera la firma HMAC SHA256 que debe incluirse como valor del header `x-signature` al realizar la solicitud `POST` a la API de Khipu. Se asegura que el cuerpo (`body`) sea idéntico al enviado, sin espacios ni saltos de línea que puedan alterar la firma.

### 4. Visualización del cobro generado

Al enviar la solicitud, se recibió una respuesta `200 OK` con las URLs de pago. Se accedió a los siguientes enlaces:

* **app\_url**: URL del cobro generado
* **simplified\_transfer\_url**: para pago con transferencia
* **transfer\_url**: para interfaz bancaria simplificada

### 5. Confirmación en Khipu

* Se ingresó a la plataforma Khipu como cobrador.
* El cobro apareció listado como activo.
* Al hacer clic en el botón de pago y completar la acción en el entorno de prueba, el estado del cobro cambió a **completado** (color verde).

---

## Estructura del Repositorio

* `Generar_Firma_HMAC_SHA256.py`: Script para generar firmas.
* `Crear_Cobro.py`: (opcional, no fue necesario para la prueba, sólo de referencia).
* `khipu_prueba_tecnica.postman_collection.json`: Exportación de la colección usada en Postman.
* Carpeta de evidencias: capturas de pantalla del proceso (registro, cobro, headers, etc).

---

## Observaciones Finales

* Se trabajó en modo desarrollador, por lo tanto, los pagos fueron simulados.

Este repositorio cumple con todos los requisitos planteados en la prueba técnica de integración con la API de Khipu.

---

Realizado por: **Román Briceño**
