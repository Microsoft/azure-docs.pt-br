---
title: Diferenças entre a Instância Gerenciada do Azure para Apache Cassandra e a API do Cassandra para Azure Cosmos DB
description: Conheça as diferenças entre a Instância Gerenciada do Azure para Apache Cassandra e a API do Cassandra no Azure Cosmos DB. Conheça também os benefícios de cada um desses serviços e quando escolher cada um.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.openlocfilehash: 3050dda4b3c0e1a05d751a4f5969bba69ad0506d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101747344"
---
# <a name="differences-between-azure-managed-instance-for-apache-cassandra-preview-and-azure-cosmos-db-cassandra-api"></a>Diferenças entre a Instância Gerenciada do Azure para Apache Cassandra (versão prévia) e a API do Cassandra para Azure Cosmos DB 

Neste artigo, você conhece as diferenças entre a Instância Gerenciada do Azure para Apache Cassandra e a API do Cassandra no Azure Cosmos DB. Este artigo fornece recomendações de como escolher entre os dois serviços ou quando hospedar seu próprio ambiente do Apache Cassandra.

> [!IMPORTANT]
> A Instância Gerenciada do Azure para Apache Cassandra está atualmente em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="key-differences"></a>Principais diferenças

A Instância Gerenciada do Azure para Apache Cassandra fornece implantação, dimensionamento e operações automatizadas para manter a integridade do nó para as instâncias do Apache Cassandra de software livre no Azure. Ela também permite escalar horizontalmente a capacidade dos clusters Apache Cassandra existentes auto-hospedados na nuvem ou locais. Ela escala horizontalmente adicionando datacenters do Cassandra gerenciados ao anel do cluster existente.

O [API do Cassandra](../cosmos-db/cassandra-introduction.md) no Azure Cosmos DB é uma camada de compatibilidade sobre o serviço de banco de dados nativo de nuvem distribuído globalmente da Microsoft, o [Azure Cosmos DB](../cosmos-db/index.yml). A combinação desses serviços no Azure fornece uma série de opções para os usuários do Apache Cassandra em ambientes de nuvem híbrida complexos.

## <a name="how-to-choose"></a>Como escolher?

A tabela a seguir mostra cenários comuns, requisitos de carga de trabalho e aspirações em que cada uma dessas abordagens de implantação se encaixa:

| |Apache Cassandra auto-hospedado no local ou no Azure | Instância Gerenciada do Azure para Apache Cassandra | API do Cassandra do Azure Cosmos DB |
|---------|---------|---------|---------|
|**Tipo de implantação**| Você tem uma implantação do Apache Cassandra altamente personalizada com snitches ou patches personalizados. | Você tem uma implantação do Apache Cassandra de software livre padrão sem nenhum código personalizado. | Você está satisfeito com uma plataforma sem o Apache Cassandra subjacente, mas está em conformidade com todos os drivers de cliente de software livre no nível do [protocolo de transmissão](../cosmos-db/cassandra-support.md). |
| **Sobrecarga operacional**| Você tem especialistas em Cassandra que podem implantar, configurar e manter seus clusters.  | Você deseja reduzir a sobrecarga operacional relacionada à integridade do nó do Apache Cassandra, mas ainda mantém o controle sobre as configurações no nível da plataforma, como a replicação e a consistência. | Você deseja eliminar a sobrecarga operacional usando um banco de dados de plataforma como serviço totalmente gerenciado na nuvem. |
| **Requisitos do sistema operacional**| Você tem o requisito de manter imagens personalizadas ou de ouro do sistema operacional da Máquina Virtual. | Você pode usar imagens básicas, mas quer ter controle sobre SKUs, memória, discos e IOPS. | Você quer que o provisionamento de capacidade seja simplificado e expresso como uma só métrica normalizada, com uma relação de um para um com a taxa de transferência, como as [unidades de solicitação](../cosmos-db/request-units.md) no Azure Cosmos DB. |
| **Modelo de preços**| Você deseja usar software de gerenciamento, como as ferramentas do Datastax, e está satisfeito com os custos de licenciamento. | Você prefere ter apenas licenciamento de software livre e o preço baseado em instâncias de VM. | Você deseja usar preços nativos de nuvem, que incluem o [dimensionamento automático](../cosmos-db/manage-scale-cassandra.md#use-autoscale) e as ofertas [sem servidor](../cosmos-db/serverless.md). |
| **Análise**| Você deseja ter controle total sobre o provisionamento de pipelines analíticos, independentemente da sobrecarga para criá-los e mantê-los. | Você deseja usar serviços analíticos baseados em nuvem, como o Azure Databricks. | Você quer ter análise transacional híbrida quase em tempo real incorporada à plataforma com o [Link do Azure Synapse para Cosmos DB](../cosmos-db/synapse-link.md). |
| **Padrão da carga de trabalho**| O estado de carga de trabalho é razoavelmente estável e você não precisa dimensionar os nós do cluster com frequência. | A carga de trabalho é volátil e você precisa ser capaz de escalar ou reduzir verticalmente os nós em um datacenter ou de adicionar/remover datacenters facilmente. | A carga de trabalho costuma ser volátil e você precisa ser capaz de escalar ou reduzir verticalmente com rapidez e em volumes significativos. |
| **SLAs**| Você está satisfeito com seus processos para manter os SLAs em termos de consistência, taxa de transferência, disponibilidade e recuperação de desastre. | Você está satisfeito com seus processos para manter os SLAs em termos de consistência, taxa de transferência e disponibilidade, mas precisa de ajuda com backups. | Você quer SLAs totalmente abrangentes em termos de consistência, taxa de transferência, disponibilidade e recuperação de desastre. |

## <a name="next-steps"></a>Próximas etapas

Introdução com um dos nossos guias de início rápido:

* [Criar um cluster da instância gerenciada no portal do Azure](create-cluster-portal.md)