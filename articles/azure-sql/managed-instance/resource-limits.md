---
title: Limites de recursos
titleSuffix: Azure SQL Managed Instance
description: Este artigo fornece uma visão geral dos limites de recursos para o Azure SQL Instância Gerenciada.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: reference
author: bonova
ms.author: bonova
ms.reviewer: sstein, jovanpop, sachinp
ms.date: 09/14/2020
ms.openlocfilehash: 9a96da607ceea5a6d5cb6ef02df5a9a4db24562e
ms.sourcegitcommit: e8bd58dbcfe0eae45979d86e071778b9aec40b6c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2020
ms.locfileid: "97770954"
---
# <a name="overview-of-azure-sql-managed-instance-resource-limits"></a>Visão geral dos limites de recursos da Instância Gerenciada de SQL do Azure
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Este artigo fornece uma visão geral das características técnicas e dos limites de recursos para o Azure SQL Instância Gerenciada e fornece informações sobre como solicitar um aumento para esses limites.

> [!NOTE]
> Para diferenças em recursos suportados e instruções T-SQL, consulte [Diferenças de recursos](../database/features-comparison.md) e [Suporte à instrução T-SQL](transact-sql-tsql-differences-sql-server.md). Para obter as diferenças gerais entre as camadas de serviço para o banco de dados SQL do Azure e o SQL Instância Gerenciada consulte [comparação da camada de serviço](../database/service-tiers-general-purpose-business-critical.md#service-tier-comparison).

## <a name="hardware-generation-characteristics"></a>Características de geração de hardware

O SQL Instância Gerenciada tem características e limites de recursos que dependem da infraestrutura e da arquitetura subjacentes. O SQL Instância Gerenciada pode ser implantado em duas gerações de hardware: Gen4 e Gen5. As gerações de hardware têm características diferentes, conforme descrito na tabela a seguir:

|   | **Gen4** | **Gen5** |
| --- | --- | --- |
| **Hardware** | Processadores Intel® E5-2673 v3 (Haswell) 2,4 GHz, vCore de SSD anexado = 1 PP (núcleo físico) | Intel® E5-2673 V4 (Broadwell) 2,3 GHz, processadores Intel® SP-8160 (Skylake) e Intel® 8272CL (Cascadey Lake) 2,5 GHz, SSD Fast NVMe, vCore = 1 LP (Hyper-thread) |
| **Número de vCores** | 8, 16, 24 vCores | 4, 8, 16, 24, 32, 40, 64, 80 vCores |
| **Memória máxima (taxa de memória/núcleo)** | 7 GB por vCore<br/>Adicione mais vCores para obter mais memória. | 5.1 GB por vCore<br/>Adicione mais vCores para obter mais memória. |
| **Memória máxima do OLTP In-Memory** | Limite de instância: 1 a 1,5 GB por vCore| Limite de instância: 0,8 a 1,65 GB por vCore |
| **Armazenamento reservado de instância máx.** |  Uso Geral: 8 TB<br/>Comercialmente Crítico: 1 TB | Uso Geral: 8 TB<br/> Comercialmente Crítico 1 TB, 2 TB ou 4 TB, dependendo do número de núcleos |

> [!IMPORTANT]
> - O hardware Gen4 está sendo descontinuado e não está mais disponível para novas implantações. Todas as novas instâncias do SQL Instância Gerenciada devem ser implantadas em hardware Gen5.
> - Considere [mover sua instância do SQL instância gerenciada para](../database/service-tiers-vcore.md) o hardware de geração 5 para experimentar uma ampla gama de escalabilidade de armazenamento e vCore, rede acelerada, melhor desempenho de e/s e latência mínima.

### <a name="in-memory-oltp-available-space"></a>Espaço disponível no OLTP na memória 

A quantidade de espaço OLTP na memória na camada de serviço [comercialmente crítico](../database/service-tier-business-critical.md) depende do número de vCores e da geração de hardware. A tabela a seguir lista os limites de memória que podem ser usados para objetos OLTP na memória.

| Espaço OLTP na memória  | **Gen5** | **Gen4** |
| --- | --- | --- |
| 4 vCores  | 3,14 GB | |   
| 8 vCores  | 6,28 GB | 8 GB |
| 16 vCores | 15,77 GB | 20 GB |
| 24 vCores | 25,25 GB | 36 GB |
| 32 vCores | 37,94 GB | |
| 40 vCores | 52,23 GB | |
| 64 vCores | 99,9 GB    | |
| 80 vCores | 131,68 GB| |

## <a name="service-tier-characteristics"></a>Características de camada de serviço

O SQL Instância Gerenciada tem duas camadas de serviço: [uso geral](../database/service-tier-general-purpose.md) e [comercialmente crítico](../database/service-tier-business-critical.md). Essas camadas fornecem [recursos diferentes](../database/service-tiers-general-purpose-business-critical.md), conforme descrito na tabela a seguir.

> [!Important]
> Comercialmente Crítico camada de serviço fornece uma cópia interna adicional da Instância Gerenciada SQL (réplica secundária) que pode ser usada para a carga de trabalho somente leitura. Se você puder separar consultas de leitura/gravação e consultas de somente leitura/análise/relatório, você receberá duas vezes o vCores e a memória pelo mesmo preço. A réplica secundária pode atrasar alguns segundos atrás da instância primária, portanto, ela foi projetada para descarregar cargas de trabalho de relatório/análise que não precisam de um estado de dados atual exato. Na tabela a seguir, as **consultas somente leitura** são as consultas que são executadas na réplica secundária.

| **Recurso** | **Uso Geral** | **Comercialmente Crítico** |
| --- | --- | --- |
| Número de vCores\* | Gen4: 8, 16, 24<br/>Gen5:4, 8, 16, 24, 32, 40, 64, 80 | Gen4: 8, 16, 24 <br/> Gen5:4, 8, 16, 24, 32, 40, 64, 80 <br/>\*O mesmo número de vCores é dedicado para consultas somente leitura. |
| Memória máxima | Gen4:56 GB-168 GB (7 GB/vCore)<br/>Gen5:20,4 GB-408 GB (5.1 GB/vCore)<br/>Adicione mais vCores para obter mais memória. | Gen4:56 GB-168 GB (7 GB/vCore)<br/>Gen5:20,4 GB-408 GB (5.1 GB/vCore) para consultas de leitura/gravação<br/>+ 20,4 GB adicionais-408 GB (5.1 GB/vCore) para consultas somente leitura.<br/>Adicione mais vCores para obter mais memória. |
| Tamanho máximo de armazenamento de instância (reservado) | -2 TB para 4 vCores (somente Gen5)<br/>-8 TB para outros tamanhos | Gen4:1 TB <br/> Gen5: <br/>-1 TB para 4, 8, 16 vCores<br/>- 2 TB para 24 vCores<br/>- 4 TB para 32, 40, 64, 80 vCores |
| Tamanho máximo do banco de dados | Até o tamanho da instância disponível no momento (máximo de 2 TB-8 TB, dependendo do número de vCores). | Até o tamanho da instância disponível no momento (máximo de 1 TB-4 TB, dependendo do número de vCores). |
| Tamanho máximo de TempDB | Limitado a 24 GB/vCore (96-1.920 GB) e ao tamanho do armazenamento de instância disponível no momento.<br/>Adicione mais vCores para obter mais espaço de TempDB.<br/> O tamanho do arquivo de log é limitado a 120 GB.| Até o tamanho do armazenamento de instância disponível no momento. |
| Número máximo de bancos de dados por instância | 100 bancos de dados de usuário, a menos que o limite de tamanho de armazenamento de instância tenha sido atingido. | 100 bancos de dados de usuário, a menos que o limite de tamanho de armazenamento de instância tenha sido atingido. |
| Número máximo de arquivos de banco de dados por instância | Até 280, a menos que o limite de tamanho do armazenamento de instância ou [do espaço de alocação do armazenamento em disco Premium do Azure](../database/doc-changes-updates-release-notes.md#exceeding-storage-space-with-small-database-files) tenha sido atingido. | 32.767 arquivos por banco de dados, a menos que o limite de tamanho do armazenamento de instância tenha sido atingido. |
| Tamanho máximo do arquivo de dados | Limitado ao tamanho de armazenamento da instância disponível no momento (máx. 2 TB-8 TB) e ao [espaço de alocação do armazenamento em disco Premium do Azure](../database/doc-changes-updates-release-notes.md#exceeding-storage-space-with-small-database-files). | Limitado ao tamanho de armazenamento de instância disponível no momento (até 1 TB-4 TB). |
| Tamanho máximo do arquivo de log | Limitado a 2 TB e tamanho de armazenamento de instância disponível no momento. | Limitado a 2 TB e tamanho de armazenamento de instância disponível no momento. |
| Dados/Log IOPS (aproximado) | Até 30-40 K IOPS por instância *, 500-7500 por arquivo<br/>\*[Aumentar o tamanho do arquivo para obter mais IOPS](#file-io-characteristics-in-general-purpose-tier)| 10 k-200 K (4000 IOPS/vCore)<br/>Adicione mais vCores para obter melhor desempenho de e/s. |
| Limite de taxa de transferência de gravação de log (por instância) | 3 MB/s por vCore<br/>Máximo de 120 MB/s por instância<br/>22-65 MB/s por BD<br/>\*[Aumente o tamanho do arquivo para obter melhor desempenho de e/s](#file-io-characteristics-in-general-purpose-tier) | 4 MB/s por vCore<br/>Máximo de 96 MB/s |
| Taxa de transferência de dados (aproximada) | 100 – 250 MB/s por arquivo<br/>\*[Aumente o tamanho do arquivo para obter melhor desempenho de e/s](#file-io-characteristics-in-general-purpose-tier) | Não limitado. |
| Latência de e/s de armazenamento (aproximada) | 5-10 ms | 1-2 ms |
| OLTP na memória | Sem suporte | Disponível, o [tamanho depende do número de vCore](#in-memory-oltp-available-space) |
| Máx. de sessões | 30000 | 30000 |
| Máximo de trabalhos simultâneos (solicitações) | Gen4: 210 x número de vCores + 800<br>Gen5: 105 x número de vCores + 800 | Gen4: 210 x contagem de vCore + 800<br>Gen5: 105 * contagem de vCore + 800 |
| [Réplicas somente leitura](../database/read-scale-out.md) | 0 | 1 (incluído no preço) |
| Isolamento de computação | Não há suporte para Gen5 porque instâncias de Uso Geral podem compartilhar hardware físico com outras instâncias<br/>Não há suporte para Gen4 devido à reprovação|Gen5:<br/>-com suporte para 40, 64, 80 vCores<br/>-sem suporte para outros tamanhos<br/><br/>Não há suporte para Gen4 devido à reprovação|


Algumas considerações adicionais: 

- O **tamanho do armazenamento de instância disponível no momento** é a diferença entre o tamanho da instância reservada e o espaço de armazenamento usado.
- Os dados e o tamanho do arquivo de log nos bancos de dado do usuário e do sistema são incluídos no tamanho do armazenamento da instância que é comparado com o limite de tamanho máximo de armazenamento. Use a exibição do sistema [Sys.master_files](/sql/relational-databases/system-catalog-views/sys-master-files-transact-sql) para determinar o total de espaço usado por bancos de dados. Logs de erros não são persistentes e não são incluídos no tamanho. Backups não são incluídos no tamanho de armazenamento.
- A taxa de transferência e o IOPS na camada de Uso Geral também dependem do [tamanho do arquivo](#file-io-characteristics-in-general-purpose-tier) que não é explicitamente limitado pelo instância gerenciada do SQL.
  Você pode criar outra réplica legível em uma região diferente do Azure usando [grupos de failover automático](../database/auto-failover-group-configure.md)
- IOPS de instância máxima dependem do layout do arquivo e da distribuição da carga de trabalho. Por exemplo, se você criar arquivos de 7 x 1 TB com 5K IOPS máximos cada e 7 arquivos pequenos (menores que 128 GB) com 500 IOPS cada, poderá obter 38500 IOPS por instância (7x5000 + 7x500) se sua carga de trabalho puder usar todos os arquivos. Observe que alguns IOPS também são usados para backups automáticos.

Encontre mais informações sobre os [limites de recursos nos pools do SQL instância gerenciada neste artigo](instance-pools-overview.md#resource-limitations).

### <a name="file-io-characteristics-in-general-purpose-tier"></a>Características de e/s de arquivo na camada de Uso Geral

Na camada de serviço do Uso Geral, cada arquivo de banco de dados obtém IOPS dedicados e taxa de transferência que dependem do tamanho do arquivo. Arquivos maiores obtêm mais IOPS e taxa de transferência. As características de e/s dos arquivos de banco de dados são mostradas na tabela a seguir:

| Tamanho do arquivo | >= 0 e <= 128 GiB | >128 e <= 512 GiB | >0,5 e <= 1 TiB    | >1 e <= 2 TiB    | >2 e <= 4 TiB | >4 e <= 8 TiB |
|---------------------|-------|-------|-------|-------|-------|-------|-------|
| IOPS por arquivo       | 500   | 2300              | 5.000              | 7500              | 7500              | 12.500   |
| Taxa de transferência por arquivo | 100 MiB/s | 150 MiB/s | 200 MiB/s | 250 MiB/s | 250 MiB/s | 480 MiB/s | 

Se você notar alta latência de e/s em algum arquivo de banco de dados ou se perceber que o IOPS/taxa de transferência está atingindo o limite, você pode melhorar [o desempenho aumentando o tamanho do arquivo](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Increase-data-file-size-to-improve-HammerDB-workload-performance/ba-p/823337).

Também há um limite de nível de instância na taxa de transferência de gravação de log máximo (veja acima para obter valores, por exemplo, 22 MB/s), para que você não consiga acessar o arquivo máximo no arquivo de log porque está atingindo o limite de taxa de transferência da instância.

## <a name="supported-regions"></a>Regiões com suporte

O SQL Instância Gerenciada pode ser criado somente em [regiões com suporte](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Para criar um Instância Gerenciada SQL em uma região que não tem suporte no momento, você pode [Enviar uma solicitação de suporte por meio do portal do Azure](../database/quota-increase-request.md).

## <a name="supported-subscription-types"></a>Tipos de assinatura com suporte

O SQL Instância Gerenciada atualmente dá suporte à implantação somente nos seguintes tipos de assinaturas:

- [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Pago conforme o uso](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Provedor de Serviços de Nuvem (CSP)](/partner-center/csp-documents-and-learning-resources)
- [Desenvolvimento/Teste Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [Desenvolvimento/Teste pré-pago](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Assinaturas com crédito Azure mensal para assinantes do Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)

## <a name="regional-resource-limitations"></a>Limitações de recursos regionais

> [!Note]
> Para obter as informações mais recentes sobre disponibilidade de região para assinaturas, primeiro Marque [selecionar uma região](https://aka.ms/sqlcapacity).

Os tipos de assinatura suportados podem conter um número limitado de recursos por região. O SQL Instância Gerenciada tem dois limites padrão por região do Azure (que podem ser aumentados sob demanda por meio da criação de uma [solicitação de suporte especial na portal do Azure](../database/quota-increase-request.md) , dependendo de um tipo de assinatura:

- **Limite de sub-rede**: o número máximo de sub-redes em que as instâncias do SQL instância gerenciada são implantadas em uma única região.
- **limite de unidade VCORE**: o número máximo de unidades VCORE que podem ser implantadas em todas as instâncias em uma única região. Um vCore da GP usa uma unidade vCore e um vCore de BC usa 4 unidades vCore. O número total de instâncias não é limitado, desde que esteja dentro do limite da unidade vCore.

> [!Note]
> Esses limites são configurações padrão e não limitações técnicas. Os limites podem ser aumentados sob demanda criando uma solicitação especial [de suporte no portal do Azure](../database/quota-increase-request.md) se você precisar de mais instâncias na região atual. Como alternativa, você pode criar novas instâncias do SQL Instância Gerenciada em outra região do Azure sem enviar solicitações de suporte.

A tabela a seguir mostra os **limites regionais padrão** para os tipos de assinatura com suporte (os limites padrão podem ser estendidos usando a solicitação de suporte descrita abaixo):

|Tipo de assinatura| Número máximo de sub-redes do SQL Instância Gerenciada | Número máximo de unidades vCore * |
| :---| :--- | :--- |
|Pago conforme o uso|3|320|
|CSP |8 (15 em algumas regiões * *)|960 (1440 em algumas regiões * *)|
|Desenvolvimento/Teste pago conforme o uso|3|320|
|Desenvolvimento/Teste Enterprise|3|320|
|EA|8 (15 em algumas regiões * *)|960 (1440 em algumas regiões * *)|
|Visual Studio Enterprise|2 |64|
|Visual Studio Professional e Plataformas MSDN|2|32|

\* No planejamento de implantações, leve em consideração que a camada de serviço de Comercialmente Crítico (BC) requer quatro (4) vezes mais capacidade de vCore do que a camada de serviço de Uso Geral (GP). Por exemplo: 1 GP vCore = 1 unidade vCore e 1 BC vCore = 4 vCore. Para simplificar a análise de consumo em relação aos limites padrão, resuma as unidades vCore em todas as sub-redes na região em que o SQL Instância Gerenciada está implantado e compare os resultados com os limites de unidade de instância para o tipo de assinatura. O limite **máximo de unidades vCore** aplica-se a cada assinatura em uma região. Não há limite por sub-redes individuais, exceto que a soma de todos os vCores implantados em várias sub-redes deve ser menor ou igual ao **número máximo de unidades vCore**.

\*\* Os limites de maior sub-rede e vCore estão disponíveis nas seguintes regiões: leste da Austrália, leste dos EUA, leste dos EUA 2, Europa Setentrional, Sul EUA Central, Sudeste Asiático, Sul do Reino Unido, Europa Ocidental, oeste dos EUA 2.

> [!IMPORTANT]
> Caso seu limite de vCore e de sub-rede seja 0, isso significa que o limite regional padrão para seu tipo de assinatura não está definido. Você também pode usar a solicitação de aumento de cota para obter acesso à assinatura em uma região específica seguindo o mesmo procedimento, fornecendo os valores de vCore e de sub-rede necessários.

## <a name="request-a-quota-increase"></a>Solicitar um aumento de cota

Se você precisar de mais instâncias em suas regiões atuais, envie uma solicitação de suporte para estender a cota usando o portal do Azure. Para obter mais informações, consulte [aumentos de cota de solicitação para o banco de dados SQL do Azure](../database/quota-increase-request.md).

## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações sobre o SQL Instância Gerenciada, consulte [o que é um instância gerenciada SQL?](sql-managed-instance-paas-overview.md).
- Para obter informações sobre preços, consulte [preços do SQL instância gerenciada](https://azure.microsoft.com/pricing/details/sql-database/managed/).
- Para saber como criar seu primeiro Instância Gerenciada do SQL, consulte [o guia de início rápido](instance-create-quickstart.md).
