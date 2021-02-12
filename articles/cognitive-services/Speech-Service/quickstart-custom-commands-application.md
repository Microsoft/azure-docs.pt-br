---
title: 'Início rápido: criar um assistente de voz usando comandos personalizados'
titleSuffix: Azure Cognitive Services
description: Neste guia de início rápido, você cria e testa um aplicativo básico de comandos personalizados usando o Speech Studio.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.custom: references_regions
ms.openlocfilehash: ea32358aa86a6093cd72adf959d5fe731cf1e125
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98786869"
---
# <a name="create-a-voice-assistant-using-custom-commands"></a>Criar um assistente de voz usando comandos personalizados

Neste guia de início rápido, você cria e testa um aplicativo básico de comandos personalizados usando o Speech Studio. Você também poderá acessar esse aplicativo de um aplicativo cliente do Windows.

## <a name="region-availability"></a>Disponibilidade de região
Neste momento, os comandos personalizados dão suporte a assinaturas de fala criadas nessas regiões:
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
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Crie um recurso de fala do Azure em uma região que dê suporte a comandos personalizados.<span class="docon docon-navigate-external x-hidden-focus"></span></a> Consulte a seção **disponibilidade de região** acima para obter a lista de regiões com suporte.
> * Baixe o arquivo JSON de exemplo do [Smart Room Lite](https://aka.ms/speech/cc-quickstart) .
> * Baixe a versão mais recente do [cliente do assistente do Windows Voice](https://aka.ms/speech/va-samples-wvac).

## <a name="go-to-the-speech-studio-for-custom-commands"></a>Ir para o Speech Studio para comandos personalizados

1. Em um navegador da Web, vá para o [Speech Studio](https://speech.microsoft.com/).
1. Insira suas credenciais para entrar no portal.

   A exibição padrão é sua lista de assinaturas de fala.
   > [!NOTE]
   > Se você não vir a página Selecionar assinatura, poderá navegar até lá escolhendo "recursos de fala" no menu configurações na barra superior.

1. Selecione sua assinatura de fala e, em seguida, selecione **ir para Studio**.
1. Selecione **comandos personalizados**.

   O modo de exibição padrão é uma lista dos aplicativos de comandos personalizados que você tem sob sua assinatura selecionada.

## <a name="import-an-existing-application-as-a-new-custom-commands-project"></a>Importar um aplicativo existente como um novo projeto de comandos personalizados

1. Selecione **novo projeto** para criar um projeto.

1. Na caixa **nome** , insira o nome do projeto como `Smart-Room-Lite` (ou outra coisa de sua escolha).
1. Na lista **idioma** , selecione **Inglês (Estados Unidos)**.
1. Selecione **procurar arquivos** e, na janela procurar, selecione o **SmartRoomLite.jsno** arquivo.

    > [!div class="mx-imgBorder"]
    > ![Criar um projeto](media/custom-commands/import-project.png)

1.  Na lista de **recursos de criação do Luis** , selecione um recurso de criação. Se não houver recursos de criação válidos, crie um selecionando  **criar novo recurso de criação de Luis**.

    > [!div class="mx-imgBorder"]
    > ![Criar um recurso](media/custom-commands/create-new-luis-resource.png)
    
    
    1. Na caixa **nome do recurso** , digite o nome do recurso.
    1. Na lista **grupo de recursos** , selecione um grupo de recursos.
    1. Na lista **local** , selecione um local.
    1. Na lista **tipo de preço** , selecione uma camada.
    
    
    > [!NOTE]
    > Você pode criar grupos de recursos inserindo o nome do grupo de recursos desejado no campo "grupo de recursos". O grupo de recursos será criado quando a **criação** for selecionada.


1. Em seguida, selecione **criar** para criar seu projeto.
1. Depois que o projeto for criado, selecione seu projeto.
Agora você deve ver a visão geral do seu novo aplicativo de comandos personalizados.

## <a name="try-out-some-voice-commands"></a>Experimente alguns comandos de voz
1. Selecione **treinar** na parte superior do painel direito.
1. Quando o treinamento for concluído, selecione **testar** e experimente o seguinte declarações:
    - Ligar a TV
    - Definir a temperatura como 80 graus
    - Desativá-lo
    - A TV
    - Definir um alarme para 5 PM

## <a name="integrate-custom-commands-application-in-an-assistant"></a>Integrar o aplicativo de comandos personalizados em um assistente
Antes de poder acessar esse aplicativo de fora do Speech Studio, você precisa publicar o aplicativo. Para publicar um aplicativo, você precisará configurar o recurso de LUIS de previsão.  

### <a name="update-prediction-luis-resource"></a>Atualizar recurso de LUIS de previsão


1. Selecione **configurações** no painel esquerdo e selecione  **recursos do Luis** no painel central.
1. Selecione um recurso de previsão ou crie um selecionando **criar novo recurso**.
1. Selecione **Salvar**.
    
    > [!div class="mx-imgBorder"]
    > ![Definir recursos do LUIS](media/custom-commands/set-luis-resources.png)

> [!NOTE]
> Como o recurso de criação dá suporte a apenas 1.000 solicitações de ponto de extremidade de previsão por mês, você precisará definir um recurso de previsão LUIS antes de publicar o aplicativo de comandos personalizados.

### <a name="publish-the-application"></a>Publicar o aplicativo

Selecione  **publicar** na parte superior do painel direito. Quando a publicação for concluída, uma nova janela será exibida. Anote a **ID do aplicativo** e o valor da chave de **recurso de fala** a partir dela. Você precisará desses dois valores para poder acessar o aplicativo de fora do Speech Studio.

Como alternativa, você também pode obter esses valores selecionando **configurações**  >  seção **geral** .

### <a name="access-application-from-client"></a>Acessar aplicativo do cliente

No escopo deste artigo, usaremos o cliente do assistente de voz do Windows que você baixou como parte dos pré-requisitos. Descompacte a pasta.
1. Iniciar **VoiceAssistantClient.exe**.
1. Crie um novo perfil de publicação e insira o valor para o **perfil de conexão**. Na seção **configurações gerais** , insira valores **chave de assinatura** (é igual ao valor da **chave de recurso de fala** que você salvou ao publicar o aplicativo), região de chave de **assinatura** e ID do **aplicativo de comandos personalizados**.
    > [!div class="mx-imgBorder"]
    > ![Captura de tela que realça a seção de configurações gerais para criar um perfil WVAC.](media/custom-commands/create-profile.png)
1. Selecione **salvar e aplicar perfil**.
1. Agora experimente as seguintes entradas por meio de fala/texto
    > [!div class="mx-imgBorder"]
    > ![WVAC criar perfil](media/custom-commands/conversation.png)


> [!TIP]
> Você pode clicar em entradas no **log de atividades** para inspecionar as respostas brutas que estão sendo enviadas do serviço de comandos personalizados.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você usou um aplicativo existente. Em seguida, nas [seções de instruções](./how-to-develop-custom-commands-application.md), você aprende a projetar, desenvolver, depurar, testar e integrar um aplicativo de comandos personalizados do zero.