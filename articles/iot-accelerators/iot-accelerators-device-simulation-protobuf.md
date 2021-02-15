---
title: Usar Buffers de Protocolo com simulação de dispositivo – Azure | Microsoft Docs
description: Neste guia de instruções, você aprenderá a usar Buffers de Protocolo para serializar a telemetria enviada do acelerador de solução de Simulação de Dispositivo.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc, amqp, devx-track-csharp
ms.date: 11/06/2018
ms.author: dobett
ms.openlocfilehash: 77a795b8f704084b612a2dcbd364b8d5be0a3333
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2020
ms.locfileid: "96852142"
---
# <a name="serialize-telemetry-using-protocol-buffers"></a>Serializar a telemetria usando Buffers de Protocolo

Buffers de protocolo (Protobuf) é um formato de serialização binária para dados estruturados. Protobuf foi projetado para enfatizar a simplicidade e o desempenho com o objetivo de ser menor e mais rápido do que o XML.

A Simulação de Dispositivo oferece suporte à versão **proto3** da linguagem de buffers de protocolo.

Como o Protobuf exige que o código compilado serialize os dados, você deve criar uma versão personalizada da Simulação de Dispositivo.

As etapas deste guia de instruções mostram como:

1. Preparar um ambiente de desenvolvimento
1. Especificar o uso do formato Protobuf em um modelo de dispositivo
1. Definir seu formato de Protobuf
1. Gerar classes de Protobuf
1. Testar localmente

## <a name="prerequisites"></a>Pré-requisitos

Para executar as etapas neste guia de instruções, você precisará de:

* Visual Studio Code. Baixe o [Visual Studio Code para Mac, Linux e Windows](https://code.visualstudio.com/download).
* .NET Core. Você pode fazer o download do [NET Core para Mac, Linux e Windows](https://www.microsoft.com/net/download).
* Postman. Baixe o [Postman para Mac, Windows ou Linux](https://www.getpostman.com/apps).
* Um [hub IoT implantado na assinatura do Azure](../iot-hub/iot-hub-create-through-portal.md). Você precisa da cadeia de conexão do hub IoT para concluir as etapas neste guia. Você pode obter a cadeia de conexão no portal do Azure.
* Um [banco de dados do Cosmos DB implantado em sua assinatura do Azure](../cosmos-db/create-sql-api-dotnet.md#create-account) que usa a API de SQL e que está configurado para [consistência forte](../cosmos-db/how-to-manage-database-account.md). Você precisa da cadeia de conexão do banco de dados do Cosmos DB para concluir as etapas neste guia. Você pode obter a cadeia de conexão no portal do Azure.
* Uma [conta de Armazenamento do Azure implantada em sua assinatura do Azure](../storage/common/storage-account-create.md). Você precisa da cadeia de conexão da conta de armazenamento para concluir as etapas neste guia. Você pode obter a cadeia de conexão no portal do Azure.

## <a name="prepare-your-development-environment"></a>Preparar seu ambiente de desenvolvimento

Conclua as seguintes tarefas para preparar seu ambiente de desenvolvimento:

* Baixe a fonte para o microsserviço de simulação de dispositivo.
* Baixe a fonte para o microsserviço de adaptador de armazenamento.
* Execute o microsserviço de adaptador de armazenamento localmente.

As instruções neste artigo pressupõem que você está usando o Windows. Se você estiver usando outro sistema operacional, talvez você precise ajustar alguns dos caminhos de arquivo e comandos para se adequar ao seu ambiente.

### <a name="download-the-microservices"></a>Baixar os microsserviços

Baixe e descompacte os [Microsserviços de Monitoramento Remoto](https://github.com/Azure/remote-monitoring-services-dotnet/archive/master.zip) do GitHub para um local adequado em seu computador local. Esse repositório inclui o microsserviço de adaptador de armazenamento que você precisa para estas instruções.

Baixe e descompacte o [microsserviço de adaptador de armazenamento](https://github.com/Azure/device-simulation-dotnet/archive/master.zip) do GitHub para um local adequado em seu computador local.

### <a name="run-the-storage-adapter-microservice"></a>Executar o microsserviço de adaptador de armazenamento

No Visual Studio Code, abra a pasta **remote-monitoring-services-dotnet-master\storage-adapter**. Clique em qualquer botão **Restaurar** para corrigir todas as dependências não resolvidas.

Abra o **. vscode/launch.jsno** arquivo e atribua sua cadeia de conexão de Cosmos DB à variável de ambiente **\_ STORAGEADAPTER \_ DOCUMENTDB \_ connString do PC** .

> [!NOTE]
> Ao executar o microsserviço localmente em seu computador, ele ainda exige uma instância do Cosmos DB no Azure para funcionar corretamente.

Para executar localmente o microsserviço do adaptador de armazenamento, clique em **Depurar \> Iniciar Depuração**.

A janela **Terminal** no Visual Studio Code mostra a saída do microsserviço em execução, incluindo uma URL para a verificação de integridade do serviço Web: <http://127.0.0.1:9022/v1/status>. Quando você navegar até esse endereço, o status deverá ser "OK: ativo e em funcionamento".

Deixe o microsserviço do adaptador de armazenamento em execução nesta instância do Visual Studio Code enquanto conclui as próximas etapas.

## <a name="define-your-device-model"></a>Definir o modelo de dispositivo

Abra a pasta **device-simulation-dotnet-master** que você baixou do GitHub em uma nova instância do Visual Studio Code. Clique em qualquer botão **Restaurar** para corrigir todas as dependências não resolvidas.

Neste guia de instruções, você criará um novo modelo de dispositivo para um controlador de ativo:

1. Crie um novo arquivo de modelo de dispositivo chamado **assettracker-01.json** na pasta **Services\data\devicemodels**.

1. Defina a funcionalidade do dispositivo no arquivo **assettracker-01.json** do modelo de dispositivo. A seção de telemetria de um modelo de dispositivo Protobuf deve:

   * Incluir o nome da classe Protobuf gerada para seu dispositivo. A seção a seguir mostra como gerar essa classe.
   * Especifique Protobuf como o formato de mensagem.

     ```json
     {
     "SchemaVersion": "1.0.0",
     "Id": "assettracker-01",
     "Version": "0.0.1",
     "Name": "Asset Tracker",
     "Description": "An asset tracker with location, temperature, and humidity",
     "Protocol": "AMQP",
     "Simulation": {
       "InitialState": {
         "online": true,
         "latitude": 47.445301,
         "longitude": -122.296307,
         "temperature": 38.0,
         "humidity": 62.0
       },
       "Interval": "00:01:00",
       "Scripts": [
         {
           "Type": "javascript",
           "Path": "assettracker-01-state.js"
         }
       ]
     },
     "Properties": {
       "Type": "AssetTracker",
       "Location": "Field",
       "Latitude": 47.445301,
       "Longitude": -122.296307
     },
     "Telemetry": [
       {
         "Interval": "00:00:10",
         "MessageTemplate": "{\"latitude\":${latitude},\"longitude\":${longitude},\"temperature\":${temperature},\"humidity\":${humidity}}",
         "MessageSchema": {
           "Name": "assettracker-sensors;v1",
           "ClassName": "Microsoft.Azure.IoTSolutions.DeviceSimulation.Services.Models.Protobuf.AssetTracker",
           "Format": "Protobuf",
           "Fields": {
             "latitude": "double",
             "longitude": "double",
             "temperature": "double",
             "humidity": "double"
           }
         }
       }
     ]
     }
     ```

### <a name="create-device-behaviors-script"></a>Criar script de comportamentos do dispositivo

Escreva o script de comportamento que define como o dispositivo se comporta. Para saber mais, confira [Criar um dispositivo simulado avançado](iot-accelerators-device-simulation-advanced-device.md).

## <a name="define-your-protobuf-format"></a>Definir seu formato de Protobuf

Quando você tiver um modelo de dispositivo e determinar o formato da mensagem, poderá criar um arquivo **proto**. No arquivo **proto**, adicione:

* Um `csharp_namespace` que corresponde à propriedade **ClassName** em seu modelo de dispositivo.
* Uma mensagem para cada estrutura de dados para serialização.
* Um nome e tipo de cada campo na mensagem.

1. Crie um novo arquivo chamado **assettracker.proto** na pasta **Services\Models\Protobuf\proto**.

1. Defina a sintaxe, o namespace e o esquema de mensagem no arquivo **proto** da seguinte maneira:

    ```proto
    syntax = "proto3";

    option csharp_namespace = "Microsoft.Azure.IoTSolutions.DeviceSimulation.Services.Models.Protobuf";

    message AssetTracker {
      double latitude=1;
      double longitude=2;
      double temperature=5;
      double humidity=6;
    }
    ```

Os marcadores `=1`, `=2` em cada elemento especificam uma marca exclusiva usada pelo campo na codificação binária. Os números 1 a 15 exigem um byte a menos para codificar do que os números mais altos.

## <a name="generate-the-protobuf-class"></a>Gerar a classe Protobuf

Quando você tiver um arquivo **proto**, a próxima etapa será gerar as classes necessárias para ler e escrever mensagens. Para concluir essa etapa, você precisa do compilador de Protobuf **Protoc**.

1. [Baixe o compilador Protobuf do GitHub](https://github.com/protocolbuffers/protobuf/releases/download/v3.4.0/protoc-3.4.0-win32.zip)

1. Execute o compilador, especificando o diretório de origem, o diretório de destino e o nome do seu arquivo **proto**. Por exemplo:

    ```cmd
    protoc -I c:\temp\device-simulation-dotnet-master\Services\Models\Protobuf\proto --csharp_out=C:\temp\device-simulation-dotnet-master\Services\Models\Protobuf assettracker.proto
    ```

    Este comando gera um arquivo **Assettracker.cs** na pasta **Services\Models\Protobuf**.

## <a name="test-protobuf-locally"></a>Testar o Protobuf localmente

Nesta seção, você testará localmente o dispositivo controlador de ativo que criou nas seções anteriores.

### <a name="run-the-device-simulation-microservice"></a>Executar o microsserviço de simulação de dispositivo

Abra o arquivo **.vscode/launch.json** e atribua:

* Sua cadeia de conexão do Hub IoT à variável de ambiente **PCS\_IOTHUB\_CONNSTRING**.
* Sua cadeia de conexão da conta de armazenamento à variável de ambiente **PCS\_AZURE\_STORAGE\_ACCOUNT**.
* Sua cadeia de conexão do Cosmos DB à variável de ambiente **PCS\_STORAGEADAPTER\_DOCUMENTDB\_CONNSTRING**.

Abra o arquivo **WebService/Properties/launchSettings.json** e atribua:

* Sua cadeia de conexão do Hub IoT à variável de ambiente **PCS\_IOTHUB\_CONNSTRING**.
* Sua cadeia de conexão da conta de armazenamento à variável de ambiente **PCS\_AZURE\_STORAGE\_ACCOUNT**.
* Sua cadeia de conexão do Cosmos DB à variável de ambiente **PCS\_STORAGEADAPTER\_DOCUMENTDB\_CONNSTRING**.

Abra o arquivo **WebService\appsettings.ini** e modifique as configurações da seguinte maneira:

#### <a name="configure-the-solution-to-include-your-new-device-model-files"></a>Configurar a solução para incluir seus novos arquivos de modelo do dispositivo

Por padrão, seus novos arquivos JSON e JS de modelo de dispositivo não serão copiados para a solução interna. Você precisa incluí-los explicitamente.

Adicione uma entrada para o arquivo **services\services.csproj** para cada arquivo que você quer incluir. Por exemplo:

```xml
<None Update="data\devicemodels\assettracker-01.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
<None Update="data\devicemodels\scripts\assettracker-01-state.js">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
```

Para executar o microsserviço localmente, clique em **Depurar \> Iniciar Depuração**.

A janela **Terminal** no Visual Studio Code mostra a saída do microsserviço em execução.

Deixe o microsserviço de simulação de dispositivo em execução nesta instância do Visual Studio Code enquanto você conclui as próximas etapas.

### <a name="set-up-a-monitor-for-device-events"></a>Configurar um monitor para eventos de dispositivo

Nesta seção, você pode usar a CLI do Azure para configurar um monitor de eventos para exibir a telemetria enviada dos dispositivos conectados ao hub IoT.

O script a seguir pressupõe que o nome do hub IoT é **device-simulation-test**.

```azurecli-interactive
# Install the IoT extension if it's not already installed
az extension add --name azure-iot

# Monitor telemetry sent to your hub
az iot hub monitor-events --hub-name device-simulation-test
```

Deixe o monitor de eventos em execução enquanto você testa os dispositivos simulados.

### <a name="create-a-simulation-with-the-asset-tracker-device-type"></a>Criar uma simulação com o tipo de dispositivo de controlador de ativo

Nesta seção, você pode usar a ferramenta Postman para solicitar ao microsserviço de simulação de dispositivo a execução de uma simulação usando o tipo de dispositivo de controlador de ativo. Postman é uma ferramenta que permite que você envie solicitações REST para um serviço Web.

Para configurar o Postman:

1. Abra o Postman no computador local.

1. Clique em **Arquivo \> Importar**. Em seguida, clique em **Escolher Arquivos**.

1. Selecione **accelerator.postman\_collection da solução Simulação de Dispositivo IoT do Azure** e **accelerator.postman\_environment da solução Simulação de Dispositivo IoT do Azure** e clique em **Abrir**.

1. Expanda o **acelerador da solução Simulação de Dispositivo IoT do Azure** para ver as solicitações que você pode enviar.

1. Clique em **Nenhum Ambiente** e selecione **acelerador da solução Simulação de Dispositivo IoT do Azure**.

Agora, você tem uma coleção e o ambiente carregado em seu workspace de Postman que você pode usar para interagir com o microsserviço de simulação de dispositivo.

Para configurar e executar a simulação:

1. Na coleção de Postman, selecione **Criar simulação de controlador de ativo** e clique em **Enviar**. Essa solicitação cria quatro instâncias do tipo de dispositivo de controlador de ativo simulado.

1. A saída do monitor de eventos na janela da CLI do Azure mostra a telemetria dos dispositivos simulados.

Para parar a simulação, selecione a solicitação **Parar a simulação** no Postman e clique em **Enviar**.

### <a name="clean-up-resources"></a>Limpar recursos

Você pode parar os dois microsserviços em execução local em suas instâncias do Visual Studio Code (**Depurar \> Parar Depuração**).

Se você não precisar mais instâncias do Hub IoT e do Cosmos DB, exclua-as de sua assinatura do Azure para evitar encargos desnecessários.

## <a name="iot-hub-support"></a>Suporte do Hub IoT

Muitos recursos do Hub IoT não dão suporte nativo a Protobuf ou a outros formatos binários. Por exemplo, você não pode rotear com base na carga de mensagem, pois o Hub IoT não será capaz de processar a carga da mensagem. No entanto, você pode rotear com base nos cabeçalhos de mensagem.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como personalizar a simulação de dispositivo para usar o Protobuf para enviar telemetria, a próxima etapa é visitar o repositório GitHub para saber mais sobre a [simulação de dispositivo](https://github.com/Azure/device-simulation-dotnet).
