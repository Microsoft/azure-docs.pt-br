---
title: "Consumo do serviço Web de Gerenciamento de Modelos do Azure Machine Learning | Microsoft Docs"
description: "Este documento descreve as etapas e os conceitos envolvidos no consumo de serviços Web implantados usando o Gerenciamento de Modelos no Azure Machine Learning."
services: machine-learning
author: raymondlaghaeian
ms.author: raymondl
manager: neerajkh
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/06/2017
ms.openlocfilehash: fc4d793bcf402a3e742fae2b2c0052f2d60d1b47
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="consuming-web-services"></a>Consumindo serviços Web
Depois de implantar um modelo como um serviço Web em tempo real, você poderá enviar dados a ele e obter previsões de uma variedade de plataformas e aplicativos. O serviço Web em tempo real expõe uma API REST para obter previsões. Você pode enviar dados para o serviço Web no formato de única linha ou de várias linhas para obter uma ou mais previsões de cada vez.

Com o serviço Web do Azure Machine Learning, um aplicativo externo comunica-se com um modelo de previsão de forma síncrona, fazendo uma chamada HTTP POST para a URL do serviço. Para fazer uma chamada ao serviço Web, o aplicativo cliente precisa especificar a chave de API que é criada quando você implanta uma previsão e colocar os dados da solicitação no corpo da solicitação POST.

Observe que as chaves de API só estão disponíveis no modo de implantação de cluster. Os serviços Web locais não têm chaves.

## <a name="service-deployment-options"></a>Opções de implantação de serviço
Os serviços Web do Azure Machine Learning podem ser implantados nos clusters baseados em nuvem para cenários de teste e de produção e para estações de trabalho locais usando o mecanismo do Docker. A funcionalidade do modelo de previsão em ambos os casos permanece a mesma. A implantação baseada em cluster oferece uma solução escalonável e de alto desempenho com base nos serviços de Contêiner do Azure, enquanto a implantação local pode ser usada para depuração. 

A API e a CLI do Azure Machine Learning fornecem comandos convenientes para criar e gerenciar ambientes de computação para implantações de serviço usando a opção ```az ml env```. 

## <a name="list-deployed-services-and-images"></a>Listar serviços e imagens implantados
Você pode listar os serviços e as imagens do Docker implantados no momento usando o comando da CLI ```az ml service list realtime -o table```. Observe que esse comando sempre funciona no contexto do ambiente de computação atual e não mostra os serviços implantados em um ambiente que não está definido para ser o atual. Para definir o ambiente, use ```az ml env set```. 

## <a name="get-service-information"></a>Obter informações de serviço
Depois que o serviço Web for implantado com êxito, use o seguinte comando para obter a URL do serviço e outros detalhes para chamar o ponto de extremidade de serviço. 

```
az ml service usage realtime -i <service name>
```

Este comando imprimirá a URL do serviço, os cabeçalhos de solicitação necessários, a URL do Swagger e os dados de exemplo para chamar o serviço se o esquema da API do serviço tiver sido fornecido no momento da implantação.

Você pode testar o serviço diretamente da CLI sem compor uma solicitação HTTP, inserindo o comando da CLI de exemplo com os dados de entrada:

```
az ml service run realtime -i <service name> -d "Your input data"
```

## <a name="get-the-service-api-key"></a>Obter a chave de API do serviço
Para obter a chave do serviço Web, use o seguinte comando:

```
az ml service keys realtime -i <web service id>
```
Ao criar a solicitação HTTP, use a chave no cabeçalho de autorização: "Autorização": "Portador <key>"

## <a name="get-the-service-swagger-description"></a>Obter a descrição do Swagger do serviço
Se o esquema da API do serviço foi fornecido, o ponto de extremidade de serviço deverá expor um documento do Swagger em ```http://<ip>/api/v1/service/<service name>/swagger.json```. O documento do Swagger pode ser usado para gerar o cliente do serviço e explorar os dados de entrada esperados e outros detalhes sobre o serviço automaticamente.

## <a name="get-service-logs"></a>Obter logs de serviço
Para compreender o comportamento do serviço e diagnosticar problemas, há várias maneiras de recuperar os logs de serviço:
- Comando da CLI ```az ml service logs realtime -i <service id>```. Esse comando funciona tanto no modo local quanto no de cluster.
- Se o registro em log do serviço foi habilitado na implantação, os logs de serviço também serão enviados para o AppInsight. O comando da CLI ```az ml service usage realtime -i <service id>``` mostra a URL do AppInsight. Observe que os logs do AppInsight podem ter um atraso de 2 a 5 minutos.
- Os logs de cluster podem ser exibidos por meio do console do Kubernetes que é conectado quando você define o ambiente de cluster atual com ```az ml env set```
- Os logs locais do Docker ficam disponíveis por meio dos logs do mecanismo do Docker quando o serviço é executado localmente.

## <a name="call-the-service-using-c"></a>Chamar o serviço usando C#
Use a URL do serviço para enviar uma solicitação de um aplicativo de console C#. 

1. No Visual Studio, crie um novo aplicativo de console: 
    * No menu, clique em, Arquivo -> Novo -> Projeto
    * No Visual Studio C#, clique em Área de Trabalho de Classe do Windows e selecione Aplicativo de Console.
2. Insira _MyFirstService_ como o nome do projeto e, em seguida, clique em OK.
3. Em Referências do Projeto, defina as referências para _System.Net_ e _System.Net.Http_.
4. Clique em Ferramentas -> Gerenciador de Pacotes NuGet -> Console do Gerenciador de Pacotes e, em seguida, instale o pacote Microsoft.AspNet.WebApi.Client.
5. Abra o arquivo Program.cs e substitua o código pelo código a seguir:
6. Atualize os parâmetros _SERVICE_URL_ e _API_KEY_ com as informações do seu serviço Web.
7. Execute o projeto.

```csharp
using System;
using System.Collections.Generic;
using System.Net.Http;
using System.Net.Http.Headers;
using Newtonsoft.Json;

namespace MyFirstService
{
    class Program
    {
        // Web Service URL (update it with your service url)
        private const string SERVICE_URL = "http://<service ip address>:80/api/v1/service/<service name>/score";
        private const string API_KEY = "your service key";

        static void Main(string[] args)
        {
            Program.PostRequest();
            Console.ReadLine();
        }

        private static void PostRequest()
        {
            HttpClient client = new HttpClient();
            client.BaseAddress = new Uri(SERVICE_URL);
            client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", API_KEY);

            var inputJson = new List<RequestPayload>();
            RequestPayload payload = new RequestPayload();
            List<InputDf> inputDf = new List<InputDf>();
            inputDf.Add(new InputDf()
            {
                feature1 = value1,
                feature2 = value2,
            });
            payload.Input_df_list = inputDf;
            inputJson.Add(payload);

            try
            {
                var request = new HttpRequestMessage(HttpMethod.Post, string.Empty);
                request.Content = new StringContent(JsonConvert.SerializeObject(payload));
                request.Content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
                var response = client.SendAsync(request).Result;

                Console.Write(response.Content.ReadAsStringAsync().Result);
            }
            catch (Exception e)
            {
                Console.Out.WriteLine(e.Message);
            }
        }
        public class InputDf
        {
            public double feature1 { get; set; }
            public double feature2 { get; set; }
        }
        public class RequestPayload
        {
            [JsonProperty("input_df")]
            public List<InputDf> Input_df_list { get; set; }
        }
    }
}
```

## <a name="call-the-web-service-using-python"></a>Chamar o serviço Web usando Python
Use o Python para enviar uma solicitação ao serviço Web em tempo real. 

1. Copie o exemplo de código a seguir em um novo arquivo Python.
2. Atualizar os parâmetros data, url e api_key
3. Execute o código. 

```python
import requests
import json

data = "{\"input_df\": [{\"feature1\": value1, \"feature2\": value2}]}"
body = str.encode(json.dumps(data))

url = 'http://<service ip address>:80/api/v1/service/<service name>/score'
api_key = 'your service key' 
headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}

resp = requests.post(url, data, headers=headers)
resp.text
```
