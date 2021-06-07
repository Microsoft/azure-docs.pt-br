---
title: Instalar e executar contêineres do Docker para o LUIS
titleSuffix: Azure Cognitive Services
description: Usar o contêiner do LUIS para carregar seu aplicativo treinado ou publicado e obter acesso às previsões locais.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18, cog-serv-seo-aug-2020
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 03/02/2021
ms.author: aahi
keywords: Local, Docker e contêiner
ms.openlocfilehash: e157e976186f03aa984877435c42b996ce476740
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102040185"
---
# <a name="install-and-run-docker-containers-for-luis"></a>Instalar e executar contêineres do Docker para o LUIS

[!INCLUDE [container image location note](../containers/includes/image-location-note.md)]

Os contêineres permitem usar o LUIS em seu ambiente. Contêineres são excelentes para especificar requisitos de segurança e governança de dados. Neste artigo, você aprenderá a baixar, instalar e executar um contêiner do LUIS.

O contêiner do LUIS (reconhecimento vocal) carrega seu modelo de Reconhecimento Vocal treinado ou publicado. Do mesmo modo que um [aplicativo LUIS](https://www.luis.ai), o contêiner do Docker fornece acesso às previsões de consulta de pontos de extremidade da API do contêiner. Você pode coletar logs de consulta do contêiner e carregá-los novamente no aplicativo de Reconhecimento vocal para aumentar a precisão da previsão do aplicativo.

O vídeo a seguir demonstra como usar esse contêiner.

[![Demonstração de contêiner para os Serviços Cognitivos](./media/luis-container-how-to/luis-containers-demo-video-still.png)](https://aka.ms/luis-container-demo)

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/cognitive-services/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Veja os seguintes pré-requisitos para executar o contêiner do LUIS:

|Obrigatório|Finalidade|
|--|--|
|Mecanismo do Docker| É necessário ter o Mecanismo Docker instalado em um [computador host](#the-host-computer). O Docker fornece pacotes que configuram o ambiente do Docker no [macOS](https://docs.docker.com/docker-for-mac/), no [Windows](https://docs.docker.com/docker-for-windows/) e no [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Para instruções sobre conceitos básicos do Docker e de contêiner, consulte a [visão geral do Docker](https://docs.docker.com/engine/docker-overview/).<br><br> O Docker deve ser configurado para permitir que os contêineres conectem-se e enviem dados de cobrança para o Azure. <br><br> **No Windows**, o Docker também deve ser configurado para dar suporte a contêineres do Linux.<br><br>|
|Familiaridade com o Docker | É necessário ter uma compreensão básica de conceitos do Docker, como registros, repositórios, contêineres e imagens de contêiner, bem como conhecimento dos comandos básicos do `docker`.|
|Recurso dos `Cognitive Services` do Azure e um arquivo de [aplicativo empacotado](luis-how-to-start-new-app.md) do LUIS |Para usar o contêiner, você precisará ter:<br><br>*Um recurso dos _Serviços Cognitivos_ do Azure e a chave de cobrança associada ao URI do ponto de extremidade de cobrança. Os dois valores estão disponíveis nas páginas de Visão Geral e Chaves. Além disso, eles são necessários para iniciar o contêiner. <br>* Um aplicativo publicado ou treinado empacotado como uma entrada montada para o contêiner com sua ID do aplicativo associado. É possível obter o arquivo empacotado no portal do LUIS ou nas APIs de Criação. Caso esteja obtendo um aplicativo empacotado do LUIS nas [APIs de Criação](#authoring-apis-for-package-file), também será preciso obter uma _Chave de Criação_.<br><br>Esses requisitos são usados para transmitir argumentos de linha de comando para as seguintes variáveis:<br><br>**{AUTHORING_KEY}** : esta chave é usada para obter o aplicativo empacotado do serviço do LUIS na nuvem e carregar logs de consulta de volta na nuvem. O formato é `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.<br><br>**{APP_ID}** : esta ID é usada para selecionar o Aplicativo. O formato é `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.<br><br>**{API_KEY}** : esta chave é usada para iniciar o contêiner. Você pode encontrar a chave do ponto de extremidade em dois locais. O primeiro local é o portal do Azure na lista de chaves do recurso dos _Serviços Cognitivos_. A chave do ponto de extremidade também está disponível no portal do LUIS na página Configurações de chaves e de ponto de extremidade. Não use a chave de inicialização.<br><br>**{ENDPOINT_URI}** : o ponto de extremidade fornecido na página de visão geral.<br><br>A [chave de criação e a chave do ponto de extremidade](luis-limits.md#key-limits) têm finalidades diferentes. Não use uma no lugar da outra. |

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="authoring-apis-for-package-file"></a>Criação de APIs para um arquivo de pacote

Criação de APIs para aplicativos empacotados:

* [API do pacote publicado](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagepublishedapplicationasgzip)
* [API do pacote somente treinado e não publicado](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagetrainedapplicationasgzip)

### <a name="the-host-computer"></a>O computador host

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Recomendações e requisitos do contêiner

A tabela abaixo listará os valores mínimos e recomendados para o host de contêiner. Seus requisitos poderão mudar de acordo com o volume de tráfego.

|Contêiner| Mínimo | Recomendadas | TPS<br>(mínimo e máximo)|
|-----------|---------|-------------|--|
|LUIS|1 núcleo e 2 GB de memória|1 núcleo e 4 GB de memória|20 e 40|

* Cada núcleo precisa ser de pelo menos 2,6 GHz (gigahertz) ou mais rápido.
* TPS – transações por segundo

Memória e núcleo correspondem às configurações `--cpus` e `--memory`, que são usadas como parte do comando `docker run`.

## <a name="get-the-container-image-with-docker-pull"></a>Obter a imagem de contêiner com `docker pull`

Use o comando [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) para fazer download de uma imagem de contêiner do repositório `mcr.microsoft.com/azure-cognitive-services/language/luis`:

```
docker pull mcr.microsoft.com/azure-cognitive-services/language/luis:latest
```

Para obter uma descrição completa das marcas disponíveis, como as `latest` usadas no comando anterior, confira [LUIS](https://go.microsoft.com/fwlink/?linkid=2043204) no Hub do Docker.

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>Como usar o contêiner

Depois que o contêiner estiver no [computador host](#the-host-computer), use o processo a seguir para trabalhar com o contêiner.

![Processo para usar o contêiner do Serviço Inteligente de Reconhecimento Vocal (LUIS)](./media/luis-container-how-to/luis-flow-with-containers-diagram.jpg)

1. [Exporte o pacote](#export-packaged-app-from-luis) para o contêiner no portal do LUIS ou nas APIs de LUIS.
1. Mova o arquivo de pacote para o diretório de **entrada** exigido no [computador host](#the-host-computer). Não renomeie, altere, substitua nem descompacte o arquivo de pacote do LUIS.
1. [Execute o contêiner](#run-the-container-with-docker-run), com a _montagem de entrada_ e as configurações de cobrança necessárias. Há outros [exemplos](luis-container-configuration.md#example-docker-run-commands) do comando `docker run` disponíveis.
1. [Consultar o ponto de extremidade de previsão do contêiner](#query-the-containers-prediction-endpoint).
1. Quando você terminar de usar o contêiner [importe os logs do ponto de extremidade](#import-the-endpoint-logs-for-active-learning) da montagem de saída no portal do LUIS e [pare](#stop-the-container) o contêiner.
1. Use o [aprendizado ativo](luis-how-to-review-endpoint-utterances.md) do portal do LUIS na página **Examinar declarações do ponto de extremidade** para aprimorar o aplicativo.

O aplicativo em execução no contêiner não pode ser alterado. Para alterar o aplicativo no contêiner, será preciso alterá-lo no serviço do LUIS usando o portal do [LUIS](https://www.luis.ai) ou as [APIs de Criação](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f) do LUIS. Treine e/ou publique e, em seguida, baixe um novo pacote e execute novamente o contêiner.

O aplicativo LUIS dentro do contêiner não pode ser exportado novamente para o serviço de LUIS. Somente os logs de consulta podem ser carregados.

## <a name="export-packaged-app-from-luis"></a>Exportar aplicativo empacotado do LUIS

O contêiner de LUIS requer um aplicativo LUIS treinado ou publicado para responder a consultas de previsão das declarações dos usuários. Para obter o aplicativo LUIS, use a API do pacote publicado ou treinado.

O local padrão é o subdiretório `input` do local em que você executa o comando `docker run`.

Coloque o arquivo de pacote em um diretório e faça referência a esse diretório como a montagem de entrada quando você executar o contêiner do Docker.

### <a name="package-types"></a>Tipos de pacote

O diretório de montagem de entrada pode conter modelos de **Produção**, **Preparo** e com **Controle de Versão** do aplicativo de modo simultâneo. Todos os pacotes são montados.

|Tipo de pacote|API do ponto de extremidade de consulta|Disponibilidade de consulta|Formato do nome de arquivo do pacote|
|--|--|--|--|
|Controle de Versão|GET e POST|Somente contêiner|`{APP_ID}_v{APP_VERSION}.gz`|
|Staging|GET e POST|Azure e contêiner|`{APP_ID}_STAGING.gz`|
|Produção|GET e POST|Azure e contêiner|`{APP_ID}_PRODUCTION.gz`|

> [!IMPORTANT]
> Não renomeie, altere, substitua nem descompacte arquivos de pacote do LUIS.

### <a name="packaging-prerequisites"></a>Pré-requisitos de empacotamento

Antes de empacotar um aplicativo LUIS, você deverá ter o seguinte:

|Requisitos de empacotamento|Detalhes|
|--|--|
|Instância de recurso dos _Serviços Cognitivos_ do Azure|As regiões com suporte incluem<br><br>Oeste dos EUA (`westus`)<br>Europa Ocidental (`westeurope`)<br>Leste da Austrália (`australiaeast`)|
|Aplicativo LUIS treinado ou publicado|Sem [dependências sem suporte][unsupported-dependencies]. |
|Acesso ao sistema de arquivos do [computador host](#the-host-computer) |O computador host deve permitir uma [montagem de entrada](luis-container-configuration.md#mount-settings).|

### <a name="export-app-package-from-luis-portal"></a>Exportar pacote de aplicativo do portal do LUIS

O [portal](https://www.luis.ai) do LUIS permite exportar o pacote do aplicativo publicado ou treinado.

### <a name="export-published-apps-package-from-luis-portal"></a>Exportar o pacote do aplicativo publicado do portal do LUIS

O pacote do aplicativo publicado está disponível na página da lista **Meus aplicativos**.

1. Entre no portal do [LUIS](https://www.luis.ai).
1. Marque a caixa de seleção à esquerda do nome do aplicativo na lista.
1. Selecione o item **Exportar** na barra de ferramentas contextual acima da lista.
1. Selecione **Exportar para o contêiner (GZIP)**.
1. Selecione o ambiente do **Slot de produção** ou do **Slot de preparo**.
1. O download do pacote é feito do navegador.

![Exportar o pacote publicado para o contêiner a partir do menu Exportar da página do aplicativo](./media/luis-container-how-to/export-published-package-for-container.png)

### <a name="export-versioned-apps-package-from-luis-portal"></a>Exportar o pacote do aplicativo com controle de versão do portal do LUIS

O pacote do aplicativo com controle com versão está disponível na página da lista de **Versões**.

1. Entre no portal do [LUIS](https://www.luis.ai).
1. Selecione o aplicativo na lista.
1. Selecione **Gerenciar** na barra de navegação do aplicativo.
1. Selecione **Versões** na barra de navegação à esquerda.
1. Marque a caixa de seleção à esquerda do nome da versão na lista.
1. Selecione o item **Exportar** na barra de ferramentas contextual acima da lista.
1. Selecione **Exportar para o contêiner (GZIP)**.
1. O download do pacote é feito do navegador.

![Exportar o pacote treinado para o contêiner a partir do menu Exportar da página Versões](./media/luis-container-how-to/export-trained-package-for-container.png)

### <a name="export-published-apps-package-from-api"></a>Exportar o pacote do aplicativo publicado da API

Use o método de API REST a seguir para empacotar um aplicativo LUIS que você já [publicou](luis-how-to-publish-app.md). Substitua os espaços reservados por seus próprios valores apropriados na chamada à API, usando a tabela abaixo da especificação de HTTP.

```http
GET /luis/api/v2.0/package/{APP_ID}/slot/{SLOT_NAME}/gzip HTTP/1.1
Host: {AZURE_REGION}.api.cognitive.microsoft.com
Ocp-Apim-Subscription-Key: {AUTHORING_KEY}
```

| Espaço reservado | Valor |
|-------------|-------|
| **{APP_ID}** | A ID de aplicativo do aplicativo LUIS publicado. |
| **{SLOT_NAME}** | O ambiente do aplicativo LUIS publicado. Use um dos seguintes valores:<br/>`PRODUCTION`<br/>`STAGING` |
| **{AUTHORING_KEY}** | A chave de criação da conta do LUIS para o aplicativo LUIS publicado.<br/>Você pode obter sua chave de criação na página **Configurações do Usuário** no portal do LUIS. |
| **{AZURE_REGION}** | A região do Azure apropriada:<br/><br/>`westus` – Oeste dos EUA<br/>`westeurope` – Europa Ocidental<br/>`australiaeast` – Leste da Austrália |

Para baixar o pacote publicado, confira a [documentação da API clicando aqui][download-published-package]. Caso ele seja baixado com êxito, a resposta será um arquivo de pacote do LUIS. Salve o arquivo no local de armazenamento especificado para a montagem de entrada do contêiner.

### <a name="export-versioned-apps-package-from-api"></a>Exportar o pacote do aplicativo com controle de versão da API

Use o método de API REST a seguir para empacotar um aplicativo LUIS que você já [treinou](luis-how-to-train.md). Substitua os espaços reservados por seus próprios valores apropriados na chamada à API, usando a tabela abaixo da especificação de HTTP.

```http
GET /luis/api/v2.0/package/{APP_ID}/versions/{APP_VERSION}/gzip HTTP/1.1
Host: {AZURE_REGION}.api.cognitive.microsoft.com
Ocp-Apim-Subscription-Key: {AUTHORING_KEY}
```

| Espaço reservado | Valor |
|-------------|-------|
| **{APP_ID}** | A ID de aplicativo do aplicativo LUIS treinado. |
| **{APP_VERSION}** | A versão de aplicativo do aplicativo LUIS treinado. |
| **{AUTHORING_KEY}** | A chave de criação da conta do LUIS para o aplicativo LUIS publicado.<br/>Você pode obter sua chave de criação na página **Configurações do Usuário** no portal do LUIS. |
| **{AZURE_REGION}** | A região do Azure apropriada:<br/><br/>`westus` – Oeste dos EUA<br/>`westeurope` – Europa Ocidental<br/>`australiaeast` – Leste da Austrália |

Para baixar o pacote com controle de versão, confira a [documentação da API clicando aqui][download-versioned-package]. Caso ele seja baixado com êxito, a resposta será um arquivo de pacote do LUIS. Salve o arquivo no local de armazenamento especificado para a montagem de entrada do contêiner.

## <a name="run-the-container-with-docker-run"></a>Executar o contêiner com `docker run`

Use o comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) para executar o contêiner. Confira [como coletar parâmetros necessários](#gathering-required-parameters) para obter detalhes sobre de que modo obter os valores `{ENDPOINT_URI}` e `{API_KEY}`.

[Exemplos](luis-container-configuration.md#example-docker-run-commands) do comando `docker run` estão disponíveis.

```console
docker run --rm -it -p 5000:5000 ^
--memory 4g ^
--cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output\,target=/output ^
mcr.microsoft.com/azure-cognitive-services/language/luis ^
Eula=accept ^
Billing={ENDPOINT_URI} ^
ApiKey={API_KEY}
```

* Este exemplo usa o diretório fora da unidade `C:` para evitar conflitos de permissão no Windows. Se você precisar usar um diretório específico como o diretório de entrada, talvez seja necessário conceder ao Docker permissão de serviço.
* Não altere a ordem dos argumentos, a menos que esteja familiarizado com contêineres do docker.
* Caso esteja usando um sistema operacional diferente, use o console/terminal, a sintaxe de pasta para executar montagens e o caractere de continuação de linha adequados para seu sistema. Esses exemplos pressupõem que você tem um console do Windows com um caractere de continuação de linha `^`. Como o contêiner é um sistema operacional Linux, a montagem de destino usa uma sintaxe de pasta em estilo Linux.

Esse comando:

* Executa um contêiner da imagem de contêiner do LUIS
* Carrega o aplicativo LUIS da montagem de entrada em *C:\input*, localizado no host de contêiner
* Aloca dois núcleos de CPU e 4 GB (gigabytes) de memória
* Expõe a porta TCP 5000 e aloca um pseudo-TTY para o contêiner
* Salva logs de contêiner e do LUIS na montagem de saída em *C:\output*, localizado no host de contêiner
* Remove automaticamente o contêiner depois que ele sai. A imagem de contêiner ainda fica disponível no computador host.

Há outros [exemplos](luis-container-configuration.md#example-docker-run-commands) do comando `docker run` disponíveis.

> [!IMPORTANT]
> As opções `Eula`, `Billing` e `ApiKey` devem ser especificadas para executar o contêiner; caso contrário, o contêiner não será iniciado.  Para mais informações, consulte [Faturamento](#billing).
> O valor de ApiKey é a **Chave** da página de **Recursos do Azure** no portal do LUIS, bem como está disponível na página de chaves do recurso dos `Cognitive Services` do Azure.

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="endpoint-apis-supported-by-the-container"></a>APIs de ponto de extremidade compatíveis com o contêiner

As versões V2 e [V3](luis-migration-api-v3.md) da API estão disponíveis juntamente com o contêiner.

## <a name="query-the-containers-prediction-endpoint"></a>Consultar o ponto de extremidade de previsão do contêiner

O contêiner fornece APIs de ponto de extremidade de previsão de consulta baseadas em REST. Os pontos de extremidade para aplicativos publicados (de preparo ou produção) têm uma rota _diferente_ dos pontos de extremidade para aplicativos com controle de versão.

Use o host, `http://localhost:5000`, para as APIs do contêiner.

# <a name="v3-prediction-endpoint"></a>[Ponto de extremidade de previsão V3](#tab/v3)

|Tipo de pacote|Verbo HTTP|Rota|Parâmetros de consulta|
|--|--|--|--|
|Publicado|GET e POST|`/luis/v3.0/apps/{appId}/slots/{slotName}/predict?`|`query={query}`<br>[`&verbose`]<br>[`&log`]<br>[`&show-all-intents`]|
|Controle de Versão|GET e POST|`/luis/v3.0/apps/{appId}/versions/{versionId}/predict?`|`query={query}`<br>[`&verbose`]<br>[`&log`]<br>[`&show-all-intents`]|

Os parâmetros de consulta configuram como e o que é retornado na resposta da consulta:

|Parâmetro de consulta|Tipo|Finalidade|
|--|--|--|
|`query`|string|A declaração do usuário.|
|`verbose`|booleano|Um valor booliano indicando se é necessário retornar todos os metadados para os modelos previstos. O padrão é false.|
|`log`|booleano|Registra as consultas, que podem ser usadas posteriormente no [aprendizado ativo](luis-how-to-review-endpoint-utterances.md). O padrão é false.|
|`show-all-intents`|booleano|Um valor booliano indicando se é necessário retornar todas as intenções ou somente a intenção com maior pontuação. O padrão é false.|

# <a name="v2-prediction-endpoint"></a>[Ponto de extremidade de previsão V2](#tab/v2)

|Tipo de pacote|Verbo HTTP|Rota|Parâmetros de consulta|
|--|--|--|--|
|Publicado|[GET](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78) e [POST](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee79)|`/luis/v2.0/apps/{appId}?`|`q={q}`<br>`&staging`<br>[`&timezoneOffset`]<br>[`&verbose`]<br>[`&log`]<br>|
|Controle de Versão|GET e POST|`/luis/v2.0/apps/{appId}/versions/{versionId}?`|`q={q}`<br>[`&timezoneOffset`]<br>[`&verbose`]<br>[`&log`]|

Os parâmetros de consulta configuram como e o que é retornado na resposta da consulta:

|Parâmetro de consulta|Tipo|Finalidade|
|--|--|--|
|`q`|string|A declaração do usuário.|
|`timezoneOffset`|número|O timezoneOffset permite [alterar o fuso horário](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) usado pela datetimeV2 predefinida da entidade.|
|`verbose`|booleano|Retorna todas as intenções e suas pontuações quando definido como true. O padrão é false, o que retorna apenas a intenção principal.|
|`staging`|booleano|Retorna a consulta dos resultados do ambiente de preparo quando definido como true. |
|`log`|booleano|Registra as consultas, que podem ser usadas posteriormente no [aprendizado ativo](luis-how-to-review-endpoint-utterances.md). O padrão é true.|

***

### <a name="query-the-luis-app"></a>Executar uma consulta no aplicativo LUIS

Um exemplo de comando CURL para consultar o contêiner em relação a um aplicativo publicado é:

# <a name="v3-prediction-endpoint"></a>[Ponto de extremidade de previsão V3](#tab/v3)

Use a seguinte API para consultar um modelo em um slot:

```bash
curl -G \
-d verbose=false \
-d log=true \
--data-urlencode "query=turn the lights on" \
"http://localhost:5000/luis/v3.0/apps/{APP_ID}/slots/production/predict"
```

Para executar consultas no ambiente de **Preparo**, substitua `production` por `staging` na rota:

`http://localhost:5000/luis/v3.0/apps/{APP_ID}/slots/staging/predict`

Use a seguinte API para consultar um modelo com controle de versão:

```bash
curl -G \
-d verbose=false \
-d log=false \
--data-urlencode "query=turn the lights on" \
"http://localhost:5000/luis/v3.0/apps/{APP_ID}/versions/{APP_VERSION}/predict"
```

# <a name="v2-prediction-endpoint"></a>[Ponto de extremidade de previsão V2](#tab/v2)

Use a seguinte API para consultar um modelo em um slot:

```bash
curl -X GET \
"http://localhost:5000/luis/v2.0/apps/{APP_ID}?q=turn%20on%20the%20lights&staging=false&timezoneOffset=0&verbose=false&log=true" \
-H "accept: application/json"
```
Para fazer consultas no ambiente de **Preparo**, altere o valor de parâmetro da cadeia de caracteres de consulta **staging** para true:

`staging=true`

Use a seguinte API para consultar um modelo com controle de versão:

```bash
curl -X GET \
"http://localhost:5000/luis/v2.0/apps/{APP_ID}/versions/{APP_VERSION}?q=turn%20on%20the%20lights&timezoneOffset=0&verbose=false&log=true" \
-H "accept: application/json"
```
O nome da versão tem no máximo 10 caracteres e contém apenas caracteres permitidos em uma URL.

***

## <a name="import-the-endpoint-logs-for-active-learning"></a>Importar os logs de ponto de extremidade para aprendizado ativo

Caso uma montagem de saída seja especificada para o contêiner do LUIS, os arquivos de log da consulta do aplicativo serão salvos no diretório de saída em que `{INSTANCE_ID}` representa a ID do contêiner. O log de consulta do aplicativo contém a consulta, a resposta e os carimbos de data/hora para cada consulta de previsão enviada para o contêiner de LUIS.

O local a seguir mostra a estrutura de diretórios aninhados para os arquivos de log do contêiner.
```
/output/luis/{INSTANCE_ID}/
```

No portal do LUIS, selecione seu aplicativo e selecione **Importar logs de ponto de extremidade** para carregá-los.

![Importar arquivos de log do contêiner para aprendizado ativo](./media/luis-container-how-to/upload-endpoint-log-files.png)

Depois que o log é carregado, [examine as declarações do ponto de extremidade](./luis-concept-review-endpoint-utterances.md) no portal do LUIS.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Parar o contêiner

Para desligar o contêiner, no ambiente de linha de comando em que o contêiner estiver em execução, pressione **Ctrl+C**.

## <a name="troubleshooting"></a>Solução de problemas

Se você executar o contêiner com uma [montagem](luis-container-configuration.md#mount-settings) de saída e o registro em log habilitado, o contêiner gerará arquivos de log que são úteis para solucionar problemas que ocorrem durante a inicialização ou execução do contêiner.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Cobrança

O contêiner do LUIS envia informações de cobrança ao Azure usando um recurso dos _Serviços Cognitivos_ em sua conta do Azure.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Para obter mais informações sobre essas opções, consulte [Configurar contêineres](luis-container-configuration.md).

## <a name="summary"></a>Resumo

Neste artigo, você aprendeu conceitos e fluxo de trabalho para baixar, instalar e executar os contêineres do Serviço Inteligente de Reconhecimento Vocal (LUIS). Em resumo:

* O Serviço Inteligente de Reconhecimento Vocal (LUIS) oferece um contêiner do Linux para Docker que fornece previsões de declarações de consultas a pontos de extremidade.
* Imagens de contêiner são baixadas do Registro de Contêiner da Microsoft (MCR).
* Imagens de contêiner são executadas no Docker.
* Você pode usar a API REST para consultar os pontos de extremidade do contêiner especificando o URI de host do contêiner.
* Você deve especificar informações de faturamento ao instanciar um contêiner.

> [!IMPORTANT]
> Os contêineres dos Serviços Cognitivos não estão licenciados para execução sem estarem conectados ao Azure para medição. Os clientes precisam ativar os contêineres para comunicar informações de cobrança com o serviço de medição em todos os momentos. Os contêineres de Serviços Cognitivos não enviam dados do cliente (por exemplo, a imagem ou o texto que está sendo analisado) para a Microsoft.

## <a name="next-steps"></a>Próximas etapas

* Revise [Configurar contêineres](luis-container-configuration.md) para configurações.
* Confira as [limitações de contêiner do LUIS](luis-container-limitations.md) para obter restrições de capacidade conhecidas.
* Consulte a [Solução de problemas](troubleshooting.md) para resolver problemas relacionados à funcionalidade LUIS.
* Use mais [Contêineres de Serviços Cognitivos](../cognitive-services-container-support.md)

<!-- Links - external -->
[download-published-package]: https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagepublishedapplicationasgzip
[download-versioned-package]: https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagetrainedapplicationasgzip

[unsupported-dependencies]: luis-container-limitations.md#unsupported-dependencies-for-latest-container