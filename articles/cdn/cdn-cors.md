---
title: Usando a CDN do Azure com o CORS | Microsoft Docs
description: Saiba como usar a CDN (Rede de Distribuição de Conteúdo) do Azure com o CORS (Compartilhamento de Recursos entre Origens).
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 86740a96-4269-4060-aba3-a69f00e6f14e
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 204183fa25203a094eecd8df85a8bfd5dcf271cc
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593967"
---
# <a name="using-azure-cdn-with-cors"></a>Usar a CDN do Azure com o CORS
## <a name="what-is-cors"></a>O que é CORS?
O CORS (Compartilhamento de Recursos entre Origens) é um recurso HTTP que permite que um aplicativo web em execução em um domínio acesse recursos em outro domínio. Para reduzir a possibilidade de ataques de script entre sites, todos os navegadores modernos implementam uma restrição de segurança conhecida como [política de mesma origem](https://www.w3.org/Security/wiki/Same_Origin_Policy).  Isso impede que uma página da Web chame APIs em um domínio diferente.  O CORS fornece uma maneira segura para permitir que uma origem (o domínio de origem) chame APIs em outra origem.

## <a name="how-it-works"></a>Como ele funciona
Há dois tipos de solicitações CORS, *solicitações simples* e *solicitações complexas.*

### <a name="for-simple-requests"></a>Para solicitações simples:

1. O navegador envia a solicitação CORS com adicional **origem** cabeçalho de solicitação HTTP. O valor desse cabeçalho é a origem que serviu a página pai, que é definida como a combinação de *protocolo,* *domínio,* e *porta.*  Quando uma página de https\://www.contoso.com tenta acessar dados de um usuário na origem fabrikam.com, o cabeçalho de solicitação a seguir seria enviado para fabrikam.com:

   `Origin: https://www.contoso.com`

2. O servidor pode responder com um dos seguintes:

   * Um cabeçalho **Access-Control-Allow-Origin** em sua resposta indicando qual site de origem é permitido. Por exemplo:

     `Access-Control-Allow-Origin: https://www.contoso.com`

   * Um código de erro HTTP como 403, se o servidor não permite que a solicitação entre origens depois de verificar o cabeçalho de origem

   * Um cabeçalho **Access-Control-Allow-Origin** com um caractere curinga que permite todas as origens:

     `Access-Control-Allow-Origin: *`

### <a name="for-complex-requests"></a>Para solicitações complexas:

Uma solicitação complexa é uma solicitação CORS onde o navegador é necessário para enviar um *solicitação de simulação* (ou seja, uma investigação preliminar) antes de enviar a solicitação CORS real. A solicitação de simulação pede a permissão do servidor se o CORS originais da solicitação pode continuar e é um `OPTIONS` solicitação para a mesma URL.

> [!TIP]
> Para obter mais detalhes sobre fluxos CORS e armadilhas comuns, consulte o [guia CORS para APIs REST](https://www.moesif.com/blog/technical/cors/Authoritative-Guide-to-CORS-Cross-Origin-Resource-Sharing-for-REST-APIs/).
>
>

## <a name="wildcard-or-single-origin-scenarios"></a>Cenários de origem única ou curinga
O CORS na Azure CDN funcionará automaticamente sem nenhuma configuração adicional quando o cabeçalho **Access-Control-Allow-Origin** for definido como caractere curinga (*) ou uma origem única.  A CDN armazenará a primeira resposta em cache e as solicitações subsequentes usarão o mesmo cabeçalho.

Se já tiverem sido feitas solicitações à CDN antes de o CORS ser definido na origem, você precisará limpar o conteúdo no conteúdo do ponto de extremidade para recarregar o conteúdo com o cabeçalho **Access-Control-Allow-Origin**.

## <a name="multiple-origin-scenarios"></a>Cenários de várias origens
Se você precisar permitir que uma lista específica de origens seja permitida para o CORS, isso será um pouco mais complicado. O problema ocorre quando a CDN armazena o cabeçalho **Access-Control-Allow-Origin** em cache para a primeira origem do CORS.  Quando uma origem do CORS diferente fizer uma solicitação subsequente, a CDN terá fornecido o cabeçalho **Access-Control-Allow-Origin** armazenado em cache que não é correspondente.  Há várias maneiras de corrigir o problema.

### <a name="azure-cdn-premium-from-verizon"></a>CDN Premium do Azure da Verizon
A melhor maneira de habilitar essa opção é usar o **Azure CDN Premium da Verizon**, que expõe algumas funcionalidades avançadas. 

Você precisará [criar uma regra](cdn-rules-engine.md) para verificar o cabeçalho **Origin** na solicitação.  Se for uma origem válida, a regra definirá o cabeçalho **Access-Control-Allow-Origin** com a origem fornecida na solicitação.  Se a origem especificada no cabeçalho **Origem** não for permitida, a regra deverá omitir o cabeçalho **Access-Control-Allow-Origin**, que fará com que o navegador rejeite a solicitação. 

Há duas maneiras de fazer isso com o mecanismo de regras. Em ambos os casos, o cabeçalho **Access-Control-Allow-Origin** do servidor de origem do arquivo é ignorado e o mecanismo de regras da CDN gerencia completamente as origens CORS permitidas.

#### <a name="one-regular-expression-with-all-valid-origins"></a>Uma expressão regular com todas as origens válidas
Nesse caso, você criará uma expressão regular que inclua todas as origens que deseja permitir: 

    https?:\/\/(www\.contoso\.com|contoso\.com|www\.microsoft\.com|microsoft.com\.com)$

> [!TIP]
> A **CDN Premium do Azure da Verizon** usa as [Expressões Regulares Compatíveis com o Perl](https://pcre.org/) como seu mecanismo de expressões regulares.  Você pode usar uma ferramenta, como [Expressões Regulares 101](https://regex101.com/), para validar sua expressão regular.  Observe que o caractere "/" é válido em expressões regulares e não precisa ter um caractere de escape. No entanto, adicionar um caractere de escape a esse caractere é considerado uma prática recomendada e é esperado por alguns validadores de regex.
> 
> 

Se a expressão regular for correspondente, a regra substituirá o cabeçalho **Access-Control-Allow-Origin** (se houver) da origem pela origem que enviou a solicitação.  Você também pode adicionar outros cabeçalhos CORS, como **Access-Control-Allow-Methods**.

![Exemplo de regras com expressões regulares](./media/cdn-cors/cdn-cors-regex.png)

#### <a name="request-header-rule-for-each-origin"></a>Solicitar a regra de cabeçalho para cada origem.
Em vez de expressões regulares, você pode criar uma regra separada para cada origem que deseja permitir usando a **condição de correspondência** [Curinga do Cabeçalho de Solicitação](/previous-versions/azure/mt757336(v=azure.100)#match-conditions). Assim como acontece com o método de expressão regular, apenas o mecanismo de regras define os cabeçalhos do CORS. 

![Exemplo de regras sem expressões regulares](./media/cdn-cors/cdn-cors-no-regex.png)

> [!TIP]
> No exemplo acima, o uso do caractere curinga * instrui o mecanismo de regras a corresponder HTTP e HTTPS.
> 
> 

### <a name="azure-cdn-standard-profiles"></a>Perfis padrão do Azure CDN
Em perfis padrão do Azure CDN (**Azure CDN Standard da Microsoft**, **Azure CDN Standard da Akamai** e **Azure CDN Standard da Verizon**), o único mecanismo para permitir várias origens sem o uso da origem curinga é o uso de [cache de cadeia de consulta](cdn-query-string.md). Habilite a configuração da cadeia de caracteres de consulta para o ponto de extremidade da CDN e usar uma cadeia de caracteres de consulta exclusiva para solicitações de cada domínio permitido. Isso fará com que a CDN armazene em cache um objeto separado para cada cadeia de caractere de consulta exclusiva. No entanto, essa abordagem não é ideal, pois resultará em várias cópias do mesmo arquivo armazenadas em cache na CDN.  

