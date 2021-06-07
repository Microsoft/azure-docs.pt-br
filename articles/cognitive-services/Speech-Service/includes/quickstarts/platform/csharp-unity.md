---
title: 'Início Rápido: Configuração de plataforma do SDK de Fala para Unity C# – Serviço de Fala'
titleSuffix: Azure Cognitive Services
description: Use este guia para configurar sua plataforma para Unity C# com o SDK do Serviço de Fala.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/15/2020
ms.author: erhopf
ms.custom: devx-track-csharp
ms.openlocfilehash: 895943b00ef2c2541b7de6cc81648877c9a73c0d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750907"
---
Este guia mostra como instalar o [SDK de Fala](~/articles/cognitive-services/speech-service/speech-sdk.md) para [Unity](https://unity3d.com/).

> [!NOTE]
> O SDK de Fala para Unity dá suporte à Área de Trabalho do Windows (x86 e x64) ou à Plataforma Universal do Windows (x86, x64, ARM/ARM64), ao Android (x86, ARM32/64) e ao iOS (simulador x64 e ARM64)

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

- No Windows, é necessário ter os [Pacotes Redistribuíveis do Microsoft Visual C++ para Visual Studio 2019](https://support.microsoft.com/en-us/topic/the-latest-supported-visual-c-downloads-2647da03-1eea-4433-9aff-95f26a218cc0) para a sua plataforma. Ao instalá-los pela primeira vez, pode ser necessária uma reinicialização.
- [Unity 2018.3 ou posterior](https://store.unity.com/) com o [Unity 2019.1 adicionando suporte ao UWP ARM64](https://blogs.unity3d.com/2019/04/16/introducing-unity-2019-1/#universal).
- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/). A versão 15.9 ou superior do Visual Studio 2017 também é aceitável.
- Para obter suporte ao Windows ARM64, instale as [ferramentas de build opcionais para o ARM64 e o SDK do Windows 10 para ARM64](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/).

## <a name="install-the-speech-sdk"></a>Instalar o SDK de Fala

Para instalar o SDK de Fala para o Unity, siga estas etapas:

1. Baixe e abra o [SDK de Fala para Unity](https://aka.ms/csspeech/unitypackage), que é empacotado como um pacote de ativos do Unity (.unitypackage) e que já deve ser associado ao Unity. Quando o pacote de ativos é aberto, a caixa de diálogo **Importar Pacote do Unity** é exibida. Talvez seja necessário criar e abrir um projeto vazio para que essa etapa funcione.

   [![Caixa de diálogo Importar pacote do Unity no Editor do Unity](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-unity-01-import.png)](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-unity-01-import.png#lightbox)

1. Verifique se todos os arquivos estão selecionados e selecione **Importar**. Depois de alguns instantes, o pacote de ativos do Unity é importado para o projeto.

Para obter mais informações sobre como importar pacotes de ativos para o Unity, confira a [documentação do Unity](https://docs.unity3d.com/Manual/AssetPackages.html).

Agora é possível avançar para as [Próximas etapas](#next-steps) abaixo.

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [windows](../quickstart-list.md)]
