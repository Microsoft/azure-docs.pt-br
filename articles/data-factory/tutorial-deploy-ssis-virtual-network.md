---
title: Tutorial para configurar um tempo de execução de integração do Azure-SSIS para ingressar em uma rede virtual
description: Saiba como unir um tempo de execução de integração do Azure-SSIS para ingressar em uma rede virtual do Azure.
author: chugugrace
ms.author: chugu
ms.service: data-factory
ms.topic: conceptual
ms.date: 1/10/2020
ms.openlocfilehash: 3dbbce4adc44696fdd12f6ce948b48b34caaed75
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100391228"
---
# <a name="configure-an-azure-sql-server-integration-services-ssis-integration-runtime-ir-to-join-a-virtual-network"></a>Configurar um IR (tempo de execução de integração) do SSIS (Azure-SQL Server Integration Services) para ingressar em uma rede virtual

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este tutorial fornece as etapas básicas para usar o portal do Azure para configurar um IR (tempo de execução de integração) do Azure-SQL Server Integration Services (SSIS) para ingressar em uma rede virtual.

As etapas são as seguintes:

- Configurar uma rede virtual.
- Ingresse o Azure-SSIS IR em uma rede virtual do portal Azure Data Factory.

## <a name="prerequisites"></a>Pré-requisitos

- **Tempo de execução de integração do Azure-SSIS**. Se você não tiver um tempo de execução de integração do Azure-SSIS, [provisione um tempo de execução de integração do Azure-SSIS em Azure data Factory antes de](tutorial-deploy-ssis-packages-azure.md) começar.

- **Permissão de usuário**. O usuário que cria o Azure-SSIS IR do Azure deve ter a [atribuição de função](../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-for-a-user-at-a-scope) pelo menos no recurso do Azure Data Factory com uma das opções abaixo:

    - Use a função interna de Colaborador de Rede. Essa função vem com a permissão _Microsoft.Network/\*_ , que tem um escopo muito maior do que o necessário.
    - Crie uma função personalizada que inclua apenas a permissão _Microsoft.Network/virtualNetworks/\*/join/action_. Se você também quiser colocar seus próprios endereços IP públicos para Azure-SSIS IR ao se juntar a uma rede virtual Azure Resource Manager, inclua a permissão _Microsoft. Network/publicIPAddresses/*/Join/Action_ na função.

- **Rede virtual**.

    - Se você não tiver uma rede virtual, [crie uma rede virtual usando o portal do Azure](../virtual-network/quick-create-portal.md).

    - Verifique se o grupo de recursos da rede virtual pode criar e excluir determinados recursos de rede do Azure.
    
        O Azure-SSIS IR precisa criar determinados recursos de rede no mesmo grupo de recursos que a rede virtual. Esses recursos incluem:
        - Um Azure Load Balancer, com o nome *\<Guid> -azurebatch-cloudserviceloadbalancer*
        - Um grupo de segurança de rede, com o nome * \<Guid> -azurebatch-cloudservicenetworksecuritygroup
        - Um endereço IP público do Azure, com o nome -azurebatch-cloudservicepublicip
    
        Esses recursos serão criados quando o Azure-SSIS IR for iniciado. Eles serão excluídos quando o Azure-SSIS IR for interrompido. Para evitar o bloqueio do Azure-SSIS IR, não reutilize esses recursos de rede nos outros recursos.

    - Certifique-se de que você não tem nenhum [bloqueio de recurso](../azure-resource-manager/management/lock-resources.md) no grupo de recursos/assinatura à qual pertence a rede virtual. Se você configurar um bloqueio somente leitura/exclusão, o início e a parada do Azure-SSIS IR falharão ou pararão de responder.

    - Certifique-se de que você não tem uma atribuição de Azure Policy que impede que os seguintes recursos sejam criados sob o grupo de recursos/assinatura ao qual a rede virtual pertence:
        - Microsoft.Network/LoadBalancers
        - Microsoft.Network/NetworkSecurityGroups

- Os cenários de **configuração de rede** abaixo não são abordados neste tutorial:
    - Se você trazer seus próprios endereços IP públicos para o Azure-SSIS IR.
    - Se você usar seu próprio servidor DNS (sistema de nomes de domínio).
    - Se você usar um NSG (grupo de segurança de rede) na sub-rede.
    - Se você usar o Azure ExpressRoute ou uma rota definida pelo usuário (UDR).
    - Se você usar Azure-SSIS IR personalizadas.
    
    Para obter mais informações, verifique a [configuração de rede virtual](join-azure-ssis-integration-runtime-virtual-network.md#virtual-network-configuration).

## <a name="configure-a-virtual-network"></a>Configurar uma rede virtual

Use o portal do Azure para configurar uma rede virtual antes de tentar adicionar uma Azure-SSIS IR a ela.

1. Inicie o Microsoft Edge ou o Google Chrome. Atualmente, somente esses navegadores da Web dão suporte à interface do usuário do Data Factory.

1. Entre no [portal do Azure](https://portal.azure.com).

1. Selecione **mais serviços**. Filtre e selecione **Redes virtuais**.

1. Filtre e selecione sua rede virtual na lista.

1. Na página **Rede virtual**, selecione **Propriedades**.

1. Selecione o botão de cópia para que a **ID DO RECURSO** copie a ID do recurso da rede virtual para a área de transferência. Salve a ID da área de transferência no OneNote ou em um arquivo.

1. No menu à esquerda, selecione **sub-redes**.

    - Verifique se a sub-rede selecionada tem espaço de endereço disponível suficiente para o Azure-SSIS IR usar. Deixe os endereços IP disponíveis pelo menos duas vezes o número do nó IR. O Azure reserva alguns endereços IP em cada sub-rede. Esses endereços não podem ser usados. O primeiro e o último endereços IP das sub-redes são reservados para a conformidade do protocolo e mais três endereços são usados para os serviços do Azure. Para obter mais informações, consulte [Existem restrições quanto ao uso de endereços IP dentro dessas sub-redes?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)
    - Não selecione GatewaySubnet para implantar um Azure-SSIS IR. Ele é dedicado a gateways de rede virtual.
    - Não use uma sub-rede que esteja exclusivamente ocupada por outros serviços do Azure (por exemplo, SQL do banco de dados SQL Instância Gerenciada, serviço de aplicativo e assim por diante).

1. Verifique se o provedor do Lote do Azure está registrado na assinatura do Azure que possui a rede virtual. Ou registre o provedor do lote do Azure. Se você já tiver uma conta do lote do Azure em sua assinatura, sua assinatura será registrada para o lote do Azure. (Se você criar o IR do Azure-SSIS no portal do Azure Data Factory,  o provedor do lote do Microsoft Azure é registrado automaticamente para você.)

   1. No portal do Azure, no menu à esquerda, selecione **assinaturas**.

   1. Selecione sua assinatura.

   1. À esquerda, selecione **provedores de recursos** e confirme se **Microsoft.Batch** é um provedor registrado.

   ![Confirmação do status "Registrado"](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Se você não visualizar **Microsoft.Batch** na lista, para registrá-lo [crie uma conta do Lote do Azure vazia](../batch/batch-account-create-portal.md) na sua assinatura. Você pode excluí-lo depois.

## <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>Unir o IR do Azure-SSIS a uma rede virtual

Depois de configurar sua rede virtual Azure Resource Manager ou rede virtual clássica, você pode unir o Azure-SSIS IR à rede virtual:

1. Inicie o Microsoft Edge ou o Google Chrome. Atualmente, somente esses navegadores da Web dão suporte à interface do usuário do Data Factory.

1. No [portal do Azure](https://portal.azure.com), no menu à esquerda, selecione **Data factories**. Se você não vir **fábricas de dados** no menu, selecione **mais serviços** e, na seção **inteligência + análise** , selecione **Data factories**.

   ![Lista de fábricas de dados](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)

1. Selecione o data factory com o Azure-SSIS IR na lista. Você verá a home page do seu data factory. Selecione o bloco **Criar e Monitorar**. Você visualiza a interface do usuário do Data Factory em uma guia separada.

   ![Página inicial do data factory](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)

1. Na interface de usuário do Data Factory, alterne para a guia **Editar**, selecione **Conexões** e alterne para a guia **Runtimes de integração**.

   ![Guia "runtimes de integração"](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)

1. Se o Azure-SSIS IR estiver em execução, na lista de **tempos de execução de integração** , na coluna **ações** , selecione o botão **parar** para seu Azure-SSIS ir. Não é possível editar seu Azure-SSIS IR até que você o interrompa.

   ![Parar o IR](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)

1. Na lista de **tempos de execução de integração** , na coluna **ações** , selecione o botão **Editar** para seu Azure-SSIS ir.

   ![Editar o runtime de integração](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)

1. No painel de instalação do Integration Runtime, avance pelas seções **configurações gerais** e **configurações do SQL** selecionando o botão **Avançar** .

1. Na seção **Configurações avançadas** :
   1. Selecione a **opção Selecionar uma VNet para sua Azure-SSIS Integration Runtime para ingressar, permitir que o ADF crie determinados recursos de rede e, opcionalmente, traga sua própria caixa de seleção de endereços IP públicos estáticos** .

   1. Para **Assinatura**, selecione a assinatura do Azure que possui sua rede virtual.

   1. Para **Local**, o mesmo local do seu runtime de integração é selecionado.

   1. Para **tipo**, selecione o tipo de sua rede virtual: clássica ou Azure Resource Manager. Recomendamos que você selecione um Azure Resource Manager rede virtual, pois as redes virtuais clássicas serão preteridas em breve.

   1. Para **Nome da VNet**, selecione o nome da sua rede virtual. Ele deve ser o mesmo usado para o banco de dados SQL com pontos de extremidade de serviço de rede virtual ou o SQL Instância Gerenciada com o Endpoint privado para hospedar o SSISDB. Ou deve ser o mesmo conectado à sua rede local. Caso contrário, pode ser qualquer rede virtual para trazer seus próprios endereços IP públicos estáticos para Azure-SSIS IR.

   1. Para **Nome da sub-rede**, selecione o nome da sub-rede da sua rede virtual. Ele deve ser o mesmo usado para o SQL banco com pontos de extremidade de serviço de rede virtual para hospedar o SSISDB. Ou deve ser uma sub-rede diferente da usada para seu Instância Gerenciada SQL com ponto de extremidade privado para hospedar o SSISDB. Caso contrário, pode ser qualquer sub-rede para trazer seus próprios endereços IP públicos estáticos para Azure-SSIS IR.

   1. Selecione **validação de VNet**. Se a validação for bem-sucedida, selecione **continuar**.

   ![Configurações avançadas com uma rede virtual](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

1. Na seção **Resumo** , examine todas as configurações de seu Azure-SSIS ir. Em seguida, selecione **Atualizar**.

1. Inicie sua Azure-SSIS IR selecionando o botão **Iniciar** na coluna **ações** para sua Azure-SSIS ir. Leva cerca de 20 a 30 minutos para iniciar o Azure-SSIS IR que une uma rede virtual.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como [unir Azure-SSIS ir a uma rede virtual](join-azure-ssis-integration-runtime-virtual-network.md).