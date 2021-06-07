---
title: Versão atual do IoT Plug and Play | Microsoft Docs
description: Saiba o que está incluído na versão atual do IoT Plug and Play.
author: dominicbetts
ms.author: dobett
ms.date: 10/01/2020
ms.topic: overview
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 7b6669ca55dc9b94dc5d702e54b42011120b8812
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99831868"
---
# <a name="what-is-in-the-current-iot-plug-and-play-release"></a>O que a versão atual do IoT Plug and Play inclui?

Este artigo resume as ferramentas, os SDKs e as APIs que dão suporte à versão atual do IoT Plug and Play. Os números de versão mostrados refletem o número de versão no momento em que o IoT Plug and Play ficou em disponibilidade geral. Os números de versão podem ser incrementados após o lançamento.

## <a name="modeling-language"></a>Linguagem de modelagem

[DTDL (Linguagem de Definição de Gêmeos Digitais) v2](https://github.com/Azure/opendigitaltwins-dtdl).

Para saber mais sobre como os dispositivos IoT Plug and Play funcionam com a DTDL, confira [Convenções do IoT Plug and Play](concepts-convention.md).

## <a name="tools-and-utilities"></a>Ferramentas e utilitários

- Azure IoT Explorer 0.12.0.

    Para saber mais, confira [Instalar e usar o Azure IoT Explorer](howto-use-iot-explorer.md).

- Extensão do VS Code 1.0.0.

    Para saber mais, confira [Instalar e usar as ferramentas de criação DTDL](howto-use-dtdl-authoring-tools.md).

- Extensão do Visual Studio 2019 1.0.0.

    Para saber mais, confira [Instalar e usar as ferramentas de criação DTDL](howto-use-dtdl-authoring-tools.md).

- Extensão de IoT da CLI do Azure 0.10.0.

    A extensão de IoT do Azure inclui comandos que ajudam na certificação de dispositivos. Consulte `az iot product -h`.

## <a name="libraries-and-sdks"></a>Bibliotecas e SDKs

Para saber mais sobre as bibliotecas e os SDKs, confira [SDKs da Microsoft para o IoT Plug and Play](libraries-sdks.md).

- SDK do dispositivo C [vcpkg 1.3.9](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/setting_up_vcpkg.md)
- SDK do dispositivo C inserido [GitHub](https://github.com/Azure/azure-sdk-for-c/)
- SDK do dispositivo .NET [NuGet 1.31.0](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client)
- SDK do dispositivo Java [Maven 1.26.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-device-client)
- SDK do dispositivo Python [pip 2.3.0](https://pypi.org/project/azure-iot-device/)
- SDK do dispositivo Node.js [npm 1.17.2](https://www.npmjs.com/package/azure-iot-device)
- .NET – Serviço Hub IoT [NuGet 1.27.1](https://www.nuget.org/packages/Microsoft.Azure.Devices )
- Java – Serviço Hub IoT [Maven 1.26.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-service-client/1.26.0)
- Node.js – Serviço Hub IoT [npm 1.13.0](https://www.npmjs.com/package/azure-iothub)
- Python – Serviço Hub IoT/Gêmeos Digitais [pip 2.2.3](https://pypi.org/project/azure-iot-hub)
- [NuGet](https://www.nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser) do analisador de modelo DTDL.

## <a name="rest-apis"></a>APIs REST

API REST [2020-09-30](/rest/api/iothub).

Para saber mais, confira o [Guia do desenvolvedor do IoT Plug and Play](concepts-developer-guide-service.md).

## <a name="iot-hub"></a>Hub IoT

O IoT Plug and Play é compatível com o Hub IoT em todas as regiões. O IoT Plug and Play é compatível somente com os hubs IoT Standard ou da Camada gratuita.

## <a name="announcements"></a>Comunicados

Para obter os anúncios atuais e anteriores do IoT Plug and Play, confira as seguintes postagens no blog:

- [Atualização da versão prévia pública (postada em 29 de agosto de 2020)](https://techcommunity.microsoft.com/t5/internet-of-things/add-quot-plug-and-play-quot-to-your-iot-solutions/ba-p/1548531)
- [Preparar e certificar seus dispositivos para o IoT Plug and Play (postado em 26 de agosto de 2020)](https://azure.microsoft.com/blog/prepare-and-certify-your-devices-for-iot-plug-and-play/)
- [Agora, o IoT Plug and Play está disponível em versão prévia (postado em 22 de agosto de 2019)](https://azure.microsoft.com/blog/iot-plug-and-play-is-now-available-in-preview/)
- [Criar com o Azure IoT Central e o IoT Plug and Play (postado em 7 de maio de 2019)](https://azure.microsoft.com/blog/build-with-azure-iot-central-and-iot-plug-and-play/)

## <a name="next-steps"></a>Próximas etapas

A próxima etapa sugerida é examinar [O que é o IoT Plug and Play?](overview-iot-plug-and-play.md).