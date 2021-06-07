---
title: Alerta sobre problemas nos Serviços de Nuvem do Azure usando a integração do Diagnóstico do Azure com o Azure Application Insights | Microsoft Docs
description: Monitore problemas como falhas na inicialização, panes e loops de reciclagem de função nos Serviços de Nuvem do Azure com o Azure Application Insights
ms.topic: conceptual
ms.date: 06/07/2018
ms.reviewer: harelbr
ms.openlocfilehash: 1cdfc6dc3ac74997743512ee07f9293699e3ad10
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87309283"
---
# <a name="alert-on-issues-in-azure-cloud-services-using-the-azure-diagnostics-integration-with-azure-application-insights"></a>Alerta sobre problemas nos Serviços de Nuvem do Azure usando a integração do diagnóstico do Azure com o Azure Application Insights

Neste artigo, descreveremos como configurar regras de alerta que monitoram problemas como falhas na inicialização, panes e loops de reciclagem de função nos Serviços de Nuvem do Azure (funções de trabalho e da Web).

O método descrito neste artigo é baseado em [Integração do Diagnóstico do Azure com o Application Insights](https://azure.microsoft.com/blog/azure-diagnostics-integration-with-application-insights/) e na funcionalidade lançada recentemente [Alertas de log para o Application Insights](https://azure.microsoft.com/blog/log-alerts-for-application-insights-preview/).

## <a name="define-a-base-query"></a>Definir uma consulta base

Para começar, definiremos uma consulta base que recupera os eventos do Log de Eventos do Windows do canal do Microsoft Azure, que são capturados no Application Insights como registros de rastreamento.
Esses registros podem ser usados para detectar uma variedade de problemas nos Serviços de Nuvem do Azure, como falhas na inicialização, falhas no runtime e loops de reciclagem.

> [!NOTE]
> A consulta base abaixo verifica se há problemas em uma janela de tempo de 30 minutos e pressupõe uma latência de 10 minutos ao ingerir os registros de telemetria. Esses padrões podem ser configurados como desejar.

```
let window = 30m;
let endTime = ago(10m);
let EventLogs = traces
| where timestamp > endTime - window and timestamp < endTime
| extend channel = tostring(customDimensions.Channel), eventId = tostring(customDimensions.EventId)
| where channel == 'Windows Azure' and isnotempty(eventId)
| where tostring(customDimensions.DeploymentName) !contains 'deployment' // discard records captured from local machines
| project timestamp, channel, eventId, message, cloud_RoleInstance, cloud_RoleName, itemCount;
```

## <a name="check-for-specific-event-ids"></a>Verificar IDs de evento específicas

Após recuperar os eventos do Log de Eventos do Windows, problemas específicos podem ser detectados verificando as respectivas propriedades de mensagem e da ID do evento (consulte os exemplos abaixo).
Basta combinar a consulta base acima com uma das consultas abaixo e usar essa consulta combinada ao definir a regra de alerta de log.

> [!NOTE]
> Nos exemplos abaixo, um problema será detectado se mais de três eventos forem encontrados durante a janela de tempo analisada. Esse padrão pode ser configurado para alterar a sensibilidade da regra de alerta.

```
// Detect failures in the OnStart method
EventLogs
| where eventId == '2001'
| where message contains '.OnStart()'
| summarize Failures = sum(itemCount) by cloud_RoleInstance, cloud_RoleName
| where Failures > 3
```

```
// Detect failures during runtime
EventLogs
| where eventId == '2001'
| where message contains '.Run()'
| summarize Failures = sum(itemCount) by cloud_RoleInstance, cloud_RoleName
| where Failures > 3
```

```
// Detect failures when running a startup task
EventLogs
| where eventId == '1000'
| summarize Failures = sum(itemCount) by cloud_RoleInstance, cloud_RoleName
| where Failures > 3
```

```
// Detect recycle loops
EventLogs
| where eventId == '1006'
| summarize Failures = sum(itemCount) by cloud_RoleInstance, cloud_RoleName
| where Failures > 3
```

## <a name="create-an-alert"></a>Criar um alerta

No menu de navegação dentro do seu recurso do Application Insights, acesse **Alertas** e selecione **Nova regra de alerta**.

![Captura de tela de criação de regra](./media/proactive-cloud-services/001.png)

Na janela **Criar regra**, na seção **Definir condição do alerta**, clique em **Adicionar critérios** e, em seguida, selecione **Pesquisa de logs personalizada**.

![Captura de tela da definição de critérios de condição para o alerta](./media/proactive-cloud-services/002.png)

Na caixa **Consulta de pesquisa**, cole a consulta combinada que você preparou na etapa anterior.

Em seguida, continue para a caixa **Limite** e defina seu valor como 0. Opcionalmente, é possível ajustar os **campos****Período** e Frequência.
Clique em **Concluído**.

![Captura de tela da configuração de consulta lógica de sinal](./media/proactive-cloud-services/003.png)

Na seção **Definir detalhes do alerta**, forneça um **Nome** e uma **Descrição** para a regra de alerta e defina sua **Gravidade**.
Além disso, certifique-se de que o botão **Habilitar regra durante a criação** está definido como **Sim**.

![Captura de tela de detalhes do alerta](./media/proactive-cloud-services/004.png)

Na seção **Definir grupo de ação**, é possível selecionar um **Grupo de ação** existente ou criar um novo.
É possível optar para que o grupo de ação contenha várias ações de vários tipos.

![Captura de tela do grupo de ação](./media/proactive-cloud-services/005.png)

Após definir o Grupo de ação, confirme suas alterações e clique em **Criar regra de alerta**.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como detectar automaticamente:

[Anomalias de falha](./proactive-failure-diagnostics.md)
[Vazamentos de memória](./proactive-potential-memory-leak.md)
[Anomalias de desempenho](./proactive-performance-diagnostics.md)

