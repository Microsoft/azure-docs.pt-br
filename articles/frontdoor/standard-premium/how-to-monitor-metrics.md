---
title: Métricas de monitoramento para o padrão de porta frontal do Azure/Premium
description: Este artigo descreve as métricas de monitoramento Standard/Premium de porta frontal do Azure.
services: frontdoor
author: duau
manager: KumudD
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: bb10fb337972db2696960b530f2d7538bd36a2fb
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2021
ms.locfileid: "101098318"
---
# <a name="real-time-monitoring-in-azure-front-door-standardpremium"></a>Monitoramento em tempo real no Azure front door Standard/Premium

> [!Note]
> Esta documentação é para o Azure front door Standard/Premium (versão prévia). Procurando informações sobre a porta frontal do Azure? Veja [aqui](../front-door-overview.md).

O Azure front door Standard/Premium é integrado com o Azure Monitor e tem 11 métricas para ajudar a monitorar o Azure front door Standard/Premium em tempo real para controlar, solucionar problemas e depurar problemas.  

As medidas Standard/Premium da porta de front-end do Azure e envia suas métricas em intervalos de 60 segundos. As métricas podem levar até 3 minutos para aparecer no Portal. As métricas podem ser exibidas em gráficos ou na grade de sua escolha e podem ser acessadas por meio do portal, do PowerShell, da CLI e da API. Para obter mais informações, consulte [Azure monitor métricas](../../azure-monitor/platform/data-platform-metrics.md).  

As métricas padrão são gratuitas. Você pode habilitar métricas adicionais para um custo extra. 

Você pode configurar alertas para cada métrica, como um limite para 4XXErrorRate ou 5XXErrorRate. Quando a taxa de erro exceder o limite, ela disparará um alerta conforme configurado. Para obter mais informações, confira [Criar, exibir e gerenciar alertas de métrica usando o Azure Monitor](../../azure-monitor/platform/alerts-metric.md). 

> [!IMPORTANT]
> O Azure front door Standard/Premium (visualização) está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="metrics-supported-in-azure-front-door-standardpremium"></a>Métricas com suporte no Azure front door Standard/Premium

| Métricas  | Descrição | Dimensões |
| ------------- | ------------- | ------------- |
| Taxa de acertos de bytes | A porcentagem de egresso do cache de AFD, computada com relação à saída total. </br> **Taxa de acertos de bytes** = (saída da borda-saída da origem)/egress da borda. </br> **Cenários excluídos no cálculo da taxa de acertos de bytes**:</br> 1. você não configurou explicitamente nenhum cache por meio do mecanismo de regras ou do comportamento de cache de cadeia de caracteres de consulta. </br> 2. você configurou explicitamente a diretiva de controle de cache com cache não armazenado ou privado. </br>3. a taxa de acertos de bytes pode ser baixa se a maior parte do tráfego é encaminhada para a origem, em vez de ser servida do cache com base em suas configurações ou cenários. | Ponto de extremidade |
| RequestCount | O número de solicitações de cliente atendidas pela CDN. | Ponto de extremidade, país do cliente, região do cliente, status do HTTP, grupo de status HTTP |
| ResponseSize | O número de solicitações de cliente atendidas por AFD. |Ponto de extremidade, país do cliente, região do cliente, status do HTTP, grupo de status HTTP |
| TotalLatency | O tempo total da solicitação do cliente recebida pela CDN **até que o último byte de resposta seja enviado da CDN para o cliente**. |Ponto de extremidade, país do cliente, região do cliente, status do HTTP, grupo de status HTTP |
| RequestSize | O número de bytes enviados como solicitações de clientes a AFD. | Ponto de extremidade, país do cliente, região do cliente, status do HTTP, grupo de status HTTP |
| 4XX% ErrorRate | A porcentagem de todas as solicitações de cliente para as quais o código de status de resposta é 4XX. | Ponto de extremidade, país do cliente, região do cliente |
| 5XX% ErrorRate | A porcentagem de todas as solicitações de cliente para as quais o código de status de resposta é 5XX. | Ponto de extremidade, país do cliente, região do cliente |
| OriginRequestCount  | O número de solicitações enviadas de AFD para origem | Ponto de extremidade, origem, status HTTP, grupo de status HTTP |
| OriginLatency | O tempo calculado a partir de quando a solicitação foi enviada pela borda de AFD para o back-end até que AFD receba o último byte de resposta do back-end. | Ponto de extremidade, origem |
| OriginHealth% | A porcentagem de investigações de integridade bem-sucedidas de AFD para origem.| Origem, grupo de origem |
| Contagem de solicitações WAF | Solicitação WAF correspondente. | Ação, nome da regra, nome da política |

## <a name="access-metrics-in-azure-portal"></a>Métricas de acesso no portal do Azure

1. No menu portal do Azure, selecione **todos os recursos**  >>  **\<your-AFD Standard/Premium (Preview) -profile>** .

2. Em **monitoramento**, selecione **métricas**:

3. Em **métricas**, selecione a métrica a ser adicionada:

   :::image type="content" source="../media/how-to-monitoring-metrics/front-door-metrics-1.png" alt-text="Captura de tela da página métricas." lightbox="../media/how-to-monitoring-metrics/front-door-metrics-1-expanded.png":::

4. Selecione **Adicionar filtro** para adicionar um filtro:

    :::image type="content" source="../media/how-to-monitoring-metrics/front-door-metrics-2.png" alt-text="Captura de tela de adição de filtros a métricas." lightbox="../media/how-to-monitoring-metrics/front-door-metrics-2-expanded.png":::
    
5. Selecione **aplicar divisão** para dividir os dados por dimensões diferentes:

   :::image type="content" source="../media/how-to-monitoring-metrics/front-door-metrics-4.png" alt-text="Captura de tela da adição de dimensões às métricas." lightbox="../media/how-to-monitoring-metrics/front-door-metrics-4-expanded.png":::

6. Selecione **novo gráfico** para adicionar um novo gráfico:

## <a name="configure-alerts-in-azure-portal"></a>Configurar alertas no portal do Azure

1. Configure alertas no Azure front door Standard/Premium (versão prévia) selecionando   >>  **alertas** de monitoramento.

1. Selecione **nova regra de alerta** para as métricas listadas na seção métricas.

O alerta será cobrado com base em Azure Monitor. Para obter mais informações sobre alertas, consulte [Azure monitor alertas](../../azure-monitor/platform/alerts-overview.md).

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre os [relatórios Standard/Premium da porta de front-end do Azure](how-to-reports.md).
- Saiba mais sobre [os logs Premium/Standard da porta do Azure](how-to-logs.md).
