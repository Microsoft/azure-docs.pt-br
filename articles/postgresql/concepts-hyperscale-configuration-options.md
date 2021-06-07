---
title: Opções de configuração – Citus (hiperescala) – banco de dados do Azure para PostgreSQL
description: Opções para um grupo de servidores de hiperescala (Citus), incluindo computação de nó, armazenamento e regiões.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 1/12/2021
ms.openlocfilehash: 48537483501165d4a978afdbd05560613170d187
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98165604"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-configuration-options"></a>Banco de dados do Azure para PostgreSQL – opções de configuração do Citus (hiperescala)

## <a name="compute-and-storage"></a>Computação e armazenamento
 
Você pode selecionar as configurações de computação e armazenamento independentemente para nós de trabalho e o nó de coordenador em um grupo de servidores de hiperescala (Citus).  Os recursos de computação são fornecidos como vCores, que representam a CPU lógica do hardware subjacente. O tamanho do armazenamento para provisionamento refere-se à capacidade disponível para os nós de coordenador e de trabalho em seu grupo de servidores de hiperescala (Citus). O armazenamento inclui arquivos de banco de dados, arquivos temporários, logs de transações e logs do servidor Postgres.
 
| Recurso              | Nó de trabalho           | Nó de coordenador      |
|-----------------------|-----------------------|-----------------------|
| Computação, vCores       | 4, 8, 16, 32, 64      | 4, 8, 16, 32, 64      |
| Memória por vCore, GiB | 8                     | 4                     |
| Tamanho do armazenamento, TiB     | 0,5, 1, 2             | 0,5, 1, 2             |
| Tipo de armazenamento          | Uso geral (SSD) | Uso geral (SSD) |
| IOPS                  | Até 3 IOPS/GiB      | Até 3 IOPS/GiB      |

A quantidade total de RAM em um único nó de hiperescala (Citus) baseia-se no número selecionado de vCores.

| vCores | Um nó de trabalho, GiB RAM | Nó de coordenador, GiB de RAM |
|--------|--------------------------|---------------------------|
| 4      | 32                       | 16                        |
| 8      | 64                       | 32                        |
| 16     | 128                      | 64                        |
| 32     | 256                      | 128                       |
| 64     | 432                      | 256                       |

A quantidade total de armazenamento que você provisiona também define a capacidade de e/s disponível para cada nó de trabalho e coordenador.

| Tamanho do armazenamento, TiB | IOPS máximo |
|-------------------|--------------|
| 0.5               | 1.536        |
| 1                 | 3.072        |
| 2                 | 6.148        |

Para todo o cluster de hiperescala (Citus), o IOPS agregado trabalha com os seguintes valores:

| Nós de trabalho | 0,5 TiB, IOPS total | 1 TiB, IOPS total | 2 TiB, IOPS total |
|--------------|---------------------|-------------------|-------------------|
| 2            | 3.072               | 6.144             | 12.296            |
| 3            | 4.608               | 9.216             | 18.444            |
| 4            | 6.144               | 12.288            | 24.592            |
| 5            | 7.680               | 15.360            | 30.740            |
| 6            | 9.216               | 18.432            | 36.888            |
| 7            | 10.752              | 21.504            | 43.036            |
| 8            | 12.288              | 24.576            | 49.184            |
| 9            | 13.824              | 27.648            | 55.332            |
| 10           | 15.360              | 30.720            | 61.480            |
| 11           | 16.896              | 33.792            | 67.628            |
| 12           | 18.432              | 36.864            | 73.776            |
| 13           | 19.968              | 39.936            | 79.924            |
| 14           | 21.504              | 43.008            | 86.072            |
| 15           | 23.040              | 46.080            | 92.220            |
| 16           | 24.576              | 49.152            | 98.368            |
| 17           | 26.112              | 52.224            | 104.516           |
| 18           | 27.648              | 55.296            | 110.664           |
| 19           | 29.184              | 58.368            | 116.812           |
| 20           | 30.720              | 61.440            | 122.960           |

## <a name="regions"></a>Regiões
Os grupos de servidores de hiperescala (Citus) estão disponíveis nas seguintes regiões do Azure:

* Américas:
    * Canadá Central
    * Centro dos EUA
    * Leste dos EUA
    * Leste dos EUA 2
    * Centro-Norte dos EUA
    * Oeste dos EUA 2
* Pacífico Asiático:
    * Leste da Austrália
    * Japan East
    * Coreia Central
    * Sudeste Asiático
* Européia
    * Norte da Europa
    * Sul do Reino Unido
    * Europa Ocidental

Algumas dessas regiões podem não ser ativadas inicialmente em todas as assinaturas do Azure. Se você quiser usar uma região da lista acima e não a vir na sua assinatura, ou se quiser usar uma região que não esteja nessa lista, abra uma [solicitação de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="limits-and-limitations"></a>Limites e limitações

A seção a seguir descreve a capacidade e os limites funcionais no serviço de hiperescala (Citus).

### <a name="maximum-connections"></a>Número máximo de conexões

Cada conexão PostgreSQL (até mesmo ociosas) usa pelo menos 10 MB de memória, portanto, é importante limitar conexões simultâneas. Aqui estão os limites que escolhemos para manter os nós íntegros:

* Nó de coordenador
   * Máximo de conexões: 300
   * Máximo de conexões de usuário: 297
* Nó de trabalho
   * Máximo de conexões: 600
   * Máximo de conexões de usuário: 597

As tentativas de conexão além desses limites falharão com um erro. O sistema reserva três conexões para nós de monitoramento, e é por isso que há três conexões menos disponíveis para consultas de usuário do que o total de conexões.

O estabelecimento de novas conexões leva tempo. Isso funciona com a maioria dos aplicativos, que solicita muitas conexões de curta duração. É recomendável usar um pool de conexões para reduzir as transações ociosas e reutilizar as conexões existentes. Para saber mais, visite nossa [postagem no blog](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/not-all-postgres-connection-pooling-is-equal/ba-p/825717).

### <a name="storage-scaling"></a>Dimensionamento de armazenamento

O armazenamento em nós de coordenador e de trabalho pode ser escalado verticalmente (aumentado), mas não pode ser reduzido (diminuído).

### <a name="storage-size"></a>Tamanho de armazenamento

Há suporte para até 2 TiB de armazenamento em nós de coordenador e de trabalho. Consulte as opções de armazenamento disponíveis e o cálculo de IOPS [acima](#compute-and-storage) para tamanhos de nó e cluster.

### <a name="database-creation"></a>Criação de banco de dados

O portal do Azure fornece credenciais para se conectar a exatamente um grupo de servidores de banco de dados por Citus (hiperescala), o `citus` banco de dados. A criação de outro banco de dados não é permitida no momento e o comando CREATE DATABASE falhará com um erro.

## <a name="pricing"></a>Preços
Para as informações mais recentes sobre preços, consulte a [página de preços](https://azure.microsoft.com/pricing/details/postgresql/) do serviço.
Para ver o custo da configuração desejada, o [portal do Azure](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) mostra o custo mensal na guia **Configurar** com base nas opções selecionadas. Se você não tiver uma assinatura do Azure, poderá usar a calculadora de preços do Azure para obter um preço estimado. No site da [calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/) , selecione **Adicionar itens**, expanda a categoria **bancos** de dados e escolha **banco de dados do Azure para PostgreSQL – Citus (hiperescala)** para personalizar as opções.
 
## <a name="next-steps"></a>Próximas etapas
Saiba como [criar um grupo de servidores do Citus (hiperescala) no portal](quickstart-create-hyperscale-portal.md).
