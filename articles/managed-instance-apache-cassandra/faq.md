---
title: Perguntas frequentes sobre a Instância Gerenciada do Azure para Apache Cassandra no portal do Azure
description: Perguntas frequentes sobre a Instância Gerenciada do Azure para Apache Cassandra. Este artigo aborda perguntas sobre quando usar instâncias gerenciadas, benefícios, limites de taxa de transferência, regiões com suporte e outros detalhes de configuração.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.openlocfilehash: 1ba2b7d648c86912118b83a566bf2eb0800baee2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101747290"
---
# <a name="frequently-asked-questions-about-azure-managed-instance-for-apache-cassandra-preview"></a>Perguntas frequentes sobre a Instância Gerenciada do Azure para Apache Cassandra (versão prévia)

Este artigo aborda as perguntas frequentes sobre a Instância Gerenciada do Azure para Apache Cassandra. Você aprenderá quando usar instâncias gerenciadas, seus benefícios, limites de taxa de transferência e regiões com suporte, bem como seus detalhes de configuração.

> [!IMPORTANT]
> A Instância Gerenciada do Azure para Apache Cassandra está atualmente em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="general-faq"></a>Perguntas frequentes gerais

### <a name="what-are-the-benefits-azure-managed-instance-for-apache-cassandra"></a>Quais são os benefícios da Instância Gerenciada do Azure para Apache Cassandra?

O banco de dados Apache Cassandra é a escolha certa quando você precisa ter escalabilidade e alta disponibilidade sem comprometer o desempenho. Trata-se de uma ótima plataforma para dados críticos devido à escalabilidade linear e à tolerância a falhas comprovada em hardwares de mercadoria ou na infraestrutura de nuvem. A Instância Gerenciada do Azure para Apache Cassandra é um serviço usado para gerenciar instâncias de datacenters de software livre do Apache Cassandra implantados no Azure.

Ela pode ser usada inteiramente na nuvem ou como parte de um cluster híbrido local e na nuvem. Esse serviço é uma ótima opção quando você quer ter a configuração e o controle refinados que tem no Apache Cassandra de software livre sem nenhuma sobrecarga de manutenção.

### <a name="why-should-i-use-this-service-instead-of-azure-cosmos-db-cassandra-api"></a>Por que usar esse serviço em vez da API do Cassandra para o Azure Cosmos DB?

A Instância Gerenciada do Azure para Apache Cassandra é fornecida pela equipe do Azure Cosmos DB. Trata-se de um serviço gerenciado autônomo para implantação, manutenção e dimensionamento de clusters e datacenters do Apache Cassandra de software livre. A [API do Cassandra para o Azure Cosmos DB](../cosmos-db/cassandra-introduction.md), por outro lado, é uma plataforma como um serviço que fornece uma camada de interoperabilidade para o protocolo de transmissão do Apache Cassandra. Se sua expectativa é que a plataforma se comporte exatamente da mesma maneira que qualquer cluster Apache Cassandra, escolha o serviço da instância gerenciada. Para saber mais, confira o artigo [Instância Gerenciada do Azure para Apache Cassandra versus API do Cassandra para Azure Cosmos DB](compare-cosmosdb-managed-instance.md).

### <a name="is-azure-managed-instance-for-apache-cassandra-dependent-on-azure-cosmos-db"></a>A Instância Gerenciada do Azure para Apache Cassandra depende do Azure Cosmos DB?

Não há nenhuma dependência de arquitetura entre a Instância Gerenciada do Azure para Apache Cassandra e o back-end do Azure Cosmos DB. 

#### <a name="can-i-deploy-azure-managed-instance-for-apache-cassandra-in-any-region"></a>Posso implantar a Instância Gerenciada do Azure para Apache Cassandra em qualquer região?

A instância gerenciada ficará disponível em um número limitado de regiões durante a versão prévia.

### <a name="what-are-the-storage-and-throughput-limits-of-azure-managed-instance-for-apache-cassandra"></a>Quais são os limites de armazenamento e taxa de transferência da Instância Gerenciada do Azure para Apache Cassandra?

Esses limites dependem das SKUs da Máquina Virtual que você escolher.

### <a name="what-is-the-cost-of-azure-managed-instance-for-apache-cassandra"></a>Qual é o custo da Instância Gerenciada do Azure para Apache Cassandra?

Os preços da instância gerenciada são baseados no custo da VM subjacente, com uma pequena marcação. Para saber mais, confira a [página de preço](https://azure.microsoft.com/pricing/details/managed-instance-apache-cassandra/).

### <a name="can-i-use-yaml-file-settings-to-configure-behavior"></a>Posso usar configurações de arquivo YAML para configurar o comportamento?

Sim, você pode inserir configurações de arquivo YAML como parte de uma implantação de modelo de Azure Resource Manager.

### <a name="how-can-i-monitor-infrastructure-along-with-throughput"></a>Como posso monitorar a infraestrutura em conjunto com a taxa de transferência?

O servidor [Prometheus](https://prometheus.io/docs/introduction/overview/) é hospedado para monitorar a atividade em todo o cluster e expõe um ponto de extremidade. Isso mantém 10 minutos ou 10 GB de dados (o limite que for atingido primeiro). Para usar esse monitoramento, você precisa configurar uma [federação](https://prometheus.io/docs/prometheus/latest/federation/) e uma ferramenta de painel apropriada, como o Grafana.

### <a name="does-azure-managed-instance-for-apache-cassandra-provide-full-backups"></a>A Instância Gerenciada do Azure para Apache Cassandra fornece backups completos?

Sim, ela fornece backups completos para o Armazenamento do Azure e restaura para um novo cluster

### <a name="how-can-i-migrate-data-from-my-existing-apache-cassandra-cluster-to-azure-managed-instance-for-apache-cassandra"></a>Como posso migrar dados de meu cluster existente do Apache Cassandra para a Instância Gerenciada do Azure para Apache Cassandra?

A Instância Gerenciada do Azure para Apache Cassandra dá suporte a todos os recursos do Apache Cassandra para replicar e transmitir dados entre datacenters.

### <a name="can-i-pair-an-on-premises-apache-cassandra-cluster-with-the-azure-managed-instance-for-apache-cassandra"></a>Posso emparelhar um cluster Apache Cassandra local com a Instância Gerenciada do Azure para Apache Cassandra?

Sim, você pode configurar um cluster híbrido com os datacenters injetados da Rede Virtual do Azure implantados pelo serviço. Os datacenters da Instância Gerenciada podem se comunicar com datacenters locais dentro do mesmo anel de cluster.

### <a name="where-can-i-give-feedback-on-azure-managed-instance-for-apache-cassandra-features"></a>Onde posso fornecer comentários sobre os recursos da Instância Gerenciada do Azure para Apache Cassandra?

Forneça comentários por meio dos [comentários do User Voice](https://feedback.azure.com/forums/263030-azure-cosmos-db?category_id=398548) usando a categoria "Apache Cassandra Gerenciado".

Para corrigir um problema com sua conta, apresente uma [solicitação de suporte](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) no portal do Azure.

## <a name="deployment-specific-faq"></a>Perguntas frequentes específicas sobre a implantação

### <a name="will-the-managed-instance-support-node-addition-cluster-status-and-node-status-commands"></a>A instância gerenciada dará suporte a comandos de adição de nó, status do cluster e status do nó?

Todos os comandos de Nodetool *somente leitura*, como `status`, estão disponíveis por meio da CLI do Azure. No entanto, operações como a *adição de nó* não estão disponíveis, pois gerenciamos a integridade dos nós na instância gerenciada. No modo híbrido, você pode se conectar ao cluster usando *Nodetool*. No entanto, o uso de Nodetool não é recomendado porque pode desestabilizar o cluster. Além disso, pode invalidar qualquer SLA de suporte de produção relacionado à integridade dos datacenters da instância gerenciada no cluster.

### <a name="what-happens-with-various-settings-for-table-metadata"></a>O que acontece com as diferentes configurações para metadados de tabela?

Configurações para metadados de tabela, como filtro de bloom, cache, chance de reparo de leitura, gc_grace e memtable_flush_period de compactação, têm suporte completo, como ocorre com qualquer ambiente do Apache Cassandra hospedado internamente.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre perguntas frequentes de outras APIs, consulte:

* [Criar um cluster da instância gerenciada no portal do Azure](create-cluster-portal.md)
* [Implantar um cluster do Apache Spark gerenciado com o Azure Databricks](deploy-cluster-databricks.md)
* [Gerenciar recursos da Instância Gerenciada do Azure para Apache Cassandra usando a CLI do Azure](manage-resources-cli.md)