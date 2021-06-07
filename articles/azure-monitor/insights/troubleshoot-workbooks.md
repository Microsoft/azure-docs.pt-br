---
title: Solução de problemas Azure Monitor informações baseadas em pasta de trabalho
description: Fornece diretrizes de solução de problemas para Azure Monitor informações baseadas em pasta de trabalho para serviços como Azure Key Vault, Azure CosmosDB, armazenamento do Azure e cache do Azure para Redis.
services: azure-monitor
ms.author: lagayhar
author: lgayhardt
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: a823b5e2ae31e62ff6d876f0b3468953c0ed9c35
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100587148"
---
# <a name="troubleshooting-workbook-based-insights"></a>Solucionando problemas de informações baseadas em pasta de trabalho

Este artigo o ajudará com o diagnóstico e a solução de problemas de alguns dos problemas comuns que você pode encontrar ao usar Azure Monitor insights baseados em pasta de trabalho.


## <a name="why-can-i-only-see-200-resources"></a>Por que só posso ver os recursos 200

O número de recursos selecionados tem um limite de 200, independentemente do número de assinaturas selecionadas.

## <a name="what-happens-when-i-click-on-a-recently-pinned-tile-in-the-dashboard"></a>O que acontece quando eu clico em um bloco recentemente afixado no painel

* Se você clicar em qualquer lugar do bloco, ele o levará para a guia em que foi afixado. Por exemplo, se você fixar um grafo na guia "visão geral", ao clicar nesse bloco no painel, ele abrirá esse modo de exibição padrão. no entanto, se você fixar um grafo de sua própria cópia salva, ele abrirá a exibição da cópia salva.
* O ícone de filtro na parte superior esquerda do título abre a guia "Definir configurações de bloco".
* O ícone de elipse no canto superior direito fornecerá as opções "Personalizar dados de título", "personalizar", "atualizar" e "remover do painel".

## <a name="what-happens-when-i-save-a-workbook"></a>O que acontece quando eu salvo uma pasta de trabalho

* Quando você salva uma pasta de trabalho, ela permite que você crie uma nova cópia da pasta de trabalho com suas edições e altere o título. Salvar não substitui a pasta de trabalho, a pasta de trabalho atual sempre será a exibição padrão.
* Uma pasta de trabalho **não salva** é apenas a exibição padrão.

## <a name="why-dont-i-see-all-my-subscriptions-in-the-portal"></a>Por que não vejo todas as minhas assinaturas no portal

O portal mostrará apenas os dados das assinaturas selecionadas na sua inicialização. Para alterar as assinaturas que estarão selecionadas, vá até o canto superior direito e clique no bloco de notas com um ícone de filtro. Esta opção mostrará a guia **diretório + assinaturas** .

![Diretório + assinatura](./media/storage-insights-overview/fqa3.png)

## <a name="what-is-time-range"></a>O que é o intervalo de tempo

O intervalo de tempo mostra os dados de um determinado período. Por exemplo, se o intervalo de tempo for de 24 horas, ele mostrará dados das últimas 24 horas.

## <a name="what-is-time-granularity-time-grain"></a>O que é granularidade de tempo (intervalo de tempo)

Granularidade de tempo é a diferença de tempo entre dois pontos de dados. Por exemplo, se o intervalo de agregação for definido com 1 segundo, isso significa que as métricas serão coletadas a cada segundo.

## <a name="what-is-the-time-granularity-once-we-pin-any-part-of-the-workbooks-to-a-dashboard"></a>Qual é a granularidade de tempo quando fixamos qualquer parte das pastas de trabalho em um painel

Quando a granularidade de tempo padrão é configurada como automática, ela não pode ser alterada no momento atual.

## <a name="how-do-i-change-the-timespan-time-range-of-the-workbook-step-on-my-dashboard"></a>Como fazer alterar o intervalo de TimeSpan/hora da etapa da pasta de trabalho no meu painel

Por padrão, o período/intervalo de tempo do bloco do painel é configurado com 24 horas; para alterá-lo, clique nas reticências no canto superior direito, selecione **Personalizar dados de bloco** , marque a caixa "substituir as configurações de tempo do painel no nível de bloco" e selecione um período de tempo usando o menu suspenso.  

![Selecione as reticências no canto direito do bloco e escolha Personalizar estes dados](./media/storage-insights-overview/fqa-data-settings.png)

![Em definir configurações de bloco, selecione o menu suspenso TimeSpan para alterar o intervalo TimeSpan/time](./media/storage-insights-overview/fqa-timespan.png)

## <a name="how-do-i-change-the-title-of-the-workbook-or-a-workbook-step-i-pinned-to-a-dashboard"></a>Como fazer alterar o título da pasta de trabalho ou uma etapa da pasta de trabalho fixada em um painel

O título da pasta de trabalho ou a etapa que foi afixada em um painel retém o mesmo nome que tinha na pasta de trabalho. Para alterar o título, você deve salvar sua própria cópia da pasta de trabalho. Em seguida, poderá nomear a pasta de trabalho antes de pressionar salvar.

![Selecione salvar na parte superior para salvar uma cópia da pasta de trabalho e alterar o nome dela](./media/storage-insights-overview/fqa-change-workbook-name.png)

Para alterar o nome de uma etapa na pasta de trabalho salva, selecione Editar na etapa e, em seguida, selecione a engrenagem na parte inferior das configurações.

![Selecione editar na parte inferior de uma etapa da pasta de trabalho para abrir as configurações](./media/storage-insights-overview/fqa-edit.png)
![Em configurações, selecione a engrenagem na parte inferior para poder alterar o nome da etapa](./media/storage-insights-overview/fqa-change-name.png)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre os cenários em que as pastas de trabalho foram projetadas para dar suporte, como criar novos e personalizar relatórios existentes e muito mais examinando a [criação de relatórios interativos com pastas de trabalho do Azure monitor](../visualize/workbooks-overview.md).
