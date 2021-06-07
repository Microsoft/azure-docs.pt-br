---
title: Usar a API do coletor de dados para criar um pipeline de dados
description: É possível usar a API do Coletor de Dados HTTP do Azure Monitor para adicionar dados JSON POST ao espaço de trabalho do Log Analytics de qualquer cliente que possa chamar a API REST. Este artigo descreve como carregar os dados armazenados em arquivos de uma maneira automatizada.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/09/2018
ms.openlocfilehash: ab2e9c691f17b8f0891ecbc82ff42cd3529a1328
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102031184"
---
# <a name="create-a-data-pipeline-with-the-data-collector-api"></a>Criar um pipeline de dados com a API do Coletor de Dados

O [API do coletor de dados do Azure Monitor](data-collector-api.md) permite que você importe quaisquer dados de log personalizados para um espaço de trabalho do Log Analytics no Azure Monitor. Os únicos requisitos são que os dados sejam formatados em JSON e divididos em segmentos de 30 MB ou menos. Este é um mecanismo completamente flexível que pode ser conectado de várias maneiras: de dados enviados diretamente do aplicativo a uploads ad hoc únicos. Este artigo descreverá alguns pontos de partida para um cenário comum: a necessidade de carregar os dados armazenados em arquivos de maneira regular e automatizada. Embora o pipeline apresentado aqui não seja aquele que tenha o melhor desempenho ou seja, de outro modo, otimizado, ele se destina a servir como um ponto de partida para a criação de um pipeline de produção próprio.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="example-problem"></a>Problema de exemplo
No restante deste artigo, examinaremos dados de exibição de página no Application Insights. Em nosso cenário hipotético, queremos correlacionar as informações geográficas coletadas por padrão pelo SDK do Application Insights a dados personalizados que contenham a população de cada país/região do mundo, com o objetivo de identificar onde devemos gastar mais os dólares de marketing. 

Usamos uma fonte de dados pública, como as [Perspectivas Demográficas Mundiais do ONU](https://esa.un.org/unpd/wpp/) para essa finalidade. Os dados terão o seguinte esquema simples:

![Esquema simples de exemplo](./media/create-pipeline-datacollector-api/example-simple-schema-01.png)

Em nosso exemplo, supomos que carregaremos um novo arquivo com os dados do último ano assim que eles ficarem disponíveis.

## <a name="general-design"></a>Design geral
Estamos usando uma lógica clássica do tipo ETL para criar nosso pipeline. A arquitetura será semelhante ao seguinte:

![Arquitetura de pipeline de coleta de dados](./media/create-pipeline-datacollector-api/data-pipeline-dataflow-architecture.png)

Este artigo não abordará como criar dados nem [carregá-los em uma conta do Armazenamento de Blobs do Azure](../../storage/blobs/storage-upload-process-images.md). Em vez disso, selecionaremos o fluxo assim que um novo arquivo for carregado no blob. A partir daqui:

1. Um processo detectará que novos dados foram carregados.  Nosso exemplo usa um [Aplicativo Lógico do Azure](../../logic-apps/logic-apps-overview.md), que tem disponível um gatilho para detectar o upload de novos dados em um blob.

2. Um processador lê esses novos dados e os converte em JSON, o formato exigido pelo Azure Monitor Neste exemplo, usamos uma [Azure Function](../../azure-functions/functions-overview.md) como uma maneira leve e econômica de executar nosso código de processamento. A função é iniciada pelo mesmo Aplicativo Lógico que usamos para detectar os novos dados.

3. Por fim, depois que o objeto JSON fica disponível, ele é enviado ao Azure Monitor. O mesmo Aplicativo Lógico envia os dados para o Azure Monitor usando a atividade interna do Coletor de Dados do Log Analytics.

Embora a configuração detalhada do armazenamento de blobs, do Aplicativo Lógico ou do Azure Function não seja descrita neste artigo, as instruções detalhadas estão disponíveis nas páginas dos produtos específicos.

Para monitorar esse pipeline, usamos o Application Insights para monitorar nosso Azure Function [detalhes aqui](../../azure-functions/functions-monitoring.md) e o Azure Monitor para monitorar nosso Aplicativo Lógico [detalhes aqui](../../logic-apps/monitor-logic-apps-log-analytics.md). 

## <a name="setting-up-the-pipeline"></a>Configurando o pipeline
Para definir o pipeline, primeiro crie e configure o contêiner de blob. Da mesma forma, crie o espaço de trabalho do Log Analytics para o qual deseja enviar os dados.

## <a name="ingesting-json-data"></a>Ingerindo dados JSON
A ingestão de dados JSON é simples com Aplicativos Lógicos e, como nenhuma transformação precisa ocorrer, podemos encerrar todo o pipeline em um único Aplicativo Lógico. Depois que o contêiner de blob e o espaço de trabalho do Log Analytics forem configurados, crie um Aplicativo Lógico e configure-o da seguinte maneira:

![Exemplo de fluxo de trabalho dos aplicativos lógicos](./media/create-pipeline-datacollector-api/logic-apps-workflow-example-01.png)

Salve o Aplicativo Lógico e continue para testá-lo.

## <a name="ingesting-xml-csv-or-other-formats-of-data"></a>Ingerindo XML, CSV ou outros formatos de dados
Hoje, os Aplicativos Lógicos não têm funcionalidades internas para transformar com facilidade XML, CSV ou outros tipos no formato JSON. Portanto, precisamos usar outro meio para realizar essa transformação. Neste artigo, usamos as funcionalidades de computação sem servidor do Azure Functions como uma maneira leve e econômica de fazer isso. 

Neste exemplo, analisamos um arquivo CSV, mas qualquer outro tipo de arquivo pode ser processado da mesma forma. Basta modificar a parte de desserialização do Azure Function para refletir a lógica correta para o tipo de dados específico.

1.  Crie um Azure Function, usando o runtime v1 do Function e baseado em consumo quando solicitado.  Selecione o modelo de **gatilho HTTP** direcionado ao C# como um ponto de partida que configura as associações, conforme necessário. 
2.  Na guia **Exibir Arquivos** no painel direito, crie um arquivo chamado **project.json** e cole o seguinte código dos pacotes NuGet que estamos usando:

    ![Projeto de exemplo do Azure Functions](./media/create-pipeline-datacollector-api/functions-example-project-01.png)
    
    ``` JSON
    {
      "frameworks": {
        "net46":{
          "dependencies": {
            "CsvHelper": "7.1.1",
            "Newtonsoft.Json": "11.0.2"
          }  
        }  
       }  
     }  
    ```

3. Alterne para **run.csx** no painel direito e substitua o código padrão pelo código a seguir. 

    >[!NOTE]
    >Para seu projeto, você precisa substituir o modelo de registro (a classe “PopulationRecord”) pelo próprio esquema de dados.
    >

    ```   
    using System.Net;
    using Newtonsoft.Json;
    using CsvHelper;
    
    class PopulationRecord
    {
        public String Location { get; set; }
        public int Time { get; set; }
        public long Population { get; set; }
    }

    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        string filePath = await req.Content.ReadAsStringAsync(); //get the CSV URI being passed from Logic App
        string response = "";

        //get a stream from blob
        WebClient wc = new WebClient();
        Stream s = wc.OpenRead(filePath);         

        //read the stream
        using (var sr = new StreamReader(s))
        {
            var csvReader = new CsvReader(sr);
    
            var records = csvReader.GetRecords<PopulationRecord>(); //deserialize the CSV stream as an IEnumerable
    
            response = JsonConvert.SerializeObject(records); //serialize the IEnumerable back into JSON
        }    

        return response == null
            ? req.CreateResponse(HttpStatusCode.BadRequest, "There was an issue getting data")
            : req.CreateResponse(HttpStatusCode.OK, response);
     }  
    ```

4. Salve a função.
5. Teste a função para garantir que o código esteja funcionando corretamente. Alterne para a guia **Testar** no painel direito, configurando o teste, conforme descrito a seguir. Coloque um link em um blob com os dados de exemplo na caixa de texto **Corpo da solicitação**. Depois de clicar em **Executar**, você deverá ver a saída em JSON na caixa **Saída**:

    ![Código de teste dos Aplicativos de funções](./media/create-pipeline-datacollector-api/functions-test-01.png)

Agora precisamos voltar e modificar o Aplicativo Lógico que começamos a criar anteriormente para incluir os dados ingeridos e convertidos no formato JSON.  Usando o Designer de Exibição, configure o Aplicativo Lógico da seguinte maneira e, em seguida, salve-o:

![Exemplo completo de fluxo de trabalho dos Aplicativos Lógicos](./media/create-pipeline-datacollector-api/logic-apps-workflow-example-02.png)

## <a name="testing-the-pipeline"></a>Testando o pipeline
Agora você pode carregar um novo arquivo no blob configurado anteriormente e monitorá-lo pelo Aplicativo Lógico. Em breve, você deverá ver uma nova instância do lançamento do Aplicativo Lógico, chamar o Azure Function e, em seguida, enviar os dados com êxito ao Azure Monitor. 

>[!NOTE]
>Pode levar até 30 minutos para que os dados sejam exibidos no Azure Monitor na primeira vez que você enviar um novo tipo de dados.


## <a name="correlating-with-other-data-in-log-analytics-and-application-insights"></a>Correlacionando com outros dados no Log Analytics e no Application Insights
Para concluir nossa meta de correlacionar os dados de exibição de página do Application Insights com os dados demográficos ingeridos de nossa fonte de dados personalizada, execute a seguinte consulta na janela Análise do Application Insights ou no espaço de trabalho do Log Analytics:

``` KQL
app("fabrikamprod").pageViews
| summarize numUsers = count() by client_CountryOrRegion
| join kind=leftouter (
   workspace("customdatademo").Population_CL
) on $left.client_CountryOrRegion == $right.Location_s
| project client_CountryOrRegion, numUsers, Population_d
```

A saída deverá mostrar as duas fontes de dados agora unidas.  

![Correlacionando dados separados em um exemplo de resultado da pesquisa](./media/create-pipeline-datacollector-api/correlating-disjoined-data-example-01.png)

## <a name="suggested-improvements-for-a-production-pipeline"></a>Sugestões de melhorias para um pipeline de produção
Este artigo apresentou um protótipo funcional, cuja lógica por trás pode ser aplicada em busca de uma verdadeira solução de qualidade de produção. Para uma solução de qualidade de produção como essa, as seguintes melhorias são recomendadas:

* Adicione o tratamento de erro e a lógica de repetição no Aplicativo Lógico e no Function.
* Adicione a lógica para garantir que o limite de 30 MB/única chamada à API da Ingestão do Log Analytics não seja excedido. Divida os dados em segmentos menores, se necessário.
* Configure uma política de limpeza no armazenamento de blobs. Depois de enviados com êxito ao espaço de trabalho do Log Analytics, a menos que você deseje manter os dados brutos disponíveis para fins de arquivamento, não há nenhum motivo para continuar os armazenando. 
* Verifique se o monitoramento está habilitado no pipeline completo, a adição de pontos de rastreamento e alertas, conforme apropriado.
* Aproveite o controle do código-fonte para gerenciar o código para a função e o Aplicativo Lógico.
* Verifique se uma política de gerenciamento de alterações adequada está sendo seguida, de modo que, se o esquema for alterado, a função e os Aplicativos Lógicos sejam modificados de acordo.
* Se você estiver carregando vários tipos de dados diferentes, separe-os em pastas individuais no contêiner de blob e crie a lógica para distribuir a lógica com base no tipo de dados. 


## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre a [API do Coletor de Dados](data-collector-api.md) para gravar dados no espaço de trabalho do Log Analytics por meio de qualquer cliente de API REST.
