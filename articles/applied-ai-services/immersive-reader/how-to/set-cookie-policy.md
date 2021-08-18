---
title: Establecimiento de la directiva de cookies del Lector inmersivo
titleSuffix: Azure Applied AI Services
description: En este artículo se explica cómo establecer la directiva de cookies del Lector inmersivo.
services: cognitive-services
author: nitinme
manager: guillasi
ms.service: applied-ai-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: nitinme
ms.custom: devx-track-js
ms.openlocfilehash: 69cf0eb1659b243220eed1199dac06effc22f363
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780832"
---
# <a name="how-to-set-the-cookie-policy-for-the-immersive-reader"></a>Cómo establecer la directiva de cookies del Lector inmersivo

El Lector inmersivo deshabilitará el uso de cookies de forma predeterminada. Si habilita el uso de cookies, el Lector inmersivo puede usar las cookies para mantener las preferencias del usuario y realizar un seguimiento del uso de características. Si habilita el uso de cookies en el Lector inmersivo, tenga en cuenta los requisitos de la directiva de cumplimiento de cookies de la UE. Es responsabilidad de la aplicación host obtener cualquier consentimiento del usuario necesario según la directiva de cumplimiento de cookies de la UE.

La directiva de cookies se puede establecer a través de las opciones de [Lector inmersivo](../reference.md#options).

## <a name="enable-cookie-usage"></a>Habilitar uso de cookies

```javascript
var options = {
    'cookiePolicy': ImmersiveReader.CookiePolicy.Enable
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="disable-cookie-usage"></a>Deshabilitar uso de cookies

```javascript
var options = {
    'cookiePolicy': ImmersiveReader.CookiePolicy.Disable
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="next-steps"></a>Pasos siguientes

* Consulte el [inicio rápido de Node.js](../quickstarts/client-libraries.md?pivots=programming-language-nodejs) para ver qué más puede hacer con el SDK del Lector inmersivo con Node.js
* Vea el [tutorial de Android](../how-to-launch-immersive-reader.md) para ver qué más puede hacer con el SDK del Lector inmersivo con Java o Kotlin para Android
* Vea el [tutorial de iOS](../how-to-launch-immersive-reader.md) para ver qué más puede hacer con el SDK del Lector inmersivo con Swift para iOS
* Vea el [tutorial para Python](../how-to-launch-immersive-reader.md) para consultar qué más puede hacer con el SDK del Lector inmersivo con Python.
* Explorar el [SDK del Lector inmersivo](https://github.com/microsoft/immersive-reader-sdk) y agregar la [Referencia del SDK del Lector inmersivo](../reference.md)