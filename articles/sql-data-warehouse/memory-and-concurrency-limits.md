---
title: Limites de memória e simultaneidade no Azure SQL Data Warehouse | Microsoft Docs
description: Exiba os limites de memória e simultaneidade alocados para os vários níveis de desempenho e classes de recursos no SQL Data Warehouse do Azure.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload-management
ms.date: 03/15/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 0a92c032027e772020eda0b626a6dc6db024bf57
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595568"
---
# <a name="memory-and-concurrency-limits-for-azure-sql-data-warehouse"></a>Memória e limites simultâneos para SQL Data Warehouse do Azure
Exiba os limites de memória e simultaneidade alocados para os vários níveis de desempenho e classes de recursos no SQL Data Warehouse do Azure. Para obter mais informações e aplicar esses recursos em seu plano de gerenciamento de carga de trabalho, consulte [Classes de recursos para gerenciamento de carga de trabalho](resource-classes-for-workload-management.md). 

Atualmente existem duas gerações disponíveis com o SQL Data Warehouse - Gen1 e Gen2. Recomendamos que você aproveite a Gen2 do SQL Data Warehouse para obter o melhor desempenho para sua carga de trabalho de data warehouse. Gen2 apresenta um novo cache de disco de estado sólido NVMe que mantém os dados acessados com mais frequência perto de CPUs. Isso remove a e/s remoto para suas cargas de trabalho mais intensas e mais exigentes. Além de desempenho, Gen2 oferece o maior nível de escala permitindo expandir até 30.000 unidades de depósito de dados e fornecer armazenamento Colunar ilimitado. Nós ainda dá suporte à geração anterior (Gen1) do SQL Data Warehouse e reter os mesmos recursos; No entanto, é recomendável [atualização para Gen2](upgrade-to-latest-generation.md) assim que for possível. 

## <a name="data-warehouse-capacity-settings"></a>Configurações de capacidade do banco de dados de data warehouse
As tabelas a seguir mostram a capacidade máxima para o data warehouse em diferentes níveis de desempenho. Para alterar o nível de desempenho, consulte [escala de computação - portal](quickstart-scale-compute-portal.md).

### <a name="gen2"></a>Gen2

Gen2 fornece 2,5 vezes mais memória por consulta que o Gen1. Essa memória extra ajuda a Gen2 entregar seu desempenho rápido.  Os níveis de desempenho para o intervalo da Gen2 de DW100c DW30000c. 

| Nível de desempenho | Nós de computação | Distribuições por nó de computação | Memória máxima por data warehouse (GB) |
|:-----------------:|:-------------:|:------------------------------:|:------------------------------:|
| DW100c            | 1             | 60                             |    60                          |
| DW200c            | 1             | 60                             |   120                          |
| DW300c            | 1             | 60                             |   180                          |
| DW400c            | 1             | 60                             |   240                          |
| DW500c            | 1             | 60                             |   300                          |
| DW1000c           | 2             | 30                             |   600                          |
| DW1500c           | 3             | 20                             |   900                          |
| DW2000c           | 4             | 15                             |  1\.200                          |
| DW2500c           | 5             | 12                             |  1500                          |
| DW3000c           | 6             | 10                             |  1800                          |
| DW5000c           | 10            | 6                              |  3000                          |
| DW6000c           | 12            | 5                              |  3600                          |
| DW7500c           | 15            | 4                              |  4500                          |
| DW10000c          | 20            | 3                              |  6000                          |
| DW15000c          | 30            | 2                              |  9000                          |
| DW30000c          | 60            | 1                              | 18000                          |

O valor máximo de Gen2 DWU é DW30000c, que possui 60 nós de cálculo e uma distribuição por nó de computação. Por exemplo, um data warehouse de 600 TB a DW30000c processa aproximadamente 10 TB por nó de Computação.

### <a name="gen1"></a>Gen1

Os níveis de serviço para o intervalo de Gen1 de DW100 a DW6000. 

| Nível de desempenho | Nós de computação | Distribuições por nó de computação | Memória máxima por data warehouse (GB) |
|:-----------------:|:-------------:|:------------------------------:|:------------------------------:|
| DW100             | 1             | 60                             |  24                            |
| DW200             | 2             | 30                             |  48                            |
| DW300             | 3             | 20                             |  72                            |
| DW400             | 4             | 15                             |  96                            |
| DW500             | 5             | 12                             | 120                            |
| DW600             | 6             | 10                             | 144                            |
| DW1000            | 10            | 6                              | 240                            |
| DW1200            | 12            | 5                              | 288                            |
| DW1500            | 15            | 4                              | 360                            |
| DW2000            | 20            | 3                              | 480                            |
| DW3000            | 30            | 2                              | 720                            |
| DW6000            | 60            | 1                              | 1440                           |

## <a name="concurrency-maximums"></a>Limites máximos de simultaneidade
Para garantir que cada consulta tenha recursos suficientes para executar com eficiência, o SQL Data Warehouse rastreia a utilização de recursos, atribuindo slots de simultaneidade a cada consulta. O sistema coloca consultas em uma fila com base na importância e slots de simultaneidade. Consultas de espera na fila até que slots de simultaneidade suficientes estejam disponíveis. [Importância](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-workload-importance) e slots de simultaneidade determinam a priorização de CPU. Para saber mais, confira [Analisar sua carga de trabalho](analyze-your-workload.md)

### <a name="gen2"></a>Gen2
 
**Classes de recursos estáticos**

A tabela a seguir mostra o número máximo de consultas simultâneas e slots de simultaneidade para cada [classe de recurso estático](resource-classes-for-workload-management.md).  

| Nível de Serviço | Máximo de consultas simultâneas | Slots de simultaneidade disponíveis | Slots usados pelo staticrc10 | Slots usados pelo staticrc20 | Slots usados pelo staticrc30 | Slots usados pelo staticrc40 | Slots usados pelo staticrc50 | Slots usados pelo staticrc60 | Slots usados pelo staticrc70 | Slots usados pelo staticrc80 |
|:-------------:|:--------------------------:|:---------------------------:|:---------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|
| DW100c        |  4                         |    4                        | 1         | 2          | 4          | 4          | 4         |  4         |  4         |  4         |
| DW200c        |  8                         |    8                        | 1         | 2          | 4          | 8          |  8         |  8         |  8         |  8        |
| DW300c        | 12                         |   12                        | 1         | 2          | 4          | 8          |  8         |  8         |  8         |   8        |
| DW400c        | 16                         |   16                        | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW500c        | 20                         |   20                        | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW1000c       | 32                         |   40                        | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW1500c       | 32                         |   60                        | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW2000c       | 48                         |   80                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW2500c       | 48                         |  100                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW3000c       | 64                         |  120                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW5000c       | 64                         |  200                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW6000c       | 128                        |  240                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW7500c       | 128                        |  300                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW10000c      | 128                        |  400                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW15000c      | 128                        |  600                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW30000c      | 128                        | 1\.200                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |

**Classes de recursos dinâmicos**

> [!NOTE]
> A classe de recurso smallrc na Gen2 adiciona dinamicamente memória à medida que o nível de serviço aumenta e dá suporte apenas a um máximo de 32 consultas simultâneas no DW1000c e 4 no DW100c.  Quando a instância é dimensionada além do DW1500c, os slots de simultaneidade e a memória usados pelo smallrc aumentam conforme o nível de serviço aumenta. 
>
>

A tabela a seguir mostra o número máximo de consultas simultâneas e slots de simultaneidade para cada [classe de recurso dinâmico](resource-classes-for-workload-management.md). Diferentemente de Gen1, classes de recursos dinâmicos em Gen2 são verdadeiramente dinâmicos.  Gen2 usa uma alocação de porcentagem de memória de 3-10-22-70 para classes de recursos pequeno médio-grandes xlarge em todos os níveis de serviço.

| Nível de Serviço | Máximo de consultas simultâneas | Slots de simultaneidade disponíveis | Slots usados pelo smallrc | Slots usados pelo mediumrc | Slots usados pelo largerc | Slots usados pelo xlargerc |
|:-------------:|:--------------------------:|:---------------------------:|:---------------------:|:----------------------:|:---------------------:|:----------------------:|
| DW100c        |  4                         |    4                        | 1                     |  1                     |  1                    |   2                    |
| DW200c        |  8                         |    8                        | 1                     |  1                     |  1                    |   5                    |
| DW300c        | 12                         |   12                        | 1                     |  1                     |  2                    |   8                    |
| DW400c        | 16                         |   16                        | 1                     |  1                     |  3                    |  11                    |
| DW500c        | 20                         |   20                        | 1                     |  2                     |  4                    |  14                    |
| DW1000c       | 32                         |   40                        | 1                     |  4                     |  8                    |  28                    |
| DW1500c       | 32                         |   60                        | 1                     |  6                     |  13                   |  42                    |
| DW2000c       | 32                         |   80                        | 2                     |  8                     |  17                   |  56                    |
| DW2500c       | 32                         |  100                        | 3                     | 10                     |  22                   |  70                    |
| DW3000c       | 32                         |  120                        | 3                     | 12                     |  26                   |  84                    |
| DW5000c       | 32                         |  200                        | 6                     | 20                     |  44                   | 140                    |
| DW6000c       | 32                         |  240                        | 7                     | 24                     |  52                   | 168                    |
| DW7500c       | 32                         |  300                        | 9                     | 30                     |  66                   | 210                    |
| DW10000c      | 32                         |  400                        | 12                    | 40                     |  88                   | 280                    |
| DW15000c      | 32                         |  600                        | 18                    | 60                     | 132                   | 420                    |
| DW30000c      | 32                         | 1\.200                        | 36                    | 120                    | 264                   | 840                    |



#### <a name="gen1"></a>Gen1

Classes de recursos estáticos

A tabela a seguir mostra o máximo de consultas simultâneas e slots de simultaneidade para cada [classe de recurso estática ](resource-classes-for-workload-management.md) em**Gen1**.

| Nível de serviço | Máximo de consultas simultâneas | Máximo de slots de simultaneidade | Slots usados pelo staticrc10 | Slots usados pelo staticrc20 | Slots usados pelo staticrc30 | Slots usados pelo staticrc40 | Slots usados pelo staticrc50 | Slots usados pelo staticrc60 | Slots usados pelo staticrc70 | Slots usados pelo staticrc80 |
|:-------------:|:--------------------------:|:-------------------------:|:---------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|
| DW100         | 4                          |   4                       | 1         | 2          | 4          | 4          |  4         |  4         |  4         |   4        |
| DW200         | 8                          |   8                       | 1         | 2          | 4          | 8          |  8         |  8         |  8         |   8        |
| DW300         | 12                         |  12                       | 1         | 2          | 4          | 8          |  8         |  8         |  8         |   8        |
| DW400         | 16                         |  16                       | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW500         | 20                         |  20                       | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW600         | 24                         |  24                       | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW1000        | 32                         |  40                       | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW1200        | 32                         |  48                       | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW1500        | 32                         |  60                       | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW2000        | 48                         |  80                       | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW3000        | 64                         | 120                       | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW6000        | 128                        | 240                       | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |

Classes de recursos dinâmicos
> [!NOTE]
> A classe de recurso de smallrc na Gen1 aloca uma quantidade fixa de memória por consulta da mesma maneira staticrc10 de classe de recurso estático.  Como smallrc é estático, ela tem a capacidade de dimensionar a 128 consultas simultâneas. 
>
>

A tabela a seguir mostra o máximo de consultas simultâneas e slots de simultaneidade para cada[classe de recurso dinâmico](resource-classes-for-workload-management.md) em**Gen1**.

| Nível de serviço | Máximo de consultas simultâneas | Slots de simultaneidade disponíveis | Slots usados pelo smallrc | Slots usados pelo mediumrc | Slots usados pelo largerc | Slots usados pelo xlargerc |
|:-------------:|:--------------------------:|:---------------------------:|:-------:|:--------:|:-------:|:--------:|
| DW100         |  4                         |   4                         | 1       |  1       |  2      |   4      |
| DW200         |  8                         |   8                         | 1       |  2       |  4      |   8      |
| DW300         | 12                         |  12                         | 1       |  2       |  4      |   8      |
| DW400         | 16                         |  16                         | 1       |  4       |  8      |  16      |
| DW500         | 20                         |  20                         | 1       |  4       |  8      |  16      |
| DW600         | 24                         |  24                         | 1       |  4       |  8      |  16      |
| DW1000        | 32                         |  40                         | 1       |  8       | 16      |  32      |
| DW1200        | 32                         |  48                         | 1       |  8       | 16      |  32      |
| DW1500        | 32                         |  60                         | 1       |  8       | 16      |  32      |
| DW2000        | 48                         |  80                         | 1       | 16       | 32      |  64      |
| DW3000        | 64                         | 120                         | 1       | 16       | 32      |  64      |
| DW6000        | 128                        | 240                         | 1       | 32       | 64      | 128      |


Quando um desses limites for atingido, novas consultas serão enfileiradas e executadas em uma base de primeira a entrar, primeira a sair.  À medida que uma consulta é concluída e o número de consultas e slots ficam abaixo do limite, o SQL Data Warehouse libera as consultas em fila. 

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como usar as classes de recursos para otimizar ainda mais a carga de trabalho, examine os seguintes artigos:
* [Classes de recursos para gerenciamento de carga de trabalho](resource-classes-for-workload-management.md)
* [Análise de sua carga de trabalho](analyze-your-workload.md)

