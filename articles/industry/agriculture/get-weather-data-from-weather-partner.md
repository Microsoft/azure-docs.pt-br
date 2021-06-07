---
title: Obter dados meteorológicos de parceiros meteorológicos
description: Este artigo descreve como obter dados meteorológicos de parceiros.
author: sunasing
ms.topic: article
ms.date: 03/31/2020
ms.author: sunasing
ms.openlocfilehash: bb28c517e353af6b8c1ee0cad788ff41b971918c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96460880"
---
# <a name="get-weather-data-from-weather-partners"></a>Obter dados meteorológicos de parceiros meteorológicos

O Azure FarmBeats ajuda a trazer dados meteorológicos de seus provedores de dados meteorológicos usando uma estrutura de conector baseada em Docker. Usando essa estrutura, os provedores de dados meteorológicos implementam um Docker que pode ser integrado ao FarmBeats. Atualmente, há suporte para o seguinte provedor de dados meteorológicos.

  ![Parceiros FarmBeats](./media/get-sensor-data-from-sensor-partner/dtn-logo.png)
  
   [DTN](https://www.dtn.com/dtn-content-integration/)

Os dados meteorológicos podem ser usados para gerar informações acionáveis e criar modelos de ia ou ML no FarmBeats.

## <a name="before-you-start"></a>Antes de começar

Para obter dados meteorológicos, verifique se você [instalou o FarmBeats](./install-azure-farmbeats.md). Há suporte para a integração do clima nas versões 1.2.11 e superior. 

## <a name="enable-weather-integration-with-farmbeats"></a>Habilitar a integração do clima com o FarmBeats

Para começar a obter dados meteorológicos em seu FarmBeats Datahub:

1. Vá para o FarmBeats Datahub Swagger `https://farmbeatswebsite-api.azurewebsites.net/swagger` .

2. Vá para a API/Partner e faça uma solicitação POST. Use a seguinte carga de entrada:

   ```json
   {  
 
     "dockerDetails": {  
       "credentials": { 
         "username": "<credentials to access private docker - not required for public docker>", 
         "password": "<credentials to access private docker – not required for public docker>"  
       },  
       "imageName" : "<docker image name",
       "imageTag" : "<docker image tag, default:latest>",
       "azureBatchVMDetails": {  
         "batchVMSKU" : "<VM SKU. Default is standard_d2_v2>",  
         "dedicatedComputerNodes" : 1,
         "nodeAgentSKUID": "<Node SKU. Default is batch.node.ubuntu 18.04>"  
       }
     },
     "partnerCredentials": {  
       "key1": "value1",  
       "key2": "value2"  
     },  
     "partnerType": "Weather",  
     "name": "<Name of the partner>",  
     "description": "<Description>",
     "properties": {  }  
   }  
   ```

   Por exemplo, para obter dados meteorológicos do DTN, use a seguinte carga. Você pode modificar o nome e a descrição de acordo com sua preferência.

   > [!NOTE]
   > A etapa a seguir requer uma chave de API. Para obter uma chave para sua assinatura do DTN, entre em contato com DTN.

   ```json
   {
 
     "dockerDetails": {
       "imageName": "dtnweather/dtn-farm-beats",
       "imageTag": "latest",
       "azureBatchVMDetails": {
         "batchVMSKU": "standard_d2_v2",
         "dedicatedComputerNodes": 1,
         "nodeAgentSKUID": "batch.node.ubuntu 18.04"
       }
     },
     "partnerCredentials": {
      "apikey": "<API key from DTN>"
      },
     "partnerType": "Weather",
     "name": "dtn-weather",
     "description": "DTN registered as a Weather Partner in FarmBeats"
   }  
   ```

   > [!NOTE]
   > Para obter mais informações sobre o objeto de parceiro, consulte o [Apêndice](get-weather-data-from-weather-partner.md#appendix) neste artigo.

   A etapa anterior provisionará os recursos para habilitar o Docker a ser executado no ambiente FarmBeats do cliente.  

   Leva cerca de 10 a 15 minutos para provisionar os recursos.

3. Verifique o status do objeto/Partner que você criou na etapa anterior. Para verificar o status, faça uma solicitação GET na API/Partner e verifique o status do objeto de parceiro. Depois que o FarmBeats provisiona o parceiro com êxito, o status é definido como **ativo**.

4. Na API do/JobType, faça uma solicitação GET. Verifique os trabalhos meteorológicos que você criou anteriormente no processo de adição de parceiro. Nos trabalhos meteorológicos, o campo **pipelinename** tem o seguinte formato: **Partner-name_partner-type_job-Name**.

      Agora, sua instância do FarmBeats tem um parceiro de dados meteorológico ativo. Você pode executar trabalhos para solicitar dados meteorológicos para um local específico (Latitude e longitude) e um intervalo de datas. Os tipos de trabalho terão detalhes sobre quais parâmetros são necessários para executar trabalhos meteorológicos.

      Por exemplo, para DTN, os seguintes tipos de trabalho serão criados:
   
      - **get_dtn_daily_observations**: Obtenha observações diárias para um local e um período de tempo.
      - **get_dtn_daily_forecasts**: Obtenha previsões diárias para um local e um período de tempo.
      - **get_dtn_hourly_observations**: obter observações por hora para um local e um período de tempo.
      - **get_dtn_hourly_forecasts**: obter previsões por hora para um local e um período de tempo.

6. Anote a ID e os parâmetros dos tipos de trabalho.

7. Vá para a API do/Jobs e faça uma solicitação POST em/Jobs. Use a seguinte carga de entrada:

   ```json
    {
         "typeId": "<id of the JobType>",
         "arguments": {
           "additionalProp1": {},
           "additionalProp2": {},
           "additionalProp3": {}
         },
         "name": "<name of the job>",
         "description": "<description>",
         "properties": {}
       }
   ```

   Por exemplo, para executar **get_dtn_daily_observations**, use a seguinte carga:

   ```json
   { 
         "typeId": "<id of the JobType>",
         "arguments": {
               "latitude": 47.620422,
               "longitude": -122.349358,
               "days": 5
         },
         "name": "<name of the job>",
         "description": "<description>",
         "properties": {}
   }
   ```

8. A etapa anterior executa os trabalhos meteorológicos conforme definido no Docker de parceiro e ingere dados meteorológicos no FarmBeats. Você pode verificar o status do trabalho fazendo uma solicitação GET em/Jobs. Na resposta, procure **CurrentState**. Quando você terminar, **CurrentState** será definido como **Succeeded**.

## <a name="query-ingested-weather-data"></a>Consultar dados meteorológicos ingeridos

Depois que os trabalhos meteorológicos forem concluídos, você poderá consultar os dados ingeridos do clima para criar modelos ou informações acionáveis usando APIs REST do FarmBeats Datahub.

Para consultar dados meteorológicos usando uma API REST do FarmBeats:

1. Em seu FarmBeats Datahub [Swagger](https://yourdatahub.azurewebsites.net/swagger), vá para a API/WeatherDataLocation e faça uma solicitação get. A resposta inclui objetos/WeatherDataLocation criados para o local (Latitude e longitude) que a execução do trabalho especificou. Anote a **ID** e a **weatherDataModelId** dos objetos.

2. Faça uma solicitação GET/{id} na API/WeatherDataModel para o **weatherDataModelId** como você fez anteriormente. O modelo de dados meteorológicos mostra todos os metadados e detalhes sobre os dados meteorológicos ingeridos. Por exemplo, no objeto de modelo de dados meteorológicos, a medida meteorológico detalha quais informações de clima são suportadas e em quais tipos e unidades. Por exemplo:

   ```json
   {
       "name": "Temperature <name of the weather measure - this is what we will receive as part of the queried weather data>",
       "dataType": "Double <Data Type - eg. Double, Enum>",
       "type": "AmbientTemperature <Type of measure eg. AmbientTemperature, Wind speed etc.>",
       "unit": "Celsius <Unit of measure eg. Celsius, Percentage etc.>",
       "aggregationType": "None <either of None, Average, Maximum, Minimum, StandardDeviation, Sum, Total>",
       "description": "<Description of the measure>"
   }
   ```

   Anote a resposta da chamada GET/{id} para o modelo de dados meteorológico.

3. Vá para a API de telemetria e faça uma solicitação POST. Use a seguinte carga de entrada:

   ```json
   {
       "weatherDataLocationId": "<id from step 1 above>",
     "searchSpan": {
       "from": "2020-XX-XXT07:30:00Z",
       "to": "2020-XX-XXT07:45:00Z"
     }
   }
   ```

    A resposta mostra os dados meteorológicos que estão disponíveis para o intervalo de tempo especificado:

   ```json
   {
     "timestamps": [
       "2020-XX-XXT07:30:00Z",
       "2020-XX-XXT07:45:00Z"
     ],
     "properties": [
       {
         "values": [
           "<id of the weatherDataLocation>",
           "<id of the weatherDataLocation>"
         ],
         "name": "Id",
         "type": "String"
       },
       {
         "values": [
           29.1,
           30.2
         ],
         "name": "Temperature <name of the WeatherMeasure as defined in the WeatherDataModel object>",
         "type": "Double <Data Type of the value - eg. Double>"
       }
     ]
   }
   ```

No exemplo anterior, a resposta mostra dados de dois carimbos de data/hora. Ele também mostra o nome da medida (temperatura) e os valores dos dados meteorológicos relatados nos dois carimbos de data/hora. Consulte o modelo de dados meteorológico associado para interpretar o tipo e a unidade dos valores relatados.

## <a name="troubleshoot-job-failures"></a>Solucionar falhas de trabalho

Para solucionar problemas de falhas de trabalho, [Verifique os logs de trabalho](troubleshoot-azure-farmbeats.md#weather-data-job-failures).


## <a name="appendix"></a>Apêndice

|        Partner (parceiro)   |  Detalhes   |
| ------- | -------             |
|     DockerDetails-imageName         |          Nome da imagem do Docker. Por exemplo, docker.io/mydockerimage (imagem em hub.docker.com) ou myazureacr.azurecr.io/mydockerimage (imagem no registro de contêiner do Azure) e assim por diante. Se nenhum registro for fornecido, o padrão será hub.docker.com.      |
|          DockerDetails - imageTag             |         Nome da marca da imagem do Docker. O padrão é "mais recente".     |
|  DockerDetails-credenciais      |  Credenciais para acessar o Docker privado. O parceiro fornece as credenciais.   |
|  DockerDetails - azureBatchVMDetails - batchVMSKU     |    SKU de VM do lote do Azure. Para obter mais informações, consulte [todas as máquinas virtuais Linux disponíveis](../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). <BR> <BR> Os valores válidos são ' Small ', ' ExtraLarge ', ' Large ', ' A8 ', ' a9 ', ' Medium ', ' a5 ', ' a6 ', ' a7 ', ' STANDARD_D1 ', ' STANDARD_D2 ', ' STANDARD_D3 ', ' STANDARD_D4 ', ' STANDARD_D11 ', ' STANDARD_D12 ', ' STANDARD_D13 ', ' STANDARD_D14 ', ' A10 ', ' a11 ', ' STANDARD_D1_V2 ', ' STANDARD_D2_V2 ', ' STANDARD_D3_V2 ', ' STANDARD_D4_V2 ', ' STANDARD_D11_V2 ', ' STANDARD_D12_V2 ', ' STANDARD_D13_V2 ', ' STANDARD_D14_V2 ', ' STANDARD_G1 ', ' STANDARD_G2 ', ' STANDARD_G3 ', ' STANDARD_G4 ' , ' STANDARD_G5 ', ' STANDARD_D5_V2 ', ' BASIC_A1 ', ' BASIC_A2 ', ' BASIC_A3 ', ' BASIC_A4 ', ' STANDARD_A1 ', ' STANDARD_A2 ', ' STANDARD_A3 ', ' STANDARD_A4 ', ' STANDARD_A5 ', ' STANDARD_A6 ', ' STANDARD_A7 ', ' STANDARD_A8 ', ' STANDARD_A9 ', ' STANDARD_A10 ', ' STANDARD_A11 ', ' STANDARD_D15_V2 ', ' STANDARD_F1 ', ' STANDARD_F2 ', ' STANDARD_F4 ', ' STANDARD_F8 ', ' STANDARD_F16 ', ' STANDARD_NV6 ', ' STANDARD_NV12 ', ' STANDARD_NV24 ', ' STANDARD_NC6 ', ' STANDARD_NC12 ', ' STANDARD_NC24 ', ' STANDARD_NC24r ' , ' STANDARD_H8 ', ' STANDARD_H8m ', ' STANDARD_H16 ', ' STANDARD_H16m ', ' STANDARD_H16mr ', ' STANDARD_H16r ', ' STANDARD_A1_V2 ', ' STANDARD_A2_V2 ', ' STANDARD_A4_V2 ', ' STANDARD_A8_V2 ', ' STANDARD_A2m_V2 ', ' STANDARD_A4m_V2 ', ' STANDARD_A8m_V2 ', ' STANDARD_M64ms ', ' STANDARD_M128s ' e ' STANDARD_D2_V3 '. *O padrão é ' STANDARD_D2_V2 '.*  |
|    DockerDetails - azureBatchVMDetails - dedicatedComputerNodes   |  Número de nós de computador dedicados por pool do lote. O valor padrão é 1. |
|    DockerDetails - azureBatchVMDetails - nodeAgentSKUID          |    ID de SKU do agente de nó do lote do Azure. Atualmente, há suporte apenas para o agente de nó do lote "Batch. Node. Ubuntu 18, 4".    |
| DockerDetails - partnerCredentials | Credenciais para chamar a API de parceiro no Docker. O parceiro fornece essas informações com base no mecanismo de autorização com suporte; por exemplo, nome de usuário e senha ou chaves de API. |
| parceirotype | "Clima". Outros tipos de parceiros no FarmBeats são "sensor" e "imagens".  |
|  name   |   Nome desejado do parceiro no sistema FarmBeats.   |
|  descrição |  Descrição.   |

## <a name="next-steps"></a>Próximas etapas

Agora que você consultou os dados do sensor de sua instância do Azure FarmBeats, saiba como [gerar mapas](generate-maps-in-azure-farmbeats.md#generate-maps) para seus farms.