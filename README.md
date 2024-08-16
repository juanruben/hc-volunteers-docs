# Herramienta de Registro de Transporte para Asambleas Especiales, Chile 2024

## Objetivo

El propósito de esta herramienta es proporcionar un mecanismo para registrar los delegados a ser transportados durante las Asambleas Especiales Chile 2024. Está dirigida exclusivamente a los voluntarios que trabajarán como capitanes de buses o sus ayudantes.

[Aplicación](https://d2zasmofz30od8.cloudfront.net/)

-   email: volunteers@example.com

-   contraseña: Password123!

<!-- # Casos de uso

## Ingreso

-   El voluntario se autentica a través del formulario de acceso:

![login](./pics/login.png)

-   Se muestran la(s) asignación(es) que tiene el voluntario para ese día:

![assignmets](./pics/assignments.png)

## Control

-   A través de la vista de control podrá registrar los delegados (y opcionalmente verificar la cantidad de sus respectivos equipajes), que vayan presentándose para abordar el transporte previamente asignado. Es posible Buscar/Filtrar entre el listado de delegados utilizando su nombre, código o alojamiento.

![assignmets](./pics/list.png)
![assignmets](./pics/list2.png)

-   Una vez ubicado el nombre del delegado se puede presionar el botón verde con la fecha hacia arriba para indicar que abordó la unidad de transporte y que la cantidad de piezas de su equipaje fueron verificadas. Esto permitirá saber fácilmente el total de delegados y de piezas de equipaje que se han registrado.

Una vez completada la verificación de los delegados, el botón superior con ícono de Bus verde permite iniciar el viaje. A partir de este momento solo resta completar el viaje y registrar la llegada utilizando el mismo botón que ahora es un Bus naranjo.

![assignmets](./pics/list3.png)
![assignmets](./pics/list4.png)

-   En el menú inferior puede seleccionarse la opción _Protocolos_ para consultar los procedimientos asociados a la asignación. Allí estará publicada información importante relacionada con la asignación, y será visible a todos los voluntarios asociados a la misma.

![assignmets](./pics/protocols.png)

-   La sección de notificaciones es una forma sencilla de enviar alertas a todos los voluntarios asignados a este punto de control. Pueden ser mensajes de último momento, recordatorios, actualizaciones, etc.

![assignmets](./pics/notifications.png)

-   La sección de Emergencia permite al voluntario consultar información sobre hospitales, clínicas, farmacias, etc. cercanos a la ubicación del punto de control.

En caso de surgir una emergencia con un delegado, también podrá reportar detalles de la misma utilizando el código del delegado y registrando un mensaje al comité de hospitalidad

![assignmets](./pics/emergency.png)
![assignmets](./pics/emergency-button.png)
![assignmets](./pics/emergency-report.png) -->

# Consideraciones técnicas

### Tipos

```typescript
// id: identificador único para el viaje. Será utilizado en otras operaciones. Se recomienda que sea un guid
// name: es el nombre visible del viaje
// description: descripción visible del viaje
// origin: Nombre del punto de origen del viaje
// destination: Nombre del punto de destino del viaje
// showLuggage: opcional. Indica si en el proceso de registro el voluntario debería contar con la funcionalidad que permite gestionar el equipaje
// isRoundTrip: optional. Indica si el viaje debe considerarse ida-vuelta
// outboundDepartureTime: opcional. Fecha y hora en que es registrada la partida del transporte desde el punto de origen
// outboundArrivalTime: opcional. Fecha y hora en que es registrada la llegada del transporte al punto de destino
// returnDepartureTime: opcional. Fecha y hora en que es registrada la partida del transporte de regreso
// returnArrivalTime: opcional. Fecha y hora en que es registrada la llegada del transporte de regreso al punto de origen
// delegates: Arreglo de delegados asignados al viaje
interface ITrip {
    id: string;
    name: string;
    description: string;
    origin: string;
    destination: string;
    showLuggage?: boolean;
    isRoundTrip?: boolean;

    outboundDepartureTime?: string;
    outboundArrivalTime?: string;
    returnDepartureTime?: string;
    returnArrivalTime?: string;

    delegates: IDelegate[];
}

// id: identificador único para el delegado
// nombre: nombre completo del delegado
// countryCode: Código de dos caracteres del país de procedencia
// accommodation: Nombre del hotel en donde aloja el delegado
// outboundTripCheckedBy: opcional. Nombre del voluntario que registró el embarque de salida del delegado
// outboundTripCheckedAt: opcional. Fecha y hora en la que se registró el embarque de salida del delegado
// returnTripCheckedBy: opcional. Nombre del voluntario que registró el embarque de regreso del delegado
// returnTripCheckedAt: opcional. Fecha y hora en la que se registró el embarque de regreso del delegado
// luggageCount: opcional. Cantidad de piezas de equipaje del delegado
// checkedLuggageCount: opcional. Cantidad de piezas de equipaje escaneadas del delegado
interface IDelegate {
    id: string;
    name: string;
    countryCode: string;
    accommodation: string;

    outboundTripCheckedBy: string;
    outboundTripCheckedAt: string;
    returnTripCheckedBy: string;
    returnTripCheckedAt: string;

    luggageCount?: number;
    checkedLuggageCount?: number;
}

// tripId: identificador único del viaje. Se recomienda que sea un guid
// title: nombre visible del titulo del viaje
// content: contenido a mostrar
interface IProtocol {
    tripId: string;
    title: string;
    content: string;
}

// id: identificador único para la notificación. Se recomienda que sea un guid
// created_at: Fecha/hora en que se creó la notificación en el backoffice
// subject: título de la notificación
// message: cuerpo de la notificación
interface INotification {
    id: string;
    dateTime: string;
    subject: string;
    message: string;
}

// tripId: identificador único del viaje al que pertenece el listado de notificaciones. Se recomienda que sea un guid
// tripName: nombre visible del viaje al que pertenece el listado de notificaciones
// notifications: array de notificaciones
interface INotificationsList {
    tripId: string;
    tripName: string;
    notifications: INotification[];
}

// tripId: identificador único del viaje. Se recomienda que sea un guid
// title: nombre visible del titulo del viaje
// content: contenido a mostrar
interface IEmergencyInfo {
    tripId: string;
    title: string;
    content: string;
}
```

# Endpoints REST

## Autenticación

#### POST /transport_volunteers/auth/login

-   **Payload:**
    ```json
    {
        "email": "string",
        "password": "string"
    }
    ```
-   **Response:**
    ```json
    {
        "token": "string",
        "transport_volunteer": {
            "name": "string",
            "email": "string"
        }
    }
    ```
-   **Ejemplo:**
    ```json
    {
        "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
        "transport_volunteer": {
            "name": "John Doe",
            "email": "volunteers@example.com"
        }
    }
    ```

Todos los endpoints a continuación están restringidos a usuarios registrados y autenticados. El token de autenticación es enviado en el header de cada request.

## Viaje

### GET `/trips`

Retorna los viajes en los que el voluntario asociado al `token` que se envía en el header del request, ha sido asignado en la fecha `date`.

-   **Params:** `date`
-   **Ejemplo:** `2024-10-25`

-   **Response:** `ITrip[]`
-   **Ejemplo:**

```json
[
    {
        "id": "e1bcfd24-08fd-4e57-bbd5-e51c4c1fac65",
        "name": "Bus 1",
        "description": "Best Western, Courtyard, Marriott",
        "showLuggage": true,
        "origin": "Aeropuerto",
        "destination": "Hotel",
        "delegates": [
            {
                "id": "53580",
                "name": "John Smith",
                "countryCode": "US",
                "accommodation": "Hotel Marriott Santiago"
            },
            {
                "id": "23456",
                "name": "Jane Smith",
                "countryCode": "CA",
                "accommodation": "Hotel Marriott Santiago"
            }
        ]
    },
    {
        "id": "e1bcfd24-08fd-4e57-bbd5-e51c4c1fac64",
        "name": "Bus 15",
        "description": "Best Western, Courtyard, Marriott",
        "isRoundTrip": true,
        "origin": "Hotel",
        "destination": "Evento",
        "delegates": [
            {
                "id": "53580",
                "name": "John Smith",
                "countryCode": "US",
                "accommodation": "Hotel Marriott Santiago"
            },
            {
                "id": "23456",
                "name": "Jane Smith",
                "countryCode": "CA",
                "accommodation": "Hotel Marriott Santiago"
            }
        ]
    }
]
```

### GET `/trips/{tripId}`

Retorna la información del viaje `tripId`.

-   **Response:** `ITrip`
-   **Ejemplo:**

```json
{
    "id": "e1bcfd24-08fd-4e57-bbd5-e51c4c1fac65",
    "name": "Bus 1",
    "description": "Best Western, Courtyard, Marriott",
    "showLuggage": true,
    "origin": "Aeropuerto",
    "destination": "Hotel",

    // "outboundDepartureTime": undefined,
    // "outboundArrivalTime": undefined,
    "delegates": [
        {
            "id": "53580",
            "name": "John Smith",
            "countryCode": "US",
            "accommodation": "Hotel Marriott Santiago",
            "outboundCheckedBy": "John Doe",
            "outboundCheckedAt": "8/12/2024, 6:29:20 PM",
            "luggageCount": 2,
            "checkedLuggageCount": 1
        },
        {
            "id": "23456",
            "name": "Jane Smith",
            "countryCode": "CA",
            "accommodation": "Hotel Marriott Santiago",
            "luggageCount": 3
        }
    ]
}
```

### `GET /trips/{tripId}/delegate/{delegateId}`

Retorna la información del delegado `delegateId` con respecto al viaje `tripId`

-   **Response:** `IDelegate`
-   **Ejemplo:**

    ```json
    {
        "id": "53580",
        "name": "John Smith",
        "countryCode": "US",
        "accommodation": "Hotel Marriott Santiago",
        "luggageCount": 2,
        "checkedLuggageCount": 1,
        "outboundCheckedBy": "John Doe",
        "outboundCheckedAt": "8/12/2024, 6:29:20 PM",
    },
    ```

## Registro de delegado en viaje

### POST `/trips/{tripId}/delegate/{delegateId}/check_in`

Agrega al registro que el delegado `delegateId` fue chequeado en el viaje `tripId`, registrando el voluntario que lo chequeó y la fecha/hora en que ocurrió. El parámetro `isReturnTrip` será enviado `true` cuando en un viaje de ida y vuelta se está chequeando al delegado al momento de abordar el transporte en el viaje de regreso.

Retorna `true` si fue exitoso.

-   **Payload:**

    ```json
    {
        "isReturnTrip": true
    }
    ```

-   **Response:** `boolean`

### POST `/trips/{tripId}/delegate/{delegateId}/check_out`

Remueve el registro del delegado `delegateId` en el viaje `tripId`, eliminando el voluntario que previamente lo chequeó y la fecha/hora. El parámetro `isReturnTrip` será enviado `true` cuando se trata de un viaje de ida y vuelta y se está removiendo al delegado como chequeado en el viaje de regreso. La finalidad de este endpoint es dar la opción de desmarcar un delegado que haya podido ser chequeado por error.

Retorna `true` si fue exitoso.

-   **Payload:**

    ```json
    {
        "isReturnTrip": true
    }
    ```

-   **Response:** `boolean`

### POST `/trips/{tripId}/luggage`

Registra el equipaje que posee el código QR `qr`, en el viaje `tripId`. En caso exitoso (cuando el equipaje no ha sido escaneado y pertenece a un delegado asignado al viaje `tripId`), esto significa que será incrementado el campo `checkedLuggageCount` del delegado propietario del equipaje. Importante: Debe tenerse en cuenta que al escanear un mismo código QR varias veces, el incremento no se vea afectado.

Retorna `true` si fue exitoso.

-   **Payload:**

    ```json
    {
        "qr": "1"
    }
    ```

-   **Response:** `boolean`

## Registro de estado del viaje

### POST `/trips/{tripId}/departure`

Registra que el viaje `tripId` ha partido, registrando el voluntario que lo chequeó y la fecha/hora en que ocurrió (modifica `outboundDepartureTime` o `returnDepartureTime`, según el valor de `isReturnTrip`). El parámetro `isReturnTrip` será enviado `true` cuando se trata de un viaje de ida y vuelta y se está iniciando el viaje de regreso.
Retorna `true` si fue exitoso.

-   **Payload:**
    ```json
    {
        "isReturnTrip": true
    }
    ```
-   **Response:** `boolean`

### POST `/trips/{tripId}/arrival`

Registra que el viaje `tripId` ha llegado a su destino, registrando el voluntario que lo chequeó y la fecha/hora en que ocurrió (modifica `outboundArrivalTime` o `returnArrivalTime`, según el valor de `isReturnTrip`). El parámetro `isReturnTrip` será enviado `true` cuando se trata de un viaje de ida y vuelta y se está dando por finalizado el viaje de regreso.
Retorna `true` si fue exitoso.

-   **Payload:**
    ```json
    {
        "isReturnTrip": true
    }
    ```
-   **Response:** `boolean`

## Otros

### `GET /trips/{tripId}/protocols`

Retorna el texto que se mostrará en la sección de protocolos, asociado al viaje `tripId`. Puede ser texto enriquecido que se mostrará apropiadamente en la aplicación.

-   **Response:** `IProtocol`
-   **Ejemplo:**

    ```json
    {
        "tripId": "e1bcfd24-08fd-4e57-bbd5-e51c4c1fac64",
        "title": "Bus 15",
        "content": "Aquí puede publicarse información importante relacionada <i style=\"font-weight: 600;\">con esta asignación</i> chequeando equipaje. La verán todos los voluntarios asociados a la misma.\n    <p>Por ejemplo:</p>\n    <ul>\n        <li>Protocolos de seguridad que se deben seguir </li>\n        <li>Procedimientos definidos por el comité de hospitalidad para esta asignación</li>\n        <li>Qué hacer ante un imprevisto</li>\n        <li>Rutas de evacuación</li>\n        <li>\n            Incluso puede haber imágenes provenientes de la configuración de la asignación en el backoffice:\n            <br />\n            <br />\n            <div>\n                <img src='https://placehold.co/400' width='100%' style=\"max-width: 300px;\" />\n            </div>\n        </li>\n    </ul>"
    }
    ```

### `GET /trips/{id}/emergency_info`

Retorna el texto que se mostrará en la sección de emergencias asociadas al viaje `tripId`. Puede ser texto enriquecido que se mostrará apropiadamente en la aplicación.

-   **Response:** `IEmergencyInfo`
-   **Ejemplo:**

    ```json
    {
        "tripId": "e1bcfd24-08fd-4e57-bbd5-e51c4c1fac64",
        "title": "Bus 15",
        "content": "<p>\n        En caso de emergencias durante esta asignación el voluntario puede consultar esta sección para encontrar información sobre hospitales, clínicas, farmacias, etc. cercanos a la ubicación\n        de esta asignación.\n    </p>\n    <p>Por ejemplo:</p>\n    <ul>\n        <li>\n            <div>Hospital Gustavo Fricke</div>\n            <a href='https://maps.google.com/?q=-33.02877500992159,%20-71.54304099594539'>📍 Álvarez 1532, Viña del Mar, Valparaíso</a>\n        </li>\n        <li>\n            <div>Farmacia Cruz Verde</div>\n            <a href='https://maps.google.com/?q=-32.99438845000991,-71.51027942681407'>📍 Avenida Alessandri, Almte. Gómez Carreño 4085, Viña del Mar, Valparaíso</a>\n        </li>\n    </ul>\n    <p>También puede reportar detalles de la emergencia introduciendo el id del delegado y registrando un mensaje al comité de hospitalidad.</p>"
    }
    ```

### `GET /trips/{tripId}/notifications`

Retorna un `INotificationsList` con todas las notificaciones asociadas al viaje `tripId`. La app se encargará de consultar este endpoint con cierta frecuencia y destacar las notificaciones más recientes.

-   **Response:** `INotificationsList`
-   **Ejemplo:**
    ```json
    {
        "tripId": "e1bcfd24-08fd-4e57-bbd5-e51c4c1fac64",
        "tripName": "Bus 15.",
        "notifications": [
            {
                "id": "fbc5f630-2388-46ee-b7cb-e00236a6e22f",
                "created_at": "2024-07-21T10:00:00Z",
                "subject": "Consideración importante",
                "message": "Sit amet diam diam lorem et sanctus sea dolor erat stet, sit voluptua gubergren dolores ipsum voluptua et aliquyam nonumy."
            }
        ]
    }
    ```

## Registrar emergencias en un viaje

Registra la eventualidad de un caso de emergencia asociado al delegado `delegateId` en el viaje `tripId`. `message` es un texto con las observaciones del voluntario que realiza el reporte

### POST `/trips/{tripId}/report_emergency`

-   **Payload:**
    ```json
    {
        "delegateId": "string",
        "message": "string"
    }
    ```
-   **Response:** `boolean`

```

```
