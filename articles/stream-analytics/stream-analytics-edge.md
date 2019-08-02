---
title: O Stream Analytics do Azure no IoT Edge
description: Crie trabalhos do Edge no Azure Stream Analytics e implante-os nos dispositivos em execução no Azure IoT Edge.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 07/01/2019
ms.custom: seodec18
ms.openlocfilehash: 8e3b6d0fbefb8e3d3437fd5e24f929e453c573df
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621008"
---
# <a name="azure-stream-analytics-on-iot-edge"></a>O Stream Analytics do Azure no IoT Edge
 
O ASA (Azure Stream Analytics) no IoT Edge oferece aos desenvolvedores a capacitação necessária para implantar inteligência analítica quase em tempo real próxima à dispositivos IoT, assim eles podem desbloquear o valor integral de dados gerados por dispositivos. O Azure Stream Analytics foi projetado para baixa latência, resiliência, uso eficiente da largura de banda e conformidade. Agora as empresas podem implantar a lógica de controle perto das operações industriais e complementar a análise de Big Data feita na nuvem.  

Azure Stream Analytics no IoT Edge executado dentro da estrutura do [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/). Depois que o trabalho é criado no ASA, você pode implantá-lo e gerenciá-lo usando um Hub IoT.

## <a name="scenarios"></a>Cenários
![Diagrama de alto nível do IoT Edge](media/stream-analytics-edge/ASAedge-highlevel-diagram.png)

* **Controle e comando de baixa latência**: por exemplo, sistemas de segurança de fabricação devem reagir aos dados operacionais com latência extremamente baixa. Com o ASA no IoT Edge, você pode analisar dados de sensor quase em tempo real e emitir comandos ao detectar anomalias para interromper um computador ou disparar gatilho.
*   **Conectividade limitada com a nuvem**: sistemas críticos, como equipamentos de mineração remotos, embarcações conectadas ou perfuração offshore, precisam analisar e reagir aos dados, mesmo quando a conectividade de nuvem é intermitente. Com o ASA, a lógica de fluxo é executado independentemente da conectividade de rede, e você pode escolher o que envia para a nuvem para processamento adicional ou armazenamento.
* **Largura de banda limitada**: o volume de dados produzidos por motores a jato ou carros conectados pode ser tão grande que os dados devem ser filtrados ou pré-processados antes de serem enviados para a nuvem. Usando o ASA, você pode filtrar ou agregar os dados que precisam ser enviados para a nuvem.
* **Conformidade**: a conformidade normativa pode exigir que alguns dados sejam mantidos anônimos localmente ou agregados antes de serem enviados para a nuvem.

## <a name="edge-jobs-in-azure-stream-analytics"></a>Trabalhos de borda no Azure Stream Analytics
### <a name="what-is-an-edge-job"></a>O que é um trabalho de "borda"?

Trabalhos ASA Edge são executados em contêineres implantados nos [dispositivos do Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works). Eles são compostos de duas partes:
1.  Uma parte de nuvem que é responsável pela definição de trabalho: os usuários definem entradas, saída, consulta e outras configurações (eventos fora de ordem etc.) na nuvem.
2.  Um módulo em execução em seus dispositivos IoT. Ele contém o mecanismo do ASA e recebe a definição de trabalho da nuvem. 

O ASA usa o Hub IoT para implantar os trabalhos de borda para o(s) dispositivo(s). Mais informações sobre a [implantação do IoT Edge podem ser vistas aqui](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).

![Trabalho do Azure Stream Analytics no Edge](media/stream-analytics-edge/stream-analytics-edge-job.png)


### <a name="installation-instructions"></a>Instruções de instalação
As etapas de alto nível são descritas na tabela a seguir. Mais detalhes são fornecidos nas seções a seguir.

|      |Etapa   | Observações   |
| ---   | ---   |  ---      |
| 1   | **Criar um contêiner de armazenamento**   | Contêineres de armazenamento são usados para salvar a definição de trabalho em que eles podem ser acessados por dispositivos IoT. <br>  Você pode reutilizar qualquer contêiner de armazenamento existente.     |
| 2   | **Criar um trabalho de borda ASA**   |  Crie um novo trabalho e selecione **Borda** como **ambiente de hospedagem**. <br> Esses trabalhos são criados/gerenciados na nuvem e executados em seus próprios dispositivos IoT Edge.     |
| 3   | **Configurar o ambiente IoT Edge nos dispositivos**   | Instruções para [Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) ou [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux).          |
| 4   | **Implantar o ASA em dispositivos IoT Edge**   |  A definição de trabalho ASA é exportada para o contêiner de armazenamento criado anteriormente.       |

Você pode seguir [este tutorial passo a passo](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics) para implantar seu primeiro trabalho ASA no IoT Edge. O vídeo a seguir deve ajudar a compreender o processo para executar um trabalho do Azure Stream Analytics em um dispositivo de borda IoT:  


> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T157/player]

#### <a name="create-a-storage-container"></a>Criar um contêiner de armazenamento
Um contêiner de armazenamento é necessário para exportar a consulta compilada por ASA e a configuração de trabalho. Ele é usado para configurar a imagem do Docker ASA com sua consulta específica. 
1. Execute [estas instruções](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account) para criar uma conta de armazenamento por meio do portal do Azure. Você pode manter todas as opções padrão para usar essa conta com o ASA.
2. Na conta de armazenamento recém-criada, crie um contêiner de armazenamento de blobs:
    1. Clique em **Blobs** e em **+ Contêiner**. 
    2. Insira um nome e mantenha o contêiner como **Privado**.

#### <a name="create-an-asa-edge-job"></a>Criar um trabalho de Borda ASA
> [!Note]
> Este tutorial se concentra na criação do trabalho ASA usando o Portal do Azure. Você também pode [usar o plug-in do Visual Studio para criar um trabalho de Borda do ASA](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-edge-jobs)

1. No portal do Azure, crie um novo "trabalho Stream Analytics". [Link direto para criar um novo trabalho ASA aqui](https://ms.portal.azure.com/#create/Microsoft.StreamAnalyticsJob).

2. Na tela de criação, selecione **Edge** como **ambiente de hospedagem** (veja a imagem a seguir)

   ![Crie trabalho do Stream Analytics no Edge](media/stream-analytics-edge/create-asa-edge-job.png)
3. Definição de trabalho
    1. **Definir Fluxo(s) de Entrada**. Defina um ou mais fluxos de entrada para o trabalho.
    2. Defina Dados de referência (opcionais).
    3. **Definir Fluxo(s) de Saída**. Defina um ou vários fluxos de saída para o trabalho. 
    4. **Definir consulta**. Defina a consulta ASA na nuvem usando o editor embutido. O compilador verifica automaticamente a sintaxe habilitada para a borda ASA. Você também pode testar a consulta carregando os dados de exemplo. 

4. Defina as informações de contêiner de armazenamento no menu **Configurações do IoT Edge**.

5. Definir configurações opcionais
    1. **Ordenação de eventos**. Você pode configurar a política fora de ordem no portal. A documentação está disponível [aqui](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics).
    2. **Localidade**. Defina o formato de internacionalização.



> [!Note]
> Quando uma implantação é criada, o ASA exporta a definição de trabalho para um contêiner de armazenamento. Esta definição de trabalho permanece a mesma em toda a duração da implantação. Como consequência, se você quiser atualizar um trabalho em execução na borda, precisará editar o trabalho no ASA e criar uma nova implantação no Hub IoT.


#### <a name="set-up-your-iot-edge-environment-on-your-devices"></a>Configurar o ambiente IoT Edge no(s) dispositivo(s)
Os trabalhos de borda podem ser implantados em dispositivos que executam o Azure IoT Edge.
Para isso, você precisa seguir estas etapas:
- Crie um Hub IoT.
- Instale o docker e o tempo de execução do IoT Edge nos dispositivos de borda.
- Defina os dispositivos como **Dispositivos IoT Edge** no Hub IoT.

Essas etapas são descritas na documentação do IoT Edge para [Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) ou [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux).  


####  <a name="deployment-asa-on-your-iot-edge-devices"></a>Implantação do ASA em dispositivos IoT Edge
##### <a name="add-asa-to-your-deployment"></a>Adicionar ASA à sua implantação
- No portal do Azure, abra o Hub IoT, navegue até **IoT Edge** e clique no dispositivo que você deseja como destino para essa implantação.
- Selecione **Definir módulos**, em seguida, selecione **+ Adicionar** e escolha **Módulo do Azure Stream Analytics**.
- Selecione a assinatura e o trabalho do ASA no Edge criado. Clique em Salvar.
![Adicionar módulo ASA em sua implantação](media/stream-analytics-edge/add-stream-analytics-module.png)


> [!Note]
> Durante esta etapa, o ASA cria uma pasta chamada "EdgeJobs" no contêiner de armazenamento (se ela ainda não existe). Para cada tipo de implantação, uma nova subpasta é criada na pasta "EdgeJobs".
> Quando você implanta seu trabalho em dispositivos IoT Edge, o ASA cria uma assinatura de acesso compartilhado (SAS) para o arquivo de definição de trabalho. A chave SAS é transmitida com segurança para os dispositivos IoT Edge usando o dispositivo gêmeo. A expiração dessa chave é de três anos a partir do dia de sua criação. Quando você atualiza um trabalho de borda de IoT, a SAS será alterado, mas a versão da imagem não será alterado. Depois que você **atualizar**, siga o fluxo de trabalho de implantação e uma notificação de atualização é registrada no dispositivo.


Para obter mais detalhes sobre implantações do IoT Edge, confira [esta página](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).


##### <a name="configure-routes"></a>Configurar rotas
O IoT Edge fornece uma maneira para rotear de modo declarativo as mensagens entre módulos e entre módulos e o Hub IoT. A sintaxe completa é descrita [aqui](https://docs.microsoft.com/azure/iot-edge/module-composition).
Os nomes das entradas e saídas criadas no trabalho ASA podem ser usados como pontos de extremidade para roteamento.  

###### <a name="example"></a>Exemplo

```json
{
    "routes": {
        "sensorToAsa":   "FROM /messages/modules/tempSensor/* INTO BrokeredEndpoint(\"/modules/ASA/inputs/temperature\")",
        "alertsToCloud": "FROM /messages/modules/ASA/* INTO $upstream",
        "alertsToReset": "FROM /messages/modules/ASA/* INTO BrokeredEndpoint(\"/modules/tempSensor/inputs/control\")"
    }
}

```
Este exemplo mostra as rotas para o cenário descrito na figura a seguir. Ele contém um trabalho de borda chamado "**ASA**", com uma entrada denominada "**temperatura**" e uma saída nomeada "**alerta**".
![Exemplo de diagrama de roteamento de mensagens](media/stream-analytics-edge/edge-message-routing-example.png)

Este exemplo define as seguintes rotas:
- Cada mensagem do **tempSensor** é enviada para o módulo chamado **ASA** para a entrada denominada **temperatura**,
- Todas as saídas do módulo **ASA** são enviadas para o Hub IoT vinculado a este dispositivo (upstream$),
- Todas as saídas do módulo **ASA** são enviadas para o ponto de extremidade **control** de **tempSensor**.


## <a name="technical-information"></a>Informações técnicas
### <a name="current-limitations-for-iot-edge-jobs-compared-to-cloud-jobs"></a>Limitações atuais dos trabalhos do IoT Edge em comparação com os trabalhos de nuvem
O objetivo é ter a paridade entre os trabalhos do IoT Edge e os trabalhos de nuvem. A maioria dos recursos de linguagem de consulta SQL têm suporte, permitindo que para executar a mesma lógica na nuvem e do IoT Edge.
No entanto, os recursos a seguir ainda não têm suporte para trabalhos de borda:
* Funções definidas pelo usuário (UDF) no JavaScript. Os UDF estão disponíveis no [C# para trabalhos do IoT Edge](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-edge-csharp-udf) (versão prévia).
* Agregações definidas pelo usuário (UDA).
* Funções do Azure ML
* Uso de mais de 14 agregações em uma única etapa.
* Formato AVRO para entrada/saída. No momento, há suporte apenas para CSV e JSON.
* Os seguintes operadores SQL:
    * PARTIÇÃO POR
    * GetMetadataPropertyValue


### <a name="runtime-and-hardware-requirements"></a>Requisitos de hardware e de tempo de execução
Para executar o ASA no IoT Edge, você precisa de dispositivos que possam executar o [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/). 

O ASA e o Azure IoT Edge usam contêineres **Docker** para fornecer uma solução portátil que é executada em vários sistemas operacionais host (Windows, Linux).

O ASA no IoT Edge é disponibilizado como imagens do Windows e do Linux, em execução em arquiteturas x86-64 ou ARM (Máquinas RISC Avançadas). 


### <a name="input-and-output"></a>Entrada e saída
#### <a name="input-and-output-streams"></a>Fluxos de Entrada e Saída
Os trabalhos ASA Edge podem obter entradas e saídas de outros módulos em execução em dispositivos de IoT Edge. Para se conectar de e para módulos específicos, você pode definir a configuração de roteamento no momento da implantação. Mais informações são descritas na [documentação do módulo de composição do IoT Edge](https://docs.microsoft.com/azure/iot-edge/module-composition).

Para entradas e saídas, há suporte para os formatos CSV e JSON.

Para cada fluxo de entrada e saída que você cria no trabalho ASA, um ponto de extremidade correspondente é criado no módulo implantado. Esses pontos de extremidade podem ser usados nas rotas da implantação.

No momento, os únicos tipos de entrada e saída de fluxo com suporte são do Hub do Edge. A entrada de referência dá suporte ao tipo de arquivo de referência. Outras saídas podem ser contatadas por meio de um downstream do trabalho de nuvem. Por exemplo, um trabalho do Stream Analytics hospedado no Edge envia a saída para o Hub do Edge, que pode então enviar a saída para o Hub IoT. Você pode usar um segundo trabalho do Azure Stream Analytics hospedado em nuvem com entrada do Hub IoT e saída para o Power BI ou outro tipo de saída.



##### <a name="reference-data"></a>Dados de referência
Os dados de referência (também conhecidos como uma tabela de pesquisa) são um conjunto de dados finito estático ou com alteração lenta por natureza. Ele é usado para executar uma pesquisa ou para se correlacionar ao fluxo de dados. Para usar dados de referência no seu trabalho do Azure Stream Analytics geralmente é preciso usar uma [União de dados de referência](https://docs.microsoft.com/stream-analytics-query/reference-data-join-azure-stream-analytics) em sua consulta. Para obter mais informações, consulte o [Usando dados de referência para pesquisas no Stream Analytics](stream-analytics-use-reference-data.md).

Apenas dados de referência local são suportados. Quando um trabalho é implantado no dispositivo IoT Edge, ele carrega dados de referência do caminho do arquivo definido pelo usuário.

Para criar um trabalho com dados de referência no Edge:

1. Crie uma nova entrada para seu trabalho.

2. Escolha **Dados de referência** como o **Tipo de Fonte**.

3. Tenha um arquivo de dados de referência pronto no dispositivo. Para um contêiner do Windows, coloque o arquivo de dados de referência na unidade local e compartilhe a unidade local com o contêiner Docker. Para um contêiner do Linux, crie um volume do Docker e preencha o arquivo de dados para o volume.

4. Defina o caminho do arquivo. Para o sistema operacional de Host do Windows e o contêiner do Windows, use o caminho absoluto: `E:\<PathToFile>\v1.csv`. Para um sistema operacional de Host do Windows e um contêiner do Linux ou um sistema operacional do Linux e um contêiner do Linux, use o caminho no volume: `<VolumeName>/file1.txt`.

![Nova entrada de dados de referência para o job do Azure Stream Analytics no IoT Edge](./media/stream-analytics-edge/Reference-Data-New-Input.png)

Os dados de referência na atualização do IoT Edge são acionados por uma implantação. Uma vez acionado, o módulo ASA seleciona os dados atualizados sem interromper o trabalho em execução.

Existem duas maneiras de atualizar os dados de referência:
* Atualize o caminho de dados de referência em sua tarefa ASA do portal do Azure.
* Atualize a implantação do IoT Edge.

## <a name="license-and-third-party-notices"></a>Licenças e notificações de terceiros
* [Licença do Azure Stream Analytics no IoT Edge](https://go.microsoft.com/fwlink/?linkid=862827). 
* [Aviso de terceiros para o Azure Stream Analytics no IoT Edge](https://go.microsoft.com/fwlink/?linkid=862828).

## <a name="azure-stream-analytics-module-image-information"></a>Informações de imagem de módulo do Stream Analytics do Azure 

Última atualização: essas informações de versão de 2019-06-27:

- Imagem: `asaedge.azurecr.io/public/azure-stream-analytics/azureiotedge:1.0.3-linux-amd64`
   - imagem base: microsoft/dotnet:2.1.6-runtime-alpine3.7
   - Plataforma:
      - architecture: amd64
      - os: linux
  
- Imagem: `asaedge.azurecr.io/public/azure-stream-analytics/azureiotedge:1.0.3-linux-arm32v7`
   - imagem base: microsoft/dotnet:2.1.6-runtime-bionic-arm32v7
   - Plataforma:
      - arquitetura: arm
      - os: linux
  
- Imagem: `asaedge.azurecr.io/public/azure-stream-analytics/azureiotedge:1.0.3-windows-amd64`
   - imagem base: microsoft/dotnet:2.1.6-runtime-nanoserver-1809
   - Plataforma:
      - architecture: amd64
      - sistema operacional: windows
      
      
## <a name="get-help"></a>Obter ajuda
Para obter mais assistência, experimente o [fórum do Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Próximas etapas

* [Mais informações sobre o Azure Iot Edge](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works)
* [ASA no tutorial de IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics)
* [Desenvolver trabalhos de Borda do Stream Analytics utilizando ferramentas do Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-edge-jobs)
* [Implementar CI/CD para Stream Analytics usando as APIs](stream-analytics-cicd-api.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
