---
title: Tutorial – Assinar e fazer solicitações para a API SMS do ACS com o Postman
titleSuffix: An Azure Communication Services tutorial
description: Saiba como assinar e fazer solicitações para o ACS com o Postman para enviar uma mensagem SMS.
author: ProbablePrime
services: azure-communication-services
ms.author: rifox
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 5805734a9253962d672a4236a5650e9de8b37f0a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105044287"
---
# <a name="tutorial-sign-and-make-requests-with-postman"></a>Tutorial: Assinar e fazer solicitações com o Postman
Neste tutorial, vamos configurar e usar o Postman para fazer uma solicitação para o ACS (Serviços de Comunicação do Azure) usando HTTP. Ao final deste tutorial, você terá enviado com êxito uma mensagem SMS usando o ACS e o Postman e poderá usar o Postman para explorar outras APIs no ACS.

Neste tutorial, nós vamos:
> [!div class="checklist"]
> * Baixar o Postman
> * Configurar o Postman para assinar solicitações HTTP
> * Fazer uma solicitação para a API do SMS do ACS para enviar uma mensagem.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. Para obter detalhes, confira [Criar uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). A conta gratuita oferece US$ 200 em créditos Azure para você experimentar qualquer combinação de serviços.
- Um recurso e uma cadeia de conexão ativos dos Serviços de Comunicação. [Saiba como criar um recurso dos Serviços de Comunicação](../quickstarts/create-communication-resource.md).
- Um número de Telefone do ACS que possa enviar mensagens SMS; confira [Obter um número de telefone](../quickstarts/telephony-sms/get-phone-number.md) para obter um.

## <a name="downloading-and-installing-postman"></a>Baixando e instalando o Postman

O Postman é um aplicativo de área de trabalho que é capaz de fazer solicitações de API para qualquer API HTTP. Ele costuma ser usado para testar e explorar APIs. Baixaremos a [Última versão de área de trabalho do site do Postman](https://www.postman.com/downloads/). O Postman tem versões para Windows, Mac e Linux, sendo assim, baixe a versão apropriada para seu sistema operacional. Depois de baixar, abra o aplicativo. Você verá uma tela inicial solicitando que entre ou crie uma conta do Postman. A criação da conta é opcional e pode ser ignorada clicando no link "Ignorar e ir para o aplicativo". A criação de uma conta salvará suas configurações de solicitação à API no Postman, o que pode permitir que você tenha acesso às solicitações em outros computadores.

:::image type="content" source="media/postman/create-account-or-skip.png" alt-text="Tela inicial do Postman mostrando a capacidade de criar uma conta ou ignorar e ir para o aplicativo.":::

Depois de criar uma conta ou ignorar a criação de uma, você deverá ver a janela principal do Postman.

## <a name="creating-and-configuring-a-postman-collection"></a>Criando e configurando uma coleção de Postman

O Postman pode organizar solicitações de várias maneiras. Para os fins deste tutorial. Criaremos uma Coleção do Postman. Para fazer isso, selecione o botão de coleções no lado esquerdo do aplicativo:

:::image type="content" source="media/postman/collections-tab.png" alt-text="Tela principal do Postman com a guia Coleções realçada.":::

Depois de selecionar, clique em "Criar Coleção" para iniciar o processo de criação da coleção. Uma nova guia será aberta na área central do Postman. Dê à coleção o nome que desejar. Aqui, ela é denominada "ACS":

:::image type="content" source="media/postman/acs-collection.png" alt-text="Postman com uma Coleção do ACS aberta e o nome da coleção realçada.":::

Após sua coleção ser criada e nomeada, você estará pronto para configurá-la.

### <a name="adding-collection-variables"></a>Adicionando variáveis de coleção

Para lidar com a autenticação e facilitar as solicitações, vamos especificar duas variáveis de coleção dentro da coleção do ACS recém-criada. Essas variáveis estão disponíveis para todas as solicitações em sua coleção do ACS. Para começar a criar variáveis, visite a guia Variável da Coleção.

:::image type="content" source="media/postman/variable-stab.png" alt-text="Postman com a guia Variáveis de uma Coleção do ACS.":::

Na guia da coleção, crie duas variáveis:
- chave – essa variável deve ser uma de suas chaves da página de chaves dos Serviços de Comunicação do Azure dentro do portal do Azure. Por exemplo, `oW...A==`.
- ponto de extremidade – essa variável deve ser seu ponto de extremidade dos Serviços de Comunicação do Azure na página de chave. **Remova a barra à direita**. Por exemplo, `https://contoso.communication.azure.com`.

Insira esses valores na coluna "Valor Inicial" da tela de variáveis. Depois de inserir, pressione o botão "Persistir Tudo" logo acima da tabela à direita. Quando configurada corretamente, a tela do Postman deve ser semelhante a esta:

:::image type="content" source="media/postman/acs-variables-set.png" alt-text="Postman com as variáveis da Coleção do ACS configuradas corretamente.":::

Saiba mais sobre variáveis lendo a [Documentação do Postman sobre elas](https://learning.postman.com/docs/sending-requests/variables).

### <a name="creating-a-pre-request-script"></a>Criando um script pré-solicitação

A próxima etapa é criar um Script pré-solicitação no Postman. Um script pré-solicitação é um script que é executado antes de cada solicitação no Postman e pode modificar ou alterar parâmetros de solicitação em seu nome. Nós o usaremos para assinar nossas solicitações HTTP para que elas possam ser autorizadas pelos Serviços do ACS. Para saber mais sobre os requisitos de Assinatura, você pode [ler nosso guia sobre autenticação](/rest/api/communication/authentication).

Vamos criar esse script dentro da Coleção de modo que ele seja executado em qualquer solicitação dentro dela. Para fazer isso, na guia da coleção, clique na subguia "Script Pré-solicitação".

:::image type="content" source="media/postman/start-pre-request-script.png" alt-text="Postman com a subguia Script Pré-solicitação de uma Coleção do ACS selecionada.":::

Nessa subguia, você pode criar um script pré-solicitação inserindo-o na área de texto abaixo. Pode ser mais fácil escrevê-lo em um editor de código completo, como o [Visual Studio Code](https://code.visualstudio.com/). antes de colá-lo quando concluído. Percorreremos cada parte do script neste tutorial. Fique à vontade para pular para o final se você quiser apenas copiá-lo no Postman e começar. Vamos começar a escrever o script.

### <a name="writing-the-pre-request-script"></a>Escrevendo script pré-solicitação

A primeira coisa que faremos é criar uma cadeia de caracteres UTC (Tempo Universal Coordenado) e adicioná-la ao cabeçalho HTTP "Date". Também armazenamos essa cadeia de caracteres em uma variável para usá-la mais tarde ao assinar:

```JavaScript
// Set the Date header to our Date as a UTC String.
const dateStr = new Date().toUTCString();
pm.request.headers.upsert({key:'Date', value: dateStr});
```

Em seguida, vamos fazer o hash do corpo da solicitação usando SHA 256 e colocá-lo no cabeçalho `x-ms-content-sha256`. O Postman inclui algumas [bibliotecas padrão](https://learning.postman.com/docs/writing-scripts/script-references/postman-sandbox-api-reference/#using-external-libraries) para hash e assinatura globalmente, de modo que não precisamos as instalar nem as exigir:

```JavaScript
// Hash the request body using SHA256 and encode it as Base64
const hashedBodyStr = CryptoJS.SHA256(pm.request.body.raw).toString(CryptoJS.enc.Base64)
// And add that to the header x-ms-content-sha256
pm.request.headers.upsert({
    key:'x-ms-content-sha256',
    value: hashedBodyStr
});
```

Agora, usaremos a variável de ponto de extremidade especificada anteriormente para diferenciar o valor do cabeçalho de host HTTP. Precisamos fazer isso, pois o cabeçalho de host não é definido até que esse script seja processado:

```JavaScript
// Get our previously specified endpoint variable
const endpoint = pm.variables.get('endpoint')
// Remove the https, prefix to create a suitable "Host" value
const hostStr = endpoint.replace('https://','');
```

Com essas informações criadas, agora podemos criar a cadeia de caracteres, que assinaremos para a solicitação HTTP e é composta por vários valores criados anteriormente:

```JavaScript
// This gets the part of our URL that is after the endpoint, for example in https://contoso.communication.azure.com/sms, it will get '/sms'
const url = pm.request.url.toString().replace('{{endpoint}}','');

// Construct our string which we'll sign, using various previously created values.
const stringToSign = pm.request.method + '\n' + url + '\n' + dateStr + ';' + hostStr + ';' + hashedBodyStr;
```

Por fim, precisamos assinar a cadeia de caracteres usando nossa chave do ACS e adicioná-la à nossa solicitação no cabeçalho `Authorization`:

```JavaScript
// Decode our access key from previously created variables, into bytes from base64.
const key = CryptoJS.enc.Base64.parse(pm.variables.get('key'));
// Sign our previously calculated string with HMAC 256 and our key. Convert it to Base64.
const signature = CryptoJS.HmacSHA256(stringToSign, key).toString(CryptoJS.enc.Base64);

// Add our final signature in Base64 to the authorization header of the request.
pm.request.headers.upsert({
    key:'Authorization',
    value: "HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature=" + signature
});
```

### <a name="the-final-pre-request-script"></a>O script pré-solicitação final

O script pré-solicitação final deve ser semelhante ao seguinte:

```JavaScript
// Set the Date header to our Date as a UTC String.
const dateStr = new Date().toUTCString();
pm.request.headers.upsert({key:'Date', value: dateStr});

// Hash the request body using SHA256 and encode it as Base64
const hashedBodyStr = CryptoJS.SHA256(pm.request.body.raw).toString(CryptoJS.enc.Base64)
// And add that to the header x-ms-content-sha256
pm.request.headers.upsert({
    key:'x-ms-content-sha256',
    value: hashedBodyStr
});

// Get our previously specified endpoint variable
const endpoint = pm.variables.get('endpoint')
// Remove the https, prefix to create a suitable "Host" value
const hostStr = endpoint.replace('https://','');

// This gets the part of our URL that is after the endpoint, for example in https://contoso.communication.azure.com/sms, it will get '/sms'
const url = pm.request.url.toString().replace('{{endpoint}}','');

// Construct our string which we'll sign, using various previously created values.
const stringToSign = pm.request.method + '\n' + url + '\n' + dateStr + ';' + hostStr + ';' + hashedBodyStr;

// Decode our access key from previously created variables, into bytes from base64.
const key = CryptoJS.enc.Base64.parse(pm.variables.get('key'));
// Sign our previously calculated string with HMAC 256 and our key. Convert it to Base64.
const signature = CryptoJS.HmacSHA256(stringToSign, key).toString(CryptoJS.enc.Base64);

// Add our final signature in Base64 to the authorization header of the request.
pm.request.headers.upsert({
    key:'Authorization',
    value: "HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature=" + signature
});
```

Insira ou cole este script final na área de texto dentro da guia Script Pré-solicitação:

:::image type="content" source="media/postman/finish-pre-request.png" alt-text="Postman com a subguia Script Pré-solicitação de uma Coleção do ACS inserida.":::

Depois de inserir, pressione CTRL + S ou pressione o botão Salvar para salvar o script na coleção. 

:::image type="content" source="media/postman/save-pre-request-script.png" alt-text="Botão Salvar Script Pré-solicitação do Postman.":::

## <a name="creating-a-request-in-postman"></a>Criando uma solicitação no Postman

Agora que tudo está configurado, estamos prontos para criar uma solicitação do ACS no Postman. Para começar, clique no ícone de adição (+) ao lado da Coleção do ACS:

:::image type="content" source="media/postman/create-request.png" alt-text="Botão de adição do Postman.":::

Isso criará uma guia para nossa solicitação no Postman. Com ela criada, precisamos configurá-la. Faremos uma solicitação para a API de Envio de SMS, portanto, confira a [documentação dessa API para obter assistência](/rest/api/communication/sms/send). Vamos configurar a solicitação do Postman.

Comece definindo o tipo de solicitação para `POST` e inserindo `{{endpoint}}/sms?api-version=2021-03-07` no campo de URL da solicitação. Essa URL usa nossa variável `endpoint` criada anteriormente para enviá-la automaticamente para o Recurso do ACS.

:::image type="content" source="media/postman/post-request-and-url.png" alt-text="Uma solicitação do Postman, com o tipo definido como POST e a URL definida corretamente.":::

Agora, selecione a guia Corpo da solicitação e altere o botão de opção abaixo para "bruto". À direita, há um menu suspenso que diz "Texto", altere-o para JSON:

:::image type="content" source="media/postman/postman-json.png" alt-text="Definindo o corpo da solicitação como bruto e JSON":::

Isso vai configurar a solicitação para enviar e receber informações em um formato JSON.

Na área de texto abaixo, você precisará inserir um corpo da solicitação, que deve estar no seguinte formato:

```JSON
{
    "from":"<Your ACS Telephone Number>",
    "message":"<The message you'd like to send>",
    "smsRecipients": [
        {
            "to":"<The number you'd like to send the message to>"
        }
    ]
}
```

Para o valor "de", você precisará [obter um número de telefone](../quickstarts/telephony-sms/get-phone-number.md) no Portal do ACS, conforme mencionado anteriormente. Insira-o sem espaços e prefixado pelo código do país. Por exemplo: `+15555551234`. Sua "mensagem" pode ser qualquer coisa que você queira enviar, mas `Hello from ACS` é um bom exemplo. O valor de "para" deve ser um telefone ao qual você tenha acesso e que possa receber mensagens SMS. Usar o próprio celular é uma boa ideia.

Depois de inserir, precisamos salvar essa solicitação na Coleção do ACS criada anteriormente. Isso garantirá que ela escolha as variáveis e o script pré-solicitação criados anteriormente. Para fazer isso, clique no botão "salvar" no canto superior direito da área de solicitação.

:::image type="content" source="media/postman/postman-save.png" alt-text="O botão salvar de uma solicitação do Postman.":::

Isso fará uma janela de caixa de diálogo ser exibida, perguntando como você deseja chamar a solicitação e onde deseja salvá-la. Você pode dar a ela o nome que quiser, mas selecione sua coleção do ACS na metade inferior da caixa de diálogo:

:::image type="content" source="media/postman/postman-save-to-acs.png" alt-text="A caixa de diálogo salvar solicitação do Postman com a coleção do ACS selecionada.":::

## <a name="sending-a-request"></a>Enviando uma solicitação

Agora que tudo está configurado, você deve ser capaz de enviar a solicitação e obter uma mensagem SMS em seu telefone. Para fazer isso, verifique se a solicitação criada está selecionada e pressione o botão "Enviar" à direita:

:::image type="content" source="media/postman/postman-send.png" alt-text="Uma solicitação do Postman com o botão Enviar realçado.":::

Se tudo tiver dado certo, agora você verá a resposta do ACS, que deve ser o código de status 202:

:::image type="content" source="media/postman/postman-202.png" alt-text="Uma solicitação do Postman enviada com êxito com um código de status 202.":::

O celular, ao qual pertence ao número que você forneceu no valor "para", também deve ter recebido uma mensagem SMS. Agora, você tem um Postman funcional configurado, que pode se comunicar com os Serviços do ACS e enviar mensagens SMS.


## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Explorar APIs do ACS](/rest/api/communication/)
> [Leia mais sobre Autenticação](/rest/api/communication/authentication)
> [Saiba mais sobre o Postman](https://learning.postman.com/)

Talvez seja necessário também:

- [Adicionar o chat ao seu aplicativo](../quickstarts/chat/get-started.md)
- [Criar tokens de acesso para o usuário](../quickstarts/access-tokens.md)
- [Saber mais sobre a arquitetura cliente e servidor](../concepts/client-and-server-architecture.md)
- [Aprender sobre autenticação](../concepts/authentication.md)