---
title: 'Guia de início rápido: criar um assistente de voz usando Comandos Personalizados'
titleSuffix: Azure Cognitive Services
description: Neste guia de início rápido, você cria e testa um aplicativo básico dos Comandos Personalizados usando o Speech Studio.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.custom: references_regions
ms.openlocfilehash: e046f8cbf6fa0418244f20e9a0c6f75f6da34136
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102434620"
---
# <a name="create-a-voice-assistant-using-custom-commands"></a>Criar um assistente de voz usando comandos personalizados

Neste guia de início rápido, você cria e testa um aplicativo básico dos Comandos Personalizados usando o Speech Studio. Você também poderá acessar o aplicativo em um aplicativo cliente do Windows.

## <a name="region-availability"></a>Disponibilidade de região
Atualmente, os Comandos Personalizados dão suporte para assinaturas de fala criadas nestas regiões:
* Oeste dos EUA
* Oeste dos EUA 2
* Leste dos EUA
* Leste dos EUA 2
* Centro-Oeste dos EUA
* Norte da Europa
* Europa Ocidental
* Leste da Ásia
* Sudeste Asiático
* Índia Central

## <a name="prerequisites"></a>Pré-requisitos

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Crie um recurso de fala do Azure em uma região que dê suporte para Comandos Personalizados.</a> Confira a lista de regiões com suporte na seção **Disponibilidade de região** acima.
> * Baixe o arquivo JSON de exemplo do [Smart Room Lite](https://aka.ms/speech/cc-quickstart).
> * Baixe a versão mais recente do [Cliente de Assistente de Voz do Windows](https://aka.ms/speech/va-samples-wvac).

## <a name="go-to-the-speech-studio-for-custom-commands"></a>Acesse o Speech Studio para Comandos Personalizados

1. Em um navegador da Web, acesse o [Speech Studio](https://speech.microsoft.com/).
1. Insira suas credenciais para entrar no portal.

   A exibição padrão é a lista de assinaturas de fala.
   > [!NOTE]
   > Se a página de seleção de assinaturas não aparecer, selecione "Recursos de fala" no menu de configurações, na barra superior, para acessá-la.

1. Selecione sua assinatura de fala e escolha **Ir para Studio**.
1. Selecione **Comandos Personalizados**.

   A exibição padrão é uma lista dos aplicativos de Comandos Personalizados da assinatura selecionada.

## <a name="import-an-existing-application-as-a-new-custom-commands-project"></a>Importar um aplicativo como novo projeto dos Comandos Personalizados

1. Selecione **Novo projeto** para criar um projeto.

1. Na caixa **Nome**, insira o nome do projeto como `Smart-Room-Lite` (ou outra coisa de sua escolha).
1. Na lista **Idiomas**, selecione **Inglês (Estados Unidos)** .
1. Selecione **Procurar arquivos** e, na janela de procura, selecione o arquivo **SmartRoomLite.json**.

    > [!div class="mx-imgBorder"]
    > ![Criar um projeto](media/custom-commands/import-project.png)

1.  Na lista de **recursos de criação do LUIS**, selecione um recurso de criação. Se não houver recursos de criação válidos, selecione **Criar recurso de criação de LUIS** para criar um.

    > [!div class="mx-imgBorder"]
    > ![Criar um recurso](media/custom-commands/create-new-luis-resource.png)
    
    
    1. Na caixa **Nome do Recurso**, insira o nome do recurso.
    1. Na lista **Grupo de Recursos**, selecione um grupo de recursos.
    1. Na lista **Localização**, selecione uma localização.
    1. Na lista **Tipo de Preço**, selecione um tipo.
    
    
    > [!NOTE]
    > Para criar grupos de recursos, você pode inserir o nome desejado no campo "Grupo de Recursos". O grupo de recursos será criado quando você selecionar **Criar**.


1. Em seguida, selecione **Criar** para criar o projeto.
1. Depois da criação do projeto, selecione o projeto.
Agora você verá a visão geral do novo aplicativo dos Comandos Personalizados.

## <a name="try-out-some-voice-commands"></a>Teste alguns comandos de voz
1. Selecione **Treinar** na parte superior do painel direito.
1. Quando o treinamento terminar, selecione **Testar** e teste estas declarações:
    - Ligue a TV
    - Ajuste a temperatura para 80 graus
    - Desligue
    - A TV
    - Crie um alarme para 17h

## <a name="integrate-custom-commands-application-in-an-assistant"></a>Integrar o aplicativo dos Comandos Personalizados a um assistente
Para acessar o aplicativo fora do Speech Studio, você precisa publicá-lo. Para publicar o aplicativo, você precisa configurar o recurso do LUIS de previsão.  

### <a name="update-prediction-luis-resource"></a>Atualizar recurso do LUIS de previsão


1. Selecione **Configurações** no painel esquerdo e selecione **Recursos do LUIS** no painel central.
1. Selecione um recurso de previsão ou crie um selecionando **Criar recurso**.
1. Selecione **Salvar**.
    
    > [!div class="mx-imgBorder"]
    > ![Configurar recursos do LUIS](media/custom-commands/set-luis-resources.png)

> [!NOTE]
> Como o recurso de criação dá suporte apenas para 1.000 solicitações de ponto de extremidade de previsão por mês, você precisa obrigatoriamente configurar um recurso de previsão do LUIS para publicar o aplicativo dos Comandos Personalizados.

### <a name="publish-the-application"></a>Publicar o aplicativo

Selecione **Publicar** na parte superior do painel direito. Quando a publicação terminar, uma nova janela será exibida. Anote a **ID do aplicativo** e o valor da **chave de recurso de fala** na janela. Você precisará desses dois valores para acessar o aplicativo fora do Speech Studio.

Outra opção para obter esses valores é selecionar a seção **Configurações** > **Geral**.

### <a name="access-application-from-client"></a>Acessar aplicativo do cliente

No escopo deste artigo, usaremos o cliente do assistente de voz do Windows que você baixou nos pré-requisitos. Descompacte a pasta.
1. Inicie **VoiceAssistantClient.exe**.
1. Crie um perfil de publicação e insira o valor de **Perfil de Conexão**. Na seção **Configurações gerais**, insira valores de **Chave de Assinatura** (é o valor da **chave do recurso de Fala** que você salvou ao publicar o aplicativo), **região de chave de assinatura** e **ID do Aplicativo dos Comandos Personalizados**.
    > [!div class="mx-imgBorder"]
    > ![Captura de tela que realça a seção Configurações Gerais para criar um perfil de WVAC.](media/custom-commands/create-profile.png)
1. Selecione **Salvar e Aplicar Perfil**.
1. Agora teste as seguintes entradas por meio de fala/texto
    > [!div class="mx-imgBorder"]
    > ![Criar perfil de WVAC](media/custom-commands/conversation.png)


> [!TIP]
> Você pode clicar nas entradas no **Log de Atividades** para inspecionar as respostas brutas que são enviadas pelo serviço dos Comandos Personalizados.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você usou um aplicativo já criado. Em seguida, nas [seções de instruções](./how-to-develop-custom-commands-application.md), você aprenderá a projetar, desenvolver, depurar, testar e integrar do zero um aplicativo dos Comandos Personalizados.