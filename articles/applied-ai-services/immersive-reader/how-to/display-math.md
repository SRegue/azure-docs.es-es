---
title: Mostrar matemáticas en el Lector inmersivo
titleSuffix: Azure Applied AI Services
description: En este artículo se explica cómo mostrar las matemáticas en el Lector inmersivo.
author: nitinme
manager: guillasi
ms.service: applied-ai-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: nitinme
ms.custom: devx-track-js
ms.openlocfilehash: 0d4a0237d502cb353c262a40c92f99dfdbb8e7d6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780486"
---
# <a name="how-to-display-math-in-the-immersive-reader"></a>Cómo mostrar matemáticas en el Lector inmersivo

El Lector inmersivo puede mostrar matemáticas cuando se proporcionan en forma de lenguaje de marcado matemático ([MathML](https://developer.mozilla.org/docs/Web/MathML)).
El tipo MIME se puede establecer a través del [fragmento](../reference.md#chunk) del Lector inmersivo. Para obtener más información, consulte los [tipos MIME admitidos](../reference.md#supported-mime-types).

## <a name="send-math-to-the-immersive-reader"></a>Envío de matemáticas al Lector inmersivo
Para enviar matemáticas al Lector inmersivo, proporcione un fragmento que contenga MathML, y establezca el tipo MIME en ```application/mathml+xml```.

Por ejemplo, si el contenido fuera el siguiente:

```html
<div id='ir-content'>
    <math xmlns='http://www.w3.org/1998/Math/MathML'>
        <mfrac>
            <mrow>
                <msup>
                    <mi>x</mi>
                    <mn>2</mn>
                </msup>
                <mo>+</mo>
                <mn>3</mn>
                <mi>x</mi>
                <mo>+</mo>
                <mn>2</mn>
            </mrow>
            <mrow>
                <mi>x</mi>
                <mo>−</mo>
                <mn>3</mn>
            </mrow>
        </mfrac>
        <mo>=</mo>
        <mn>4</mn>
    </math>
</div>
```

Podría mostrar el contenido mediante el siguiente código JavaScript.

```javascript
const data = {
    title: 'My Math',
    chunks: [{
        content: document.getElementById('ir-content').innerHTML.trim(),
        mimeType: 'application/mathml+xml'
    }]
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, data, YOUR_OPTIONS);
```

Al iniciar el Lector inmersivo, debería ver:

![Matemáticas en el Lector inmersivo](../media/how-tos/1-math.png)

## <a name="next-steps"></a>Pasos siguientes

* Explorar el [SDK del Lector inmersivo](https://github.com/microsoft/immersive-reader-sdk) y agregar la [Referencia del SDK del Lector inmersivo](../reference.md)