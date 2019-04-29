---
title: Rastreamento Go do OpenCensus com Azure Application Insights | Microsoft Docs
description: Fornece instruções para integrar o rastreamento Go do OpenCensus com o encaminhador local e o Application Insights
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/15/2018
ms.service: application-insights
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: cdf01fbbcc8ef1f90b2e0f8973f59c46c5bf70f8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60577752"
---
# <a name="collect-distributed-traces-from-go-preview"></a>Coletar rastreamentos distribuídos do Go (versão prévia)

Agora o Application Insights dá suporte ao rastreamento distribuído de aplicativos Go por meio da integração com o [OpenCensus](https://opencensus.io) e nosso novo [encaminhador local](./opencensus-local-forwarder.md). Este artigo orientará você passo a passo durante o processo de configuração do OpenCensus para Go e obtenção dos dados de rastreamento para o Application Insights.

## <a name="prerequisites"></a>Pré-requisitos

- É necessária uma assinatura do Azure.
- O Go deve ser instalado. Este artigo usa a versão 1.11 [Download do Go](https://golang.org/dl/).
- Siga as instruções para instalar o [encaminhador local como um serviço Windows](./opencensus-local-forwarder.md).

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [Portal do Azure](https://portal.azure.com/).

## <a name="create-application-insights-resource"></a>Criar recurso do Application Insights

Primeiro, você precisa criar um recurso do Application Insights que vai gerar uma chave de instrumentação (ikey). O ikey é usado para configurar o encaminhador local para enviar os rastreamentos distribuídos do seu aplicativo OpenCensus instrumentado para o Application Insights.   

1. Selecione **Criar um recurso** > **Ferramentas para Desenvolvedores** > **Application Insights**.

   ![Adicionando um Recurso do Application Insights](./media/opencensus-Go/0001-create-resource.png)

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

   ![Captura de tela da chave de instrumentação](./media/opencensus-Go/0003-instrumentation-key.png)

2. Edite o arquivo `LocalForwarder.config` e adicione a chave de instrumentação. Se você tiver seguido as instruções de [pré-requisito](./opencensus-local-forwarder.md), o arquivo estará localizado em `C:\LF-WindowsServiceHost`

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

## <a name="opencensus-go-packages"></a>Pacotes Go do OpenCensus

1. Instale os pacotes do Open Census para Go da linha de comando:

    ```go
    go get -u go.opencensus.io
    go get -u contrib.go.opencensus.io/exporter/ocagent
    ```

2. Adicione o seguinte código a um arquivo .go e, em seguida, compile e execute. (Este exemplo é derivado de diretrizes oficiais OpenCensus com o código adicionado que facilita a integração com o encaminhador local)

     ```go
        // Copyright 2018, OpenCensus Authors
        //
        // Licensed under the Apache License, Version 2.0 (the "License");
        // you may not use this file except in compliance with the License.
        // You may obtain a copy of the License at
        //
        //     https://www.apache.org/licenses/LICENSE-2.0
        //
        // Unless required by applicable law or agreed to in writing, software
        // distributed under the License is distributed on an "AS IS" BASIS,
        // WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
        // See the License for the specific language governing permissions and
        // limitations under the License.
        package main
        
        import (
        
            "bytes"
            "fmt"
            "log"
            "net/http"
            os "os"
            
            ocagent "contrib.go.opencensus.io/exporter/ocagent"
            "go.opencensus.io/plugin/ochttp"
            "go.opencensus.io/plugin/ochttp/propagation/tracecontext"
            "go.opencensus.io/trace"
        
        )
        
        func main() {
            // Register stats and trace exporters to export the collected data.
            serviceName := os.Getenv("SERVICE_NAME")
            if len(serviceName) == 0 {
                serviceName = "go-app"
            }
            fmt.Printf(serviceName)
            agentEndpoint := os.Getenv("OCAGENT_TRACE_EXPORTER_ENDPOINT")

            if len(agentEndpoint) == 0 {
                agentEndpoint = fmt.Sprintf("%s:%d", ocagent.DefaultAgentHost, ocagent.DefaultAgentPort)
            }
        
            fmt.Printf(agentEndpoint)
            exporter, err := ocagent.NewExporter(ocagent.WithInsecure(), ocagent.WithServiceName(serviceName), ocagent.WithAddress(agentEndpoint))
        
            if err != nil {
                log.Printf("Failed to create the agent exporter: %v", err)
            }
        
            trace.RegisterExporter(exporter)
        
            trace.ApplyConfig(trace.Config{DefaultSampler: trace.AlwaysSample()})
        
            client := &http.Client{Transport: &ochttp.Transport{Propagation: &tracecontext.HTTPFormat{}}}
        
            http.HandleFunc("/", func(w http.ResponseWriter, req *http.Request) {
                fmt.Fprintf(w, "hello world")
        
                var jsonStr = []byte(`[ { "url": "http://blank.org", "arguments": [] } ]`)
                r, _ := http.NewRequest("POST", "http://blank.org", bytes.NewBuffer(jsonStr))
                r.Header.Set("Content-Type", "application/json")
        
                // Propagate the trace header info in the outgoing requests.
                r = r.WithContext(req.Context())
                resp, err := client.Do(r)
                if err != nil {
                    log.Println(err)
                } else {
                    // TODO: handle response
                    resp.Body.Close()
                }
            })
        
            http.HandleFunc("/call_blank", func(w http.ResponseWriter, req *http.Request) {
                fmt.Fprintf(w, "hello world")
        
                r, _ := http.NewRequest("GET", "http://blank.org", nil)

                // Propagate the trace header info in the outgoing requests.
                r = r.WithContext(req.Context())
                resp, err := client.Do(r)
        
                if err != nil {
                    log.Println(err)
                } else {
                    // TODO: handle response
                    resp.Body.Close()
                }        
            })
        
            log.Fatal(http.ListenAndServe(":50030", &ochttp.Handler{Propagation: &tracecontext.HTTPFormat{}}))
        
        }
     ```

3. Depois que o aplicativo go simples estiver em execução, navegue para `http://localhost:50030`. Cada atualização do navegador gerará o texto “hello world” acompanhado pelos dados do período correspondentes que são obtidos pelo encaminhador local.

4. Para confirmar que o **local encaminhador** está capturando os rastreamentos, verifique o arquivo `LocalForwarder.config`. Se você tiver seguido as etapas em [pré-requisito](https://docs.microsoft.com/azure/application-insights/local-forwarder), ele estará localizado em `C:\LF-WindowsServiceHost`.

    Na imagem abaixo do arquivo de log, você pode ver que, antes de executar o segundo script em que adicionamos um exportador, `OpenCensus input BatchesReceived` era 0. Depois que começamos a executar o script atualizado, `BatchesReceived` foi incrementado do mesmo modo que o número de valores que inserimos:
    
    ![Formulário de recursos do Application Insights novo](./media/opencensus-go/0004-batches-received.png)

## <a name="start-monitoring-in-the-azure-portal"></a>Iniciar o monitoramento no Portal do Azure

1. Agora você pode reabrir a página **Visão Geral** do Application Insights no portal do Azure para exibir detalhes sobre seu aplicativo em execução no momento. Selecione **Live Metrics Stream**.

   ![Captura de tela do painel de visão geral com Live Metrics Stream selecionado na caixa vermelha](./media/opencensus-go/0005-overview-live-metrics-stream.png)

2. Se você executar o segundo aplicativo Go novamente e começar a atualizar o navegador para `http://localhost:50030`, verá os dados de rastreamento ao vivo conforme eles chegam no Application Insights do serviço do encaminhador local.

   ![Captura de tela do Live Metrics Stream com os dados de desempenho exibidos](./media/opencensus-go/0006-stream.png)

3. Navegue de volta para a página **Visão Geral** e selecione **Mapa do Aplicativo** para um layout visual das relações de dependência e tempo da chamada entre os componentes do aplicativo.

    ![Captura de tela do mapa do aplicativo básico](./media/opencensus-go/0007-application-map.png)

    Uma vez que o objetivo era apenas rastrear uma chamada de método, nosso mapa do aplicativo não é tão interessante. Porém, o mapa do aplicativo pode ser dimensionado para visualizar aplicativos muito mais distribuídos:

   ![Mapa de aplicativo](media/opencensus-go/application-map.png)

4. Selecione **Investigar Desempenho** para executar uma análise de desempenho detalhada e determinar a causa raiz da lentidão no desempenho.

    ![Captura de tela do painel de desempenho](./media/opencensus-go/0008-performance.png)

5. Selecionar **Exemplos** e, em seguida, clicar em qualquer um dos exemplos que aparecem no painel à direita inicializará a experiência de detalhes de transação de ponta a ponta. Embora nosso aplicativo de exemplo apenas nos mostre um único evento, um aplicativo mais complexo permitiria explorar a transação de ponta a ponta até o nível da pilha de chamadas do evento individual.

     ![Captura de tela da interface de transação de ponta a ponta](./media/opencensus-go/0009-end-to-end-transaction.png)

## <a name="opencensus-trace-for-go"></a>Rastreamento do OpenCensus para Go

Abordamos apenas as noções básicas da integração do OpenCensus ao Go com o encaminhador local e o Application Insights. A [diretriz de uso oficial do OpenCensus Go](https://godoc.org/go.opencensus.io) cobre tópicos mais avançados.

## <a name="next-steps"></a>Próximas etapas

* [Mapa do aplicativo](./../../azure-monitor/app/app-map.md)
* [Monitoramento de desempenho de ponta a ponta](./../../azure-monitor/learn/tutorial-performance.md)
