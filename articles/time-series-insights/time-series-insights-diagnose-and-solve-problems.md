---
title: Diagnostique, solucione problemas e resolva problemas-Azure Time Series Insights
description: Este artigo descreve como diagnosticar, solucionar problemas e resolver problemas comuns em seu ambiente de Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.reviewer: v-mamcge
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 09/29/2020
ms.custom: seodec18
ms.openlocfilehash: 15f2ff5aaa1d731c13125d0a3ab4ac32acb9276c
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95023268"
---
# <a name="diagnose-and-solve-issues-in-your-azure-time-series-insights-gen1-environment"></a>Diagnosticar e resolver problemas em seu ambiente de Azure Time Series Insights Gen1

> [!CAUTION]
> Esse é um artigo do Gen1.

Este artigo descreve os problemas que você pode encontrar em seu ambiente de Azure Time Series Insights. O artigo fornece possíveis causas e soluções para resolução.

## <a name="video"></a>Vídeo

### <a name="learn-about-common-azure-time-series-insights-challenges-and-mitigationsbr"></a>Saiba mais sobre os desafios e as mitigações comuns do Azure Time Series Insights</br>

> [!VIDEO https://www.youtube.com/embed/7U0SwxAVSKw]

## <a name="problem-no-data-is-shown"></a>Problema: nenhum dado é mostrado

Se nenhum dado estiver aparecendo no [Azure Time Series insights Explorer](https://insights.timeseries.azure.com), considere essas causas comuns.

### <a name="cause-a-event-source-data-isnt-in-json-format"></a>Causa: os dados de origem do evento não estão no formato JSON

O Azure Time Series Insights dá suporte somente a dados JSON. Para obter exemplos do JSON, consulte [Formas de JSON com suporte](./concepts-json-flattening-escaping-rules.md).

### <a name="cause-b-the-event-source-key-is-missing-a-required-permission"></a>Causa B: a chave de origem do evento não tem uma permissão necessária

* Para um hub IoT no Hub IoT do Azure, você precisa fornecer a chave que tem permissões de conexão de serviço. Selecione o **iothubowner** ou a política de **serviço** . Ambos têm permissões de conexão de serviço.

   [![Permissões de conexão de serviço do Hub IoT](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png)](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png#lightbox)

* Para um hub de eventos nos hubs de eventos do Azure, você precisa fornecer a chave que tem permissões de escuta. As políticas de **leitura** e **Gerenciamento** funcionarão porque ambas têm permissões de escuta.

   [![Permissão de escuta do hub de eventos](media/diagnose-and-solve-problems/eventhub-listen-permissions.png)](media/diagnose-and-solve-problems/eventhub-listen-permissions.png#lightbox)

### <a name="cause-c-the-provided-consumer-group-isnt-exclusive-to-azure-time-series-insights"></a>Causa C: o grupo de consumidores fornecido não é exclusivo para Azure Time Series Insights

Quando você registra um hub IoT ou um hub de eventos, é importante definir o grupo de consumidores que deseja usar para ler os dados. Esse grupo de consumidores *não pode ser compartilhado*. Se o grupo de consumidores for compartilhado, o hub IoT ou o hub de eventos subjacente desconectará de forma automática e aleatória um dos leitores. Forneça um grupo de consumidores exclusivo para Azure Time Series Insights ler.

### <a name="cause-d-the-environment-has-just-been-provisioned"></a>Causa D: o ambiente acabou de ser provisionado

Os dados aparecerão em seu Azure Time Series Insights Explorer dentro de alguns minutos depois que o ambiente e seus dados forem criados primeiro.

## <a name="problem-some-data-is-shown-but-data-is-missing"></a>Problema: alguns dados são mostrados, mas os dados estão ausentes

Quando os dados aparecem apenas parcialmente e os dados parecem estar atrasados, considere esses possíveis problemas.

### <a name="cause-a-your-environment-is-being-throttled"></a>Causa um: seu ambiente está sendo limitado

A [limitação](time-series-insights-environment-mitigate-latency.md) é um problema comum quando os ambientes são provisionados depois que você cria uma origem de evento com dados. Os Hubs de Eventos do Azure e o Hub IoT do Azure armazenam dados por até sete dias. Azure Time Series Insights sempre inicia com o evento mais antigo na origem do evento (primeiro a entrar, primeiro a sair ou *FIFO*).

Por exemplo, se você tiver 5 milhões eventos em uma origem de evento quando você se conectar a um ambiente de Azure Time Series Insights de unidade única S1, Azure Time Series Insights lerá aproximadamente 1 milhão eventos por dia. Pode parecer que Azure Time Series Insights está experimentando cinco dias de latência. Mas o que acontece é que o ambiente está sendo limitado.

Se houver eventos antigos na origem do evento, você poderá lidar com a limitação de duas maneiras:

* Altere os limites de retenção da origem do evento para ajudar a remover eventos antigos que você não deseja exibir no Azure Time Series Insights.
* Provisionar um tamanho de ambiente maior (número de unidades) para aumentar a taxa de transferência de eventos antigos. No exemplo anterior, se você aumentar o mesmo ambiente S1 para cinco unidades por um dia, o ambiente deverá ser acumulado em um dia. Se a produção de eventos de estado estacionário for 1 milhão ou menos eventos por dia, você poderá reduzir a capacidade do evento para uma unidade depois que Azure Time Series Insights for atualizado.

O limite de limitação imposto baseia-se na capacidade e no tipo de SKU do ambiente. Todas as origens do evento no ambiente compartilham essa capacidade. Se a origem do evento para o Hub IoT ou o Hub de eventos enviar dados para além dos limites impostos, você terá limitação e um retardo.

A figura a seguir mostra um ambiente Azure Time Series Insights que tem uma SKU de S1 e uma capacidade de 3. Ele pode ingressar 3 milhões de eventos por dia.

[![Capacidade do ambiente](media/diagnose-and-solve-problems/environment-sku-current-capacity.png)](media/diagnose-and-solve-problems/environment-sku-current-capacity.png#lightbox)

Imagine um ambiente que ingere mensagens de um hub de eventos. Ele tem uma taxa de entrada diária de cerca de 67.000 mensagens. Essa taxa é equivalente a aproximadamente 46 mensagens por minuto.

* Se cada mensagem do hub de eventos for achatada para um único evento de Azure Time Series Insights, a limitação não ocorrerá.
* Se cada mensagem do hub de eventos for achatada para 100 Azure Time Series Insights eventos, 4.600 os eventos devem ser ingeridos a cada minuto.

Um ambiente de SKU S1 que tem uma capacidade de 3 pode ingressar apenas 2.100 eventos por minuto (1 milhão de eventos por dia = 700 eventos por minuto em 3 unidades = 2.100 eventos por minuto).

Para obter uma compreensão de alto nível de como funciona a lógica de mesclagem, consulte [formas de JSON com suporte](./concepts-json-flattening-escaping-rules.md).

#### <a name="recommended-resolutions-for-excessive-throttling"></a>Resolução recomendadas para limitação excessiva

Para corrigir o retardo, aumente a capacidade do SKU do ambiente. Para obter mais informações, leia [dimensionar seu ambiente de Azure Time Series insights](time-series-insights-how-to-scale-your-environment.md).

### <a name="cause-b-initial-ingestion-of-historical-data-slows-ingress"></a>Causa B: a ingestão inicial de dados históricos torna a entrada mais lenta

Se você se conectar a uma origem do evento existente, provavelmente o hub IoT ou o hub de eventos já conterá dados. O ambiente inicia o pull dos dados desde o início do período de retenção de mensagens da origem do evento. Esse processamento padrão não pode ser substituído. Você pode acionar a limitação. A limitação pode levar algum tempo para ficar atualizada conforme ela consome os dados históricos.

#### <a name="recommended-resolutions-for-large-initial-ingestion"></a>Soluções recomendadas de ingestão inicial grande

Para corrigir o retardo:

1. aumente a capacidade do SKU para o valor máximo permitido (10, neste caso). Após você aumentar a capacidade, o processo de entrada começará a se atualizar muito mais rapidamente. Você é cobrado pela maior capacidade. Para visualizar a rapidez com que você está se capturando, você pode exibir o gráfico de disponibilidade no [Azure Time Series insights Explorer](https://insights.timeseries.azure.com).

2. Depois que o retardo for atualizado, diminua a capacidade do SKU para a taxa de entrada normal.

## <a name="problem-data-was-showing-previously-but-is-no-longer-showing"></a>Problema: os dados estavam sendo mostrados anteriormente, mas não estão mais sendo mostrados

Se Azure Time Series Insights não estiver mais ingerindo dados, mas os eventos ainda estiverem transmitindo para o Hub IOT ou Hub de eventos, considere essa possível causa.

### <a name="cause-a-your-hub-access-key-was-regenerated-and-your-environment-needs-to-be-updated"></a>Causa: a chave de acesso do Hub foi regenerada e seu ambiente precisa ser atualizado

Esse problema ocorre quando a chave fornecida quando você criou a origem do evento não é mais válida. Você veria a telemetria em seu hub, mas nenhuma mensagem de entrada recebeu em Azure Time Series Insights. Se você não tiver certeza se a chave foi regenerada, poderá pesquisar o log de atividades do seu hub de eventos para "criar ou atualizar regras de autorização de namespace". Para um hub IoT, pesquise "criar ou atualizar o recurso IotHub".

Para atualizar seu ambiente de Azure Time Series Insights com a nova chave, abra o recurso de Hub no portal do Azure e copie a nova chave. Vá para o recurso Azure Time Series Insights e selecione **origens do evento**:

   [![Selecionar origens do evento](media/diagnose-and-solve-problems/update-hub-key-step-1.png)](media/diagnose-and-solve-problems/update-hub-key-step-1.png#lightbox)

Selecione a origem do evento ou as fontes das quais a ingestão foi interrompida, Cole a nova chave e, em seguida, selecione **salvar**:

   [![Colar na nova chave](media/diagnose-and-solve-problems/update-hub-key-step-2.png)](media/diagnose-and-solve-problems/update-hub-key-step-2.png#lightbox)

## <a name="problem-the-event-sources-timestamp-property-name-setting-doesnt-work"></a>Problema: a configuração do nome da propriedade Timestamp da origem do evento não funciona

Verifique se o valor da propriedade Timestamp que vem da origem do evento como uma cadeia de caracteres JSON está no formato _aaaa-mm-ddThh: mm: SS. FFFFFFFK_. Aqui está um exemplo: **2008-04-12T12:53Z**.

Tenha em mente que o nome da propriedade Timestamp diferencia maiúsculas de minúsculas.

A maneira mais fácil de garantir que o nome da propriedade de carimbo de data/hora seja capturado e funcionando corretamente é usar o Azure Time Series Insights Explorer. No Azure Time Series Insights Explorer, usando o gráfico, selecione um período de tempo depois de inserir o nome da propriedade de carimbo de data/hora. Clique com o botão direito do mouse na seleção e selecione **explorar eventos**.

O cabeçalho da primeira coluna deve ser o nome da propriedade de carimbo de data/hora. Ao lado do **carimbo de data/hora** do Word, **($TS)** será exibido.

Os seguintes valores não serão exibidos:

* *(ABC)*: indica que Azure Time Series insights está lendo os valores de dados como cadeias de caracteres.
* *Ícone de calendário*: indica que Azure Time Series insights está lendo os valores de dados como valores de data e hora.
* *#*: Indica que Azure Time Series Insights está lendo os valores de dados como inteiros.

## <a name="next-steps"></a>Próximas etapas

* Leia sobre [como mitigar a latência no Azure Time Series insights](time-series-insights-environment-mitigate-latency.md).

* Saiba [como dimensionar seu ambiente de Azure Time Series insights](time-series-insights-how-to-scale-your-environment.md).