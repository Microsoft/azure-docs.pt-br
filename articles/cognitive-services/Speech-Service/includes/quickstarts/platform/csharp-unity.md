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
ms.openlocfilehash: fd592b6f565cb23d7a922af2a68e6328911c2dc0
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/26/2020
ms.locfileid: "96188141"
---
Este guia mostra como instalar o [SDK de Fala](~/articles/cognitive-services/speech-service/speech-sdk.md) para [Unity](https://unity3d.com/).

> [!NOTE]
> O SDK de Fala para Unity dá suporte à Área de Trabalho do Windows (x86 e x64) ou à Plataforma Universal do Windows (x86, x64, ARM/ARM64), ao Android (x86, ARM32/64) e ao iOS (simulador x64, ARM32 e ARM64)

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

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
