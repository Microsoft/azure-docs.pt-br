---
title: Use o Azure SQL Instância Gerenciada com o SSIS (Azure-SQL Server Integration Services) no Azure Data Factory
description: Saiba como usar o Azure SQL Instância Gerenciada com o SSIS (SQL Server Integration Services) no Azure Data Factory.
author: chugugrace
ms.author: chugu
ms.service: data-factory
ms.topic: conceptual
ms.date: 4/15/2020
ms.openlocfilehash: 43b118b8f3f7444cd3c4d0cfa31b72a3522d6d84
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100392571"
---
# <a name="use-azure-sql-managed-instance-with-sql-server-integration-services-ssis-in-azure-data-factory"></a>Usar o Azure SQL Instância Gerenciada com o SQL Server Integration Services (SSIS) no Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Agora você pode mover suas cargas de trabalho, pacotes e projetos do SSIS (SQL Server Integration Services) para a nuvem do Microsoft Azure. Implante, execute e gerencie projetos e pacotes do SSIS no banco de dados SQL do Azure ou no SQL Instância Gerenciada com ferramentas familiares, como SQL Server Management Studio (SSMS). Este artigo realça as seguintes áreas específicas ao usar o Azure SQL Instância Gerenciada com o tempo de execução de integração (IR) do Azure-SSIS:

- [Provisionar um Azure-SSIS IR com o catálogo do SSIS (SSISDB) hospedado pelo SQL Instância Gerenciada do Azure](#provision-azure-ssis-ir-with-ssisdb-hosted-by-azure-sql-managed-instance)
- [Executar pacotes SSIS pelo trabalho do Agente de Instância Gerenciada do SQL do Azure](how-to-invoke-ssis-package-managed-instance-agent.md)
- [Limpar logs do SSISDB pelo trabalho do Agente de Instância Gerenciada do SQL do Azure](#clean-up-ssisdb-logs)
- [Failover de Azure-SSIS IR com o SQL do Azure Instância Gerenciada](configure-bcdr-azure-ssis-integration-runtime.md#azure-ssis-ir-failover-with-a-sql-managed-instance)
- [Migrar cargas de trabalho SSIS locais para o SSIS no ADF com o Azure SQL Instância Gerenciada como destino de carga de trabalho de banco de dados](scenario-ssis-migration-overview.md#azure-sql-managed-instance-as-database-workload-destination)

## <a name="provision-azure-ssis-ir-with-ssisdb-hosted-by-azure-sql-managed-instance"></a>Provisionar Azure-SSIS IR com o SSISDB hospedado pelo SQL Instância Gerenciada do Azure

### <a name="prerequisites"></a>Pré-requisitos

1. [Habilite o Azure Active Directory (Azure AD) no SQL instância gerenciada do Azure](enable-aad-authentication-azure-ssis-ir.md#configure-azure-ad-authentication-for-azure-sql-managed-instance), ao escolher a autenticação Azure Active Directory.

1. Escolha como conectar o SQL Instância Gerenciada, sobre o ponto de extremidade privado ou sobre o ponto de extremidade público:

    - Ponto de extremidade privado (preferencial)

        1. Escolha a rede virtual para o Azure-SSIS IR ingressar:
            - Dentro da mesma rede virtual que a instância gerenciada, com uma **sub-rede diferente**.
            - Dentro de uma rede virtual diferente da instância gerenciada, por meio do emparelhamento de rede virtual (que é limitado à mesma região devido a restrições de emparelhamento de VNet global) ou a uma conexão da rede virtual com a rede virtual.

            Para obter mais informações sobre a conectividade do SQL Instância Gerenciada, consulte [conectar seu aplicativo ao SQL do Azure instância gerenciada](https://review.docs.microsoft.com/azure/sql-database/sql-database-managed-instance-connect-app).

        1. [Configure a rede virtual](#configure-virtual-network).

    - Ponto de extremidade público

        As instâncias gerenciadas do SQL do Azure podem fornecer conectividade sobre [pontos de extremidade públicos](../azure-sql/managed-instance/public-endpoint-configure.md). Os requisitos de entrada e saída precisam ser atendidos para permitir o tráfego entre o SQL Instância Gerenciada e o Azure-SSIS IR:

        - quando Azure-SSIS IR não está dentro de uma rede virtual (preferencial)

            **Requisito de entrada do SQL instância gerenciada** para permitir o tráfego de entrada de Azure-SSIS ir.

            | Protocolo de transporte | Fonte | Intervalo de portas de origem | Destino | Intervalo de portas de destino |
            |---|---|---|---|---|
            |TCP|Marca de serviço de nuvem do Azure|*|VirtualNetwork|3342|

            Para saber mais, confira [Permitir tráfego de ponto de extremidade público no grupo de segurança de rede](../azure-sql/managed-instance/public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group).

        - quando Azure-SSIS IR está dentro de uma rede virtual

            Há um cenário especial quando o SQL Instância Gerenciada está em uma região que Azure-SSIS IR não dá suporte, Azure-SSIS IR está dentro de uma rede virtual sem emparelhamento VNet devido à limitação de emparelhamento VNet global. Nesse cenário, **Azure-SSIS ir dentro de uma rede virtual conecta o** SQL instância gerenciada **sobre o ponto de extremidade público**. Use as regras do NSG (grupo de segurança de rede) abaixo para permitir o tráfego entre o SQL Instância Gerenciada e o Azure-SSIS IR:

            1. **Requisito de entrada do SQL instância gerenciada** para permitir o tráfego de entrada de Azure-SSIS ir.

                | Protocolo de transporte | Fonte | Intervalo de portas de origem | Destino |Intervalo de portas de destino |
                |---|---|---|---|---|
                |TCP|Endereço IP estático de Azure-SSIS IR <br> Para obter detalhes, confira [Traga seu IP público para o Azure-SSIS IR](join-azure-ssis-integration-runtime-virtual-network.md#publicIP).|*|VirtualNetwork|3342|

             1. **Requisito de saída de Azure-SSIS ir** para permitir o tráfego de saída para o SQL instância gerenciada.

                | Protocolo de transporte | Fonte | Intervalo de portas de origem | Destino |Intervalo de portas de destino |
                |---|---|---|---|---|
                |TCP|VirtualNetwork|*|[Endereço IP do ponto de extremidade público do SQL Instância Gerenciada](../azure-sql/managed-instance/management-endpoint-find-ip-address.md)|3342|

### <a name="configure-virtual-network"></a>Configurar rede virtual

1. **Permissão de usuário**. O usuário que cria o Azure-SSIS IR do Azure deve ter a [atribuição de função](../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-for-a-user-at-a-scope) pelo menos no recurso do Azure Data Factory com uma das opções abaixo:

    - Use a função interna de Colaborador de Rede. Essa função vem com a permissão _Microsoft.Network/\*_ , que tem um escopo muito maior do que o necessário.
    - Crie uma função personalizada que inclua apenas a permissão _Microsoft.Network/virtualNetworks/\*/join/action_. Se você também deseja trazer seus endereços IP públicos para o Azure-SSIS IR enquanto ingressa em uma rede virtual do Azure Resource Manager, inclua também a permissão _Microsoft.Network/publicIPAddresses/*/join/action_ na função.

1. **Rede virtual**.

    1. Verifique se o grupo de recursos da rede virtual pode criar e excluir determinados recursos de rede do Azure.

        O Azure-SSIS IR precisa criar determinados recursos de rede no mesmo grupo de recursos que a rede virtual. Esses recursos incluem:
        - Um Azure Load Balancer, com o nome *\<Guid> -azurebatch-cloudserviceloadbalancer*
        - Um grupo de segurança de rede, com o nome * \<Guid> -azurebatch-cloudservicenetworksecuritygroup
        - Um endereço IP público do Azure, com o nome -azurebatch-cloudservicepublicip

        Esses recursos serão criados quando o Azure-SSIS IR for iniciado. Eles serão excluídos quando o Azure-SSIS IR for interrompido. Para evitar o bloqueio do Azure-SSIS IR, não reutilize esses recursos de rede nos outros recursos.

    1. Certifique-se de que você não tem nenhum [bloqueio de recurso](../azure-resource-manager/management/lock-resources.md) no grupo de recursos/assinatura à qual pertence a rede virtual. Se você configurar um bloqueio somente leitura/exclusão, o início e a parada do Azure-SSIS IR falharão ou pararão de responder.

    1. Certifique-se de que você não tem uma política do Azure que impede que os recursos a seguir sejam criados sob o grupo de recursos/assinatura à qual pertence a rede virtual:
        - Microsoft.Network/LoadBalancers
        - Microsoft.Network/NetworkSecurityGroups

    1. Permitir o tráfego na regra do NSG (grupo de segurança de rede), para permitir o tráfego entre o SQL Instância Gerenciada e o Azure-SSIS IR e o tráfego necessário para Azure-SSIS IR.
        1. **Requisito de entrada do SQL instância gerenciada** para permitir o tráfego de entrada de Azure-SSIS ir.

            | Protocolo de transporte | Fonte | Intervalo de portas de origem | Destino | Intervalo de portas de destino | Comentários |
            |---|---|---|---|---|---|
            |TCP|VirtualNetwork|*|VirtualNetwork|1433, 11000-11999|Se a política de conexão do servidor de Banco de Dados SQL estiver definida como **Proxy** em vez de **Redirecionar**, será necessária apenas a porta 1433.|

        1. **Requisito de saída de Azure-SSIS ir** para permitir tráfego de saída para SQL instância gerenciada e outro tráfego necessário para Azure-SSIS ir.

        | Protocolo de transporte | Fonte | Intervalo de portas de origem | Destino | Intervalo de portas de destino | Comentários |
        |---|---|---|---|---|---|
        | TCP | VirtualNetwork | * | VirtualNetwork | 1433, 11000-11999 |Permitir tráfego de saída para o SQL Instância Gerenciada. Se a política de conexão estiver definida como **Proxy** em vez de **Redirecionar**, apenas a porta 1433 será necessária. |
        | TCP | VirtualNetwork | * | AzureCloud | 443 | Os nós do Azure-SSIS IR na rede virtual usam essa porta para acessar os serviços do Azure, como o Armazenamento do Microsoft Azure e Hubs de Eventos do Azure. |
        | TCP | VirtualNetwork | * | Internet | 80 | (Opcional) Os nós de seu Azure-SSIS IR na rede virtual usam essa porta para baixar a lista de revogação de certificados da Internet. Se você bloquear esse tráfego, poderá sofrer um downgrade de desempenho ao iniciar o IR e perder a capacidade de verificar a lista de revogação de certificados quanto ao uso de certificados. Se você deseja restringir ainda mais o destino a determinados FQDNs, confira [Usar o Azure ExpressRoute ou o Roteamento Definido pelo Usuário (UDR)](./join-azure-ssis-integration-runtime-virtual-network.md#route).|
        | TCP | VirtualNetwork | * | Armazenamento | 445 | (Opcional) Esta regra é necessária apenas quando você deseja executar o pacote SSIS armazenado nos Arquivos do Azure. |
        |||||||

        1. **Requisito de entrada do Azure-SSIS IR** para permitir o tráfego necessário para o Azure-SSIS IR.

        | Protocolo de transporte | Fonte | Intervalo de portas de origem | Destino | Intervalo de portas de destino | Comentários |
        |---|---|---|---|---|---|
        | TCP | BatchNodeManagement | * | VirtualNetwork | 29876, 29877 (se você unir o IR a uma rede virtual do Azure Resource Manager) <br/><br/>10100, 20100, 30100 (se você unir o IR a uma rede virtual clássica)| Os serviços do Azure Data Factory usam essas portas para comunicarem-se com os nós de seu tempo de execução de integração do Azure-SSIS IR na rede virtual. <br/><br/> Se você criar ou não um NSG no nível de sub-rede, o Azure Data Factory sempre configurará um NSG no nível dos adaptadores de rede anexados às máquinas virtuais que hospedam o Azure-SSIS IR. Somente o tráfego de entrada dos endereços IP do Data Factory nas portas especificadas é permitido pelo NSG no nível do adaptador de rede. Mesmo se você abrir essas portas para o tráfego da Internet no nível de sub-rede, o tráfego de endereços IP que não sejam endereços IP do Data Factory será bloqueado no nível do adaptador de rede. |
        | TCP | CorpNetSaw | * | VirtualNetwork | 3389 | (Opcional) Esta regra é necessária apenas quando o suporte da Microsoft solicita que o cliente abra para solução avançada de problemas e pode ser fechado logo após a solução. A marca de serviço **CorpNetSaw** permite que apenas as estações de trabalho de acesso seguro na rede corporativa da Microsoft usem a área de trabalho remota. E essa marca de serviço não pode ser selecionada no portal e só está disponível via Azure PowerShell ou CLI do Azure. <br/><br/> No NSG no nível da NIC, a porta 3389 é aberta por padrão e permitimos que você controle a porta 3389 no nível de sub-rede NSG. Enquanto isso, o Azure-SSIS IR não permite a saída da porta 3389 por padrão na regra de firewall do Windows em cada nó de IR para proteção. |
        |||||||

    1. Confira [configuração de rede virtual](join-azure-ssis-integration-runtime-virtual-network.md#virtual-network-configuration) para saber mais:
        - Se você trouxer seus endereços IP públicos para o Azure-SSIS IR
        - Se você usar seu servidor DNS (Sistema de Nomes de Domínio)
        - Se você usar o Azure ExpressRoute ou uma UDR (rota definida pelo usuário)
        - Se você usar Azure-SSIS IR personalizado

### <a name="provision-azure-ssis-integration-runtime"></a>Provisionar um Azure-SSIS Integration Runtime

1. Selecione SQL Instância Gerenciada ponto de extremidade privado ou ponto de extremidade público.

    Ao [provisionar Azure-SSIS ir](create-azure-ssis-integration-runtime.md#provision-an-azure-ssis-integration-runtime) no aplicativo portal do Azure/ADF, na página Configurações do SQL, use o **ponto de extremidade privado** do SQL instância gerenciada ou **ponto de extremidade público** ao criar o catálogo do SSIS (SSISDB).

    O nome do host do ponto de extremidade público é fornecido no formato <nome_mi>.public.<zona_dns>.database.windows.net e a porta usada para a conexão é 3342.  

    ![A captura de tela mostra a configuração do Integration Runtime com a criação de catálogo S I S selecionado e o ponto de extremidade do servidor de banco de dados](./media/how-to-use-sql-managed-instance-with-ir/catalog-public-endpoint.png)

1. Selecione autenticação do Azure AD quando aplicável.

    ![catalog-public-endpoint](./media/how-to-use-sql-managed-instance-with-ir/catalog-aad.png)

    Para obter mais informações sobre como habilitar a autenticação do Azure AD, consulte [habilitar o Azure AD no Azure SQL instância gerenciada](enable-aad-authentication-azure-ssis-ir.md#configure-azure-ad-authentication-for-azure-sql-managed-instance).

1. Associe o Azure-SSIS IR à rede virtual quando aplicável.

    Na página de configuração avançada, selecione a rede virtual e a sub-rede para ingressar.
    
    Quando estiver dentro da mesma rede virtual que o SQL Instância Gerenciada, escolha uma **sub-rede diferente** da instância gerenciada do SQL. 

    Para saber mais sobre como ingressar no Azure-SSIS IR em uma rede virtual, confira [Associar um tempo de execução de integração do Azure-SSIS a uma rede virtual](join-azure-ssis-integration-runtime-virtual-network.md).

    ![Captura de tela mostra as configurações avançadas da instalação do Integration Runtime, em que você pode selecionar uma rede virtual para o tempo de execução ingressar.](./media/how-to-use-sql-managed-instance-with-ir/join-virtual-network.png)

Para obter mais informações sobre como criar um Azure-SSIS IR, consulte [Criar um runtime de integração do Azure-SSIS no Azure Data Factory](create-azure-ssis-integration-runtime.md#provision-an-azure-ssis-integration-runtime).

## <a name="clean-up-ssisdb-logs"></a>Limpar logs do SSISDB

A política de retenção de logs do SSISDB é definida pelas propriedades abaixo nas [catalog.catalog_properties](/sql/integration-services/system-views/catalog-catalog-properties-ssisdb-database):

- OPERATION_CLEANUP_ENABLED

    Quando o valor for TRUE, os detalhes da operação e as mensagens da operação anteriores a RETENTION_WINDOW (dias) serão excluídos do catálogo. Quando o valor for FALSE, todos os detalhes da operação e mensagens da operação serão armazenados no catálogo. Observação: um trabalho do SQL Server executa a limpeza da operação.

- RETENTION_WINDOW

    O número de dias em que os detalhes da operação e as mensagens da operação serão armazenados no catálogo. Quando o valor for -1, a janela de retenção será infinita. Observação: Se nenhuma limpeza for desejada, defina OPERATION_CLEANUP_ENABLED como FALSE.

Para remover os logs do SSISDB que estão fora da janela de retenção definida pelo administrador, você pode disparar o procedimento armazenado `[internal].[cleanup_server_retention_window_exclusive]`. Opcionalmente, você pode agendar a execução do trabalho do SQL Instância Gerenciada Agent para disparar o procedimento armazenado.

## <a name="next-steps"></a>Próximas etapas

- [Executar pacotes SSIS pelo trabalho do Agente de Instância Gerenciada do SQL do Azure](how-to-invoke-ssis-package-managed-instance-agent.md)
- [Configurar BCDR (continuidade de negócios e recuperação de desastres)](configure-bcdr-azure-ssis-integration-runtime.md)
- [Migrar cargas de trabalho do SSIS local para o SSIS no ADF](scenario-ssis-migration-overview.md)