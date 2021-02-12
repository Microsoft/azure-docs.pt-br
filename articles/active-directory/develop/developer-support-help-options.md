---
title: Opções de suporte e ajuda para desenvolvedores de aplicativos do Azure AD
description: Saiba como obter ajuda e suporte para problemas e dúvidas relacionados ao desenvolvimento ao criar aplicativos que se integram com as identidades da Microsoft (conta Azure Active Directory e Microsoft)
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/23/2019
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.openlocfilehash: bce9479d063d091eb4fa68d2452d8a4218d45db9
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2021
ms.locfileid: "99219936"
---
# <a name="support-and-help-options-for-developers"></a>Opções de suporte e ajuda para desenvolvedores

Se começar a integração ao Azure AD (Azure Active Directory), identidades da Microsoft ou API do Microsoft Graph ou quando estiver implementando um novo recurso no aplicativo, haverá vezes em que você precisará ter a ajuda da comunidade ou entender as opções de suporte que tem como desenvolvedor. Este artigo ajuda a entender essas opções, incluindo:

> [!div class="checklist"]
> * Como pesquisar para verificar se a pergunta não foi respondida pela comunidade ou se já existe uma documentação para o recurso que você está tentando implementar
> * Em alguns casos, você apenas deseja usar nossas ferramentas de suporte para ajudar a depurar um problema específico
> * Se você não conseguir encontrar a resposta de que precisa, talvez queira fazer uma pergunta no *Microsoft Q&a*
> * Se você encontrar um problema em uma de nossas bibliotecas de autenticação, será gerado um problema do *GitHub*
> * Por fim, se precisar falar com alguém, talvez queira abrir uma solicitação de suporte

## <a name="search"></a>Pesquisar

Se você tiver uma pergunta relacionada ao desenvolvimento, poderá encontrar a resposta na documentação, nos [exemplos do GitHub](https://github.com/azure-samples)ou nas respostas para a [Microsoft Q&](https://docs.microsoft.com/answers/products/) perguntas.

### <a name="scoped-search"></a>Pesquisa com escopo

Para obter resultados mais rápidos, use o escopo de sua pesquisa para a [Microsoft Q&uma](https://docs.microsoft.com/answers/products/)documentação e os exemplos de código usando a seguinte consulta em seu mecanismo de pesquisa favorito:

```
{Your Search Terms} (site:http://www.docs.microsoft.com/answers/products/ OR site:docs.microsoft.com OR site:github.com/azure-samples OR site:cloudidentity.com OR site:developer.microsoft.com/graph)
```

Em que *{Seus Termos de Pesquisa}* correspondem às palavras-chave de pesquisa.

## <a name="use-the-development-support-tools"></a>Use as ferramentas de suporte de desenvolvimento

| Ferramenta  | Descrição  |
|---------|---------|
| [jwt.ms](https://jwt.ms) | Cole uma ID ou token de acesso para decodificar os nomes das declarações e valores. |
| [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)| Ferramentas que permitem fazer solicitações e ver as respostas na API do Microsoft Graph. |

## <a name="post-a-question-to-microsoft-qa"></a>Poste uma pergunta para a Microsoft Q&A

O [Microsoft Q&a](https://docs.microsoft.com/answers/products/) é o canal preferido para perguntas relacionadas ao desenvolvimento. Aqui, os membros da comunidade de desenvolvedores e da equipe da Microsoft estão diretamente envolvidos em ajudá-lo a resolver seus problemas.

Se você não encontrar uma resposta à sua pergunta por meio de pesquisa, envie uma nova pergunta para a [Microsoft Q&a](https://docs.microsoft.com/answers/products/) . Use uma das seguintes marcas ao fazer perguntas para ajudar a comunidade a identificar e responder a pergunta com mais rapidez:

|Componente/área  | Marcações |
|---------|---------|
| Biblioteca ADAL | [Adal](https://docs.microsoft.com/answers/topics/azure-ad-adal-deprecation.html) |
| Biblioteca MSAL     | [MSAL](https://docs.microsoft.com/answers/topics/azure-ad-msal.html) |
| Middleware OWIN  | [[Azure-Active-Directory]](https://docs.microsoft.com/answers/topics/azure-active-directory.html) |
| [B2B do Azure](../external-identities/what-is-b2b.md)  | [[azure-ad-b2b]](https://docs.microsoft.com/answers/topics/azure-ad-b2b.html) |
| [B2C do Azure](https://azure.microsoft.com/services/active-directory-b2c/)  | [[azure-ad-b2c]](https://docs.microsoft.com/answers/topics/azure-ad-b2c.html) |
| [API do Microsoft Graph](https://developer.microsoft.com/graph/) | [[Azure-ad-Graph]](https://docs.microsoft.com/answers/topics/azure-ad-graph.html) |
| Qualquer outra área relacionada aos tópicos de autenticação ou autorização | [[Azure-Active-Directory]](https://docs.microsoft.com/answers/topics/azure-active-directory.html) |

As seguintes postagens da [Microsoft Q&a](https://docs.microsoft.com/answers/products/) contêm dicas sobre como fazer perguntas e como adicionar código-fonte. Siga essas diretrizes para aumentar as chances de os membros da comunidade avaliarem e responderem à sua pergunta rapidamente:

* [Como fazer fazer uma boa pergunta](https://docs.microsoft.com/answers/articles/24951/how-to-write-a-quality-question.html)
* [Como criar um exemplo mínimo, completo e verificável](https://docs.microsoft.com/answers/articles/24907/how-to-write-a-quality-answer.html)

## <a name="create-a-github-issue"></a>Criar um problema do GitHub

Se encontrar um bug ou problema relacionado às nossas bibliotecas, gere um problema em nossos repositórios do GitHub. Como nossas bibliotecas são de software livre, você também pode enviar uma solicitação de pull.

Para obter uma lista de bibliotecas e seus repositórios GitHub, consulte o seguinte:

* Bibliotecas [Adal (Azure Active Directory Authentication Library)](../azuread-dev/active-directory-authentication-libraries.md) e repositórios github
* Bibliotecas [do MSAL (biblioteca de autenticação da Microsoft)](reference-v2-libraries.md) e repositórios do github

## <a name="open-a-support-request"></a>Abrir uma solicitação de suporte

Se precisar falar com alguém, poderá abrir uma solicitação de suporte. Se você for um cliente do Azure, há várias opções de suporte disponíveis. Para comparar os planos, confira [esta página](https://azure.microsoft.com/support/plans/). O suporte ao desenvolvedor também está disponível para os clientes do Azure. Para obter informações sobre como adquirir planos de suporte do Desenvolvedor, confira [esta página](https://azure.microsoft.com/support/plans/developer/).

* Se você já tiver um Plano de Suporte do Azure, [abra uma solicitação de suporte aqui](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

* Se você não for um cliente do Azure, também poderá abrir uma solicitação de suporte com a Microsoft por meio do [nosso suporte comercial](https://support.serviceshub.microsoft.com/supportforbusiness).

Você também pode experimentar um [agente virtual](https://support.microsoft.com/contactus/?ws=support) para obter suporte ou fazer perguntas.