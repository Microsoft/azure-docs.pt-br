---
title: Suporte a alto volume de tráfego do Gateway de Aplicativo
description: Este artigo fornece orientação para configurar o Gateway de Aplicativo do Azure para dar suporte a cenários de alto volume de tráfego de rede.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: caya
ms.openlocfilehash: a5f7569fc46d4678ca0c12299e33caa3c78df849
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/26/2020
ms.locfileid: "96182913"
---
# <a name="application-gateway-high-traffic-support"></a>Suporte a alto tráfego do Gateway de Aplicativo

>[!NOTE]
> Este artigo descreve algumas diretrizes sugeridas para ajudá-lo a configurar o gateway de aplicativo para lidar com tráfego extra para qualquer volume de tráfego alto que possa ocorrer. Os limites de alerta são puramente sugestões e genéricos por natureza. Os usuários podem determinar os limites de alerta com base em suas expectativas de carga de trabalho e utilização.

Você pode usar o Gateway de Aplicativo com o WAF (firewall do aplicativo Web) para um modo escalonável e seguro de gerenciar o tráfego para seus aplicativos Web.

É importante que você dimensione seu gateway de aplicativo de acordo com seu tráfego e com um pouco de buffer para que você esteja preparado para qualquer sobretensão de tráfego ou picos e minimizando o impacto que ele pode ter em seu QoS. As sugestões a seguir ajudam a configurar o Gateway de Aplicativo com o WAF para lidar com o tráfego extra.

Verifique a [documentação de métricas](./application-gateway-metrics.md) para obter a lista completa de métricas oferecidas pelo gateway de aplicativo. Consulte [Visualizar métricas](./application-gateway-metrics.md#metrics-visualization) no portal do Azure e na [documentação do Azure monitor](../azure-monitor/platform/alerts-metric.md) sobre como definir alertas para métricas.

## <a name="scaling-for-application-gateway-v1-sku-standardwaf-sku"></a>Dimensionamento para SKU do gateway de aplicativo V1 (SKU Standard/WAF)

### <a name="set-your-instance-count-based-on-your-peak-cpu-usage"></a>Definir sua contagem de instâncias com base no pico de uso da CPU
Se você estiver usando um gateway de SKU v1, terá a capacidade de definir seu gateway de aplicativo até 32 instâncias para dimensionamento. Verifique a utilização da CPU do gateway de aplicativo no passado um mês para obter os picos acima de 80%, ele está disponível como uma métrica para você monitorar. É recomendável que você defina a contagem de instâncias de acordo com seu uso de pico e com um buffer de 10% a 20% adicional para levar em conta os picos de tráfego.

:::image type="content" source="./media/application-gateway-covid-guidelines/v1-cpu-utilization-inline.png" alt-text="Métricas de utilização de CPU v1" lightbox="./media/application-gateway-covid-guidelines/v1-cpu-utilization-exp.png":::

### <a name="use-the-v2-sku-over-v1-for-its-autoscaling-capabilities-and-performance-benefits"></a>Use o SKU v2 em vez da v1 para funcionalidades de dimensionamento automático e benefícios de desempenho
O SKU v2 oferece dimensionamento automático para garantir que o Gateway de Aplicativo possa escalar verticalmente conforme o tráfego aumentar. Ela também oferece outros benefícios de desempenho significativos, como desempenho de descarregamento de TLS cinco vezes melhor, tempos de atualização e implantação mais rápidos, redundância de zona e muito mais em comparação com a v1. Para obter mais informações, consulte nossa [documentação de v2](./application-gateway-autoscaling-zone-redundant.md) e veja nossa documentação de [migração](./migrate-v1-v2.md) v1 para v2 para saber como migrar seus gateways de SKU v1 existentes para o SKU v2. 

## <a name="autoscaling-for-application-gateway-v2-sku-standard_v2waf_v2-sku"></a>Dimensionamento automático para SKU do gateway de aplicativo v2 (SKU Standard_v2/WAF_v2)

### <a name="set-maximum-instance-count-to-the-maximum-possible-125"></a>Definir a contagem máxima de instâncias para o máximo possível (125)
 
Para o SKU do gateway de aplicativo v2, definir a contagem máxima de instâncias para o valor máximo possível de 125 permite que o gateway de aplicativo seja expandido conforme necessário. Isso permite que ele manipule o possível aumento no tráfego para seus aplicativos. Você será cobrado apenas pelas CUs (unidades de capacidade) que usar. 

Certifique-se de verificar o tamanho da sub-rede e a contagem de endereços IP disponíveis em sua sub-rede e defina sua contagem máxima de instâncias com base nela. Se a sua sub-rede não tiver espaço suficiente para acomodar, você precisará recriar o gateway na mesma sub-rede ou em outra que tenha capacidade suficiente. 

:::image type="content" source="./media/application-gateway-covid-guidelines/v2-autoscaling-max-instances-inline.png" alt-text="V2 configuração de dimensionamento automático" lightbox="./media/application-gateway-covid-guidelines/v2-autoscaling-max-instances-exp.png":::

### <a name="set-your-minimum-instance-count-based-on-your-average-compute-unit-usage"></a>Definir a contagem mínima de instâncias com base no uso médio da unidade de computação

Para o SKU do gateway de aplicativo v2, o dimensionamento automático leva de seis a sete minutos para escalar horizontalmente e provisionar um conjunto adicional de instâncias prontas para tomar o tráfego. Até lá, se houver picos curtos no tráfego, suas instâncias de gateway existentes poderão ficar sob estresse e isso poderá causar latência inesperada ou perda de tráfego. 

É recomendável que você defina a contagem mínima de instâncias para um nível ideal. Por exemplo, se você precisar de 50 instâncias para lidar com o tráfego no pico de carga, definir o mínimo de 25 a 30 é uma boa ideia em vez de <10 para que, mesmo quando houver picos de tráfego, o gateway de aplicativo seja capaz de tratá-lo e dar tempo suficiente para que o dimensionamento automático responda e entre em vigor.

Verifique a métrica da unidade de computação para o último mês. A métrica da unidade de computação é uma representação da utilização da CPU do gateway e com base no seu uso máximo dividido por 10, você pode definir o número mínimo de instâncias necessárias. Observe que 1 instância do gateway de aplicativo pode manipular um mínimo de 10 unidades de computação

:::image type="content" source="./media/application-gateway-covid-guidelines/compute-unit-metrics-inline.png" alt-text="Métricas da unidade de computação v2" lightbox="./media/application-gateway-covid-guidelines/compute-unit-metrics-exp.png":::

## <a name="manual-scaling-for-application-gateway-v2-sku-standard_v2waf_v2"></a>Dimensionamento manual para SKU do gateway de aplicativo v2 (Standard_v2/WAF_v2)

### <a name="set-your-instance-count-based-on-your-peak-compute-unit-usage"></a>Definir sua contagem de instâncias com base em seu uso de unidade de computação de pico 

Ao contrário do dimensionamento automático, no dimensionamento manual, você deve definir manualmente o número de instâncias do seu gateway de aplicativo com base nos requisitos de tráfego. É recomendável que você defina a contagem de instâncias de acordo com seu uso de pico e com um buffer de 10% a 20% adicional para levar em conta os picos de tráfego. Por exemplo, se o tráfego exigir 50 instâncias no pico, provisione 55 para 60 instâncias para lidar com picos de tráfego inesperados que podem ocorrer.

Verifique a métrica da unidade de computação para o último mês. A métrica da unidade de computação é uma representação da utilização da CPU do seu gateway e com base no seu uso máximo dividido por 10, você pode definir o número de instâncias necessárias, uma vez que 1 instância do gateway de aplicativo pode manipular um mínimo de 10 unidades de computação

## <a name="monitoring-and-alerting"></a>Monitoramento e alertas

Para ser notificado de qualquer anomalia de tráfego ou de utilização, você pode configurar alertas em determinadas métricas. Consulte a [documentação de métricas](./application-gateway-metrics.md) para obter a lista completa de métricas oferecidas pelo gateway de aplicativo. Consulte [Visualizar métricas](./application-gateway-metrics.md#metrics-visualization) no portal do Azure e na [documentação do Azure monitor](../azure-monitor/platform/alerts-metric.md) sobre como definir alertas para métricas.

## <a name="alerts-for-application-gateway-v1-sku-standardwaf"></a>Alertas para SKU do gateway de aplicativo V1 (Standard/WAF)

### <a name="alert-if-average-cpu-utilization-crosses-80"></a>Alertar se a utilização média da CPU ultrapassar 80%

Sob condições normais, o uso da CPU não deveria exceder 90%, pois isso pode causar latência nos sites hospedados atrás do gateway de aplicativo e comprometer a experiência do cliente. Você pode controlar indiretamente ou melhorar a utilização da CPU modificando a configuração do gateway de aplicativo aumentando a contagem de instâncias ou movendo para um tamanho de SKU maior ou fazendo ambos. Definir um alerta se a métrica de utilização da CPU ficar acima de 80% da média.

### <a name="alert-if-unhealthy-host-count-crosses-threshold"></a>Alertar se a contagem de hosts não íntegros cruzar o limite

Essa métrica indica o número de servidores back-end que o gateway de aplicativo não consegue investigar com êxito. Isso detectará problemas em que as instâncias do gateway de aplicativo não conseguem se conectar ao back-end. Alertar se esse número for superior a 20% da capacidade de back-end. Por ex.: se, no momento, você tiver 30 servidores de back-end em seu pool de back-end, defina um alerta se a contagem de hosts não íntegros for acima de 6.

### <a name="alert-if-response-status-4xx-5xx-crosses-threshold"></a>Alertar se o status da resposta (4xx, 5xx) cruzar o limite 

Criar alerta quando o status de resposta do gateway de aplicativo for 4xx ou 5xx. Pode haver uma resposta ocasional de 4xx ou 5xx vista devido a problemas transitórios. Você deve observar o gateway em produção para determinar o limite estático ou usar o limite dinâmico para o alerta.

### <a name="alert-if-failed-requests-crosses-threshold"></a>Alertar se as solicitações com falha ultrapassarem o limite 

Criar alerta quando a métrica de solicitações com falha cruzar o limite. Você deve observar o gateway em produção para determinar o limite estático ou usar o limite dinâmico para o alerta.

### <a name="example-setting-up-an-alert-for-more-than-100-failed-requests-in-the-last-5-minutes"></a>Exemplo: Configurando um alerta para mais de 100 solicitações com falha nos últimos 5 minutos

Este exemplo mostra como usar o portal do Azure para configurar um alerta quando a contagem de solicitações com falha nos últimos 5 minutos for maior que 100.
1. Navegue até o **Gateway de Aplicativo**.
2. No painel esquerdo, selecione **Métricas** na guia **Monitoramento**. 
3. Adicione uma métrica para **solicitações com falha**.
4. Clique em **nova regra de alerta** e defina sua condição e ações
5. Clique em **criar regra de alerta** para criar e habilitar o alerta

:::image type="content" source="./media/application-gateway-covid-guidelines/create-alerts-inline.png" alt-text="V2 criar alertas" lightbox="./media/application-gateway-covid-guidelines/create-alerts-exp.png":::

## <a name="alerts-for-application-gateway-v2-sku-standard_v2waf_v2"></a>Alertas para SKU do gateway de aplicativo v2 (Standard_v2/WAF_v2)

### <a name="alert-if-compute-unit-utilization-crosses-75-of-average-usage"></a>Alertar se a utilização da unidade de computação cruzar 75% do uso médio 

Unidade de computação é a medida de utilização de computação do seu gateway de aplicativo. Verifique o uso da unidade de computação média no último mês e defina alerta se ele exceder 75% de ti. Por exemplo, se seu uso médio for 10 unidades de computação, defina um alerta em 7,5 CUs. Isso alertará se o uso estiver aumentando e dará tempo para você responder. Você pode aumentar o mínimo se considerar que esse tráfego será mantido para alertar que o tráfego pode estar aumentando. Siga as sugestões de dimensionamento acima para escalar horizontalmente conforme necessário.

### <a name="example-setting-up-an-alert-on-75-of-average-cu-usage"></a>Exemplo: como definir um alerta em 75% do uso médio de CU

Este exemplo mostra como usar o portal do Azure para configurar um alerta quando 75% do uso médio de CU é atingido. 
1. Navegue até o **Gateway de Aplicativo**.
2. No painel esquerdo, selecione **Métricas** na guia **Monitoramento**. 
3. Adicione uma métrica para a **média de unidades de computação atuais**. 
4. Se você tiver definido a contagem mínima de instâncias como seu uso médio de CU, prossiga e defina um alerta quando 75% de suas instâncias mínimas estiverem em uso. Por exemplo, se o uso médio for 10 CUs, defina um alerta em 7,5 CUs. Isso alertará se o uso estiver aumentando e dará tempo para você responder. Você pode aumentar o mínimo se considerar que esse tráfego será mantido para alertar que o tráfego pode estar aumentando. 

:::image type="content" source="./media/application-gateway-covid-guidelines/compute-unit-alert-inline.png" alt-text="V2 alertas de unidade de computação" lightbox="./media/application-gateway-covid-guidelines/compute-unit-alert-exp.png":::

> [!NOTE]
> Você pode definir que o alerta ocorra em um percentual de utilização de CU mais baixa ou mais alta, dependendo da sensibilidade que você deseja para os picos de tráfego potenciais.

### <a name="alert-if-capacity-unit-utilization-crosses-75-of-peak-usage"></a>Alertar se a utilização da unidade de capacidade cruzar 75% do pico de uso 

As unidades de capacidade representam a utilização geral do gateway em termos de taxa de transferência, computação e contagem de conexões. Verifique o uso da unidade de capacidade máxima no último mês e defina alerta se ele exceder 75% de ti. Por exemplo, se seu uso máximo for de 100 unidades de capacidade, defina um alerta em 75 CUs. Siga as duas sugestões acima para escalar horizontalmente, conforme necessário.

### <a name="alert-if-unhealthy-host-count-crosses-threshold"></a>Alertar se a contagem de hosts não íntegros cruzar o limite 

Essa métrica indica o número de servidores back-end que o gateway de aplicativo não consegue investigar com êxito. Isso detectará problemas em que as instâncias do gateway de aplicativo não conseguem se conectar ao back-end. Alertar se esse número for superior a 20% da capacidade de back-end. Por ex.: se, no momento, você tiver 30 servidores de back-end em seu pool de back-end, defina um alerta se a contagem de hosts não íntegros for acima de 6.

### <a name="alert-if-response-status-4xx-5xx-crosses-threshold"></a>Alertar se o status da resposta (4xx, 5xx) cruzar o limite 

Criar alerta quando o status de resposta do gateway de aplicativo for 4xx ou 5xx. Pode haver uma resposta ocasional de 4xx ou 5xx vista devido a problemas transitórios. Você deve observar o gateway em produção para determinar o limite estático ou usar o limite dinâmico para o alerta.

### <a name="alert-if-failed-requests-crosses-threshold"></a>Alertar se as solicitações com falha ultrapassarem o limite 

Criar alerta quando a métrica de solicitações com falha cruzar o limite. Você deve observar o gateway em produção para determinar o limite estático ou usar o limite dinâmico para o alerta.

### <a name="alert-if-backend-last-byte-response-time-crosses-threshold"></a>Alerta se o tempo de resposta do último byte de back-end cruzar o limite 

Essa métrica indica o intervalo de tempo entre o início do estabelecimento de uma conexão com o servidor de back-end e o recebimento do último byte do corpo da resposta. Crie um alerta se a latência de resposta de back-end for mais específica do que um limite normal. Por exemplo, defina isso para ser alertado quando a latência de resposta de back-end aumentar em mais de 30% do valor usual.

### <a name="alert-if-application-gateway-total-time-crosses-threshold"></a>Alertar se o tempo total do gateway de aplicativo ultrapassar o limite

Esse é o intervalo a partir do momento em que o gateway de aplicativo recebe o primeiro byte da solicitação HTTP até a hora em que o último byte de resposta foi enviado ao cliente. Deve criar um alerta se a latência de resposta de back-end for mais específica do que um limite normal. Por exemplo, eles podem definir isso para ser alertado quando a latência de tempo total aumenta em mais de 30% do valor usual.

## <a name="set-up-waf-with-geo-filtering-and-bot-protection-to-stop-attacks"></a>Configurar o WAF com a filtragem geográfica e a proteção de bot para interromper os ataques
Se você quiser uma camada extra de segurança na frente do seu aplicativo, use o SKU do WAF_v2 do Gateway de Aplicativo para funcionalidades de WAF. Você pode configurar o SKU v2 para permitir somente o acesso aos seus aplicativos de um determinado país/região ou países/regiões. Você configura uma regra personalizada WAF para permitir ou bloquear explicitamente o tráfego com base na localização geográfica. Para obter mais informações, consulte [regras personalizadas de filtragem geográfica](../web-application-firewall/ag/geomatch-custom-rules.md) e [como configurar regras personalizadas no Gateway de aplicativo WAF_v2 SKU por meio do PowerShell](../web-application-firewall/ag/configure-waf-custom-rules.md).

Habilite a proteção contra bots para bloquear bots defeituosos conhecidos. Isso deve reduzir a quantidade de tráfego que chegará ao seu aplicativo. Para obter mais informações, confira [Proteção contra bots com instruções de configuração](../web-application-firewall/ag/configure-waf-custom-rules.md).

## <a name="turn-on-diagnostics-on-application-gateway-and-waf"></a>Ativar diagnósticos no Gateway de Aplicativo e no WAF

Os logs de diagnóstico permitem ver logs de firewall, logs de desempenho e logs de acesso. Você pode usar esses logs no Azure para gerenciar e solucionar problemas de Gateways de Aplicativo. Para obter mais informações, confira a [documentação de diagnóstico](./application-gateway-diagnostics.md#diagnostic-logging). 

## <a name="set-up-an-tls-policy-for-extra-security"></a>Configurar uma política de TLS para segurança extra
Verifique se você está usando a versão mais recente da política TLS ([AppGwSslPolicy20170401S](./application-gateway-ssl-policy-overview.md#appgwsslpolicy20170401s)). Isso impõe o TLS 1.2 e codificações mais fortes. Para saber mais, confira [Como configurar versões de política TLS e conjuntos de codificação via PowerShell](./application-gateway-configure-ssl-policy-powershell.md).