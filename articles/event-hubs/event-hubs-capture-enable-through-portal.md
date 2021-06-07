---
title: Hubs de Eventos – Capturar eventos de streaming usando o portal do Azure
description: Este artigo descreve como habilitar a captura de eventos de streaming por meio de Hubs de Eventos do Azure usando o portal do Azure.
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: 8c89a6e56c2d0bc7b7ff09d5fd63737009239eb9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100653944"
---
# <a name="enable-capturing-of-events-streaming-through-azure-event-hubs"></a>Habilitar a captura de eventos de streaming por meio de Hubs de Eventos do Azure

A [Captura de Hubs de Eventos][capture-overview] do Azure permite o fornecimento automático dos dados de streaming nos Hubs de Eventos para uma conta do [Armazenamento de Blobs do Azure](https://azure.microsoft.com/services/storage/blobs/) ou do [Azure Data Lake Storage Gen1 ou Gen2](https://azure.microsoft.com/services/data-lake-store/) escolhida.

Você pode configurar Captura na hora da criação do hub de eventos usando o [portal do Azure](https://portal.azure.com). Você pode capturar os dados em um contêiner do [Armazenamento de Blobs](https://azure.microsoft.com/services/storage/blobs/) do Azure ou em uma conta do [Azure Data Lake Storage Gen1 ou Gen2](https://azure.microsoft.com/services/data-lake-store/).

Para saber mais, confira [Visão geral da captura dos Hubs de Eventos][capture-overview].

> [!IMPORTANT]
> A conta de armazenamento de destino (Armazenamento do Azure ou Azure Data Lake Storage) precisa estar na mesma assinatura do hub de eventos.

## <a name="capture-data-to-azure-storage"></a>Capturar dados no Armazenamento do Azure

Quando você cria um hub de eventos, pode habilitar a Captura clicando no botão **Ativado** na tela **Criar Hub de Eventos** do portal. Em seguida, especifique uma Conta de Armazenamento e um contêiner clicando em **Armazenamento do Azure** na caixa **Provedor da Captura**. Já que a Captura dos Hubs de Eventos usa a autenticação de serviços com o armazenamento, você não precisa especificar uma cadeia de conexão de armazenamento. O seletor de recurso seleciona automaticamente o URI do recurso para sua conta de armazenamento. Se você usar o Azure Resource Manager, deverá fornecer esse URI explicitamente como uma cadeia de caracteres.

A janela de tempo padrão é de 5 minutos. O valor mínimo é 1, o máximo é 15. A janela **Tamanho** tem um intervalo de 10 a 500 MB.

![Janela de tempo para captura][1]

> [!NOTE]
> Você pode habilitar ou desabilitar a emissão de arquivos vazios quando nenhum evento ocorre durante a janela de Captura. 

## <a name="capture-data-to-azure-data-lake-storage-gen-2"></a>Capturar dados no Azure Data Lake Storage Gen2 

1. Siga o artigo [Criar uma conta de armazenamento](../storage/common/storage-account-create.md?tabs=azure-portal#create-a-storage-account) para criar uma conta do Armazenamento do Azure. Defina **Namespace hierárquico** como **Habilitado** na guia **Avançado** para torná-lo uma conta do Azure Data Lake Storage Gen2.
2. Ao criar um hub de eventos, execute as seguintes etapas: 

    1. Selecione **Ativada** em **Captura**. 
    2. Selecione **Armazenamento do Azure** como o provedor de captura. A opção **Azure Data Lake Store** vista para o **Provedor de captura** refere-se à Gen1 do Azure Data Lake Storage. Para usar uma Gen2 do Azure Data Lake Storage, selecione **Armazenamento do Azure**.
    2. Escolha o botão **Selecionar Contêiner**. 

        ![Habilitar a captura no Data Lake Storage Gen2](./media/event-hubs-capture-enable-through-portal/data-lake-storage-gen2.png)
3. Escolha a conta do **Azure Data Lake Storage Gen2** na lista. 

    ![Selecionar o Data Lake Storage Gen2](./media/event-hubs-capture-enable-through-portal/select-data-lake-storage-gen2.png)
4. Selecione o **contêiner** (sistema de arquivos no Data Lake Storage Gen2).

    ![Selecionar o sistema de arquivos no armazenamento](./media/event-hubs-capture-enable-through-portal/select-file-system-data-lake-storage.png)
5. Na página **Criar Hub de Eventos**, selecione **Criar**. 

    ![Selecionar o botão Criar](./media/event-hubs-capture-enable-through-portal/create-event-hub-data-lake-storage.png)

    > [!NOTE]
    > O contêiner criado em um Azure Data Lake Storage Gen2 com esta interface do usuário é mostrado em **Sistemas de arquivos** em **Gerenciador de Armazenamento**. Da mesma forma, o sistema de arquivos criado em uma conta do Data Lake Storage Gen2 é exibido como um contêiner nesta interface do usuário. 


## <a name="capture-data-to-azure-data-lake-storage-gen-1"></a>Capturar dados no Azure Data Lake Storage Gen1 

Para capturar dados no Azure Data Lake Storage Gen1, crie uma conta do Data Lake Storage Gen1 e um hub de eventos:

### <a name="create-an-azure-data-lake-storage-gen-1-account-and-folders"></a>Criar uma conta e pastas do Azure Data Lake Storage Gen1

1. Crie uma conta do Data Lake Storage seguindo as instruções descritas em [Introdução ao Azure Data Lake Storage Gen1 usando o portal do Azure](../data-lake-store/data-lake-store-get-started-portal.md).
2. Siga as instruções na seção [Atribuir permissões aos Hubs de Eventos](../data-lake-store/data-lake-store-archive-eventhub-capture.md#assign-permissions-to-event-hubs) para criar uma pasta dentro da conta do Data Lake Storage Gen1 na qual deseja capturar os dados dos Hubs de Eventos e atribuir permissões aos Hubs de Eventos para que eles possam gravar dados na sua conta do Data Lake Storage Gen1.  


### <a name="create-an-event-hub"></a>Criar um hub de eventos

1. O hub de eventos precisa estar na mesma assinatura do Azure que a conta do Azure Data Lake Storage Gen1 criada. Crie o hub de eventos clicando no botão **Ativado** em **Capturar** na página **Criar Hub de Eventos** do portal. 
2. Na página **Criar Hub de Eventos** do portal, selecione **Azure Data Lake Store** na caixa **Provedor da Captura**.
3. Em **Selecionar Repositório** ao lado da lista suspensa **Data Lake Store**, especifique a conta do Data Lake Storage Gen1 criada anteriormente e, no campo **Caminho do Data Lake**, insira o caminho para a pasta de dados criada.

    ![Selecionar conta do Data Lake Storage][3]


## <a name="add-or-configure-capture-on-an-existing-event-hub"></a>Adicionar ou configurar a Captura em um hub de eventos existente

Você pode configurar a Captura em hubs de eventos existentes que estejam em namespaces dos Hubs de Eventos. Para habilitar a Captura em um hub de eventos existente ou alterar as configurações da Captura, clique no namespace para carregar a tela de visão geral e, em seguida, clique no hub de eventos para o qual você deseja habilitar ou alterar a configuração da Captura. Por fim, clique na opção **Captura** do lado esquerdo da página aberta e edite as configurações, conforme exibido nas figuras abaixo:

### <a name="azure-blob-storage"></a>Armazenamento do Blobs do Azure

![Configurar Armazenamento de Blobs do Azure][2]

### <a name="azure-data-lake-storage-gen-2"></a>Azure Data Lake Storage Gen 2

![Configurar o Azure Data Lake Storage Gen2](./media/event-hubs-capture-enable-through-portal/configure-data-lake-storage-gen2.png)

### <a name="azure-data-lake-storage-gen-1"></a>Azure Data Lake Storage Gen 1 

![Configurar o Azure Data Lake Storage][4]

[1]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture1.png
[2]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture2.png
[3]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture3.png
[4]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture4.png

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre a captura de Hubs de Eventos lendo a [visão geral da Captura de Hubs de Eventos][capture-overview].
- Você também pode configurar a Captura dos Hubs de Eventos usando modelos do Azure Resource Manager. Para saber mais, confira [Habilitar Captura usando um modelo do Azure Resource Manager](event-hubs-resource-manager-namespace-event-hub-enable-capture.md).
- [Saiba como criar uma assinatura da Grade de Eventos do Azure com um namespace de Hubs de Eventos como sua fonte](store-captured-data-data-warehouse.md)
- [Introdução ao Azure Data Lake Store usando o portal do Azure](../data-lake-store/data-lake-store-get-started-portal.md)

[capture-overview]: event-hubs-capture-overview.md
