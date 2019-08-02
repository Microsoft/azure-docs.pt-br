---
title: Monitorar aplicativos do Python com o Azure Application Insights | Microsoft Docs
description: Fornece instruções para conectar OpenCensus Python com o Application Insights
services: application-insights
keywords: ''
author: reyang
ms.author: reyang
ms.date: 07/02/2019
ms.service: application-insights
ms.topic: conceptual
ms.reviewer: mbullwin
manager: carmonm
ms.openlocfilehash: 2c043ad793dcf5e59eaf460d1ec4aa7a3b48810d
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67541445"
---
# <a name="collect-distributed-traces-from-python-preview"></a>Coletar rastreamentos distribuídos do Python (Versão Prévia)

Application Insights agora dá suporte a distribuídas o rastreamento de aplicativos do Python por meio da integração com [OpenCensus](https://opencensus.io). Este artigo orientará você passo a passo durante o processo de configuração OpenCensus para Python e obtendo os dados de monitoramento para o Application Insights.

## <a name="prerequisites"></a>Pré-requisitos

- É necessária uma assinatura do Azure.
- O Python deve ser instalado; este artigo usa [Python 3.7.0](https://www.python.org/downloads/), embora versões anteriores provavelmente funcionem com um pequeno ajuste.

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [Portal do Azure](https://portal.azure.com/).

## <a name="create-application-insights-resource"></a>Criar recurso do Application Insights

Primeiro você precisa criar um recurso do Application Insights, que irá gerar um key(ikey) de instrumentação. O ikey é usado para configurar o OpenCensus SDK para enviar dados de telemetria ao Application Insights.

1. Selecione **Criar um recurso** > **Ferramentas para Desenvolvedores** > **Application Insights**.

   ![Adicionando um Recurso do Application Insights](./media/opencensus-python/0001-create-resource.png)

   Uma caixa de configuração é exibida. Use a tabela a seguir para preencher os campos de entrada.

    | Configurações        | Value           | DESCRIÇÃO  |
   | ------------- |:-------------|:-----|
   | **Name**      | Valor Globalmente Exclusivo | Nome que identifica o aplicativo que você está monitorando |
   | **Grupo de recursos**     | myResourceGroup      | Nome para o novo grupo de recursos no qual hospedar dados do Application Insights |
   | **Localidade** | East US | Escolher uma localização perto de você ou perto onde seu aplicativo está hospedado |

2. Clique em **Criar**.

## <a name="install-opencensus-azure-monitor-exporters"></a>Instalar o Azure OpenCensus Exportadores de Monitor

1. Instale os Azure OpenCensus Monitor Exportadores:

    ```console
    python -m pip install opencensus-ext-azure
    ```

    > [!NOTE]
    > `python -m pip install opencensus-ext-azure` pressupõe que você tenha uma variável de ambiente PATH definida para sua instalação do Python. Se você não tiver configurado isso, precisará fornecer o caminho completo para o local em que o executável do Python está localizado, o que resultará em um comando como: `C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure`.

2. Primeiro vamos gerar alguns dados de rastreamento localmente. No Python IDLE, ou seu editor preferencial, digite o seguinte código:

    ```python
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer

    tracer = Tracer(sampler=ProbabilitySampler(1.0))

    def valuePrompt():
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

3. Executar o código solicitará repetidamente que você insira um valor. A cada entrada, o valor será impresso no shell, e uma parte correspondente do **SpanData** será gerada pelo módulo OpenCensus Python. O projeto OpenCensus define um [_rastreamento como uma árvore de spans_](https://opencensus.io/core-concepts/tracing/).
    
    ```
    Enter a value: 4
    4
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='15ac5123ac1f6847', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:22.805429Z', end_time='2019-06-27T18:21:44.933405Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 25
    25
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='2e512f846ba342de', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:44.933405Z', end_time='2019-06-27T18:21:46.156787Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 100
    100
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='f3f9f9ee6db4740a', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:46.157732Z', end_time='2019-06-27T18:21:47.269583Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    ```

4. Embora seja útil para fins de demonstração, por fim, queremos emitir o SpanData ao Application Insights. Modifique seu código da etapa anterior com base no seguinte exemplo de código:

    ```python
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer
    
    # TODO: replace the all-zero GUID with your instrumentation key.
    tracer = Tracer(
        exporter=AzureExporter(
            instrumentation_key='00000000-0000-0000-0000-000000000000',
        ),
        sampler=ProbabilitySampler(1.0),
    )

    def valuePrompt():
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```
5. Agora, ao executar o script de Python, você ainda deverá ser solicitado a inserir valores, mas agora apenas o valor está sendo impressa no shell.

## <a name="start-monitoring-in-the-azure-portal"></a>Iniciar o monitoramento no Portal do Azure

1. Agora você pode reabrir a página **Visão Geral** do Application Insights no portal do Azure para exibir detalhes sobre seu aplicativo em execução no momento. Selecione **Live Metrics Stream**.

   ![Captura de tela do painel de visão geral com Live Metrics Stream selecionado na caixa vermelha](./media/opencensus-python/0005-overview-live-metrics-stream.png)

2. Navegue de volta para a página **Visão Geral** e selecione **Mapa do Aplicativo** para um layout visual das relações de dependência e tempo da chamada entre os componentes do aplicativo.

    ![Captura de tela do mapa do aplicativo básico](./media/opencensus-python/0007-application-map.png)

    Uma vez que o objetivo era apenas rastrear uma chamada de método, nosso mapa do aplicativo não é tão interessante. Porém, o mapa do aplicativo pode ser dimensionado para visualizar aplicativos muito mais distribuídos:

   ![Mapa de aplicativo](media/opencensus-python/application-map.png)

3. Selecione **Investigar Desempenho** para executar uma análise de desempenho detalhada e determinar a causa raiz da lentidão no desempenho.

    ![Captura de tela do painel de desempenho](./media/opencensus-python/0008-performance.png)

4. Selecionar **Exemplos** e, em seguida, clicar em qualquer um dos exemplos que aparecem no painel à direita inicializará a experiência de detalhes de transação de ponta a ponta. Embora nosso aplicativo de exemplo apenas nos mostre um único evento, um aplicativo mais complexo permitiria explorar a transação de ponta a ponta até o nível da pilha de chamadas do evento individual.

     ![Captura de tela da interface de transação de ponta a ponta](./media/opencensus-python/0009-end-to-end-transaction.png)

## <a name="opencensus-for-python"></a>OpenCensus para Python

* [Personalização](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [Integração do Flask](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-flask)
* [Integração do Django](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-django)
* [Integração do MySQL](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-mysql)
* [PostgreSQL](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-postgresql)

## <a name="next-steps"></a>Próximas etapas

* [OpenCensus Python no GitHub](https://github.com/census-instrumentation/opencensus-python)
* [Mapa do aplicativo](./../../azure-monitor/app/app-map.md)
* [Monitoramento de desempenho de ponta a ponta](./../../azure-monitor/learn/tutorial-performance.md)