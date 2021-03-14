---
title: Instalar e executar contêineres do Docker para LUIS
titleSuffix: Azure Cognitive Services
description: Use o contêiner LUIS para carregar seu aplicativo treinado ou publicado e obter acesso às suas previsões locais.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18, cog-serv-seo-aug-2020
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 03/02/2021
ms.author: aahi
keywords: local, Docker, contêiner
ms.openlocfilehash: e157e976186f03aa984877435c42b996ce476740
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102040185"
---
# <a name="install-and-run-docker-containers-for-luis"></a>Instalar e executar contêineres do Docker para LUIS

[!INCLUDE [container image location note](../containers/includes/image-location-note.md)]

Os contêineres permitem que você use o LUIS em seu próprio ambiente. Contêineres são excelentes para especificar requisitos de segurança e governança de dados. Neste artigo, você aprenderá a baixar, instalar e executar um contêiner LUIS.

O contêiner Reconhecimento vocal (LUIS) carrega seu modelo de Reconhecimento vocal treinado ou publicado. Como um [aplicativo Luis](https://www.luis.ai), o contêiner do Docker fornece acesso às previsões de consulta dos pontos de extremidade de API do contêiner. Você pode coletar logs de consulta do contêiner e carregá-los de volta para o aplicativo Reconhecimento vocal para melhorar a precisão da previsão do aplicativo.

O vídeo a seguir demonstra como usar esse contêiner.

[![Demonstração do contêiner para serviços cognitivas](./media/luis-container-how-to/luis-containers-demo-video-still.png)](https://aka.ms/luis-container-demo)

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/cognitive-services/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para executar o contêiner LUIS, observe os seguintes pré-requisitos:

|Obrigatório|Finalidade|
|--|--|
|Mecanismo do Docker| É necessário ter o Mecanismo Docker instalado em um [computador host](#the-host-computer). O Docker fornece pacotes que configuram o ambiente do Docker no [macOS](https://docs.docker.com/docker-for-mac/), no [Windows](https://docs.docker.com/docker-for-windows/) e no [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Para instruções sobre conceitos básicos do Docker e de contêiner, consulte a [visão geral do Docker](https://docs.docker.com/engine/docker-overview/).<br><br> O Docker deve ser configurado para permitir que os contêineres conectem-se e enviem dados de cobrança para o Azure. <br><br> **No Windows**, o Docker também deve ser configurado para dar suporte a contêineres do Linux.<br><br>|
|Familiaridade com o Docker | É necessário ter uma compreensão básica de conceitos do Docker, como registros, repositórios, contêineres e imagens de contêiner, bem como conhecimento dos comandos básicos do `docker`.|
|Recurso do Azure `Cognitive Services` e arquivo de [aplicativo EMPACOTAdo](luis-how-to-start-new-app.md) Luis |Para usar o contêiner, você precisará ter:<br><br>* Um recurso do Azure de _Serviços cognitivas_ e a chave de cobrança associada ao URI do ponto de extremidade de cobrança. Ambos os valores estão disponíveis nas páginas visão geral e chaves para o recurso e são necessários para iniciar o contêiner. <br>* Um aplicativo publicado ou treinado empacotado como uma entrada montada para o contêiner com sua ID do aplicativo associado. Você pode obter o arquivo empacotado no portal do LUIS ou as APIs de criação. Se você estiver obtendo um aplicativo LUIS empacotado das [APIs de criação](#authoring-apis-for-package-file), também precisará da sua _chave de criação_.<br><br>Esses requisitos são usados para transmitir argumentos de linha de comando para as seguintes variáveis:<br><br>**{AUTHORING_KEY}**: essa chave é usada para obter o aplicativo empacotado do serviço Luis na nuvem e carregar os logs de consulta de volta para a nuvem. O formato é `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.<br><br>**{APP_ID}**: essa ID é usada para selecionar o aplicativo. O formato é `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.<br><br>**{Api_key}**: essa chave é usada para iniciar o contêiner. Você pode encontrar a chave do ponto de extremidade em dois locais. A primeira é a portal do Azure na lista de chaves do recurso de _Serviços cognitivas_ . A chave do ponto de extremidade também está disponível no portal do LUIS na página Configurações de chaves e de ponto de extremidade. Não use a chave de inicialização.<br><br>**{ENDPOINT_URI}**: o ponto de extremidade conforme fornecido na página Visão geral.<br><br>A [chave de criação e a chave do ponto de extremidade](luis-limits.md#key-limits) têm finalidades diferentes. Não use uma no lugar da outra. |

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="authoring-apis-for-package-file"></a>Criando APIs para o arquivo de pacote

Criando APIs para aplicativos empacotados:

* [API do pacote publicado](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagepublishedapplicationasgzip)
* [API de pacote apenas não publicada, com treinamento](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagetrainedapplicationasgzip)

### <a name="the-host-computer"></a>O computador host

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Recomendações e requisitos do contêiner

A tabela abaixo lista os valores mínimos e recomendados para o host do contêiner. Seus requisitos podem mudar dependendo do volume de tráfego.

|Contêiner| Mínimo | Recomendadas | TPS<br>(Mínimo, máximo)|
|-----------|---------|-------------|--|
|LUIS|1 núcleo, 2 GB de memória|1 núcleo, 4 GB de memória|20, 40|

* Cada núcleo precisa ser de pelo menos 2,6 GHz (gigahertz) ou mais rápido.
* TPS – transações por segundo

Memória e núcleo correspondem às configurações `--cpus` e `--memory`, que são usadas como parte do comando `docker run`.

## <a name="get-the-container-image-with-docker-pull"></a>Obter a imagem de contêiner com `docker pull`

Use o [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) comando para baixar uma imagem de contêiner do `mcr.microsoft.com/azure-cognitive-services/language/luis` repositório:

```
docker pull mcr.microsoft.com/azure-cognitive-services/language/luis:latest
```

Para obter uma descrição completa das marcas disponíveis, como as `latest` usadas no comando anterior, confira [LUIS](https://go.microsoft.com/fwlink/?linkid=2043204) no Hub do Docker.

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>Como usar o contêiner

Depois que o contêiner estiver no [computador host](#the-host-computer), use o processo a seguir para trabalhar com o contêiner.

![Processo para usar o contêiner do Serviço Inteligente de Reconhecimento Vocal (LUIS)](./media/luis-container-how-to/luis-flow-with-containers-diagram.jpg)

1. [Exporte o pacote](#export-packaged-app-from-luis) para o contêiner no portal do LUIS ou nas APIs de LUIS.
1. Mova o arquivo de pacote para o diretório de **entrada** exigido no [computador host](#the-host-computer). Não renomeie, altere, substitua ou Descompacte o arquivo de pacote LUIS.
1. [Execute o contêiner](#run-the-container-with-docker-run), com a _montagem de entrada_ e as configurações de cobrança necessárias. Há outros [exemplos](luis-container-configuration.md#example-docker-run-commands) do comando `docker run` disponíveis.
1. [Consultar o ponto de extremidade de previsão do contêiner](#query-the-containers-prediction-endpoint).
1. Quando você terminar de usar o contêiner [importe os logs do ponto de extremidade](#import-the-endpoint-logs-for-active-learning) da montagem de saída no portal do LUIS e [pare](#stop-the-container) o contêiner.
1. Use o [aprendizado ativo](luis-how-to-review-endpoint-utterances.md) do portal do LUIS na página **Examinar declarações do ponto de extremidade** para aprimorar o aplicativo.

O aplicativo em execução no contêiner não pode ser alterado. Para alterar o aplicativo no contêiner, você precisa alterar o aplicativo no serviço LUIS usando o portal do [Luis](https://www.luis.ai) ou usar as [APIs de criação](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f)do Luis. Treine e/ou publique e, em seguida, baixe um novo pacote e execute novamente o contêiner.

O aplicativo LUIS dentro do contêiner não pode ser exportado novamente para o serviço de LUIS. Somente os logs de consulta podem ser carregados.

## <a name="export-packaged-app-from-luis"></a>Exportar aplicativo empacotado do LUIS

O contêiner de LUIS requer um aplicativo LUIS treinado ou publicado para responder a consultas de previsão das declarações dos usuários. Para obter o aplicativo LUIS, use a API do pacote publicado ou treinado.

O local padrão é o subdiretório `input` do local em que você executa o comando `docker run`.

Coloque o arquivo de pacote em um diretório e faça referência a esse diretório como a montagem de entrada quando você executar o contêiner do Docker.

### <a name="package-types"></a>Tipos de pacote

O diretório de montagem de entrada pode conter os modelos de **produção**, de **preparo** e com **controle de versão** do aplicativo simultaneamente. Todos os pacotes são montados.

|Tipo de pacote|API do ponto de extremidade de consulta|Disponibilidade de consulta|Formato do nome de arquivo do pacote|
|--|--|--|--|
|Versão|GET, POST|Somente contêiner|`{APP_ID}_v{APP_VERSION}.gz`|
|Staging|GET, POST|Azure e contêiner|`{APP_ID}_STAGING.gz`|
|Produção|GET, POST|Azure e contêiner|`{APP_ID}_PRODUCTION.gz`|

> [!IMPORTANT]
> Não renomeie, altere, substitua ou descompacte os arquivos de pacote LUIS.

### <a name="packaging-prerequisites"></a>Pré-requisitos de empacotamento

Antes de empacotar um aplicativo LUIS, você deverá ter o seguinte:

|Requisitos de empacotamento|Detalhes|
|--|--|
|Instância de recurso de _Serviços cognitivas_ do Azure|As regiões com suporte incluem<br><br>Oeste dos EUA (`westus`)<br>Europa Ocidental (`westeurope`)<br>Leste da Austrália (`australiaeast`)|
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

### <a name="export-versioned-apps-package-from-luis-portal"></a>Exportar pacote do aplicativo com versão do portal do LUIS

O pacote do aplicativo com versão está disponível na página de lista de **versões** .

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

Para baixar o pacote publicado, consulte a [documentação da API aqui][download-published-package]. Se for baixado com êxito, a resposta será um arquivo de pacote LUIS. Salve o arquivo no local de armazenamento especificado para a montagem de entrada do contêiner.

### <a name="export-versioned-apps-package-from-api"></a>Exportar pacote do aplicativo com versão da API

Use o método de API REST a seguir para empacotar um aplicativo LUIS que você já [treinou](luis-how-to-train.md). Substitua os espaços reservados por seus próprios valores apropriados na chamada à API, usando a tabela abaixo da especificação de HTTP.

```http
GET /luis/api/v2.0/package/{APP_ID}/versions/{APP_VERSION}/gzip HTTP/1.1
Host: {AZURE_REGION}.api.cognitive.microsoft.com
Ocp-Apim-Subscription-Key: {AUTHORING_KEY}
```

| Espaço reservado | Valor |
|-------------|-------|
| **{APP_ID}** | A ID de aplicativo do aplicativo LUIS treinado. |
| **{APP_VERSION}** | A versão do aplicativo do aplicativo LUIS treinado. |
| **{AUTHORING_KEY}** | A chave de criação da conta do LUIS para o aplicativo LUIS publicado.<br/>Você pode obter sua chave de criação na página **Configurações do Usuário** no portal do LUIS. |
| **{AZURE_REGION}** | A região do Azure apropriada:<br/><br/>`westus` – Oeste dos EUA<br/>`westeurope` – Europa Ocidental<br/>`australiaeast` – Leste da Austrália |

Para baixar o pacote com versão, consulte a [documentação da API aqui][download-versioned-package]. Se for baixado com êxito, a resposta será um arquivo de pacote LUIS. Salve o arquivo no local de armazenamento especificado para a montagem de entrada do contêiner.

## <a name="run-the-container-with-docker-run"></a>Executar o contêiner com `docker run`

Use o comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) para executar o contêiner. Consulte [coletando parâmetros necessários](#gathering-required-parameters) para obter detalhes sobre como obter os `{ENDPOINT_URI}` `{API_KEY}` valores e.

[Exemplos](luis-container-configuration.md#example-docker-run-commands) do `docker run` comando estão disponíveis.

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

* Este exemplo usa o diretório fora da `C:` unidade para evitar conflitos de permissão no Windows. Se você precisar usar um diretório específico como o diretório de entrada, talvez seja necessário conceder ao Docker permissão de serviço.
* Não altere a ordem dos argumentos, a menos que você esteja familiarizado com contêineres do Docker.
* Se você estiver usando um sistema operacional diferente, use o console/terminal correto, a sintaxe de pasta para montagens e o caractere de continuação de linha para o seu sistema. Esses exemplos pressupõem um console do Windows com um caractere de continuação de linha `^` . Como o contêiner é um sistema operacional Linux, a montagem de destino usa uma sintaxe de pasta em estilo Linux.

Esse comando:

* Executa um contêiner da imagem de contêiner do LUIS
* Carrega o aplicativo LUIS da montagem de entrada em *C:\input*, localizado no host do contêiner
* Aloca dois núcleos de CPU e 4 GB (gigabytes) de memória
* Expõe a porta TCP 5000 e aloca um pseudo-TTY para o contêiner
* Salva os logs de contêiner e LUIS na montagem de saída em *C:\output*, localizado no host do contêiner
* Remove automaticamente o contêiner depois que ele sai. A imagem de contêiner ainda fica disponível no computador host.

Há outros [exemplos](luis-container-configuration.md#example-docker-run-commands) do comando `docker run` disponíveis.

> [!IMPORTANT]
> As opções `Eula`, `Billing` e `ApiKey` devem ser especificadas para executar o contêiner; caso contrário, o contêiner não será iniciado.  Para mais informações, consulte [Faturamento](#billing).
> O valor ApiKey é a **chave** da página de **recursos do Azure** no portal do Luis e também está disponível na `Cognitive Services` página chaves de recurso do Azure.

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="endpoint-apis-supported-by-the-container"></a>APIs de ponto de extremidade com suporte no contêiner

As versões V2 e [v3](luis-migration-api-v3.md) da API estão disponíveis com o contêiner.

## <a name="query-the-containers-prediction-endpoint"></a>Consultar o ponto de extremidade de previsão do contêiner

O contêiner fornece APIs de ponto de extremidade de previsão de consulta baseadas em REST. Os pontos de extremidade para aplicativos publicados (de preparo ou produção) têm uma rota _diferente_ de pontos de extremidade para aplicativos com controle de versão.

Use o host, `http://localhost:5000`, para as APIs do contêiner.

# <a name="v3-prediction-endpoint"></a>[Ponto de extremidade de previsão V3](#tab/v3)

|Tipo de pacote|Verbo HTTP|Rota|Parâmetros de consulta|
|--|--|--|--|
|Publicado|GET, POST|`/luis/v3.0/apps/{appId}/slots/{slotName}/predict?`|`query={query}`<br>[`&verbose`]<br>[`&log`]<br>[`&show-all-intents`]|
|Versão|GET, POST|`/luis/v3.0/apps/{appId}/versions/{versionId}/predict?`|`query={query}`<br>[`&verbose`]<br>[`&log`]<br>[`&show-all-intents`]|

Os parâmetros de consulta configuram como e o que é retornado na resposta da consulta:

|Parâmetro de consulta|Tipo|Finalidade|
|--|--|--|
|`query`|string|A declaração do usuário.|
|`verbose`|booleano|Um valor booliano que indica se todos os metadados para os modelos previstos devem ser retornados. O padrão é false.|
|`log`|booleano|Registra as consultas, que podem ser usadas posteriormente no [aprendizado ativo](luis-how-to-review-endpoint-utterances.md). O padrão é false.|
|`show-all-intents`|booleano|Um valor booliano que indica se todas as tentativas ou a primeira tentativa de Pontuação devem ser retornadas. O padrão é false.|

# <a name="v2-prediction-endpoint"></a>[Ponto de extremidade de previsão V2](#tab/v2)

|Tipo de pacote|Verbo HTTP|Rota|Parâmetros de consulta|
|--|--|--|--|
|Publicado|[Get](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78), [post](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee79)|`/luis/v2.0/apps/{appId}?`|`q={q}`<br>`&staging`<br>[`&timezoneOffset`]<br>[`&verbose`]<br>[`&log`]<br>|
|Versão|GET, POST|`/luis/v2.0/apps/{appId}/versions/{versionId}?`|`q={q}`<br>[`&timezoneOffset`]<br>[`&verbose`]<br>[`&log`]|

Os parâmetros de consulta configuram como e o que é retornado na resposta da consulta:

|Parâmetro de consulta|Tipo|Finalidade|
|--|--|--|
|`q`|string|A declaração do usuário.|
|`timezoneOffset`|número|O timezoneOffset permite [alterar o fuso horário](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) usado pela datetimeV2 predefinida da entidade.|
|`verbose`|booleano|Retorna todas as intenções e suas pontuações quando definido como true. O padrão é false, o que retorna apenas a intenção principal.|
|`staging`|booleano|Retorna a consulta dos resultados do ambiente de preparo quando definido como true. |
|`log`|booleano|Registra as consultas, que podem ser usadas posteriormente no [aprendizado ativo](luis-how-to-review-endpoint-utterances.md). O padrão é true.|

***

### <a name="query-the-luis-app"></a>Consultar o aplicativo LUIS

Um exemplo de comando CURL para consultar o contêiner em relação a um aplicativo publicado é:

# <a name="v3-prediction-endpoint"></a>[Ponto de extremidade de previsão V3](#tab/v3)

Para consultar um modelo em um slot, use a seguinte API:

```bash
curl -G \
-d verbose=false \
-d log=true \
--data-urlencode "query=turn the lights on" \
"http://localhost:5000/luis/v3.0/apps/{APP_ID}/slots/production/predict"
```

Para fazer consultas ao ambiente de **preparo** , substitua `production` na rota por `staging` :

`http://localhost:5000/luis/v3.0/apps/{APP_ID}/slots/staging/predict`

Para consultar um modelo com controle de versão, use a seguinte API:

```bash
curl -G \
-d verbose=false \
-d log=false \
--data-urlencode "query=turn the lights on" \
"http://localhost:5000/luis/v3.0/apps/{APP_ID}/versions/{APP_VERSION}/predict"
```

# <a name="v2-prediction-endpoint"></a>[Ponto de extremidade de previsão V2](#tab/v2)

Para consultar um modelo em um slot, use a seguinte API:

```bash
curl -X GET \
"http://localhost:5000/luis/v2.0/apps/{APP_ID}?q=turn%20on%20the%20lights&staging=false&timezoneOffset=0&verbose=false&log=true" \
-H "accept: application/json"
```
Para fazer consultas no ambiente de **Preparo**, altere o valor de parâmetro da cadeia de caracteres de consulta **staging** para true:

`staging=true`

Para consultar um modelo com controle de versão, use a seguinte API:

```bash
curl -X GET \
"http://localhost:5000/luis/v2.0/apps/{APP_ID}/versions/{APP_VERSION}?q=turn%20on%20the%20lights&timezoneOffset=0&verbose=false&log=true" \
-H "accept: application/json"
```
O nome da versão tem no máximo 10 caracteres e contém apenas caracteres permitidos em uma URL.

***

## <a name="import-the-endpoint-logs-for-active-learning"></a>Importar os logs de ponto de extremidade para aprendizado ativo

Se uma montagem de saída for especificada para o contêiner LUIS, os arquivos de log de consulta de aplicativo serão salvos no diretório de saída, em que `{INSTANCE_ID}` é a ID do contêiner. O log de consulta do aplicativo contém a consulta, a resposta e os carimbos de data/hora para cada consulta de previsão enviada para o contêiner de LUIS.

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

O contêiner LUIS envia informações de cobrança para o Azure, usando um recurso de _Serviços cognitivas_ em sua conta do Azure.

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

* Examine [configurar contêineres](luis-container-configuration.md) para definições de configuração.
* Consulte [limitações de contêiner Luis](luis-container-limitations.md) para obter restrições de funcionalidade conhecidas.
* Consulte a [Solução de problemas](troubleshooting.md) para resolver problemas relacionados à funcionalidade LUIS.
* Use mais [Contêineres de Serviços Cognitivos](../cognitive-services-container-support.md)

<!-- Links - external -->
[download-published-package]: https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagepublishedapplicationasgzip
[download-versioned-package]: https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagetrainedapplicationasgzip

[unsupported-dependencies]: luis-container-limitations.md#unsupported-dependencies-for-latest-container