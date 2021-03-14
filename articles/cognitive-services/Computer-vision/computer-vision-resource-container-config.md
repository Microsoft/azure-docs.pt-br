---
title: Configurar contêineres de OCR de leitura-Pesquisa Visual Computacional
titleSuffix: Azure Cognitive Services
description: Este artigo mostra como definir as configurações obrigatórias e opcionais para ler contêineres de OCR no Pesquisa Visual Computacional.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 11/23/2020
ms.author: aahi
ms.custom: seodec18
ms.openlocfilehash: ee2e4fca697c086b95e83feb9d40ce8e07dc344c
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102611888"
---
# <a name="configure-read-ocr-docker-containers"></a>Configurar contêineres de Docker de OCR de leitura

Você configura o ambiente de tempo de execução do contêiner de OCR de Pesquisa Visual Computacional leitura usando os `docker run` argumentos de comando. Esse contêiner tem várias configurações obrigatórias e outras configurações opcionais. Há vários [exemplos](#example-docker-run-commands) do comando disponíveis. As configurações específicas do contêiner são as configurações de cobrança. 

## <a name="configuration-settings"></a>Definições de configuração

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> As [`ApiKey`](#apikey-configuration-setting) [`Billing`](#billing-configuration-setting) configurações,, e [`Eula`](#eula-setting) são usadas juntas, e você deve fornecer valores válidos para todos os três; caso contrário, o contêiner não será iniciado. Para obter mais informações sobre como usar essas configurações para instanciar um contêiner, consulte [Faturamento](computer-vision-how-to-install-containers.md).

O contêiner também tem as seguintes definições de configuração específicas de contêiner:

|Necessária|Configuração|Finalidade|
|--|--|--|
|Não|ReadEngineConfig:ResultExpirationPeriod| somente contêineres v 2.0. Período de expiração do resultado em horas. O padrão é 48 horas. A configuração especifica quando o sistema deve limpar os resultados de reconhecimento. Por exemplo, se `resultExpirationPeriod=1` o sistema limpar o resultado de reconhecimento 1 hora após o processo. Se `resultExpirationPeriod=0` , o sistema limpará o resultado de reconhecimento depois que o resultado for recuperado.|
|Não|Cache: Redis| somente contêineres v 2.0. Habilita o armazenamento Redis para armazenar os resultados. Um cache será *necessário* se vários contêineres de leitura forem colocados atrás de um balanceador de carga.|
|Não|Fila: RabbitMQ|somente contêineres v 2.0. Habilita RabbitMQ para tarefas de expedição. A configuração é útil quando vários contêineres de leitura são colocados atrás de um balanceador de carga.|
|Não|Fila: Azure: QueueVisibilityTimeoutInMilliseconds | somente contêineres v3. x. A hora de uma mensagem ser invisível quando outro trabalhador a estiver processando. |
|Não|Armazenamento::D ocumentStore:: MongoDB|somente contêineres v 2.0. Habilita o MongoDB para armazenamento de resultado permanente. |
|Não|Armazenamento: ObjectStore: AzureBlob: ConnectionString| somente contêineres v3. x. Cadeia de conexão do armazenamento de BLOBs do Azure. |
|Não|Armazenamento: TimeToLiveInDays| somente contêineres v3. x. Período de expiração do resultado em dias. A configuração especifica quando o sistema deve limpar os resultados de reconhecimento. O padrão é 2 dias (48 horas), o que significa que qualquer resultado ao vivo por mais tempo do que esse período não tem garantia de ser recuperado com êxito. |
|Não|Tarefa: MaxRunningTimeSpanInMinutes| somente contêineres v3. x. Tempo máximo de execução para uma única solicitação. O padrão é de 60 minutos. |

## <a name="apikey-configuration-setting"></a>Configuração de configuração do ApiKey

A `ApiKey` configuração especifica a chave de recurso do Azure `Cognitive Services` usada para rastrear informações de cobrança para o contêiner. Você deve especificar um valor para o ApiKey e o valor deve ser uma chave válida para o recurso de _Serviços cognitivas_ especificado para a [`Billing`](#billing-configuration-setting) definição de configuração.

Essa configuração pode ser localizada no seguinte local:

* Portal do Azure: gerenciamento de recursos de **Serviços cognitivas** , em **chaves**

## <a name="applicationinsights-setting"></a>Configuração applicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Definição de configuração de cobrança

A `Billing` configuração especifica o URI do ponto de extremidade do recurso de _Serviços cognitivas_ no Azure usado para medir as informações de cobrança do contêiner. Você deve especificar um valor para essa definição de configuração e o valor deve ser um URI de ponto de extremidade válido para um recurso de _Serviços cognitivas_ no Azure. O contêiner relata o uso a cada 10 a 15 minutos.

Essa configuração pode ser localizada no seguinte local:

* Portal do Azure: visão geral **dos serviços cognitivas** , rotulados `Endpoint`

Lembre-se de adicionar o `vision/v1.0` Roteamento ao URI do ponto de extremidade, conforme mostrado na tabela a seguir. 

|Necessária| Name | Tipo de dados | Descrição |
|--|------|-----------|-------------|
|Sim| `Billing` | String | URI do ponto de extremidade de cobrança<br><br>Exemplo:<br>`Billing=https://westcentralus.api.cognitive.microsoft.com/vision/v1.0` |

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

|Opcional| Name | Tipo de dados | Descrição |
|-------|------|-----------|-------------|
|Não permitido| `Input` | String | Os contêineres de Pesquisa Visual Computacional não usam isso.|
|Opcional| `Output` | Cadeia de caracteres | O destino de montagem de saída. O valor padrão é `/output`. Esse é o local dos logs. Isso inclui logs de contêiner. <br><br>Exemplo:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Comandos docker run de exemplo

Os exemplos a seguir usam as definições de configuração para ilustrar como escrever e usar comandos `docker run`.  Quando em execução, o contêiner continuará a ser executado até que você o [pare](computer-vision-how-to-install-containers.md#stop-the-container).

* **Caractere de continuação de linha**: os comandos do Docker nas seções a seguir usam a barra invertida, `\` , como um caractere de continuação de linha. Substitua ou remova essa barra com base nos requisitos do sistema operacional de seu computador host. 
* **Ordem do argumento**: não altere a ordem dos argumentos, a menos que você esteja muito familiarizado com contêineres do Docker.

Substitua {_argument_name_} pelos seus próprios valores:

| Espaço reservado | Valor | Formato ou exemplo |
|-------------|-------|---|
| **{API_KEY}** | A chave do ponto de extremidade do `Computer Vision` recurso na `Computer Vision` página de chaves do Azure. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | O valor do ponto de extremidade de cobrança está disponível na `Computer Vision` página Visão geral do Azure.| Consulte [coletando parâmetros necessários](computer-vision-how-to-install-containers.md#gathering-required-parameters) para obter exemplos explícitos. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> As opções `Eula`, `Billing` e `ApiKey` devem ser especificadas para executar o contêiner; caso contrário, o contêiner não será iniciado.  Para mais informações, consulte [Faturamento](computer-vision-how-to-install-containers.md#billing).
> O valor de ApiKey é a **chave** da página de chaves de recurso do Azure `Cognitive Services` .

## <a name="container-docker-examples"></a>Exemplos de Docker de contêiner

Os exemplos do Docker a seguir são para o contêiner de leitura.


# <a name="version-32-preview"></a>[Versão 3,2-visualização](#tab/version-3-2)

### <a name="basic-example"></a>Exemplo básico

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:3.2-preview.1 \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}

```

### <a name="logging-example"></a>Exemplo de log 

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:3.2-preview.1 \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
Logging:Console:LogLevel:Default=Information
```

# <a name="version-20-preview"></a>[Versão 2,0-visualização](#tab/version-2)

### <a name="basic-example"></a>Exemplo básico

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}

```

### <a name="logging-example"></a>Exemplo de log 

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

* Examine [como instalar e executar contêineres](computer-vision-how-to-install-containers.md).
