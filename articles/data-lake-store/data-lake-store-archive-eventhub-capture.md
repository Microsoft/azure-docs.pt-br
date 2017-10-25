---
title: Capturar dados de Hubs de Eventos no Azure Data Lake Store | Microsoft Docs
description: Use o Azure Data Lake Store Use para capturar dados de Hubs de Eventos
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/28/2017
ms.author: nitinme
ms.openlocfilehash: a9e69576958ae96d22a4eb03d0df429f0b307298
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-data-lake-store-to-capture-data-from-event-hubs"></a>Use o Azure Data Lake Store Use para capturar dados de Hubs de Eventos

Saiba como usar o Azure Data Lake Store para capturar dados recebidos pelos Hubs de Eventos do Azure.

## <a name="prerequisites"></a>Pré-requisitos

* **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Uma conta do repositório Azure Data Lake**. Para obter instruções sobre como criar uma, consulte [Introdução ao Azure Data Lake Store](data-lake-store-get-started-portal.md).

*  **Um namespace dos Hubs de Eventos**. Para obter instruções, confira [Criar um namespace dos Hubs de Eventos](../event-hubs/event-hubs-create.md#create-an-event-hubs-namespace). Verifique se a conta do Data Lake Store e o namespace dos Hubs de Eventos estão na mesma assinatura do Azure.


## <a name="assign-permissions-to-event-hubs"></a>Atribuir permissões aos Hubs de Eventos

Nesta seção, você criará uma pasta dentro da conta na qual você deseja capturar os dados dos Hubs de Eventos. Você também atribui permissões aos Hubs de Eventos para que eles possam gravar dados em uma conta do Data Lake Store. 

1. Abra a conta do Data Lake Store onde você deseja capturar dados dos Hubs de Eventos e, em seguida, clique em **Data Explorer**.

    ![Data explorer do Data Lake Store](./media/data-lake-store-archive-eventhub-capture/data-lake-store-open-data-explorer.png "Data explorer do Data Lake Store")

2.  Clique em **Nova Pasta** e digite um nome para a pasta onde você deseja capturar os dados.

    ![Criar uma nova pasta no Data Lake Store](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-new-folder.png "Criar uma nova pasta no Data Lake Store")

3. Atribua permissões na raiz do Data Lake Store. 

    a. Clique em **Data Explorer**, selecione a raiz da conta do Data Lake Store e, em seguida, clique em **Acesso**.

    ![Atribuir permissões para a raiz do Data Lake Store](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-root.png "Atribuir permissões para a raiz do Data Lake Store")

    b. Em **Acesso**, clique em **Adicionar**, clique em **Selecionar Usuário ou Grupo** e procure por `Microsoft.EventHubs`. 

    ![Atribuir permissões para a raiz do Data Lake Store](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "Atribuir permissões para a raiz do Data Lake Store")
    
    Clique em **Selecionar**.

    c. Em **Atribuir Permissões**, clique em **Selecionar Permissões**. Defina **Permissões** como **Executar**. Defina **Adicionar a** como **Esta pasta e todas as filhas**. Defina **Adicionar como** como **Uma entrada de permissão de acesso e uma entrada de permissão predefinida**.

    ![Atribuir permissões para a raiz do Data Lake Store](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp1.png "Atribuir permissões para a raiz do Data Lake Store")

    Clique em **OK**.

4. Atribua permissões para a pasta na conta do Data Lake Store onde você deseja capturar dados.

    a. Clique em **Data Explorer**, selecione a pasta na conta do Data Lake Store e, em seguida, clique em **Acesso**.

    ![Atribuir permissões para a pasta do Data Lake Store](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-folder.png "Atribuir permissões para a pasta do Data Lake Store")

    b. Em **Acesso**, clique em **Adicionar**, clique em **Selecionar Usuário ou Grupo** e procure por `Microsoft.EventHubs`. 

    ![Atribuir permissões para a pasta do Data Lake Store](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "Atribuir permissões para a pasta do Data Lake Store")
    
    Clique em **Selecionar**.

    c. Em **Atribuir Permissões**, clique em **Selecionar Permissões**. Defina **Permissões** como **Ler, Gravar** e **Executar**. Defina **Adicionar a** como **Esta pasta e todas as filhas**. Por fim, defina **Adicionar como** como **Uma entrada de permissão de acesso e uma entrada de permissão predefinida**.

    ![Atribuir permissões para a pasta do Data Lake Store](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp-folder.png "Atribuir permissões para a pasta do Data Lake Store")
    
    Clique em **OK**. 

## <a name="configure-event-hubs-to-capture-data-to-data-lake-store"></a>Configurar os Hubs de Eventos para capturar dados no Data Lake Store

Nesta seção, você criará um Hub de Eventos dentro de um namespace de Hubs de Eventos. Também configurará o Hub de Eventos para capturar os dados em uma conta do Azure Data Lake Store. Esta seção pressupõe que você já criou um namespace dos Hubs de Eventos.

2. No painel **Visão Geral** do namespace de Hubs de Eventos, clique em **+ Hub de Eventos**.

    ![Criar Hub de Eventos](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-event-hub.png "Criar Hub de Eventos")

3. Forneça os valores a seguir para configurar os Hubs de Eventos a fim de capturar dados no Data Lake Store.

    ![Criar Hub de Eventos](./media/data-lake-store-archive-eventhub-capture/data-lake-store-configure-eventhub.png "Criar Hub de Eventos")

    a. Forneça um nome para o Hub de Eventos.
    
    b. Para este tutorial, defina **Contagem de Partições** e **Retenção de Mensagem** com os valores padrão.
    
    c. Defina **Capturar** como **Ativado**. Defina a **Janela de Tempo** (a frequência de captura) e **Janela de Tamanho** (tamanho dos dados para captura). 
    
    d. Para **Provedor de Captura**, selecione **Azure Data Lake Store** e selecione o Data Lake Store criado anteriormente. Para **Caminho do Data Lake**, digite o nome da pasta que você criou na conta do Data Lake Store. Você só precisa fornecer o caminho relativo para a pasta.

    e. Deixe os **Formatos de nome de arquivo de captura de exemplo** como o valor padrão. Essa opção controla a estrutura de pastas criada sob a pasta de captura.

    f. Clique em **Criar**.

## <a name="test-the-setup"></a>Testar a configuração

Agora, você pode testar a solução enviando dados para o Hub de Eventos do Azure. Siga as instruções em [Enviar eventos aos Hubs de Eventos do Azure](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md). Quando você começar a enviar os dados, verá os dados refletidos no Data Lake Store usando a estrutura de pastas que você especificou. Por exemplo, você verá uma estrutura de pastas, conforme mostra a seguinte captura de tela, em seu Data Lake Store.

![Exemplo de dados do Hub de Eventos no Data Lake Store](./media/data-lake-store-archive-eventhub-capture/data-lake-store-eventhub-data-sample.png "Exemplo de dados do Hub de Eventos no Data Lake Store")

> [!NOTE]
> Mesmo se não houver mensagens chegando aos Hubs de Eventos, os Hubs de Eventos gravarão os arquivos vazios apenas com os cabeçalhos na conta do Data Lake Store. Os arquivos são gravados no mesmo intervalo de tempo fornecido durante a criação dos Hubs de Eventos.
> 
>

## <a name="analyze-data-in-data-lake-store"></a>Analisar dados no Repositório Data Lake

Quando os dados estiverem no Data Lake Store, você poderá executar trabalhos de análise para processar e juntar os dados. Confira [Exemplo de USQL Avro](https://github.com/Azure/usql/tree/master/Examples/AvroExamples) sobre como fazer isso usando o Azure Data Lake Analytics.
  

## <a name="see-also"></a>Consulte também
* [Proteger dados no Repositório Data Lake](data-lake-store-secure-data.md)
* [Copiar dados de Blobs do Armazenamento do Azure para o Repositório Data Lake](data-lake-store-copy-data-azure-storage-blob.md)
