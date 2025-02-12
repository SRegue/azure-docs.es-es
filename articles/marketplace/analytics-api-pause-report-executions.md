---
title: Pausa de API de ejecuciones de informes
description: Use esta API para pausar la ejecución programada de un informe de análisis de marketplace comercial.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: smannepalle
ms.author: smannepalle
ms.reviewer: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 80a7238e76b7152f13dd157aa0a1b7fc9937867a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121745208"
---
# <a name="pause-report-executions-api"></a>Pausa de API de ejecuciones de informes

En la ejecución, esta API pone en pausa la ejecución programada de los informes.

**Sintaxis de la solicitud**

| Método | URI de la solicitud |
| ------------ | ------------- |
| PUT | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport/pause/{Report ID}` |
|||

**Encabezado de solicitud**

| Encabezado | Tipo | Descripción |
| ------------ | ------------- | ------------- |
| Authorization | string | Necesario. Token de acceso de Azure Active Directory (Azure AD) con el formato `Bearer <token>` |
| Content-Type | string | `Application/JSON` |
||||

**Parámetro de ruta de acceso**

Ninguno

**Parámetro de consulta**

| Nombre de parámetro | Obligatorio | Tipo | Descripción |
| ------------ | ------------- | ------------- | ------------- |
| `reportId` | Sí | string | Id. del informe que se está modificando |
|||||

**Glosario**

Ninguno

**Respuesta**

La carga de respuesta tiene la estructura siguiente en formato JSON:

Código de respuesta: 200, 400, 401, 403, 404, 500 Carga de respuesta:

```json
{
  "Value": [
    {
      "ReportId": "string",
      "ReportName": "string",
      "Description": "string",
      "QueryId": "string",
      "Query": "string",
      "User": "string",
      "CreatedTime": "string",
      "ModifiedTime": "string",
      "StartTime": "string",
      "ReportStatus": "string",
      "RecurrenceInterval": 0,
      "RecurrenceCount": 0,
      "CallbackUrl": "string",
      "Format": "string"
    }
  ],
  "TotalCount": 0,
  "Message": "string",
  "StatusCode": 0
}
```

**Glosario**

| Parámetro | Description |
| ------------ | ------------- |
| `ReportId` | Identificador único universal (UUID) del informe eliminado. |
| `ReportName` | Nombre dado al informe durante la creación. |
| `Description` | Descripción proporcionada durante la creación del informe. |
| `QueryId` | Id. de consulta pasado en el momento en que se creó el informe. |
| `Query` | Texto de la consulta que se ejecutará para este informe. |
| `User` | Id. de usuario que se usa para la creación del informe. |
| `CreatedTime` | Hora de creación del informe. El formato de la hora es aaaa-MM-ddTHH:mm:ssZ. |
| `ModifiedTime` | Hora en que se modificó el informe por última vez. El formato de la hora es aaaa-MM-ddTHH:mm:ssZ. |
| `StartTime` | Hora a la que se iniciará la ejecución del informe. El formato de la hora es aaaa-MM-ddTHH:mm:ssZ. |
| `ReportStatus` | Estado de la ejecución del informe. Los valores posibles son en pausa, activo e inactivo. |
| `RecurrenceInterval` | Intervalo de periodicidad proporcionado durante la creación del informe. |
| `RecurrenceCount` | Recuento de periodicidad proporcionado durante la creación del informe. |
| `CallbackUrl` | Dirección URL de devolución de llamada proporcionada en la solicitud. |
| `Format` | Formato de los archivos de informe. |
|||
