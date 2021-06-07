---
title: Detalhes personalizados da superfície nos alertas do Azure Sentinel | Microsoft Docs
description: Extraia e Surface detalhes do evento personalizado em alertas em regras de análise do Azure Sentinel, para obter informações de incidentes melhores e mais completas
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
ms.openlocfilehash: 45f0ef5366d97c275c40d4d436020dbaf3501d42
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102456194"
---
# <a name="surface-custom-event-details-in-alerts-in-azure-sentinel"></a>Detalhes do evento personalizado da superfície em alertas no Azure Sentinel 

> [!IMPORTANT]
>
> - O recurso de detalhes personalizados está em versão **prévia**. Consulte os [termos de uso suplementares para Microsoft Azure visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) de termos legais adicionais que se aplicam aos recursos do Azure que estão em versão beta, visualização ou, de outra forma, ainda não foram lançadas em disponibilidade geral.

## <a name="introduction"></a>Introdução

[As regras de análise de consulta agendadas](tutorial-detect-threats-custom.md) analisam **eventos** de fontes de dados conectadas ao Azure Sentinel e geram **alertas** quando o conteúdo desses eventos é significativo de uma perspectiva de segurança. Esses alertas são analisados, agrupados e filtrados por vários mecanismos do Azure Sentinel e são ainda diferentes em **incidentes** que garantem a atenção de um analista de SOC. No entanto, quando o analista exibe o incidente, somente as propriedades dos próprios alertas do componente ficam visíveis imediatamente. Obtendo o conteúdo real-as informações contidas nos eventos – requer alguma coisa.

Usando o recurso de **detalhes personalizados** no **Assistente de regra de análise**, você pode listar dados de evento nos alertas construídos a partir desses eventos, tornando os dados de evento das propriedades do alerta. Na verdade, isso oferece visibilidade imediata do conteúdo do evento em seus incidentes, permitindo que você faça a triagem, investigue, desenhe conclusões e responda com muito mais velocidade e eficiência.

O procedimento detalhado abaixo faz parte do assistente de criação de regra de análise. Ele é tratado aqui de forma independente para abordar o cenário de adição ou alteração de detalhes personalizados em uma regra de análise existente.

## <a name="how-to-surface-custom-event-details"></a>Como retonar detalhes do evento personalizado

1. No menu de navegação do Azure Sentinel, selecione **análise**.

1. Selecione uma regra de consulta agendada e clique em **Editar**. Ou crie uma nova regra clicando em **criar &#10132; regra de consulta agendada** na parte superior da tela.

1. Clique na guia **definir lógica de regra** .

1. Na seção **aprimoramento de alerta** , selecione **detalhes personalizados**.

    :::image type="content" source="media/surface-custom-details-in-alerts/alert-enhancement.png" alt-text="Localizar e selecionar detalhes personalizados":::

1. Na seção **detalhes personalizados** agora expandidos, adicione pares de chave-valor correspondentes aos detalhes que você deseja trazer para a superfície:

    1. No campo **chave** , insira um nome de sua escolha que será exibido como o nome do campo em alertas.

    1. No campo **valor** , escolha o parâmetro de evento que você deseja Surface nos alertas da lista suspensa. Essa lista será preenchida por valores correspondentes aos campos nas tabelas que são o assunto da consulta de regra.
    
        :::image type="content" source="media/surface-custom-details-in-alerts/custom-details.png" alt-text="Adicionar detalhes personalizados":::

1. Clique em **Adicionar novo** à superfície mais detalhes, repetindo as últimas etapas para definir os pares de chave-valor. 

    Se você mudar de ideia ou se cometer um erro, poderá remover um detalhe personalizado clicando no ícone de Lixeira ao lado da lista suspensa **valor** para esse detalhe.

1. Quando terminar de definir os detalhes personalizados, clique na guia **revisar e criar** . Depois que a validação da regra for bem-sucedida, clique em **salvar**.

    > [!NOTE]
    > 
    >**Limites de serviço**
    > - Você pode definir **até 20 detalhes personalizados** em uma única regra de análise.
    >
    > - O limite de tamanho para todos os detalhes personalizados, coletivamente, é **2 KB**.

## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu a retonar detalhes personalizados em alertas usando regras de análise do Sentinela do Azure. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Obtenha a imagem completa em [regras de análise de consulta agendada](tutorial-detect-threats-custom.md).
- Saiba mais sobre [entidades no Azure Sentinel](entities-in-azure-sentinel.md).
