---
title: Gerenciamento de clientes potenciais com o armazenamento de blobs do Azure - Marketplace comercial do Azure
description: Saiba como usar o blob do Azure para configurar clientes potenciais para o Microsoft AppSource e o Azure Marketplace
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 05/01/2020
ms.openlocfilehash: bd2f3d40b1aea1c0133f95e069ebfd527d30bd59
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94491096"
---
# <a name="use-azure-blob-storage-to-manage-commercial-marketplace-leads"></a>Usar o armazenamento de blobs do Azure para gerenciar clientes potenciais do Marketplace comercial

>[!Caution]
>O suporte do Marketplace comercial para o armazenamento de blobs do Azure foi preterido e não é mais uma opção para processar os clientes potenciais da sua oferta. Se, no momento, você tiver uma oferta do Marketplace comercial com gerenciamento de clientes potenciais configurado para o blob do Azure, nenhum cliente potencial adicional será recebido. Atualize a configuração de gerenciamento de clientes potenciais para qualquer uma das outras opções. Saiba mais sobre as outras opções na [página de aterrissagem de gerenciamento de clientes potenciais](./commercial-marketplace-get-customer-leads.md).

 Se seu sistema de gerenciamento de relacionamento com o cliente (CRM) não tiver suporte explícito na Central de Parceiros para receber clientes potenciais do Microsoft AppSource e do Azure Marketplace, você poderá usar o armazenamento de blobs do Azure. Você pode optar por exportar os dados e importá-los no sistema de CRM. As instruções neste artigo explicarão o processo de criação de uma conta de Armazenamento do Microsoft Azure e um blob sob essa conta. Além disso, você pode criar um novo fluxo com o Power Automate para enviar uma notificação por email quando sua oferta receber um cliente potencial.

>[!NOTE]
>O conector do Power Automate usado nestas instruções requer uma assinatura paga do Power Automate. Verifique se sua conta cumpre esse requisito antes de seguir as instruções neste artigo.

## <a name="configure-azure-blob-storage"></a>Configurar armazenamento de blobs do Azure

1. Se você não tiver uma conta do Azure, poderá [criar uma conta de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).

2. Após a sua conta do Azure, entre no [portal do Azure](https://portal.azure.com).

3. No portal do Azure, use o procedimento a seguir para criar uma conta de armazenamento.  
    1. Selecione **+Criar um recurso** na barra de menu esquerda.  O painel (folha) **Novo** será exibido à direita.
    2. Selecione **Armazenamento** no painel **Novo**.  Uma lista **Em destaque** aparece à direita.
    3. Selecione a **conta de armazenamento**  para iniciar a criação da conta.  Siga as instruções no artigo [Criar uma conta de armazenamento](../../storage/common/storage-account-create.md?tabs=azure-portal).

    ![Etapas para criar uma conta de armazenamento do Azure](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-create.png)

    Para obter mais informações sobre contas de armazenamento, consulte este guia de [início rápido](../../storage/blobs/storage-quickstart-blobs-portal.md).  Para obter mais informações sobre preços de armazenamento, veja [preço de armazenamento](https://azure.microsoft.com/pricing/details/storage/).

4. Aguarde alguns minutos até que sua conta de armazenamento seja provisionada.  Em seguida, acesse sua conta de armazenamento na **página inicial** do portal do Azure selecionando **Ver todos os seus recursos** ou **Todos os recursos** na barra de navegação esquerda do portal do Azure.

    ![Acesse sua conta de armazenamento do Azure](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-access.png)

5. No painel da sua conta de armazenamento, selecione **Chaves de acesso** e copie o valor da *cadeia de conexão* para a chave. Salve esse valor, pois ele é o valor da *Cadeia de conexão da conta de armazenamento* que você precisará fornecer ao portal de publicação para receber clientes potenciais para sua oferta no Marketplace.

     Um exemplo de uma cadeia de conexão é:

     ```sql
     DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
     ```

    ![Captura de tela que mostra a página "chaves de acesso" com a caixa de texto "cadeia de conexão" realçada.](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-keys-2.png)

6. Na página da conta de armazenamento, selecione **Blobs**.

   ![Captura de tela da página da conta de armazenamento do Azure com BLOBs selecionados](./media/commercial-marketplace-lead-management-instructions-azure-blob/select-blobs.png)

7. Uma vez na página de blobs, selecione o botão **+ Contêiner**.

8. Digite um **nome** para o novo contêiner. O nome do contêiner deve estar com letras minúsculas, começar com uma letra ou número e pode incluir apenas letras, números e o caractere traço (-). Para obter mais informações sobre nomes de contêiner e de blobs, confira [Nomeando e referenciando contêineres, blobs e metadados](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

    Salve esse valor, pois ele é o valor de *nome do contêiner* que você precisa fornecer no portal de publicação para receber clientes potenciais para sua oferta no Marketplace.

9. Defina o nível de acesso público para o contêiner como **Privado (sem acesso anônimo)** .

10. Selecione **OK** para criar o contêiner.

    ![Novo contêiner](./media/commercial-marketplace-lead-management-instructions-azure-blob/new-container.png)

## <a name="configure-your-offer-to-send-leads-to-azure-blob-storage"></a>Configurar sua oferta para enviar clientes potenciais ao armazenamento de blobs do Azure

Quando estiver pronto para configurar as informações de gerenciamento de clientes potenciais para a oferta no portal de publicação, siga as etapas abaixo:

1. Vá para a página **Configuração de oferta** da sua oferta.
2. Na seção **Clientes potenciais** , selecione **Conectar**.

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-azure-blob/customer-leads.png" alt-text="Clientes potenciais":::

3. Na janela pop-up de detalhes da conexão, selecione **Blob do Azure** para o destino de clientes potenciais.

    ![Detalhes da conexão](./media/commercial-marketplace-lead-management-instructions-azure-blob/connect-details.png) 

4. Informe o **nome do contêiner** e a **cadeia de conexão da conta de armazenamento** que você recebeu ao seguir estas instruções.

    * Exemplo de nome de contêiner: `marketplaceleadcontainer`
    * Exemplo de cadeia de conexão da conta de armazenamento: `DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net` ![Detalhes da conexão](./media/commercial-marketplace-lead-management-instructions-azure-blob/connection-details.png) 

5. Clique em **Salvar**.

    > [!NOTE]
    > Conclua a configuração do restante da oferta e publique-a antes de receber clientes potenciais para a oferta.