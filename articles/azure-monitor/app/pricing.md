---
title: Gerenciar o uso e os custos do Azure Application Insights | Microsoft Docs
description: Gerencie volumes de telemetria e monitore custos no Application Insights.
services: application-insights
documentationcenter: ''
author: DaleKoetke
manager: carmonm
ms.assetid: ebd0d843-4780-4ff3-bc68-932aa44185f6
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 05/29/2019
ms.author: dalek
ms.openlocfilehash: 188911fa3fb872c8db1612406bf3d4e9b36d837b
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67303832"
---
# <a name="manage-usage-and-costs-for-application-insights"></a>Gerenciar o uso e os custos do Application Insights

> [!NOTE]
> Este artigo descreve como analisar o uso de dados Insights de aplicativos.  Consulte os artigos a seguir para informações relacionadas.
> - [Monitorando o uso e os custos estimados](../../monitoring-and-diagnostics/monitoring-usage-and-estimated-costs.md) descreve como visualizar o uso e os custos estimados em vários recursos de monitoramento do Azure para diferentes modelos de preços. Também descreve como alterar seu modelo de preços.

Se tiver dúvidas sobre como os preços são aplicados ao Application Insights, você poderá postar uma pergunta em nosso [fórum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=ApplicationInsights).

## <a name="pricing-model"></a>Modelo de preços

Os preços para [Azure Application Insights][start] baseia-se no volume de dados ingerido. Cada recurso do Application Insights é cobrado como um serviço separado e contribui para a cobrança da sua assinatura do Azure.

### <a name="data-volume-details"></a>Detalhes do volume de dados

* O volume de dados é o número de bytes de telemetria recebidos pelo Application Insights. O volume de dados é medido como o tamanho do pacote de dados JSON descompactado recebido pelo Application Insights do seu aplicativo. Para [dados tabulares importados para o Analytics](https://docs.microsoft.com/azure/application-insights/app-insights-analytics-import), o volume de dados é medido como o tamanho descompactado de arquivos enviados ao Application Insights.
* Seus encargos de volume de dados do seu aplicativo agora são relatados em um novo medidor de cobrança denominado **Ingestão de Dados** a partir de abril de 2018. Esse novo medidor é compartilhado entre tecnologias, como aplicativos de Insights e Log Analytics de monitoramento e está atualmente sob o nome do serviço **do Log Analytics**. 
* Os dados de [Live Metrics Stream](../../azure-monitor/app/live-stream.md) não são contatos para fins de preços.

> [!NOTE]
> Por exemplo, todos os preços exibidos nas capturas de tela neste artigo são apenas para fins. Para preços atuais em sua moeda e região, consulte [Preços do Application Insights][pricing].

### <a name="multi-step-web-tests"></a>Testes na Web com diversas etapas

Há uma cobrança adicional para [testes na Web de várias etapas](../../azure-monitor/app/availability-multistep.md). Testes na Web de várias etapas se referem a testes na Web que executam uma sequência de ações.

Não há nenhuma cobrança separada para *testes de ping* de uma única página. A telemetria de testes de ping e de testes de várias etapas é cobrada da mesma forma que outras telemetrias do seu aplicativo.

## <a name="review-usage-and-estimate-costs"></a>Revisar uso e estimar custos

O Application Insights facilita a compreensão de quais são seus custos com base nos padrões de uso recentes. Para começar, no portal do Azure, do recurso Application Insights, acesse a página **Uso e custos estimados**:

![Escolher preços](./media/pricing/pricing-001.png)

a. Examine o volume de dados do mês. Isso inclui todos os dados recebidos e mantidos (após qualquer [amostragem](../../azure-monitor/app/sampling.md)) de seu servidor e aplicativos cliente e dos testes de disponibilidade.  
B. Uma cobrança separada é feita pelos [testes na Web de várias etapas](../../azure-monitor/app/availability-multistep.md). (Isso não inclui testes de disponibilidade simples, que são incluídos na cobrança de volume de dados).  
C. Visualize as tendências do volume de dados do último mês.  
D. Habilite a [amostragem](../../azure-monitor/app/sampling.md) de ingestão de dados.   
E. Configure o limite de volume de dados diários.  

Para investigar mais profundamente o uso do Application Insights, abra a página **Métricas**, adicione a métrica nomeada "Volume do ponto de dados" e selecione a opção *Aplicar divisão* para dividir os dados por "Tipo de item de telemetria". 

Encargos do Application Insights são adicionados à sua conta do Azure. Você pode ver os detalhes da sua conta do Azure na seção de **Cobrança** do Portal do Azure ou no [portal de cobrança do Azure](https://account.windowsazure.com/Subscriptions). 

![No menu à esquerda, selecione Cobrança](./media/pricing/02-billing.png)

## <a name="data-rate"></a>Taxa de dados
O volume de dados enviados é limitado de três formas:

* **Amostragem**: Você pode usar a amostragem para reduzir o volume de telemetria enviado do seu servidor e de aplicativos cliente, com mínima distorção de métricas. Amostragem é a ferramenta principal que você pode usar para ajustar a quantidade de dados enviados. Saiba mais sobre [recursos de amostragem](../../azure-monitor/app/sampling.md). 
* **Limite diário**: Quando você cria um recurso do Application Insights no portal do Microsoft Azure, o limite diário é definido como 100 GB/dia. Quando você cria um recurso do Application Insights no Visual Studio, o padrão é pequeno (somente 32,3 MB/dia). O padrão de limite diário é definido para facilitar o teste. O propósito dele é que o usuário irá gerar o limite diário antes de colocar o aplicativo em produção. 

    O limite máximo é 1.000 GB/dia, a menos que você solicite um máximo maior para um aplicativo de alto tráfego. 

    Tome cuidado ao definir o limite diário. A intenção deve ser *nunca atingir o limite diário*. Se atingir o limite diário, você perderá os dados para o restante do dia e não poderá monitorar seu aplicativo. Para alterar o limite diário, use a opção **Limite de volume diário**. Você pode acessar essa opção no painel **Uso e custos estimados** (isso está descrito em mais detalhes mais adiante neste artigo).
    Removemos a restrição de alguns tipos de assinatura com crédito que não pôde ser usado no Application Insights. Anteriormente, se a assinatura tivesse um limite de gastos, a caixa de diálogo de limite diário teria instruções sobre como remover esse limite e permitir que ele fosse aumentado para mais de 32,3 MB/dia.
* **Limitação**: Esta opção limita a taxa de dados para 32.000 eventos por segundo, com média de 1 minuto por chave de instrumentação.

*O que acontece se o aplicativo exceder a taxa de limitação?*

* O volume de dados que seu aplicativo envia é avaliado a cada minuto. Se ele exceder a taxa por segundo média por minuto, o servidor recusa algumas solicitações. O SDK armazena em buffer os dados e, em seguida, tenta enviá-los novamente. Ele espalha um surto por vários minutos. Se o seu aplicativo enviar dados acima da taxa de limitação constantemente, alguns dados serão descartados. (Os SDKs do ASP.NET, Java e JavaScript tentam reenviar dados dessa maneira; outros SDKs poderão simplesmente descartar dados limitados.) Caso ocorra uma limitação, um aviso de notificação o alertará de que isso ocorreu.

*Como posso saber quantos dados meu aplicativo está enviando?*

Você pode usar uma das opções a seguir para ver a quantidade de dados sendo enviada pelo seu aplicativo:

* Vá para o painel **Uso e custos estimados** para ver o gráfico de volume de dados diário. 
* No Metrics Explorer, adicione um novo gráfico. Para a métrica do gráfico, selecione **Volume de ponto de dados**. Ative o **Agrupamento** e agrupe por **Tipo de dados**.

## <a name="reduce-your-data-rate"></a>Reduza sua taxa de dados
Veja abaixo o que é possível fazer para reduzir o volume de dados:

* Use a [Amostragem](../../azure-monitor/app/sampling.md). Essa tecnologia reduz a taxa de dados sem distorcer sua métrica. Você não perderá a capacidade de navegar entre os itens relacionados em Pesquisar. Em aplicativos de servidor, a amostragem funciona automaticamente.
* [Limite o número de chamadas do Ajax que podem ser informadas](../../azure-monitor/app/javascript.md#detailed-configuration) em cada modo de exibição de página ou desative o relatório de Ajax.
* [Edite Applicationinsights](../../azure-monitor/app/configuration-with-applicationinsights-config.md) para desativar os módulos de coleção desnecessários. Por exemplo, você pode decidir que os contadores de desempenho ou dados de dependência não são essenciais.
* Divida a telemetria em chaves de instrumentação separadas. 
* Métricas de pré-agregação. Se tiver feito chamadas para o TrackMetric no seu aplicativo, você poderá reduzir o tráfego usando a sobrecarga que aceita o cálculo do desvio médio e padrão de um lote de medições. Ou você pode usar um [pacote de pré-agregação](https://www.myget.org/gallery/applicationinsights-sdk-labs).

## <a name="manage-the-maximum-daily-data-volume"></a>Gerenciar o volume de dados diário máximo

Você pode usar o limite de volume diário para limitar os dados coletados. No entanto, se o limite for atingido, ocorrerá uma perda de toda a telemetria enviada do seu aplicativo no restante do dia. *Não é aconselhável* deixar o aplicativo atingir o limite diário. Não será possível rastrear a integridade e o desempenho do seu aplicativo após ele atingir o limite diário.

Em vez de usar o limite de volume diário, use a [amostragem](../../azure-monitor/app/sampling.md) para ajustar o volume de dados para o nível desejado. Em seguida, use o limite diário apenas como um "último recurso", no caso de seu aplicativo inesperadamente começar a enviar volumes muito mais altos de telemetria.

Para alterar o limite diário, na seção **Configurar** do recurso Application Insights, no painel **Uso e custos estimados**, selecione **Limite Diário**.

![Ajustar o limite de volume de telemetria diário](./media/pricing/pricing-003.png)

## <a name="sampling"></a>amostragem
[Amostragem](../../azure-monitor/app/sampling.md) é um método de redução da taxa em que a telemetria é enviada para o seu aplicativo, enquanto mantém a capacidade de encontrar eventos relacionados durante as pesquisas de diagnóstico. Você também mantém contagens de eventos corretas.

A amostragem é uma maneira eficiente de reduzir encargos e permanecer dentro de sua cota mensal. O algoritmo de amostragem mantém itens de telemetria relacionados, para que, por exemplo, quando Pesquisar for utilizado, você possa encontrar a solicitação relacionada a uma exceção específica. O algoritmo também mantém contagens corretas, para que você veja os valores corretos no Metrics Explorer referentes a taxas de solicitação, taxas de exceção e outras contagens.

Há várias formas de amostragem.

* [Amostragem adaptável](../../azure-monitor/app/sampling.md) é o padrão para o SDK do ASP.NET. A amostragem adaptável se ajusta automaticamente ao volume de telemetria enviado por seu aplicativo. Ela opera automaticamente no SDK em seu aplicativo Web, para que o tráfego de telemetria na rede seja reduzido. 
* *amostragem de ingestão* é uma alternativa que opera no ponto em que a telemetria de seu aplicativo entra no serviço do Application Insights. A amostragem de ingestão não afeta o volume de telemetria enviado do seu aplicativo, mas reduz o volume retido pelo serviço. Você pode usar a amostragem de ingestão para reduzir a cota usada pela telemetria de navegadores e de outros SDKs.

Para definir a amostragem de ingestão, vá para o painel **Preços**:

![No painel Cota e preços, clique no bloco Amostras e selecione uma fração de amostragem](./media/pricing/pricing-004.png)

> [!WARNING]
> O painel **Amostragem de dados** controla somente o valor de amostragem de ingestão. Ele não reflete a taxa de amostragem aplicada pelo SDK do Application Insights no seu aplicativo. Se a telemetria de entrada já tiver sido obtida como amostra no SDK, a amostragem de ingestão não será aplicada.
>

Para descobrir a taxa de amostragem real, independentemente de onde ela tiver sido aplicada, use uma [consulta do Analytics](analytics.md). A consulta tem esta aparência:

    requests | where timestamp > ago(1d)
    | summarize 100/avg(itemCount) by bin(timestamp, 1h)
    | render areachart

Em cada registro mantido, `itemCount` indica o número de registros originais que ele representa. É igual a 1 + o número de registros descartados anteriormente. 

## <a name="limits-summary"></a>Resumo de limites

[!INCLUDE [application-insights-limits](../../../includes/application-insights-limits.md)]

## <a name="disable-daily-cap-e-mails"></a>Desabilitar os emails de limite diário

Para desabilitar os emails de limite diário, na seção **Configurar** do recurso Application Insights, no painel **Uso e custos estimados**, selecione **Limite Diário**. Há configurações para enviar email quando o limite é alcançado, bem como quando um nível de aviso ajustável é atingido. Se você quiser desabilitar todos os diário cap relacionado a volume emails desmarque ambas as caixas.

## <a name="legacy-enterprise-per-node-pricing-tier"></a>Legacy Enterprise (por nó) tipo de preço

Para pioneiros do Azure Application Insights, ainda há dois tipos de preço possíveis: Básico e Enterprise. O tipo de preço básico é o mesmo descrito acima e é a camada padrão. Ele inclui todos os recursos de camada empresariais, sem nenhum custo adicional. As listas de camada básica principalmente no volume de dados ingeridos. 

> [!NOTE]
> Esses tipos de preço herdados foram renomeados. Agora é chamado de camada de preços Enterprise **por nó** e o tipo de preço básico agora é chamado **por GB**. Esses novos nomes são usados abaixo e no portal do Azure.  

A camada por nó (anteriormente conhecido como Enterprise) tem um encargo por nó, e cada nó recebe uma franquia diária de dados. O tipo de preço por nó, será cobrado pelos dados ingeridos acima da permissão inclusa. Se você estiver usando o Operations Management Suite, você deve escolher a camada por nó. 

Para preços atuais em sua moeda e região, consulte [Preços do Application Insights](https://azure.microsoft.com/pricing/details/application-insights/).

> [!NOTE]
> Em abril de 2018, [introduzimos](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) um novo modelo de preços para monitoramento do Azure. Esse modelo adota um modelo de "pagamento conforme o uso" simples no portfólio completo de serviços de monitoramento. Saiba mais sobre o [novo modelo de preços](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs), como a [avaliar o impacto de migrar para esse modelo](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#assessing-the-impact-of-the-new-pricing-model) com base nos seus padrões de uso e [como aceitar o novo modelo](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#moving-to-the-new-pricing-model)

### <a name="per-node-tier-and-operations-management-suite-subscription-entitlements"></a>Por tipo de nó e direitos de assinatura do Operations Management Suite

Os clientes que compram o Operations Management Suite E1 e E2 podem obter Insights por nó de aplicativo como um componente adicional sem custo adicional como [anunciado anteriormente](https://blogs.technet.microsoft.com/msoms/2017/05/19/azure-application-insights-enterprise-as-part-of-operations-management-suite-subscription/). Especificamente, cada unidade do Operations Management Suite E1 e E2 tem direito a um nó da camada de aplicativo Insights por nó. Cada nó do Application Insights inclui até 200 MB de dados ingeridos por dia (separado de ingestão de dados do Log Analytics), com a retenção de dados de 90 dias sem nenhum custo adicional. A camada é descrita mais detalhados posteriormente neste artigo. 

Como essa camada é aplicável somente para clientes com uma assinatura do Operations Management Suite, os clientes que não tem uma assinatura do Operations Management Suite não veem uma opção para selecionar essa camada.

> [!NOTE]
> Para garantir que você obtenha este direito, seus recursos do Application Insights devem estar no tipo de preço por nó. Esse direito se aplica apenas como nós. Recursos do Application Insights na camada por GB não têm nenhum benefício. Esse direito não será visível nos custos estimados mostrados no painel **Uso e custo estimado**. Além disso, se você mover uma assinatura do Azure novo modelo de preços de monitoramento em abril de 2018, a camada por GB é a única camada disponível. Migrar uma assinatura para o novo modelo de preços de monitoramento do Azure não será recomendado se você tiver uma assinatura do Operations Management Suite.

### <a name="how-the-per-node-tier-works"></a>Como funciona a camada por nó

* Você paga por cada nó que envia a telemetria para todos os aplicativos na camada por nó.
  * Um *nó* é um computador de servidor físico ou virtual ou uma instância de função de plataforma como serviço que hospeda o aplicativo.
  * Computadores de desenvolvimento, navegadores do cliente e dispositivos móveis não contam como nós.
  * Se o aplicativo tiver vários componentes que enviam telemetria, como um serviço Web e um trabalhado de back-end, os componentes serão contados separadamente.
  * Os dados de [Live Metrics Stream](../../azure-monitor/app/live-stream.md) não são contatos para fins de preços. Em uma assinatura, seus encargos são por nó, não por aplicativo. Se você tiver cinco nós que enviam telemetria para 12 aplicativos, o encargo será de cinco nós.
* Embora as cobrança sejam cotadas por mês, você é cobrado apenas por aquelas horas em que um nó envia telemetria de um aplicativo. O encargo por hora é a cobrança mensal cotada dividida por 744 (o número de horas em um mês de 31 dias).
* Uma alocação de volume de dados de 200 MB por dia é fornecida para cada nó detectado (com granularidade por hora). A alocação de dados não utilizada não é transportada de um dia para o outro.
  * Se você escolher o tipo de preço por nó, cada assinatura recebe uma franquia diária de dados com base no número de nós que enviam telemetria para recursos do Application Insights nessa assinatura. Portanto, se você tiver cinco nós que enviam dados todos os dias, você terá uma permissão em pool de 1 GB aplicada a todos os recursos do Application Insights nessa assinatura. Não importa se determinados nós enviam mais dados que outros nós, porque os dados incluídos são compartilhados entre todos os nós. Se em um determinado dia, os recursos do Application Insights recebem mais dados do que está incluído na alocação de dados diária para esta assinatura, os encargos de dados excedentes por GB se aplicam. 
  * A permissão de dados diária é calculada como o número de horas por dia (usando o UTC) que cada nó envia telemetria dividido por 24, multiplicado por 200 MB. Portanto, se você tiver quatro nós que enviam telemetria durante 15 das 24 horas do dia, os dados incluídos para esse dia serão ((4 &#215; 15) / 24) &#215; 200 MB = 500 MB. Pelo preço de US$ 2,30 por GB de dados excedentes, o valor a cobrar seria de US$ 1,15 se os nós enviassem 1 GB de dados naquele dia.
  * A franquia diária de camada por nó não sejam compartilhada com aplicativos para os quais você escolheu a camada por GB. A permissão não utilizada não é herdada do dia a dia. 

### <a name="examples-of-how-to-determine-distinct-node-count"></a>Exemplos de como determinar a contagem de nós distinta

| Cenário                               | Contagem de nós diária total |
|:---------------------------------------|:----------------:|
| 1 aplicativo usando 3 instâncias do Serviço de Aplicativo do Azure e 1 servidor virtual | 4 |
| 3 aplicativos em execução em 2 VMs; recursos do Application Insights para esses aplicativos estão na mesma assinatura e na camada por nó | 2 | 
| 4 aplicativos cujos recursos do Applications Insights estão na mesma assinatura; cada aplicativo executando 2 instâncias durante 16 horas fora de pico e 4 instâncias durante 8 horas de pico | 13.33 | 
| Serviços de nuvem com uma função de trabalho e uma função web, cada uma executando duas instâncias | 4 | 
| Um cluster do Azure Service Fabric de 5 nós que executa 50 microsserviços; cada microsserviço executando 3 instâncias | 5|

* A contagem de nós exata depende de qual SDK do Application Insights seu aplicativo está usando. 
  * No SDK versões 2.2 e posterior, o [SDK Core](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) do Application Insights e o [SDK Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) relatam cada host de aplicativo como um nó. Exemplos são o nome do computador para servidores físicos e hosts de VM ou o nome da instância para serviços de nuvem.  A única exceção é um aplicativo que usa apenas o [.NET Core](https://dotnet.github.io/) e o Application Insights o SDK Core. Nesse caso, apenas um nó é relatado para todos os hosts porque o nome do host não está disponível. 
  * Para versões anteriores do SDK, o [SDK Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) se comporta como as versões do SDK mais recentes, mas o [SDK Core](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) relata apenas um nó, independentemente do número de hosts do aplicativo. 
  * Se o aplicativo usar o SDK para definir **roleInstance** como um valor personalizado, por padrão, esse mesmo valor será usado para determinar a contagem de nós. 
  * Se você estiver usando uma nova versão do SDK com um aplicativo que executa a partir de computadores clientes ou dispositivos móveis, a contagem de nós poderá retornar um número muito grande (devido ao grande número de computadores clientes ou dispositivos móveis). 

## <a name="automation"></a>Automação

Você pode escrever um script para definir o tipo de preço usando o gerenciamento de recursos do Azure. [Saiba como](powershell.md#price).


## <a name="next-steps"></a>Próximas etapas

* [Amostragem](../../azure-monitor/app/sampling.md)

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: ../../azure-monitor/app/app-insights-overview.md
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/
