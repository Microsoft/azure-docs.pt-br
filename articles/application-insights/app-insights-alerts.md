---
title: Definir alertas no Azure Application Insights | Microsoft Docs
description: "Seja notificado sobre os tempos de resposta lentos, as exceções e outras alterações de desempenho ou de uso em seu aplicativo Web."
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.assetid: f8ebde72-f819-4ba5-afa2-31dbd49509a5
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: bwren
ms.openlocfilehash: c8386ffc5d68260eeb75edf7efb77db1163dcef8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="set-alerts-in-application-insights"></a>Definir alertas no Application Insights
O [Azure Application Insights][start] pode alertá-lo sobre as alterações nas métricas de desempenho ou de uso do aplicativo Web. 

O Application Insights monitora o aplicativo ativo em um [ampla variedade de plataformas][platforms] para ajudá-lo a diagnosticar problemas de desempenho e compreender os padrões de uso.

Há três tipos de alerta:

* Os **alertas de métrica** informam quando uma métrica ultrapassa um valor limite durante determinado período – como tempos de resposta, contagens de exceção, uso da CPU ou exibições de página. 
* Os [**testes da Web**][availability] informam quando site está indisponível na Internet ou está respondendo com lentidão. [Saiba mais][availability].
* Os [**diagnósticos proativos**](app-insights-proactive-diagnostics.md) são configurados automaticamente para notificar você sobre padrões de desempenho incomuns.

Vamos nos concentrar nos alertas de métricas deste artigo.

## <a name="set-a-metric-alert"></a>Definir um alerta de Métrica
Abra a folha Regras de alerta e, em seguida, use o botão adicionar. 

![Na folha das Regras de alerta, escolha Adicionar Alerta. Defina seu aplicativo como o recurso a ser medido, forneça um nome para o alerta e escolha uma métrica.](./media/app-insights-alerts/01-set-metric.png)

* Defina o recurso antes de outras propriedades. **Escolha o recurso "(componentes)"** se desejar definir alertas em métricas de desempenho ou de uso.
* O nome dado ao alerta deve ser exclusivo dentro do grupo de recursos (não apenas no seu aplicativo).
* Observe as unidades quando você for solicitado para inserir o valor de limite.
* Se você marcar a caixa “Proprietários de email...”, os alertas serão enviados por email para qualquer pessoa que tenha acesso a esse grupo de recursos. Para expandir esse grupo de pessoas, adicione-as à [assinatura ou grupo de recursos](app-insights-resources-roles-access-control.md) (não o recurso).
* Se você especificar “Emails adicionais”, os alertas serão enviados aos indivíduos ou grupos (sem levar em conta se a caixa “proprietários de email...” foi marcada ou não). 
* Defina um [endereço de webhook](../monitoring-and-diagnostics/insights-webhooks-alerts.md) se tiver configurado um aplicativo Web que responda aos alertas. Ele é chamado quando o alerta é ativado e quando ele está resolvido. (Mas observe que, no momento, os parâmetros de consulta não são passados como propriedades de webhook)
* É possível Desabilitar ou Habilitar o alerta: veja os botões na parte superior da folha.

*Não vejo o botão Adicionar Alerta.* 

* Você está usando uma conta organizacional? Você poderá definir alertas se tiver acesso de proprietário ou colaborador a esse recurso de aplicativo. Vejamos a folha Controle de Acesso. [Saiba mais sobre o controle de acesso][roles].

> [!NOTE]
> Na folha de alertas, você vê que já existe uma configuração de alerta: [Proactive Diagnostics](app-insights-proactive-failure-diagnostics.md). O alerta automático monitora uma métrica específica, taxa de falha de solicitação. A menos que você opte por desabilitar o alerta proativo, não precisa definir seu próprio alerta na taxa de falha de solicitação. 
> 
> 

## <a name="see-your-alerts"></a>Ver seus alertas
Você recebe um email quando o estado de um alerta é mudado de inativo para ativo. 

O estado atual de cada alerta é mostrado na folha de regras de Alerta.

Há um resumo da atividade recente no menu suspenso de alertas:

![Lista suspensa Alertas](./media/app-insights-alerts/010-alert-drop.png)

O histórico das alterações de estado está no Log de Atividades:

![Na folha Visão geral, clique em Configurações, Logs de Auditoria](./media/app-insights-alerts/09-alerts.png)

## <a name="how-alerts-work"></a>Como funcionam os alertas
* Um alerta tem três estados: “Nunca ativado”, “Ativado” e “Resolvido”. Ativado significa que a condição especificada era true, quando ela foi avaliada pela última vez.
* Uma notificação é gerada quando um alerta muda de estado. (Se a condição do alerta já era true quando o alerta foi criado, talvez você não receba uma notificação até que a condição mude para false.)
* Cada notificação gerará um email caso tenha marcado a caixa de emails ou fornecido endereços de email. Também é possível examinar a lista suspensa Notificações.
* Um alerta é avaliado toda vez que uma métrica chega, mas não o contrário.
* A avaliação agrega a métrica ao longo do período anterior e a compara com o limite para determinar o novo estado.
* O período que você escolhe especifica o intervalo durante o qual as métricas são agregadas. Ele não afeta a frequência com que o alerta é avaliado: isto depende da frequência da chegada das métricas.
* Se nenhum dado chegar para uma determinada métrica por algum tempo, a lacuna tem efeitos diferentes sobre a avaliação do alerta e sobre os gráficos no Metrics Explorer. No Metrics Explorer, se nenhum dado for visto por mais tempo do que o intervalo de amostragem do gráfico, o gráfico mostrará um valor 0. Mas um alerta com base na mesma métrica não é avaliado novamente e o estado do alerta permanece inalterado. 
  
    Quando os dados chegam, o gráfico volta para um valor diferente de zero. O alerta é avaliado com base nos dados disponíveis para o período especificado. Se o novo ponto de dados for o único disponível no período, a agregação se baseará apenas nisso.
* Um alerta pode piscar frequentemente entre os estados de alerta e íntegro, mesmo que você defina um longo período. Isso pode acontecer se o valor da métrica estiver em torno do limite. Não há nenhuma histerese no limite: a transição para o alerta acontece com o mesmo valor que a transição para o estado íntegro.

## <a name="what-are-good-alerts-to-set"></a>Quais são alguns alertas que é recomendável definir?
Depende de seu aplicativo. Para começar, é melhor não definir um número excessivo de métricas. Passe algum tempo examinando seus gráficos de métricas enquanto seu aplicativo está em execução para ter uma noção de como ele se comporta normalmente. Esta prática ajuda a encontrar maneiras de melhorar o desempenho. Em seguida, configure alertas para informá-lo quando as métricas estiverem fora da zona normal. 

Alguns alertas populares são:

* [Métricas de navegador][client], especificamente, **tempos de carregamento de página** do navegador, são adequados para aplicativos Web. Se a página tem muitos scripts, você deve procurar **exceções de navegador**. Para obter essas métricas e esses alertas, você precisa configurar o [monitoramento de página da Web][client].
* **Tempo de resposta do servidor** para o lado do servidor de aplicativos Web. Além de configurar alertas, confira essa métrica para ver se ela varia de forma desproporcional com altas taxas de solicitação: a variação pode indicar que seu aplicativo está ficando sem recursos. 
* **Exceções de servidor** - para vê-las, você precisa fazer algumas [configurações adicionais](app-insights-asp-net-exceptions.md).

Não se esqueça de que [diagnósticos de taxa de falha proativos](app-insights-proactive-failure-diagnostics.md) monitoram automaticamente a taxa em que seu aplicativo responde às solicitações com códigos de falha. 

## <a name="automation"></a>Automação
* [Usar o PowerShell para automatizar a configuração de alertas](app-insights-powershell-alerts.md)
* [Usar webhooks para automatizar a resposta a alertas](../monitoring-and-diagnostics/insights-webhooks-alerts.md)

## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="see-also"></a>Consulte também
* [Testes de disponibilidade na Web](app-insights-monitor-web-app-availability.md)
* [Automatizar a configuração de alertas](app-insights-powershell-alerts.md)
* [Diagnóstico proativo](app-insights-proactive-diagnostics.md) 

<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[client]: app-insights-javascript.md
[platforms]: app-insights-platforms.md
[roles]: app-insights-resources-roles-access-control.md
[start]: app-insights-overview.md

