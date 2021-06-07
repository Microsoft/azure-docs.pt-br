---
title: 'Início Rápido: Implantar o Conector IoT do Azure para FHIR (versão prévia) usando o portal do Azure'
description: Neste guia de início rápido, você aprenderá a implantar, configurar e usar o recurso do Conector IoT do Azure para FHIR da API do Azure para FHIR por meio do portal do Azure.
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: quickstart
ms.date: 04/05/2021
ms.author: punagpal
ms.openlocfilehash: 0a382ad948e7fd2efc6ab59eb94da3d6cbb210d7
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106443757"
---
# <a name="quickstart-deploy-azure-iot-connector-for-fhir-preview-using-azure-portal"></a>Início Rápido: Implantar o Conector IoT do Azure para FHIR (versão prévia) usando o portal do Azure

O Conector IoT do Azure para FHIR (Fast Healthcare Interoperability Resources&#174;)* é um recurso opcional da API do Azure para FHIR que fornece a capacidade de ingerir dados de dispositivos IoMT (Internet das Coisas Médicas). Durante a fase de versão prévia, o recurso Conector IoT do Azure para FHIR fica disponível gratuitamente. Neste início rápido, você aprenderá a:
- Implantar e configurar o Conector IoT do Azure para FHIR usando o portal do Azure
- Usar um dispositivo simulado para enviar dados ao Conector IoT do Azure para FHIR
- Exibir recursos criados pelo Conector IoT do Azure para FHIR na API do Azure para FHIR

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura ativa do Azure: [crie uma gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Recurso da API do Azure para FHIR – [Implantar a API do Azure para FHIR usando o portal do Azure](fhir-paas-portal-quickstart.md)

## <a name="go-to-azure-api-for-fhir-resource"></a>Acessar o recurso da API do Azure para FHIR

Abra o [portal do Azure](https://portal.azure.com) e acesse o recurso da **API do Azure para FHIR** para o qual deseja criar o recurso do Conector IoT do Azure para FHIR.

[![Recurso da API do Azure para FHIR](media/quickstart-iot-fhir-portal/portal-azure-api-fhir.jpg)](media/quickstart-iot-fhir-portal/portal-azure-api-fhir.jpg#lightbox)

No menu de navegação à esquerda, clique em **Conector IoT (versão prévia)** na seção **Suplementos** para abrir a página **Conectores IoT**.

[![Recurso do Conector IoT](media/quickstart-iot-fhir-portal/portal-iot-connectors.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connectors.jpg#lightbox)

## <a name="create-new-azure-iot-connector-for-fhir-preview"></a>Criar um Conector IoT do Azure para FHIR (versão prévia)

Clique no botão **Adicionar** para abrir a página **Criar Conector IoT**.

[![Adicionar Conector IoT](media/quickstart-iot-fhir-portal/portal-iot-connectors-add.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connectors-add.jpg#lightbox)

Insira as configurações para o novo Conector IoT do Azure para FHIR. Clique no botão **Criar** e aguarde a implantação do Conector IoT do Azure para FHIR.

> [!NOTE]
> É necessário selecionar **Criar** como o valor no menu suspenso do **Tipo de resolução** para esta instalação. 

[![Criar Conector IoT](media/quickstart-iot-fhir-portal/portal-iot-connector-create.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-create.jpg#lightbox)

|Configuração|Valor|Descrição |
|---|---|---|
|Nome do conector|Um nome exclusivo|Insira um nome para identificar o Conector IoT do Azure para FHIR esse nome deve ser exclusivo em um recurso de API do Azure para FHIR. O nome pode conter apenas letras minúsculas, números e o caractere de hífen (-). Ele precisa começar e terminar com uma letra ou um número e precisa ter entre 3 e 24 caracteres.|
|Tipo de resolução|Pesquisa ou criação|Selecione **Pesquisa** se você tiver um processo fora de banda para criar recursos FHIR de [Dispositivo](https://www.hl7.org/fhir/device.html) e [Paciente](https://www.hl7.org/fhir/patient.html) na API do Azure para FHIR. O Conector IoT do Azure para FHIR usará a referência a esses recursos ao criar um recurso FHIR de [Observação](https://www.hl7.org/fhir/observation.html) para declarar os dados do dispositivo. Selecione **Criar** quando desejar que o Conector IoT do Azure para FHIR crie recursos básicos de Dispositivo e de Paciente na API do Azure para FHIR usando os respectivos valores de identificador presentes nos dados do dispositivo.|

Quando a instalação for concluída, o Conector IoT do Azure para FHIR recém-criado será exibido na página **Conectores IoT**.

[![Conector IoT criado](media/quickstart-iot-fhir-portal/portal-iot-connector-created.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-created.jpg#lightbox)

## <a name="configure-azure-iot-connector-for-fhir-preview"></a>Configurar o Conector IoT do Azure para FHIR (versão prévia)

O Conector IoT do Azure para FHIR precisa de dois modelos de mapeamento para transformar as mensagens do dispositivo em recursos de Observação baseados no FHIR: **mapeamento de dispositivo** e **mapeamento FHIR**. O Conector IoT do Azure para FHIR não ficará totalmente operacional até que esses mapeamentos sejam carregados.

[![Mapeamentos ausentes do Conector IoT](media/quickstart-iot-fhir-portal/portal-iot-connector-missing-mappings.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-missing-mappings.jpg#lightbox)

Para carregar modelos de mapeamento, clique no Conector IoT do Azure para FHIR recém-implantado para acessar a página **Conector IoT**.

[![Clique no Conector IoT](media/quickstart-iot-fhir-portal/portal-iot-connector-click.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-click.jpg#lightbox)

#### <a name="device-mapping"></a>Mapeamento de dispositivo

O modelo de mapeamento de dispositivo transforma os dados do dispositivo em um esquema normalizado. Na página **Conector IoT**, clique no botão **Configurar mapeamento de dispositivo** para acessar a página **Mapeamento de dispositivo**. 

[![Clique no Conector IoT e Configurar mapeamento de dispositivo](media/quickstart-iot-fhir-portal/portal-iot-connector-click-device-mapping.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-click-device-mapping.jpg#lightbox)

Na página **Mapeamento de dispositivo**, adicione o script a seguir ao editor de JSON e clique em **Salvar**.

```json
{
  "templateType": "CollectionContent",
  "template": [
    {
      "templateType": "IotJsonPathContent",
      "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@Body.telemetry.HeartRate)]",
        "patientIdExpression": "$.Properties.iotcentral-device-id",
        "values": [
          {
            "required": "true",
            "valueExpression": "$.Body.telemetry.HeartRate",
            "valueName": "hr"
          }
        ]
      }
    }
  ]
}
```

[![Mapeamento de dispositivo do Conector IoT](media/quickstart-iot-fhir-portal/portal-iot-device-mapping.jpg)](media/quickstart-iot-fhir-portal/portal-iot-device-mapping.jpg#lightbox)

#### <a name="fhir-mapping"></a>Mapeamento FHIR

O modelo de mapeamento FHIR transforma uma mensagem normalizada em um recurso de Observação baseado no FHIR. Na página **Conector IoT**, clique no botão **Configurar mapeamento FHIR** para acessar a página **Mapeamento FHIR**.  

[![Clique no Conector IoT e Configurar mapeamento FHIR](media/quickstart-iot-fhir-portal/portal-iot-connector-click-fhir-mapping.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-click-fhir-mapping.jpg#lightbox)

Na página **Mapeamento FHIR**, adicione o script a seguir ao editor de JSON e clique em **Salvar**.

```json
{
  "templateType": "CollectionFhir",
  "template": [
    {
      "templateType": "CodeValueFhir",
      "template": {
        "codes": [
          {
            "code": "8867-4",
            "system": "http://loinc.org",
            "display": "Heart rate"
          }
        ],
        "periodInterval": 0,
        "typeName": "heartrate",
        "value": {
          "unit": "count/min",
          "valueName": "hr",
          "valueType": "Quantity"
        }
      }
    }
  ]
}
```

[![Mapeamento FHIR do Conector IoT](media/quickstart-iot-fhir-portal/portal-iot-fhir-mapping.jpg)](media/quickstart-iot-fhir-portal/portal-iot-fhir-mapping.jpg#lightbox)

## <a name="generate-a-connection-string"></a>Criar uma cadeia de conexão

O dispositivo IoMT precisa de uma cadeia de conexão para se conectar e enviar mensagens ao Conector IoT do Azure para FHIR. Na página **Conector IoT** do Conector IoT do Azure para FHIR recém-implantado, selecione o botão **Gerenciar conexões de cliente**. 

[![Clique no Conector IoT e Gerenciar conexões de cliente](media/quickstart-iot-fhir-portal/portal-iot-connector-click-client-connections.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-click-client-connections.jpg#lightbox)

Na página **Conexões**, clique no botão **Adicionar** para criar uma conexão. 

[![Conexões do Conector IoT](media/quickstart-iot-fhir-portal/portal-iot-connections.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connections.jpg#lightbox)

Forneça um nome amigável para essa conexão na janela de sobreposição e selecione o botão **Criar**.

[![Nova conexão do Conector IoT](media/quickstart-iot-fhir-portal/portal-iot-new-connection.jpg)](media/quickstart-iot-fhir-portal/portal-iot-new-connection.jpg#lightbox)

Selecione a conexão recém-criada na página **Conexões** e copie o valor do campo **Cadeia de conexão primária** da janela de sobreposição à direita.

[![Cadeia de conexão do Conector IoT](media/quickstart-iot-fhir-portal/portal-iot-connection-string.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connection-string.jpg#lightbox)

Preserve essa cadeia de conexão, pois ela será usada em uma etapa posterior. 

## <a name="connect-your-devices-to-iot"></a>Conectar seus dispositivos à IoT

O Azure oferece um amplo pacote de produtos de IoT para conectar e gerenciar seus dispositivos IoT. Você pode criar uma solução própria baseada em PaaS usando o Hub IoT do Azure ou comece a gerenciar uma plataforma de aplicativos IoT com o Azure IoT Central. Para este tutorial, aproveitaremos o Azure IoT Central, que tem modelos de solução voltados para o setor para ajudar você a começar.

Implante o [Modelo de aplicativo de monitoramento contínuo de pacientes](../../iot-central/healthcare/tutorial-continuous-patient-monitoring.md#create-an-application-template). Esse modelo inclui dois dispositivos simulados que produzem dados em tempo real para ajudar você a começar: **Patch de Sinais Vitais Inteligente** e **Braçadeira de Joelho Inteligente**.

> [!NOTE]
> Sempre que os seus dispositivos reais estiverem prontos, você poderá usar o mesmo aplicativo IoT Central para [integrar seus dispositivos](../../iot-central/core/howto-set-up-template.md) e substituir os simuladores de dispositivo. Os dados do dispositivo também começarão a fluir automaticamente para o FHIR. 

## <a name="connect-your-iot-data-with-the-azure-iot-connector-for-fhir-preview"></a>Conectar seus dados de IoT com o Conector IoT do Azure para FHIR (versão prévia)

Depois de implantar seu aplicativo do IoT Central, seus dois dispositivos simulados prontos para uso começarão a gerar telemetria. Para este tutorial, vamos ingerir a telemetria do simulador *Patch de Sinais Vitais Inteligente* no FHIR por meio do Conector IoT do Azure para FHIR. Para exportar seus dados de IoT para o Conector IoT do Azure para FHIR, o ideal é [configurar uma exportação contínua de dados no IoT Central](../../iot-central/core/howto-export-data.md). Primeiro, precisamos criar uma conexão com o destino e, em seguida, criaremos um trabalho de exportação de dados para execução contínua: 

> [!NOTE]
> Você desejará selecionar **exportação de dados** versus **exportação de dados (herdada)** dentro das configurações do aplicativo IOT central para esta seção.

[![IoT Central configurações de exportação de dados](media/quickstart-iot-fhir-portal/iot-central-data-export-dashboard.png)](media/quickstart-iot-fhir-portal/iot-central-data-export-dashboard.png#lightbox)

Crie um novo destino:
- Vá para a guia **destinos** e crie um novo destino.
- Comece dando ao seu destino um nome exclusivo.
- Escolha os *hubs de eventos do Azure* como o tipo de destino.
- Forneça o conector do Azure IoT para a cadeia de conexão do FHIR obtida em uma etapa anterior para o campo de **cadeia de conexão** .

Criar uma nova exportação de dados:
- Depois de criar seu destino, vá para a guia **Exports** e crie uma nova exportação de dados. 
- Comece dando a ela a exportação de dados um nome exclusivo.
- Em **dados** , selecione *telemetria* como o *tipo de dados a ser exportado*.
- Em **destino** , selecione o nome de destino que você criou no nome anterior.

## <a name="view-device-data-in-azure-api-for-fhir"></a>Ver os dados do dispositivo na API do Azure para FHIR

Veja os recursos de Observação com base em FHIR criados pelo Conector IoT do Azure para FHIR na API do Azure para FHIR usando o Postman. Configure o [Postman para acessar a API do Azure para FHIR](access-fhir-postman-tutorial.md) e faça uma solicitação `GET` a `https://your-fhir-server-url/Observation?code=http://loinc.org|8867-4` para ver os recursos de Observação do FHIR com o valor de frequência cardíaca. 

> [!TIP]
> Verifique se o usuário tem acesso apropriado ao plano de dados da API do Azure para FHIR. Use o [Azure RBAC (controle de acesso baseado em função do Azure)](configure-azure-rbac.md) para atribuir as funções de plano de dados necessárias.

## <a name="clean-up-resources"></a>Limpar os recursos

Exclua uma instância do Conector IoT do Azure para FHIR removendo o grupo de recursos associado, o serviço da API do Azure para FHIR ou a própria instância do Conector IoT do Azure para FHIR quando eles não forem mais necessários. 

Para remover uma instância do Conector IoT do Azure para FHIR diretamente, selecione a instância na página **Conectores IoT** para acessar a página **Conector IoT** e clique no botão **Excluir**. Selecione **Sim** quando a confirmação for solicitada. 

[![Excluir a instância do Conector IoT](media/quickstart-iot-fhir-portal/portal-iot-connector-delete.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-delete.jpg#lightbox)

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você implantou o recurso do Conector IoT do Azure para FHIR no recurso da API do Azure para FHIR. Selecione uma das seguintes etapas abaixo para saber mais sobre o Conector IoT do Azure para FHIR:

entenda as diferentes fases do fluxo de dados no Conector IoT do Azure para FHIR.

>[!div class="nextstepaction"]
>[Fluxo de dados do Conector IoT do Azure para FHIR](iot-data-flow.md)

Saiba como configurar o Conector IoT usando modelos de mapeamento FHIR e do dispositivo.

>[!div class="nextstepaction"]
>[Modelos de mapeamento do Conector IoT do Azure para FHIR](iot-mapping-templates.md)

*No portal do Azure, o Conector IoT do Azure para FHIR é chamado de Conector IoT (versão prévia). FHIR é uma marca registrada da HL7, usada com permissão da HL7.
