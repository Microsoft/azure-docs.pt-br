---
title: Mapear campos de dados para entidades do Azure Sentinel | Microsoft Docs
description: Mapear campos de dados em tabelas para entidades do Azure Sentinel em regras de análise, para obter melhores informações de incidente
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/10/2021
ms.author: yelevin
ms.openlocfilehash: cb91d269f6b166510db54637d17d776e71137408
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2021
ms.locfileid: "102456202"
---
# <a name="map-data-fields-to-entities-in-azure-sentinel"></a>Mapear campos de dados para entidades no Azure Sentinel 

> [!IMPORTANT]
>
> - A nova versão do recurso de mapeamento de entidade está em versão **prévia**. Consulte os [termos de uso suplementares para Microsoft Azure visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) de termos legais adicionais que se aplicam aos recursos do Azure que estão em versão beta, visualização ou, de outra forma, ainda não foram lançadas em disponibilidade geral.

> [!IMPORTANT]
>
> - Consulte [observações sobre a nova versão](#notes-on-the-new-version) no final deste documento para obter informações importantes sobre a compatibilidade com versões anteriores e as diferenças entre as versões novas e antigas do mapeamento de entidade.

## <a name="introduction"></a>Introdução

O mapeamento de entidade é parte integrante da configuração de [regras de análise de consulta agendada](tutorial-detect-threats-custom.md). Ele enriquece a saída das regras (alertas e incidentes) com informações essenciais que servem como os blocos de construção de quaisquer processos investigativos e ações corretivas a seguir.

O procedimento detalhado abaixo faz parte do assistente de criação de regra de análise. Ele é tratado aqui de forma independente para tratar do cenário de adição ou alteração de mapeamentos de entidade em uma regra de análise existente.

## <a name="how-to-map-entities"></a>Como mapear entidades

1. No menu de navegação do Azure Sentinel, selecione **análise**.

1. Selecione uma regra de consulta agendada e clique em **Editar**. Ou crie uma nova regra clicando em **criar &#10132; regra de consulta agendada** na parte superior da tela.

1. Clique na guia **definir lógica de regra** .

    :::image type="content" source="media/map-data-fields-to-entities/map-entities.png" alt-text="Mapear campos para entidades":::

1. Na seção **aprimoramento de alerta** , em **mapeamento de entidade**, selecione um tipo de entidade na lista suspensa **tipo de entidade** .

1. Selecione um **identificador** para a entidade. Identificadores são atributos de uma entidade que pode identificá-lo suficientemente. Escolha um na lista suspensa **identificador** e, em seguida, escolha um campo de dados na lista suspensa **valor** que corresponderá ao identificador. Com algumas exceções, a lista de **valores** é preenchida pelos campos de dados na tabela definida como o assunto da consulta de regra.

    Você pode definir **até três identificadores** para uma determinada entidade. Alguns identificadores são necessários, outros são opcionais. Você deve escolher pelo menos um identificador necessário. Se você não fizer isso, uma mensagem de aviso informará quais identificadores são necessários. Para obter melhores resultados-para a identificação exclusiva máxima – você deve usar **identificadores fortes** sempre que possível e usar vários identificadores fortes permitirá maior correlação entre fontes de dados. Consulte a lista completa de [entidades e identificadores](entities-reference.md)disponíveis.

1. Clique em **Adicionar nova entidade** para mapear mais entidades. Você pode mapear **até cinco entidades** em uma única regra de análise. Você também pode mapear mais de um do mesmo tipo. Por exemplo, você pode mapear duas entidades de **IP** , uma de um campo de *endereço IP de origem* e outra de um campo de *endereço IP de destino* . Dessa forma, você pode rastreá-los.

    Se você mudar de ideia ou se cometer um erro, poderá remover um mapeamento de entidade clicando no ícone de Lixeira ao lado da lista suspensa entidade.

1. Quando terminar de mapear entidades, clique na guia **revisar e criar** . Depois que a validação da regra for bem-sucedida, clique em **salvar**.

## <a name="notes-on-the-new-version"></a>Observações sobre a nova versão

- Se você tiver definido anteriormente mapeamentos de entidade para essa regra de análise usando a versão antiga, esses mapeamentos aparecerão no código de consulta. Mapeamentos de entidade definidos na nova versão **não aparecem no código de consulta**. As regras de análise podem dar suporte apenas a uma versão de mapeamentos de entidade por vez, e a nova versão tem precedência. Portanto, qualquer mapeamento único que você definir aqui fará com que **todos** os mapeamentos definidos no código de consulta sejam **desconsiderados** quando a consulta for executada. 

- Se você ainda precisar usar a **versão antiga** do mapeamento de entidade (desde que a nova versão ainda esteja em versão prévia), você ainda poderá acessá-la usando um sinalizador de recurso na URL. Coloque o cursor entre `https://portal.azure.com/` e e `#blade` Insira o texto `?feature.EntityMapping=false` .

  - Os limites da versão antiga continuarão a ser aplicados. Você pode mapear somente as entidades usuário, host, endereço IP, URL e hash de arquivo e apenas uma delas.

  - Você deve **remover** todos os mapeamentos de entidade criados usando a nova versão **antes** de retornar para a versão antiga, caso contrário, os mapeamentos de entidade que usam a versão antiga **não funcionarão**.

- Depois que a nova versão do mapeamento de entidade estiver em disponibilidade geral, não será mais possível usar a versão antiga. É altamente recomendável que você migre os mapeamentos de entidade antigos para a nova versão.


## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu como mapear campos de dados para entidades em regras de análise do Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Obtenha a imagem completa em [regras de análise de consulta agendada](tutorial-detect-threats-custom.md).
- Saiba mais sobre [entidades no Azure Sentinel](entities-in-azure-sentinel.md).
