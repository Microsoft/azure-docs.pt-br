---
title: 'Tutorial: Receber dados do dispositivo por meio do Hub IoT do Azure'
description: Neste tutorial, você aprenderá a habilitar o roteamento de dados do dispositivo do Hub IoT para a API do Azure para FHIR por meio do Conector IoT do Azure para FHIR.
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: tutorial
ms.date: 11/13/2020
ms.author: punagpal
ms.openlocfilehash: 77d54ef0c9bef40af47c2fc48b9d4b5d6315b119
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780387"
---
# <a name="tutorial-receive-device-data-through-azure-iot-hub"></a>Tutorial: Receber dados do dispositivo por meio do Hub IoT do Azure

O Conector IoT do Azure para FHIR (Fast Healthcare Interoperability Resources&#174;)*fornece a você a capacidade de ingerir dados de dispositivos IoMT (Internet das Coisas Médicas) na API do Azure para FHIR. O guia de início rápido [Implantar o Conector IoT do Azure para FHIR (versão prévia) usando o portal do Azure](iot-fhir-portal-quickstart.md) mostrou um exemplo de dispositivo gerenciado pelo Azure IoT Central [enviando a telemetria](iot-fhir-portal-quickstart.md#connect-your-devices-to-iot) para o Conector IoT do Azure para FHIR. O conector IoT do Azure para FHIR também pode trabalhar com dispositivos provisionados e gerenciados por meio do Hub IoT do Azure. Este tutorial fornece o procedimento para conectar e rotear dados de dispositivo do Hub IoT do Azure para o conector IoT do Azure para FHIR.

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura ativa do Azure: [crie uma gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Recurso de API do Azure para FHIR com pelo menos um Conector IoT do Azure para FHIR – [implante o conector IoT do Azure para FHIR (versão prévia) usando o portal do Azure](iot-fhir-portal-quickstart.md)
- Recurso do Hub IoT do Azure conectado com dispositivos reais ou simulados: [Criar um hub IoT usando o portal do Azure](../../iot-hub/quickstart-send-telemetry-dotnet.md)

> [!TIP]
> Se estiver usando um aplicativo de dispositivo simulado do Hub IoT do Azure, fique à vontade para escolher seu aplicativo preferido entre diferentes linguagens e sistemas compatíveis.

## <a name="get-connection-string-for-azure-iot-connector-for-fhir-preview"></a>Obter cadeia de conexão para o conector IoT do Azure para FHIR (versão prévia)

O Hub IoT do Azure exige uma cadeia de conexão para se conectar com segurança ao Conector IoT do Azure para FHIR. Crie uma cadeia de conexão para o Conector IoT do Azure para FHIR, conforme descrito em [Gerar uma cadeia de conexão](iot-fhir-portal-quickstart.md#generate-a-connection-string). Preserve essa cadeia de conexão, pois ela será usada na próxima etapa.

O conector IoT do Azure para FHIR usa uma instância do Hub de Eventos do Azure nos bastidores para receber mensagens do dispositivo. A cadeia de conexão criada acima é basicamente a cadeia de conexão para esse Hub de Eventos subjacente.

## <a name="connect-azure-iot-hub-with-the-azure-iot-connector-for-fhir-preview"></a>Conectar o Hub IoT do Azure com o Conector IoT do Azure para FHIR (versão prévia)

O Hub IoT do Azure dá suporte a um recurso chamado [roteamento de mensagens](../../iot-hub/iot-hub-devguide-messages-d2c.md) que fornece a capacidade de enviar dados do dispositivo para vários serviços do Azure, como o Hub de Eventos, a conta de armazenamento e o Barramento de Serviço. O Conector IoT do Azure para FHIR aproveita esse recurso para se conectar e enviar dados do dispositivo do Hub IoT do Azure para o respectivo ponto de extremidade do Hub de Eventos.

> [!NOTE] 
> No momento, você só pode usar o comando do PowerShell ou da CLI para [criar o roteamento de mensagens](../../iot-hub/tutorial-routing.md), porque o Conector IoT do Azure para o Hub de Eventos do FHRI não está hospedado na assinatura do cliente e, portanto, não estará visível para você por meio do portal do Azure. No entanto, depois que os objetos de rota de mensagem são adicionados por meio do PowerShell ou da CLI, eles ficam visíveis no portal do Azure e podem ser gerenciados nele.

A configuração de um roteamento de mensagens consiste em duas etapas.

### <a name="add-an-endpoint"></a>Adicionar um ponto de extremidade
Essa etapa define um ponto de extremidade para o qual o Hub IoT roteará os dados. Crie esse ponto de extremidade usando o comando [Add-AzIotHubRoutingEndpoint](/powershell/module/az.iothub/Add-AzIotHubRoutingEndpoint) do PowerShell ou o comando [az iot hub routing-endpoint create](/cli/azure/iot/hub/routing-endpoint#az_iot_hub_routing_endpoint_create) da CLI, como preferir.

Esta é a lista de parâmetros a serem usados com o comando para criar um ponto de extremidade:

|Parâmetro do PowerShell|Parâmetro da CLI|Descrição|
|---|---|---|
|ResourceGroupName|resource-group|Nome do grupo de recursos do recurso do Hub IoT.|
|Nome|hub-name|Nome do recurso do Hub IoT.|
|EndpointName|endpoint-name|Use um nome que deseja atribuir ao ponto de extremidade que está sendo criado.|
|EndpointType|endpoint-type|Tipo de ponto de extremidade ao qual o Hub IoT precisa se conectar. Use o valor literal "EventHub" para o PowerShell e "eventhub" para a CLI.|
|EndpointResourceGroup|endpoint-resource-group|Nome do grupo de recursos para o recurso a API do Azure para FHIR do Conector IoT do Azure para FHIR. Obtenha esse valor na página Visão geral da API do Azure para FHIR.|
|EndpointSubscriptionId|endpoint-subscription-id|ID da assinatura para o recurso de API do Azure para FHIR do Conector IoT do Azure para FHIR. Obtenha esse valor na página Visão geral da API do Azure para FHIR.|
|ConnectionString|connection-string|Cadeia de conexão para o Conector IoT do Azure para FHIR. Use o valor obtido na etapa anterior.|

### <a name="add-a-message-route"></a>Adicionar uma rota de mensagens
Essa etapa define uma rota de mensagens usando o ponto de extremidade criado acima. Crie uma rota usando o comando [Add-AzIotHubRoute](/powershell/module/az.iothub/Add-AzIoTHubRoute) do PowerShell ou o comando [az iot hub route create](/cli/azure/iot/hub/route#az_iot_hub_route_create) da CLI, como preferir.

Esta é a lista de parâmetros a serem usados com o comando para adicionar uma rota de mensagem:

|Parâmetro do PowerShell|Parâmetro da CLI|Descrição|
|---|---|---|
|ResourceGroupName|g|Nome do grupo de recursos do recurso do Hub IoT.|
|Nome|hub-name|Nome do recurso do Hub IoT.|
|EndpointName|endpoint-name|Nome do ponto de extremidade criado acima.|
|RouteName|route-name|Um nome que você deseja atribuir à rota de mensagens que está sendo criada.|
|Fonte|source-type|Tipo de dados a ser enviado ao ponto de extremidade. Use o valor literal "DeviceMessages" para o PowerShell e "devicemessages" para a CLI.|

## <a name="send-device-message-to-iot-hub"></a>Enviar uma mensagem do dispositivo para o Hub IoT

Use seu dispositivo (real ou simulado) para enviar a mensagem de frequência cardíaca de exemplo mostrada abaixo para o Hub IoT do Azure. Essa mensagem será roteada para o Conector IoT do Azure para FHIR, em que será transformada em um recurso de Observação do FHIR e armazenada na API do Azure para FHIR.

```json
{
  "HeartRate": 80,
  "RespiratoryRate": 12,
  "HeartRateVariability": 64,
  "BodyTemperature": 99.08839032397609,
  "BloodPressure": {
    "Systolic": 23,
    "Diastolic": 34
  },
  "Activity": "walking"
}
```
> [!IMPORTANT]
> Envie a mensagem do dispositivo que está em conformidade com os [modelos de mapeamento](iot-mapping-templates.md) configurados com o Conector IoT do Azure para FHIR.

## <a name="view-device-data-in-azure-api-for-fhir"></a>Ver os dados do dispositivo na API do Azure para FHIR

Veja os recursos de Observação do FHIR criados pelo Conector IoT do Azure para FHIR na API do Azure para FHIR usando o Postman. Configure o [Postman para acessar a API do Azure para FHIR](access-fhir-postman-tutorial.md) e faça uma solicitação `GET` a `https://your-fhir-server-url/Observation?code=http://loinc.org|8867-4` para ver os recursos de Observação do FHIR com o valor de frequência cardíaca enviado na mensagem de exemplo acima.

> [!TIP]
> Verifique se o usuário tem acesso apropriado ao plano de dados da API do Azure para FHIR. Use o [Azure RBAC (controle de acesso baseado em função do Azure)](configure-azure-rbac.md) para atribuir as funções de plano de dados necessárias.


## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você vai configurar o Hub IoT do Azure para rotear dados do dispositivo para o Conector IoT do Azure para FHIR. Selecione uma das seguintes etapas abaixo para saber mais sobre o Conector IoT do Azure para FHIR:

entenda as diferentes fases do fluxo de dados no Conector IoT do Azure para FHIR.

>[!div class="nextstepaction"]
>[Fluxo de dados do Conector IoT do Azure para FHIR](iot-data-flow.md)

Saiba como configurar o Conector IoT usando modelos de mapeamento FHIR e do dispositivo.

>[!div class="nextstepaction"]
>[Modelos de mapeamento do Conector IoT do Azure para FHIR](iot-mapping-templates.md)

*No portal do Azure, o Conector IoT do Azure para FHIR é chamado de Conector IoT (versão prévia). FHIR é uma marca registrada da HL7, usada com permissão da HL7.