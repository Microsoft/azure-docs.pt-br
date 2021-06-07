---
title: Diagnosticar e solucionar problemas de um ambiente Gen2-Azure Time Series Insights | Microsoft Docs
description: Saiba como diagnosticar e solucionar problemas de um ambiente de Azure Time Series Insights Gen2.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/01/2020
ms.custom: seodec18
ms.openlocfilehash: d9dd07e3a35d83ff6bd9c7c493768d1197667c39
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98108782"
---
# <a name="diagnose-and-troubleshoot-an-azure-time-series-insights-gen2-environment"></a>Diagnosticar e solucionar problemas de um ambiente de Azure Time Series Insights Gen2

Este artigo resume vários problemas comuns que você pode encontrar ao trabalhar com o ambiente do Azure Time Series Insights Gen2. O artigo também descreve possíveis causas e soluções para cada problema.

## <a name="problem-i-cant-find-my-environment-in-the-gen2-explorer"></a>Problema: não consigo encontrar meu ambiente no Gen2 Explorer

Esse problema pode ocorrer se você não tiver permissões para acessar o ambiente de Time Series Insights. Os usuários precisam de uma função de acesso de nível de leitura para exibir o ambiente do Time Series Insights. Para verificar os níveis de acesso atuais e conceder acesso adicional, acesse a seção **políticas de acesso a dados** no recurso de Time Series Insights no [portal do Azure](https://portal.azure.com/).

  [![Verifique as políticas de acesso a dados.](media/preview-troubleshoot/verify-data-access-policies.png)](media/preview-troubleshoot/verify-data-access-policies.png#lightbox)

## <a name="problem-no-data-is-seen-in-the-gen2-explorer"></a>Problema: nenhum dado é visto no Gerenciador de Gen2

Há vários motivos comuns pelos quais os dados podem não aparecer no [Azure Time Series insights Gen2 Explorer](https://insights.timeseries.azure.com/preview).

- Pode ser que a origem do evento não esteja recebendo dados.

    Verifique se a origem do evento, que é um hub de eventos ou um hub IoT, está recebendo dados de suas marcas ou instâncias. Para verificar, acesse a página de visão geral do recurso no portal do Azure.

    [![Examine a visão geral das métricas do painel.](media/preview-troubleshoot/verify-dashboard-metrics.png)](media/preview-troubleshoot/verify-dashboard-metrics.png#lightbox)

- Os dados de origem do evento não estão no formato JSON.

    O Time Series Insights é compatível somente com os dados JSON. Para exemplos de JSON, leia [formas de JSON com suporte](./concepts-json-flattening-escaping-rules.md).

- A chave da origem do evento não tem uma permissão necessária.

  - Para o Hub IoT, você precisa fornecer a chave com a permissão de **conexão de serviço**.

    [![Verifique as permissões do Hub IoT.](media/preview-troubleshoot/verify-correct-permissions.png)](media/preview-troubleshoot/verify-correct-permissions.png#lightbox)

    - As políticas **iothubowner** e **serviço** funcionam porque têm permissão de **conexão de serviço** .

  - Para o hub de eventos, você precisa fornecer a chave que tem a permissão de **Escuta**.
  
    [![Examine as permissões do hub de eventos.](media/preview-troubleshoot/verify-eh-permissions.png)](media/preview-troubleshoot/verify-eh-permissions.png#lightbox)

    - As políticas de **leitura** e de **Gerenciamento** funcionam porque têm permissão de **escuta** .

- O grupo de consumidores fornecido não é exclusivo do Time Series Insights.

    Durante o registro de um hub IoT ou hub de eventos, você especifica o grupo de consumidores que é usado para ler os dados. Esse grupo de consumidores deve ser exclusivo por ambiente. Se o grupo de consumidores for compartilhado, o hub de eventos subjacente desconectará automaticamente um dos leitores de forma aleatória. Forneça um grupo de consumidores exclusivo para o Time Series Insights para leitura.

- A propriedade de ID do Time Series especificada no momento do provisionamento está incorreta, ausente ou nula.

    Esse problema poderá ocorrer se a propriedade de ID do Time Series for configurada incorretamente no momento do provisionamento do ambiente. Para obter mais informações, leia [as práticas recomendadas para escolher uma ID de série temporal](./how-to-select-tsid.md). Neste momento, você não pode atualizar um ambiente existente do Time Series Insights para usar uma ID do Time Series diferente.

## <a name="problem-some-data-shows-but-some-is-missing"></a>Problema: alguns dados são mostrados, mas alguns estão ausentes

Pode ser que você esteja enviando dados sem a ID do Time Series.

- Esse problema pode ocorrer quando você envia eventos sem o campo de ID do Time Series no conteúdo. Para obter mais informações, leia [formas de JSON com suporte](./concepts-json-flattening-escaping-rules.md).
- Esse problema pode ocorrer porque o seu ambiente está sendo limitado.

    > [!NOTE]
    > Neste momento, Time Series Insights dá suporte a uma taxa máxima de ingestão de 1 Mbps.

## <a name="problem-data-was-showing-but-now-ingestion-has-stopped"></a>Problema: os dados estavam sendo mostrados, mas agora a ingestão foi interrompida

- A chave de origem do evento pode ter sido regenerada e seu ambiente Gen2 precisa da nova chave de origem do evento.

Esse problema ocorre quando a chave fornecida ao criar a origem do evento não é mais válida. Você veria a telemetria em seu hub, mas nenhuma mensagem de entrada recebeu em Time Series Insights. Se não tiver certeza de que a chave foi regenerada, você poderá pesquisar o log de atividades de seus hubs de eventos para obter "criar ou atualizar as regras de autorização de namespace" ou Pesquisar "criar ou atualizar recurso IotHub" para o Hub IoT.

Para atualizar o ambiente do Time Series Insights Gen2 com a nova chave, abra o recurso de Hub no portal do Azure e copie a nova chave. Navegue até o recurso TSI e clique em fontes de evento.

   [![Captura de tela mostra o recurso I S com o item de menu de origens de evento chamado.](media/preview-troubleshoot/update-hub-key-step-1.png)](media/preview-troubleshoot/update-hub-key-step-1.png#lightbox)

Selecione a origem (s) do evento que tem da qual a ingestão foi interrompida, Cole a nova chave e clique em salvar.

   [![A captura de tela mostra o recurso T I com a chave de política de Hub I T inserida.](media/preview-troubleshoot/update-hub-key-step-2.png)](media/preview-troubleshoot/update-hub-key-step-2.png#lightbox)

## <a name="problem-my-event-sources-timestamp-property-name-doesnt-work"></a>Problema: o nome da propriedade de carimbo de data/hora da origem do evento não funciona

Verifique se o nome e o valor estão em conformidade com as seguintes regras:

- O nome da propriedade Carimbo de data/hora diferencia maiúsculas de minúsculas.
- O valor da propriedade Timestamp que vem da origem do evento como uma cadeia de caracteres JSON tem o formato `yyyy-MM-ddTHH:mm:ss.FFFFFFFK` . Um exemplo dessa cadeia de caracteres é `"2008-04-12T12:53Z"`.

A maneira mais fácil de garantir que seu nome de propriedade de carimbo de data/hora seja capturado e funcionando corretamente é usar o Time Series Insights Explorer Gen2. Dentro do Time Series Insights Gen2 Explorer, use o gráfico para selecionar um período de tempo depois de fornecer o nome da propriedade de carimbo de data/hora. Clique com o botão direito do mouse na seleção e escolha a opção **explorar eventos**. O primeiro cabeçalho de coluna é o nome da propriedade Carimbo de data/hora. Ele deve ter `($ts)` ao lado da palavra `Timestamp`, em vez de:

- `(abc)`, que indica que o Time Series Insights lê os valores de dados como cadeias de caracteres.
- O ícone de **calendário** , que indica que Time Series insights lê o valor de dados como DateTime.
- `#`, que indica que o Time Series Insights lê os valores de dados como um inteiro.

Se a propriedade timestamp não for especificada explicitamente, o Hub IoT do evento ou o tempo de enfileiramento do hub de eventos será usado como carimbo de data/hora padrão.

## <a name="problem-i-cant-view-data-from-my-warm-store-in-the-explorer"></a>Problema: não consigo exibir dados de minha loja a quente no Gerenciador

- Você pode ter provisionado sua loja passiva recentemente e os dados ainda estão fluindo.
- Você pode ter excluído sua loja passiva, caso em que você teria perdido os dados.

## <a name="problem-i-cant-view-or-edit-my-time-series-model"></a>Problema: não consigo exibir ou editar meu modelo de série temporal

- Talvez você esteja acessando um ambiente S1 ou S2 do Time Series Insights.

   Os modelos de série temporal têm suporte apenas em ambientes pagos conforme o uso. Para obter mais informações sobre como acessar seu ambiente S1 ou S2 no Time Series Insights Gen2 Explorer, leia [Visualizar dados no Gerenciador](./concepts-ux-panels.md).

   [![Nenhum evento no ambiente.](media/preview-troubleshoot/troubleshoot-no-events.png)](media/preview-troubleshoot/troubleshoot-no-events.png#lightbox)

- Talvez você não tenha permissões para exibir e editar o modelo.

   Os usuários precisam de acesso de nível de colaborador para editar e exibir o modelo do Time Series. Para verificar os níveis de acesso atuais e conceder acesso adicional, acesse a seção **políticas de acesso a dados** em seu recurso de Time Series Insights no portal do Azure.

## <a name="problem-all-my-instances-in-the-gen2-explorer-lack-a-parent"></a>Problema: todas as minhas instâncias no Gen2 Explorer não têm um pai

Esse problema pode ocorrer se o seu ambiente não tiver uma hierarquia de modelo de série temporal definida. Para obter mais informações, leia sobre como [trabalhar com modelos de série temporal](./time-series-insights-overview.md).

  [![As instâncias não pais exibirão um aviso.](media/preview-troubleshoot/unparented-instances.png)](media/preview-troubleshoot/unparented-instances.png#lightbox)

## <a name="next-steps"></a>Próximas etapas

- Leia sobre como [trabalhar com modelos de série temporal](./time-series-insights-overview.md).

- Saiba mais sobre [as formas JSON com suporte](./concepts-json-flattening-escaping-rules.md).

- Examine o [planejamento e os limites](./how-to-plan-your-environment.md) no Azure Time Series insights Gen2.