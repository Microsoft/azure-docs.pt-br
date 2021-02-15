---
title: Camada de serviço de Uso Geral
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Saiba mais sobre a camada de serviço Uso Geral para o banco de dados SQL do Azure e o SQL Instância Gerenciada do Azure.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 02/07/2019
ms.openlocfilehash: ee218253309995e721c97f4a7f7b4547b32f7c36
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85986634"
---
# <a name="general-purpose-service-tier---azure-sql-database-and-azure-sql-managed-instance"></a>Camada de serviço Uso Geral-banco de dados SQL do Azure e SQL Instância Gerenciada do Azure
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

> [!NOTE]
> A camada de serviço Uso Geral no modelo de compra baseado em vCore é chamada de camada de serviço Standard no modelo de compra baseado em DTU. Para obter uma comparação do modelo de compra baseado em vCore com o modelo de compra baseado em DTU, consulte [comprando modelos e recursos](purchasing-models.md).

O banco de dados SQL do Azure e o Azure SQL Instância Gerenciada são baseados na arquitetura do mecanismo de banco de dados SQL Server adaptada para o ambiente de nuvem a fim de garantir a disponibilidade de 99,99% mesmo nos casos de falhas de infraestrutura. 

Há duas camadas de serviço usadas pelo banco de dados SQL do Azure e o SQL Instância Gerenciada: 

- Uso Geral
- Comercialmente Crítico

O banco de dados SQL do Azure também tem uma terceira camada de serviço, que não está disponível no momento para o Azure SQL Instância Gerenciada:

- Hiperescala

O modelo de arquitetura para a camada de serviço do Uso Geral é baseado em uma separação de computação e armazenamento. Esse modelo de arquitetura baseia-se na alta disponibilidade e na confiabilidade do Armazenamento de Blobs do Azure, que replica os arquivos de banco de dados de forma transparente e garante que não haja perda de dados se ocorrer uma falha na infraestrutura subjacente.

A figura a seguir mostra quatro nós no modelo de arquitetura padrão com as camadas de computação e armazenamento separadas.

![Separação de computação e armazenamento](./media/service-tier-general-purpose/general-purpose-service-tier.png)

No modelo de arquitetura da camada de serviço Uso Geral, há duas camadas:

- Uma camada de computação sem estado que está executando o processo `sqlservr.exe` e contém apenas dados temporários e armazenados em cache (por exemplo - cache de plano, conjunto de buffers, conjunto de armazenamentos de colunas). Esse nó sem estado é operado pelo Service Fabric do Azure que inicializa o processo, controla a integridade do nó e executa o failover para outro local, se necessário.
- Uma camada de dados com estado, com arquivos de banco de dados (.mdf/.ldf) que são armazenados no Armazenamento de Blobs do Azure. O Armazenamento de Blobs do Azure garante que não haja perda de dados de nenhum registro colocado em qualquer arquivo de banco de dados. O armazenamento do Azure tem redundância/disponibilidade de dados interna que garante que todos os registros no arquivo de log ou na página no arquivo de dados serão preservados mesmo que o processo falhe.

Sempre que o mecanismo de banco de dados ou o sistema operacional for atualizado, alguma parte da infraestrutura subjacente falhar ou se algum problema crítico for detectado no `sqlservr.exe` processo, o Azure Service Fabric moverá o processo sem estado para outro nó de computação sem estado. Há um conjunto de nós sobressalentes que fica esperando a execução do novo serviço de computação se ocorrer um failover do nó primário a fim de minimizar o tempo de failover. Os dados na camada de armazenamento do Azure não são afetados e os arquivos de dados/log são anexados ao processo inicializado recentemente. Esse processo garante a disponibilidade de 99,99%, mas pode haver alguns impactos no desempenho em cargas de trabalho pesadas em execução devido ao tempo de transição e ao fato de o novo nó começar com o cache frio.

## <a name="when-to-choose-this-service-tier"></a>Quando escolher essa camada de serviço

A camada de serviço Uso Geral é uma camada de serviço padrão no banco de dados SQL do Azure e SQL Instância Gerenciada do Azure que é projetada para a maioria das cargas de trabalho genéricas. Se você precisar de um mecanismo de banco de dados totalmente gerenciado com SLA de 99,99% com latência de armazenamento entre 5 e 10 MS que correspondam SQL Server em uma máquina virtual do Azure na maioria dos casos, a camada de Uso Geral é a opção para você.

## <a name="next-steps"></a>Próximas etapas

- Encontre características de recursos (número de núcleos, e/s, memória) da camada Uso Geral/Standard no [SQL instância gerenciada](../managed-instance/resource-limits.md#service-tier-characteristics), banco de dados individual no modelo de [VCORE](resource-limits-vcore-single-databases.md#general-purpose---provisioned-compute---gen4) ou [modelo de DTU](resource-limits-dtu-single-databases.md#single-database-storage-sizes-and-compute-sizes), ou pool elástico no modelo de [VCORE](resource-limits-vcore-elastic-pools.md#general-purpose---provisioned-compute---gen4) e do tipo de [DTU](resource-limits-dtu-elastic-pools.md#standard-elastic-pool-limits).
- Saiba mais sobre camadas [Comercialmente Críticas](service-tier-business-critical.md) e de [Hiperescala](service-tier-hyperscale.md).
- Saiba mais sobre [Service Fabric](../../service-fabric/service-fabric-overview.md).
- Para obter mais opções de alta disponibilidade e recuperação de desastres, consulte [continuidade de negócios](business-continuity-high-availability-disaster-recover-hadr-overview.md).
