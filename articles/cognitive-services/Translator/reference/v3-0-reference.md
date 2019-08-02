---
title: Referência da API de texto do tradutor V3.0
titleSuffix: Azure Cognitive Services
description: Documentação de referência para a API de texto do tradutor V3.0.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/29/2018
ms.author: swmachan
ms.openlocfilehash: ad619ad965cf4b7d94b781818c658152f71250a7
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595012"
---
# <a name="translator-text-api-v30"></a>API de Tradução de Texto v3.0

## <a name="whats-new"></a>Novidades?

A versão 3 do API de tradução de texto fornece uma API de Web modernos baseados em JSON. Ela melhora o uso e o desempenho por meio da consolidação dos recursos existentes em menos operações, além de fornecer novos recursos.

 * Transliteração para converter texto em um idioma de um script para outro script.
 * Tradução para vários idiomas em uma solicitação.
 * Detecção de idioma, tradução e transliteração em uma solicitação.
 * Dicionário para pesquisar traduções alternativas de um termo, para localizar traduções reversas e exemplos mostrando os termos usados no contexto.
 * Resultados de detecção de idioma mais informativos.

## <a name="base-urls"></a>URLs base

O Microsoft Translator é distribuído a partir de vários locais de datacenter. Atualmente, eles estão localizados em 10 [geografias do Azure](https://azure.microsoft.com/global-infrastructure/regions):

* **Américas:** Leste dos EUA, Sul EUA Central, Oeste EUA Central e oeste dos EUA 2 
* **Pacífico Asiático:** Sul da Coreia, leste do Japão, Sudeste Asiático e leste da Austrália
* **Europa:** Europa Setentrional e Europa Ocidental

As solicitações para a API de Tradução de Texto da Microsoft são na maioria dos casos, tratadas pelo datacenter mais próximo que originou a solicitação. No caso de uma falha de datacenter, a solicitação pode ser roteada para fora da geografia do Azure.

Para forçar a manipulação da solicitação por uma geografia do Azure específica, altere o ponto de extremidade global na solicitação de API para o ponto de extremidade regional desejado:

|Descrição|Geografia do Azure|URL Base|
|:--|:--|:--|
|Azure|Global (não regional)|   api.cognitive.microsofttranslator.com|
|Azure|Estados Unidos|   api-nam.cognitive.microsofttranslator.com|
|Azure|Europa|  api-eur.cognitive.microsofttranslator.com|
|Azure|Pacífico Asiático|    api-apc.cognitive.microsofttranslator.com|


## <a name="authentication"></a>Autenticação

Assine API de Tradução de Texto ou [Serviços cognitivas multiatendimento](https://azure.microsoft.com/pricing/details/cognitive-services/) nos serviços cognitivas da Microsoft e use sua chave de assinatura (disponível no portal do Azure) para autenticar. 

Há três cabeçalhos que você pode usar para autenticar sua assinatura. Esta tabela descreve como cada um é usado:

|Cabeçalhos|Descrição|
|:----|:----|
|Ocp-Apim-Subscription-Key|*Use com a assinatura dos Serviços Cognitivos se você estiver passando a chave secreta*.<br/>O valor é a chave secreta do Azure da sua assinatura para a API de Tradução de Texto.|
|Autorização|*Use com a assinatura dos Serviços Cognitivos se você estiver passando um token de autenticação.*<br/>O valor é o token de portador: `Bearer <token>`.|
|Ocp-Apim-Subscription-Region|*Use com a assinatura de vários serviços cognitivas se você estiver passando uma chave secreta de vários serviços.*<br/>O valor é a região da assinatura de vários serviços. Esse valor é opcional quando não estiver usando uma assinatura de vários serviços.|

###  <a name="secret-key"></a>Chave secreta
A primeira opção é autenticar usando o cabeçalho `Ocp-Apim-Subscription-Key`. Basta adicionar o cabeçalho `Ocp-Apim-Subscription-Key: <YOUR_SECRET_KEY>` à sua solicitação.

### <a name="authorization-token"></a>Token de autorização
Como alternativa, você pode trocar sua chave secreta por um token de acesso. Esse token é incluído em cada solicitação como o cabeçalho `Authorization`. Para obter um token de autorização, faça uma solicitação `POST` à URL a seguir:

| Ambiente     | URL do serviço de autenticação                                |
|-----------------|-----------------------------------------------------------|
| Azure           | `https://api.cognitive.microsoft.com/sts/v1.0/issueToken` |

Estas são as solicitações de exemplo para obter um token de acordo com uma chave secreta:

```
// Pass secret key using header
curl --header 'Ocp-Apim-Subscription-Key: <your-key>' --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken'

// Pass secret key using query string parameter
curl --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken?Subscription-Key=<your-key>'
```

Uma solicitação bem-sucedida retorna o token de acesso codificado como texto sem formatação no corpo da resposta. O token válido é passado para o serviço de Tradução como um token de portador na Autorização.

```
Authorization: Bearer <Base64-access_token>
```

Um token de autenticação é válido por 10 minutos. O token deve ser usado novamente ao fazer várias chamadas para as APIs de Tradução. No entanto, se o programa faz solicitações para a API de Tradução por um longo período, seu programa deverá solicitar um novo token de acesso em intervalos regulares (por exemplo, a cada oito minutos).

### <a name="multi-service-subscription"></a>Assinatura de vários serviços

A última opção de autenticação é usar a assinatura de vários serviços de um serviço cognitiva. Isso permite que você use uma única chave secreta para autenticar solicitações para vários serviços. 

Ao usar uma chave secreta de vários serviços, você deve incluir dois cabeçalhos de autenticação com sua solicitação. O primeiro passa a chave secreta, o segundo especifica a região associada à sua assinatura. 
* `Ocp-Apim-Subscription-Key`
* `Ocp-Apim-Subscription-Region`

A região é necessária para a assinatura de API de texto de vários serviços. A região selecionada é a única região que você pode usar para a tradução de texto ao usar a chave de assinatura de vários serviços e deve ser a mesma região que você selecionou quando se inscreveu para sua assinatura de vários serviços por meio do portal do Azure.

As regiões disponíveis são `australiaeast`, `brazilsouth`, `canadacentral`, `centralindia`, `centraluseuap`, `eastasia`, `eastus`, `eastus2`, `japaneast`, `northeurope`, `southcentralus`, `southeastasia`, `uksouth`, `westcentralus`, `westeurope`, `westus` e `westus2`.

Se você passar a chave secreta na cadeia de caracteres de consulta com o parâmetro `Subscription-Key`, então você deverá especificar a região com o parâmetro de consulta `Subscription-Region`.

Se você usar um token de portador, você deve obter o token do ponto de extremidade de região: `https://<your-region>.api.cognitive.microsoft.com/sts/v1.0/issueToken`.


## <a name="errors"></a>Erros

Uma resposta de erro padrão é um objeto JSON com o par de nome/valor chamado `error`. O valor também é um objeto JSON com propriedades:

  * `code`: Um código de erro definido pelo servidor.

  * `message`: Uma cadeia de caracteres que fornece uma representação legível do erro.

Por exemplo, um cliente com uma assinatura de avaliação gratuita receberia o seguinte erro após a cota livre esgotar:

```
{
  "error": {
    "code":403001,
    "message":"The operation is not allowed because the subscription has exceeded its free quota."
    }
}
```
O código de erro é um número de 6 dígitos que combina o código de status HTTP de 3 dígitos seguido por um número de 3 dígitos para categorizar ainda mais o erro. Códigos de erro comuns são:

| Código | Descrição |
|:----|:-----|
| 400000| Uma das entradas de solicitação não é válida.|
| 400001| O parâmetro "scope" é inválido.|
| 400002| O parâmetro "category" é inválido.|
| 400003| Um especificador de linguagem está ausente ou inválido.|
| 400004| Um especificador de script de destino ("To script") está ausente ou é inválido.|
| 400005| Um texto de entrada está faltando ou é inválido.|
| 400006| A combinação de idioma e script não é válida.|
| 400018| Um especificador de script de origem ("From script") está ausente ou é inválido.|
| 400019| Um dos idiomas especificados não é suportado.|
| 400020| Um dos elementos na matriz de texto de entrada não é válido.|
| 400021| O parâmetro da versão da API está ausente ou é inválido.|
| 400023| Um dos pares de idiomas especificados não é válido.|
| 400035| O idioma de origem (campo "De") não é válido.|
| 400036| O idioma de destino (campo "Para") está ausente ou é inválido.|
| 400042| Uma das opções especificadas (campo "Opções") não é válida.|
| 400043| O ID de rastreio do cliente (campo ClientTraceId ou cabeçalho X-ClientTranceId) está ausente ou é inválido.|
| 400050| O texto de entrada é muito longo. Exibir [limites de solicitação](../request-limits.md).|
| 400064| O parâmetro "translation" está ausente ou é inválido.|
| 400070| O número de scripts de destino (parâmetro ToScript) não corresponde ao número de idiomas de destino (para o parâmetro To).|
| 400071| O valor não é válido para o TextType.|
| 400072| A matriz de texto de entrada possui muitos elementos.|
| 400073| O parâmetro de script não é válido.|
| 400074| O corpo da solicitação não é válido como JSON.|
| 400075| A combinação de pares de idiomas e categorias não é válida.|
| 400077| O tamanho máximo da solicitação foi excedido. Exibir [limites de solicitação](../request-limits.md).|
| 400079| O sistema personalizado solicitado para tradução entre e para o idioma não existe.|
| 400080| A transliteração não tem suporte para o idioma ou o script.|
| 401000| A solicitação não está autorizada porque as credenciais estão ausentes ou são inválidas.|
| 401015| "As credenciais fornecidas são para a Speech API. Esta solicitação requer credenciais para a API de texto. Por favor, use uma assinatura para o Translator Text API ".|
| 403000| A operação não é permitida.|
| 403001| A operação não é permitida porque a assinatura excedeu sua cota gratuita.|
| 405000| O método de solicitação não é suportado para o recurso solicitado.|
| 408001| O sistema de tradução solicitado está sendo preparado. Tente novamente em alguns minutos.|
| 408002| A solicitação atingiu o tempo limite aguardando o fluxo de entrada. O cliente não produziu uma solicitação no momento em que o servidor foi preparado para aguardar. O cliente pode repetir a solicitação sem modificações em nenhum momento posterior.|
| 415000| O cabeçalho Content-Type está ausente ou é inválido.|
| 429000, 429001, 429002| O servidor rejeitou a solicitação porque o cliente excedeu os limites de solicitação.|
| 500000| Ocorreu um erro inesperado. Se o erro persistir, informe-o com data / hora do erro, solicite o identificador do cabeçalho de resposta X-RequestId e o identificador de cliente do cabeçalho de solicitação X-ClientTraceId.|
| 503000| O serviço está temporariamente indisponível. Tente novamente. Se o erro persistir, informe-o com data / hora do erro, solicite o identificador do cabeçalho de resposta X-RequestId e o identificador de cliente do cabeçalho de solicitação X-ClientTraceId.|

