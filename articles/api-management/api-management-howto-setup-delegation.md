---
title: "Como delegar o registro de usuário e a assinatura do produto"
description: "Saiba como delegar a assinatura de produto e registro de usuário a um terceiro no Gerenciamento de API do Azure."
services: api-management
documentationcenter: 
author: antonba
manager: erikre
editor: 
ms.assetid: 8b7ad5ee-a873-4966-a400-7e508bbbe158
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 2637ab6405f2d4ea1da84981295a144874dfa4f6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-delegate-user-registration-and-product-subscription"></a>Como delegar o registro de usuário e a assinatura do produto
A delegação permite usar seu site existente para gerenciar a entrada/inscrição e assinatura de produtos feitas por desenvolvedores em vez de usar a funcionalidade integrada no portal do desenvolvedor. Isso permite que seu site tenha os dados dos usuários e realize a validação dessas etapas de forma personalizada.

## <a name="delegate-signin-up"> </a>Delegando a entrada e inscrição de desenvolvedores
Para delegar a entrada e a assinatura do desenvolvedor em seu site existente, você precisará criar um ponto de extremidade de delegação especial em seu site que atue como ponto de entrada para qualquer solicitação desse tipo por meio do portal do desenvolvedor do Gerenciamento de API.

O fluxo de trabalho final será o seguinte:

1. O desenvolvedor clica no link de assinatura ou entrada no portal do desenvolvedor do Gerenciamento de API
2. O navegador é redirecionado ao ponto de extremidade de delegação
3. O ponto de extremidade de delegação, por sua vez, redireciona ou apresenta a IU solicitando o usuário a entrar ou se inscrever
4. Em caso de êxito, o usuário é redirecionado de volta para o portal do desenvolvedor do Gerenciamento de API onde começou

Para começar, vamos configurar o Gerenciamento de API para encaminhar as solicitações por meio do seu ponto de extremidade de delegação. No portal do editor do Gerenciamento de API, clique em **Segurança** e na guia **Delegação**. Clique na caixa de seleção para habilitar "Delegar entrada e inscrição".

![Página de delegação][api-management-delegation-signin-up]

* Decida qual será o URL do seu ponto de extremidade de delegação especial e insira-o no campo **URL do ponto de extremidade de delegação** . 
* No campo **Chave de autenticação de delegação** , insira um segredo que será usado para calcular uma assinatura fornecida a você para verificação, para garantir que a solicitação realmente venha do Gerenciamento de API do Azure. Você pode clicar no botão **Gerar** para o Gerenciamento de API gerar aleatoriamente uma chave para você.

Agora, você precisa criar o **ponto de extremidade de delegação**. Ele precisa realizar uma série de ações:

1. Receba uma solicitação com a seguinte forma:
   
   > *http://www.yourwebsite.com/apimdelegation?operation=SignIn&returnUrl={URL da página de origem}&salt={string}&sig={string}*
   > 
   > 
   
    Parâmetros de consulta para a entrada/inscrição:
   
   * **operation**: identifica o tipo de solicitação de delegação – neste caso, pode ser somente **SignIn**
   * **returnUrl**: a URL da página em que o usuário clicou em um link de entrada ou de inscrição
   * **salt**: uma cadeia de caracteres de salt especial usada para calcular um hash de segurança
   * **sig**: um hash de segurança calculado para ser usado para comparação com seu próprio hash calculado
2. Confirme que a solicitação está vindo do Gerenciamento de API do Azure (opcional, mas altamente recomendado por segurança)
   
   * Calcule um hash HMAC-SHA512 de uma cadeia de caracteres baseada nos parâmetros de consulta **returnUrl** e **salt** ([código de exemplo fornecido abaixo]):
     
     > HMAC(**salt**+ '\n' +**returnUrl**)
     > 
     > 
   * Compare o hash calculado acima ao valor do parâmetro de consulta **sig**. Se os dois hashes forem correspondentes, prossiga para a próxima etapa. Caso contrário, recuse as solicitações.
3. Verifique se que você está recebendo uma solicitação de entrada/inscrição: o parâmetro de consulta **operation** será definido como "**SignIn**".
4. Apresente ao usuário a IU para entrar ou se inscrever
5. Se o usuário estiver se inscrevendo, você precisará criar uma conta correspondente para ele no Gerenciamento de API. [Crie um usuário] com a API REST do Gerenciamento de API. Ao fazer isso, certifique-se de definir a ID de usuário como a mesma que está em seu repositório de usuários ou como uma ID que você possa acompanhar.
6. Quando o usuário for autenticado com sucesso:
   
   * [solicite um token de logon único (SSO)] por meio da API REST do Gerenciamento de API
   * anexe um parâmetro de consulta returnUrl ao URL SSO que você recebeu da chamada à API acima:
     
     > por exemplo: https://customer.portal.azure-api.net/signin-sso?token&returnUrl=/return/url 
     > 
     > 
   * redirecione o usuário à URL produzida acima

Além da operação **SignIn** , você também pode executar o gerenciamento de conta seguindo as etapas anteriores e usando uma das seguintes operações.

* **ChangePassword**
* **ChangeProfile**
* **CloseAccount**

Você deve passar os seguintes parâmetros de consulta para operações de gerenciamento de conta.

* **operation**: identifica o tipo de solicitação de delegação (ChangePassword, ChangeProfile ou CloseAccount)
* **userId**: a identificação de usuário da conta a ser gerenciada
* **salt**: uma cadeia de caracteres de salt especial usada para calcular um hash de segurança
* **sig**: um hash de segurança calculado para ser usado para comparação com seu próprio hash calculado

## <a name="delegate-product-subscription"> </a>Delegando a assinatura de produtos
A delegação de uma assinatura de produto funciona de forma semelhante à delegação de uma entrada/inscrição de usuário. O fluxo de trabalho final seria o seguinte:

1. O desenvolvedor selecione um produto no portal do desenvolvedor do Gerenciamento de API e clica no botão Assinar
2. O navegador é redirecionado ao ponto de extremidade de delegação
3. O ponto de extremidade de delegação realiza as etapas de assinatura de produto necessária - isso depende de você e pode envolver o redirecionamento para outra página para solicitar informações de cobrança, fazer perguntas adicionais ou simplesmente armazenar as informações sem precisar de ações do usuário

Para habilitar a funcionalidade, na página **Delegação**, clique em **Delegar assinatura do produto**.

Depois, certifique-se de que o ponto de extremidade de delegação realize as ações a seguir:

1. Receba uma solicitação com a seguinte forma:
   
   > *http://www.yourwebsite.com/apimdelegation?operation={operation}&productId={produto a ser assinado}&userId={usuário que faz a solicitação}&salt={string}&sig={string}*
   > 
   > 
   
    Parâmetros de consulta para a assinatura de produto:
   
   * **operation**: identifica o tipo de solicitação de delegação. Para solicitações de assinatura do produto, as opções válidas são:
     * “Subscribe”: uma solicitação para que o usuário assine determinado produto com uma ID fornecida (veja abaixo)
     * “Unsubscribe”: uma solicitação para cancelar a assinatura do usuário de um produto
     * “Renew”: uma solicitação para renovar uma assinatura (que pode, por exemplo, estar expirando)
   * **productId**: a ID do produto para o qual o usuário solicitou uma assinatura
   * **userId**: a ID do usuário para quem a solicitação está sendo feita
   * **salt**: uma cadeia de caracteres de salt especial usada para calcular um hash de segurança
   * **sig**: um hash de segurança calculado para ser usado para comparação com seu próprio hash calculado
2. Confirme que a solicitação está vindo do Gerenciamento de API do Azure (opcional, mas altamente recomendado por segurança)
   
   * Calcule um hash HMAC-SHA512 de uma cadeia baseada nos parâmetros de consulta **productId**, **userId** e **salt**:
     
     > HMAC(**salt**+ '\n' +**productId**+ '\n' +**userId**)
     > 
     > 
   * Compare o hash calculado acima ao valor do parâmetro de consulta **sig**. Se os dois hashes forem correspondentes, prossiga para a próxima etapa. Caso contrário, recuse as solicitações.
3. Faça o processamento de qualquer assinatura de produto com base no tipo de operação solicitada em **operation** - por exemplo, faturamento, perguntas etc.
4. Após realizar com êxito a assinatura do produto pelo usuário pela sua parte, assine o usuário do produto do Gerenciamento de API [chamando a API REST para assinatura do produto].

## <a name="delegate-example-code"> </a> Código de exemplo
Estes códigos de exemplo mostram como usar a *chave de validação de delegação*, que é definida na tela Delegação do Portal do editor, para criar um HMAC que será usado para validar a assinatura, comprovando a validade da returnUrl passada. O mesmo código funciona para productId e userId com pequenas modificações.

**Código C# para gerar hash de returnUrl**

```c#
using System.Security.Cryptography;

string key = "delegation validation key";
string returnUrl = "returnUrl query parameter";
string salt = "salt query parameter";
string signature;
using (var encoder = new HMACSHA512(Convert.FromBase64String(key)))
{
    signature = Convert.ToBase64String(encoder.ComputeHash(Encoding.UTF8.GetBytes(salt + "\n" + returnUrl)));
    // change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
    // compare signature to sig query parameter
}
```

**Código NodeJS para gerar hash de returnUrl**

```
var crypto = require('crypto');

var key = 'delegation validation key'; 
var returnUrl = 'returnUrl query parameter';
var salt = 'salt query parameter';

var hmac = crypto.createHmac('sha512', new Buffer(key, 'base64'));
var digest = hmac.update(salt + '\n' + returnUrl).digest();
// change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
// compare signature to sig query parameter

var signature = digest.toString('base64');
```

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre delegação, consulte o vídeo a seguir.

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Delegating-User-Authentication-and-Product-Subscription-to-a-3rd-Party-Site/player]
> 
> 

[Delegating developer sign-in and sign-up]: #delegate-signin-up
[Delegating product subscription]: #delegate-product-subscription
[solicite um token de logon único (SSO)]: http://go.microsoft.com/fwlink/?LinkId=507409
[Crie um usuário]: http://go.microsoft.com/fwlink/?LinkId=507655#CreateUser
[chamando a API REST para assinatura do produto]: http://go.microsoft.com/fwlink/?LinkId=507655#SSO
[Next steps]: #next-steps
[código de exemplo fornecido abaixo]: #delegate-example-code

[api-management-delegation-signin-up]: ./media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png 
