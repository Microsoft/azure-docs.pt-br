---
title: Conceitos para distribuição de dados e expansão com o grupo de servidores de hiperescala PostgreSQL habilitado para Arc
titleSuffix: Azure Arc enabled data services
description: Conceitos para distribuição de dados com o grupo de servidores de hiperescala PostgreSQL habilitado para Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: c01da4aed9e27296ea7b570420bb190b16749848
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "90933631"
---
# <a name="concepts-for-distributing-data-with-arc-enabled-postgresql-hyperscale-server-group"></a>Conceitos para distribuição de dados com o grupo de servidores de hiperescala PostgreSQL habilitado para Arc

Este artigo explica os principais conceitos que são importantes para se beneficiar ao máximo de hiperescalas PostgreSQL habilitadas para o Arc do Azure.
Os artigos vinculados abaixo apontam para os conceitos explicados para o banco de dados do Azure para PostgreSQL Citus (hiperescala). É a mesma tecnologia que o Azure Arc habilitou o hiperescala do PostgreSQL para que os mesmos conceitos e perspectivas se apliquem.

**Qual é a diferença entre eles?**
- _Banco de Dados do Azure para PostgreSQL – Hiperescala (Citus)_

Esse é o fator forma de hiperescala do mecanismo de banco de dados Postgres disponível como um banco de dados como um serviço no Azure (PaaS). Ele é alimentado pela extensão Citus que habilita a experiência de hiperescala. Neste formato, o serviço é executado nos data centers da Microsoft e é operado pela Microsoft.

- _Hiperescala do PostgreSQL habilitado para Arc do Azure_

Esse é o fator forma de hiperescala do mecanismo de banco de dados Postgres oferecido disponível com o serviço de dados habilitado para Arc do Azure. Nesse fator forma, nossos clientes fornecem a infraestrutura que hospeda os sistemas e os opera.

Os principais conceitos sobre o PostgreSQL do Azure habilitado para o semicírculo são resumidos abaixo:

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="nodes-and-tables"></a>Nós e tabelas
É importante saber mais sobre os conceitos a seguir para se beneficiar mais do Azure Arc habilitado para o postgres em hiperescala:
- Nós postgres especializados em hiperescala do PostgreSQL habilitado para Arc do Azure: coordenador e trabalhadores
- Tipos de tabelas: tabelas distribuídas, tabelas de referência e tabelas locais
- Fragmentos

Veja mais informações em [nós e tabelas no banco de dados do Azure para PostgreSQL – Citus (hiperescala)](../../postgresql/concepts-hyperscale-nodes.md). 

## <a name="determine-the-application-type"></a>Determinar o tipo de aplicativo
Identificar claramente o tipo de aplicativo que você está compilando é importante. Por quê? Como a execução de consultas eficientes em um grupo de servidores de hiperescala PostgreSQL habilitado para o Azure Arc requer que as tabelas sejam distribuídas corretamente entre servidores. A distribuição recomendada varia de acordo com o tipo de aplicativo e seus padrões de consulta. Há, em geral, dois tipos de aplicativos que funcionam bem na hiperescala postgres habilitada para o Azure Arc:
- Aplicativos multilocatários
- Aplicativos em tempo real

A primeira etapa na modelagem de dados é identificar quais deles se assemelham mais bem ao seu aplicativo.

Consulte os detalhes em [determinando o tipo de aplicativo](../../postgresql/concepts-hyperscale-app-type.md).


## <a name="choose-a-distribution-column"></a>Escolher uma coluna de distribuição
Por que escolher uma coluna distribuída?

Essa é uma das decisões de modelagem mais importantes que você fará. A hiperescala do PostgreSQL habilitada para Arc do Azure armazena linhas em fragmentos com base no valor da coluna de distribuição de linhas. A opção correta agrupa os dados relacionados nos mesmos nós físicos, o que torna as consultas rápidas e adiciona suporte a todos os recursos do SQL. Uma opção incorreta faz com que o sistema seja executado lentamente e não dará suporte a todos os recursos do SQL entre nós. Este artigo fornece dicas de coluna de distribuição para os dois cenários mais comuns de hiperescala.

Veja detalhes em [escolher colunas de distribuição](../../postgresql/concepts-hyperscale-choose-distribution-column.md).


## <a name="table-colocation"></a>Colocação de tabela

A colocação é sobre o armazenamento de informações relacionadas em conjunto nos mesmos nós. As consultas podem ficar rápidas quando todos os dados necessários estiverem disponíveis sem qualquer tráfego de rede. A colocação de dados relacionados em nós diferentes permite que as consultas sejam executadas com eficiência em paralelo em cada nó.

Consulte os detalhes em colocação da [tabela](../../postgresql/concepts-hyperscale-colocation.md).


## <a name="next-steps"></a>Próximas etapas
- [Leia sobre como criar a hiperescala do PostgreSQL habilitado para Arc do Azure](create-postgresql-hyperscale-server-group.md)
- [Leia sobre expansão de grupos de servidores de hiperescala PostgreSQL habilitados para Arc do Azure criados em seu controlador de dados de arco](scale-out-postgresql-hyperscale-server-group.md)
- [Leia sobre os serviços de dados habilitados para Arc do Azure](https://azure.microsoft.com/services/azure-arc/hybrid-data-services)
- [Leia sobre o arco do Azure](https://aka.ms/azurearc)

