---
title: Configurar contêineres de OCR de Leitura – Pesquisa Visual Computacional
titleSuffix: Azure Cognitive Services
description: Este artigo mostrará de que modo definir configurações obrigatórias e opcionais para executar contêineres de OCR de Leitura na Pesquisa Visual Computacional.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/09/2021
ms.author: aahi
ms.custom: seodec18
ms.openlocfilehash: 3e6c4b73e8aeb26c6ac4025ef3c07fb4f8d48eaf
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107308635"
---
# <a name="configure-read-ocr-docker-containers"></a>Configurar contêineres de OCR de Leitura do Docker

Configure o ambiente de runtime do contêiner de OCR de Leitura da Pesquisa Visual Computacional usando argumentos do comando `docker run`. Esse contêiner tem várias configurações obrigatórias e outras configurações opcionais. Há vários [exemplos](#example-docker-run-commands) do comando disponíveis. As configurações específicas do contêiner são as configurações de cobrança. 

## <a name="configuration-settings"></a>Definições de configuração

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> As configurações [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) e [`Eula`](#eula-setting) são usadas juntas e você deve fornecer valores válidos para todos os três; caso contrário, seu contêiner não será iniciado. Para obter mais informações sobre como usar essas configurações para instanciar um contêiner, consulte [Faturamento](computer-vision-how-to-install-containers.md).

O contêiner também tem as seguintes definições de configuração específica:

|Obrigatório|Configuração|Finalidade|
|--|--|--|
|Não|ReadEngineConfig:ResultExpirationPeriod| Somente contêineres v2.0. Período de término do resultado em horas. O padrão é 48 horas. A configuração especifica quando o sistema deverá limpar os resultados de reconhecimento. Por exemplo, no caso `resultExpirationPeriod=1`, o sistema limpará o resultado do reconhecimento uma hora após o processo. No caso de `resultExpirationPeriod=0`, o sistema limpará o resultado do reconhecimento após a recuperação do resultado.|
|Não|Cache: Redis| Somente contêineres v2.0. Habilita o armazenamento do Redis para armazenar resultados. Um cache será *necessário* caso vários contêineres de OCR de leitura sejam inseridos atrás de um balanceador de carga.|
|Não|Queue:RabbitMQ|Somente contêineres v2.0. Habilita o RabbitMQ para despachar tarefas. A configuração será útil quando vários contêineres de OCR de leitura forem inseridos atrás de um balanceador de carga.|
|Não|Queue:Azure:QueueVisibilityTimeoutInMilliseconds | Somente contêineres v3.x. O tempo que uma mensagem levará para ficar invisível quando outro trabalhador estiver executando o processamento dela. |
|Não|Storage::DocumentStore::MongoDB|Somente contêineres v2.0. Habilita o MongoDB para executar um armazenamento permanente de resultados. |
|Não|Storage:ObjectStore:AzureBlob:ConnectionString| Somente contêineres v3.x. A cadeia de conexão do Armazenamento de Blobs do Azure. |
|Não|Storage:TimeToLiveInDays| Somente contêineres v3.x. Período de término do resultado em dias. A configuração especifica quando o sistema deverá limpar os resultados de reconhecimento. O padrão é a cada dois dias (48 horas). Isso significa que nenhum resultado ativo por mais tempo do que esse período terá garantia de ser recuperado com êxito. |
|Não|Task:MaxRunningTimeSpanInMinutes| Somente contêineres v3.x. Tempo máximo de execução de uma solicitação. O padrão é de 60 minutos. |

## <a name="apikey-configuration-setting"></a>Configuração de configuração do ApiKey

A configuração `ApiKey` especifica a chave de recurso de `Cognitive Services` do Azure usada para rastrear informações de cobrança do contêiner. Será necessário especificar um valor para ApiKey. Além disso, o valor deverá ser uma chave válida para o recurso dos _Serviços Cognitivos_ especificado para a definição da configuração [`Billing`](#billing-configuration-setting).

Essa configuração pode ser localizada no seguinte local:

* Portal do Azure: gerenciamento de recursos dos **Serviços Cognitivos** na opção **Chaves**

## <a name="applicationinsights-setting"></a>Configuração applicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Definição de configuração de cobrança

A configuração `Billing` especifica o URI do ponto de extremidade de um recurso dos _Serviços Cognitivos_ no Azure usado para medir informações de cobrança para o contêiner. Será preciso especificar um valor para essa definição de configuração. Além disso, o valor deverá ser um URI do ponto de extremidade válido para um recurso dos _Serviços Cognitivos_ no Azure. O contêiner relata o uso a cada 10 a 15 minutos.

Essa configuração pode ser localizada no seguinte local:

* Portal do Azure: visão geral dos **Serviços Cognitivos** rotulados como `Endpoint`

Lembre-se de adicionar o roteamento `vision/<version>` ao URI do ponto de extremidade, conforme mostrado na tabela a seguir. 

|Obrigatório| Nome | Tipo de dados | Descrição |
|--|------|-----------|-------------|
|Sim| `Billing` | String | URI do ponto de extremidade de cobrança<br><br>Exemplo:<br>`Billing=https://westcentralus.api.cognitive.microsoft.com/vision/v3.2` |

## <a name="eula-setting"></a>Configuração de EULA

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Configurações de Fluentd

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Configurações das credenciais de proxy HTTP

[!INCLUDE [Container shared configuration HTTP proxy settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Configurações de log
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Configurações de montagem

Use montagens de associação para ler e gravar dados do contêiner e para ele. Você pode especificar uma montagem de entrada ou saída especificando a opção `--mount` no comando [docker run](https://docs.docker.com/engine/reference/commandline/run/).

Os contêineres de Pesquisa Visual Computacional não usam montagens de entrada ou saída para armazenar dados de treinamento ou serviço. 

A sintaxe exata do local da montagem do host varia de acordo com o sistema operacional do host. Além disso, a localização de montagem do [computador host](computer-vision-how-to-install-containers.md#the-host-computer) pode não estar acessível devido a um conflito entre as permissões usadas pela conta de serviço do Docker e as permissões da localização de montagem do host. 

|Opcional| Nome | Tipo de dados | Descrição |
|-------|------|-----------|-------------|
|Não permitido| `Input` | String | Os contêineres de Pesquisa Visual Computacional não usam isso.|
|Opcional| `Output` | Cadeia de caracteres | O destino de montagem de saída. O valor padrão é `/output`. Esse é o local dos logs. Isso inclui logs de contêiner. <br><br>Exemplo:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Comandos docker run de exemplo

Os exemplos a seguir usam as definições de configuração para ilustrar como escrever e usar comandos `docker run`.  Quando em execução, o contêiner continuará a ser executado até que você o [pare](computer-vision-how-to-install-containers.md#stop-the-container).

* **Caractere de continuação de linha**: os comandos do Docker das seções a seguir usam a barra invertida (`\`) como um caractere de continuação de linha. Substitua ou remova essa barra com base nos requisitos do sistema operacional de seu computador host. 
* **Ordem do argumento**: não altere a ordem dos argumentos, a menos que você esteja muito familiarizado com os contêineres do Docker.

Substitua {_argument_name_} pelos seus próprios valores:

| Espaço reservado | Valor | Formato ou exemplo |
|-------------|-------|---|
| **{API_KEY}** | A chave de ponto de extremidade do recurso da `Computer Vision` na página de Chaves da `Computer Vision` do Azure. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | O valor do ponto de extremidade de cobrança está disponível na página de Visão Geral da `Computer Vision` do Azure.| Confira [como coletar parâmetros necessários](computer-vision-how-to-install-containers.md#gathering-required-parameters) para obter exemplos explícitos. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> As opções `Eula`, `Billing` e `ApiKey` devem ser especificadas para executar o contêiner; caso contrário, o contêiner não será iniciado.  Para mais informações, consulte [Faturamento](computer-vision-how-to-install-containers.md#billing).
> O valor de ApiKey é a **Chave** da página de chaves do Recurso da `Cognitive Services` do Azure.

## <a name="container-docker-examples"></a>Exemplos de contêineres do Docker

Os exemplos abaixo do Docker são específicos para o contêiner de OCR de Leitura.


# <a name="version-32"></a>[Versão 3.2](#tab/version-3-2)

### <a name="basic-example"></a>Exemplo básico

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:3.2 \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}

```

### <a name="logging-example"></a>Exemplo de registro em log 

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:3.2 \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
Logging:Console:LogLevel:Default=Information
```

# <a name="version-20-preview"></a>[Versão 2.0 – versão prévia](#tab/version-2)

### <a name="basic-example"></a>Exemplo básico

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}

```

### <a name="logging-example"></a>Exemplo de registro em log 

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
Logging:Console:LogLevel:Default=Information
```

---

## <a name="next-steps"></a>Próximas etapas

* Examine [Como instalar e executar contêineres](computer-vision-how-to-install-containers.md).
