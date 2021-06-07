---
title: 'Início Rápido: Criar uma chave LUIS'
titleSuffix: Azure Cognitive Services
description: Neste início rápido, você aprende a criar um aplicativo LUIS e a obter uma chave.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 06/25/2020
ms.author: trbye
ms.openlocfilehash: 45c509ea2391c370aa0a399b2f0c621238fabc55
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107209673"
---
# <a name="quickstart-getting-a-luis-endpoint-key"></a>Início Rápido: Obter uma chave de ponto de extremidade LUIS

## <a name="prerequisites"></a>Pré-requisitos

Você precisará ter os itens a seguir antes de começar este tutorial:

* Uma conta LUIS. Você pode obter uma gratuitamente por meio do [portal do LUIS](https://www.luis.ai/home).

## <a name="luis-and-speech"></a>LUIS e fala

O LUIS integra-se ao serviço de Fala para reconhecer intenções de fala. Você não precisa de uma assinatura do serviço de Fala, apenas do LUIS.

O LUIS usa três tipos de chaves:

|Tipo de chave|Finalidade|
|--------|-------|
|Criação|Permite criar e modificar aplicativos LUIS programaticamente|
|Inicial|Permite testar o aplicativo LUIS usando somente texto|
|Ponto de extremidade |Autoriza o acesso a um aplicativo LUIS específico|

Para este tutorial, você precisará do tipo de chave de ponto de extremidade. O tutorial usa o aplicativo LUIS de Automação Residencial como exemplo, que pode ser criado seguindo o início rápido [Usar o aplicativo de Automação residencial predefinido](../../luis/luis-get-started-create-app.md). Se já tiver criado um aplicativo LUIS próprio, você poderá usá-lo.

Ao criar um aplicativo LUIS, o LUIS gera automaticamente uma chave de início para que você possa testar o aplicativo usando consultas de texto. Essa chave não habilita a integração com o Serviço de Fala e não funciona com este tutorial. Crie um recurso LUIS no painel do Azure e atribua-o ao aplicativo LUIS. Você pode usar a camada de assinatura gratuita para este tutorial.

Após criar o recurso LUIS no painel do Azure, faça logon no [portal do LUIS](https://www.luis.ai/home), escolha seu aplicativo na página **Meus Aplicativos** e alterne para a página **Gerenciar** do aplicativo. Por fim, selecione **Chaves e os pontos de extremidade** na barra lateral.

![Configurações de ponto de extremidade e chaves do portal do LUIS](~/articles/cognitive-services/Speech-Service/media/sdk/luis-keys-endpoints-page.png)

Na página **Configurações de Chave se Ponto de Extremidade**:

1. Role para baixo até a seção **Recursos e Chaves** e selecione **Atribuir recurso**.
1. Na caixa de diálogo **Atribuir uma chave ao seu aplicativo**, faça as seguintes alterações:

   * Em **Locatário**, escolha **Microsoft**.
   * Em **Nome da Assinatura**, escolha a assinatura do Azure que contém o recurso LUIS que você deseja usar.
   * Em **Chave**, escolha o recurso LUIS que você deseja usar com o aplicativo.

   Em alguns instantes, a nova assinatura será exibida na tabela na parte inferior da página.

1. Selecione o ícone ao lado da chave para copiá-la para a área de transferência. (Você pode usar qualquer uma das chaves.)

![Chaves da assinatura do app LUIS](~/articles/cognitive-services/Speech-Service/media/sdk/luis-keys-assigned.png)


## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Reconhecer intenções](~/articles/cognitive-services/Speech-Service/get-started-intent-recognition.md)