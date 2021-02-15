---
title: Escolher colunas de distribuição – Citus (hiperescala)-banco de dados do Azure para PostgreSQL
description: Saiba como escolher colunas de distribuição em cenários comuns no banco de dados do Azure para PostgreSQL-Citus (hiperescala).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 129eff8c954c0c5469d3607e6ae16ce3202630ed
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91929329"
---
# <a name="choose-distribution-columns-in-azure-database-for-postgresql--hyperscale-citus"></a>Escolher colunas de distribuição no banco de dados do Azure para PostgreSQL – Citus (hiperescala)

Escolher a coluna de distribuição de cada tabela é uma das decisões de modelagem mais importantes que você tomará. Banco de dados do Azure para PostgreSQL – hiperescala (Citus) armazena linhas em fragmentos com base no valor da coluna de distribuição de linhas.

A opção correta agrupa os dados relacionados nos mesmos nós físicos, o que torna as consultas rápidas e adiciona suporte a todos os recursos do SQL. Uma opção incorreta faz com que o sistema seja executado lentamente e não dará suporte a todos os recursos do SQL entre nós.

Este artigo fornece dicas de coluna de distribuição para os dois cenários mais comuns de hiperescala (Citus).

### <a name="multi-tenant-apps"></a>Aplicativos multilocatário

A arquitetura multilocatário usa uma forma de modelagem de banco de dados hierárquica para distribuir consultas entre nós no grupo de servidores. A parte superior da hierarquia de dados é conhecida como a *ID do locatário* e precisa ser armazenada em uma coluna em cada tabela.

O Citus (hiperscale) inspeciona consultas para ver qual ID de locatário eles envolvem e localiza o fragmento de tabela correspondente. Ele roteia a consulta para um único nó de trabalho que contém o fragmento. A execução de uma consulta com todos os dados relevantes colocados no mesmo nó é chamada de colocalização.

O diagrama a seguir ilustra a colocação no modelo de dados multilocatário. Ele contém duas tabelas, contas e campanhas, cada uma distribuída pelo `account_id` . As caixas sombreadas representam fragmentos. Os fragmentos verdes são armazenados juntos em um nó de trabalho, e os fragmentos azuis são armazenados em outro nó de trabalho. Observe como uma consulta de junção entre contas e campanhas tem todos os dados necessários juntos em um nó quando ambas as tabelas são restritas à mesma ID de conta \_ .

![Colocalização multilocatário](media/concepts-hyperscale-choosing-distribution-column/multi-tenant-colocation.png)

Para aplicar esse design em seu próprio esquema, identifique o que constitui um locatário em seu aplicativo. As instâncias comuns incluem empresa, conta, organização ou cliente. O nome da coluna será algo como `company_id` ou `customer_id` . Examinar cada uma de suas consultas e se perguntar, ele funcionaria se tivesse cláusulas WHERE adicionais para restringir todas as tabelas envolvidas em linhas com a mesma ID de locatário?
As consultas no modelo multilocatário estão no escopo de um locatário. Por exemplo, as consultas de vendas ou de inventário estão no escopo de uma determinada loja.

#### <a name="best-practices"></a>Práticas recomendadas

-   **Particione tabelas distribuídas por uma coluna de ID de locatário comum \_ .** Por exemplo, em um aplicativo SaaS em que os locatários são empresas, a ID do locatário \_ provavelmente será a \_ ID da empresa.
-   **Converta pequenas tabelas de locatário cruzado para tabelas de referência.** Quando vários locatários compartilham uma pequena tabela de informações, distribua-o como uma tabela de referência.
-   **Restringir o filtro de todas as consultas de aplicativo por ID de locatário \_ .** Cada consulta deve solicitar informações para um locatário por vez.

Leia o [tutorial de vários locatários](./tutorial-design-database-hyperscale-multi-tenant.md) para obter um exemplo de como criar esse tipo de aplicativo.

### <a name="real-time-apps"></a>Aplicativos em tempo real

A arquitetura multilocatário introduz uma estrutura hierárquica e usa o local de dados para rotear consultas por locatário. Por outro lado, as arquiteturas em tempo real dependem de propriedades de distribuição específicas de seus dados para obter um processamento altamente paralelo.

Usamos "ID da entidade" como um termo para colunas de distribuição no modelo em tempo real. As entidades típicas são usuários, hosts ou dispositivos.

As consultas em tempo real normalmente solicitam agregações numéricas agrupadas por data ou categoria. O Citus (subscale) envia essas consultas a cada fragmento para resultados parciais e monta a resposta final no nó de coordenador. As consultas são executadas mais rapidamente quando muitos nós contribuem o máximo possível e quando nenhum nó deve fazer uma quantidade desproporcional de trabalho.

#### <a name="best-practices"></a>Práticas recomendadas

-   **Escolha uma coluna com alta cardinalidade como a coluna de distribuição.** Para comparação, um campo de status em uma tabela de pedidos com valores novos, pagos e enviados é uma opção inadequada da coluna de distribuição. Ele assume apenas esses poucos valores, o que limita o número de fragmentos que podem conter os dados e o número de nós que podem processá-lo. Entre colunas com alta cardinalidade, também é bom escolher as colunas que são usadas com frequência em cláusulas Group by ou como chaves de junção.
-   **Escolha uma coluna com distribuição uniforme.** Se você distribuir uma tabela em uma coluna distorcida para determinados valores comuns, os dados na tabela tendem a ser acumulados em determinados fragmentos. Os nós que mantêm esses fragmentos acabam fazendo mais trabalho do que outros nós.
-   **Distribua tabelas de fatos e dimensões em suas colunas comuns.**
    Sua tabela de fatos pode ter apenas uma chave de distribuição. As tabelas que ingressam em outra chave não serão colocalizadas com a tabela de fatos. Escolha uma dimensão a ser colocalizada com base na frequência com que ela é unida e no tamanho das linhas de junção.
-   **Altere algumas tabelas de dimensões para tabelas de referência.** Se uma tabela de dimensões não puder ser colocalizada com a tabela de fatos, você poderá melhorar o desempenho da consulta distribuindo cópias da tabela de dimensões para todos os nós na forma de uma tabela de referência.

Leia o [tutorial do painel em tempo real](./tutorial-design-database-hyperscale-realtime.md) para obter um exemplo de como criar esse tipo de aplicativo.

### <a name="time-series-data"></a>Dados de série temporal

Em uma carga de trabalho de série temporal, os aplicativos consultam informações recentes enquanto arquivam informações antigas.

O erro mais comum em modelar informações de série temporal em Citus (hiperescala) é usar o carimbo de data/hora em si como uma coluna de distribuição. Uma distribuição de hash com base no tempo distribui os tempos de forma aleatória em fragmentos diferentes, em vez de manter intervalos de tempo juntos em fragmentos. As consultas que envolvem o tempo geralmente fazem referência a intervalos de tempo, por exemplo, os dados mais recentes. Esse tipo de distribuição de hash leva à sobrecarga da rede.

#### <a name="best-practices"></a>Práticas recomendadas

-   **Não escolha um carimbo de data/hora como a coluna de distribuição.** Escolha uma coluna de distribuição diferente. Em um aplicativo multilocatário, use a ID do locatário ou em um aplicativo em tempo real, use a ID da entidade.
-   **Em vez disso, use o particionamento de tabela do PostgreSQL para o tempo.** Use o particionamento de tabela para dividir uma grande tabela de dados ordenados por tempo em várias tabelas herdadas com cada tabela que contém intervalos de tempo diferentes. A distribuição de uma tabela postgres em hiperescala (Citus) cria fragmentos para as tabelas herdadas.

## <a name="next-steps"></a>Próximas etapas
- Saiba como a [colocalização](concepts-hyperscale-colocation.md) entre os dados distribuídos ajuda as consultas a serem executadas rapidamente.
