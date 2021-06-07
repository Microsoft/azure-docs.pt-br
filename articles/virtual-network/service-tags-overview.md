---
title: Visão geral das marcas de serviço do Azure
titlesuffix: Azure Virtual Network
description: Saiba mais sobre as marcas de serviço. As marcas de serviço ajudam a minimizar a complexidade da criação de regras de segurança.
services: virtual-network
documentationcenter: na
author: allegradomel
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/30/2020
ms.author: kumud
ms.reviewer: kumud
ms.openlocfilehash: 2d14ca2423d34926a9e297823a6515c2c5dde06a
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105607109"
---
# <a name="virtual-network-service-tags"></a>Marcas de serviço de rede virtual
<a name="network-service-tags"></a>

Uma marca de serviço representa um grupo de prefixos de endereço IP de um determinado serviço do Azure. A Microsoft gerencia os prefixos de endereço englobados pela marca de serviço e atualiza automaticamente a marca de serviço em caso de alteração de endereços, minimizando a complexidade de atualizações frequentes das regras de segurança de rede.

Você pode usar marcas de serviço para definir os controles de acesso à rede em [grupos de segurança de rede](./network-security-groups-overview.md#security-rules) ou no [Firewall do Azure](../firewall/service-tags.md). Use marcas de serviço em vez de endereços IP específicos ao criar regras de segurança. Ao especificar o nome da marca de serviço, como **ApiManagement**, no campo de *origem* ou *destino* apropriado de uma regra, você pode permitir ou negar o tráfego para o serviço correspondente. 

> [!NOTE] 
> A partir de março de 2021, você também pode usar marcas de serviço no lugar de intervalos de IP explícitos em [rotas definidas pelo usuário](./virtual-networks-udr-overview.md). Esse recurso está atualmente em visualização pública. 

Você pode usar as marcas de serviço para obter o isolamento da rede e proteger os recursos do Azure da Internet em geral ao acessar os serviços do Azure que tenham pontos de extremidade públicos. Crie regras de entrada/saída para o grupo de segurança de rede a fim de negar o tráfego de/para a **Internet** e permitir o tráfego de/para o **AzureCloud** ou outras [marcas de serviço disponíveis](#available-service-tags) de serviços específicos do Azure.

![Isolamento de rede dos serviços do Azure usando marcas de serviço](./media/service-tags-overview/service_tags.png)

## <a name="available-service-tags"></a>Marcas de serviço disponíveis
A tabela a seguir inclui todas as marcas de serviço disponíveis para uso em regras do [grupo de segurança de rede](./network-security-groups-overview.md#security-rules).

As colunas indicam se a marca:

- É adequada para regras que abrangem o tráfego de entrada ou de saída.
- Dá suporte a um escopo [regional](https://azure.microsoft.com/regions).
- Pode ser usada em regras do [Firewall do Azure](../firewall/service-tags.md).

Por padrão, as marcas de serviço refletem os intervalos para toda a nuvem. Algumas marcas de serviço também permitem um controle mais granular ao restringir os intervalos de IP correspondentes a uma região especificada. Por exemplo, a marca de serviço **Armazenamento** representa o Armazenamento do Azure para toda a nuvem, mas **Storage.WestUS** limita o intervalo apenas aos intervalos de endereços IP de armazenamento da região do Oeste dos EUA. A tabela a seguir indica se cada marca de serviço dá suporte a esse escopo regional.  

| Marca | Finalidade | É possível usar entrada ou saída? | Pode ser regional? | É possível usar com o Firewall do Azure? |
| --- | -------- |:---:|:---:|:---:|
| **ActionGroup** | Grupo de ações. | Entrada | Não | Não |
| **ApiManagement** | Tráfego do gerenciamento de implantações dedicadas do Gerenciamento de API do Azure. <br/><br/>*Observação:* essa marca representa o ponto de extremidade do serviço de Gerenciamento de API do Azure para o plano de controle por região. Isso permite que os clientes executem operações de gerenciamento nas APIs, Operações, Políticas, NamedValues configurados no serviço de Gerenciamento de API.  | Entrada | Sim | Sim |
| **ApplicationInsightsAvailability** | Disponibilidade do Application Insights. | Entrada | Não | Não |
| **AppConfiguration** | Configuração de Aplicativos. | Saída | Não | Não |
| **AppService**    | Serviço de Aplicativo do Azure. Essa marca é recomendada para regras de segurança de saída para aplicativos Web e aplicativos de funções.  | Saída | Sim | Sim |
| **AppServiceManagement** | Tráfego de gerenciamento para implantações dedicadas ao Ambiente do Serviço de Aplicativo. | Ambos | Não | Sim |
| **AzureActiveDirectory** | Azure Active Directory. | Saída | Não | Sim |
| **AzureActiveDirectoryDomainServices** | Tráfego de gerenciamento para implantações dedicadas ao Azure Active Directory Domain Services. | Ambos | Não | Sim |
| **AzureAdvancedThreatProtection** | Proteção Avançada contra Ameaças do Azure. | Saída | Não | Não |
| **AzureAPIForFHIR** | API do Azure para FHIR (recursos de interoperabilidade do Fast Healthcare).<br/><br/> *Observação: essa marca não é configurável no momento por meio do portal do Azure.*| Saída | Não | Não |
| **AzureArcInfrastructure** | Servidores habilitados para Arc do Azure, Azure Arc habilitado kubernetes e tráfego de configuração de convidado.<br/><br/>*Observação:* Essa marca tem uma dependência nas marcas **AzureActiveDirectory**,**AzureTrafficManager** e **AzureResourceManager** . *Essa marca não é configurável no momento por meio do portal do Azure*.| Saída | Não | Sim |
| **AzureBackup** |Backup do Azure.<br/><br/>*Observação:* essa marca tem uma dependência nas marcas **Armazenamento** e **AzureActiveDirectory**. | Saída | Não | Sim |
| **AzureBotService** | Serviço de Bot do Azure. | Saída | Não | Não |
| **AzureCloud** | Todos os [endereços IP públicos do datacenter](https://www.microsoft.com/download/details.aspx?id=56519). | Saída | Sim | Sim |
| **AzureCognitiveSearch** | Azure Cognitive Search. <br/><br/>Esta marca ou os endereços IP cobertos por essa marca podem ser usados para conceder aos indexadores o acesso seguro a fontes de dados. Consulte a [documentação de conexão do indexador](../search/search-indexer-troubleshooting.md#connection-errors) para obter mais detalhes. <br/><br/> *Observação*: o IP do serviço de pesquisa não está incluído na lista de intervalos de IP para essa marca de serviço e **também precisa ser adicionado** ao firewall de IP de fontes de dados. | Entrada | Não | Não |
| **AzureConnectors** | Essa marca representa os endereços IP usados para conectores gerenciados que fazem retornos de chamada de webhook de entrada para o serviço de aplicativos lógicos do Azure e chamadas de saída para seus respectivos serviços, por exemplo, armazenamento do Azure ou hubs de eventos do Azure. | Entrada/saída | Sim | Sim |
| **AzureContainerRegistry** | Registro de Contêiner do Azure. | Saída | Sim | Sim |
| **AzureCosmosDB** | Azure Cosmos DB. | Saída | Sim | Sim |
| **AzureDatabricks** | Azure Databricks. | Ambos | Não | Não |
| **AzureDataExplorerManagement** | Gerenciamento do Azure Data Explorer. | Entrada | Não | Não |
| **AzureDataLake** | Azure Data Lake Storage Gen1. | Saída | Não | Sim |
| **AzureDevSpaces** | Azure Dev Spaces. | Saída | Não | Não |
| **AzureDevOps** | Operações de desenvolvimento do Azure.<br/><br/>*Observação: essa marca não é configurável no momento por meio do portal do Azure*| Entrada | Não | Sim |
| **AzureDigitalTwins** | Gêmeos digital do Azure.<br/><br/>*Observação:* Esta marca ou os endereços IP cobertos por essa marca podem ser usados para restringir o acesso a pontos de extremidade configurados para rotas de eventos. *Essa marca não é configurável no momento por meio do portal do Azure* | Entrada | Não | Sim |
| **AzureEventGrid** | Grade de Eventos do Azure. | Ambos | Não | Não |
| **AzureFrontDoor.Frontend** <br/> **AzureFrontDoor.Backend** <br/> **AzureFrontDoor.FirstParty**  | Azure Front Door. | Ambos | Não | Não |
| **AzureInformationProtection** | Proteção de Informações do Azure.<br/><br/>*Observação:* essa marca tem uma dependência nas marcas **AzureActiveDirectory**, **AzureFrontDoor.Frontend** e **AzureFrontDoor.FirstParty**. | Saída | Não | Não |
| **AzureIoTHub** | Hub IoT do Azure. | Saída | Não | Não |
| **AzureKeyVault** | Azure Key Vault.<br/><br/>*Observação:* essa marca tem uma dependência na marca **AzureActiveDirectory**. | Saída | Sim | Sim |
| **AzureLoadBalancer** | O balanceador de carga de infraestrutura do Azure. A marca é traduzida para o [endereço IP virtual do host](./network-security-groups-overview.md#azure-platform-considerations) (168.63.129.16), no qual as sondas de integridade do Azure se originam. Isso inclui apenas o tráfego de investigação, não o tráfego real para o recurso de back-end. Se não estiver usando o Azure Load Balancer, você poderá substituir essa regra. | Ambos | Não | Não |
| **AzureMachineLearning** | Azure Machine Learning. | Ambos | Não | Sim |
| **AzureMonitor** | Log Analytics, Application Insights, AzMon e métricas personalizadas (pontos de extremidade GiG).<br/><br/>*Observação:* Por Log Analytics, a marca de **armazenamento** também é necessária. Se os agentes do Linux forem usados, a marca **GuestAndHybridManagement** também será necessária. | Saída | Não | Sim |
| **AzureOpenDatasets** | Conjunto de Dados em Aberto no Azure.<br/><br/>*Observação:* essa marca tem uma dependência nas marcas **AzureFrontDoor.Frontend** e **Storage**. | Saída | Não | Não |
| **AzurePlatformDNS** | O serviço DNS de infraestrutura básica (padrão).<br/><br>Você pode usar essa marca para desabilitar o DNS padrão. Tenha cuidado ao usar essa marca. Recomendamos que você leia as [Considerações sobre a plataforma do Azure](./network-security-groups-overview.md#azure-platform-considerations). Também recomendamos que você execute os testes antes de usar essa marca. | Saída | Não | Não |
| **AzurePlatformIMDS** | O IMDS (Serviço de Metadados de Instância do Azure), que é um serviço de infraestrutura básico.<br/><br/>Você pode usar essa marca para desabilitar o DNS padrão. Tenha cuidado ao usar essa marca. Recomendamos que você leia as [Considerações sobre a plataforma do Azure](./network-security-groups-overview.md#azure-platform-considerations). Também recomendamos que você execute os testes antes de usar essa marca. | Saída | Não | Não |
| **AzurePlatformLKM** | Serviço de gerenciamento de chaves ou licenciamento do Windows.<br/><br/>Você pode usar essa marca para desabilitar os padrões de licenciamento. Tenha cuidado ao usar essa marca. Recomendamos que você leia as [Considerações sobre a plataforma do Azure](./network-security-groups-overview.md#azure-platform-considerations).  Também recomendamos que você execute os testes antes de usar essa marca. | Saída | Não | Não |
| **AzureResourceManager** | Azure Resource Manager. | Saída | Não | Não |
| **AzureSignalR** | Azure SignalR. | Saída | Não | Não |
| **AzureSiteRecovery** | Azure Site Recovery.<br/><br/>*Observação:* essa marca tem uma dependência nas marcas **AzureActiveDirectory**, **AzureKeyVault**, **EventHub**,**GuestAndHybridManagement** e **Storage**. | Saída | Não | Não |
| **AzureTrafficManager** | Endereços IP de investigação do Gerenciador de Tráfego do Azure.<br/><br/>Saiba mais sobre os endereços IP de investigação no Gerenciador de Tráfego nas [Perguntas frequentes sobre o Gerenciador de Tráfego do Azure](../traffic-manager/traffic-manager-faqs.md). | Entrada | Não | Sim |  
| **BatchNodeManagement** | Tráfego de gerenciamento para implantações dedicadas ao Lote do Azure. | Ambos | Não | Sim |
| **CognitiveServicesManagement** | Os intervalos de endereços para o tráfego dos Serviços Cognitivos do Azure. | Ambos | Não | Não |
| **DataFactory**  | Fábrica de dados do Azure | Ambos | Não | Não |
| **DataFactoryManagement** | Tráfego de gerenciamento do Azure Data Factory. | Saída | Não | Não |
| **Dynamics365ForMarketingEmail** | Os intervalos de endereços para o serviço de email de marketing do Dynamics 365. | Saída | Sim | Não |
| **EventHub** | Hubs de Eventos do Azure. | Saída | Sim | Sim |
| **GatewayManager** | Tráfego de gerenciamento para implantações dedicadas ao Gateway de VPN e ao Gateway de Aplicativo do Azure. | Entrada | Não | Não |
| **GuestAndHybridManagement** | Configuração de convidado e Automação do Azure. | Saída | Não | Sim |
| **HDInsight** | Azure HDInsight. | Entrada | Sim | Não |
| **Internet** | Esse espaço de endereços IP fica fora da rede virtual e que pode ser acessado por meio da Internet pública.<br/><br/>O intervalo de endereços inclui o [espaço de endereço IP público de propriedade do Azure](https://www.microsoft.com/download/details.aspx?id=41653). | Ambos | Não | Não |
| **LogicApps** | Aplicativos Lógicos. | Ambos | Não | Não |
| **LogicAppsManagement** | Tráfego de gerenciamento para Aplicativos Lógicos. | Entrada | Não | Não |
| **MicrosoftCloudAppSecurity** | Microsoft Cloud App Security. | Saída | Não | Não |
| **MicrosoftContainerRegistry** | Registro de contêiner de imagens de contêiner da Microsoft. <br/><br/>*Observação:* essa marca tem uma dependência na marca **AzureFrontDoor.FirstParty**. | Saída | Sim | Sim |
| **PowerBI** | PowerBi. *Observação: essa marca não é configurável no momento por meio do portal do Azure.* | Ambos | Não | Não|
| **PowerQueryOnline** | Power Query Online. | Ambos | Não | Não |
| **Barramento de Serviço** | O tráfego do Barramento de Serviço do Azure que usa a camada de serviço Premium. | Saída | Sim | Sim |
| **ServiceFabric** | Azure Service Fabric.<br/><br/>*Observação:* essa marca representa o ponto de extremidade do serviço Service Fabric do plano de controle por região. Ele permite que os clientes executem operações de gerenciamento para os clusters do Service Fabric em sua VNET (ponto de extremidade, por exemplo, https:// westus.servicefabric.azure.com) | Ambos | Não | Não |
| **Sql** | Banco de dados SQL do Azure, banco de dados do Azure para MySQL, banco de dados do Azure para PostgreSQL e Azure Synapse Analytics.<br/><br/>*Observação:* essa marca representa o serviço, mas não as instâncias específicas do serviço. Por exemplo, a marca representa o serviço Banco de Dados SQL do Azure, mas não um banco de dados ou servidor SQL específico. Essa marca não se aplica à instância gerenciada do SQL. | Saída | Sim | Sim |
| **SqlManagement** | Tráfego de gerenciamento para implantações dedicadas do SQL. | Ambos | Não | Sim |
| **Storage** | Armazenamento do Microsoft Azure. <br/><br/>*Observação:* essa marca representa o serviço, mas não as instâncias específicas do serviço. Por exemplo, a marca representa o serviço Armazenamento do Azure, mas não uma conta do Armazenamento do Azure específica. | Saída | Sim | Sim |
| **StorageSyncService** | Serviço de Sincronização de Armazenamento. | Ambos | Não | Não |
| **WindowsVirtualDesktop** | Área de Trabalho Virtual do Windows. | Ambos | Não | Sim |
| **VirtualNetwork** | O espaço de endereço de rede virtual (todos os intervalos de endereço IP definidos para a rede virtual), todos os espaços de endereço locais conectados, redes virtuais [emparelhadas](virtual-network-peering-overview.md), redes virtuais conectadas a um [gateway de rede virtual](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%3ftoc.json), o [endereço IP virtual do host](./network-security-groups-overview.md#azure-platform-considerations) e os prefixos de endereços usados em [rotas definidas pelo usuário](virtual-networks-udr-overview.md). Essa marca também pode conter rotas padrão. | Ambos | Não | Não |

>[!NOTE]
>No modelo de implantação clássico (antes do Azure Resource Manager), há suporte para um subconjunto das marcas listadas na tabela anterior. Essas marcas são escritas de forma diferente:
>
>| Grafia clássica | Marca equivalente do Resource Manager |
>|---|---|
>| BALANCEADORDECARGA_AZURE | AzureLoadBalancer |
>| INTERNET | Internet |
>| REDE_VIRTUAL | VirtualNetwork |

> [!NOTE]
> As marcas de serviços do Azure indicam os prefixos de endereços de uma nuvem específica a ser usada. Por exemplo, os intervalos de IP subjacentes que correspondem ao valor da marca **SQL** na nuvem pública do Azure serão diferentes dos intervalos subjacentes da nuvem do Azure na China.

> [!NOTE]
> Se você implementar um [ponto de extremidade de serviço de rede virtual](virtual-network-service-endpoints-overview.md) em um serviço, como o Armazenamento do Azure ou o Banco de Dados SQL do Azure, o Azure adicionará uma [rota](virtual-networks-udr-overview.md#optional-default-routes) para uma sub-rede de rede virtual para o serviço. Os prefixos de endereços na rota são os mesmos prefixos de endereços, ou intervalos CIDR, aos correspondentes da marca de serviço.

## <a name="service-tags-on-premises"></a>Marcas de serviço locais  
Você pode obter as informações da marca de serviço e dos intervalos atuais para incluir como parte das configurações de firewall local. Essas informações estão na lista pontual atual dos intervalos de IP que correspondem a cada marca de serviço. Você pode obter as informações de forma programática ou por meio do download de um arquivo JSON, como descrito nas seções a seguir.

### <a name="use-the-service-tag-discovery-api-public-preview"></a>Usar a API de Descoberta de Marca de Serviço (versão prévia pública)
Você pode recuperar de forma programática a lista atual de marcas de serviço com os detalhes dos intervalos de endereços IP:

- [REST](/rest/api/virtualnetwork/servicetags/list)
- [PowerShell do Azure](/powershell/module/az.network/Get-AzNetworkServiceTag)
- [CLI do Azure](/cli/azure/network#az-network-list-service-tags)

> [!NOTE]
> Embora esteja em versão prévia pública, a API de Descoberta pode retornar informações que estão menos atualizadas do que as informações retornadas pelos downloads JSON. (Confira a próxima seção.)


### <a name="discover-service-tags-by-using-downloadable-json-files"></a>Descobrir marcas de serviço usando download de arquivos JSON 
Você pode baixar arquivos JSON que contêm a lista atual de marcas de serviço com detalhes de intervalos de endereços IP. Essas listas são atualizadas e publicadas semanalmente. Os locais de cada nuvem são:

- [Público do Azure](https://www.microsoft.com/download/details.aspx?id=56519)
- [Governo dos EUA para Azure](https://www.microsoft.com/download/details.aspx?id=57063)  
- [Azure China:](https://www.microsoft.com/download/details.aspx?id=57062) 
- [Azure Alemanha](https://www.microsoft.com/download/details.aspx?id=57064)   

Os intervalos de endereços IP nesses arquivos estão na notação CIDR. 

> [!NOTE]
>Um subconjunto com essas informações foi publicado em arquivos XML para o [Público do Azure](https://www.microsoft.com/download/details.aspx?id=41653), [Azure China](https://www.microsoft.com/download/details.aspx?id=42064) e [Azure Alemanha](https://www.microsoft.com/download/details.aspx?id=54770). Esses downloads XML serão preteridos até 30 de junho de 2020 e não estarão mais disponíveis após essa data. Você deve migrar usando a API de Descoberta ou os downloads de arquivos JSON, como descrito nas seções anteriores.

### <a name="tips"></a>Dicas 
- Você pode detectar as atualizações de uma publicação em comparação com a próxima observando os valores *changeNumber* no arquivo JSON. Cada subseção (por exemplo, **Storage.WestUS**) tem seu *changeNumber*, que aumenta conforme ocorrem as alterações. O nível superior do *changeNumber* do arquivo aumenta quando qualquer uma das subseções é alterada.
- Para obter exemplos de como analisar as informações de marcas de serviço (por exemplo, para obter todos os intervalos de endereços para Armazenamento no Oeste dos EUA), confira a documentação [API de Descoberta de Marca de Serviço do PowerShell](/powershell/module/az.network/Get-AzNetworkServiceTag).
- Quando novos endereços IP forem adicionados às marcas de serviço, eles não serão usados no Azure por pelo menos uma semana. Isso dá tempo para atualizar todos os sistemas que talvez precisem controlar os endereços IP associados às marcas de serviço.

## <a name="next-steps"></a>Próximas etapas
- Saiba como [criar um grupo de segurança de rede](tutorial-filter-network-traffic.md).
