---
title: Determinar o tipo de aplicativo – Citus (hiperescala)-banco de dados do Azure para PostgreSQL
description: Identifique seu aplicativo para uma modelagem de dados distribuída efetiva
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 07/17/2020
ms.openlocfilehash: 92333857177d33307d6997bfcbdf79787d3ab127
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "90895948"
---
# <a name="determining-application-type"></a>Determinando o tipo de aplicativo

A execução de consultas eficientes em um grupo de servidores Citus (hiperescala) exige que as tabelas sejam distribuídas corretamente entre os servidores. A distribuição recomendada varia de acordo com o tipo de aplicativo e seus padrões de consulta.

Há, em geral, dois tipos de aplicativos que funcionam bem em Citus (hiperescala). A primeira etapa na modelagem de dados é identificar quais deles se assemelham mais bem ao seu aplicativo.

## <a name="at-a-glance"></a>Visão geral

| Aplicativos multilocatários                                 | Aplicativos em tempo real                                |
|-----------------------------------------------------------|-------------------------------------------------------|
| Às vezes, dezenas ou centenas de tabelas em um esquema          | Um número pequeno de tabelas                                |
| Consultas relacionadas a um locatário (empresa/repositório) por vez | Consultas de análise relativamente simples com agregações |
| Cargas de trabalho OLTP para atender clientes Web                    | Grande volume de ingestão de dados, principalmente os imutáveis           |
| Cargas de trabalho OLAP que atendem consultas analíticas por locatário   | Muitas vezes centralizam uma grande tabela de eventos            |

## <a name="examples-and-characteristics"></a>Exemplos e características

**Aplicativo multilocatário**

> Normalmente, são aplicativos SaaS que atendem a outras empresas, contas ou organizações. A maioria dos aplicativos SaaS são inerentemente relacionais. Eles têm uma dimensão natural para distribuir dados entre nós: apenas fragmento por ID de locatário \_ .
>
> O Citus (hiperescala) permite que você Escale horizontalmente seu banco de dados para milhões de locatários sem precisar rearquitetar seu aplicativo. Você pode manter a semântica relacional de que precisa, como junções, restrições de chave estrangeira, transações, ACID e consistência.
>
> -   **Exemplos**: sites que hospedam lojas-Fronts para outras empresas, como uma solução de marketing digital ou uma ferramenta de automação de vendas.
> -   **Características**: consultas relacionadas a um único locatário em vez de unir informações entre locatários. Isso inclui cargas de trabalho OLTP para atender a clientes Web e cargas de trabalho OLAP que atendem a consultas analíticas por locatário. Ter dezenas ou centenas de tabelas em seu esquema de banco de dados também é um indicador para o modelo de dado multilocatário.
>
> Dimensionar um aplicativo multilocatário com o Citus (hiperescala) também requer alterações mínimas no código do aplicativo. Temos suporte para estruturas populares como Ruby on Rails e Django.

**Análise em tempo real**

> Aplicativos que precisam de um grande paralelismo, coordenando centenas de núcleos para resultados rápidos em consultas numéricas, estatísticas ou de contagem.  Fragmentando e paralelizando consultas SQL em vários nós, o Citus (hiperescala) possibilita a execução de consultas em tempo real em bilhões de registros em um segundo.
>
> As tabelas em modelos de dados de análise em tempo real normalmente são distribuídas por colunas como \_ ID de usuário, ID do host \_ ou ID do dispositivo \_ .
>
> -   **Exemplos**: painéis de análise voltados para o cliente que exigem tempos de resposta de subsegundos.
> -   **Características**: poucas tabelas, muitas vezes centralizando uma grande tabela de eventos de dispositivo, site ou usuário e exigindo alto volume de ingestão de dados praticamente imutáveis. Consultas de análise relativamente simples (mas computacionalmente intensivas) que envolvem várias agregações e selecionando de grupo.

Se sua situação se assemelhar a ambos os casos acima, a próxima etapa será decidir como fragmentar seus dados no grupo de servidores. A \' escolha das colunas de distribuição do administrador de banco de dados precisa corresponder aos padrões de acesso de consultas típicas para garantir o desempenho.

## <a name="next-steps"></a>Próximas etapas

* [Escolha uma coluna de distribuição](concepts-hyperscale-choose-distribution-column.md) para tabelas em seu aplicativo para distribuir dados com eficiência
