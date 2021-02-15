---
title: Camadas de serviço-modelo de compra baseado em DTU
description: Saiba mais sobre as camadas de serviço no modelo de compra baseado em DTU para o banco de dados SQL do Azure para fornecer tamanhos de computação e armazenamento.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: references_regions
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.date: 10/15/2020
ms.reviewer: ''
ms.openlocfilehash: 19178359d1eeb935499a01828f7c53b123e17571
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92793170"
---
# <a name="service-tiers-in-the-dtu-based-purchase-model"></a>Camadas de serviço no modelo de compra baseado em DTU
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

As camadas de serviço no modelo de compra baseado em DTU são diferenciadas por uma gama de tamanhos da computação com quantidade fixa de armazenamento incluído, período de retenção fixo para backups e preço fixo. Todas as camadas de serviço no modelo de compra baseado em DTU fornecem flexibilidade para alterar os tamanhos de computação com [tempo de inatividade](https://azure.microsoft.com/support/legal/sla/sql-database/v1_2/)mínimo; no entanto, há uma mudança no período em que a conectividade é perdida no banco de dados por um curto período de tempo, o que pode ser mitigado usando a lógica de repetição. Os bancos de dados individuais e os pools elásticos são cobrados por hora com base na camada de serviço e no tamanho da computação.

> [!IMPORTANT]
> O [Azure SQL instância gerenciada](../managed-instance/sql-managed-instance-paas-overview.md) não dá suporte a um modelo de compra baseado em DTU. 


> [!NOTE]
> Para obter informações sobre as camadas de serviço com base em vCore, confira [camadas de serviço com base em vCore](service-tiers-vcore.md). Para obter informações sobre como diferenciar camadas de serviço baseadas em DTU e camadas de serviço baseadas em vCore, consulte [comprando modelos](purchasing-models.md).

## <a name="compare-the-dtu-based-service-tiers"></a>Compare as camadas de serviço com base em DTU

Escolher uma camada de serviço depende principalmente da continuidade dos negócios, armazenamento e requisitos de desempenho.

||Basic|Standard|Premium|
| :-- | --: |--:| --:|
|**Carga de trabalho de destino**|Desenvolvimento e produção|Desenvolvimento e produção|Desenvolvimento e produção|
|**SLA de tempo de atividade**|99,99%|99,99%|99,99%|
|**Retenção de backup máxima**|7 dias|35 dias|35 dias|
|**CPU**|Baixo|Baixo, Médio, Alto|Médio, Alto|
|**IOPS (aproximado)**\* |1-4 IOPS por DTU| 1-4 IOPS por DTU | 25 IOPS por DTU|
|**Latência de E/S (aproximada)**|5 ms (leitura), 10 ms (gravação)|5 ms (leitura), 10 ms (gravação)|2 ms (leitura/gravação)|
|**Indexação ColumnStore** |N/D|S3 e acima|Com suporte|
|**OLTP na memória**|N/D|N/D|Com suporte|

\* Todos os IOPS de leitura e gravação em arquivos de dados, incluindo e/s em segundo plano (ponto de verificação e gravador lento)

> [!IMPORTANT]
> Os objetivos de serviço básico, S0, S1 e S2 fornecem menos de um vCore (CPU).  Para cargas de trabalho com uso intensivo de CPU, recomenda-se um objetivo de serviço S3 ou superior. 
>
> Nos objetivos de serviço básico, S0 e S1, os arquivos de banco de dados são armazenados no armazenamento standard do Azure, que usa mídia de armazenamento baseada em HDD (unidade de disco rígido). Esses objetivos de serviço são mais adequados para desenvolvimento, teste e outras cargas de trabalho acessadas com pouca frequência que são menos sensíveis à variabilidade de desempenho.
>

> [!TIP]
> Para ver os limites reais de [governança de recursos](resource-limits-logical-server.md#resource-governance) para um banco de dados ou pool elástico, consulte a exibição de [Sys.dm_user_db_resource_governance](/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) .

> [!NOTE]
> Você pode obter um banco de dados gratuito no banco de dados SQL do Azure na camada de serviço básica em conjunto com uma conta gratuita do Azure para explorar o Azure. Para obter informações, consulte [Crie um banco de dados de nuvem gerenciado com sua conta gratuita do Azure](https://azure.microsoft.com/free/services/sql-database/).

## <a name="single-database-dtu-and-storage-limits"></a>DTU de banco de dados único e limite de armazenamento

Os tamanhos da computação são expressos em termos de DTUs (unidades de transação de banco de dados) para bancos de dados individuais e de eDTUs (unidades de transação do banco de dados elástico) para pools elásticos. Para obter mais informações sobre DTUs e eDTUs, consulte [modelo de compra baseado em DTU](purchasing-models.md#dtu-based-purchasing-model).

||Basic|Standard|Premium|
| :-- | --: | --: | --: |
| **Tamanho máximo de armazenamento** | 2 GB | 1 TB | 4 TB  |
| **Máximo de DTUs** | 5 | 3000 | 4000 |

> [!IMPORTANT]
> Em algumas circunstâncias, talvez seja necessário reduzir um banco de dados para recuperar o espaço não utilizado. Para obter mais informações, consulte [gerenciar o espaço de arquivo no banco de dados SQL do Azure](file-space-manage.md).

## <a name="elastic-pool-edtu-storage-and-pooled-database-limits"></a>EDTU de pool elástico, armazenamento e limites de banco de dados em pool

|| **Basic** | **Standard** | **Premium** |
| :-- | --: | --: | --: |
| **Tamanho máximo de armazenamento por banco de dados**  | 2 GB | 1 TB | 1 TB |
| **Tamanho máximo de armazenamento por pool** | 156 GB | 4 TB | 4 TB |
| **Máximo de eDTUs por banco de dados** | 5 | 3000 | 4000 |
| **Máximo de eDTUs por pool** | 1600 | 3000 | 4000 |
| **Número máximo de bancos de dados por pool** | 500  | 500 | 100 |

> [!IMPORTANT]
> Mais de 1 TB de armazenamento na camada Premium está disponível atualmente em todas as regiões, exceto: Leste da China, Norte da China, Alemanha Central e Alemanha nordeste. Nessas regiões, o armazenamento máximo na camada Premium é limitado a 1 TB.  Para obter mais informações, confira [Limitações atuais de P11-P15](single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb).  
> [!IMPORTANT]
> Em algumas circunstâncias, talvez seja necessário reduzir um banco de dados para recuperar o espaço não utilizado. Para obter mais informações, consulte [gerenciar o espaço de arquivo no banco de dados SQL do Azure](file-space-manage.md).

## <a name="dtu-benchmark"></a>Parâmetro de comparação de DTU

As características físicas (CPU, memória, IO) associadas a cada medida de DTU são calibradas usando um parâmetro de comparação que simula a carga de trabalho do banco de dados real.

### <a name="correlating-benchmark-results-to-real-world-database-performance"></a>Correlacionando resultados do parâmetro de comparação ao desempenho de um banco de dados real

É importante entender que todos os parâmetros de comparação são representativos e indicativos apenas. As taxas de transação obtidas com o aplicativo de parâmetro de comparação não serão iguais às que podem ser obtidas com outros aplicativos. O parâmetro de comparação compreende um conjunto de tipos de transação diferentes executados em um esquema com uma variedade de tabelas e de tipos de dados. Embora o parâmetro de comparação exercite as mesmas operações básicas comuns a todas as cargas de trabalho OLTP, ele não representa qualquer classe específica de banco de dados ou de aplicativo. A meta do parâmetro de comparação é fornecer um guia razoável para o desempenho relativo de um banco de dados que pode ser esperado ao escalar ou reduzir verticalmente entre os tamanhos da computação. Na realidade, os bancos de dados de diferentes tamanhos e complexidade, lidam com combinações diferentes de cargas de trabalho e responderão de formas diferentes. Por exemplo, um aplicativo de E/S intensiva pode atingir os limites de E/S mais cedo ou um aplicativo de uso intensivo de CPU pode atingir os limites de CPU mais cedo. Não há nenhuma garantia de que qualquer banco de dados específico será dimensionado da mesma forma que o parâmetro de comparação em um aumento da carga.

O parâmetro de comparação e sua metodologia são descritos em mais detalhes abaixo.

### <a name="benchmark-summary"></a>Resumo do parâmetro de comparação

O parâmetro de comparação mede o desempenho de um conjunto de operações básicas do banco de dados que ocorrem com mais frequência em cargas de trabalho de OLPT (transação online). Embora o parâmetro de comparação seja projetado com a computação em nuvem em mente, o esquema do banco de dados, o preenchimento dos dados e as transações foram projetados para ser amplamente representativos dos elementos básicos mais comumente usados em cargas de trabalho OLTP.

### <a name="schema"></a>Esquema

O esquema foi projetado para ter variedade e complexidade suficiente para dar suporte a uma ampla gama de operações. O parâmetro de comparação é executado em um banco de dados composto por seis tabelas. As tabelas recaem em três categorias: tamanho fixo, em escala e crescente. Há duas tabelas de tamanho fixo; três tabelas em escala e uma tabela crescente. As tabelas de tamanho fixo têm um número constante de linhas. As tabelas em escala têm uma cardinalidade proporcional ao desempenho do banco de dados, mas não são alteradas durante o parâmetro de comparação. A tabela crescente é dimensionada como uma tabela em escala na carga inicial, mas depois a cardinalidade é alterada no curso da execução do parâmetro de comparação à medida que as linhas são inseridas e excluídas.

O esquema inclui uma combinação de tipos de dados, incluindo inteiros, numéricos, caracteres e data/hora. O esquema inclui chaves primárias e secundárias, mas nenhuma chave estrangeira – ou seja, não há restrições de integridade referencial entre as tabelas.

Um programa de geração de dados gera os dados para o banco de dados inicial. Os dados numéricos e inteiros são gerados com várias estratégias. Em alguns casos, os valores são distribuídos aleatoriamente em um intervalo. Em outros casos, um conjunto de valores é aleatoriamente permutado para garantir que uma distribuição específica seja mantida. Os campos de texto são gerados de uma lista ponderada de palavras para gerar dados de aparência realista.

O banco de dados é dimensionado com base em um "fator de escala". O fator de escala (abreviado como SF) determina a cardinalidade das tabelas em escala e crescentes. Conforme descrito abaixo na seção Usuários e Definição, o tamanho do banco de dados, o número de usuários e o desempenho máximo são dimensionados proporcionalmente entre si.

### <a name="transactions"></a>Transactions

A carga de trabalho consiste em nove tipos de transação, conforme mostrado na tabela a seguir. Cada transação é projetada para realçar um conjunto de características do sistema em particular no mecanismo de banco de dados e no hardware do sistema, com alto contraste em relação às outras transações. Essa abordagem facilita a avaliação do impacto dos diferentes componentes no desempenho geral. Por exemplo, a transação "Leitura Intensa" gera um número significativo de operações de leitura do disco.

| Tipo de transação | Descrição |
| --- | --- |
| Leitura Simples |SELECT; na memória; somente leitura |
| Leitura Média |SELECT; maior parte na memória; somente leitura |
| Leitura Intensa |SELECT; maior parte fora da memória; somente leitura |
| Atualização Simples |UPDATE; na memória; leitura/gravação |
| Atualização Intensa |UPDATE; maior parte fora da memória; leitura/gravação |
| Inserção Simples |INSERT; na memória; leitura/gravação |
| Inserção Intensa |INSERT; maior parte fora da memória; leitura/gravação |
| Excluir |DELETE; combinação de na memória e não na memória; leitura/gravação |
| CPU Intensa |SELECT; na memória; carga de CPU relativamente intensa; somente leitura |

### <a name="workload-mix"></a>Combinação de carga de trabalho

As transações são selecionadas aleatoriamente em uma distribuição ponderada com a combinação geral a seguir. A combinação geral tem uma taxa de leitura/gravação de aproximadamente 2:1.

| Tipo de transação | % de combinação |
| --- | --- |
| Leitura Simples |35 |
| Leitura Média |20 |
| Leitura Intensa |5 |
| Atualização Simples |20 |
| Atualização Intensa |3 |
| Inserção Simples |3 |
| Inserção Intensa |2 |
| Excluir |2 |
| CPU Intensa |10 |

### <a name="users-and-pacing"></a>Usuários e definição

O parâmetro de comparação de desempenho é direcionado de uma ferramenta que envia transações por meio de um conjunto de conexões para simular o comportamento de alguns usuários simultâneos. Embora todas as conexões e transações sejam geradas por máquina, para manter a simplicidade nos referimos a essas conexões como "usuários". Embora cada usuário opere independentemente dos demais, todos os usuários realizam o mesmo ciclo de etapas mostrado abaixo:

1. Estabeleça uma conexão de banco de dados.
2. Repita até receber um sinal para sair:
   - Selecione uma transação aleatoriamente (de uma distribuição ponderada).
   - Execute a transação selecionada e meça o tempo de resposta.
   - Aguarde uma definição de atraso.
3. Feche a conexão de banco de dados.
4. Sair.

A definição de atraso (na etapa 2c) é selecionada aleatoriamente, mas com uma distribuição cuja média é de 1,0 segundo. Portanto, cada usuário pode, em média, gerar no máximo uma transação por segundo.

### <a name="scaling-rules"></a>Regras de dimensionamento

O número de usuários é determinado pelo tamanho do banco de dados (em unidades de fator de escala). Há um usuário para cada cinco unidades de fator de escala. Devido à definição de atraso, um usuário pode gerar no máximo uma transação por segundo, em média.

Por exemplo, um fator de escala de 500 (SF = 500) bancos de dados terá 100 usuários e pode atingir uma taxa máxima de 100 TPS. Conduzir uma taxa TPS mais alta exige mais usuários e um banco de dados maior.

### <a name="measurement-duration"></a>Duração da medida

Uma execução válida do parâmetro de comparação exige uma duração da medida permanente de pelo menos uma hora.

### <a name="metrics"></a>Métricas

As principais métricas no parâmetro de comparação são a taxa de transferência e o tempo de resposta.

- A taxa de transferência é a medida de desempenho essencial no parâmetro de comparação. A taxa de transferência é informada em transações por unidade de tempo, contando todos os tipos de transação.
- O tempo de resposta é uma medida da previsibilidade do desempenho. A restrição de tempo de resposta varia de acordo com a classe de serviço, com classes superiores de serviço com um requisito de tempo de resposta mais rigoroso, conforme mostrado abaixo.

| Classe de serviço | Medida de taxa de transferência | Requisito de tempo de resposta |
| --- | --- | --- |
| Premium |Transações por segundo |95º percentil em 0,5 segundo |
| Standard |Transações por minuto |90º percentil em 1,0 segundo |
| Basic |Transações por hora |80º percentil em 2,0 segundos |

## <a name="next-steps"></a>Próximas etapas

- Para obter detalhes sobre os tamanhos da computação específicos e as opções de tamanho de armazenamento disponíveis para bancos de dados individuais, confira [Limites de recursos baseados em DTU do Banco de Dados SQL para bancos de dados individuais](resource-limits-dtu-single-databases.md#single-database-storage-sizes-and-compute-sizes).
- Para obter detalhes sobre os tamanhos da computação específicos e opções de tamanho de armazenamento disponíveis para pools elásticos, confira [Limites de recursos baseados em DTU do Banco de Dados SQL](resource-limits-dtu-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes).