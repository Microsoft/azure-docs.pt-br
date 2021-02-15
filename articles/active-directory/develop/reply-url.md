---
title: Restrições de URI de redirecionamento (URL de resposta) | Azure
titleSuffix: Microsoft identity platform
description: Uma descrição das restrições e limitações do formato URI de redirecionamento (URL de resposta) imposto pela plataforma de identidade da Microsoft.
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 11/23/2020
ms.topic: conceptual
ms.subservice: develop
ms.custom: aaddev
ms.service: active-directory
ms.reviewer: marsma, lenalepa, manrath
ms.openlocfilehash: 91df89a69368056c1967e641562cf8515f44ade0
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99582801"
---
# <a name="redirect-uri-reply-url-restrictions-and-limitations"></a>Restrições e limitações do URI de redirecionamento (URL de resposta)

Um URI de redirecionamento, ou URL de resposta, é o local onde o servidor de autorização envia o usuário depois que o aplicativo é autorizado com êxito e recebe um código de autorização ou um token de acesso. O servidor de autorização envia o código ou o token para o URI de redirecionamento, portanto, é importante que você registre o local correto como parte do processo de registro do aplicativo.

 As restrições a seguir se aplicam a URLs de redirecionamento:

* O URI de redirecionamento deve começar com o esquema `https` . Há algumas [exceções para URIs de](#localhost-exceptions) redirecionamento de localhost.

* O URI de redirecionamento diferencia maiúsculas de minúsculas. As letras maiúsculas e minúsculas devem corresponder às letras maiúsculas e minúsculas do caminho da URL do aplicativo em execução. Por exemplo, se seu aplicativo incluir como parte de seu caminho `.../abc/response-oidc` , não especifique `.../ABC/response-oidc` no URI de redirecionamento. Como o navegador da Web trata os caminhos diferenciando maiúsculas de minúsculas, os cookies associados a `.../abc/response-oidc` podem ser excluídos se forem redirecionados para a URL de `.../ABC/response-oidc` com maiúsculas e minúsculas não correspondentes.

## <a name="maximum-number-of-redirect-uris"></a>Número máximo de URIs de redirecionamento

Esta tabela mostra o número máximo de URIs de redirecionamento que você pode adicionar a um registro de aplicativo na plataforma de identidade da Microsoft.

| Contas sendo conectadas | Número máximo de URIs de redirecionamento | Descrição |
|--------------------------|---------------------------------|-------------|
| Contas corporativas ou de estudante da Microsoft no locatário do Azure Active Directory (Azure AD) de qualquer organização | 256 | O campo `signInAudience` no manifesto do aplicativo é definido como *AzureADMyOrg* ou *AzureADMultipleOrgs*. |
| Contas pessoais e contas corporativas e de estudante da Microsoft | 100 | O campo `signInAudience` no manifesto do aplicativo é definido como *AzureADandPersonalMicrosoftAccount*. |

## <a name="maximum-uri-length"></a>Tamanho máximo do URI

Você pode usar um máximo de 256 caracteres para cada URI de redirecionamento que você adicionar a um registro de aplicativo.

## <a name="supported-schemes"></a>Esquemas compatíveis

O modelo de aplicativo Azure Active Directory (Azure AD) atualmente dá suporte a esquemas HTTP e HTTPS para aplicativos que entram em contas corporativas ou de estudante no locatário do Azure AD de qualquer organização. Esses tipos de conta são especificados pelos `AzureADMyOrg` `AzureADMultipleOrgs` valores e no `signInAudience` campo do manifesto do aplicativo. Para aplicativos que entram em contas pessoais da Microsoft (MSA) *e* contas corporativas e de estudante (ou seja, o `signInAudience` é definido como `AzureADandPersonalMicrosoftAccount` ), somente o esquema HTTPS é permitido.

Para adicionar URIs de redirecionamento com um esquema HTTP aos registros de aplicativo que entram em contas corporativas ou de estudante, use o editor de manifesto do aplicativo em [registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) no portal do Azure. No entanto, embora seja possível definir um URI de redirecionamento baseado em HTTP usando o editor de manifesto, é *altamente* recomendável que você use o esquema HTTPS para seus URIs de redirecionamento.

## <a name="localhost-exceptions"></a>Exceções de localhost

De acordo com as [seções RFC 8252 8,3](https://tools.ietf.org/html/rfc8252#section-8.3) e [7,3](https://tools.ietf.org/html/rfc8252#section-7.3), os URIs de redirecionamento "Loopback" ou "localhost" vêm com duas considerações especiais:

1. `http` Os esquemas de URI são aceitáveis porque o redirecionamento nunca deixa o dispositivo. Dessa forma, ambos os URIs são aceitáveis:
    - `http://localhost/myApp`
    - `https://localhost/myApp`
1. Devido a intervalos de porta efêmeras geralmente exigidos por aplicativos nativos, o componente de porta (por exemplo, `:5001` ou `:443` ) é ignorado para fins de correspondência de um URI de redirecionamento. Como resultado, todos esses URIs são considerados equivalentes:
    - `http://localhost/MyApp`
    - `http://localhost:1234/MyApp`
    - `http://localhost:5000/MyApp`
    - `http://localhost:8080/MyApp`

Do ponto de vista do desenvolvimento, isso significa algumas coisas:

* Não registre vários URIs de redirecionamento onde apenas a porta difere. O servidor de logon escolherá um arbitrariamente e usará o comportamento associado a esse URI de redirecionamento (por exemplo, se for um `web` `native` redirecionamento-,-ou `spa` -tipo).

    Isso é especialmente importante quando você deseja usar fluxos de autenticação diferentes no mesmo registro de aplicativo, por exemplo, tanto a concessão de código de autorização quanto o fluxo implícito. Para associar o comportamento de resposta correto a cada URI de redirecionamento, o servidor de logon deve ser capaz de distinguir entre os URIs de redirecionamento e não pode fazê-lo quando apenas a porta difere.
* Para registrar vários URIs de redirecionamento no localhost para testar fluxos diferentes durante o desenvolvimento, diferencie-os usando o componente de *caminho* do URI. Por exemplo, `http://localhost/MyWebApp` não corresponde `http://localhost/MyNativeApp` .
* O endereço de loopback IPv6 ( `[::1]` ) não tem suporte no momento.

#### <a name="prefer-127001-over-localhost"></a>Preferir 127.0.0.1 em localhost

Para impedir que seu aplicativo seja interrompido por firewalls configurados incorretamente ou por interfaces de rede renomeadas, use o endereço IP literal loopback `127.0.0.1` no URI de redirecionamento em vez de `localhost` . Por exemplo, `https://127.0.0.1`.

No entanto, você não pode usar a caixa de texto **URIs de redirecionamento** na portal do Azure para adicionar um URI de redirecionamento baseado em auto-retorno que usa o `http` esquema:

:::image type="content" source="media/reply-url/portal-01-no-http-loopback-redirect-uri.png" alt-text="Caixa de diálogo de erro no portal do Azure mostrando o URI de redirecionamento de loopback baseado em http não permitido":::

Para adicionar um URI de redirecionamento que usa o `http` esquema com o `127.0.0.1` endereço de loopback, você deve modificar atualmente o atributo [replyUrlsWithType](reference-app-manifest.md#replyurlswithtype-attribute) no [manifesto do aplicativo](reference-app-manifest.md).

## <a name="restrictions-on-wildcards-in-redirect-uris"></a>Restrições em curingas em URIs de redirecionamento

URIs de curinga como `https://*.contoso.com` podem parecer convenientes, mas devem ser evitados devido a implicações de segurança. De acordo com a especificação OAuth 2.0 ([seção 3.1.2 do RFC 6749](https://tools.ietf.org/html/rfc6749#section-3.1.2)), um URI de ponto de extremidade de redirecionamento deve ser um URI absoluto.

Atualmente, não há suporte para URIs curinga em registros de aplicativo configurados para entrar em contas pessoais da Microsoft e contas corporativas ou de estudante. Os URIs curinga são permitidos, no entanto, para aplicativos configurados para entrar somente em contas corporativas ou de estudante no locatário do Azure AD de uma organização.

Para adicionar URIs de redirecionamento com curingas aos registros de aplicativo que entram em contas corporativas ou de estudante, use o editor de manifesto do aplicativo em [registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) no portal do Azure. Embora seja possível definir um URI de redirecionamento com um curinga usando o editor de manifesto, é *altamente* recomendável aderir à [seção 3.1.2 do RFC 6749](https://tools.ietf.org/html/rfc6749#section-3.1.2) e usar apenas URIs absolutos.

Se seu cenário exigir mais URIs de redirecionamento do que o limite máximo permitido, considere a seguinte [abordagem de parâmetro de estado](#use-a-state-parameter) em vez de adicionar um URI de redirecionamento de caractere curinga.

#### <a name="use-a-state-parameter"></a>Usar um parâmetro de estado

Se você tiver vários subdomínios e seu cenário exigir que, após a autenticação bem-sucedida, você redirecione os usuários para a mesma página da qual eles foram iniciados, o uso de um parâmetro de estado pode ser útil.

Nesta abordagem:

1. Crie um URI de redirecionamento "compartilhado" por aplicativo para processar os tokens de segurança que você recebe do ponto de extremidade da autorização.
1. Seu aplicativo pode enviar parâmetros específicos do aplicativo (como a URL do subdomínio em que o usuário originou ou qualquer coisa como informações de identidade visual) no parâmetro State. Ao usar um parâmetro de estado, considere a proteção contra CSRF, como especificado na [seção 10.12 da RFC 6749](https://tools.ietf.org/html/rfc6749#section-10.12)).
1. Os parâmetros específicos do aplicativo incluirão todas as informações necessárias para que o aplicativo processe a experiência correta para o usuário, ou seja, construa o estado apropriado do aplicativo. O ponto de extremidade da autorização do Azure AD retira o HTML do parâmetro de estado; portanto, certifique-se de que você não está passando o conteúdo HTML nesse parâmetro.
1. Quando o Azure AD envia uma resposta para o URI de redirecionamento "compartilhado", ele envia o parâmetro de estado de volta para o aplicativo.
1. Assim, o aplicativo pode usar o valor do parâmetro de estado para determinar a URL de destino do usuário. Não se esqueça de garantir a proteção contra CSRF.

> [!WARNING]
> Essa abordagem permite que um cliente comprometido modifique os parâmetros adicionais enviados no parâmetro de estado, redirecionando, assim, o usuário para uma URL diferente, que é a [ameaça de redirecionamento aberta](https://tools.ietf.org/html/rfc6819#section-4.2.4) descrita na RFC 6819. Portanto, o cliente deve proteger esses parâmetros criptografando o estado ou verificando-o por outros meios, como validar o nome de domínio no URI de redirecionamento em relação ao token.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o [manifesto do aplicativo](reference-app-manifest.md)de registro de aplicativo.
