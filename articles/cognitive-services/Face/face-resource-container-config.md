---
title: Configurar contêineres
titlesuffix: Face - Azure Cognitive Services
description: Definições de configuração para contêineres.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: diberry
ms.openlocfilehash: 4152cf90d9de2eda15a798fbf6b5b4aa4f5646f7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60815388"
---
# <a name="configure-face-docker-containers"></a>Configurar contêineres do Docker de Detecção Facial

O ambiente de tempo de execução do contêiner de **Detecção Facial** é configurado usando argumentos do comando `docker run`. Esse contêiner tem várias configurações obrigatórias e outras configurações opcionais. Há vários [exemplos](#example-docker-run-commands) do comando disponíveis. As configurações específicas do contêiner são as configurações de cobrança. 

## <a name="configuration-settings"></a>Definições de configuração

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> As configurações [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) e [`Eula`](#eula-setting) são usadas juntas e você deve fornecer valores válidos para todos os três; caso contrário, seu contêiner não será iniciado. Para obter mais informações sobre como usar essas configurações para instanciar um contêiner, consulte [Faturamento](face-how-to-install-containers.md#billing).

## <a name="apikey-configuration-setting"></a>Configuração de configuração do ApiKey

A configuração `ApiKey` especifica a chave de recurso do Azure usada para rastrear informações de cobrança do contêiner. Você deve especificar um valor para o ApiKey e o valor deve ser uma chave válida para o _dos serviços Cognitivos_ recurso especificado para o [ `Billing` ](#billing-configuration-setting) definição de configuração.

Essa configuração pode ser localizada no seguinte local:

* Portal do Azure: **Serviços cognitivos** gerenciamento de recursos, em **chaves**

## <a name="applicationinsights-setting"></a>Configuração applicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Definição de configuração de cobrança

O `Billing` configuração especifica o URI do ponto de extremidade do _os serviços Cognitivos_ recursos no Azure é usado para monitorar informações de cobrança para o contêiner. Você deve especificar um valor para este parâmetro de configuração e o valor deve ser um URI de ponto de extremidade válido para um _dos serviços Cognitivos_ recursos no Azure. O contêiner relata o uso a cada 10 a 15 minutos.

Essa configuração pode ser localizada no seguinte local:

* Portal do Azure: **Serviços cognitivos** visão geral, rotulado `Endpoint`

Lembre-se de adicionar o _Face_ roteamento para o URI do ponto de extremidade, conforme mostrado no exemplo. 

|Obrigatório| NOME | Tipo de dados | DESCRIÇÃO |
|--|------|-----------|-------------|
|Sim| `Billing` | Cadeia de caracteres | URI do ponto de extremidade de cobrança<br><br>Exemplo:<br>`Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0` |

<!-- specific to face only -->

## <a name="cloudai-configuration-settings"></a>Definições de configuração do CloudAI

As definições de configuração na seção `CloudAI` fornecem opções específicas aos contêineres exclusivas para seu contêiner. Os seguintes objetos e configurações são compatíveis com o contêiner de Detecção Facial na seção `CloudAI`

| NOME | Tipo de dados | DESCRIÇÃO |
|------|-----------|-------------|
| `Storage` | Object | O cenário de armazenamento usado pelo contêiner de Detecção Facial. Para obter mais informações sobre cenários de armazenamento e configurações associadas para o objeto `Storage`, confira [Configurações de cenário de armazenamento](#storage-scenario-settings) |

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

| NOME | Tipo de dados | DESCRIÇÃO |
|------|-----------|-------------|
| `StorageScenario` | Cadeia de caracteres | O cenário de armazenamento compatível com o contêiner. Os valores a seguir estão disponíveis<br/>`Memory` – Valor padrão. O contêiner usa armazenamento não persistente, não distribuído e de na memória, para uso temporário e de nó único. Se o contêiner for interrompido ou removido, o armazenamento desse contêiner será destruído.<br/>`Azure` – O contêiner usa recursos do Azure para armazenamento. Se o contêiner for interrompido ou removido, o armazenamento desse contêiner será persistente.|
| `ConnectionStringOfAzureStorage` | Cadeia de caracteres | A cadeia de conexão para o recurso do Armazenamento do Microsoft Azure usado pelo contêiner.<br/>Essa configuração se aplicará somente se `Azure` for especificado para a definição de configuração `StorageScenario`. |
| `ConnectionStringOfCosmosMongo` | Cadeia de caracteres | A cadeia de conexão do MongoDB para o recurso do Azure Cosmos DB usado pelo contêiner.<br/>Essa configuração se aplicará somente se `Azure` for especificado para a definição de configuração `StorageScenario`. |

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

|Opcional| NOME | Tipo de dados | DESCRIÇÃO |
|-------|------|-----------|-------------|
|Não permitido| `Input` | Cadeia de caracteres | Contêineres de Detecção Facial não usam isso.|
|Opcional| `Output` | Cadeia de caracteres | O destino de montagem de saída. O valor padrão é `/output`. Esse é o local dos logs. Isso inclui logs de contêiner. <br><br>Exemplo:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Comandos docker run de exemplo 

Os exemplos a seguir usam as definições de configuração para ilustrar como escrever e usar comandos `docker run`.  Quando em execução, o contêiner continuará a ser executado até que você o [pare](face-how-to-install-containers.md#stop-the-container).

* **Caractere de continuação de linha**: Os comandos do Docker nas seções a seguir usam a barra invertida, `\`, como um caractere de continuação de linha. Substitua ou remova essa barra com base nos requisitos do sistema operacional de seu computador host. 
* **Ordem do argumento**: Não altere a ordem dos argumentos, a menos que você esteja familiarizado com contêineres do Docker.

Substitua {_argument_name_} pelos seus próprios valores:

| Placeholder | Value | Formato ou exemplo |
|-------------|-------|---|
|{BILLING_KEY} | A chave do ponto de extremidade do recurso dos serviços Cognitivos. |xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|
|{BILLING_ENDPOINT_URI} | O valor de ponto de extremidade cobrança incluindo roteamento de face e região.|`https://westcentralus.api.cognitive.microsoft.com/face/v1.0`|

> [!IMPORTANT]
> As opções `Eula`, `Billing` e `ApiKey` devem ser especificadas para executar o contêiner; caso contrário, o contêiner não será iniciado.  Para mais informações, consulte [Faturamento](face-how-to-install-containers.md#billing).
> O valor de ApiKey é o **chave** do Azure `Cognitive Services` página chaves de recurso. 

## <a name="face-container-docker-examples"></a>Exemplos do Docker do contêiner de Detecção Facial

Os seguintes exemplos do Docker são para o contêiner de Detecção Facial. 

### <a name="basic-example"></a>Exemplo básico 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-face \
  Eula=accept \
  Billing={BILLING_ENDPOINT_URI} \
  ApiKey={BILLING_KEY} 
  ```

### <a name="logging-example"></a>Exemplo de registro em log 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face \
  Eula=accept \
  Billing={BILLING_ENDPOINT_URI} ApiKey={BILLING_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>Próximas etapas

* Reveja [Como instalar e executar contêineres](face-how-to-install-containers.md)
