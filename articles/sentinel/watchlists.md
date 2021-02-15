---
title: Usar watchlists do Azure Sentinel
description: Este artigo descreve como usar o Azure Sentinel watchlists investigar ameaças, importar dados corporativos, criar listas de permissões e enriquecer dados de evento.
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.custom: mvc
ms.date: 09/06/2020
ms.openlocfilehash: 97509b878fb5e0cb28bddc5d1b58c21b32c34675
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99555643"
---
# <a name="use-azure-sentinel-watchlists"></a>Usar watchlists do Azure Sentinel

> [!IMPORTANT]
> O recurso watchlists está atualmente em **Visualização**. Consulte os [termos de uso suplementares para Microsoft Azure visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) de termos legais adicionais que se aplicam aos recursos do Azure que estão em versão beta, visualização ou, de outra forma, ainda não foram lançadas em disponibilidade geral.

O Azure Sentinel watchlists permite a coleta de dados de fontes de dados externas para correlação com os eventos em seu ambiente do Azure Sentinel. Depois de criado, você pode usar o watchlists em sua pesquisa, regras de detecção, busca de ameaças e guias estratégicos de resposta. Os Watchlists são armazenados em seu espaço de trabalho do Azure Sentinel como pares de nome-valor e são armazenados em cache para desempenho de consulta ideal e baixa latência.

Os cenários comuns para usar o watchlists incluem:

- **Investigação de ameaças** e resposta a incidentes rapidamente com a rápida importação de endereços IP, hashes de arquivo e outros dados de arquivos CSV. Depois de importado, você pode usar os pares nome-valor da inspeção para junções e filtros em regras de alerta, busca de ameaças, pastas de trabalho, blocos de anotações e consultas gerais.

- **Importando dados corporativos** como uma inspeção. Por exemplo, importe listas de usuários com acesso de sistema privilegiado ou funcionários demitidos e, em seguida, use a inspeção para criar listas de permissão e negação usadas para detectar ou impedir que esses usuários façam logon na rede.

- **Reduzindo o alerta fadiga**. Crie listas de permissões para suprimir alertas de um grupo de usuários, como usuários de endereços IP autorizados que executam tarefas que normalmente disparam o alerta e evitar que eventos benignos se tornem alertas.

- **Enriquecer dados de eventos**. Use watchlists para enriquecer seus dados de evento com combinações de nome-valor derivadas de fontes de dados externas.

## <a name="create-a-new-watchlist"></a>Criar uma Nova inspeção

1. No portal do Azure, navegue até a inspeção de configuração **do Azure Sentinel**  >    >   e, em seguida, selecione **Adicionar novo**.

    > [!div class="mx-imgBorder"]
    > ![Nova inspeção](./media/watchlists/sentinel-watchlist-new.png)

1. Na página **geral** , forneça o nome, a descrição e o alias para a Watchlist e, em seguida, selecione **Avançar**.

    > [!div class="mx-imgBorder"]
    > ![página Geral da Watchlist](./media/watchlists/sentinel-watchlist-general.png)

1. Na página **origem** , selecione o tipo de conjunto de texto, carregue um arquivo e, em seguida, selecione **Avançar**.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-source.png" alt-text="página de origem da Watchlist" lightbox="./media/watchlists/sentinel-watchlist-source.png":::

    > [!NOTE]
    >
    > Os carregamentos de arquivo estão atualmente limitados a arquivos de até 3,8 MB de tamanho.

1. Examine as informações, verifique se elas estão corretas e, em seguida, selecione **criar**.

    > [!div class="mx-imgBorder"]
    > ![página de revisão da Watchlist](./media/watchlists/sentinel-watchlist-review.png)

    Uma notificação é exibida assim que a inspeção é criada.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-complete.png" alt-text="notificação de criação com êxito da inspeçãolist" lightbox="./media/watchlists/sentinel-watchlist-complete.png":::

## <a name="use-watchlists-in-queries"></a>Usar watchlists em consultas

1. No portal do Azure, navegue até a inspeção de configuração **do Azure Sentinel**  >    >  , selecione a inspeção que você deseja usar e, em seguida, selecione **Exibir no log Analytics**.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-queries-list.png" alt-text="usar watchlists em consultas" lightbox="./media/watchlists/sentinel-watchlist-queries-list.png":::

1. Os itens em sua lista de observação são extraídos automaticamente para sua consulta e aparecerão na guia **resultados** . O exemplo a seguir mostra os resultados da extração dos campos **ServerName** e **IPAddress** .

    > [!NOTE]
    > O carimbo de data/hora em suas consultas será ignorado na interface do usuário da consulta e nos alertas agendados.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-queries-fields.png" alt-text="consultas com campos Watchlist" lightbox="./media/watchlists/sentinel-watchlist-queries-fields.png":::
    
1. Você pode consultar dados em qualquer tabela em relação a dados de uma inspeção de observação tratando a inspeção como uma tabela para junções e pesquisas.

    ```kusto
    Heartbeat
    | lookup kind=leftouter _GetWatchlist('IPlist') 
     on $left.ComputerIP == $right.IPAddress
    ```
    :::image type="content" source="./media/watchlists/sentinel-watchlist-queries-join.png" alt-text="consultas em Watchlist como pesquisa":::

## <a name="use-watchlists-in-analytics-rules"></a>Usar o watchlists em regras de análise

Para usar o watchlists em regras de análise, no portal do Azure, navegue até **Azure Sentinel**  >  **Configuration**  >  **Analytics** e crie uma regra usando a `_GetWatchlist('<watchlist>')` função na consulta.

1. Neste exemplo, crie uma Watchlist chamada "ipwatchlist" com os seguintes valores:

    :::image type="content" source="./media/watchlists/create-watchlist.png" alt-text="lista de quatro itens para Watchlist":::

    :::image type="content" source="./media/watchlists/sentinel-watchlist-new-2.png" alt-text="criar inspeção de observação com quatro itens":::

1. Em seguida, crie a regra de análise.  Neste exemplo, incluímos apenas eventos de endereços IP na Watchlist:

    ```kusto
    //Watchlist as a variable
    let watchlist = (_GetWatchlist('ipwatchlist') | project IPAddress);
    Heartbeat
    | where ComputerIP in (watchlist)
    ```
    ```kusto
    //Watchlist inline with the query
    Heartbeat
    | where ComputerIP in ( 
        (_GetWatchlist('ipwatchlist')
        | project IPAddress)
    )
    ```

:::image type="content" source="./media/watchlists/sentinel-watchlist-analytics-rule-2.png" alt-text="usar o watchlists em regras de análise":::

## <a name="view-list-of-watchlists-aliases"></a>Exibir a lista de aliases do watchlists

Para obter uma lista de aliases do Watchlist, na portal do Azure, navegue até logs **gerais do Sentinela do Azure**  >    >  e execute a seguinte consulta: `_GetWatchlistAlias` . Você pode ver a lista de aliases na guia **resultados** .

> [!div class="mx-imgBorder"]
> ![listar watchlists](./media/watchlists/sentinel-watchlist-alias.png)

## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu a usar o watchlists no Azure Sentinel para enriquecer dados e aprimorar investigações. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](./tutorial-detect-threats-built-in.md).
- [Use pastas de trabalho](tutorial-monitor-your-data.md) para monitorar seus dados.
