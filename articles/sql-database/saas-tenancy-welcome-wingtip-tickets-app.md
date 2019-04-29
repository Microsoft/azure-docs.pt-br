---
title: Bem-vindo ao aplicativo Wingtips - Banco de Dados SQL do Azure | Microsoft Docs
description: Saiba mais sobre modelos de aluguel de banco de dados e sobre o aplicativo de exemplo Wingtips SaaS para o Banco de Dados SQL do Azure no ambiente de nuvem.
keywords: tutorial do banco de dados SQL
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: billgib
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 963d7d44ef3ef77604fc5a9faac479a9e4c91ee6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61487352"
---
# <a name="the-wingtip-tickets-saas-application"></a>O aplicativo Wingtip Tickets SaaS

O mesmo aplicativo SaaS *Wingtip Tickets* é implementado em cada uma das três amostras. O aplicativo é um aplicativo SaaS de emissão de tíquetes e listagem de eventos simples voltado para pequenos locais – teatros, clubes etc. Cada local é um locatário do aplicativo e tem seus próprios dados: detalhes do local, listas de eventos, clientes, pedidos de tíquete etc.  O aplicativo, junto com os scripts de gerenciamento e os tutoriais, apresenta um cenário de SaaS de ponta a ponta. Isso inclui provisionamento de locatários, monitoramento e gerenciamento de desempenho, gerenciamento de esquema e relatórios e análises entre locatários.

## <a name="three-saas-application-and-tenancy-patterns"></a>Três padrões de locatários e aplicativos SaaS

Há três versões do aplicativo disponíveis. Cada uma explora um padrão diferente de locação de banco de dados no banco de dados SQL do Azure.  O primeiro usa um aplicativo autônomo por locatário com seu próprio banco de dados. O segundo usa um aplicativo multilocatário, com um banco de dados por locatário. O terceiro exemplo usa um aplicativo multilocatário com bancos de dados multilocatários compartilhados.

![Três padrões de locatário][image-three-tenancy-patterns]

 Cada exemplo inclui o código do aplicativo, além de scripts de gerenciamento e tutoriais que exploram um intervalo de padrões de design e de gerenciamento.  Cada exemplo é implantado em menos de cinco minutos.  Todos os três podem ser implantados lado a lado, portanto, você pode comparar as diferenças no design e no gerenciamento.

## <a name="standalone-application-per-tenant-pattern"></a>Padrão de aplicativo por locatário autônomo

O padrão de aplicativo por locatário autônomo usa um aplicativo de locatário único com um banco de dados para cada locatário. Cada aplicativo de locatário, incluindo seu banco de dados, é implantado em um grupo de recursos do Azure separado. O grupo de recursos pode ser implantado na assinatura do provedor de serviços ou na assinatura do locatário e gerenciado pelo provedor em nome do locatário. O padrão de aplicativo por locatário autônomo fornece o maior isolamento de locatários, mas é geralmente mais caro, pois não há nenhuma oportunidade de compartilhar recursos entre vários locatários.  Esse padrão é adequado para aplicativos que podem ser mais complexa e são implantados para um número menor de locatários.  Em implantações autônomas, o aplicativo pode ser personalizado para cada locatário mais facilmente do que em outros padrões.  

Confira os [tutoriais] [ docs-tutorials-for-wingtip-sa] e o código no GitHub [.../Microsoft/WingtipTicketsSaaS-StandaloneApp][github-code-for-wingtip-sa].

## <a name="database-per-tenant-pattern"></a>Padrão de banco de dados por locatário

O padrão de banco de dados por locatário é eficaz para provedores de serviço que se preocupam com isolamento de locatários e desejam executar um serviço centralizado que permite o uso econômico dos recursos compartilhados. Um banco de dados é criado para cada local, ou locatário, e todos os bancos de dados são gerenciados centralmente. Os bancos de dados podem ser hospedados em pools elásticos para fornecer um gerenciamento de desempenho fácil e econômico, que aproveita os padrões de carga de trabalho imprevisíveis dos locatários. O banco de dados de catálogo contém o mapeamento entre locatários e seus bancos de dados. Esse mapeamento é gerenciado usando os recursos de gerenciamento de mapa de fragmento da [Biblioteca de Cliente de Banco de Dados Elástico](sql-database-elastic-database-client-library.md), que fornece um gerenciamento de conexão eficiente para o aplicativo.

Confira os [tutoriais] [ docs-tutorials-for-wingtip-dpt] e o código no GitHub [.../Microsoft/WingtipTicketsSaaS-DbPerTenant][github-code-for-wingtip-dpt].

## <a name="sharded-multi-tenant-database-pattern"></a>Padrão de bancos de dados multilocatários compartilhados

Bancos de dados multilocatários são eficientes para provedores de serviço que buscam um menor custo por locatário e que de acordo com o isolamento de locatários reduzido. Esse padrão permite empacotar grandes números de locatários em um banco de dados individual, reduzindo o custo por locatário. A escala quase infinita é possível pela fragmentação de locatários entre diversos bancos de dados. Um banco de dados do catálogo mapeia os locatários para os bancos de dados.  

Esse padrão também permite um modelo *híbrido* no qual você pode otimizar para economizar com vários locatários em um banco de dados ou otimizar para o isolamento com um único locatário em seu próprio banco de dados. A escolha pode ser feita com base em locatário por locatário, seja quando o locatário for provisionado ou mais tarde, sem nenhum impacto no aplicativo.  Esse modelo pode ser usado com eficiência quando grupos de locatários precisam ser tratados de modo diferente. Por exemplo, locatários de baixo custo podem ser atribuídos a bancos de dados compartilhados, enquanto locatários premium podem ser atribuídos a seus próprios bancos de dados. 

Confira os [tutoriais][docs-tutorials-for-wingtip-mt] e o código no GitHub [.../Microsoft/WingtipTicketsSaaS-MultiTenantDb][github-code-for-wingtip-mt].

## <a name="next-steps"></a>Próximas etapas

#### <a name="conceptual-descriptions"></a>Descrições conceituais

- Uma explicação mais detalhada dos padrões de locatário do aplicativo está disponível em [Padrões de locatário de banco de dados SaaS multilocatário][saas-tenancy-app-design-patterns-md]

#### <a name="tutorials-and-code"></a>Tutoriais e código

- Aplicativo autônomo por locatário:
    - [Tutoriais para o aplicativo autônomo][docs-tutorials-for-wingtip-sa].
    - [Código para aplicativo autônomo no GitHub][github-code-for-wingtip-sa].

- Banco de dados por locatário:
    - [Tutoriais do banco de dados por locatário][docs-tutorials-for-wingtip-dpt].
    - [Código para banco de dados por locatário no GitHub][github-code-for-wingtip-dpt].

- Multilocatário fragmentado:
    - [Tutoriais para vários locatários fragmentados][docs-tutorials-for-wingtip-mt].
    - [Código para vários locatários fragmentados no GitHub][github-code-for-wingtip-mt].



<!-- Image references. -->

[image-three-tenancy-patterns]: media/saas-tenancy-welcome-wingtip-tickets-app/three-tenancy-patterns.png "Três padrões de locatário."

<!-- Docs.ms.com references. -->

[saas-tenancy-app-design-patterns-md]: saas-tenancy-app-design-patterns.md

<!-- WWWeb http references. -->

[docs-tutorials-for-wingtip-sa]: https://aka.ms/wingtipticketssaas-sa
[github-code-for-wingtip-sa]: https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp

[docs-tutorials-for-wingtip-dpt]: https://aka.ms/wingtipticketssaas-dpt
[github-code-for-wingtip-dpt]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant

[docs-tutorials-for-wingtip-mt]: https://aka.ms/wingtipticketssaas-mt
[github-code-for-wingtip-mt]: https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb

