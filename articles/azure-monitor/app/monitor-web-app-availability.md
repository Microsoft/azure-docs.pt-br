---
title: Monitorar a disponibilidade e a capacidade de resposta de qualquer site da Web | Microsoft Docs
description: Configure testes da web no Application Insights. Obtenha alertas se um site fica indisponível ou responde lentamente.
ms.topic: conceptual
ms.date: 03/10/2021
ms.reviewer: sdash
ms.openlocfilehash: d7c610e374dcb7b97850d815ba8bb927cdebacfc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103012557"
---
# <a name="monitor-the-availability-of-any-website"></a>Monitorar a disponibilidade de qualquer site

Depois de implantar seu aplicativo Web/site, você pode configurar testes recorrentes para monitorar a disponibilidade e a capacidade de resposta. [Application Insights do Azure](./app-insights-overview.md) envia solicitações da Web ao aplicativo em intervalos regulares de pontos no mundo todo. Ele poderá alertá-lo se seu aplicativo não estiver respondendo ou se responder muito devagar.

Você pode configurar testes de disponibilidade para qualquer ponto de extremidade HTTP ou HTTPS que for acessível da Internet pública. Você não precisa fazer nenhuma alteração no site que está testando. Na verdade, nem precisa ser um site que você possui. Você pode testar a disponibilidade de uma API REST da qual seu serviço depende.

### <a name="types-of-availability-tests"></a>Tipos de testes de disponibilidade:

Há três tipos de testes de disponibilidade:

* [Teste de ping de URL](#create-a-url-ping-test): um teste simples que você pode criar no Portal do Azure.
* [Teste na Web de várias etapas](availability-multistep.md): uma gravação de uma sequência de solicitações da Web, que pode ser reproduzida para testar cenários mais complexos. Os testes na Web de várias etapas são criados no Visual Studio Enterprise e carregados no portal para execução.
* [Testes de disponibilidade de acompanhamento personalizado](/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability): se você decidir criar um aplicativo personalizado para executar testes de disponibilidade, o `TrackAvailability()` método poderá ser usado para enviar os resultados para Application insights.

**Você pode criar até 100 testes de disponibilidade por recurso de Application Insights.**

> [!IMPORTANT]
> Ambos, o [teste de ping de URL](#create-a-url-ping-test) e o [teste na Web de várias etapas](availability-multistep.md) dependem da infraestrutura de DNS da Internet pública para resolver os nomes de domínio dos pontos de extremidade testados. Isso significa que, se você estiver usando DNS privado, deverá garantir que todos os nomes de domínio do seu teste também sejam resolvidos pelos servidores de nome de domínio público ou, quando não for possível, você poderá usar os [testes de disponibilidade de rastreamento personalizado](/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability) em vez disso.

## <a name="create-an-application-insights-resource"></a>Criar um recurso do Application Insights

Para criar um teste de disponibilidade, primeiro você precisa criar um recurso de Application Insights. Se você já tiver criado um recurso, vá para a próxima seção para [criar um teste de ping de URL](#create-a-url-ping-test).

No portal do Azure, selecione **criar um recurso**  >  **ferramentas para desenvolvedores**  >  **Application insights** e [criar um recurso de Application insights](create-new-resource.md).

## <a name="create-a-url-ping-test"></a>Criar um teste de ping de URL

O nome "teste de ping de URL" é um pouco de um nome. Para ser claro, esse teste não faz uso do ICMP (protocolo de mensagem de controle da Internet) para verificar a disponibilidade do site. Em vez disso, ele usa a funcionalidade de solicitação HTTP mais avançada para validar se um ponto de extremidade está respondendo. Ele também mede o desempenho associado a essa resposta e adiciona a capacidade de definir critérios de êxito personalizados acoplados a recursos mais avançados, como a análise de solicitações dependentes, além de permitir novas tentativas.

Para criar sua primeira solicitação de disponibilidade, abra o painel disponibilidade e selecione **criar teste**.

![Preencha pelo menos o URL do seu site](./media/monitor-web-app-availability/availability-create-test-001.png)

### <a name="create-a-test"></a>Criar um teste

|Configuração| Explicação
|----|----|----|
|**URL** |  A URL pode ser qualquer página da web que você deseja testar, mas ela deve estar visível na Internet pública. A URL pode incluir uma cadeia de consulta. Por exemplo, você pode utilizar um pouco seu banco de dados. Se a URL for resolvida para um redirecionamento, nós a seguiremos, até um máximo de 10 redirecionamentos.|
|**Analisar solicitações dependentes**| Solicitações de teste imagens, scripts, arquivos de estilo e outros arquivos que fazem parte da página da Web em teste. O tempo de resposta gravado inclui o tempo necessário para obter esses arquivos. O teste falhará se qualquer um desses recursos não puder ser baixado com êxito dentro do tempo limite para o teste inteiro. Se a opção não estiver marcada, o teste solicitará apenas o arquivo na URL especificada. A habilitação dessa opção resulta em uma verificação mais estrita. O teste pode falhar para casos, o que pode não ser perceptível ao navegar manualmente no site.
|**Habilitar novas tentativas**|Quando o teste falha, ele é repetido após um curto intervalo. Uma falha só será relatada se três tentativas sucessivas falharem. Testes subsequentes são então executados com a frequência de teste normal. A repetição é suspensa temporariamente até o próximo sucesso. Essa regra é aplicada independentemente em cada local de teste. **Recomendamos essa opção**. Em média, aproximadamente 80% das falhas desaparecem na repetição.|
|**Frequência de teste**| define a frequência com que o teste é executado em cada localização de teste. Com uma frequência padrão de cinco minutos e cinco locais de teste, seu site é testado em média a cada minuto.|
|**Locais de teste**| São os locais de onde nossos servidores enviam solicitações da Web para a sua URL. **O número mínimo de locais de teste recomendado é cinco**, para garantir que você possa diferenciar problemas no seu site de problemas na rede. Você pode selecionar até 16 locais.

**Se a URL não estiver visível na Internet pública, você poderá optar por abrir seletivamente o firewall para permitir apenas as transações de teste**. Para saber mais sobre as exceções de firewall para nossos agentes de teste de disponibilidade, consulte o [Guia de endereço IP](./ip-addresses.md#availability-tests).

> [!NOTE]
> É altamente recomendável testar de vários locais com **um mínimo de cinco locais**. Isso é para evitar alarmes falsos que podem resultar de problemas temporários com um local específico. Além disso, descobrimos que a configuração ideal é ter o **número de locais de teste igual ao limite de local do alerta + 2**.

### <a name="success-criteria"></a>Critérios de êxito

|Configuração| Explicação
|----|----|----|
| **Tempo limite de teste** |diminua esse valor para ser alertado sobre respostas lentas. O teste é considerado uma falha se as respostas de seu site não são recebidas dentro desse período. Se você tiver selecionado **Analisar solicitações dependentes**, todas as imagens, arquivos de estilo, scripts e outros recursos dependentes devem ter sido recebidos dentro desse período.|
| **Resposta HTTP** | o código de status retornado que é contado como êxito. 200 é o código que indica que uma página da Web normal foi retornada.|
| **Correspondência de conteúdo** | Uma cadeia de caracteres como, por exemplo, “Bem-vindo!” Faremos o teste que uma correspondência exata de maiúsculas e minúsculas ocorre em todas as respostas. É necessário que seja uma cadeia de caracteres simples, sem curingas. Lembre-se de que se o conteúdo de sua página for alterado, talvez seja necessário atualizá-lo. **Somente caracteres da língua inglesa têm suporte na correspondência de conteúdo** |

### <a name="alerts"></a>Alertas

|Configuração| Explicação
|----|----|----|
|**Quase em tempo real (Versão prévia)** | É recomendável usar alertas quase em tempo real. A configuração desse tipo de alerta é feita após a criação do teste de disponibilidade.  |
|**Limite de locais de alerta**|é recomendável um mínimo de 3/5 locais. A relação ideal entre o limite de alertas locais e o número de locais de teste é o **limite de alertas locais** = **número de locais de teste - 2, com um mínimo de cinco locais de teste.**|

### <a name="location-population-tags"></a>Marcas de população de local

As seguintes marcas de população podem ser usadas para o atributo de localização geográfica ao implantar um teste de ping de URL de disponibilidade usando Azure Resource Manager.

#### <a name="azure-gov"></a>Gov do Azure

| Nome de exibição   | Nome da população     |
|----------------|---------------------|
| Gov. EUA – Virgínia | usgov-VA-AZR        |
| Gov. EUA – Arizona  | usgov-PHX-AZR       |
| Gov. EUA – Texas    | usgov-TX-AZR        |
| Leste do USDoD     | usgov-ddeast-AZR    |
| USDoD Central  | usgov-ddcentral-AZR |

#### <a name="azure"></a>Azure

| Nome de exibição                           | Nome da população   |
|----------------------------------------|-------------------|
| Leste da Austrália                         | EMEA-au-Syd-Edge  |
| Brazil South                           | lata de-br-Gru-Edge |
| Centro dos EUA                             | US-FL-Mia-Edge    |
| Leste da Ásia                              | Pacífico-HK-hkn-AZR   |
| Leste dos EUA                                | US-VA-Ash-AZR     |
| Sul da França (antiga França central) | EMEA-ch-ZRH-Edge  |
| França Central                         | EMEA-fr-pra-Edge  |
| Japan East                             | alta-JP-Kaw-borda  |
| Norte da Europa                           | EMEA-GB-DB3-AZR   |
| Centro-Norte dos EUA                       | US-Il-CH1-AZR     |
| Centro-Sul dos Estados Unidos                       | US-TX-SN1-AZR     |
| Sudeste Asiático                         | alta-SG-Sin-AZR   |
| Oeste do Reino Unido                                | EMEA-se-ARM-Edge  |
| Europa Ocidental                            | EMEA-nl-AMS-AZR   |
| Oeste dos EUA                                | US-CA-SJC-AZR     |
| Sul do Reino Unido                               | EMEA-ru-MSA-Edge  |

## <a name="see-your-availability-test-results"></a>Ver os resultados de teste de disponibilidade

Os resultados do teste de disponibilidade podem ser visualizados com exibições de gráfico de linha e dispersão.

Depois de alguns minutos, clique em **Atualizar** para ver os resultados do teste.

![Captura de tela mostra a página disponibilidade com o botão atualizar realçado.](./media/monitor-web-app-availability/availability-refresh-002.png)

A exibição dispersão mostra exemplos dos resultados de teste que têm detalhes da etapa de teste de diagnóstico neles. O mecanismo de teste armazena detalhes de diagnóstico para testes com falhas. Para testes bem-sucedidos, detalhes de diagnóstico são armazenados para um subconjunto das execuções. Passe o mouse sobre qualquer um dos pontos verdes/vermelhos para ver o teste, o nome do teste e o local.

![exibição Linha](./media/monitor-web-app-availability/availability-scatter-plot-003.png)

Selecione um teste específico e uma localização, ou reduza o período de tempo para ver mais resultados em todo o período de tempo de interesse. Use o Search Explorer para ver os resultados de todas as execuções ou usar consultas de análise para executar relatórios personalizados sobre esses dados.

## <a name="inspect-and-edit-tests"></a>Como inspecionar e editar testes

Para editar, desabilitar temporariamente ou excluir um teste, clique nas reticências ao lado de um nome de teste. Pode levar até 20 minutos para que as alterações de configuração sejam propagadas para todos os agentes de teste depois que uma alteração é feita.

![Exibir detalhes do teste. Editar e desabilitar um teste na Web](./media/monitor-web-app-availability/edit.png)

Você talvez queira desabilitar testes de disponibilidade ou as regras de alerta associadas a eles, enquanto você estiver fazendo a manutenção de seu serviço.

## <a name="if-you-see-failures"></a>Se você encontrar falhas

Clique em um ponto vermelho.

![Clique em um ponto vermelho](./media/monitor-web-app-availability/open-instance-3.png)

De um resultado do teste de disponibilidade, você pode ver os detalhes de transações em todos os componentes. Aqui, você pode ver:

* Inspecionar a resposta recebida do servidor.
* Diagnosticar falha na telemetria do lado do servidor correlacionado coletada durante o processamento do teste de disponibilidade com falha.
* Registrar um problema ou um item de trabalho no Git ou no Azure Boards para controlar o problema. O bug conterá um link para este evento.
* Abrir o resultado do teste na Web no Visual Studio.

Saiba mais sobre a experiência de diagnóstico de transação de ponta a ponta [aqui](./transaction-diagnostics.md).

Clique na linha de exceção para ver os detalhes da exceção do lado servidor que causou a falha no teste de disponibilidade sintético. Você também pode obter o [instantâneo de depuração](./snapshot-debugger.md) para diagnóstico mais rico em nível de código.

![Diagnóstico do lado do servidor](./media/monitor-web-app-availability/open-instance-4.png)

Além dos resultados brutos, você também pode exibir duas métricas de disponibilidade principais no [Metrics Explorer](../essentials/metrics-getting-started.md):

1. Disponibilidade: percentual dos testes que foram bem-sucedidos, em todas as execuções de teste.
2. Duração do teste: duração média em todas as execuções de teste.

## <a name="automation"></a>Automação

* [Use os scripts do PowerShell para configurar um teste de disponibilidade](./powershell.md#add-an-availability-test) automaticamente.
* Configure um [webhook](../alerts/alerts-webhooks.md) , que é chamado quando um alerta é gerado.

## <a name="troubleshooting"></a>Solução de problemas

[Artigo exclusivo para solução de problemas](troubleshoot-availability.md).

## <a name="next-steps"></a>Próximas etapas

* [Alertas de disponibilidade](availability-alerts.md)
* [Testes na Web com diversas etapas](availability-multistep.md)

