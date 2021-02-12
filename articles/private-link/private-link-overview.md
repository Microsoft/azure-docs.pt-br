---
title: O que é o Link Privado do Azure?
description: Visão geral dos recursos do, da arquitetura e da implementação do Link Privado do Azure. Saiba como os Pontos de Extremidade Privados do Azure e o serviço de Link Privado do Azure funcionam e como usá-los.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: overview
ms.date: 01/28/2021
ms.author: allensu
ms.custom: fasttrack-edit, references_regions
ms.openlocfilehash: 681e4efc8a9edda896249fdc939d19b4f644f246
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98986472"
---
# <a name="what-is-azure-private-link"></a>O que é o Link Privado do Azure? 
O Link Privado do Azure lhe permite acessar os serviços de PaaS do Azure (por exemplo, Armazenamento do Azure e Banco de Dados SQL) e serviços de parceiros/de propriedade de clientes hospedados no Azure em um [ponto de extremidade privado](private-endpoint-overview.md) em sua rede virtual.

O tráfego entre sua rede virtual e o serviço viaja a rede de backbone da Microsoft. Expor seu serviço à Internet pública não é mais necessário. Você pode criar o próprio [serviço de link privado](private-link-service-overview.md) em sua rede virtual e oferecê-lo aos seus clientes. A configuração e o consumo usando o Link Privado do Azure são consistentes entre os serviços de parceiro de PaaS do Azure, de propriedade do cliente e de parceiros compartilhados.

> [!IMPORTANT]
> O Link Privado do Azure já está em disponibilidade geral. O Ponto de Extremidade Privado e o serviço de Link Privado (serviço por trás do Standard Load Balancer) estão em disponibilidade geral. O Azure PaaS diferente será integrado ao Link Privado do Azure em datas diferentes. Verifique a seção de [disponibilidade](#availability) neste artigo para ter um status preciso do PaaS do Azure no Link Privado. Para conhecer as limitações conhecidas, confira [Ponto de Extremidade Privado](private-endpoint-overview.md#limitations) e [Serviço de Link Privado](private-link-service-overview.md#limitations). 

:::image type="content" source="./media/private-link-overview/private-link-center.png" alt-text="Centro de Link Privado do Azure no portal do Azure" border="false":::

## <a name="key-benefits"></a>Principais benefícios
O Link Privado do Azure fornece os seguintes benefícios:  
- **Acesse os serviços de forma privada na plataforma Azure**: Conecte sua rede virtual a serviços no Azure sem um endereço IP público na origem ou no destino. Os provedores de serviços podem renderizar seus serviços em sua rede virtual e os consumidores podem acessar esses serviços em sua rede virtual local. A plataforma de Link Privado manipulará a conectividade entre o consumidor e os serviços na rede de backbone do Azure. 
 
- **Redes locais e emparelhadas**: Acesse serviços em execução no Azure do local por meio de emparelhamento privado do ExpressRoute, túneis de VPN e redes virtuais emparelhadas usando pontos de extremidade privados. Não é necessário configurar o emparelhamento da Microsoft no ExpressRoute nem atravessar a Internet para acessar o serviço. O Link Privado fornece uma forma segura de migrar cargas de trabalho para o Azure.
 
- **Proteção contra vazamento de dados**: um ponto de extremidade privado é mapeado para uma instância de um recurso de PaaS em vez de todo o serviço. Os consumidores só podem se conectar ao recurso específico. O acesso a qualquer outro recurso no serviço é bloqueado. Esse mecanismo fornece proteção contra riscos de vazamento de dados. 
 
- **Alcance Global**: Conecte-se de maneira privada aos serviços executados em outras regiões. A rede virtual do consumidor pode estar na região A e pode se conectar aos serviços por trás do Link Privado na região B.  
 
- **Estenda para seus próprios serviços**: Habilite a mesma experiência e a mesma funcionalidade para renderizar seu serviço de maneira privada para consumidores no Azure. Colocando seu serviço atrás de um Azure Load Balancer padrão, você pode habilitá-lo para o Link Privado. O consumidor pode então se conectar diretamente ao seu serviço usando um ponto de extremidade privado em sua rede virtual. Você pode gerenciar as solicitações de conexão usando um fluxo de chamada de aprovação. O Link Privado do Azure funciona para consumidores e serviços que pertencem a locatários diferentes do Azure Active Directory. 

## <a name="availability"></a>Disponibilidade 
 A tabela a seguir lista os serviços de Link Privado e as regiões em que estão disponíveis. 

|Serviços com suporte  |Regiões disponíveis | Considerações adicionais | Status  |
|:-------------------|:-----------------|:----------------|:--------|
|Serviços de Link Privado atrás do Azure Load Balancer padrão | Todas as regiões públicas<br/> Todas as regiões do Governo<br/>Todas as regiões da China  | Com suporte no Standard Load Balancer | GA <br/> [Saiba como criar um serviço de link privado.](create-private-link-service-portal.md) |
| Armazenamento de Blobs do Azure (incluindo Data Lake Storage Gen2)       |  Todas as regiões públicas<br/> Todas as regiões do Governo       |  Com suporte no Tipo de Conta de Uso Geral V2 | GA <br/> [Saiba como criar um ponto de extremidade privado para o armazenamento de blobs.](tutorial-private-endpoint-storage-portal.md)  |
| Arquivos do Azure | Todas as regiões públicas<br/> Todas as regiões do Governo      | |   GA <br/> [Saiba como criar pontos de extremidade de rede de Arquivos do Azure.](../storage/files/storage-files-networking-endpoints.md)   |
| Sincronização de Arquivos do Azure | Todas as regiões públicas      | |   GA <br/> [Saiba como criar pontos de extremidade de rede de Arquivos do Azure.](../storage/files/storage-sync-files-networking-endpoints.md)   |
| Armazenamento de Filas do Azure       |  Todas as regiões públicas<br/> Todas as regiões do Governo       |  Com suporte no Tipo de Conta de Uso Geral V2 | GA <br/> [Saiba como criar um ponto de extremidade privado para o armazenamento de filas.](tutorial-private-endpoint-storage-portal.md) |
| Armazenamento da tabela do Azure       |  Todas as regiões públicas<br/> Todas as regiões do Governo       |  Com suporte no Tipo de Conta de Uso Geral V2 | GA <br/> [Saiba como criar um ponto de extremidade privado para o armazenamento de tabelas.](tutorial-private-endpoint-storage-portal.md)  |
|  Banco de Dados SQL do Azure         | Todas as regiões públicas <br/> Todas as regiões do Governo<br/>Todas as regiões da China      |  Com suporte para [política de conexão](../azure-sql/database/connectivity-architecture.md#connection-policy) de Proxy | GA <br/> [Saiba como criar um ponto de extremidade privado para o SQL do Azure](create-private-endpoint-portal.md)      |
|Azure Synapse Analytics| Todas as regiões públicas <br/> Todas as regiões do Governo |  Com suporte para [política de conexão](../azure-sql/database/connectivity-architecture.md#connection-policy) de Proxy |GA <br/> [Saiba como criar um ponto de extremidade privado para o Azure Synapse Analytics.](../azure-sql/database/private-endpoint-overview.md)|
|Azure Cosmos DB|  Todas as regiões públicas<br/> Todas as regiões do Governo</br> Todas as regiões da China | |GA <br/> [Saiba como criar um ponto de extremidade privado para o Cosmos DB.](./tutorial-private-endpoint-cosmosdb-portal.md)|
|  Banco de Dados do Azure para PostgreSQL – Servidor único         | Todas as regiões públicas <br/> Todas as regiões do Governo<br/>Todas as regiões da China     | Compatível com os tipos de preço uso geral e otimizado para memória | GA <br/> [Saiba como criar um ponto de extremidade privado para o Banco de Dados do Azure para PostgreSQL.](../postgresql/concepts-data-access-and-security-private-link.md)      |
|  Banco de Dados do Azure para MySQL         | Todas as regiões públicas<br/> Todas as regiões do Governo<br/>Todas as regiões da China      |  | GA <br/> [Saiba como criar um ponto de extremidade privado para o Banco de Dados do Azure para MySQL.](../mysql/concepts-data-access-security-private-link.md)     |
|  Banco de Dados do Azure para MariaDB         | Todas as regiões públicas<br/> Todas as regiões do Governo<br/>Todas as regiões da China     |  | GA <br/> [Saiba como criar um ponto de extremidade privado para o Banco de Dados do Azure para MariaDB.](../mariadb/concepts-data-access-security-private-link.md)      |
|  Gêmeos Digitais do Azure         | Todas as regiões públicas com suporte nos Gêmeos Digitais do Azure     |  | Versão Prévia <br/> [Saiba como criar um ponto de extremidade privado para os Gêmeos Digitais do Azure.](../digital-twins/how-to-enable-private-link.md)      |
|  Cofre de Chave do Azure         | Todas as regiões públicas<br/> Todas as regiões do Governo      |  | GA   <br/> [Saiba como criar um ponto de extremidade privado para o Azure Key Vault.](../key-vault/general/private-link-service.md)   |
|Serviço de Kubernetes do Azure – API Kubernetes | Todas as regiões públicas      |  | GA   <br/> [Saiba como criar um ponto de extremidade privado para Serviço de Kubernetes do Azure.](../aks/private-clusters.md)   |
|Azure Search | Todas as regiões públicas <br/> Todas as regiões do Governo | Compatível com o serviço no modo privado | GA   <br/> [Saiba como criar um ponto de extremidade privado para o Azure Search.](../search/service-create-private-endpoint.md)    |
|Registro de Contêiner do Azure | Todas as regiões públicas<br/> Todas as regiões do Governo    | Compatível com nível Premium do registro de contêiner. [Selecione para camadas](../container-registry/container-registry-skus.md)| GA   <br/> [Saiba como criar um ponto de extremidade privado para o Registro de Contêiner do Azure.](../container-registry/container-registry-private-link.md)   |
|Configuração de Aplicativo do Azure | Todas as regiões públicas      |  | Visualização  </br> [Saiba como criar um ponto de extremidade privado para a Configuração de Aplicativos do Azure](../azure-app-configuration/concept-private-endpoint.md) |
|Serviço de Backup do Azure | Todas as regiões públicas<br/> Todas as regiões do Governo   |  | GA   <br/> [Saiba como criar um ponto de extremidade privado para o Backup do Azure.](../backup/private-endpoints.md)   |
|Hub de Eventos do Azure | Todas as regiões públicas<br/>Todas as regiões do Governo      |   | GA   <br/> [Saiba como criar um ponto de extremidade privado para o Hub de Eventos do Azure.](../event-hubs/private-link-service.md)  |
|Barramento de Serviço do Azure | Todas as regiões públicas<br/>Todas as regiões do Governo  | Com suporte com a camada Premium do Barramento de Serviço do Azure. [Selecione para camadas](../service-bus-messaging/service-bus-premium-messaging.md) | GA   <br/> [Saiba como criar um ponto de extremidade privado para o Barramento de Serviço do Azure.](../service-bus-messaging/private-link-service.md)    |
|Retransmissão do Azure | Todas as regiões públicas      |  | Visualização <br/> [Saiba como criar um ponto de extremidade privado para a Retransmissão do Azure.](../azure-relay/private-link-service.md)  |
|Grade de Eventos do Azure| Todas as regiões públicas<br/> Todas as regiões do Governo       |  | GA   <br/> [Saiba como criar um ponto de extremidade privado para a Grade de Eventos do Azure.](../event-grid/network-security.md) |
|Aplicativos Web do Azure | Todas as regiões públicas      | Com suporte com o plano PremiumV2, PremiumV3 ou Function Premium  | GA   <br/> [Saiba como criar um ponto de extremidade privado para Aplicativos Web do Azure.](./tutorial-private-endpoint-webapp-portal.md)   |
|Azure Machine Learning | Todas as regiões públicas    |  | GA   <br/> [Saiba como criar um ponto de extremidade privado para o Azure Machine Learning.](../machine-learning/how-to-configure-private-link.md)   |
| Automação do Azure  | Todas as regiões públicas<br/> Todas as regiões do Governo |  | Visualização </br> [Saiba como criar um ponto de extremidade privado para a Automação do Azure.](../automation/how-to/private-link-security.md)| |
| Hub IoT do Azure | Todas as regiões públicas    |  | GA   <br/> [Saiba como criar um ponto de extremidade privado para o Hub IoT do Azure.](../iot-hub/virtual-network-support.md) |
| Azure SignalR | LESTE DOS EUA, CENTRO-SUL DOS EUA,<br/>OESTE DOS EUA 2, Todas as regiões da China      |  | Visualizar   <br/> [Saiba como criar um ponto de extremidade privado para o Azure SignalR.](../azure-signalr/howto-private-endpoints.md)   |
| Azure Monitor <br/>(Log Analytics e Application Insights) | Todas as regiões públicas      |  | GA   <br/> [Saiba como criar um ponto de extremidade privado para o Azure Monitor.](../azure-monitor/platform/private-link-security.md)   | 
| Lote do Azure | Todas as regiões públicas, exceto: Alemanha CENTRAL, Nordeste da ALEMANHA <br/> Todas as regiões do Governo  | | GA <br/> [Saiba como criar um ponto de extremidade privado para o Lote do Azure.](../batch/private-connectivity.md) |
|Fábrica de dados do Azure | Todas as regiões públicas<br/> Todas as regiões do Governo<br/>Todas as regiões da China    | As credenciais devem ser armazenadas no Azure Key Vault| GA   <br/> [Saiba como criar um ponto de extremidade privado para o Azure Data Factory.](../data-factory/data-factory-private-link.md)   |
|Azure Managed Disks | Todas as regiões públicas<br/> Todas as regiões do Governo<br/>Todas as regiões da China    | [Clique aqui para ver as limitações conhecidas](../virtual-machines/disks-enable-private-links-for-import-export-portal.md#limitations) | GA   <br/> [Saiba como criar um ponto de extremidade privado para os Azure Managed Disks.](../virtual-machines/disks-enable-private-links-for-import-export-portal.md)   |



Para obter as notificações mais recentes, confira a [página de atualizações de Link Privado do Azure](https://azure.microsoft.com/updates/?product=private-link).

## <a name="logging-and-monitoring"></a>Log e monitoramento

O Link Privado do Azure tem integração ao Azure Monitor. Essa combinação permite:

 - O arquivamento dos logs em uma conta de armazenamento.
 - A transmissão de eventos para o Hub de Eventos.
 - O registro em log do Azure Monitor.

Você pode acessar as seguintes informações no Azure Monitor: 
- **Ponto de extremidade privado**: 
    - dados processados pelo Ponto de Extremidade Privado (ENTRADA/SAÍDA)
 
- **Serviço de Link Privado**:
    - dados processados pelo serviço de Link Privado (ENTRADA/SAÍDA)
    - Disponibilidade da porta NAT  
 
## <a name="pricing"></a>Preços   
Para obter detalhes de preço, confira [Preço do Link Privado do Azure](https://azure.microsoft.com/pricing/details/private-link/).
 
## <a name="faqs"></a>Perguntas frequentes  
Para perguntas frequentes, confira [Perguntas frequentes sobre Link Privado do Azure](private-link-faq.md).
 
## <a name="limits"></a>limites  
Para limites, confira [Limites do Link Privado do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#private-link-limits).

## <a name="service-level-agreement"></a>SLA (Contrato de Nível de Serviço)
Para SLA, confira [SLA para o Link Privado do Azure](https://azure.microsoft.com/support/legal/sla/private-link/v1_0/).

## <a name="next-steps"></a>Próximas etapas

- [Início Rápido: Criar um ponto de extremidade privado usando portal do Azure](create-private-endpoint-portal.md)
- [Início Rápido: Criar um serviço de Link Privado usando o portal do Azure](create-private-link-service-portal.md)