---
title: Disponibilidade de link privado do Azure
description: Neste artigo, saiba mais sobre quais serviços do Azure dão suporte ao link privado.
author: asudbring
ms.author: allensu
ms.service: private-link
ms.topic: conceptual
ms.date: 3/15/2021
ms.custom: template-concept,references_regions
ms.openlocfilehash: 26485c84749b7d4c91159476b3f683c2b0f3831b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103555238"
---
# <a name="azure-private-link-availability"></a>Disponibilidade de link privado do Azure

O Link Privado do Azure lhe permite acessar os serviços de PaaS do Azure (por exemplo, Armazenamento do Azure e Banco de Dados SQL) e serviços de parceiros/de propriedade de clientes hospedados no Azure em um [ponto de extremidade privado](private-endpoint-overview.md) em sua rede virtual. 

> [!IMPORTANT]
> O Link Privado do Azure já está em disponibilidade geral. O Ponto de Extremidade Privado e o serviço de Link Privado (serviço por trás do Standard Load Balancer) estão em disponibilidade geral. O Azure PaaS diferente será integrado ao Link Privado do Azure em datas diferentes. Para conhecer as limitações conhecidas, confira [Ponto de Extremidade Privado](private-endpoint-overview.md#limitations) e [Serviço de Link Privado](private-link-service-overview.md#limitations). 

## <a name="service-availability"></a>Disponibilidade do serviço

As tabelas a seguir listam os serviços de vínculo privado e as regiões em que estão disponíveis. 

### <a name="ai--machine-learning"></a>IA + Machine Learning

|Serviços com suporte  |Regiões disponíveis | Outras considerações | Status  |
|:-------------------|:-----------------|:----------------|:--------|
|Azure Machine Learning | Todas as regiões públicas    |  | GA   <br/> [Saiba como criar um ponto de extremidade privado para o Azure Machine Learning.](../machine-learning/how-to-configure-private-link.md)   |

### <a name="analytics"></a>Análise

|Serviços com suporte  |Regiões disponíveis | Outras considerações | Status  |
|:-------------------|:-----------------|:----------------|:--------|
|Azure Synapse Analytics| Todas as regiões públicas <br/> Todas as regiões do Governo |  Com suporte para [política de conexão](../azure-sql/database/connectivity-architecture.md#connection-policy) de Proxy |GA <br/> [Saiba como criar um ponto de extremidade privado para o Azure Synapse Analytics.](../azure-sql/database/private-endpoint-overview.md)|
|Hub de Eventos do Azure | Todas as regiões públicas<br/>Todas as regiões do Governo      |   | GA   <br/> [Saiba como criar um ponto de extremidade privado para o Hub de Eventos do Azure.](../event-hubs/private-link-service.md)  |
| Azure Monitor <br/>(Log Analytics e Application Insights) | Todas as regiões públicas      |  | GA   <br/> [Saiba como criar um ponto de extremidade privado para o Azure Monitor.](../azure-monitor/logs/private-link-security.md)   |
|Fábrica de dados do Azure | Todas as regiões públicas<br/> Todas as regiões do Governo<br/>Todas as regiões da China    | As credenciais devem ser armazenadas no Azure Key Vault| GA   <br/> [Saiba como criar um ponto de extremidade privado para o Azure Data Factory.](../data-factory/data-factory-private-link.md)   |

### <a name="compute"></a>Computação

|Serviços com suporte  |Regiões disponíveis | Outras considerações | Status  |
|:-------------------|:-----------------|:----------------|:--------|
|Configuração de Aplicativo do Azure | Todas as regiões públicas      |  | Visualização  </br> [Saiba como criar um ponto de extremidade privado para a Configuração de Aplicativos do Azure](../azure-app-configuration/concept-private-endpoint.md) |
|Discos gerenciados pelo Azure | Todas as regiões públicas<br/> Todas as regiões do Governo<br/>Todas as regiões da China    | [Selecione para limitações conhecidas](../virtual-machines/disks-enable-private-links-for-import-export-portal.md#limitations) | GA   <br/> [Saiba como criar um ponto de extremidade privado para os Azure Managed Disks.](../virtual-machines/disks-enable-private-links-for-import-export-portal.md)   |

### <a name="containers"></a>Contêineres

|Serviços com suporte  |Regiões disponíveis | Outras considerações | Status  |
|:-------------------|:-----------------|:----------------|:--------|
|Registro de Contêiner do Azure | Todas as regiões públicas<br/> Todas as regiões do Governo    | Compatível com nível Premium do registro de contêiner. [Selecione para camadas](../container-registry/container-registry-skus.md)| GA   <br/> [Saiba como criar um ponto de extremidade privado para o Registro de Contêiner do Azure.](../container-registry/container-registry-private-link.md)   |
|Serviço de Kubernetes do Azure – API Kubernetes | Todas as regiões públicas      |  | GA   <br/> [Saiba como criar um ponto de extremidade privado para Serviço de Kubernetes do Azure.](../aks/private-clusters.md)   |

### <a name="databases"></a>Bancos de dados

|Serviços com suporte  |Regiões disponíveis | Outras considerações | Status  |
|:-------------------|:-----------------|:----------------|:--------|
|  Banco de Dados SQL do Azure         | Todas as regiões públicas <br/> Todas as regiões do Governo<br/>Todas as regiões da China      |  Com suporte para [política de conexão](../azure-sql/database/connectivity-architecture.md#connection-policy) de Proxy | GA <br/> [Saiba como criar um ponto de extremidade privado para o SQL do Azure](create-private-endpoint-portal.md)      |
|Azure Cosmos DB|  Todas as regiões públicas<br/> Todas as regiões do Governo</br> Todas as regiões da China | |GA <br/> [Saiba como criar um ponto de extremidade privado para o Cosmos DB.](./tutorial-private-endpoint-cosmosdb-portal.md)|
|  Banco de Dados do Azure para PostgreSQL – Servidor único         | Todas as regiões públicas <br/> Todas as regiões do Governo<br/>Todas as regiões da China     | Compatível com os tipos de preço uso geral e otimizado para memória | GA <br/> [Saiba como criar um ponto de extremidade privado para o Banco de Dados do Azure para PostgreSQL.](../postgresql/concepts-data-access-and-security-private-link.md)      |
|  Banco de Dados do Azure para MySQL         | Todas as regiões públicas<br/> Todas as regiões do Governo<br/>Todas as regiões da China      |  | GA <br/> [Saiba como criar um ponto de extremidade privado para o Banco de Dados do Azure para MySQL.](../mysql/concepts-data-access-security-private-link.md)     |
|  Banco de Dados do Azure para MariaDB         | Todas as regiões públicas<br/> Todas as regiões do Governo<br/>Todas as regiões da China     |  | GA <br/> [Saiba como criar um ponto de extremidade privado para o Banco de Dados do Azure para MariaDB.](../mariadb/concepts-data-access-security-private-link.md)      |

### <a name="integration"></a>Integração

|Serviços com suporte  |Regiões disponíveis | Outras considerações | Status  |
|:-------------------|:-----------------|:----------------|:--------|
|Grade de Eventos do Azure| Todas as regiões públicas<br/> Todas as regiões do Governo       |  | GA   <br/> [Saiba como criar um ponto de extremidade privado para a Grade de Eventos do Azure.](../event-grid/network-security.md) |
|Barramento de Serviço do Azure | Todas as regiões públicas<br/>Todas as regiões do Governo  | Com suporte com a camada Premium do Barramento de Serviço do Azure. [Selecione para camadas](../service-bus-messaging/service-bus-premium-messaging.md) | GA   <br/> [Saiba como criar um ponto de extremidade privado para o Barramento de Serviço do Azure.](../service-bus-messaging/private-link-service.md)    |

### <a name="internet-of-things-iot"></a>Internet das coisas (IoT)

|Serviços com suporte  |Regiões disponíveis | Outras considerações | Status  |
|:-------------------|:-----------------|:----------------|:--------|
| Hub IoT do Azure | Todas as regiões públicas    |  | GA   <br/> [Saiba como criar um ponto de extremidade privado para o Hub IoT do Azure.](../iot-hub/virtual-network-support.md) |
|  Gêmeos Digitais do Azure         | Todas as regiões públicas com suporte nos Gêmeos Digitais do Azure     |  | Versão Prévia <br/> [Saiba como criar um ponto de extremidade privado para os Gêmeos Digitais do Azure.](../digital-twins/how-to-enable-private-link-portal.md)      |

### <a name="management-and-governance"></a>Gerenciamento e governança

| Serviços com suporte | Regiões disponíveis | Outras considerações | Status  |
| ------------ | ----------------| ------------| ----------------|
| Automação do Azure  | Todas as regiões públicas<br/> Todas as regiões do Governo |  | Visualização </br> [Saiba como criar um ponto de extremidade privado para a Automação do Azure.](../automation/how-to/private-link-security.md)|
|Serviço de Backup do Azure | Todas as regiões públicas<br/> Todas as regiões do Governo   |  | GA <br/> [Saiba como criar um ponto de extremidade privado para o Backup do Azure.](../backup/private-endpoints.md)   |

### <a name="security"></a>Segurança

|Serviços com suporte  |Regiões disponíveis | Outras considerações | Status  |
|:-------------------|:-----------------|:----------------|:--------|
|  Cofre de Chave do Azure         | Todas as regiões públicas<br/> Todas as regiões do Governo      |  | GA   <br/> [Saiba como criar um ponto de extremidade privado para o Azure Key Vault.](../key-vault/general/private-link-service.md)   |

### <a name="storage"></a>Armazenamento
|Serviços com suporte  |Regiões disponíveis | Outras considerações | Status  |
|:-------------------|:-----------------|:----------------|:--------|
| Armazenamento de Blobs do Azure (incluindo Data Lake Storage Gen2)       |  Todas as regiões públicas<br/> Todas as regiões do Governo       |  Com suporte no Tipo de Conta de Uso Geral V2 | GA <br/> [Saiba como criar um ponto de extremidade privado para o armazenamento de blobs.](tutorial-private-endpoint-storage-portal.md)  |
| Arquivos do Azure | Todas as regiões públicas<br/> Todas as regiões do Governo      | |   GA <br/> [Saiba como criar pontos de extremidade de rede de Arquivos do Azure.](../storage/files/storage-files-networking-endpoints.md)   |
| Sincronização de Arquivos do Azure | Todas as regiões públicas      | |   GA <br/> [Saiba como criar pontos de extremidade de rede de Arquivos do Azure.](../storage/files/storage-sync-files-networking-endpoints.md)   |
| Armazenamento de Filas do Azure       |  Todas as regiões públicas<br/> Todas as regiões do Governo       |  Com suporte no Tipo de Conta de Uso Geral V2 | GA <br/> [Saiba como criar um ponto de extremidade privado para o armazenamento de filas.](tutorial-private-endpoint-storage-portal.md) |
| Armazenamento da tabela do Azure       |  Todas as regiões públicas<br/> Todas as regiões do Governo       |  Com suporte no Tipo de Conta de Uso Geral V2 | GA <br/> [Saiba como criar um ponto de extremidade privado para o armazenamento de tabelas.](tutorial-private-endpoint-storage-portal.md)  |
| Lote do Azure | Todas as regiões públicas, exceto: Alemanha CENTRAL, Nordeste da ALEMANHA <br/> Todas as regiões do Governo  | | GA <br/> [Saiba como criar um ponto de extremidade privado para o Lote do Azure.](../batch/private-connectivity.md) |

### <a name="web"></a>Web
|Serviços com suporte  |Regiões disponíveis | Outras considerações | Status  |
|:-------------------|:-----------------|:----------------|:--------|
| Azure SignalR | LESTE DOS EUA, CENTRO-SUL DOS EUA,<br/>OESTE DOS EUA 2, Todas as regiões da China      |  | Visualizar   <br/> [Saiba como criar um ponto de extremidade privado para o Azure SignalR.](../azure-signalr/howto-private-endpoints.md)   |
|Aplicativos Web do Azure | Todas as regiões públicas<br/> Norte da China 2 & East 2    | Com suporte com o plano PremiumV2, PremiumV3 ou Function Premium  | GA   <br/> [Saiba como criar um ponto de extremidade privado para Aplicativos Web do Azure.](./tutorial-private-endpoint-webapp-portal.md)   |
|Azure Search | Todas as regiões públicas <br/> Todas as regiões do Governo | Compatível com o serviço no modo privado | GA   <br/> [Saiba como criar um ponto de extremidade privado para o Azure Search.](../search/service-create-private-endpoint.md)    |
|Retransmissão do Azure | Todas as regiões públicas      |  | Visualização <br/> [Saiba como criar um ponto de extremidade privado para a Retransmissão do Azure.](../azure-relay/private-link-service.md)  |

### <a name="private-link-service-with-a-standard-load-balancer"></a>Serviço de vínculo privado com um balanceador de carga padrão

|Serviços com suporte  |Regiões disponíveis | Outras considerações | Status  |
|:-------------------|:-----------------|:----------------|:--------|
|Serviços de Link Privado atrás do Azure Load Balancer padrão | Todas as regiões públicas<br/> Todas as regiões do Governo<br/>Todas as regiões da China  | Com suporte no Standard Load Balancer | GA <br/> [Saiba como criar um serviço de link privado.](create-private-link-service-portal.md) |

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o serviço de vínculo privado do Azure:
- [O que é o Link Privado do Azure?](private-link-overview.md)
- [Criar um ponto de extremidade privado usando o portal do Azure](create-private-endpoint-portal.md)
