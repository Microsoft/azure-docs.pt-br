---
title: Introdução à Instância Gerenciada do Azure para Apache Cassandra
description: Saiba sobre a Instância Gerenciada do Azure para Apache Cassandra. Esse serviço gerencia a implantação e o dimensionamento de instâncias nativas open-source do Apache Cassandra no Azure.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: overview
ms.date: 03/02/2021
ms.openlocfilehash: d99c62e7d88510c351f87d85b4f8c5c271767cb3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101747283"
---
# <a name="what-is-azure-managed-instance-for-apache-cassandra-preview"></a>O que é a Instância Gerenciada do Azure para Apache Cassandra? (Visualização)

> [!IMPORTANT]
> A Instância Gerenciada do Azure para Apache Cassandra está atualmente em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A Instância Gerenciada do Azure do serviço Apache Cassandra fornece implantação automatizada e operações de dimensionamento para datacenters open-source gerenciados do Apache Cassandra. Esse serviço ajuda você a acelerar cenários híbridos e a reduzir a manutenção contínua. Ele terá recursos de integração profunda e de movimentação de dados com a [API do Cassandra do Azure Cosmos DB ](../cosmos-db/cassandra-introduction.md) em sua versão geral.

:::image type="content" source="./media/introduction/icon.gif" alt-text="A Instância Gerenciada do Azure para o Apache Cassandra é um serviço gerenciado do Apache Cassandra." border="false":::

## <a name="key-benefits"></a>Principais benefícios

### <a name="hybrid-deployments"></a>Implantações híbridas

Você pode usar esse serviço para posicionar facilmente as instâncias gerenciadas de datacenters do Apache Cassandra, que são implantadas automaticamente como conjuntos de dimensionamento de máquinas virtuais, em uma Rede Virtual nova ou existente do Azure. Esses data centers podem ser adicionados ao anel do Apache Cassandra existente e em execução no local por meio do [Azure ExpressRoute](/azure/architecture/reference-architectures/hybrid-networking/expressroute) no Azure ou a outro ambiente de nuvem.

- **Implantação simplificada:** depois que a conectividade híbrida é estabelecida, a implantação se torna fácil por meio do protocolo fofoca.
- **Métricas hospedadas:** as métricas são hospedadas no [Prometheus](https://prometheus.io/docs/introduction/overview/) para monitorar a atividade em todo o cluster.

### <a name="simplified-scaling"></a>Dimensionamento simplificado

Na instância gerenciada, os dimensionamentos vertical e horizontal de nós em um datacenter são totalmente gerenciados. Você insere o número de nós de que precisa e o orquestrador de dimensionamento cuida do estabelecimento de sua operação no anel do Cassandra.

### <a name="managed-and-cost-effective"></a>Gerenciado e econômico

O serviço fornece operações de gerenciamento para as seguintes tarefas comuns do Apache Cassandra:

- Provisionar um cluster
- Provisionar um datacenter
- Dimensionar um datacenter
- Excluir um datacenter
- Iniciar uma ação de reparo em um keyspace
- Alterar a configuração de um datacenter
- Backups de instalação

O modelo de preços é flexível, sob demanda, baseado em instância e não tem taxas de licenciamento. Esse modelo de preços permite que você se ajuste às suas necessidades específicas de carga de trabalho. Você escolhe a quantidade de núcleos, o SKU da VM, o tamanho da memória e o tamanho do espaço em disco necessários.

## <a name="next-steps"></a>Próximas etapas

Introdução com um dos nossos guias de início rápido:

* [Criar um cluster de instância gerenciada a partir do portal do Azure](create-cluster-portal.md)
* [Implantar um cluster do Apache Spark gerenciado com o Azure Databricks](deploy-cluster-databricks.md)
* [Gerenciar recursos da Instância Gerenciada do Azure para Apache Cassandra usando a CLI do Azure](manage-resources-cli.md)
