---
title: Configurar contêineres-API de detecção facial
titleSuffix: Azure Cognitive Services
description: Definições de configuração para contêineres.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: dapine
ms.openlocfilehash: 6dd047b0ba7f9a123ffcc014cff5604466946d07
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564114"
---
# <a name="configure-face-docker-containers"></a>Configurar contêineres do Docker de Detecção Facial

O ambiente de tempo de execução do contêiner de **Detecção Facial** é configurado usando argumentos do comando `docker run`. Esse contêiner tem várias configurações obrigatórias e outras configurações opcionais. Há vários [exemplos](#example-docker-run-commands) do comando disponíveis. As configurações específicas do contêiner são as configurações de cobrança. 

## <a name="configuration-settings"></a>Parâmetros de configuração

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> As configurações [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) e [`Eula`](#eula-setting) são usadas juntas e você deve fornecer valores válidos para todos os três; caso contrário, seu contêiner não será iniciado. Para obter mais informações sobre como usar essas configurações para instanciar um contêiner, consulte [Faturamento](face-how-to-install-containers.md#billing).

## <a name="apikey-configuration-setting"></a>Configuração de configuração do ApiKey

A configuração `ApiKey` especifica a chave de recurso do Azure usada para rastrear informações de cobrança do contêiner. Você deve especificar um valor para o ApiKey e o valor deve ser uma chave válida para o recurso de _Serviços cognitivas_ especificado [`Billing`](#billing-configuration-setting) para a definição de configuração.

Essa configuração pode ser localizada no seguinte local:

* Portal do Azure: **Serviços cognitivas** Gerenciamento de recursos, em **chaves**

## <a name="applicationinsights-setting"></a>Configuração applicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Definição de configuração de cobrança

A `Billing` configuração especifica o URI do ponto de extremidade do recurso de _Serviços cognitivas_ no Azure usado para medir as informações de cobrança do contêiner. Você deve especificar um valor para essa definição de configuração e o valor deve ser um URI de ponto de extremidade válido para um recurso de _Serviços cognitivas_ no Azure. O contêiner relata o uso a cada 10 a 15 minutos.

Essa configuração pode ser localizada no seguinte local:

* Portal do Azure: **Serviços cognitivas** Visão geral, rotulada`Endpoint`

Lembre-se de adicionar o roteamento _facial_ ao URI do ponto de extremidade, conforme mostrado no exemplo. 

|Necessário| Nome | Tipo de dados | Descrição |
|--|------|-----------|-------------|
|Sim| `Billing` | Cadeia | URI do ponto de extremidade de cobrança<br><br>Exemplo:<br>`Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0` |

<!-- specific to face only -->

## <a name="cloudai-configuration-settings"></a>Definições de configuração do CloudAI

As definições de configuração na seção `CloudAI` fornecem opções específicas aos contêineres exclusivas para seu contêiner. Os seguintes objetos e configurações são compatíveis com o contêiner de Detecção Facial na seção `CloudAI`

| Nome | Tipo de dados | Descrição |
|------|-----------|-------------|
| `Storage` | Objeto | O cenário de armazenamento usado pelo contêiner de Detecção Facial. Para obter mais informações sobre cenários de armazenamento e configurações associadas para o objeto `Storage`, confira [Configurações de cenário de armazenamento](#storage-scenario-settings) |

### <a name="storage-scenario-settings"></a>Configurações de cenário de armazenamento

O contêiner de Detecção Facial armazena dados de blob, cache, metadados e fila, dependendo do que está sendo armazenado. Por exemplo, os índices e resultados de treinamento de um grupo grande de pessoas são armazenados como dados de blob. O contêiner de Detecção Facial fornece dois cenários de armazenamento diferentes ao interagir e armazenar esses tipos de dados:

* Memória  
  Todos os quatro tipos de dados são armazenados na memória. Não são distribuídos, nem persistentes. Se o contêiner de Detecção Facial for interrompido ou removido, todos os dados no armazenamento desse contêiner serão destruídos.  
  Esse é o cenário de armazenamento padrão para o contêiner de Detecção Facial.
* Azure  
  O contêiner de Detecção Facial usa o Armazenamento do Microsoft Azure e o Azure Cosmos DB para distribuir esses quatro tipos de dados no armazenamento persistente. Os dados de blob e de fila são manipulados pelo Armazenamento do Microsoft Azure. Metadados e dados de cache são tratados pelo Azure Cosmos DB. Se o contêiner de Detecção Facial for interrompido ou removido, todos os dados no armazenamento desse contêiner permanecerão armazenados no Armazenamento do Microsoft Azure e no Azure Cosmos DB.  
  Os recursos usados pelo cenário de armazenamento do Azure têm os seguintes requisitos adicionais
  * O recurso de Armazenamento do Microsoft Azure deve usar o tipo de conta StorageV2
  * O recurso do Azure Cosmos DB deve usar a API do Azure Cosmos DB para MongoDB

Os cenários de armazenamento e as definições de configuração associadas são gerenciados pelo objeto `Storage`, de acordo com a seção de configuração `CloudAI`. As seguintes configurações estão disponíveis no objeto `Storage`:

| Nome | Tipo de dados | Descrição |
|------|-----------|-------------|
| `StorageScenario` | Cadeia | O cenário de armazenamento compatível com o contêiner. Os valores a seguir estão disponíveis<br/>`Memory` – Valor padrão. O contêiner usa armazenamento não persistente, não distribuído e de na memória, para uso temporário e de nó único. Se o contêiner for interrompido ou removido, o armazenamento desse contêiner será destruído.<br/>`Azure` – O contêiner usa recursos do Azure para armazenamento. Se o contêiner for interrompido ou removido, o armazenamento desse contêiner será persistente.|
| `ConnectionStringOfAzureStorage` | Cadeia | A cadeia de conexão para o recurso do Armazenamento do Microsoft Azure usado pelo contêiner.<br/>Essa configuração se aplicará somente se `Azure` for especificado para a definição de configuração `StorageScenario`. |
| `ConnectionStringOfCosmosMongo` | Cadeia | A cadeia de conexão do MongoDB para o recurso do Azure Cosmos DB usado pelo contêiner.<br/>Essa configuração se aplicará somente se `Azure` for especificado para a definição de configuração `StorageScenario`. |

Por exemplo, o comando a seguir especifica o cenário de armazenamento do Azure e fornece exemplos de cadeias de conexão para os recursos de Armazenamento do Microsoft Azure e do Cosmos DB usados para armazenar dados do contêiner de Detecção Facial.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 CloudAI:Storage:StorageScenario=Azure CloudAI:Storage:ConnectionStringOfCosmosMongo="mongodb://samplecosmosdb:0123456789@samplecosmosdb.documents.azure.com:10255/?ssl=true&replicaSet=globaldb" CloudAI:Storage:ConnectionStringOfAzureStorage="DefaultEndpointsProtocol=https;AccountName=sampleazurestorage;AccountKey=0123456789;EndpointSuffix=core.windows.net"
  ```

O cenário de armazenamento é manipulado separadamente por meio de montagens de entrada e de saída. Você pode especificar uma combinação desses recursos para um único contêiner. Por exemplo, o comando a seguir define uma montagem de associação do Docker à pasta `D:\Output` no computador host como a montagem de saída, depois cria uma instância de um contêiner com base na imagem de contêiner de Detecção Facial, salvando arquivos de log no formato JSON na montagem de saída. O comando também especifica o cenário de armazenamento do Azure e fornece exemplos de cadeias de conexão para os recursos de Armazenamento do Microsoft Azure e do Cosmos DB, usados para armazenar dados do contêiner de Detecção Facial.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 --mount type=bind,source=D:\Output,destination=/output containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 Logging:Disk:Format=json CloudAI:Storage:StorageScenario=Azure CloudAI:Storage:ConnectionStringOfCosmosMongo="mongodb://samplecosmosdb:0123456789@samplecosmosdb.documents.azure.com:10255/?ssl=true&replicaSet=globaldb" CloudAI:Storage:ConnectionStringOfAzureStorage="DefaultEndpointsProtocol=https;AccountName=sampleazurestorage;AccountKey=0123456789;EndpointSuffix=core.windows.net"
  ```

## <a name="eula-setting"></a>Configuração de EULA

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Configurações de Fluentd

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Configurações das credenciais de proxy HTTP

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Configurações de registro em log
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Configurações de montagem

Use montagens de associação para ler e gravar dados do contêiner e para ele. Você pode especificar uma montagem de entrada ou saída especificando a opção `--mount` no comando [docker run](https://docs.docker.com/engine/reference/commandline/run/).

Os contêineres de Detecção Facial não usam montagens de entrada ou saída para armazenar dados de treinamento ou serviço. 

A sintaxe exata do local da montagem do host varia de acordo com o sistema operacional do host. Além disso, a localização de montagem do [computador host](face-how-to-install-containers.md#the-host-computer) pode não estar acessível devido a um conflito entre as permissões usadas pela conta de serviço do Docker e as permissões da localização de montagem do host. 

|Opcional| Nome | Tipo de dados | Descrição |
|-------|------|-----------|-------------|
|Não permitido| `Input` | Cadeia | Contêineres de Detecção Facial não usam isso.|
|Opcional| `Output` | Cadeia | O destino de montagem de saída. O valor padrão é `/output`. Esse é o local dos logs. Isso inclui logs de contêiner. <br><br>Exemplo:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Comandos docker run de exemplo 

Os exemplos a seguir usam as definições de configuração para ilustrar como escrever e usar comandos `docker run`.  Quando em execução, o contêiner continuará a ser executado até que você o [pare](face-how-to-install-containers.md#stop-the-container).

* **Caractere de continuação de linha**: Os comandos do Docker nas seções a seguir usam a barra invertida, `\`, como um caractere de continuação de linha. Substitua ou remova essa barra com base nos requisitos do sistema operacional de seu computador host. 
* **Ordem do argumento**: Não altere a ordem dos argumentos, a menos que você esteja familiarizado com contêineres do Docker.

Substitua {_argument_name_} pelos seus próprios valores:

| Espaço reservado | Valor | Formato ou exemplo |
|-------------|-------|---|
|{API_KEY} | A chave do ponto de extremidade do recurso de serviços cognitivas. |xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|
|{ENDPOINT_URI} | O valor do ponto de extremidade, incluindo roteamento de região e face.|`https://westcentralus.api.cognitive.microsoft.com/face/v1.0`|

> [!IMPORTANT]
> As opções `Eula`, `Billing` e `ApiKey` devem ser especificadas para executar o contêiner; caso contrário, o contêiner não será iniciado.  Para mais informações, consulte [Faturamento](face-how-to-install-containers.md#billing).
> O valor de ApiKey é a **chave** da página `Cognitive Services` de chaves de recurso do Azure. 

## <a name="face-container-docker-examples"></a>Exemplos do Docker do contêiner de Detecção Facial

Os seguintes exemplos do Docker são para o contêiner de Detecção Facial. 

### <a name="basic-example"></a>Exemplo básico 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-face \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} 
  ```

### <a name="logging-example"></a>Exemplo de log 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face \
  Eula=accept \
  Billing={ENDPOINT_URI} ApiKey={API_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>Próximas etapas

* Reveja [Como instalar e executar contêineres](face-how-to-install-containers.md)
