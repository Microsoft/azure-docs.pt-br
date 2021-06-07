---
title: Visão geral da Hiperescala (Citus) do Banco de Dados do Azure para PostgreSQL
description: Fornece uma visão geral da opção de implantação Hiperescala (Citus)
author: jonels-msft
ms.author: jonels
ms.custom: mvc
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: overview
ms.date: 09/01/2020
ms.openlocfilehash: 1734128384d63749d3c777cf6315278fced9d140
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95025133"
---
# <a name="what-is-azure-database-for-postgresql---hyperscale-citus"></a>O que é a Hiperescala (Citus) do Banco de Dados do Azure para PostgreSQL?

O Banco de Dados do Azure para PostgreSQL é um serviço de banco de dados relacional na nuvem da Microsoft projetado para desenvolvedores. Ele é baseado na versão da comunidade do mecanismo de banco de dados [PostgreSQL](https://www.postgresql.org/) de software livre.

A Hiperescala (Citus) é uma opção de implantação que escala horizontalmente as consultas em vários computadores por meio da fragmentação. Seu mecanismo de consulta faz a correspondência entre consultas SQL recebidas nesses servidores para obter respostas mais rápidas em grandes conjuntos de dados. Ele atende aplicativos que exigem maior escala e desempenho do que outras opções de implantação: geralmente, cargas de trabalho que estão se aproximando dos 100 GB de dados ou que já excederam esse limite.

A Hiperescala (Citus) fornece:

- Dimensionamento horizontal entre vários computadores usando a fragmentação
- Consultar a paralelização nesses servidores para obter respostas mais rápidas em grandes conjuntos de dados
- Excelente suporte para aplicativos multilocatário, análise operacional em tempo real e cargas de trabalho transacionais de alta taxa de transferência

Os aplicativos criados para o PostgreSQL podem executar consultas distribuídas em Hiperescala (Citus) com [bibliotecas de conexão](./concepts-connection-libraries.md) padrão e alterações mínimas.

## <a name="next-steps"></a>Próximas etapas

- Comece pela [criação do seu primeiro](./quickstart-create-hyperscale-portal.md) grupo de servidores da Hiperescala (Citus) do Banco de Dados do Azure para PostgreSQL.
- Consultar a [página de preços](https://azure.microsoft.com/pricing/details/postgresql/) para ver comparações de custo e calculadoras. A Hiperescala (Citus) também oferece descontos de Instância Reservada pré-paga; confira as páginas de [preços da Hiperescala (Citus) em RI](concepts-hyperscale-reserved-pricing.md) para obter detalhes.
- Determinar o melhor [tamanho inicial](howto-hyperscale-scale-initial.md) para seu grupo de servidores
