---
title: Mover um cluster dedicado de hubs de eventos do Azure para outra região | Microsoft Docs
description: Este artigo mostra como mover um cluster dedicado de hubs de eventos do Azure da região atual para outra região.
ms.topic: how-to
ms.date: 09/01/2020
ms.openlocfilehash: 94be44ee8f9442a3a65e899d7a58524b2570f194
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "89380578"
---
# <a name="move-an-azure-event-hubs-dedicated-cluster-to-another-region"></a>Mover um cluster dedicado de hubs de eventos do Azure para outra região
Este artigo mostra como exportar um modelo de Azure Resource Manager para um cluster dedicado de hubs de eventos existente e, em seguida, usar o modelo para criar um cluster com as mesmas definições de configuração em outra região. 

Se você tiver outros recursos, como namespaces e hubs de eventos no grupo de recursos do Azure que contém o cluster de hubs de eventos, talvez você queira exportar o modelo no nível do grupo de recursos para que todos os recursos relacionados possam ser movidos para a nova região em uma única etapa. As etapas neste artigo mostram como exportar um **cluster de hubs de eventos** para o modelo. As etapas para exportar um **grupo de recursos** para o modelo são semelhantes. 

## <a name="prerequisites"></a>Pré-requisitos
Verifique se o cluster dedicado pode ser criado na região de destino. A maneira mais fácil de descobrir é usar o portal do Azure para tentar [criar um cluster de hubs de eventos dedicado](event-hubs-dedicated-cluster-create-portal.md). Você verá a lista de regiões com suporte nesse momento para criar o cluster. 

## <a name="prepare"></a>Preparar
Para começar, exporte um modelo do Resource Manager. Este modelo contém configurações que descrevem o cluster de hubs de eventos dedicado.

1. Entre no [portal do Azure](https://portal.azure.com).
2. Selecione **todos os recursos** e, em seguida, selecione o cluster de hubs de eventos dedicado.
3. Selecione **configurações** de >  >  **modelo de exportação**.
4. Escolha **baixar** na página **Exportar modelo** .

    :::image type="content" source="./media/move-cluster-across-regions/download-template.png" alt-text="Baixar modelo do Resource Manager" lightbox="./media/move-cluster-across-regions/download-template.png":::
5. Localize o arquivo. zip que você baixou do portal e descompacte esse arquivo em uma pasta de sua escolha.

   Esse arquivo zip contém os arquivos. JSON que incluem o modelo e os scripts para implantar o modelo.


## <a name="move"></a>Mover

Implante o modelo para criar um cluster de hubs de eventos dedicado na região de destino. 


1. No portal do Azure, selecione **Criar um recurso**.
2. Em **Pesquisar no Marketplace**, digite **implantação de modelo** e selecione **implantação de modelo (implantar usando modelos personalizados)**.
5. Selecione **Criar seu próprio modelo no editor**.
6. Selecione **carregar arquivo** e siga as instruções para carregar o **template.jsno** arquivo que você baixou na última seção.
1. Atualize o valor da `location` propriedade para apontar para a nova região. Para obter códigos de localização, consulte [locais do Azure](https://azure.microsoft.com/global-infrastructure/locations/). O código de uma região é o nome da região sem espaços, por exemplo, `West US` é igual a `westus` .
1. Selecione **salvar** para salvar o modelo. 
1. Na página **implantação personalizada** , siga estas etapas: 
    1. Selecione uma **assinatura** do Azure. 
    2. Selecione um **grupo de recursos** existente ou crie um. 
    3. Selecione o **local** ou a região de destino. Se você selecionou um grupo de recursos existente, essa configuração será somente leitura. 
    4. Na seção **configurações** , execute as seguintes etapas:    
        1. Insira o nome do novo **cluster**. 

            :::image type="content" source="./media/move-cluster-across-regions/deploy-template.png" alt-text="Implantar modelo do Resource Manager":::
    5. Selecione **Revisar + criar** na parte inferior da página. 
    1. Na página **revisar + criar** , examine as configurações e, em seguida, selecione **criar**.  

## <a name="discard-or-clean-up"></a>Descartar ou limpar
Após a implantação, se você quiser começar novamente, poderá excluir o **cluster dedicado de hubs de eventos de destino** e repetir as etapas descritas nas seções [preparar](#prepare) e [mover](#move) deste artigo.

Para confirmar as alterações e concluir a movimentação de um cluster de hubs de eventos, exclua o **cluster de hubs de eventos** na região original. 

Para excluir um cluster de hubs de eventos (origem ou destino) usando o portal do Azure:

1. Na janela de pesquisa na parte superior de portal do Azure, digite **clusters de hubs de eventos** e selecione **clusters de hubs de eventos** nos resultados da pesquisa. Você verá o cluster de hubs de eventos em uma lista.
2. Selecione o cluster a ser excluído e selecione **excluir** na barra de ferramentas. 
3. Na página **excluir cluster** , confirme a exclusão digitando o **nome do cluster** e, em seguida, selecione **excluir**. 

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu a mover um cluster de hubs de eventos dedicado de uma região para outra. 

Consulte o artigo [mover os namespaces dos hubs de eventos entre regiões](move-across-regions.md) para obter instruções sobre como mover um namespace de uma região para outra região. 

Para saber mais sobre como mover recursos entre regiões e recuperação de desastres no Azure, confira:

- [Mover recursos para um novo grupo de recursos ou assinatura](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Mover as VMs do Azure para outra região](../site-recovery/azure-to-azure-tutorial-migrate.md)
