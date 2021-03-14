---
title: Log Analytics em Azure Monitor oferece conjuntos de consultas de exemplo que você pode executar por conta própria ou usar como um ponto de partida para suas próprias consultas.
description: Consultas que você pode iniciar e modificar para suas necessidades
ms.topic: article
author: rboucher
ms.author: robb
ms.date: 06/16/2020
ms.openlocfilehash: e4f20032febe1c4afe9cb0964c1b448eaa018103
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102030844"
---
# <a name="example-queries-in-azure-monitor-log-analytics"></a>Exemplos de consultas no Azure Monitor Log Analytics
Log Analytics oferece conjuntos de consultas de exemplo que você pode executar por conta própria ou usar como um ponto de partida para suas próprias consultas. Este artigo descreve as consultas de exemplo e como usá-las.

Se você não estiver familiarizado com Log Analytics ou a linguagem de consulta KQL, as consultas de exemplo serão uma ótima maneira de começar. Eles podem fornecer informações instantâneas sobre um recurso e fornecer uma ótima maneira de começar a aprender e usar o KQL, reduzindo o tempo necessário para começar a usar o Log Analytics. Coletamos e reunimos mais de 250 consultas de exemplo projetadas para fornecer valor instantâneo e que o número de consultas de exemplo está crescendo continuamente.

## <a name="in-context-queries"></a>Consultas no contexto

A nova experiência filtra e sugere consultas no contexto. Em outras palavras, o sistema mostra automaticamente somente as consultas relevantes para o escopo selecionado.

- Para um **único recurso** – as consultas são filtradas de acordo com o tipo de recurso.
- Para um **grupo de recursos** -as consultas são filtradas de acordo com os recursos no grupo de recursos específico.
- Para um **espaço de trabalho** – as consultas são filtradas de acordo com as soluções instaladas no espaço de trabalho.

Esse comportamento é consistente para todos os escopos de Log Analytics. Se você não estiver vendo uma consulta de exemplo para o tipo de recurso desejado, pode ser devido a filtros devido ao contexto. Uma seção posterior descreve como remover o escopo no contexto para que você possa exibir todas as consultas possíveis.

> [!TIP]
> Quanto mais recursos você tiver em seu escopo, mais tempo o portal deve filtrar e mostrar a caixa de diálogo de consulta de exemplo.

## <a name="example-query-user-interface"></a>Exemplo de interface do usuário de consulta

Você pode obter exemplos de consultas de dois locais diferentes.

### <a name="example-query-dialog"></a>Exemplo de diálogo de consulta

Ao entrar pela primeira vez na experiência de Log Analytics, a *caixa de diálogo de consultas de exemplo* é mostrada automaticamente.  Ele também pode ser acessado clicando no canto superior direito da tela em **consultas de exemplo**.

![Barra lateral – consultas de exemplo](media/example-queries/sidebar-2.png)

A caixa de diálogo de consulta de exemplo aparece como mostrado abaixo:  

![Tela de consultas de exemplo](media/example-queries/example-query-start.png)

A captura anterior exibe a tela logs de uma instância Azure Key Vault. Conforme mencionado anteriormente neste artigo, as consultas são mostradas no contexto.  Como resultado, a captura de tela mostra apenas Key Vault exemplos relacionados. Se você selecionar uma assinatura inteira, as consultas para todos os tipos de recursos nessa assinatura serão exibidas.  

Cada consulta de exemplo é representada por um cartão. Você pode examinar rapidamente as consultas para encontrar o que precisa. Você pode executar a consulta diretamente da caixa de diálogo ou optar por carregá-la no editor de consultas para ajustes e ajustes adicionais.

### <a name="sidebar-query-experience"></a>Experiência de consulta da barra lateral

Toda a mesma funcionalidade da experiência de diálogo pode ser acessada no painel consultas na barra lateral esquerda da Log Analytics. Você pode passar o mouse sobre um nome de consulta para obter a descrição da consulta e a funcionalidade adicional.

![Captura de tela que mostra o painel consultas.](media/example-queries/sidebar-3.png)

## <a name="finding-and-filtering-queries"></a>Localizando e filtrando consultas

As opções nesta seção estão disponíveis na experiência de consulta da caixa de diálogo e da barra lateral, mas com uma interface do usuário ligeiramente diferente.  

### <a name="use-favorites"></a>Usar favoritos

Você pode usar as consultas favoritas com frequência para fornecer acesso mais rápido.

> [!TIP]
> A coleta e a exibição de consultas posteriormente é uma boa maneira de começar. Localize as consultas de que você precisa e clique na estrela ao lado da consulta para adicioná-la aos favoritos. Se, posteriormente, você achar que a consulta não é útil, você pode desfazê-la favorita.  

### <a name="filtering-and-group-by"></a>Filtrando e agrupando por

Embora a caixa de diálogo de consulta Conheça os filers para mostrar apenas consultas com o contexto correto, você pode optar por remover o filtro e ver todas as consultas.

### <a name="group-by"></a>Agrupar por

Altere o agrupamento das consultas clicando na lista suspensa *Agrupar por* :

![Exemplo de consulta GroupBy da tela](media/example-queries/example-query-groupby.png)

A caixa de diálogo oferece suporte ao agrupamento por:

- **Tipo de recurso** – um recurso, conforme definido no Azure, como uma máquina virtual. Consulte a [referência de tabela Azure monitor](/azure/azure-monitor/reference/tables/tables-resourcetype) para obter um mapeamento completo de Azure monitor logs/tabelas de log Analytics para o tipo de recurso.  
- **Categoria** – um tipo de informações como *segurança* ou *auditoria*. As categorias são idênticas às categorias definidas no painel do lado de tabelas. Consulte a [referência de tabela Azure monitor](/azure/azure-monitor/reference/tables/tables-category) para obter uma lista completa de categorias.  
- **Solução** – uma solução Azure monitor associada às consultas
- **Tópico** – o tópico da consulta de exemplo, como *logs de atividade* ou *logs de aplicativo*. A propriedade topic é exclusiva para exemplos de consultas e pode diferir de acordo com o tipo de recurso específico.

Os valores de agrupamento também agem como um sumário ativo. Clicar em um dos valores no lado esquerdo da tela rola o modo de exibição consultas diretamente para o item clicado.

### <a name="filter"></a>Filtrar

Você também pode filtrar as consultas de acordo com os valores de **Group by** mencionados anteriormente. Na caixa de diálogo de consulta de exemplo, os filtros são encontrados na parte superior.

![Filtro de tela de consultas de exemplo](media/example-queries/example-query-filter.png)

### <a name="combining-group-by-and-filter"></a>Combinando Group by e Filter

A funcionalidade filtrar e agrupar por é projetada para funcionar em conjunto. Elas fornecem flexibilidade em como as consultas são organizadas. Por exemplo, se você estiver usando um grupo de recursos com vários recursos, talvez queira filtrar para um tipo de recurso específico e organizar as consultas resultantes por tópico.

## <a name="sample-query-dialog-appearance-behavior"></a>Comportamento de aparência da caixa de diálogo de consulta de exemplo

Se você for um KQL pro e preferir ir diretamente para o editor de consultas, poderá alternar a caixa de diálogo de consulta "off". Com a alternância desativada, a caixa de diálogo de consulta de exemplo não é carregada quando Log Analytics tela é carregada.

![Exemplos de ativado](media/example-queries/examples-on-off.png)

Você sempre pode acessar a experiência de exemplo de pop-up de consulta no botão *consultas de exemplo* na barra superior de log Analytics.

## <a name="query-explorer"></a>Gerenciador de consultas

A experiência do Gerenciador de consultas para salvar e compartilhar consultas geradas pelo usuário permanece inalterada por enquanto.

## <a name="next-steps"></a>Próximas etapas

[Introdução às consultas do KQL](./get-started-queries.md)