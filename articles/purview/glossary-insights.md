---
title: Relatório de Glossário sobre seus dados usando o alcance insights
description: Este guia de instruções descreve como exibir e usar os relatórios de Glossário do alcance insights sobre seus dados.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/20/2020
ms.openlocfilehash: eb1d59ae41b04be60dec90aaee4b2305b6d39ca6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102095843"
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

O **Glossário insights** fornece uma informação valiosa para o usuário comercial, a fim de manter um glossário bem definido para sua organização.

1. O relatório começa com **KPIs de alto nível** que mostra **o _total de termos_*na sua conta do alcance,* com termos _aprovados sem_ os termos de ativos *e _*_expirados com ativos_**. Cada um desses valores ajudará a identificar a integridade do seu Glossário.

   :::image type="content" source="./media/glossary-insights/glossary-kpi.png" alt-text="Exibir KPI de informações do glossário"::: 


2. O **instantâneo da seção de termos** (exibido acima) mostra o status do termo como **_rascunho_*_, _*_aprovado_*_, _*_alerta_*_ e _*_expirado_** para termos com ativos e termos sem ativos.

3. Clique em **Exibir mais** para ver os nomes dos termos com vários status e mais detalhes sobre os especialistas em **_Administração_*_ e _***. 

   :::image type="content" source="./media/glossary-insights/glossary-view-more.png" alt-text="Instantâneo de termos com e sem ativos":::  

4. Quando você clica em "exibir mais" para ***termos aprovados com ativos**, as informações permitem que você navegue até a página de detalhes do termo _ *Glossário**, de onde você pode navegar ainda mais para a lista de ativos com os termos anexados. 

   :::image type="content" source="./media/glossary-insights/navigate-to-glossary-detail.png" alt-text="Informações sobre o Glossário"::: 

4. Na página insights do glossário, exiba uma distribuição de **termos incompletos** por tipo de informações ausentes. O grafo mostra a contagem de termos com a **_definição ausente_*_, _* o _expert_*_, _* is ausentes de _Administração_ _ *e _* estão faltando _vários_** campos.

1. Clique em ***Exibir mais** _ de _ * termos incompletos * * para exibir os termos com informações ausentes. Você pode navegar até a página de detalhes do termo do glossário para inserir as informações ausentes e garantir que o termo do glossário esteja concluído.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como criar um termo do glossário por meio do [Glossário](./how-to-create-import-export-glossary.md)