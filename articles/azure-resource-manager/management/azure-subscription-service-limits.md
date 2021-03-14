---
title: Limites e cotas de assinatura do Azure
description: Fornece uma lista de assinaturas comuns do Azure e limites de serviço, cotas e restrições. Este artigo inclui informações sobre como aumentar os limites juntamente com os valores máximos.
ms.topic: conceptual
ms.date: 09/02/2020
ms.openlocfilehash: fd9f8c9fff60ea9f7c24eb5d6f35542a197fe366
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101734006"
---
# <a name="azure-subscription-and-service-limits-quotas-and-constraints"></a>Assinatura do Azure e limite de serviços, cotas e restrições

Este documento lista alguns dos limites mais comuns do Microsoft Azure, que também são chamados de cotas.

Para saber mais sobre os preços do Azure, consulte [visão geral de preços do Azure](https://azure.microsoft.com/pricing/). Lá, você pode estimar seus custos usando a [calculadora de preços](https://azure.microsoft.com/pricing/calculator/). Você também pode ir para a página de detalhes de preços de um serviço específico, por exemplo, [VMs do Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows). Para obter dicas sobre como ajudar a gerenciar custos, consulte [Evitar custos inesperados com o gerenciamento de custo e cobrança do Azure](../../cost-management-billing/cost-management-billing-overview.md).

## <a name="managing-limits"></a>Gerenciando limites

> [!NOTE]
> Alguns serviços têm limites ajustáveis.
>
> Quando um serviço não tem limites ajustáveis, as tabelas a seguir usam o **limite** de cabeçalho. Nesses casos, os limites padrão e máximo são os mesmos.
>
> Quando o limite pode ser ajustado, as tabelas incluem o **limite padrão** e os cabeçalhos de **limite máximo** . O limite pode ser gerado acima do limite padrão, mas não acima do limite máximo.
>
> Se você quiser aumentar o limite ou a cota acima do limite padrão, [abra uma solicitação de atendimento ao cliente online sem encargos](../templates/error-resource-quota.md).
>
> Os termos de *limite flexível* e *rígido* geralmente são usados informalmente para descrever o limite atual, ajustável (limite flexível) e o limite máximo (limite fixo). Se um limite não for ajustável, não haverá um limite flexível, apenas um limite rígido.
>

As [assinaturas de avaliação gratuita](https://azure.microsoft.com/offers/ms-azr-0044p) não estão qualificadas para aumentos de cota ou limite. Se você tiver uma [assinatura de Avaliação Gratuita](https://azure.microsoft.com/offers/ms-azr-0044p), você poderá atualizar para uma assinatura de [pagamento conforme o uso](https://azure.microsoft.com/offers/ms-azr-0003p/). Para obter mais informações, consulte [atualizar sua assinatura de avaliação gratuita do Azure para uma assinatura paga conforme o uso](../../cost-management-billing/manage/upgrade-azure-subscription.md) e as [perguntas frequentes sobre assinatura de avaliação gratuita](https://azure.microsoft.com/free/free-account-faq).

Alguns limites são gerenciados em um nível regional.

Vamos usar cotas vCPU como um exemplo. Para solicitar um aumento de cota com suporte para vCPUs, você deve decidir quantas vCPUs deseja usar em quais regiões. Em seguida, você faz uma solicitação específica para as cotas de vCPU do grupo de recursos do Azure para os valores e as regiões desejadas. Se você precisar usar 30 vCPUs em Europa Ocidental para executar seu aplicativo lá, solicite especificamente 30 vCPUs em Europa Ocidental. Sua cota de vCPU não é aumentada em nenhuma outra região-somente Europa Ocidental tem a cota de 30 vCPU.

Como resultado, decida quais suas cotas do grupo de recursos do Azure devem ser para sua carga de trabalho em qualquer região. Em seguida, solicite esse valor em cada região na qual você deseja implantar. Para obter ajuda sobre como determinar suas cotas atuais para regiões específicas, consulte [resolver erros de cotas de recursos](../templates/error-resource-quota.md).

## <a name="general-limits"></a>Limites gerais

Para limites de nomes de recursos, consulte [regras e restrições de nomenclatura para recursos do Azure](resource-name-rules.md).

Para obter informações sobre limites de leitura e gravação da API do Resource Manager, consulte [solicitações do Gerenciador de Recursos de limitação](request-limits-and-throttling.md).

### <a name="management-group-limits"></a>Limites do grupo de gerenciamento

Os limites a seguir se aplicam a [grupos de gerenciamento](../../governance/management-groups/overview.md).

[!INCLUDE [management-group-limits](../../../includes/management-group-limits.md)]

### <a name="subscription-limits"></a>Limites de assinatura

Os limites a seguir se aplicam quando você usa Azure Resource Manager e grupos de recursos do Azure.

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="resource-group-limits"></a>Limites de grupo de recursos

[!INCLUDE [azure-resource-groups-limits](../../../includes/azure-resource-groups-limits.md)]

## <a name="active-directory-limits"></a>Limites do Active Directory

[!INCLUDE [AAD-service-limits](../../../includes/active-directory-service-limits-include.md)]

## <a name="api-management-limits"></a>Limites de Gerenciamento de API

[!INCLUDE [api-management-service-limits](../../../includes/api-management-service-limits.md)]

## <a name="app-service-limits"></a>Limites do Serviço de Aplicativo

Os seguintes limites do Serviço de Aplicativo incluem limites para Aplicativos Web, Aplicativos Móveis e Aplicativos de API.

[!INCLUDE [azure-websites-limits](../../../includes/azure-websites-limits.md)]

## <a name="automation-limits"></a>Limites de automação

[!INCLUDE [automation-limits](../../../includes/azure-automation-service-limits.md)]

## <a name="azure-app-configuration"></a>Configuração de Aplicativo do Azure

[!INCLUDE [app-configuration-limits](../../../includes/app-configuration-limits.md)]

## <a name="azure-cache-for-redis-limits"></a>Limites do Cache Redis do Azure

[!INCLUDE [redis-cache-service-limits](../../../includes/redis-cache-service-limits.md)]

## <a name="azure-cloud-services-limits"></a>Limites dos serviços de nuvem do Azure

[!INCLUDE [azure-cloud-services-limits](../../../includes/azure-cloud-services-limits.md)]

## <a name="azure-cognitive-search-limits"></a>Limites de Pesquisa Cognitiva do Azure

Os tipos de preço determinam a capacidade e os limites de seu serviço Search. Eles incluem:

* O serviço multilocatário **gratuito** , compartilhado com outros assinantes do Azure, destina-se a avaliação e projetos de desenvolvimento pequenos.
* **Básico** fornece recursos de computação dedicados para cargas de trabalho de produção em uma escala menor, com até três réplicas para cargas de trabalho de consulta altamente disponíveis.
* O **padrão**, que inclui a alta densidade S1, S2, S3 e S3, é para cargas de trabalho de produção maiores. Existem vários níveis na camada Standard para que você possa escolher uma configuração de recurso que melhor corresponda ao perfil de carga de trabalho.

**Limites por assinatura**

[!INCLUDE [azure-search-limits-per-subscription](../../../includes/azure-search-limits-per-subscription.md)]

**Limites por serviço Search**

[!INCLUDE [azure-search-limits-per-service](../../../includes/azure-search-limits-per-service.md)]

Para saber mais sobre os limites em um nível mais granular, como tamanho do documento, consultas por segundo, chaves, solicitações e respostas, confira [limites de serviço no Azure pesquisa cognitiva](../../search/search-limits-quotas-capacity.md).

## <a name="azure-cognitive-services-limits"></a>Limites de serviços cognitivas do Azure

[!INCLUDE [azure-cognitive-services-limits](../../../includes/azure-cognitive-services-limits.md)]

## <a name="azure-cosmos-db-limits"></a>Limites do Azure Cosmos DB

Para limites de Azure Cosmos DB, consulte [limites em Azure Cosmos DB](../../cosmos-db/concepts-limits.md).

## <a name="azure-data-explorer-limits"></a>Limites de Data Explorer do Azure

[!INCLUDE [azure-data-explorer-limits](../../../includes/data-explorer-limits.md)]

## <a name="azure-database-for-mysql"></a>Banco de Dados do Azure para MySQL

Para limites do Banco de Dados do Azure para MySQL, consulte [Limitações no Banco de Dados do Azure para MySQL](../../mysql/concepts-limits.md).

## <a name="azure-database-for-postgresql"></a>Banco de Dados do Azure para PostgreSQL

Para limites do Banco de Dados do Azure para PostgreSQL, consulte [Limitações no Banco de Dados do Azure para PostgreSQL](../../postgresql/concepts-limits.md).

## <a name="azure-functions-limits"></a>Limites de Azure Functions

[!INCLUDE [functions-limits](../../../includes/functions-limits.md)]

Para obter mais informações, consulte [comparação de funções de Hospedagem de planos](../../azure-functions/functions-scale.md).

## <a name="azure-kubernetes-service-limits"></a>Limites do serviço kubernetes do Azure

[!INCLUDE [container-service-limits](../../../includes/container-service-limits.md)]

## <a name="azure-machine-learning-limits"></a>Limites de Azure Machine Learning

Os valores mais recentes para Azure Machine Learning cotas de computação podem ser encontrados na [página de cota de Azure Machine Learning](../../machine-learning/how-to-manage-quotas.md)

## <a name="azure-maps-limits"></a>Limites do Azure Mapas

[!INCLUDE [maps-limits](../../../includes/maps-limits.md)]

## <a name="azure-monitor-limits"></a>Limites de Azure Monitor

### <a name="alerts"></a>Alertas

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-alerts.md)]

### <a name="action-groups"></a>Grupos de ação

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-action-groups.md)]

### <a name="autoscale"></a>Autoscale

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-autoscale.md)]

### <a name="log-queries-and-language"></a>Consultas de log e idioma

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-log-queries.md)]

### <a name="log-analytics-workspaces"></a>Workspaces do Log Analytics

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-workspaces.md)]

### <a name="application-insights"></a>Application Insights

[!INCLUDE [monitoring-limits](../../../includes/application-insights-limits.md)]

## <a name="azure-policy-limits"></a>Limites do Azure Policy

[!INCLUDE [policy-limits](../../../includes/azure-policy-limits.md)]

## <a name="azure-quantum-limits"></a>Limites do quantum do Azure

[!INCLUDE [quantum-limits](../../../includes/azure-quantum-limits.md)]

## <a name="azure-role-based-access-control-limits"></a>Limites de controle de acesso baseado em função do Azure

[!INCLUDE [role-based-access-control-limits](../../../includes/role-based-access-control/limits.md)]

## <a name="azure-signalr-service-limits"></a>Limites do serviço de Signaler do Azure

[!INCLUDE [signalr-service-limits](../../../includes/signalr-service-limits.md)]

## <a name="azure-vmware-solution-limits"></a>Limites da solução VMware do Azure

[!INCLUDE [azure-vmware-solutions-limits](../../azure-vmware/includes/azure-vmware-solutions-limits.md)]

## <a name="backup-limits"></a>Limites do Backup

[!INCLUDE [azure-backup-limits](../../../includes/azure-backup-limits.md)]

## <a name="batch-limits"></a>Limites de lote

[!INCLUDE [azure-batch-limits](../../../includes/azure-batch-limits.md)]

## <a name="classic-deployment-model-limits"></a>Limites do modelo de implantação clássico

Se você usar o modelo de implantação clássico em vez do modelo de implantação Azure Resource Manager, os limites a seguir serão aplicados.

[!INCLUDE [azure-subscription-limits](../../../includes/azure-subscription-limits.md)]

## <a name="container-instances-limits"></a>Limites das Instâncias de Contêiner

[!INCLUDE [container-instances-limits](../../../includes/container-instances-limits.md)]

## <a name="container-registry-limits"></a>Limites do Registro de Contêiner

A tabela a seguir fornece detalhes dos recursos e limites das [camadas de serviço](../../container-registry/container-registry-skus.md)Basic, Standard e Premium.

[!INCLUDE [container-registry-limits](../../../includes/container-registry-limits.md)]

## <a name="content-delivery-network-limits"></a>Limites de rede de distribuição de conteúdo

[!INCLUDE [cdn-limits](../../../includes/cdn-limits.md)]

## <a name="data-factory-limits"></a>Limites de fábrica de dados

[!INCLUDE [azure-data-factory-limits](../../../includes/azure-data-factory-limits.md)]

## <a name="data-lake-analytics-limits"></a>Limites do Data Lake Analytics

[!INCLUDE [azure-data-lake-analytics-limits](../../../includes/azure-data-lake-analytics-limits.md)]

## <a name="data-lake-storage-limits"></a>Limites de Data Lake Storage

[!INCLUDE [azure-data-lake-store-limits](../../../includes/azure-data-lake-store-limits.md)]

## <a name="data-share-limits"></a>Limites de compartilhamento de dados

[!INCLUDE [azure-data-share-limits](../../../includes/azure-data-share-limits.md)]

## <a name="database-migration-service-limits"></a>Limites do Serviço de Migração do Banco de Dados

[!INCLUDE [database-migration-service-limits](../../../includes/database-migration-service-limits.md)]

## <a name="digital-twins-limits"></a>Limites de gêmeos digital

> [!NOTE]
> Algumas áreas desse serviço têm limites ajustáveis e outras não. Isso é representado nas tabelas abaixo com a coluna *ajustável?* . Quando o limite pode ser ajustado, o valor *ajustável?* é *Sim*.

[!INCLUDE [digital-twins-limits](../../../includes/digital-twins-limits.md)]

## <a name="event-grid-limits"></a>Limites de grade de eventos

[!INCLUDE [event-grid-limits](../../../includes/event-grid-limits.md)]

## <a name="event-hubs-limits"></a>Limites de Hubs de Eventos

[!INCLUDE [azure-servicebus-limits](../../../includes/event-hubs-limits.md)]

## <a name="iot-central-limits"></a>Limites de IoT Central
[!INCLUDE [iot-central-limits](../../../includes/iot-central-limits.md)]

## <a name="iot-hub-limits"></a>Limites do Hub IoT

[!INCLUDE [azure-iothub-limits](../../../includes/iot-hub-limits.md)]

## <a name="iot-hub-device-provisioning-service-limits"></a>Limites do Serviço de Provisionamento de Dispositivos no Hub IoT

[!INCLUDE [azure-iotdps-limits](../../../includes/iot-dps-limits.md)]

## <a name="key-vault-limits"></a>Limites do Cofre da Chave

[!INCLUDE [key-vault-limits](../../../includes/key-vault-limits.md)]

## <a name="managed-identity-limits"></a>Limites de identidade gerenciados

[!INCLUDE [Managed-Identity-Limits](../../../includes/managed-identity-limits.md)]


## <a name="media-services-limits"></a>Limites de Serviços de Mídia

[!INCLUDE [azure-mediaservices-limits](../../../includes/media-servieces-limits-quotas-constraints.md)]

### <a name="media-services-v2-legacy"></a>Serviços de Mídia v2 (herdado)

Para limites específicos para os serviços de mídia v2 (herdados), consulte [serviços de mídia v2 (Herdado)](../../media-services/previous/media-services-quotas-and-limitations.md)

## <a name="mobile-services-limits"></a>Limites de Serviços Móveis

[!INCLUDE [mobile-services-limits](../../../includes/mobile-services-limits.md)]

## <a name="multi-factor-authentication-limits"></a>Limites da autenticação multifator

[!INCLUDE [azure-mfa-service-limits](../../../includes/azure-mfa-service-limits.md)]

## <a name="networking-limits"></a>Limites de rede

[!INCLUDE [azure-virtual-network-limits](../../../includes/azure-virtual-network-limits.md)]

### <a name="expressroute-limits"></a>Limites de ExpressRoute

[!INCLUDE [expressroute-limits](../../../includes/expressroute-limits.md)]

### <a name="virtual-network-gateway-limits"></a>Limites de gateway de rede virtual

[!INCLUDE [virtual-network-gateway-limits](../../../includes/azure-virtual-network-gateway-limits.md)]

### <a name="nat-gateway-limits"></a>Limites de gateway NAT

[!INCLUDE [nat-gateway-limits](../../../includes/azure-nat-gateway-limits.md)]

### <a name="virtual-wan-limits"></a>Limites de WAN virtual

[!INCLUDE [virtual-wan-limits](../../../includes/virtual-wan-limits.md)]

### <a name="application-gateway-limits"></a>Limites do Gateway de Aplicativo

A tabela a seguir se aplica aos SKUs v1, v2, Standard e WAF, salvo indicação em contrário.
[!INCLUDE [application-gateway-limits](../../../includes/application-gateway-limits.md)]

### <a name="network-watcher-limits"></a>Limites do Observador de Rede

[!INCLUDE [network-watcher-limits](../../../includes/network-watcher-limits.md)]

### <a name="private-link-limits"></a>Limites de Link Privado

[!INCLUDE [private-link-limits](../../../includes/private-link-limits.md)]

## <a name="purview-limits"></a>Limites de alcance

Os valores mais recentes para cotas do Azure alcance podem ser encontrados na [página de cota do Azure alcance](../../purview/how-to-manage-quotas.md)

### <a name="traffic-manager-limits"></a>Limites do Gerenciador de Tráfego

[!INCLUDE [traffic-manager-limits](../../../includes/traffic-manager-limits.md)]

### <a name="azure-bastion-limits"></a>Limites de bastiões do Azure

[!INCLUDE [Azure Bastion limits](../../../includes/bastion-limits.md)]

### <a name="azure-dns-limits"></a>Limites de DNS do Azure

[!INCLUDE [dns-limits](../../../includes/dns-limits.md)]

### <a name="azure-firewall-limits"></a>Limites de Firewall do Azure

[!INCLUDE [azure-firewall-limits](../../../includes/firewall-limits.md)]

### <a name="azure-front-door-service-limits"></a>Limites do Azure Front Door Service

[!INCLUDE [azure-front-door-service-limits](../../../includes/front-door-limits.md)]

## <a name="notification-hubs-limits"></a>Limites de hubs de notificação

[!INCLUDE [notification-hub-limits](../../../includes/notification-hub-limits.md)]

## <a name="service-bus-limits"></a>Limites de Barramento de Serviço

[!INCLUDE [azure-servicebus-limits](../../../includes/service-bus-quotas-table.md)]

## <a name="site-recovery-limits"></a>Limites da Recuperação de Site

[!INCLUDE [site-recovery-limits](../../../includes/site-recovery-limits.md)]

## <a name="sql-database-limits"></a>Limites de banco de dados SQL

Para os limites do banco de dados SQL, consulte [limites de recursos do banco de dados SQL para bancos únicos](../../azure-sql/database/resource-limits-vcore-single-databases.md), [limites de recursos do banco de dados SQL para pools elásticos e bancos](../../azure-sql/database/resource-limits-vcore-elastic-pools.md)de dados em pool e [limites de recursos do banco de dados SQL para o SQL instância gerenciada](../../azure-sql/managed-instance/resource-limits.md).

## <a name="azure-synapse-analytics-limits"></a>Limites do Azure Synapse Analytics

Para limites do Azure Synapse Analytics, consulte [limites de recursos do Synapse do Azure](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md).

## <a name="azure-files-and-azure-file-sync"></a>Arquivos e Sincronização de Arquivos do Azure do Azure
Para saber mais sobre os limites de arquivos e Sincronização de Arquivos do Azure, consulte [metas de desempenho e escalabilidade de arquivos do Azure](../../storage/files/storage-files-scale-targets.md).

## <a name="storage-limits"></a>Limites de armazenamento

<!--like # storage accts -->
[!INCLUDE [azure-storage-account-limits-standard](../../../includes/azure-storage-account-limits-standard.md)]

Para obter mais informações sobre os limites para contas de armazenamento padrão, consulte [metas de escalabilidade para contas de armazenamento padrão](../../storage/common/scalability-targets-standard-account.md).

### <a name="storage-resource-provider-limits"></a>Limites do provedor de recursos de armazenamento

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

### <a name="azure-blob-storage-limits"></a>Limites de Armazenamento de Blobs do Azure

[!INCLUDE [storage-blob-scale-targets](../../../includes/storage-blob-scale-targets.md)]

### <a name="azure-queue-storage-limits"></a>Limites de Armazenamento de Filas do Azure

[!INCLUDE [storage-queues-scale-targets](../../../includes/storage-queues-scale-targets.md)]

### <a name="azure-table-storage-limits"></a>Limites de Armazenamento de Tabelas do Azure

[!INCLUDE [storage-tables-scale-targets](../../../includes/storage-tables-scale-targets.md)]

<!-- conceptual info about disk limits -- applies to unmanaged and managed -->
### <a name="virtual-machine-disk-limits"></a>Limites de disco de máquina virtual

[!INCLUDE [azure-storage-limits-vm-disks](../../../includes/azure-storage-limits-vm-disks.md)]

Para obter mais informações, consulte [tamanhos de máquina virtual](../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

#### <a name="disk-encryption-sets"></a>Conjuntos de criptografia de disco

Há uma limitação de 1000 conjuntos de criptografia de disco por região, por assinatura. Para obter mais informações, consulte a documentação de criptografia para máquinas virtuais [Linux](../../virtual-machines/disk-encryption.md#restrictions) ou [Windows](../../virtual-machines/disk-encryption.md#restrictions) . Se você precisar aumentar a cota, entre em contato com o suporte do Azure.

### <a name="managed-virtual-machine-disks"></a>Discos de máquina virtual gerenciados

[!INCLUDE [azure-storage-limits-vm-disks-managed](../../../includes/azure-storage-limits-vm-disks-managed.md)]

### <a name="unmanaged-virtual-machine-disks"></a>Discos de máquina virtual não gerenciados

[!INCLUDE [azure-storage-limits-vm-disks-standard](../../../includes/azure-storage-limits-vm-disks-standard.md)]

[!INCLUDE [azure-storage-limits-vm-disks-premium](../../../includes/azure-storage-limits-vm-disks-premium.md)]

## <a name="storsimple-system-limits"></a>Limites do sistema StorSimple

[!INCLUDE [storsimple-limits-table](../../../includes/storsimple-limits-table.md)]

## <a name="stream-analytics-limits"></a>Limites do Stream Analytics

[!INCLUDE [stream-analytics-limits-table](../../../includes/stream-analytics-limits-table.md)]

## <a name="virtual-machines-limits"></a>Limites de Máquinas virtuais

### <a name="virtual-machines-limits"></a>Limites de Máquinas virtuais

[!INCLUDE [azure-virtual-machines-limits](../../../includes/azure-virtual-machines-limits.md)]

### <a name="virtual-machines-limits---azure-resource-manager"></a>Limites de Máquinas Virtuais – Gerenciador de Recursos do Azure

Os limites a seguir se aplicam quando você usa Azure Resource Manager e grupos de recursos do Azure.

[!INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../../../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

### <a name="shared-image-gallery-limits"></a>Limites da Galeria de imagens compartilhadas

Há limites por assinatura para implantar recursos usando Galerias de Imagens Compartilhadas:

- 100 galerias de imagens compartilhadas por assinatura por região
- 1\.000 definições de imagem por assinatura por região
- 10.000 versões de imagem por assinatura por região

## <a name="virtual-machine-scale-sets-limits"></a>Limites dos conjuntos de dimensionamento de máquinas virtuais

[!INCLUDE [virtual-machine-scale-sets-limits](../../../includes/azure-virtual-machine-scale-sets-limits.md)]

## <a name="see-also"></a>Confira também

* [Entender os limites e as aumentos do Azure](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
* [Tamanhos de máquina virtual e serviço de nuvem para o Azure](../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Tamanhos dos serviços de nuvem do Azure](../../cloud-services/cloud-services-sizes-specs.md)
* [Regras de nomenclatura e restrições para recursos do Azure](resource-name-rules.md)
