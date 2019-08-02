---
title: Monitorar a disponibilidade e a capacidade de resposta de qualquer site da Web | Microsoft Docs
description: Configure testes da web no Application Insights. Obtenha alertas se um site fica indisponível ou responde lentamente.
services: application-insights
author: mrbullwinkle
manager: carmonm
ms.assetid: 46dc13b4-eb2e-4142-a21c-94a156f760ee
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/19/2019
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: abe55007aa8a8719d6b6f1659e00a089a2e28771
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67303771"
---
# <a name="monitor-the-availability-of-any-website"></a>Monitore a disponibilidade de qualquer site

Depois que você implantou seu aplicativo/site da web, você pode configurar testes recorrentes para monitorar a disponibilidade e capacidade de resposta. [Application Insights do Azure](../../azure-monitor/app/app-insights-overview.md) envia solicitações da Web ao aplicativo em intervalos regulares de pontos no mundo todo. Ele pode alertá-lo se seu aplicativo não está respondendo, ou se ele responde muito lentamente.

Você pode configurar testes de disponibilidade para qualquer ponto de extremidade HTTP ou HTTPS que for acessível da Internet pública. Você não precisa fazer nenhuma alteração para o site que você está testando. Na verdade, até mesmo não precisa ser um site que você possui. Você pode testar a disponibilidade de uma API REST que depende de seu serviço.

### <a name="types-of-availability-tests"></a>Tipos de testes de disponibilidade:

Há três tipos de testes de disponibilidade:

* [Teste de ping de URL](#create-a-url-ping-test): um teste simples que você pode criar no Portal do Azure.
* [Teste da web de várias etapas](availability-multistep.md): Uma gravação de uma sequência de solicitações da web, que pode ser reproduzido para testar os cenários mais complexos. Testes da web de várias etapas são criados no Visual Studio Enterprise e carregados no portal para execução.
* [Testes de disponibilidade de controle personalizado](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability?view=azure-dotnet): O `TrackAvailability()` método pode ser usado para criar seus próprios testes personalizados de disponibilidade.

**Você pode criar até 100 testes de disponibilidade por recurso do Application Insights.**

## <a name="create-an-application-insights-resource"></a>Criar um recurso do Application Insights

Para criar um teste de disponibilidade, você precisa primeiro criar um recurso do Application Insights. Se você já tiver criado um recurso, vá para a próxima seção para [criar um teste de Ping de URL](#create-a-url-ping-test).

No portal do Azure, selecione **criar um recurso** > **ferramentas de desenvolvedor** > **Application Insights** e [criar um Recurso do Application Insights](create-new-resource.md).

## <a name="create-a-url-ping-test"></a>Criar um teste de ping de URL

O nome "ping teste de URL" é um pouco de um nome inapropriado. Para ser claro, esse teste não está fazendo a qualquer uso de ICMP (Internet Control Message Protocol) para verificar a disponibilidade do seu site. Em vez disso, ele usa a funcionalidade de solicitação HTTP mais avançada para validar se um ponto de extremidade está respondendo. Ele também mede o desempenho associado a essa resposta e adiciona a capacidade de definir os critérios de sucesso personalizado juntamente com recursos mais avançados, como analisar solicitações dependentes e permitindo novas tentativas.

Para criar sua primeira solicitação de disponibilidade, abra o painel de disponibilidade e selecione **Criar teste**.

![Preencha pelo menos o URL do seu site](./media/monitor-web-app-availability/availability-create-test-001.png)

### <a name="create-a-test"></a>Criar um teste

|Configuração| Explicação
|----|----|----|
|**URL** |  A URL pode ser qualquer página da web que você deseja testar, mas ele deve estar visível na internet pública. A URL pode incluir uma cadeia de consulta. Por exemplo, você pode utilizar um pouco seu banco de dados. Se a URL for resolvida para um redirecionamento, nós a seguiremos, até um máximo de 10 redirecionamentos.|
|**Analisar solicitações dependentes**| Teste solicitará imagens, scripts, arquivos de estilo e outros arquivos que fazem parte da página da web em teste. O tempo de resposta gravado inclui o tempo necessário para obter esses arquivos. O teste falhará se qualquer um desses recursos não podem ser baixados com êxito dentro do tempo limite para o teste inteiro. Se a opção não estiver marcada, o teste solicitará apenas o arquivo na URL especificada. Habilitar essa opção resulta em uma verificação mais rigorosa. O teste de possibilidade de falha de casos, o que podem não ser perceptíveis ao navegar manualmente o site.
|**Habilitar novas tentativas**|Quando o teste falhar, ele é repetido após um breve intervalo. Uma falha só será relatada se três tentativas sucessivas falharem. Testes subsequentes são então executados com a frequência de teste normal. A repetição é suspensa temporariamente até o próximo sucesso. Essa regra é aplicada independentemente em cada local de teste. **Recomendamos essa opção**. Em média, aproximadamente 80% das falhas desaparecem na repetição.|
|**Frequência de teste**| define a frequência com que o teste é executado em cada localização de teste. Com uma frequência padrão de cinco minutos e cinco locais de teste, seu site é testado em média a cada minuto.|
|**Locais de teste**| São os locais de onde os nossos servidores enviam solicitações da web para a URL. **Nosso número mínimo de locais teste recomendado é cinco** para garantir que você possa diferenciar problemas no seu site de problemas da rede. Você pode selecionar até 16 locais.

**Se a URL não estiver visível na internet pública, você pode optar por abrir seu firewall para permitir que somente as transações de teste por meio de seletivamente**. Para saber mais sobre as exceções de firewall para nossos agentes de teste de disponibilidade, consulte o [guia de endereço IP](https://docs.microsoft.com/azure/azure-monitor/app/ip-addresses#availability-tests).

> [!NOTE]
> É altamente recomendável testar vários locais com **um mínimo de cinco locais**. Isso é para evitar alarmes falsos que podem resultar de problemas temporários com um local específico. Além disso descobrimos que a configuração ideal é ter o **número de locais de teste ser igual ao limite de alerta local + 2**.

### <a name="success-criteria"></a>Critérios de sucesso

|Configuração| Explicação
|----|----|----|
| **Tempo limite de teste** |diminua esse valor para ser alertado sobre respostas lentas. O teste é considerado uma falha se as respostas de seu site não são recebidas dentro desse período. Se você tiver selecionado **Analisar solicitações dependentes**, todas as imagens, arquivos de estilo, scripts e outros recursos dependentes devem ter sido recebidos dentro desse período.|
| **Resposta HTTP** | o código de status retornado que é contado como êxito. 200 é o código que indica que uma página da Web normal foi retornada.|
| **Correspondência de conteúdo** | Uma cadeia de caracteres como "Bem-vindo!" Faremos o teste que uma correspondência exata de maiúsculas e minúsculas ocorre em todas as respostas. É necessário que seja uma cadeia de caracteres simples, sem curingas. Lembre-se de que se o conteúdo de sua página for alterado, talvez seja necessário atualizá-lo. **Somente caracteres em inglês têm suporte com correspondência de conteúdo** |

### <a name="alerts"></a>Alertas

|Configuração| Explicação
|----|----|----|
|**Tempo quase real (versão prévia)** | É recomendável usar alertas quase em tempo real. Configurar esse tipo de alerta é feito depois que o teste de disponibilidade é criado.  |
|**Clássico** | Não recomendamos usando alertas clássicos para novos testes de disponibilidade.|
|**Limite de alerta local**|é recomendável um mínimo de 3/5 locais. É a relação ideal entre o limite de alerta local e o número de locais de teste **limite de alerta local** = **número de locais de teste - 2, com um mínimo de cinco locais de teste.**|

## <a name="see-your-availability-test-results"></a>Ver os resultados de teste de disponibilidade

Resultados de teste de disponibilidade podem ser visualizados com modos de exibição de gráfico de linha ou dispersão.

Depois de alguns minutos, clique em **Refresh** para ver os resultados do teste.

![Exibição de linha](./media/monitor-web-app-availability/availability-refresh-002.png)

A exibição de dispersão mostra exemplos dos resultados de teste que têm detalhes de etapa de teste de diagnóstico. O mecanismo de teste armazena detalhes de diagnóstico para testes com falhas. Para testes bem-sucedidos, detalhes de diagnóstico são armazenados para um subconjunto das execuções. Passe o mouse sobre qualquer um dos pontos verde/vermelho para ver o teste, nome e local de teste.

![Exibição de linha](./media/monitor-web-app-availability/availability-scatter-plot-003.png)

Selecione um teste específico e uma localização, ou reduza o período de tempo para ver mais resultados em todo o período de tempo de interesse. Use o Search Explorer para ver os resultados de todas as execuções ou usar consultas de análise para executar relatórios personalizados sobre esses dados.

## <a name="inspect-and-edit-tests"></a>Inspecionar e editar testes

Para editar, desabilitar temporariamente ou excluir um teste de clique nas reticências ao lado de um nome de teste. Ele pode levar até 20 minutos para que as alterações de configuração sejam propagadas para todos os agentes de teste depois que uma alteração é feita.

![Exibir detalhes do teste. Editar e desabilitar um teste da web](./media/monitor-web-app-availability/edit.png)

Você talvez queira desabilitar testes de disponibilidade ou as regras de alerta associadas a eles, enquanto você estiver fazendo a manutenção de seu serviço.

## <a name="if-you-see-failures"></a>Se você encontrar falhas

Clique em um ponto vermelho.

![Clique em um ponto vermelho](./media/monitor-web-app-availability/open-instance-3.png)

De um resultado do teste de disponibilidade, você pode ver os detalhes de transações em todos os componentes. Aqui, você pode ver:

* Inspecionar a resposta recebida do servidor.
* Diagnosticar falha na telemetria do lado do servidor correlacionado coletada durante o processamento do teste de disponibilidade com falha.
* Registrar um problema ou um item de trabalho no Git ou no Azure Boards para controlar o problema. O bug conterá um link para este evento.
* Abrir o resultado do teste na Web no Visual Studio.

Saiba mais sobre a experiência de diagnóstico de transação de ponta a ponta [aqui](../../azure-monitor/app/transaction-diagnostics.md).

Clique na linha de exceção para ver os detalhes da exceção do lado servidor que causou a falha no teste de disponibilidade sintético. Você também pode obter o [instantâneo de depuração](../../azure-monitor/app/snapshot-debugger.md) para diagnóstico mais rico em nível de código.

![Diagnóstico do lado do servidor](./media/monitor-web-app-availability/open-instance-4.png)

Além dos resultados brutos, você também pode exibir os dois principais métricas de disponibilidade no [Metrics Explorer](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started):

1. Disponibilidade: percentual dos testes que foram bem-sucedidos, em todas as execuções de teste.
2. Duração do teste: duração média em todas as execuções de teste.

## <a name="automation"></a>Automação

* [Use os scripts do PowerShell para configurar um teste de disponibilidade](../../azure-monitor/app/powershell.md#add-an-availability-test) automaticamente.
* Configure um [webhook](../../azure-monitor/platform/alerts-webhooks.md) , que é chamado quando um alerta é gerado.

## <a name="troubleshooting"></a>solução de problemas

Dedicado [artigo de solução de problemas](troubleshoot-availability.md).

## <a name="next-steps"></a>Próximas etapas

* [Alertas de disponibilidade](availability-alerts.md)
* [Testes da web de várias etapas](availability-multistep.md)


