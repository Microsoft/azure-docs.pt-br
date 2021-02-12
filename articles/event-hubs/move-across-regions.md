---
title: Mover um namespace de hubs de eventos do Azure para outra região | Microsoft Docs
description: Este artigo mostra como mover um namespace de hubs de eventos do Azure da região atual para outra região.
ms.topic: how-to
ms.date: 09/01/2020
ms.openlocfilehash: b177c3916919e3d97325f9d8c6b6027c00cb476f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96019915"
---
# <a name="move-an-azure-event-hubs-namespace-to-another-region"></a>Mover um namespace de hubs de eventos do Azure para outra região
Este artigo mostra como exportar um modelo de Azure Resource Manager para um namespace de hubs de eventos existente e, em seguida, usar o modelo para criar um namespace com as mesmas definições de configuração em outra região. No entanto, esse processo não move eventos que ainda não foram processados. Você precisa processar os eventos do namespace original antes de excluí-lo.
 
Se você tiver outros recursos no grupo de recursos do Azure que contenham o namespace de hubs de eventos, talvez queira exportar o modelo no nível do grupo de recursos para que todos os recursos relacionados possam ser movidos para a nova região em uma única etapa. As etapas neste artigo mostram como exportar um **namespace** para o modelo. As etapas para exportar um **grupo de recursos** para o modelo são semelhantes. 

## <a name="prerequisites"></a>Pré-requisitos

- Verifique se os serviços e recursos que sua conta usa têm suporte na região de destino.
- Se você tiver o **recurso de captura** habilitado para hubs de eventos no namespace, mova o [armazenamento do Azure ou Azure data Lake Store gen 2](../storage/common/storage-account-move.md) ou Azure data Lake Store contas [Gen 1](../data-lake-store/data-lake-store-migration-cross-region.md) antes de mover o namespace dos hubs de eventos. Você também pode mover o grupo de recursos que contém os namespaces de armazenamento e hubs de eventos para a outra região seguindo as etapas semelhantes às descritas neste artigo. 
- Se o namespace de hubs de eventos estiver em um **cluster de hubs de eventos**, [mova o cluster dedicado](move-cluster-across-regions.md) para a **região de destino** antes de percorrer as etapas neste artigo. Você também pode usar o [modelo de início rápido no GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-cluster-namespace-eventhub/) para criar um cluster de hubs de eventos. No modelo, remova a parte de namespace do JSON para criar apenas o cluster. 

## <a name="prepare"></a>Preparar
Para começar, exporte um modelo do Resource Manager. Este modelo contém configurações que descrevem seu namespace de hubs de eventos.

1. Entre no [portal do Azure](https://portal.azure.com).
2. Selecione **todos os recursos** e, em seguida, selecione seu namespace de hubs de eventos.
3. Selecione **configurações** de >  >  **modelo de exportação**.
4. Escolha **baixar** na página **Exportar modelo** .

    ![Baixar modelo do Resource Manager](./media/move-across-regions/download-template.png)
5. Localize o arquivo. zip que você baixou do portal e descompacte esse arquivo em uma pasta de sua escolha.

   Esse arquivo zip contém os arquivos. JSON que incluem o modelo e os scripts para implantar o modelo.


## <a name="move"></a>Mover

Implante o modelo para criar um namespace de hubs de eventos na região de destino. 


1. Na portal do Azure, selecione **criar um recurso**.
2. Em **Pesquisar no Marketplace**, digite **implantação de modelo** e selecione **implantação de modelo (implantar usando modelos personalizados)**.
5. Selecione **Criar seu próprio modelo no editor**.
6. Selecione **carregar arquivo** e siga as instruções para carregar o **template.jsno** arquivo que você baixou na última seção.
1. Atualize o valor da `location` propriedade para apontar para a nova região. Para obter códigos de localização, consulte [locais do Azure](https://azure.microsoft.com/global-infrastructure/locations/). O código de uma região é o nome da região sem espaços, por exemplo, `West US` é igual a `westus` .
1. Selecione **salvar** para salvar o modelo. 
1. Na página **implantação personalizada** , siga estas etapas: 
    1. Selecione uma **assinatura** do Azure. 
    2. Selecione um **grupo de recursos** existente ou crie um. Se o namespace de origem estava em um cluster de hubs de eventos, selecione o grupo de recursos que contém o cluster na região de destino. 
    3. Selecione o **local** ou a região de destino. Se você selecionou um grupo de recursos existente, essa configuração será somente leitura. 
    4. Na seção **configurações** , execute as seguintes etapas:    
        1. Insira o novo **nome do namespace**. 

            ![Implantar modelo do Resource Manager](./media/move-across-regions/deploy-template.png)
        2. Se o namespace de origem estava em **um cluster de hubs de eventos**, insira os nomes do grupo de **recursos** e do cluster de hubs de **eventos** como parte da **ID externa**. 

              ```
              /subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<CLUSTER'S RESOURCE GROUP>/providers/Microsoft.EventHub/clusters/<CLUSTER NAME>
              ```   
        3. Se o Hub de eventos em seu namespace usar uma conta de armazenamento para capturar eventos, especifique o nome do grupo de recursos e a conta de armazenamento para o `StorageAccounts_<original storage account name>_external` campo. 
            
            ```
            /subscriptions/0000000000-0000-0000-0000-0000000000000/resourceGroups/<STORAGE'S RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>
            ```    
    5. Selecione **Revisar + criar** na parte inferior da página. 
    1. Na página **revisar + criar** , examine as configurações e, em seguida, selecione **criar**.   

## <a name="discard-or-clean-up"></a>Descartar ou limpar
Após a implantação, se você quiser começar novamente, poderá excluir o namespace de **hubs de eventos de destino** e repetir as etapas descritas nas seções [preparar](#prepare) e [mover](#move) deste artigo.

Para confirmar as alterações e concluir a movimentação de um namespace de hubs de eventos, exclua o **namespace de hubs de eventos** na região original. Certifique-se de que você processou todos os eventos no namespace antes de excluir o namespace. 

Para excluir um namespace de hubs de eventos (origem ou destino) usando o portal do Azure:

1. Na janela de pesquisa na parte superior de portal do Azure, digite **hubs de eventos** e selecione **hubs de eventos** nos resultados da pesquisa. Você vê os namespaces dos hubs de eventos em uma lista.
2. Selecione o namespace de destino a ser excluído e selecione **excluir** na barra de ferramentas. 

    ![Excluir namespace-botão](./media/move-across-regions/delete-namespace-button.png)
3. Na página **excluir namespace** , confirme a exclusão digitando o **nome do namespace** e, em seguida, selecione **excluir**. 

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você moveu um namespace de hubs de eventos do Azure de uma região para outra e limpou os recursos de origem.  Para saber mais sobre como mover recursos entre regiões e recuperação de desastres no Azure, confira:


- [Mover recursos para um novo grupo de recursos ou assinatura](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Mover as VMs do Azure para outra região](../site-recovery/azure-to-azure-tutorial-migrate.md)
