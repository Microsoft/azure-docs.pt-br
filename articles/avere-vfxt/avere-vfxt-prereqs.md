---
title: Pré-requisitos de Avere vFXT – Azure
description: Saiba mais sobre as tarefas a serem executadas antes de criar um cluster no avere vFXT para o Azure, incluindo lidar com assinaturas, cotas e pontos de extremidade de serviço de armazenamento.
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 01/21/2020
ms.author: rohogue
ms.openlocfilehash: d87f57873a4bb84b20df3da3880017d9ef2484a5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96008405"
---
# <a name="prepare-to-create-the-avere-vfxt"></a>Preparar para criar o Avere vFXT

Este artigo explica as tarefas de pré-requisito para criar um cluster avere vFXT.

## <a name="create-a-new-subscription"></a>Criar uma nova assinatura

Comece criando uma nova assinatura do Azure. Use uma assinatura separada para cada projeto avere vFXT para simplificar o rastreamento e a limpeza de despesas e para garantir que outros projetos não sejam afetados por cotas ou limitação de recursos ao provisionar o fluxo de trabalho do cluster.

Para criar uma nova assinatura do Azure no portal do Azure:

1. Navegue até a [folha Assinaturas](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)
1. Clique no botão **+ Adicionar** na parte superior
1. Entre se solicitado
1. Selecione uma oferta e percorra as etapas para criar uma nova assinatura

## <a name="configure-subscription-owner-permissions"></a>Configure as permissões do proprietário da assinatura

Um usuário com permissões de proprietário para a assinatura deve criar o cluster do vFXT. A criação de cluster exige que um proprietário aceite os termos de serviço do software e autorize as alterações nos recursos de rede e de armazenamento.

Há algumas soluções alternativas para permitir que um não proprietário crie um avere vFXT para o cluster do Azure. Esses cenários envolvem a restrição de recursos e a atribuição de funções do Azure adicionais ao criador. Em todos esses casos, um proprietário de assinatura também deve [aceitar os termos de software avere vFXT](#accept-software-terms) antes do tempo.

| Cenário | Restrições | Funções de acesso necessárias para criar o cluster avere vFXT |
|----------|--------|-------|
| O administrador do grupo de recursos cria o vFXT | A rede virtual, o controlador de cluster e os nós de cluster devem ser criados dentro do grupo de recursos. | Funções de administrador e [colaborador](../role-based-access-control/built-in-roles.md#contributor) de [acesso do usuário](../role-based-access-control/built-in-roles.md#user-access-administrator) , ambas com escopo para o grupo de recursos de destino. |
| Usar uma rede virtual externa existente | O controlador de cluster e os nós de cluster são criados dentro do grupo de recursos do vFXT, mas usam uma rede virtual existente em um grupo de recursos diferente. | (1) funções de administrador e [colaborador](../role-based-access-control/built-in-roles.md#contributor) de [acesso de usuário](../role-based-access-control/built-in-roles.md#user-access-administrator) com escopo para o grupo de recursos vFXT; e (2) [colaborador de máquina virtual](../role-based-access-control/built-in-roles.md#virtual-machine-contributor), [administrador de acesso de usuário](../role-based-access-control/built-in-roles.md#user-access-administrator)e funções de colaborador de [avere](../role-based-access-control/built-in-roles.md#avere-contributor) com escopo definido para o grupo de recursos da rede virtual. |
| Função personalizada para criadores de cluster | Sem restrições de posicionamento de recursos. Esse método fornece privilégios significativos que não são proprietários. | O proprietário da assinatura cria uma função personalizada do Azure, conforme explicado neste [artigo](avere-vfxt-non-owner.md). |

## <a name="quota-for-the-vfxt-cluster"></a>Cota para o cluster vFXT

Verifique se você tem cota suficiente para os seguintes componentes do Azure. Se necessário, [solicite um aumento de cota](../azure-portal/supportability/resource-manager-core-quotas-request.md).

> [!NOTE]
> As máquinas virtuais e componentes SSD aqui listados são para o próprio cluster vFXT. Lembre-se de que você também precisa de cota para as VMs e SSDs que serão usadas para seu farm de computação.
>
> Verifique se a cota está habilitada para a região em que você pretende executar o fluxo de trabalho.

|Componente do Azure|Quota|
|----------|-----------|
|Máquinas virtuais|3 ou mais E32s_v3 (um por nó de cluster) |
|Armazenamento SSD Premium|Espaço do sistema operacional de 200 GB, além de 1 a 4 TB de espaço do cache por nó |
|Conta de armazenamento (opcional) |v2|
|Armazenamento de back-end de dados (opcional) |Um novo contêiner de Blob de LRS |
<!-- this table also appears in the overview - update it there if updating here -->

## <a name="accept-software-terms"></a>Aceitar os termos de software

> [!TIP]
> Ignore esta etapa se um proprietário de assinatura criar o cluster avere vFXT.

Durante a criação do cluster, você deve aceitar os termos de serviço para o software Avere vFXT. Se você não é um proprietário de assinatura, faça com que um proprietário de assinatura aceite os termos de antemão.

Essa etapa só precisa ser feita uma vez por assinatura.

Para aceitar os termos de software de antemão:

1. Abra um Cloud Shell no portal do Azure ou navegando até <https://shell.azure.com>. Entre com sua ID da assinatura.

   ```azurecli
    az login
    az account set --subscription <subscription ID>
   ```

1. Execute este comando para aceitar os termos de serviço e habilitar acesso programático para a imagem do software Avere vFXT para Azure:

   ```azurecli
   az vm image accept-terms --urn microsoft-avere:vfxt:avere-vfxt-controller:latest
   ```

## <a name="create-a-storage-service-endpoint-in-your-virtual-network-if-needed"></a>Criar um ponto de extremidade de serviço de armazenamento em sua rede virtual (se necessário)

Um [ponto de extremidade de serviço](../virtual-network/virtual-network-service-endpoints-overview.md) mantém o tráfego de BLOBs do Azure local, em vez de roteá-lo fora da rede virtual. É recomendável para qualquer vFXT avere para o cluster do Azure que usa o blob do Azure para armazenamento de dados de back-end.

Se você criar uma nova rede virtual ao criar o cluster, um ponto de extremidade será criado automaticamente. Se você fornecer uma rede virtual existente, ela deverá ter um ponto de extremidade do serviço de armazenamento da Microsoft se você quiser criar um novo contêiner de armazenamento de BLOBs durante a criação do cluster.<!-- if there is no endpoint in that situation, the cluster creation will fail -->

> [!TIP]
>
>* Ignore esta etapa se você estiver criando uma nova rede virtual como parte da criação do cluster.
>* Um ponto de extremidade será opcional se você não estiver criando um armazenamento de blob durante a criação do cluster. Nesse caso, você poderá criar o ponto de extremidade de serviço posteriormente se decidir usar o blob do Azure.

Crie o ponto de extremidade do serviço de armazenamento no portal do Azure.

1. No portal, abra a lista de redes virtuais.
1. Selecione a rede virtual para o cluster.
1. Clique em **pontos de extremidade de serviço** no menu à esquerda.
1. Clique em **Adicionar** na parte superior.
1. Escolha o serviço ``Microsoft.Storage`` .
1. Selecione a sub-rede do cluster.
1. Na parte inferior, clique em **Adicionar**.

   ![Captura de tela do portal do Azure com anotações para as etapas de criação de ponto de extremidade de serviço](media/avere-vfxt-service-endpoint.png)

## <a name="next-steps"></a>Próximas etapas

Depois de concluir esses pré-requisitos, você poderá criar o cluster. Leia [Implantar o cluster vFXT](avere-vfxt-deploy.md) para obter instruções.