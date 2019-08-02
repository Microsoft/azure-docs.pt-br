---
title: 'Início Rápido: Gerar uma miniatura – REST, PHP'
titleSuffix: Azure Cognitive Services
description: Neste início rápido, você gerará uma miniatura de uma imagem usando a API da Pesquisa Visual Computacional com PHP.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 69a7438f2daf1eb16efff7f0b4f503064afac29d
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603593"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-rest-api-and-php"></a>Início Rápido: Gerar uma miniatura usando a API REST da Pesquisa Visual Computacional e o PHP

Neste início rápido, você gerará uma miniatura de uma imagem usando a API REST da Pesquisa Visual Computacional. Com o método [Get Thumbnail](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb), é possível gerar uma miniatura de uma imagem. Você especifica a altura e largura, que podem ser diferentes da proporção da imagem de entrada. A Pesquisa Visual Computacional usa o corte inteligente para identificar a área de interesse de modo inteligência e gerar as coordenadas de corte com base nessa região.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

- É necessário ter o [PHP](https://secure.php.net/downloads.php) instalado.
- É necessário ter o [Pear](https://pear.php.net) instalado.
- Você precisa ter uma chave de assinatura para a Pesquisa Visual Computacional. É possível obter uma chave de avaliação gratuita em [Experimente os Serviços Cognitivos](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Ou siga as instruções em [Criar uma conta dos Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para assinar a Pesquisa Visual Computacional e obter sua chave.

## <a name="create-and-run-the-sample"></a>Criar e executar o exemplo

Para criar e executar o exemplo, siga estas etapas:

1. Instale o pacote [`HTTP_Request2`](https://pear.php.net/package/HTTP_Request2) PHP5.
   1. Abra uma janela de prompt de comando como administrador.
   1. Execute o comando a seguir:

      ```console
      pear install HTTP_Request2
      ```

   1. Depois que o pacote for instalado com êxito, feche a janela do prompt de comando.

1. Copie o código a seguir em um editor de texto.
1. Faça as alterações a seguir no código quando necessário:
    1. Substitua o valor de `subscriptionKey` pela sua chave de assinatura.
    1. Substitua o valor de `uriBase` pela URL do ponto de extremidade para o método [Get Thumbnail](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) da região do Azure em que você adquiriu suas chaves de assinatura, se necessário.
    1. Outra opção é substituir o valor de `imageUrl` pela URL de uma imagem diferente para a qual você deseja gerar uma miniatura.
1. Salve o código como um arquivo com uma extensão `.php`. Por exemplo, `get-thumbnail.php`.
1. Abra uma janela do navegador com suporte para PHP.
1. Arraste e solte o arquivo na janela do navegador.

```php
<html>
<head>
    <title>Get Thumbnail Sample</title>
</head>
<body>
<?php
// Replace <Subscription Key> with a valid subscription key.
$ocpApimSubscriptionKey = '<Subscription Key>';

// You must use the same location in your REST call as you used to obtain
// your subscription keys. For example, if you obtained your subscription keys
// from westus, replace "westcentralus" in the URL below with "westus".
$uriBase = 'https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/';

$imageUrl =
    'https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg';

require_once 'HTTP/Request2.php';

$request = new Http_Request2($uriBase . 'generateThumbnail');
$url = $request->getUrl();

$headers = array(
    // Request headers
    'Content-Type' => 'application/json',
    'Ocp-Apim-Subscription-Key' => $ocpApimSubscriptionKey
);
$request->setHeader($headers);

$parameters = array(
    // Request parameters
    'width' => '100',  // Width of the thumbnail.
    'height' => '100', // Height of the thumbnail.
    'smartCropping' => 'true',
);
$url->setQueryVariables($parameters);

$request->setMethod(HTTP_Request2::METHOD_POST);

// Request body parameters
$body = json_encode(array('url' => $imageUrl));

// Request body
$request->setBody($body);

try
{
    $response = $request->send();
    echo "<pre>" .
        json_encode(json_decode($response->getBody()), JSON_PRETTY_PRINT) . "</pre>";
}
catch (HttpException $ex)
{
    echo "<pre>" . $ex . "</pre>";
}
?>
</body>
</html>
```

## <a name="examine-the-response"></a>Examinar a resposta

Uma resposta bem-sucedida é retornada como dados binários, que representam os dados da imagem da miniatura. Se a solicitação falhar, a resposta será exibida na janela do navegador. A resposta à solicitação com falha contém um código de erro e uma mensagem para ajudar a determinar o que deu errado.

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, exclua o arquivo e, em seguida, desinstale o pacote `HTTP_Request2` PHP5. Para desinstalar o pacote, siga estas etapas:

1. Abra uma janela de prompt de comando como administrador.
2. Execute o comando a seguir:

   ```console
   pear uninstall HTTP_Request2
   ```

3. Depois que o pacote for desinstalado com êxito, feche a janela do prompt de comando.

## <a name="next-steps"></a>Próximas etapas

Explore a API da Pesquisa Visual Computacional para analisar uma imagem, detectar celebridades e marcos, criar uma miniatura e extrair textos manuscritos e impressos. Para testar rapidamente a API da Pesquisa Visual Computacional, experimente o [Abrir o console de teste de API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Explorar a API da Pesquisa Visual Computacional](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
