---
title: Carregar arquivos em uma conta dos Serviços de Mídia do Azure usando o Aspera | Microsoft Docs
description: Este tutorial orienta você pelas etapas de carregamento de arquivos em uma conta de armazenamento que está associada a uma conta dos Serviços de Mídia usando o serviço **Servidor Aspera sob demanda** no Azure.
services: media-services
documentationcenter: ''
author: johndeu
manager: femila
editor: ''
ms.assetid: 8812623a-b425-4a0f-9e05-0ee6c839b6f9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: e522883da7fddad44741599107f2dbc4c99aace6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60826820"
---
# <a name="upload-files-into-a-media-services-account-using-the-aspera-server-on-demand-service-on-azure"></a>Carregar arquivos em uma conta de Serviços de Mídia usando o serviço Aspera Server On-Demand no Azure 

## <a name="overview"></a>Visão geral

**Aspera** é um software de transferência de arquivos de alta velocidade. O **Servidor Aspera sob demanda** para o Azure permite um carregamento de alta velocidade e download de arquivos grandes diretamente no armazenamento do objeto de Blob do Azure. Para obter informações sobre o **Aspera sob demanda**, confira o site [Nuvem do Aspera](http://cloud.asperasoft.com/). 
  
O **Servidor Aspera sob demanda** para Azure está disponível para compra no [Azure marketplace](https://azure.microsoft.com/marketplace/). Para concluir uma compra do **Servidor Aspera sob demanda** no Azure, faça logon no Azure Marketplace com sua Windows Live ID.

Este tutorial orienta você pelas etapas de carregamento de arquivos em uma conta de armazenamento que está associada a uma conta dos Serviços de Mídia usando o serviço **Aspera Server On-Demand** no Azure. 

Você pode encontrar um exemplo que mostra como usar as funções do Azure com o Aspera e os Serviços de Mídia [aqui](https://github.com/Azure-Samples/media-services-dotnet-functions-integration/tree/master/103-aspera-ingest).

>[!NOTE]
>Há um limite para o tamanho máximo do arquivo suportado para processamento com processadores de mídia (MPs) dos Serviços de Mídia do Azure. Confira [este](media-services-quotas-and-limitations.md) artigo para obter detalhes sobre a limitação de tamanho do arquivo.
>

## <a name="prerequisites"></a>Pré-requisitos 

Para concluir este tutorial, você precisará:

* Uma ID do Windows Live
* Uma [conta do Azure](https://azure.microsoft.com). Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). 
* Uma [conta de Serviços de Mídia do Azure](media-services-portal-create-account.md).

## <a name="purchase-aspera-on-demand-for-azure"></a>Comprar Aspera sob demanda para Azure

Depois que você entrar no Azure Marketplace, siga estas etapas básicas para concluir a compra do Aspera sob demanda para Azure.

1. Pesquise por Aspera e selecione 'Servidor sob demanda'.

   ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera001.png)

2. Analise os planos de assinatura e clique em 'Inscrever'

   ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera002.png)

3. Preencha com as especificidades da sua assinatura do Servidor Sob Demanda.

   ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera003.png)

4. Clique no **Tipo de preço** e selecione o volume mensal desejado no subpainel. No painel **Planejar detalhes**, selecione **OK**. No painel **Escolha seu Tipo de preço**, clique em **Selecionar**.

   ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera004.png)

5. Clique em **Termos legais** para exibir e aceitar os termos legais no subpainel. Depois de rever os termos legais, clique em **Comprar**.

   ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera005.png)

6. Conclua a compra ao clicar em **Criar**.

   ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera006.png)

7. O painel do Azure anunciará que ele está provisionando o serviço.  Depois de concluído com o provisionamento, você pode encontrar a nova assinatura pesquisando em seus recursos pelo nome do serviço. Após encontrar o serviço, clique duas vezes nele para iniciar o portal de gerenciamento de serviço.

   ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera007.png)

8. Inicie o portal de gerenciamento do Aspera. Depois de localizar seu novo serviço do Aspera, você pode obter acesso ao portal de gerenciamento clicando no serviço.  Um novo painel será iniciado. De dentro desse novo painel, clique no **Nome do Recurso** de seu novo serviço.  Na captura de tela a seguir, o nome do recurso é 'AsperaTransferDemo'. Após clicar no nome do recurso, outro painel será iniciado. Nesse painel recém-lançado, você verá um link 'Gerenciar'. Clique no link 'Gerenciar' para iniciar o portal de gerenciamento Aspera.

   ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera008.png)

9. Ao clicar no link Gerenciar, você obterá a página de registro, que é necessária para acessar o serviço.

   ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera009.png)

10. Neste ponto, você deve ter acesso ao portal de gerenciamento de serviço do Aspera, onde você pode criar chaves de acesso, baixar as licenças e os clientes Aspera, exibir o uso e saber mais sobre as APIs.

    A captura de tela a seguir mostra a criação do acesso. 

    ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera010.png)

    A captura de tela a seguir mostra o uso de interfaces de relatório no portal. 

    ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera011.png)

## <a name="upload-files-with-aspera"></a>Carregar arquivos com o Aspera

1. Baixar e instalar o software cliente do Aspera:
    
    * [Plug-in de navegador](https://downloads.asperasoft.com/connect2/)
    * [Cliente avançado](https://downloads.asperasoft.com/en/downloads/2)

2. Faça sua primeira transferência. Para usar o cliente Aspera para transferir com o serviço de transferência do Aspera, você precisa concluir o seguinte: 

   1. Crie uma chave de acesso, usando o portal do Aspera.  
   2. Baixar, instalar e licenciar o cliente Aspera (o software pode ser encontrado no portal do Aspera).  

      >[!NOTE]
      >Leia o guia de cliente Aspera para obter informações de configuração.
    
   3. Recupere algumas informações da sua conta de armazenamento que está associada à sua Conta de Mídia do Azure usando o [portal do Azure](https://portal.azure.com/). Especificamente, nome e chave, e o nome do contêiner do blob de armazenamento ao qual você deseja inserir o conteúdo. 

       * Para obter as informações de armazenamento do portal: localize sua conta de armazenamento, clique nas Teclas de Acesso e copie o nome e a chave de sua conta.
       * Para obter o nome de contêiner: encontre sua conta de armazenamento, selecione **Blobs**, selecione o nome do contêiner para carregar o conteúdo. 

      Abaixo está a captura de tela do cliente Aspera **Gerenciador de Conexão** onde você deve especificar o tipo de armazenamento e credenciais 'Azure', bem como o contêiner de blob.

      ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera012.png)

## <a name="resources"></a>Recursos

Os seguintes recursos foram mencionados neste artigo. 

* [Plug-in Connect Browser](https://downloads.asperasoft.com/connect2/)
* [Guia do Connect](https://downloads.asperasoft.com/en/documentation/8)
* [Aspera Client](https://downloads.asperasoft.com/en/downloads/2)
* [Guia do Client](https://downloads.asperasoft.com/en/documentation/2)

## <a name="next-steps"></a>Próximas etapas

Agora você pode [copiar blobs de uma conta de armazenamento para uma conta do AMS](media-services-copying-existing-blob.md#copy-blobs-from-a-storage-account-into-an-ams-account).

## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

