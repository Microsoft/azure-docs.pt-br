---
title: Habilitar a Captura de Hubs de Eventos do Azure por meio do portal | Microsoft Docs
description: Habilite o recurso Captura de Hubs de Eventos usando o portal do Azure.
services: event-hubs
documentationcenter: 
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/28/2017
ms.author: sethm
ms.openlocfilehash: 4a4ab1ee022b6b33d35217df916d01f32e04d3a4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="enable-event-hubs-capture-using-the-azure-portal"></a>Habilitar a Captura de Hubs de Evento usando o portal do Azure

A [Captura de Hubs de Eventos][capture-overview] do Azure habilita o fornecimento automático dos dados de streaming no Hub de Eventos para uma conta do [Armazenamento de Blobs do Azure](https://azure.microsoft.com/services/storage/blobs/) ou do [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/) desejada.

Você pode configurar Captura na hora da criação do hub de eventos usando o [portal do Azure](https://portal.azure.com). Ou pode capturar os dados em um contêiner do [Armazenamento de Blobs](https://azure.microsoft.com/services/storage/blobs/) do Azure ou em uma conta do [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/).

Para saber mais, confira [Visão geral da captura dos Hubs de Eventos][capture-overview].

## <a name="capture-data-to-an-azure-storage-account"></a>Capturar dados em uma conta do Armazenamento do Azure  

Quando você cria um hub de eventos, pode habilitar a Captura clicando no botão **Ativado** na tela **Criar Hub de Eventos** do portal. Em seguida, especifique uma Conta de Armazenamento e um contêiner clicando em **Armazenamento do Azure** na caixa **Provedor da Captura**. Já que a Captura dos Hubs de Eventos usa a autenticação de serviços com o armazenamento, você não precisa especificar uma cadeia de conexão de armazenamento. O seletor de recurso seleciona automaticamente o URI do recurso para sua conta de armazenamento. Se você usar o Azure Resource Manager, deverá fornecer esse URI explicitamente como uma cadeia de caracteres.

A janela de tempo padrão é de 5 minutos. O valor mínimo é 1, o máximo é 15. A janela **Tamanho** tem um intervalo de 10 a 500 MB.

![][1]

## <a name="capture-data-to-an-azure-data-lake-store-account"></a>Capturar dados em uma conta do Azure Data Lake Store

Para capturar dados em um Azure Data Lake Store, você pode criar uma conta do Data Lake Store e um hub de eventos:

### <a name="create-an-azure-data-lake-store-account-and-folders"></a>Criar uma conta e pastas do Azure Data Lake Store

1. Crie uma conta do Data Lake Store seguindo as instruções em [Introdução ao Azure Data Lake Store usando o portal do Azure](../data-lake-store/data-lake-store-get-started-portal.md).
2. Siga as instruções na seção [Atribuir permissões aos Hubs de Eventos](../data-lake-store/data-lake-store-archive-eventhub-capture.md#assign-permissions-to-event-hubs) para criar uma pasta dentro da conta do Data Lake Store na qual você deseja capturar os dados dos Hubs de eventos e atribuir permissões a Hubs de Eventos para que ele possa escrever dados em sua conta do Data Lake Store.  

### <a name="create-an-event-hub"></a>Criar um Hub de Evento

1. Observe que o hub de eventos deve estar na mesma assinatura do Azure que o Azure Data Lake Store recém-criado. Crie o hub de eventos clicando no botão **Ativado** em **Capturar** na página **Criar Hub de Eventos** do portal. 
2. Na página **Criar Hub de Eventos** do portal, selecione **Azure Data Lake Store** na caixa **Provedor da Captura**.
3. Em **Azure Data Lake Store**, especifique a conta do Data Lake Store criada anteriormente e, no campo **Caminho do Data Lake**, digite o caminho para a pasta de dados criada.

    ![][3]

## <a name="add-or-configure-capture-on-an-existing-event-hub"></a>Adicionar ou configurar a Captura em um hub de eventos existente

Você pode configurar a Captura em hubs de eventos existentes que estejam em namespaces dos Hubs de Eventos. Para habilitar a Captura em um hub de eventos existente ou alterar as configurações da Captura, clique no namespace para carregar a tela **Essentials** e clique no Hub de Eventos para o qual você deseja habilitar ou alterar a configuração da Captura. Por fim, clique na seção **Propriedades** da página aberta e edite as configurações de captura conforme mostrado nas figuras abaixo:

### <a name="azure-blob-storage"></a>Armazenamento do Blobs do Azure

![][2]

### <a name="azure-data-lake-store"></a>Repositório Azure Data Lake

![][4]

[1]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture1.png
[2]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture2.png
[3]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture3.png
[4]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture4.png

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre a captura de Hubs de Eventos lendo a [visão geral da Captura de Hubs de Eventos][capture-overview].
- Você também pode configurar a Captura dos Hubs de Eventos usando modelos do Azure Resource Manager. Para saber mais, confira [Habilitar Captura usando um modelo do Azure Resource Manager](event-hubs-resource-manager-namespace-event-hub-enable-capture.md).
- [Introdução ao Azure Data Lake Store usando o portal do Azure](../data-lake-store/data-lake-store-get-started-portal.md)

[capture-overview]: event-hubs-capture-overview.md