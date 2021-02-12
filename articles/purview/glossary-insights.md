---
title: Relatório de Glossário sobre seus dados usando o alcance insights
description: Este guia de instruções descreve como exibir e usar os relatórios de Glossário do alcance insights sobre seus dados.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/20/2020
ms.openlocfilehash: f61d99a61cb50886d70489b586d948bfa751e196
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96576767"
---
# <a name="glossary-insights-on-your-data-in-azure-purview"></a>Informações de Glossário sobre seus dados no Azure alcance

Este guia de instruções descreve como acessar, exibir e filtrar relatórios de insights de Glossário alcance para seus dados.

Neste guia de instruções, você aprenderá a:

> [!div class="checklist"]
> - Ir para informações de sua conta do alcance
> - Obtenha uma visão geral dos seus dados

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar a usar o alcance insights, verifique se você concluiu as seguintes etapas:

- Configurar os recursos do Azure e preencher a conta com dados

- Configurar e concluir uma verificação no tipo de origem

- Configurar um glossário e anexar ativos aos termos do glossário

Para obter mais informações, consulte [gerenciar fontes de dados no Azure alcance (versão prévia)](manage-data-sources.md).

## <a name="use-purview-glossary-insights"></a>Usar o alcance Glossário insights

No Azure alcance, você pode criar termos do glossário e anexá-los aos ativos. Posteriormente, você pode exibir a distribuição do glossário no Glossário insights. Isso informa o estado do seu Glossário por termos anexados aos ativos. Ele também informa os termos por status e distribuição de funções por número de usuários.

**Para exibir as informações do glossário:**

1. Vá para a tela instância **do Azure alcance** [na portal do Azure](https://aka.ms/purviewportal) e selecione sua conta do alcance.

1. Na página **visão geral** , **na seção Introdução** , selecione o bloco iniciar conta do **alcance** .

   :::image type="content" source="./media/glossary-insights/portal-access.png" alt-text="Inicie o alcance no portal do Azure":::

1. Na página **inicial** do alcance, selecione o bloco **Exibir informações** para acessar sua área do **insights** :::image type="icon" source="media/glossary-insights/ico-insights.png" border="false"::: .

   :::image type="content" source="./media/glossary-insights/view-insights.png" alt-text="Exibir suas ideias no portal do Azure":::

1. Na área **insights** :::image type="icon" source="media/glossary-insights/ico-insights.png" border="false"::: , selecione **Glossário** para exibir o relatório de **informações do glossário** do alcance.

A página de **informações do glossário** exibe as seguintes áreas:
1. **KPIs de alto nível** para mostrar os termos do glossário e os usuários do catálogo

2. Os **principais termos do glossário e a contagem de ativos** mostra os cinco primeiros termos do glossário com ativos anexados a eles. Todos os outros ativos são contabilizados na categoria "outros" no grafo.

3. Os **termos do glossário por status do termo** mostram a distribuição de termos do glossário por status, como "rascunho", "aprovado", "alerta" e "expirado". 

1. Focalize ou clique na fatia do grafo com um status e anote a contagem de termos com esse status.

1. **A distribuição de funções por número de usuários** mostra a distribuição de funções por número de usuários por função no alcance.

   :::image type="content" source="./media/glossary-insights/glossary-insights1.png" alt-text="Exibir informações do glossário":::

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre os relatórios do Azure alcance Insight por meio de [insights de ativos](./asset-insights.md)