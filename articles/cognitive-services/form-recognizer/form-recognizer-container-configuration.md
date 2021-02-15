---
title: Como configurar um contêiner para o reconhecedor de formulário
titleSuffix: Azure Cognitive Services
description: Saiba como configurar o contêiner do Reconhecimento de Formulários para analisar dados de formulário e de tabela.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: aahi
ms.openlocfilehash: 324b70fc810acc4faba4f488f821049f7eb0875e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86537996"
---
# <a name="configure-form-recognizer-containers"></a>Configurar contêineres do Reconhecimento de Formulários

[!INCLUDE [Form Recognizer containers limit](includes/container-limit.md)]

Usando os contêineres do Reconhecimento de Formulários do Azure, é possível criar uma arquitetura de aplicativo otimizada para aproveitar as funcionalidades de nuvem robustas e a localidade de borda.

Configure o ambiente de tempo de execução do contêiner do Reconhecimento de Formulários usando os argumentos de comando `docker run`. Esse contêiner tem várias configurações obrigatórias e outras configurações opcionais. Para ver alguns exemplos, consulte a seção ["Exemplos de comandos docker run"](#example-docker-run-commands). As configurações específicas do contêiner são as configurações de cobrança.

## <a name="configuration-settings"></a>Definições de configuração

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> As [`ApiKey`](#apikey-configuration-setting) [`Billing`](#billing-configuration-setting) configurações,, e [`Eula`](#eula-setting) são usadas juntas. Você precisa fornecer valores válidos para as três configurações; caso contrário, seu contêiner não será iniciado. Para obter mais informações sobre como usar essas configurações para instanciar um contêiner, consulte [Faturamento](form-recognizer-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>Configuração de configuração do ApiKey

A configuração `ApiKey` especifica a chave de recurso do Azure usada para rastrear informações de cobrança do contêiner. O valor da ApiKey precisa ser uma chave válida para o recurso de _Reconhecimento de Formulários_ especificado para `Billing` na seção "Definição de configuração de cobrança".

Você pode encontrar essa configuração no portal do Azure em **Gerenciamento de Recursos do Reconhecimento de Formulários**, em **Chaves**.

## <a name="applicationinsights-setting"></a>Configuração applicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Definição de configuração de cobrança

A configuração `Billing` especifica o URI do ponto de extremidade do recurso _Reconhecimento de Formulários_ no Azure usado para medir informações de cobrança para o contêiner. O valor dessa definição de configuração precisa ser um URI do ponto de extremidade válido para um recurso do _Reconhecimento de Formulários_ no Azure. O contêiner relata o uso a cada 10 a 15 minutos.

Você pode encontrar essa configuração no portal do Azure, em **Visão Geral do Reconhecimento de Formulários**, em **Ponto de extremidade**.

|Obrigatório| Nome | Tipo de dados | Descrição |
|--|------|-----------|-------------|
|Sim| `Billing` | String | URI do ponto de extremidade de cobrança. Para obter mais informações sobre como obter o URI de cobrança, consulte [coletando parâmetros necessários](form-recognizer-container-howto.md#gathering-required-parameters). Para saber mais e para obter uma lista completa de pontos de extremidade regionais, confira [Nomes de subdomínio personalizados para Serviços Cognitivos](../cognitive-services-custom-subdomains.md). |

## <a name="eula-setting"></a>Configuração de EULA

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Configurações de Fluentd

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Configurações das credenciais de proxy HTTP

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Configurações de log

[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]


## <a name="mount-settings"></a>Configurações de montagem

Use montagens de associação para ler e gravar dados do contêiner e para ele. Você pode especificar uma montagem de entrada ou uma montagem de saída especificando a `--mount` opção no [ `docker run` comando](https://docs.docker.com/engine/reference/commandline/run/).

O contêiner do Reconhecimento de Formulários exige uma montagem de entrada e uma montagem de saída. A montagem de entrada pode ser somente leitura e é necessária para acessar os dados que serão usados para treinamento e pontuação. A montagem de saída deve ser gravável e será usada para armazenar os modelos e os dados temporários.

A sintaxe exata do local da montagem do host varia de acordo com o sistema operacional do host. Além disso, a localização de montagem do [computador host](form-recognizer-container-howto.md#the-host-computer) pode não estar acessível devido a um conflito entre as permissões de conta de serviço do Docker e as permissões da localização de montagem do host.

|Opcional| Nome | Tipo de dados | Descrição |
|-------|------|-----------|-------------|
|Obrigatório| `Input` | String | O destino de montagem de entrada. O valor padrão é `/input`.    <br><br>Exemplo:<br>`--mount type=bind,src=c:\input,target=/input`|
|Obrigatório| `Output` | String | O destino de montagem de saída. O valor padrão é `/output`.  <br><br>Exemplo:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Comandos docker run de exemplo

Os exemplos a seguir usam as definições de configuração para ilustrar como escrever e usar comandos `docker run`. Quando em execução, o contêiner continuará a ser executado até que você o [pare](form-recognizer-container-howto.md#stop-the-container).

* **Caractere de continuação de linha**: os comandos do Docker nas seções a seguir usam uma barra invertida ( \\ ) como um caractere de continuação de linha. Substitua ou remova esse caractere, dependendo dos requisitos do sistema operacional de seu computador host.
* **Ordem do argumento**: não altere a ordem dos argumentos, a menos que você esteja familiarizado com contêineres do Docker.

Substitua {_argument_name_} na tabela a seguir por seus próprios valores:

| Espaço reservado | Valor |
|-------------|-------|
| **{FORM_RECOGNIZER_API_KEY}** | A chave usada para iniciar o contêiner. Ela está disponível na página Chaves de Reconhecimento de Formulários do portal do Azure. |
| **{FORM_RECOGNIZER_ENDPOINT_URI}** | O valor de URI do ponto de extremidade de cobrança está disponível na página Visão Geral do Reconhecimento de Formulários do portal do Azure.|
| **{COMPUTER_VISION_API_KEY}** | A chave está disponível na página Chaves de API de Pesquisa Visual Computacional do portal do Azure.|
| **{COMPUTER_VISION_ENDPOINT_URI}** | O ponto de extremidade de cobrança. Se você estiver usando um recurso baseado em nuvem da Pesquisa Visual Computacional, o valor do URI estará disponível na página Visão Geral da API de Pesquisa Visual Computacional do portal do Azure. Se você estiver usando um contêiner *cognitiva-Services-Recognize-Text* , use a URL do ponto de extremidade de cobrança que é passada para o contêiner no `docker run` comando. |

Consulte [coletando parâmetros necessários](form-recognizer-container-howto.md#gathering-required-parameters) para obter detalhes sobre como obter esses valores.

[!INCLUDE [cognitive-services-custom-subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> Para executar o contêiner, especifique as opções `Eula`, `Billing` e `ApiKey`; caso contrário, o contêiner não será iniciado. Para mais informações, consulte [Faturamento](#billing-configuration-setting).

## <a name="form-recognizer-container-docker-examples"></a>Exemplos do Docker do contêiner do Reconhecimento de Formulários

Os exemplos do Docker a seguir referem-se ao contêiner do Reconhecimento de Formulários.

### <a name="basic-example-for-form-recognizer"></a>Exemplo básico do Reconhecimento de Formulários

```Docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={FORM_RECOGNIZER_ENDPOINT_URI} \
ApiKey={FORM_RECOGNIZER_API_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

### <a name="logging-example-for-form-recognizer"></a>Exemplo de log do Reconhecimento de Formulários

```Docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={FORM_RECOGNIZER_ENDPOINT_URI} \
ApiKey={FORM_RECOGNIZER_API_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
Logging:Console:LogLevel:Default=Information
```

## <a name="next-steps"></a>Próximas etapas

* Como [Instalar e executar contêineres](form-recognizer-container-howto.md).
