---
title: "Visão geral do parâmetro de comparação do Banco de Dados SQL do Azure"
description: "Este tópico descreve o Parâmetro de Comparação do Banco de Dados SQL do Azure usado para medir o desempenho do Banco de Dados SQL do Azure."
services: sql-database
documentationcenter: na
author: jan-eng
manager: jhubbard
editor: monicar
ms.assetid: e26f8a66-2c12-49d7-8297-45b4d48a5c01
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 06/21/2016
ms.author: janeng
ms.openlocfilehash: 57178352e0a402f3559c94ce25f125dcbefd1374
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-sql-database-benchmark-overview"></a>Visão geral do parâmetro de comparação do Banco de Dados SQL do Azure
## <a name="overview"></a>Visão geral
O Banco de Dados SQL do Microsoft Azure oferece três [camadas de serviço](sql-database-service-tiers.md) com vários níveis de desempenho. Cada nível de desempenho fornece um conjunto de recursos cada vez maior, ou ‘potência’, projetada para fornecer uma taxa de transferência crescente.

É importante ser capaz de quantificar como o aumento de capacidade de cada nível de desempenho resulta em um desempenho de banco de dados melhor. Para fazer isso, a Microsoft desenvolveu o Benchmark do Banco de Dados SQL do Azure (ASDB). O parâmetro de comparação exercita uma combinação de operações básicas encontradas em todas as cargas de trabalho OLTP. Nós medimos a taxa de transferência obtida para bancos de dados em execução em cada nível de desempenho.

Os recursos e a potência de cada camada de serviço e nível de desempenho são expressos em termos de [Unidades de Transação de Banco de Dados (DTUs)](sql-database-what-is-a-dtu.md). As DTUs oferecem uma maneira de descrever a capacidade de um nível de desempenho com base em uma medida combinada de CPU, memória e taxas de leitura e gravação oferecidas por cada nível de desempenho. Dobrar a taxa de DTU é o equivalente a dobrar a potência do banco de dados. O parâmetro de comparação nos permite avaliar o impacto no desempenho do banco de dados causado pelo aumento de capacidade oferecido por cada nível de desempenho ao executar operações de banco de dados reais, durante o dimensionamento do banco de dados, do número de usuários e das taxas de transação em proporção aos recursos fornecidos para o banco de dados.

Ao expressar a taxa de transferência da camada de serviço Basic usando transações por hora, a camada de serviço Standard usando transações por minuto e a camada de serviço Premium usando transações por segundo, fica mais fácil relacionar rapidamente o potencial do desempenho de cada camada de serviço aos requisitos de um aplicativo.

## <a name="correlating-benchmark-results-to-real-world-database-performance"></a>Correlacionando resultados do parâmetro de comparação ao desempenho de um banco de dados real
É importante entender que o ASDB, como todos os parâmetros de comparação, é representativo e indicativo apenas. As taxas de transação obtidas com o aplicativo de parâmetro de comparação não serão iguais às que podem ser obtidas com outros aplicativos. O parâmetro de comparação compreende um conjunto de tipos de transação diferentes executados em um esquema com uma variedade de tabelas e de tipos de dados. Embora o parâmetro de comparação exercite as mesmas operações básicas comuns a todas as cargas de trabalho OLTP, ele não representa qualquer classe específica de banco de dados ou de aplicativo. O objetivo do parâmetro de comparação é fornecer um guia razoável para o desempenho relativo de um banco de dados que pode ser esperado ao dimensionar horizontal ou verticalmente entre os níveis de desempenho. Na realidade, os bancos de dados de diferentes tamanhos e complexidade, lidam com combinações diferentes de cargas de trabalho e responderão de formas diferentes. Por exemplo, um aplicativo de E/S intensiva pode atingir os limites de E/S mais cedo ou um aplicativo de uso intensivo de CPU pode atingir os limites de CPU mais cedo. Não há nenhuma garantia de que qualquer banco de dados específico será dimensionado da mesma forma que o parâmetro de comparação em um aumento da carga.

O parâmetro de comparação e sua metodologia são descritos em mais detalhes abaixo.

## <a name="benchmark-summary"></a>Resumo do parâmetro de comparação
O ASDB mede o desempenho de uma combinação de operações básicas de banco de dados que ocorrem com mais frequência em cargas de trabalho de processamento de transações online (OLTP). Embora o parâmetro de comparação seja projetado com a computação em nuvem em mente, o esquema do banco de dados, o preenchimento dos dados e as transações foram projetados para ser amplamente representativos dos elementos básicos mais comumente usados em cargas de trabalho OLTP.

## <a name="schema"></a>Esquema
O esquema foi projetado para ter variedade e complexidade suficiente para dar suporte a uma ampla gama de operações. O parâmetro de comparação é executado em um banco de dados composto por seis tabelas. As tabelas recaem em três categorias: tamanho fixo, em escala e crescente. Há duas tabelas de tamanho fixo; três tabelas em escala e uma tabela crescente. As tabelas de tamanho fixo têm um número constante de linhas. As tabelas em escala têm uma cardinalidade proporcional ao desempenho do banco de dados, mas não são alteradas durante o parâmetro de comparação. A tabela crescente é dimensionada como uma tabela em escala na carga inicial, mas depois a cardinalidade é alterada no curso da execução do parâmetro de comparação à medida que as linhas são inseridas e excluídas.

O esquema inclui uma combinação de tipos de dados, incluindo inteiros, numéricos, caracteres e data/hora. O esquema inclui chaves primárias e secundárias, mas nenhuma chave estrangeira – ou seja, não há restrições de integridade referencial entre as tabelas.

Um programa de geração de dados gera os dados para o banco de dados inicial. Os dados numéricos e inteiros são gerados com várias estratégias. Em alguns casos, os valores são distribuídos aleatoriamente em um intervalo. Em outros casos, um conjunto de valores é aleatoriamente permutado para garantir que uma distribuição específica seja mantida. Os campos de texto são gerados de uma lista ponderada de palavras para gerar dados de aparência realista.

O banco de dados é dimensionado com base em um "fator de escala". O fator de escala (abreviado como SF) determina a cardinalidade das tabelas em escala e crescentes. Conforme descrito abaixo na seção Usuários e Definição, o tamanho do banco de dados, o número de usuários e o desempenho máximo são dimensionados proporcionalmente entre si.

## <a name="transactions"></a>Transações
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

## <a name="workload-mix"></a>Combinação de carga de trabalho
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

## <a name="users-and-pacing"></a>Usuários e definição
O parâmetro de comparação de desempenho é direcionado de uma ferramenta que envia transações por meio de um conjunto de conexões para simular o comportamento de alguns usuários simultâneos. Embora todas as conexões e transações sejam geradas por máquina, para manter a simplicidade nos referimos a essas conexões como "usuários". Embora cada usuário opere independentemente dos demais, todos os usuários realizam o mesmo ciclo de etapas mostrado abaixo:

1. Estabeleça uma conexão de banco de dados.
2. Repita até receber um sinal para sair:
   * Selecione uma transação aleatoriamente (de uma distribuição ponderada).
   * Execute a transação selecionada e meça o tempo de resposta.
   * Aguarde uma definição de atraso.
3. Feche a conexão de banco de dados.
4. Sair.

A definição de atraso (na etapa 2c) é selecionada aleatoriamente, mas com uma distribuição cuja média é de 1,0 segundo. Portanto, cada usuário pode, em média, gerar no máximo uma transação por segundo.

## <a name="scaling-rules"></a>Regras de dimensionamento
O número de usuários é determinado pelo tamanho do banco de dados (em unidades de fator de escala). Há um usuário para cada cinco unidades de fator de escala. Devido à definição de atraso, um usuário pode gerar no máximo uma transação por segundo, em média.

Por exemplo, um fator de escala de 500 (SF = 500) bancos de dados terá 100 usuários e pode atingir uma taxa máxima de 100 TPS. Conduzir uma taxa TPS mais alta exige mais usuários e um banco de dados maior.

A tabela a seguir mostra o número de usuários realmente mantidos para cada nível de desempenho e da camada de serviço.

| Camada de serviço (nível de desempenho) | Usuários | Tamanho do banco de dados |
| --- | --- | --- |
| Basic |5 |720 MB |
| Standard (S0) |10 |1 GB |
| Standard (S1) |20 |2,1 GB |
| Standard (S2) |50 |7,1 GB |
| Premium (P1) |100 |14 GB |
| Premium (P2) |200 |28 GB |
| Premium (P6/P3) |800 |114 GB |

## <a name="measurement-duration"></a>Duração da medida
Uma execução válida do parâmetro de comparação exige uma duração da medida permanente de pelo menos uma hora.

## <a name="metrics"></a>Métricas
As principais métricas no parâmetro de comparação são a taxa de transferência e o tempo de resposta.

* A taxa de transferência é a medida de desempenho essencial no parâmetro de comparação. A taxa de transferência é informada em transações por unidade de tempo, contando todos os tipos de transação.
* O tempo de resposta é uma medida da previsibilidade do desempenho. A restrição de tempo de resposta varia de acordo com a classe de serviço, com classes superiores de serviço com um requisito de tempo de resposta mais rigoroso, conforme mostrado abaixo.

| Classe de serviço | Medida de taxa de transferência | Requisito de tempo de resposta |
| --- | --- | --- |
| Premium |Transações por segundo |95º percentil em 0,5 segundo |
| Standard |Transações por minuto |90º percentil em 1,0 segundo |
| Basic |Transações por hora |80º percentil em 2,0 segundos |

## <a name="conclusion"></a>Conclusão
O Parâmetro de Comparação do Banco de Dados SQL do Azure mede o desempenho relativo do Banco de Dados SQL do Azure em execução no intervalo de camadas de serviço disponíveis e nos níveis de desempenho. O parâmetro de comparação exercita uma combinação de operações básicas de banco de dados que ocorrem com mais frequência em cargas de trabalho de processamento de transações online (OLTP). Ao medir o desempenho real, o parâmetro de comparação fornece uma avaliação mais significativa do impacto na taxa de transferência da alteração do nível de desempenho que é possível apenas listando os recursos fornecidos por cada nível, como a velocidade da CPU, o tamanho da memória e o IOPS. No futuro, continuaremos a desenvolver esse parâmetro de comparação para ampliar seu escopo e expandir os dados fornecidos.

## <a name="resources"></a>Recursos
[Introdução ao Banco de Dados SQL](sql-database-technical-overview.md)

[Camadas de serviço e níveis de desempenho](sql-database-service-tiers.md)

[Diretrizes de desempenho para bancos de dados únicos](sql-database-performance-guidance.md)
