---
title: Rastreamento Python do OpenCensus com Azure Application Insights | Microsoft Docs
description: Fornece instruções para conectar o rastreamento do OpenCensus Python com o encaminhador local e o Application Insights
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/18/2018
ms.service: application-insights
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 22e58f31e2f891eb09c3d42a01763c68cdcd11a8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60577429"
---
# <a name="collect-distributed-traces-from-python-preview"></a>Coletar rastreamentos distribuídos do Python (Versão Prévia)

Agora o Application Insights dá suporte ao rastreamento distribuído de aplicativos Python por meio da integração com o [OpenCensus](https://opencensus.io) e nosso novo [encaminhador local](./../../azure-monitor/app/opencensus-local-forwarder.md). Este artigo orientará você passo a passo durante o processo de configuração do OpenCensus para Python e obtenção dos dados de rastreamento para o Application Insights.

## <a name="prerequisites"></a>Pré-requisitos

- É necessária uma assinatura do Azure.
- O Python deve ser instalado; este artigo usa [Python 3.7.0](https://www.python.org/downloads/), embora versões anteriores provavelmente funcionem com um pequeno ajuste.
- Siga as instruções para instalar o [encaminhador local como um serviço Windows](./../../azure-monitor/app/opencensus-local-forwarder.md)

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [Portal do Azure](https://portal.azure.com/).

## <a name="create-application-insights-resource"></a>Criar recurso do Application Insights

Primeiro, você precisa criar um recurso do Application Insights que vai gerar uma chave de instrumentação (ikey). O ikey é usado para configurar o encaminhador local para enviar os rastreamentos distribuídos do seu aplicativo OpenCensus instrumentado para o Application Insights.   

1. Selecione **Criar um recurso** > **Ferramentas para Desenvolvedores** > **Application Insights**.

   ![Adicionando um Recurso do Application Insights](./media/opencensus-python/0001-create-resource.png)

   Uma caixa de configuração é exibida. Use a tabela a seguir para preencher os campos de entrada.

    | Configurações        | Value           | DESCRIÇÃO  |
   | ------------- |:-------------|:-----|
   | **Nome**      | Valor Globalmente Exclusivo | Nome que identifica o aplicativo que você está monitorando |
   | **Tipo de Aplicativo** | Geral | O tipo do aplicativo que você está monitorando |
   | **Grupo de recursos**     | myResourceGroup      | Nome para o novo grupo de recursos no qual hospedar dados do Application Insights |
   | **Localidade** | Leste dos EUA | Escolher uma localização perto de você ou perto onde seu aplicativo está hospedado |

2. Clique em **Criar**.

## <a name="configure-local-forwarder"></a>Configurar o encaminhador local

1. Selecione **Visão Geral** > **Essentials** > Copiar a **Chave de Instrumentação** do aplicativo.

   ![Captura de tela da chave de instrumentação](./media/opencensus-python/0003-instrumentation-key.png)

2. Edite o arquivo `LocalForwarder.config` e adicione a chave de instrumentação. Se você tiver seguido as instruções de [pré-requisito](./../../azure-monitor/app/opencensus-local-forwarder.md), o arquivo estará localizado em `C:\LF-WindowsServiceHost`

    ```xml
      <OpenCensusToApplicationInsights>
        <!--
          Instrumentation key to track telemetry to.
          -->
        <InstrumentationKey>{enter-instrumentation-key}</InstrumentationKey>
      </OpenCensusToApplicationInsights>
    
      <!-- Describes aspects of processing Application Insights telemetry-->
      <ApplicationInsights>
        <LiveMetricsStreamInstrumentationKey>{enter-instrumentation-key}</LiveMetricsStreamInstrumentationKey>
      </ApplicationInsights>
    </LocalForwarderConfiguration>
    ```

3. Reinicie o aplicativo serviço de **Encaminhador Local**.

## <a name="opencensus-python-package"></a>Pacote do OpenCensus Python

1. Instale o pacote do Open Census para Python com pip ou pipenv da linha de comando:

    ```python
    python -m pip install opencensus
    # pip env install opencensus
    ```

    > [!NOTE]
    > `python -m pip install opencensus` pressupõe que você tenha uma variável de ambiente PATH definida para sua instalação do Python. Se você não tiver configurado isso, precisará fornecer o caminho completo para o local em que o executável do Python está localizado, o que resultará em um comando como: `C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus`.

2. Primeiro vamos gerar alguns dados de rastreamento localmente. No Python IDLE, ou seu editor preferencial, digite o seguinte código:

    ```python
    from opencensus.trace.tracer import Tracer
    
    def main():
        while True:
            valuePrompt()
    
    def valuePrompt():
        tracer = Tracer()
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)
    
    if __name__ == "__main__":
        main()
    
    ```

3. Executar o código solicitará repetidamente que você insira um valor. A cada entrada, o valor será impresso no shell, e uma parte correspondente do **SpanData** será gerada pelo módulo OpenCensus Python. O projeto OpenCensus define um [_rastreamento como uma árvore de spans_](https://opencensus.io/core-concepts/tracing/).
    
    ```python
    Enter a value: 4
    4
    [SpanData(name='test', context=SpanContext(trace_id=1f07f062ac394c50925f2ae61e635e14, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='5c17a4ad6ba14299', parent_span_id=None, attributes={}, start_time='2018-09-15T20:42:15.847292Z', end_time='2018-09-15T20:42:17.615664Z', child_span_count=0, stack_trace=None, time_events=[], links=[], status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 25
    25
    [SpanData(name='test', context=SpanContext(trace_id=c71b4e88a22a495da61df52ce3eee3e1, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='51547c0af5f046eb', parent_span_id=None, attributes={}, start_time='2018-09-15T20:42:17.615664Z', end_time='2018-09-15T20:48:11.160314Z', child_span_count=0, stack_trace=None, time_events=[], links=[], status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 100
    100
    [SpanData(name='test', context=SpanContext(trace_id=b4cdcc9e6df44a8fbb6e8ddeccc1351c, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='f2caacf7892744d1', parent_span_id=None, attributes={}, start_time='2018-09-15T20:48:11.175931Z', end_time='2018-09-15T20:48:12.629178Z', child_span_count=0, stack_trace=None, time_events=[], links=[], status=None, same_process_as_parent_span=None, span_kind=0)]
    ```

4. Embora isso seja útil para fins de demonstração, queremos emitir o SpanData de modo que ele possa ser selecionado pelo nosso **serviço do encaminhador local** e enviado ao Application Insights. Modifique seu código da etapa anterior para o seguinte:

    ```python
    from opencensus.trace.tracer import Tracer
    from opencensus.trace import config_integration
    from opencensus.trace.exporters.ocagent import trace_exporter
    from opencensus.trace import tracer as tracer_module
    
    import os
    
    def main():        
        while True:
            valuePrompt()
    
    def valuePrompt():
        export_LocalForwarder = trace_exporter.TraceExporter(
        service_name=os.getenv('SERVICE_NAME', 'python-service'),
        endpoint=os.getenv('OCAGENT_TRACE_EXPORTER_ENDPOINT'))
        
        tracer = Tracer(exporter=export_LocalForwarder)
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)
    
    if __name__ == "__main__":
        main()
    ```

5. Se você salvar e tentar executar o módulo acima, poderá receber um `ModuleNotFoundError` para `grpc`. Se isso ocorrer, execute o seguinte para instalar o [pacote grpcio](https://pypi.org/project/grpcio/) com:

    ```
    python -m pip install grpcio
    ```

6. Agora, quando você executa o script Python acima, ainda deve ser solicitado a inserir valores, mas agora apenas o valor que está sendo impresso no shell.

7. Para confirmar que o **local encaminhador** está capturando os rastreamentos, verifique o arquivo `LocalForwarder.config`. Se você tiver seguido as etapas em [pré-requisito](https://docs.microsoft.com/azure/application-insights/local-forwarder), ele estará localizado em `C:\LF-WindowsServiceHost`.

    Na imagem abaixo do arquivo de log, você pode ver que, antes de executar o segundo script em que adicionamos um exportador, `OpenCensus input BatchesReceived` era 0. Depois que começamos a executar o script atualizado, `BatchesReceived` foi incrementado do mesmo modo que o número de valores que inserimos:
    
    ![Formulário de recursos do Application Insights novo](./media/opencensus-python/0004-batches-received.png)

## <a name="start-monitoring-in-the-azure-portal"></a>Iniciar o monitoramento no Portal do Azure

1. Agora você pode reabrir a página **Visão Geral** do Application Insights no portal do Azure para exibir detalhes sobre seu aplicativo em execução no momento. Selecione **Live Metrics Stream**.

   ![Captura de tela do painel de visão geral com Live Metrics Stream selecionado na caixa vermelha](./media/opencensus-python/0005-overview-live-metrics-stream.png)

2. Se você executar o segundo script do Python novamente e começar a inserir valores, verá os dados de rastreamento ao vivo conforme eles chegam no Application Insights do serviço do encaminhador local.

   ![Captura de tela do Live Metrics Stream com os dados de desempenho exibidos](./media/opencensus-python/0006-stream.png)

3. Navegue de volta para a página **Visão Geral** e selecione **Mapa do Aplicativo** para um layout visual das relações de dependência e tempo da chamada entre os componentes do aplicativo.

    ![Captura de tela do mapa do aplicativo básico](./media/opencensus-python/0007-application-map.png)

    Uma vez que o objetivo era apenas rastrear uma chamada de método, nosso mapa do aplicativo não é tão interessante. Porém, o mapa do aplicativo pode ser dimensionado para visualizar aplicativos muito mais distribuídos:

   ![Mapa de aplicativo](media/opencensus-python/application-map.png)

4. Selecione **Investigar Desempenho** para executar uma análise de desempenho detalhada e determinar a causa raiz da lentidão no desempenho.

    ![Captura de tela do painel de desempenho](./media/opencensus-python/0008-performance.png)

5. Selecionar **Exemplos** e, em seguida, clicar em qualquer um dos exemplos que aparecem no painel à direita inicializará a experiência de detalhes de transação de ponta a ponta. Embora nosso aplicativo de exemplo apenas nos mostre um único evento, um aplicativo mais complexo permitiria explorar a transação de ponta a ponta até o nível da pilha de chamadas do evento individual.

     ![Captura de tela da interface de transação de ponta a ponta](./media/opencensus-python/0009-end-to-end-transaction.png)

## <a name="opencensus-trace-for-python"></a>Rastreamento do OpenCensus para Python

Abordamos apenas as noções básicas da conexão do OpenCensus para Python com o encaminhador de local e o Application Insights. A diretriz de uso oficial cobre tópicos mais avançados, como:

* [Amostradores](https://opencensus.io/api/python/trace/usage.html#samplers)
* [Integração do Flask](https://opencensus.io/api/python/trace/usage.html#flask)
* [Integração do Django](https://opencensus.io/api/python/trace/usage.html#django)
* [Integração do MySQL](https://opencensus.io/api/python/trace/usage.html#service-integration)
* [PostgreSQL](https://opencensus.io/api/python/trace/usage.html#postgresql)
  
## <a name="next-steps"></a>Próximas etapas

* [Guia de uso do OpenCensus Python](https://opencensus.io/api/python/trace/usage.html)
* [Mapa do aplicativo](./../../azure-monitor/app/app-map.md)
* [Monitoramento de desempenho de ponta a ponta](./../../azure-monitor/learn/tutorial-performance.md)
