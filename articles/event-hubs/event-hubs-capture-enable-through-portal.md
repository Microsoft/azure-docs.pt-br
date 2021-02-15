---
title: Hubs de eventos-capturar eventos de streaming usando portal do Azure
description: Este artigo descreve como habilitar a captura de eventos de streaming por meio de Hubs de Eventos do Azure usando o portal do Azure.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 2381bfa627d00a78ed91af0ba81579588ee016ce
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91613570"
---
# <a name="enable-capturing-of-events-streaming-through-azure-event-hubs"></a>Habilitar a captura de eventos de streaming por meio de Hubs de Eventos do Azure

A [captura de hubs de eventos][capture-overview] do Azure permite que você forneça automaticamente os dados de streaming em hubs de eventos para um [armazenamento de BLOBs do Azure](https://azure.microsoft.com/services/storage/blobs/) ou uma conta de [Azure data Lake Storage Gen1 ou Gen 2](https://azure.microsoft.com/services/data-lake-store/) de sua escolha.

Você pode configurar Captura na hora da criação do hub de eventos usando o [portal do Azure](https://portal.azure.com). Você pode capturar os dados para um contêiner de [armazenamento de BLOBs](https://azure.microsoft.com/services/storage/blobs/) do Azure ou para uma conta [Azure data Lake Storage Gen 1 ou Gen 2](https://azure.microsoft.com/services/data-lake-store/) .

Para saber mais, confira [Visão geral da captura dos Hubs de Eventos][capture-overview].

> [!IMPORTANT]
> A conta de armazenamento de destino (armazenamento do Azure ou Azure Data Lake Storage) deve estar na mesma assinatura que o Hub de eventos.

## <a name="capture-data-to-azure-storage"></a>Capturar dados para o armazenamento do Azure

Quando você cria um hub de eventos, pode habilitar a Captura clicando no botão **Ativado** na tela **Criar Hub de Eventos** do portal. Em seguida, especifique uma Conta de Armazenamento e um contêiner clicando em **Armazenamento do Azure** na caixa **Provedor da Captura**. Já que a Captura dos Hubs de Eventos usa a autenticação de serviços com o armazenamento, você não precisa especificar uma cadeia de conexão de armazenamento. O seletor de recurso seleciona automaticamente o URI do recurso para sua conta de armazenamento. Se você usar o Azure Resource Manager, deverá fornecer esse URI explicitamente como uma cadeia de caracteres.

A janela de tempo padrão é de 5 minutos. O valor mínimo é 1, o máximo é 15. A janela **Tamanho** tem um intervalo de 10 a 500 MB.

![Janela de tempo para captura][1]

> [!NOTE]
> Você pode habilitar ou desabilitar a emissão de arquivos vazios quando nenhum evento ocorre durante a janela de Captura. 

## <a name="capture-data-to-azure-data-lake-storage-gen-2"></a>Capturar dados para Azure Data Lake Storage Gen 2 

1. Siga [o artigo criar uma conta de armazenamento](../storage/common/storage-account-create.md?tabs=azure-portal#create-a-storage-account) para criar uma conta de armazenamento do Azure. Defina o **namespace hierárquico** como **habilitado** na guia **avançado** para torná-lo uma conta Azure data Lake Storage Gen 2.
2. Ao criar um hub de eventos, execute as seguintes etapas: 

    1. Selecione **ativado** para **captura**. 
    2. Selecione **armazenamento do Azure** como o provedor de captura. A opção de **Azure data Lake Store** que você vê para o **provedor de captura** é para a Gen 1 de Azure data Lake Storage. Para usar uma Gen 2 de Azure Data Lake Storage, selecione **armazenamento do Azure**.
    2. Selecione o botão **selecionar contêiner** . 

        ![Habilitar a captura para o Data Lake Storage Gen 2](./media/event-hubs-capture-enable-through-portal/data-lake-storage-gen2.png)
3. Selecione a conta **Azure data Lake Storage Gen 2** na lista. 

    ![Selecione Data Lake Storage Gen 2](./media/event-hubs-capture-enable-through-portal/select-data-lake-storage-gen2.png)
4. Selecione o **contêiner** (sistema de arquivos no data Lake Storage Gen 2).

    ![Selecionar sistema de arquivos no armazenamento](./media/event-hubs-capture-enable-through-portal/select-file-system-data-lake-storage.png)
5. Na página **criar Hub de eventos** , selecione **criar**. 

    ![Selecione o botão criar](./media/event-hubs-capture-enable-through-portal/create-event-hub-data-lake-storage.png)

    > [!NOTE]
    > O contêiner que você cria em um Azure Data Lake Storage Gen 2 usando essa interface do usuário (IU) é mostrado em **sistemas de arquivos** em **Gerenciador de armazenamento**. Da mesma forma, o sistema de arquivos que você cria em uma conta Data Lake Storage Gen 2 aparece como um contêiner nesta interface do usuário. 


## <a name="capture-data-to-azure-data-lake-storage-gen-1"></a>Capturar dados para Azure Data Lake Storage Gen 1 

Para capturar dados para Azure Data Lake Storage Gen 1, crie uma conta do Data Lake Storage Gen 1 e um hub de eventos:

### <a name="create-an-azure-data-lake-storage-gen-1-account-and-folders"></a>Criar uma conta e pastas do Azure Data Lake Storage Gen 1

1. Crie uma conta de Data Lake Storage, seguindo as instruções em introdução [ao Azure data Lake Storage Gen 1 usando o portal do Azure](../data-lake-store/data-lake-store-get-started-portal.md).
2. Siga as instruções na seção [atribuir permissões a hubs de eventos](../data-lake-store/data-lake-store-archive-eventhub-capture.md#assign-permissions-to-event-hubs) para criar uma pasta dentro da conta data Lake Storage Gen 1 na qual você deseja capturar os dados dos hubs de eventos e atribuir permissões a hubs de eventos para que ele possa gravar dados em sua conta do data Lake Storage Gen 1.  


### <a name="create-an-event-hub"></a>Criar um Hub de Evento

1. O Hub de eventos deve estar na mesma assinatura do Azure que a conta do Azure Data Lake Storage Gen 1 que você criou. Crie o hub de eventos clicando no botão **Ativado** em **Capturar** na página **Criar Hub de Eventos** do portal. 
2. Na página **Criar Hub de Eventos** do portal, selecione **Azure Data Lake Store** na caixa **Provedor da Captura**.
3. Em **selecionar armazenamento** ao lado da lista suspensa **Data Lake Store** , especifique a conta data Lake Storage Gen 1 que você criou anteriormente e, no campo **caminho do data Lake** , insira o caminho para a pasta de dados que você criou.

    ![Selecionar conta do Data Lake Storage][3]


## <a name="add-or-configure-capture-on-an-existing-event-hub"></a>Adicionar ou configurar a Captura em um hub de eventos existente

Você pode configurar a Captura em hubs de eventos existentes que estejam em namespaces dos Hubs de Eventos. Para habilitar a Captura em um hub de eventos existente ou alterar as configurações da Captura, clique no namespace para carregar a tela de visão geral e, em seguida, clique no hub de eventos para o qual você deseja habilitar ou alterar a configuração da Captura. Por fim, clique na opção **Captura** do lado esquerdo da página aberta e edite as configurações, conforme exibido nas figuras abaixo:

### <a name="azure-blob-storage"></a>Armazenamento do Blobs do Azure

![Configurar Armazenamento de Blobs do Azure][2]

### <a name="azure-data-lake-storage-gen-2"></a>Azure Data Lake Storage Gen 2

![Configurar Azure Data Lake Storage Gen 2](./media/event-hubs-capture-enable-through-portal/configure-data-lake-storage-gen2.png)

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
