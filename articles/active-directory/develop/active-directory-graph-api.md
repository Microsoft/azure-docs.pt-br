---
title: API do Graph do Active Directory do Azure
description: Uma visão geral e guia de início rápido para a API do Graph do Azure AD, que permite acesso programático ao Azure AD por meio de pontos de extremidade da API REST.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/26/2019
ms.author: ryanwi
ms.reviewer: dkershaw, sureshja
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: da99468b1582c4acab192ad3b96761172aa69580
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89068653"
---
# <a name="azure-active-directory-graph-api"></a>API do Graph do Active Directory do Azure

> [!IMPORTANT]
> É altamente recomendável que você use [Microsoft Graph](https://developer.microsoft.com/graph) em vez de API do Graph do Azure ad para acessar recursos do Azure Active Directory (AD do Azure). Nossos esforços de desenvolvimento agora estão concentrados no Microsoft Graph e não há planejamento de novas melhorias para a API do Azure AD Graph. Há um número muito limitado de cenários para os quais o Azure AD API do Graph ainda pode ser apropriado; para obter mais informações, consulte a postagem do blog [Microsoft Graph ou do Azure ad Graph](https://developer.microsoft.com/office/blogs/microsoft-graph-or-azure-ad-graph/) e [migrar aplicativos do Azure ad Graph para Microsoft Graph](/graph/migrate-azure-ad-graph-planning-checklist).

Este artigo se aplica à API do Graph do Azure Active Directory. Para obter informações semelhantes relacionadas a API do Microsoft Graph, consulte [usar a API do Graph](/graph/use-the-api).

A API do Graph do Active Directory do Azure fornece acesso programático ao AD do Azure por meio de pontos de extremidade da API REST. Aplicativos podem usar a API do Microsoft Azure AD Graph para realizar operações CRUD (criar, ler, atualizar e excluir) nos objetos e dados do diretório. Por exemplo, a API do Microsoft Azure AD Graph fornece suporte às seguintes operações comuns para um objeto de usuário:

* Criar um novo usuário em um diretório
* Obter as propriedades detalhadas de um usuário, como seus grupos
* Atualizar as propriedades de um usuário, como seu local e número de telefone, ou alterar sua senha
* Verificar a associação de grupo de um usuário para acesso baseado em função
* Desabilitar a conta de um usuário ou excluí-la inteiramente

Além disso, você pode executar operações semelhantes em outros objetos, como grupos e aplicativos. Para chamar a API do Microsoft Azure AD Graph em um diretório, o aplicativo deve estar registrado no Microsoft Azure AD. O aplicativo também deve ter acesso concedido para API do Microsoft Azure AD Graph. Esse acesso é normalmente obtido por meio de um fluxo de consentimento do usuário ou administrador.

Para começar a usar o Azure Active Directory API do Graph, consulte o [Guia de início rápido do Azure ad API do Graph](./microsoft-graph-intro.md)ou exiba a [documentação de referência interativa do Azure ad API do Graph](/previous-versions/azure/ad/graph/api/api-catalog).

## <a name="features"></a>Recursos

API do Microsoft Azure AD Graph fornece os seguintes recursos:

* **Pontos de extremidade da API REST**: a API do Microsoft Azure AD Graph é um serviço RESTful composto por pontos de extremidade acessados por meio de solicitações HTTP padrão. A API do Microsoft Azure AD Graph fornece suporte a tipos de conteúdo XML ou JavaScript Object Notation (JSON) para solicitações e respostas. Para saber mais, consulte [Referência da API REST do Graph do Azure AD](/previous-versions/azure/ad/graph/api/api-catalog).
* **Autenticação com Microsoft Azure AD**: cada solicitação à API do Microsoft Azure AD Graph deve ser autenticada com o anexo de um JSON Web Token (JWT) no cabeçalho de Autorização da solicitação. Esse token é adquirido fazendo uma solicitação ao ponto de extremidade do token do Azure AD e fornecendo credenciais válidas. Você pode usar o fluxo de credenciais de cliente OAuth 2.0 ou o fluxo de concessão do código de autorização para adquirir um token para chamar o Graph. Para obter mais informações, veja [OAuth 2.0 no AD do Azure](/previous-versions/azure/dn645545(v=azure.100)).
* **RBAC (Autorização baseada em função)**: grupos de segurança são usados para executar a RBAC na API do Microsoft Azure AD Graph. Por exemplo, se você quiser determinar se um usuário tem acesso a um recurso específico, o aplicativo poderá chamar a operação [Verificar associação de grupo (transitiva)](/previous-versions/azure/ad/graph/api/functions-and-actions#checkMemberGroups), que retornará verdadeiro ou falso.
* **Consulta diferencial**: consulta diferencial permite controlar as alterações em um diretório entre dois períodos de tempo sem precisar fazer consultas frequentes à API do Microsoft Azure AD Graph. Este tipo de solicitação retornará apenas as alterações feitas entre a solicitação de consulta diferencial anterior e a solicitação atual. Para saber mais, leia [Consulta diferencial da API do Graph Azure AD](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-differential-query).
* **Extensões de diretório**: é possível adicionar propriedades personalizadas a objetos de diretório sem exigir um armazenamento de dados externo. Por exemplo, se o aplicativo exigir uma propriedade de ID do Skype para cada usuário, você poderá registrar a nova propriedade no diretório e estará disponível para uso em cada objeto de usuário. Para obter mais informações, consulte [Extensões de esquema do diretório da API do Graph do Azure AD](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-directory-schema-extensions).
* **Protegido pelos escopos de permissão**: a API do Microsoft Azure AD Graph expõe escopos de permissão que permitem acesso seguro aos dados do Microsoft Azure AD usando o OAuth 2.0. Isso fornece suporta a uma variedade de tipos de aplicativo cliente, incluindo:
  
  * interfaces de usuário que recebem acesso delegado aos dados por meio de autorização do usuário conectado (delegado)
  * aplicativos de serviço/daemon que funcionam em segundo plano sem que um usuário conectado esteja presente e use o controle de acesso baseado em função definido pelo aplicativo
    
    Ambas as permissões delegadas e de aplicativo representam um privilégio exposto pela API do Microsoft Azure AD Graph e podem ser solicitadas pelos aplicativos clientes por meio dos recursos de permissões de registro do aplicativo no [Portal do Azure](https://portal.azure.com). O [Azure AD API do Graph escopos de permissão](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes) fornece informações sobre o que está disponível para uso pelo aplicativo cliente.

## <a name="scenarios"></a>Cenários

A API do Microsoft Azure AD Graph habilita muitos cenários de aplicativos. A seguir, os cenários mais comuns:

* **Aplicativo de linha de negócios (único locatário)**: neste cenário, um desenvolvedor corporativo trabalha para uma organização que tem uma assinatura do Office 365. O desenvolvedor está criando um aplicativo da Web que interage com o Azure AD para executar tarefas, como atribuir uma licença a um usuário. Essa tarefa exige acesso à API do Graph do Azure AD, portanto, o desenvolvedor registra o aplicativo de locatário único no Azure AD e configura as permissões de leitura e gravação da API do Graph do Azure AD. Em seguida, o aplicativo é configurado para usar suas próprias credenciais ou as do usuário de login atual para adquirir um token para chamar a API do Graph do Azure AD.
* **Software como um aplicativo de serviço (multilocatário)**: nesse cenário, um fornecedor de software independente (ISV) está desenvolvendo um aplicativo da Web multilocatário hospedado que fornece recursos de gerenciamento de usuário para outras organizações que usam o Azure AD. Esses recursos exigem acesso a objetos de diretório, portanto, o aplicativo precisa chamar a API do Graph do Azure AD. O desenvolvedor registra o aplicativo no Microsoft Azure AD, configura para exigir as permissões de leitura e gravação para a API do Microsoft Azure AD Graph e habilita o acesso externo para que outras organizações possam concordar com o uso do aplicativo em seu diretório. Quando um usuário de outra organização autentica o aplicativo pela primeira vez, é mostrada uma caixa de diálogo de consentimento com as permissões que o aplicativo está solicitando. Conceder consentimento dará ao aplicativo as permissões solicitadas ao Azure AD API do Graph no diretório do usuário. Para saber mais sobre a estrutura de consentimento, consulte [Visão geral da estrutura de consentimento](consent-framework.md).

## <a name="next-steps"></a>Próximas etapas

Para começar a usar a API do Graph do Azure Active Directory, consulte os tópicos a seguir:

* [Guia de início rápido para a API do Graph do Azure AD](./microsoft-graph-intro.md)
* [Documentação do REST para a Microsoft Azure AD Graph](/previous-versions/azure/ad/graph/api/api-catalog)
