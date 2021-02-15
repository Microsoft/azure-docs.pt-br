---
title: Use o SCIM, o Microsoft Graph e o Azure AD para provisionar usuários e enriquecer aplicativos com dados
description: Usando o SCIM e o Microsoft Graph em conjunto para provisionar usuários e enriquecer seu aplicativo com os dados de que precisa.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: reference
ms.date: 04/26/2020
ms.author: kenwith
ms.reviewer: arvinh, celested
ms.openlocfilehash: 0a5d84585f28f6d13cbceb1fec41d6cdabf6d08c
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99255620"
---
# <a name="using-scim-and-microsoft-graph-together-to-provision-users-and-enrich-your-application-with-the-data-it-needs"></a>Usando o SCIM e o Microsoft Graph juntos para provisionar usuários e enriquecer seu aplicativo com os dados de que precisa

**Público-alvo:** Este artigo destina-se a desenvolvedores que criam aplicativos a serem integrados ao Azure Active Directory (AD do Azure). Se você estiver procurando usar aplicativos já integrados ao Azure AD, como zoom, ServiceNow e DropBox, poderá ignorar este artigo e examinar os [tutoriais](../saas-apps/tutorial-list.md) específicos do aplicativo ou examinar [como o serviço de provisionamento funciona](./how-provisioning-works.md).

**Cenários comuns**

O Azure AD fornece um serviço pronto para uso para provisionamento e uma plataforma extensível para criar seus aplicativos. A árvore de decisão descreve como um desenvolvedor usaria o [scim](https://aka.ms/scimoverview) e o [Microsoft Graph](/graph/overview) para automatizar o provisionamento. 

> [!div class="checklist"]
> * Criar automaticamente usuários em meu aplicativo
> * Remover automaticamente os usuários do meu aplicativo quando eles não tiverem mais acesso
> * Integrar meu aplicativo com vários provedores de identidade para provisionamento
> * Enriquecer meu aplicativo com dados de serviços da Microsoft, como equipes, Outlook e Office.
> * Criar, atualizar e excluir automaticamente usuários e grupos no Azure AD e Active Directory

![Árvore de decisão do grafo SCIM](./media/user-provisioning/scim-graph.png)

## <a name="scenario-1-automatically-create-users-in-my-app"></a>Cenário 1: criar automaticamente usuários em meu aplicativo
Hoje, os administradores de ti provisionam os usuários criando manualmente as contas de usuário ou carregando arquivos CSV periodicamente em meu aplicativo. O processo é demorado para os clientes e reduz a adoção do meu aplicativo. Tudo o que preciso é de informações básicas do usuário, como nome, email e userPrincipalName, para criar um usuário. 

**Recomendação**: 
* Se os seus clientes usam vários IdPs e você não deseja manter um mecanismo de sincronização para integração com cada um, dê suporte a um ponto de extremidade de conformidade com SCIM [/Users](https://aka.ms/scimreferencecode) . Seus clientes poderão usar facilmente esse ponto de extremidade para se integrarem ao serviço de provisionamento do Azure AD e criar automaticamente contas de usuário quando precisarem de acesso. Você pode criar o ponto de extremidade uma vez e ele será compatível com todos os IdPs. Confira a solicitação de exemplo abaixo para saber como um usuário seria criado usando SCIM.
* Se você precisar de dados de usuário encontrados no objeto de usuário no Azure AD e outros dados de toda a Microsoft, considere criar um ponto de extremidade SCIM para provisionamento de usuário e chamar o Microsoft Graph para obter o restante dos dados. 

```json
POST /Users
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"],
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "userName": "BillG",
    "active": true,
    "meta": {
        "resourceType": "User"
    },
    "name": {
        "formatted": "Bill Gates",
        "familyName": "Gates",
        "givenName": "Bill"
    },
    "roles": []
}
```

## <a name="scenario-2-automatically-remove-users-from-my-app"></a>Cenário 2: remover automaticamente os usuários do meu aplicativo
Os clientes que usam meu aplicativo são focados em segurança e têm requisitos de governança para remover contas quando os funcionários não precisam mais delas. Como é possível automatizar o desprovisionamento do meu aplicativo?

**Recomendação:** Suporte a um ponto de extremidade SCIM em conformidade/users. O serviço de provisionamento do Azure AD enviará solicitações para desabilitar e excluir quando o usuário não precisar mais de acesso. Recomendamos dar suporte à desabilitação e à exclusão de usuários. Consulte os exemplos abaixo para saber a aparência de uma solicitação de desabilitação e exclusão. 

Desabilitar usuário
```json
PATCH /Users/5171a35d82074e068ce2 HTTP/1.1
{
    "Operations": [
        {
            "op": "Replace",
            "path": "active",
            "value": false
        }
    ],
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ]
}
```
Excluir usuário
```json
DELETE /Users/5171a35d82074e068ce2 HTTP/1.1
```

## <a name="scenario-3-automate-managing-group-memberships-in-my-app"></a>Cenário 3: automatizar o gerenciamento de associações de grupo em meu aplicativo
Meu aplicativo depende de grupos para acesso a vários recursos, e os clientes desejam reutilizar os grupos que têm no Azure AD. Como importar grupos do Azure AD e mantê-los atualizados à medida que as associações forem alteradas?  

**Recomendação:** Suporte a um ponto de [extremidade](https://aka.ms/scimreferencecode)/groups em conformidade com SCIM. O serviço de provisionamento do Azure AD cuidará da criação de grupos e do gerenciamento de atualizações de associação em seu aplicativo. 

## <a name="scenario-4-enrich-my-app-with-data-from-microsoft-services-such-as-teams-outlook-and-onedrive"></a>Cenário 4: enriquecer meu aplicativo com dados de serviços da Microsoft, como equipes, Outlook e OneDrive
Meu aplicativo é integrado ao Microsoft Teams e se baseia em dados de mensagens. Além disso, armazenamos arquivos para usuários no OneDrive. Como eu posso enriquecer meu aplicativo com os dados desses serviços e pela Microsoft?

**Recomendação:** O [Microsoft Graph](/graph/) é seu ponto de entrada para acessar os dados da Microsoft. Cada carga de trabalho expõe APIs com os dados de que você precisa. O Microsoft Graph pode ser usado junto com o [provisionamento do scim](./use-scim-to-provision-users-and-groups.md) para os cenários acima. Você pode usar o SCIM para provisionar atributos de usuário básicos em seu aplicativo ao chamar o Graph para obter quaisquer outros dados necessários. 

## <a name="scenario-5-track-changes-in-microsoft-services-such-as-teams-outlook-and-azure-ad"></a>Cenário 5: controlar alterações nos serviços da Microsoft, como equipes, Outlook e Azure AD
Preciso ser capaz de controlar as alterações nas equipes e nas mensagens do Outlook e reagir a elas em tempo real. Como posso obter essas alterações enviadas por push para meu aplicativo?

**Recomendação:** O Microsoft Graph fornece [notificações de alteração](/graph/webhooks) e [controle de alterações](/graph/delta-query-overview) para vários recursos. Observe as seguintes limitações de notificações de alteração:
- Se um receptor de eventos reconhecer um evento, mas falhar em agir por qualquer motivo, o evento poderá ser perdido.
- A ordem na qual as alterações são recebidas não tem garantia de ser cronológica.
- As notificações de alteração nem sempre contêm os [dados do recurso](/graph/webhooks-with-resource-data) pelos motivos acima, os desenvolvedores geralmente usam notificações de alteração junto com o controle de alterações para cenários de sincronização. 

## <a name="scenario-6-provision-users-and-groups-in-azure-ad"></a>Cenário 6: provisionar usuários e grupos no Azure AD
Meu aplicativo cria informações sobre um usuário que os clientes precisam no Azure AD. Esse pode ser um aplicativo de RH do que gerencia a contratação, um aplicativo de comunicações que cria números de telefone para usuários ou algum outro aplicativo que gera dados que seriam valiosos no Azure AD. Como fazer popular o registro de usuário no Azure AD com esses dados? 

**Recomendação** O Microsoft Graph expõe/Users e/groups pontos de extremidade que você pode integrar hoje para provisionar usuários no Azure AD. Observe que Azure Active Directory não dá suporte à gravação desses usuários de volta ao Active Directory. 

> [!NOTE]
> A Microsoft tem um serviço de provisionamento que efetua pull de dados de aplicativos de RH, como workday e SuccessFactors. Essas integrações são criadas e gerenciadas pela Microsoft. Para a integração de um novo aplicativo de RH ao nosso serviço, você pode solicitá-lo no [UserVoice](https://feedback.azure.com/forums/374982-azure-active-directory-application-requests). 

## <a name="related-articles"></a>Artigos relacionados

- [Examinar a documentação de Microsoft Graph de sincronização](/graph/api/resources/synchronization-overview?view=graph-rest-beta)
- [Integrando um aplicativo SCIM personalizado com o Azure AD](use-scim-to-provision-users-and-groups.md)