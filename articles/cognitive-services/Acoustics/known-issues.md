---
title: Problemas conhecidos com o plug-in de Projeto Acústico
titlesuffix: Azure Cognitive Services
description: Você pode encontrar os seguintes problemas conhecidos ao usar o Designer Preview for Project Acoustics.
services: cognitive-services
author: kylestorck
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: resources
ms.date: 03/20/2019
ms.author: kylestorck
ms.openlocfilehash: 50de4d983ed24440d655cf5b9ba3fb5e33d8d7cd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61335340"
---
# <a name="project-acoustics-known-issues"></a>Problemas conhecidos do projeto acústica
Você pode encontrar os seguintes problemas conhecidos ao usar o Designer Preview for Project Acoustics.

## <a name="acoustic-parameters-are-lost-when-you-rename-a-scene"></a>Parâmetros acústicos são perdidos quando você renomeia uma cena

Se você renomear uma cena, todos os parâmetros acústicos que pertencem a essa cena automaticamente não transferir para a nova cena. Eles serão ainda existem no arquivo de ativo antigo no entanto. Procure o arquivo **SceneName_AcousticParameters.asset** dentro do diretório **Editor** ao lado do seu arquivo de cena. Renomeie o arquivo para refletir o novo nome da cena.

## <a name="unity-crashes-when-closing-project"></a>Falha de Unity ao fechar projeto

Nas versões mais recentes do Unity (2018.2+), há um bug conhecido no qual o Unity travará quando você fechar o projeto. Isso é controlado por [esse problema Unity](https://issuetracker.unity3d.com/issues/crash-on-assetdatabase-getassetimporterversions-when-closing-a-specific-unity-project).

## <a name="deploying-to-android-from-some-unity-versions"></a>Implantação no Android de algumas versões do Unity

Algumas versões do Unity tem um bug com a implantação de plug-ins de áudio para o Android. Verifique se você não estiver usando uma versão afetada pela [esse bug](https://issuetracker.unity3d.com/issues/android-ios-audiosource-playing-through-google-resonance-audio-sdk-with-spatializer-enabled-does-not-play-on-built-player).

## <a name="i-get-an-error-that-could-not-find-metadata-file-systemsecuritydll"></a>Eu recebo um erro que 'não foi possível encontrar o arquivo de metadados dll'

Assegure-se de que a Versão do Runtime do Script nas configurações do Player esteja definida como **Equivalente ao .NET 4.x** e reinicie o Unity.

## <a name="im-having-authentication-problems-when-connecting-to-azure"></a>Estou tendo problemas de autenticação ao se conectar ao Azure

Verifique se você usou as credenciais corretas para sua conta do Azure, se sua conta oferece suporte ao tipo de nó solicitado no bake e se o relógio do sistema está correto.

## <a name="canceling-a-bake-leaves-the-bake-tab-in-deleting-state"></a>Cancelar um bake deixa a guia Bake em estado de "exclusão"
Projeto acústica limpará todos os recursos do Azure para um trabalho na conclusão bem-sucedida ou cancelamento. Isso pode levar até 5 minutos.

## <a name="next-steps"></a>Próximos passos
* Experimente o [Unity](unity-quickstart.md) ou [Unreal](unreal-quickstart.md) conteúdo de exemplo

