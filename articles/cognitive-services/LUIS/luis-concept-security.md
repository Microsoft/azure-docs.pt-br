---
title: Segurança quando colaboram
titleSuffix: Language Understanding - Azure Cognitive Services
description: O acesso de criação está disponível para proprietários e colaboradores. Para um aplicativo privado, o acesso de ponto de extremidade está disponível para proprietários e colaboradores.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/18/2018
ms.author: diberry
ms.openlocfilehash: 5abc2026362e19b406b34a4321e766c3229cc0b4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60813612"
---
# <a name="authoring-and-endpoint-user-access"></a>Acesso de criação e do usuário de ponto de extremidade
O acesso de criação está disponível para proprietários e colaboradores. Para um aplicativo privado, o acesso de ponto de extremidade está disponível para proprietários e colaboradores. Para um aplicativo público, o acesso de ponto de extremidade está disponível a todos que têm sua conta LUIS e que tem a ID do aplicativo público. 

## <a name="access-to-authoring"></a>Acesso à criação
O acesso ao aplicativo do site do [LUIS](luis-reference-regions.md#luis-website) ou às [APIs de criação](https://aka.ms/luis-authoring-apis) é controlado pelo proprietário do aplicativo. 

O proprietário e todos os colaboradores têm acesso para criar o aplicativo. 

|O acesso de criação inclui|Observações|
|--|--|
|Adicionar ou remover chaves de ponto de extremidade||
|Versão de exportação||
|Exportar logs de ponto de extremidade||
|Versão de importação||
|Tornar um aplicativo público|Quando um aplicativo for público, qualquer pessoa com uma chave de criação ou de ponto de extremidade poderá consultá-lo.|
|Modificar modelo|
|Publicar|
|Examinar declarações de ponto de extremidade para [aprendizado ativo](luis-how-to-review-endpoint-utterances.md)|
|Treinar|

## <a name="access-to-endpoint"></a>Acesso ao ponto de extremidade

O acesso para consultar o ponto de extremidade é controlado por uma configuração na página **Informações do aplicativo** na seção **Gerenciar**. 

![Definir aplicativo para público](./media/luis-concept-security/set-application-as-public.png)

|[Ponto de extremidade privado](#private-app-endpoint-security)|[Ponto de extremidade público](#public-app-endpoint-access)|
|:--|:--|
|Disponível para proprietário e colaboradores|Disponível para proprietário, colaboradores e para as pessoas que sabem a ID do aplicativo|

### <a name="private-app-endpoint-security"></a>Segurança de ponto de extremidade de aplicativo privado

O ponto de extremidade de um aplicativo privado está disponível para o seguinte:

|Chave e usuário|Explicação|
|--|--|
|Chave de criação do proprietário| Até 1000 ocorrências de ponto de extremidade|
|Chaves de criação dos colaboradores| Até 1000 ocorrências de ponto de extremidade|
|Qualquer chave atribuída ao LUIS por um autor ou colaborador|Com base na camada de uso da chave|

#### <a name="microsoft-user-accounts"></a>Contas de usuário Microsoft

Autores e colaboradores podem atribuir chaves a um aplicativo de LUIS privado. A conta de usuário Microsoft que cria a chave de LUIS no portal do Azure precisa ser o proprietário do aplicativo ou um colaborador do aplicativo. Você não pode atribuir uma chave a um aplicativo privado de outra conta do Azure.

Consulte [Usuário do locatário do Azure Active Directory](luis-how-to-collaborate.md#azure-active-directory-tenant-user) para saber mais sobre as contas de usuário do Active Directory. 

### <a name="public-app-endpoint-access"></a>Acesso de ponto de extremidade do aplicativo público

Depois que um aplicativo for configurado como público, _qualquer_ chave de criação LUIS válida ou chave do ponto de extremidade LUIS poderá consultar seu aplicativo, desde que a chave não tenha usado toda a cota de ponto de extremidade.

Um usuário que não é proprietário ou colaborador só poderá acessar um aplicativo público se tiver recebido a ID do aplicativo. O LUIS não tem um _mercado_ público ou outra maneira de pesquisar um aplicativo público.  

Um aplicativo público é publicado em todas as regiões para que um usuário com uma chave de recurso do LUIS baseada em região possa acessar o aplicativo em qualquer região associada com a chave de recurso.

## <a name="microsoft-user-accounts"></a>Contas de usuário Microsoft

Autores e colaboradores podem adicionar chaves ao LUIS na página de publicação. A conta de usuário Microsoft que cria a chave de LUIS no portal do Azure precisa ser o proprietário do aplicativo ou um colaborador do aplicativo. 

Consulte [Usuário do locatário do Azure Active Directory](luis-how-to-collaborate.md#azure-active-directory-tenant-user) para saber mais sobre as contas de usuário do Active Directory. 

## <a name="securing-the-endpoint"></a>Protegendo o ponto de extremidade 

É possível controlar quem pode ver sua chave do ponto de extremidade LUIS chamando-a em um ambiente servidor a servidor. Se você estiver usando o LUIS de um bot, a conexão entre o bot e o LUIS já estará segura. Se você estiver chamando o ponto de extremidade LUIS diretamente, deverá criar a API do servidor (como uma [função](https://azure.microsoft.com/services/functions/) do Azure) com acesso controlado (como [AAD](https://azure.microsoft.com/services/active-directory/)). Quando a API do servidor for chamada e a autenticação e a autorização forem verificadas, passe a chamada para o LUIS. Embora essa estratégia não impeça ataques "man-in-the-middle", ela oculta seu ponto de extremidade dos usuários, permite que você controle o acesso e que você adicione um log de resposta de ponto de extremidade (como o [Application Insights](https://azure.microsoft.com/services/application-insights/)).  

## <a name="security-compliance"></a>Conformidade de segurança
 
[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-security-compliance.md)]

## <a name="next-steps"></a>Próximas etapas

Confira [Melhores práticas](luis-concept-best-practices.md) para saber como usar intenções e entidades para obter as melhores previsões.
