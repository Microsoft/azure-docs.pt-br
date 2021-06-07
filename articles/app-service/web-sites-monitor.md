---
title: Monitorar aplicativos
description: Saiba como monitorar aplicativos no Serviço de Aplicativo do Azure usando o portal do Azure. Entenda as cotas e as métricas que são relatadas.
author: btardif
ms.assetid: d273da4e-07de-48e0-b99d-4020d84a425e
ms.topic: article
ms.date: 04/23/2020
ms.author: byvinyal
ms.custom: seodec18
ms.openlocfilehash: bf230032afe80680dc392c2a74da2a5aef381983
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "100586093"
---
# <a name="monitor-apps-in-azure-app-service"></a>Monitorar aplicativos no Serviço de Aplicativo do Azure
O [serviço de Azure app](./overview.md) fornece funcionalidade de monitoramento interna para aplicativos Web, móveis e aplicativos de API no [portal do Azure](https://portal.azure.com).

No portal do Azure, você pode examinar *cotas* e *métricas* para um aplicativo e um plano do serviço de aplicativo e configurar *alertas* e métricas baseadas em regras de *dimensionamento* automático.

## <a name="understand-quotas"></a>Entender cotas

Aplicativos hospedados no Serviço de Aplicativo estão sujeitos a determinados limites de uso de recursos. Os limites são definidos pelo plano do Serviço de Aplicativo associado ao aplicativo.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Se o aplicativo estiver hospedado em um plano *Gratuito* ou *Compartilhado*, os limites de uso dos recursos por parte do aplicativo são definidos por cotas.

Se o aplicativo estiver hospedado em um plano *Básico*, *Standard* ou *Premium*, os limites de uso dos recursos são definidos pelo *tamanho* (Pequeno, Médio, Grande) e *contagem de instâncias* (1, 2, 3...) do Plano do Serviço de Aplicativo.

As cotas para aplicativos Gratuitos ou Compartilhados são:

| Quota | Descrição |
| --- | --- |
| **CPU (Curto)** | A quantidade de CPU permitida para esse aplicativo em um intervalo de cinco minutos. Essa cota é definida novamente a cada cinco minutos. |
| **CPU (Dia)** | A quantidade total de CPU permitida para esse aplicativo em um dia. Essa cota é definida novamente a cada 24 horas, à meia-noite UTC. |
| **Memória** | A quantidade total de memória permitida para esse aplicativo. |
| **Largura de banda** | A quantidade total de largura de banda de saída permitida para esse aplicativo em um dia. Essa cota é definida novamente a cada 24 horas, à meia-noite UTC. |
| **WPD** | A quantidade total de armazenamento permitida. |

A única cota aplicável a aplicativos hospedados em *Basic*, *Standard* e *Premium* é FileSystem.

Para saber mais sobre cotas, limites e recursos específicos disponíveis para os vários SKUs de Serviço de Aplicativo, confira [Limites do serviço de assinatura do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits).

### <a name="quota-enforcement"></a>Aplicação de cota

Se um aplicativo exceder a cota *CPU (Curto)*, *CPU (Dia)* ou *largura de banda*, o aplicativo será interrompido até que a cota seja redefinida. Durante esse tempo, todas as solicitações de entrada resultarão em um erro HTTP 403.

![Mensagem de erro 403][http403]

Se a cota de memória do aplicativo for excedida, o aplicativo será interrompido temporariamente.

Se a cota de Sistema de arquivos for excedida, qualquer operação de gravação falhará. Falhas em operações de gravação incluem gravações em logs.

Aumente ou remova cotas de seu aplicativo atualizando seu Plano do Serviço de Aplicativo.

## <a name="understand-metrics"></a>Entender as métricas

> [!NOTE]
> O **uso do sistema de arquivos** é uma nova métrica sendo distribuída globalmente, nenhum dado é esperado, a menos que seu aplicativo esteja hospedado em um ambiente do serviço de aplicativo.
> 

> [!IMPORTANT]
> O **tempo médio de resposta** será preterido para evitar confusão com agregações de métricas. Use o **tempo de resposta** como uma substituição.

> [!NOTE]
> As métricas para um aplicativo incluem as solicitações para o site do SCM do aplicativo (kudu).  Isso inclui solicitações para exibir o logstream do site usando kudu.  As solicitações Logstream podem abranger vários minutos, o que afetará as métricas de tempo de solicitação.  Os usuários devem estar cientes dessa relação ao usar essas métricas com a lógica de dimensionamento automático.
> 

Métricas fornecem informações sobre o aplicativo ou sobre o comportamento do Plano do Serviço de Aplicativo.

Para um aplicativo, as métricas disponíveis são:

| Métrica | Descrição |
| --- | --- |
| **Tempo de resposta** | O tempo necessário para o aplicativo atender solicitações, em segundos. |
| **Tempo médio de resposta (preterido)** | O tempo médio necessário para o aplicativo atender solicitações, em segundos. |
| **Conjunto de trabalho de memória média** | A quantidade média de memória usada pelo aplicativo em megabytes (MiB). |
| **Conexões** | O número de soquetes associados existentes na área restrita (w3wp.exe e seus processos filho).  Um soquete associado é criado chamando APIs bind()/connect() e permanece até que seja fechado com CloseHandle()/closesocket(). |
| **Tempo de CPU** | A quantidade de CPU consumida pelo aplicativo em segundos. Para obter mais informações sobre essa métrica, consulte [CPU time vs CPU Percentage](#cpu-time-vs-cpu-percentage). |
| **Assemblies Atuais** | O número atual de Assemblies carregados em todos os AppDomains nesse aplicativo. |
| **Dados em** | A quantidade de largura de banda de entrada consumida pelo aplicativo em MiB. |
| **Saída de dados** | A quantidade de largura de banda de saída consumida pelo aplicativo em MiB. |
| **Uso do sistema de arquivos** | A quantidade de uso em bytes por compartilhamento de armazenamento. |
| **Coletas de lixo da Ger 0** | O número de vezes que os objetos da geração 0 são coletados como lixo desde o início do processo do aplicativo. As coletas de lixo de geração superior incluem todas as coletas da geração inferior.|
| **Coletas de lixo da Ger 1** | O número de vezes que os objetos da geração 1 são coletados como lixo desde o início do processo do aplicativo. As coletas de lixo de geração superior incluem todas as coletas da geração inferior.|
| **Coletas de lixo da Ger 2** | O número de vezes que os objetos da geração 2 são coletados como lixo desde o início do processo do aplicativo.|
| **Núm. de Identificadores** | O número total de identificadores atualmente abertos pelo processo do aplicativo.|
| **Status da verificação de integridade** | O status de integridade médio entre as instâncias do aplicativo no plano do serviço de aplicativo.|
| **Http 2xx** | A contagem de solicitações que resultam em um código de status HTTP ≥ 200, mas < 300. |
| **Http 3xx** | A contagem de solicitações que resultam em um código de status HTTP ≥ 300, mas < 400. |
| **Http 401** | A contagem de solicitações que resultam em um código de status HTTP 401. |
| **Http 403** | A contagem de solicitações que resultam em um código de status HTTP 403. |
| **Http 404** | A contagem de solicitações que resultam em um código de status HTTP 404. |
| **Http 406** | A contagem de solicitações que resultam em um código de status HTTP 406. |
| **4xx http** | A contagem de solicitações que resultam em um código de status HTTP ≥ 400, mas < 500. |
| **Erros do servidor http** | A contagem de solicitações que resultam em um código de status HTTP ≥ 500, mas < 600. |
| **E/S de outros bytes por segundo** | A taxa na qual o processo do aplicativo está emitindo bytes para operações de e/s que não envolvem dados, como operações de controle.|
| **E/S de outras operações por segundo** | A taxa na qual o processo do aplicativo está emitindo operações de e/s que não são operações de leitura ou gravação.|
| **E/S de bytes de leitura por segundo** | A taxa na qual o processo do aplicativo está lendo bytes das operações de E/S.|
| **E/S de operações de leitura por segundo** | A taxa na qual o processo do aplicativo está gerando operações de E/S de leitura.|
| **E/S de bytes de gravação por segundo** | A taxa na qual o processo do aplicativo está gravando bytes nas operações de E/S.|
| **E/S de operações de gravação por segundo** | A taxa na qual o processo do aplicativo está gerando operações de E/S de gravação.|
| **Conjunto de trabalho de memória** | A quantidade atual de memória usada pelo aplicativo em MiB. |
| **Bytes Particulares** | Bytes privados é o tamanho atual, em bytes, da memória que o processo do aplicativo alocou que não pode ser compartilhado com outros processos.|
| **Solicitações** | O número total de solicitações, independentemente de seu código de status HTTP resultante. |
| **Solicitações na fila do aplicativo** | O número de solicitações na fila de solicitação do aplicativo.|
| **Contagem de Threads** | O número de threads atualmente ativos no processo do aplicativo.|
| **Total de domínios de aplicativo** | O número atual de AppDomains carregados nesse aplicativo.|
| **Total de domínios de aplicativo descarregados** | O número total de AppDomains descarregados desde o início do aplicativo.|


Para um Plano do Serviço de Aplicativo, as métricas disponíveis são:

> [!NOTE]
> As métricas do Plano do Serviço de Aplicativo só estão disponíveis para os planos *Básico*, *Standard* e *Premium*.
> 

| Métrica | Descrição |
| --- | --- |
| **Percentual de CPU** | A média de CPU usada em todas as instâncias do plano. |
| **Porcentagem de memória** | A média de memória usada em todas as instâncias do plano. |
| **Dados em** | A média de largura de banda de entrada usada em todas as instâncias do plano. |
| **Saída de dados** | A média de largura de banda de saída usada em todas as instâncias do plano. |
| **Comprimento da fila de disco** | O número médio de solicitações de leitura e gravação enfileiradas no armazenamento. Um comprimento de fila de disco alto é uma indicação de um aplicativo que pode estar causando lentidão devido à e/s excessiva de disco. |
| **Comprimento da fila http** | O número médio de solicitações HTTP que tiveram de esperar na fila antes de serem atendidas. Um tamanho de fila HTTP alto ou crescente é um sintoma de um plano sob carga pesada. |

### <a name="cpu-time-vs-cpu-percentage"></a>Tempo de CPU versus porcentagem de CPU
<!-- To do: Fix Anchor (#CPU-time-vs.-CPU-percentage) -->

Há duas métricas que refletem o uso da CPU:

**Tempo de CPU**: útil para aplicativos hospedados em planos gratuitos ou compartilhados, pois uma de suas cotas é definida em minutos de CPU usados pelo aplicativo.

**Percentual de CPU**: útil para aplicativos hospedados nos planos básico, Standard e Premium, pois eles podem ser escalados horizontalmente. A porcentagem de CPU é uma boa indicação do uso geral em todas as instâncias.

## <a name="metrics-granularity-and-retention-policy"></a>Granularidade de métricas e política de retenção
As métricas para um aplicativo e um plano do serviço de aplicativo são registradas e agregadas pelo serviço e [mantidas de acordo com essas regras](../azure-monitor/essentials/data-platform-metrics.md#retention-of-metrics).

## <a name="monitoring-quotas-and-metrics-in-the-azure-portal"></a>Monitoramento de cotas e métricas no portal do Azure
Para examinar o status das várias cotas e métricas que afetam um aplicativo, acesse o [portal do Azure](https://portal.azure.com).

![Gráfico de cotas no portal do Azure][quotas]

Para localizar cotas, selecione **configurações**  >  **cotas**. No gráfico, você pode ver: 
1. O nome da cota.
1. O intervalo de redefinição.
1. O limite atual.
1. O valor atual.

![Gráfico de métrica no portal do Azure ][metrics] você pode acessar as métricas diretamente da página **visão geral** do recurso. Aqui você verá gráficos que representam algumas das métricas de aplicativos.

Clicar em qualquer um desses gráficos levará você para a exibição de métricas, na qual você pode criar gráficos personalizados, consultar diferentes métricas e muito mais. 

Para saber mais sobre as métricas, confira [Monitorar as métricas do serviço](../azure-monitor/data-platform.md).

## <a name="alerts-and-autoscale"></a>Alertas e dimensionamento automático
As métricas para um aplicativo ou Plano do Serviço de Aplicativo podem ser vinculadas a alertas. Para obter mais informações, consulte [receber notificações de alerta](../azure-monitor/alerts/alerts-classic-portal.md).

Os aplicativos do serviço de aplicativo hospedados em planos do serviço de aplicativo básico ou superior dão suporte a autoescala. Com o dimensionamento automático, você pode configurar regras que monitoram as métricas do Plano do Serviço de Aplicativo. As regras podem aumentar ou diminuir a contagem de instâncias, o que pode fornecer recursos adicionais, conforme o necessário. As regras também podem ajudar você a economizar dinheiro quando o aplicativo for excessivamente provisionado.

Para saber mais sobre o dimensionamento automático, confira [Como dimensionar](../azure-monitor/autoscale/autoscale-get-started.md) e [Melhores práticas para o dimensionamento automático do Azure Monitor](../azure-monitor/autoscale/autoscale-best-practices.md).

[fzilla]:https://go.microsoft.com/fwlink/?LinkId=247914
[vmsizes]:https://go.microsoft.com/fwlink/?LinkID=309169

<!-- Images. -->
[http403]: ./media/web-sites-monitor/http403.png
[quotas]: ./media/web-sites-monitor/quotas.png
[metrics]: ./media/web-sites-monitor/metrics.png
