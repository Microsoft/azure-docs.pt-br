---
title: Capturar dados dos hubs de eventos para Azure Data Lake Storage Gen1
description: Saiba como usar o Azure Data Lake Storage Gen1 para capturar dados recebidos pelos Hubs de Eventos do Azure. Comece verificando os pré-requisitos.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 8be242369ecae2c809a38428284c9ddcad440e3f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91578233"
---
# <a name="use-azure-data-lake-storage-gen1-to-capture-data-from-event-hubs"></a>Use o Azure Data Lake Storage Gen1 para capturar dados dos Hubs de Eventos

Saiba como usar o Azure Data Lake Storage Gen1 para capturar dados recebidos pelos Hubs de Eventos do Azure.

## <a name="prerequisites"></a>Pré-requisitos

* **Uma assinatura do Azure**. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Uma conta do Azure Data Lake Storage Gen1**. Para obter instruções de como criar uma, confira [Introdução ao Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md).

*  **Um namespace de hubs de eventos**. Para obter instruções, confira [Criar um namespace dos Hubs de Eventos](../event-hubs/event-hubs-create.md#create-an-event-hubs-namespace). Verifique se a conta do Azure Data Lake Storage Gen1 e o namespace dos Hubs de Eventos estão na mesma assinatura do Azure.


## <a name="assign-permissions-to-event-hubs"></a>Atribuir permissões aos Hubs de Eventos

Nesta seção, você criará uma pasta dentro da conta na qual você deseja capturar os dados dos Hubs de Eventos. Você também atribui permissões aos Hubs de Eventos para que eles possam gravar dados em uma conta do Azure Data Lake Storage Gen1. 

1. Abra a conta do Azure Data Lake Storage Gen1 em que você deseja capturar dados dos Hubs de Eventos e, em seguida, clique em **Data Explorer**.

    ![Data Lake Storage Gen1 data Explorer](./media/data-lake-store-archive-eventhub-capture/data-lake-store-open-data-explorer.png "Data Lake Storage Gen1 data Explorer")

1.  Clique em **Nova Pasta** e digite um nome para a pasta onde você deseja capturar os dados.

    ![Criar uma nova pasta no Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-new-folder.png "Criar uma nova pasta no Data Lake Storage Gen1")

1. Atribua permissões na raiz do Data Lake Storage Gen1. 

    a. Clique em **Data Explorer**, selecione a raiz da conta do Data Lake Storage Gen1 e, em seguida, clique em **Acesso**.

    ![Captura de tela do data Explorer com a raiz da conta e a opção de acesso chamada out.](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-root.png "Atribuir permissões para a raiz de Data Lake Storage Gen1")

    b. Em **Acesso**, clique em **Adicionar**, clique em **Selecionar Usuário ou Grupo** e procure por `Microsoft.EventHubs`. 

    ![Captura de tela da página de acesso com a opção Adicionar, selecione a opção usuário ou grupo e a opção Microsoft Eventhubs chamada out.](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "Atribuir permissões para a raiz de Data Lake Storage Gen1")
    
    Clique em **Selecionar**.

    c. Em **Atribuir Permissões**, clique em **Selecionar Permissões**. Defina **Permissões** como **Executar**. Defina **Adicionar a** como **Esta pasta e todas as filhas**. Defina **Adicionar como** como **Uma entrada de permissão de acesso e uma entrada de permissão predefinida**.

    > [!IMPORTANT]
    > Ao criar uma nova hierarquia de pastas para capturar dados recebidos pelos Hubs de Eventos do Azure, essa é uma maneira fácil de garantir o acesso à pasta de destino.  No entanto, adicionar permissões a todos os elementos filhos de uma pasta de nível superior com muitos arquivos e pastas secundários pode levar um longo tempo.  Se a pasta raiz contiver um grande número de arquivos e pastas, talvez seja mais rápido adicionar permissões **Executar** para `Microsoft.EventHubs`individualmente em cada pasta no caminho para a pasta de destino final. 

    ![Captura de tela da seção atribuir permissões com a opção Selecionar permissões chamada out. A seção selecionar permissões é próxima a ela com a opção Executar, adicionar à opção e adicionar como opção chamada out.](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp1.png "Atribuir permissões para a raiz de Data Lake Storage Gen1")

    Clique em **OK**.

1. Atribua permissões para a pasta na conta do Data Lake Storage Gen1 em que você deseja capturar dados.

    a. Clique em **Data Explorer**, selecione a pasta na conta do Data Lake Storage Gen1 e, em seguida, clique em **Acesso**.

    ![Captura de tela do data Explorer com uma pasta na conta e a opção de acesso chamada out.](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-folder.png "Atribuir permissões para a pasta Data Lake Storage Gen1")

    b. Em **Acesso**, clique em **Adicionar**, clique em **Selecionar Usuário ou Grupo** e procure por `Microsoft.EventHubs`. 

    ![Captura de tela da página de acesso do data Explorer com a opção Adicionar, selecione a opção usuário ou grupo e a opção Microsoft Eventhubs chamada out.](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "Atribuir permissões para a pasta Data Lake Storage Gen1")
    
    Clique em **Selecionar**.

    c. Em **Atribuir Permissões**, clique em **Selecionar Permissões**. Defina **Permissões** como **Ler, Gravar** e **Executar**. Defina **Adicionar a** como **Esta pasta e todas as filhas**. Por fim, defina **Adicionar como** como **Uma entrada de permissão de acesso e uma entrada de permissão predefinida**.

    ![Captura de tela da seção atribuir permissões com a opção Selecionar permissões chamada out. A seção selecionar permissões é próxima a ela com as opções ler, gravar e executar, a opção Adicionar a e a opção Adicionar como chamada out.](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp-folder.png "Atribuir permissões para a pasta Data Lake Storage Gen1")
    
    Clique em **OK**. 

## <a name="configure-event-hubs-to-capture-data-to-data-lake-storage-gen1"></a>Configurar os Hubs de Eventos para capturar dados no Data Lake Storage Gen1

Nesta seção, você criará um Hub de Eventos dentro de um namespace de Hubs de Eventos. Também configurará o Hub de Eventos para capturar os dados em uma conta do Azure Data Lake Storage Gen1. Esta seção pressupõe que você já criou um namespace dos Hubs de Eventos.

1. No painel **Visão Geral** do namespace de Hubs de Eventos, clique em **+ Hub de Eventos**.

    ![Captura de tela do painel Visão geral com a opção de Hub de eventos chamada out.](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-event-hub.png "Criar Hub de Eventos")

1. Forneça os valores a seguir para configurar os Hubs de Eventos a fim de capturar dados no Data Lake Storage Gen1.

    ![Captura de tela da caixa de diálogo Criar Hub de eventos com a caixa de texto nome, a opção de captura, a opção provedor de captura, a opção Selecionar Data Lake Store e a opção Data Lake caminho chamada out.](./media/data-lake-store-archive-eventhub-capture/data-lake-store-configure-eventhub.png "Criar Hub de Eventos")

    a. Forneça um nome para o Hub de Eventos.
    
    b. Para este tutorial, defina **Contagem de Partições** e **Retenção de Mensagem** com os valores padrão.
    
    c. Defina **Capturar** como **Ativado**. Defina a **Janela de Tempo** (a frequência de captura) e **Janela de Tamanho** (tamanho dos dados para captura). 
    
    d. Para **Provedor de Captura**, selecione **Azure Data Lake Store** e selecione a conta do Data Lake Storage Gen1 criada anteriormente. Para **Caminho do Data Lake**, digite o nome da pasta que você criou na conta do Data Lake Storage Gen1. Você só precisa fornecer o caminho relativo para a pasta.

    e. Deixe os **Formatos de nome de arquivo de captura de exemplo** como o valor padrão. Essa opção controla a estrutura de pastas criada sob a pasta de captura.

    f. Clique em **Criar**.

## <a name="test-the-setup"></a>Testar a configuração

Agora, você pode testar a solução enviando dados para o Hub de Eventos do Azure. Siga as instruções em [Enviar eventos aos Hubs de Eventos do Azure](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md). Quando você começar a enviar os dados, verá os dados refletidos no Data Lake Storage Gen1 usando a estrutura de pastas que você especificou. Por exemplo, você verá uma estrutura de pastas, conforme mostra a seguinte captura de tela, em seu Data Lake Storage Gen1.

![Exemplo de dados do EventHub no Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-eventhub-data-sample.png "Exemplo de dados do EventHub no Data Lake Storage Gen1")

> [!NOTE]
> Mesmo se não houver mensagens chegando aos Hubs de Eventos, os Hubs de Eventos gravarão os arquivos vazios apenas com os cabeçalhos na conta do Data Lake Storage Gen1. Os arquivos são gravados no mesmo intervalo de tempo fornecido durante a criação dos Hubs de Eventos.
> 
>

## <a name="analyze-data-in-data-lake-storage-gen1"></a>Analisar dados no Data Lake Storage Gen1

Quando os dados estiverem no Data Lake Storage Gen1, você poderá executar trabalhos de análise para processar e juntar os dados. Confira [Exemplo de USQL Avro](https://github.com/Azure/usql/tree/master/Examples/AvroExamples) sobre como fazer isso usando o Azure Data Lake Analytics.
  

## <a name="see-also"></a>Veja também
* [Proteger dados no Armazenamento do Data Lake Gen1](data-lake-store-secure-data.md)
* [Copiar dados do Azure Storage Blobs para o Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
