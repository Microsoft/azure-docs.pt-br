---
title: O que é um Ponto de Extremidade Privado do Azure?
description: Saiba mais sobre o Ponto de Extremidade Privado
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: allensu
ms.openlocfilehash: 79d21549e7234e4ee342776466f8d3d8ced5f08c
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102508802"
---
# <a name="what-is-azure-private-endpoint"></a>O que é o Ponto de Extremidade Privado do Azure?

O ponto de extremidade privado do Azure é uma interface de rede que conecta você de forma privada e segura a um serviço com tecnologia do Link Privado do Azure. O Ponto de Extremidade Privado usa um endereço IP privado de sua VNet, colocando efetivamente em sua VNet. O serviço pode ser um serviço do Azure, como o Armazenamento do Azure, Azure Cosmos DB, SQL, etc. ou seu próprio [Serviço de Link Privado](private-link-service-overview.md).
  
## <a name="private-endpoint-properties"></a>Propriedades do Ponto de Extremidade Privado 
 Um Ponto de Extremidade Privado especifica as seguintes propriedades: 


|Propriedade  |Descrição |
|---------|---------|
|Nome    |    Um nome exclusivo dentro do grupo de recursos.      |
|Sub-rede    |  A sub-rede para implantar e alocar endereços IP privados de uma rede virtual. Para obter os requisitos de sub-rede, confira a seção Limitações neste artigo.         |
|Recurso do link privado    |   O recurso do link privado para conectar-se usando a ID do recurso ou alias da lista de tipos disponíveis. Um identificador de rede exclusivo é gerado para todo o tráfego enviado para esse recurso.       |
|Sub-recurso de destino   |      O sub-recurso a ser conectado. Cada tipo de recurso do link privado tem opções diferentes para selecionar com base na preferência.    |
|Método de aprovação de conexão    |  Automático ou manual. Com base nas permissões do Azure RBAC (controle de acesso baseado em função), seu ponto de extremidade privado pode ser aprovado automaticamente. Se você tentar se conectar a um recurso de link privado sem o RBAC do Azure, use o método manual para permitir que o proprietário do recurso aprove a conexão.        |
|Mensagem de solicitação     |  Você pode especificar uma mensagem para que as conexões solicitadas sejam aprovadas manualmente. Essa mensagem pode ser usada para identificar uma solicitação específica.        |
|Status da conexão   |   Uma propriedade somente leitura que especifica se o ponto de extremidade privado está ativo. Somente os pontos de extremidade privados no estado aprovado podem ser usados para enviar tráfego. Outros estados disponíveis: <br>-**Aprovado**: A conexão foi aprovada automaticamente ou manualmente e está pronta para ser usada.</br><br>-**Pendente**: A conexão foi criada manualmente e está pendente de aprovação do proprietário do recurso do link privado.</br><br>-**Rejeitado**: A conexão foi rejeitada pelo proprietário do recurso do link privado.</br><br>-**Desconectado**: A conexão foi removida pelo proprietário do recurso do link privado. O ponto de extremidade privado se torna informativo e deve ser excluído para limpeza. </br>|

Estes são alguns detalhes importantes sobre os pontos de extremidade privados: 
- O ponto de extremidade privado viabiliza a conectividade entre os consumidores da mesma VNet, VNets emparelhadas de forma regional, VNets emparelhadas de forma global e local usando a [VPN](https://azure.microsoft.com/services/vpn-gateway/) ou [Rota Expressa](https://azure.microsoft.com/services/expressroute/) e serviços alimentados pelo Link Privado.
 
- As conexões de rede só podem ser iniciadas por clientes conectados ao ponto de extremidade privado. Os provedores de serviço não têm configurações de roteamento para iniciar conexões com os consumidores de serviço. As conexões só podem ser estabelecidas em uma única direção.

- Durante a criação de um ponto de extremidade privado, uma interface de rede somente leitura também é criada para o ciclo de vida do recurso. A interface recebe endereços IP privados dinamicamente da sub-rede que mapeia para o recurso do link privado. O valor do endereço IP privado permanece inalterado para todo o ciclo de vida do ponto de extremidade privado.
 
- O ponto de extremidade privado deve ser implantado na mesma região que a rede virtual. 
 
- O recurso do link privado pode ser implantado em uma região diferente da rede virtual e do ponto de extremidade privado.
 
- Vários pontos de extremidade privados podem ser criados usando o mesmo recurso do link privado. Para uma única rede que usa uma configuração comum do servidor DNS, a melhor prática é usar um único ponto de extremidade privado para determinado recurso do link privado, a fim de evitar entradas duplicadas ou conflitos na resolução de DNS. 
 
- Vários pontos de extremidade privados podem ser criados na mesma sub-rede ou em sub-redes diferentes na mesma rede virtual. Há limites para o número de pontos de extremidade privados que você pode criar em uma assinatura. Para obter detalhes, confira  [Limites do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).

- A assinatura do recurso de link privado também deve ser registrada com o provedor de recursos Microsoft. Network. Para obter detalhes, consulte [provedores de recursos do Azure](../azure-resource-manager/management/resource-providers-and-types.md).

 
## <a name="private-link-resource"></a>Recurso do link privado 
Um recurso do link privado é o destino pretendido de determinado ponto de extremidade privado. Veja a seguir uma lista dos tipos de recursos do link privado disponíveis: 
 
|Nome do recurso do link privado  |Tipo de recurso   |Sub-recursos  |
|---------|---------|---------|
|**Serviço do Link Privado** (seu próprio serviço)   |  Microsoft.Network/privateLinkServices       | empty |
|**Automação do Azure** |  Microsoft.Automation/automationAccounts | Webhook, DSCAndHybridWorker |
|**Banco de Dados SQL do Azure** | Microsoft.Sql/servers    |  SQL Server (sqlServer)        |
|**Azure Synapse Analytics** | Microsoft.Sql/servers    |  SQL Server (sqlServer)        | 
|**Armazenamento do Azure**  | Microsoft.Storage/storageAccounts    |  Blob (blob, blob_secondary)<BR> Tabela (tabela, table_secondary)<BR> Fila (fila, queue_secondary)<BR> Arquivo (arquivo, file_secondary)<BR> Web (Web, web_secondary)        |
|**Azure Data Lake Storage Gen2**  | Microsoft.Storage/storageAccounts    |  Blob (blob, blob_secondary)<BR> Sistema de arquivos Data Lake Gen2 (dfs, dfs_secondary)       |
|**Azure Cosmos DB** | Microsoft.AzureCosmosDB/databaseAccounts    | Sql, MongoDB, Cassandra, Gremlin, Table|
|**Banco de Dados do Azure para PostgreSQL - Servidor Único** | Microsoft.DBforPostgreSQL/servers    | postgresqlServer |
|**Banco de Dados do Azure para MySQL** | Microsoft.DBforMySQL/servers    | mysqlServer |
|**Banco de Dados do Azure para MariaDB** | Microsoft.DBforMariaDB/servers    | mariadbServer |
|**Hub IoT do Azure** | Microsoft.Devices/IotHubs    | iotHub |
|**Cofre da Chave do Azure** | Microsoft.KeyVault/vaults    | cofre |
|**Serviço de Kubernetes do Azure - API Kubernetes** | Microsoft.ContainerService/managedClusters    | gerenciamento |
|**Azure Search** | Microsoft.Search/searchService| searchService|  
|**Registro de Contêiner do Azure** | Microsoft.ContainerRegistry/registries    | Registro |
|**Configuração de Aplicativos do Azure** | Microsoft.Appconfiguration/configurationStores    | configurationStores |
|**Serviço de Backup do Azure** | Microsoft.RecoveryServices/vaults    | cofre |
|**Hub de Eventos do Azure** | Microsoft.EventHub/namespaces    | namespace |
|**Barramento de Serviço do Azure** | Microsoft.ServiceBus/namespaces | namespace |
|**Retransmissão do Azure** | Microsoft.Relay/namespaces | namespace |
|**Grade de Eventos do Azure** | Microsoft.EventGrid/topics    | topic |
|**Grade de Eventos do Azure** | Microsoft.EventGrid/domains    | domínio |
|**Serviço de Aplicativo do Azure** | Microsoft.Web/sites    | sites |
|**Azure Machine Learning** | Microsoft.MachineLearningServices/workspaces    | amlworkspace |
|**SignalR** | Microsoft.SignalRService/SignalR    | signalR |
|**Azure Monitor** | Microsoft. insights/privateLinkScopes    | azuremonitor |
|**Serviços Cognitivos** | (Microsoft. Cognitivaservices/contas    | account |
|**Sincronização de Arquivos do Azure** | Microsoft. StorageSync/storageSyncServices    | AFS |
    
  

  
 
## <a name="network-security-of-private-endpoints"></a>Segurança de rede dos pontos de extremidade privados 
Ao usar pontos de extremidade privados para serviços do Azure, o tráfego é protegido para um recurso do link particular específico. A plataforma realiza um controle de acesso para validar as conexões de rede que atingem apenas o recurso do link particular especificado. Para acessar recursos adicionais dentro do mesmo serviço do Azure, são necessários pontos de extremidade privados adicionais. 
 
Você pode impedir completamente que as cargas de trabalho acessem os pontos de extremidade públicos para se conectar a um serviço compatível do Azure. Esse controle fornece uma camada de segurança de rede adicional para os recursos, fornecendo uma proteção integrada contra exfiltração que impede o acesso a outros recursos hospedados no mesmo serviço do Azure. 
 
## <a name="access-to-a-private-link-resource-using-approval-workflow"></a>Acesso a um recurso do link privado usando o fluxo de trabalho de aprovação 
Você pode se conectar a um recurso do link privado usando os seguintes métodos de aprovação de conexão:
- Aprovado **automaticamente** quando você detém o recurso do link particular específico ou possui permissão para usá-lo. A permissão necessária é baseada no tipo de recurso de link privado no seguinte formato: Microsoft. \<Provider> /<resource_type>/privateEndpointConnectionApproval/action
- Solicitação **manual** quando você não tem a permissão necessária e deseja solicitar acesso. Um fluxo de trabalho de aprovação será iniciado. O ponto de extremidade privado e a conexão do ponto de extremidade particular subsequente serão criados em um estado "Pendente". O proprietário do recurso de link privado é responsável por aprovar a conexão. Depois de aprovado, o ponto de extremidade privado é habilitado para enviar o tráfego normalmente, conforme mostrado no diagrama de fluxo de trabalho de aprovação a seguir.  

![aprovação do fluxo de trabalho](media/private-endpoint-overview/private-link-paas-workflow.png)
 
O proprietário do recurso do link privado pode realizar as seguintes ações em uma conexão de ponto de extremidade privado: 
- Revisão de todos detalhes das conexões de ponto de extremidade privado. 
- Aprovação de uma conexão de ponto de extremidade privado. O ponto de extremidade privado correspondente será habilitado para enviar tráfego para o recurso do link privado. 
- Rejeição de uma conexão de ponto de extremidade privado. O ponto de extremidade privado correspondente será atualizado para refletir o status.
- Exclusão de uma conexão do ponto de extremidade privado. O ponto de extremidade privado correspondente será atualizado com um estado desconectado para refletir a ação. O proprietário do ponto de extremidade privado só pode excluir o recurso nesse momento. 
 
> [!NOTE]
> Somente um ponto de extremidade privado em um estado aprovado pode enviar tráfego para determinado recurso do link privado. 

### <a name="connecting-using-alias"></a>Conexão usando alias
Alias é um moniker exclusivo gerado quando o proprietário do serviço cria o serviço de vínculo privado por trás de um balanceador de carga padrão. O proprietário do serviço pode compartilhar esse alias com os consumidores offline. Os consumidores podem solicitar uma conexão com o serviço de vínculo privado, usando o URI do recurso ou o alias. Se você quiser se conectar usando o alias, deve criar um ponto de extremidade privado usando o método de aprovação de conexão manual. Para usar o método de aprovação de conexão manual, defina o parâmetro de solicitação manual como true durante o fluxo de criação do ponto de extremidade privado. Veja [New-AzPrivateEndpoint](/powershell/module/az.network/new-azprivateendpoint) e [az network private-endpoint create](/cli/azure/network/private-endpoint#az-network-private-endpoint-create) para obter detalhes. 

## <a name="dns-configuration"></a>Configuração de DNS 
Ao se conectar a um recurso do link privado usando um FQDN (nome de domínio totalmente qualificado) como parte da cadeia de conexão, é importante definir corretamente as configurações de DNS para resolver o endereço IP privado alocado. Os serviços do Azure existentes já podem ter uma configuração de DNS para usar ao se conectar por meio de um ponto de extremidade público. Ela precisa ser substituída para se conectar usando o ponto de extremidade privado. 
 
O adaptador de rede associado ao ponto de extremidade privado contém o conjunto completo de informações necessárias para configurar o DNS, incluindo endereços IP privados e FQDN alocados para determinado recurso do link privado. 

Para obter informações detalhadas completas sobre as melhores práticas e recomendações para configurar o DNS para pontos de extremidade privados, examine o [artigo de configuração de DNS do ponto de extremidade privado](private-endpoint-dns.md).



 
## <a name="limitations"></a>Limitações
 
A tabela a seguir inclui uma lista de limitações conhecidas ao usar pontos de extremidade privados: 


|Limitações |Descrição |Atenuação  |
|---------|---------|---------|
|As regras do grupo de segurança de rede (NSG) e as rotas definidas pelo usuário não se aplicam ao ponto de extremidade privado    |O NSG não é compatível com pontos de extremidade privados. Embora as sub-redes que contenham o ponto de extremidade privado possam ter o NSG associado a ela, as regras não entrarão em vigor no tráfego processado pelo ponto de extremidade privado. Você deve [desabilitar a imposição de políticas de rede](disable-private-endpoint-network-policy.md) para implantar pontos de extremidade privados em uma sub-rede. O NSG ainda é aplicado em outras cargas de trabalho hospedadas na mesma sub-rede. As rotas em qualquer sub-rede do cliente usarão um prefixo /32. Alterar o comportamento de roteamento padrão requer um UDR semelhante  | Controle o tráfego usando as regras de NSG para o tráfego de saída nos clientes de origem. Implante rotas individuais com o prefixo /32 para substituir as rotas de ponto de extremidade privado. Os logs de fluxo e as informações de monitoramento do NSG para conexões de saída ainda são compatíveis e podem ser usados        |


## <a name="next-steps"></a>Próximas etapas
- [Criar um ponto de extremidade privado para o banco de dados SQL usando o portal](create-private-endpoint-portal.md)
- [Criar um ponto de extremidade privado para o banco de dados SQL usando o PowerShell](create-private-endpoint-powershell.md)
- [Criar um ponto de extremidade privado para o banco de dados SQL usando a CLI](create-private-endpoint-cli.md)
- [Criar um ponto de extremidade privado para a conta de armazenamento usando o portal](./tutorial-private-endpoint-storage-portal.md)
- [Criar um ponto de extremidade privado para a conta do Azure Cosmos usando o portal](../cosmos-db/how-to-configure-private-endpoints.md)
- [Criar seu próprio serviço de Link Privado usando o Azure PowerShell](create-private-link-service-powershell.md)
- [Criar seu próprio link privado para o banco de dados do Azure para PostgreSQL-servidor único usando o portal](../postgresql/howto-configure-privatelink-portal.md)
- [Criar seu próprio Link Privado para o Banco de Dados do Azure para PostgreSQL - servidor único usando a CLI](../postgresql/howto-configure-privatelink-cli.md)
- [Criar seu próprio link privado para o banco de dados do Azure para MySQL usando o portal](../mysql/howto-configure-privatelink-portal.md)
- [Criar seu próprio Link Privado para o Banco de Dados do Azure para MySQL usando a CLI](../mysql/howto-configure-privatelink-cli.md)
- [Criar seu próprio link privado para o banco de dados do Azure para MariaDB usando o portal](../mariadb/howto-configure-privatelink-portal.md)
- [Criar seu próprio Link Privado para o Banco de Dados do Azure para MariaDB usando a CLI](../mariadb/howto-configure-privatelink-cli.md)
- [Crie seu próprio link privado para Azure Key Vault usando o portal e a CLI](../key-vault/general/private-link-service.md)
