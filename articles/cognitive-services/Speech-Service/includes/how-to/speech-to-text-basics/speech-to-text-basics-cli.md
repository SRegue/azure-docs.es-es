---
author: PatrickFarley
ms.service: cognitive-services
ms.topic: include
ms.date: 09/08/2020
ms.author: pafarley
ms.openlocfilehash: 8f3574839f8f7c330b49249f5b90a5404117ab9e
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/07/2021
ms.locfileid: "123542851"
---
Una de las características principales del servicio de voz es la capacidad para reconocer y transcribir la voz humana (que a menudo se denomina "conversión de voz en texto"). En este inicio rápido, aprenderá a usar la CLI de Voz en sus aplicaciones y productos para realizar una conversión de voz en texto de alta calidad.

[!INCLUDE [SPX Setup](../../spx-setup.md)]

## <a name="speech-to-text-from-microphone"></a>Conversión de voz en texto desde un micrófono

Conecte y encienda el micrófono de su PC y desactive las aplicaciones que puedan usarlo también. Algunos equipos tienen un micrófono integrado, mientras que otros requieren la configuración de un dispositivo Bluetooth.

Ahora está listo para ejecutar la CLI de Voz y reconocer la voz que procede del micrófono. Desde la línea de comandos, cambie al directorio que contiene el archivo binario de la CLI de Voz y ejecute el comando siguiente.

```bash
spx recognize --microphone
```

> [!NOTE]
> La CLI de Voz tiene como valor predeterminado el inglés. Puede elegir un idioma diferente [en la tabla de voz a texto](../../../../language-support.md).
> Por ejemplo, agregue `--source de-DE` para reconocer voz en alemán.

Hable al micrófono y verá la transcripción de sus palabras en texto en tiempo real. La CLI de Voz se detendrá después de un período de silencio o cuando presione ctrl+C.

## <a name="speech-to-text-from-audio-file"></a>Conversión de voz en texto desde un archivo de audio

La CLI de Voz puede reconocer voz en muchos formatos de archivo e idiomas naturales. En este ejemplo, puede usar un archivo WAV (16 kHz u 8 kHz, 16 bits y PCM mono) que contenga voz en inglés. O bien, si desea algo más rápido, descargue el archivo <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/whatstheweatherlike.wav" download="whatstheweatherlike" target="_blank">whatstheweatherlike.wav <span class="docon docon-download x-hidden-focus"></span></a> y cópielo en el mismo directorio que el archivo binario de la CLI de Voz.

Ya está listo para ejecutar la CLI de Voz y reconocer la voz que se encuentra en el archivo de sonido. Para ello, solo hay que ejecutar el siguiente comando.

```bash
spx recognize --file whatstheweatherlike.wav
```

> [!NOTE]
> La CLI de Voz tiene como valor predeterminado el inglés. Puede elegir un idioma diferente [en la tabla de voz a texto](../../../../language-support.md).
> Por ejemplo, agregue `--source de-DE` para reconocer voz en alemán.

La CLI de Voz mostrará una transcripción a texto de la voz en la pantalla y, luego,