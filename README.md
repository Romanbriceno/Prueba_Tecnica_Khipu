# Prueba Técnica: Integración de Pagos con Khipu usando Postman y Python

Este repositorio documenta el desarrollo completo de una integración de pagos utilizando la API de Khipu. El proceso fue realizado como parte de una prueba técnica, donde se cumplieron los requisitos de generar un cobro de prueba, visualizarlo en la plataforma de Khipu, y documentar el proceso técnico.

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

* Se agregaron los siguientes headers:

  * `Content-Type: application/json`
  * `x-api-key`: (API Key de Khipu)
  * `x-signature`: Firma HMAC SHA256 generada con Python.

### 3. Generación de la firma HMAC SHA256

Se creó el archivo `Generar_Firma_HMAC_SHA256.py` con el siguiente código:

```python
import hmac
import hashlib

# Clave secreta entregada por Khipu
secret = b'd28b8445b536406379f72800b42f8887061b4109'

# El mensaje debe ser la ruta SIN dominio
msg = b'/v3/payments/v4ei-x5cw-uaky'

signature = hmac.new(secret, msg, hashlib.sha256).hexdigest()
print("x-signature:", signature)
```

Este script genera la firma que debe incluirse como `x-signature` en los headers de la solicitud.

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
