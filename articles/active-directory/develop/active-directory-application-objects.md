---
title: "Objetos de Entidade de Serviço e Aplicativo do Azure Active Directory"
description: "Uma discussão sobre a relação entre objetos de aplicativo e de entidade de serviço no Azure Active Directory"
documentationcenter: dev-center-name
author: bryanla
manager: mbaldwin
services: active-directory
editor: 
ms.assetid: adfc0569-dc91-48fe-92c3-b5b4833703de
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/26/2017
ms.author: bryanla
ms.custom: aaddev
ms.openlocfilehash: 53ab4c04901994982b451149c4a82a5b72c9fc82
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="application-and-service-principal-objects-in-azure-active-directory-azure-ad"></a>Objetos de entidade de serviço e aplicativo no Azure Active Directory (Azure AD)
Às vezes, o significado do termo "aplicativo" pode ser interpretado de forma errada quando usado no contexto do Azure AD. A meta deste artigo é esclarecer os aspectos conceituais e concretos de integração de aplicativos do Azure AD, seguidos de uma ilustração de registro e consentimento para um [aplicativo multilocatário](active-directory-dev-glossary.md#multi-tenant-application).

## <a name="overview"></a>Visão geral
Um aplicativo que foi integrado ao Azure AD tem implicações que ultrapassam o aspecto de software. "Aplicativo" é frequentemente usado como um termo conceitual, referindo-se não apenas ao software de aplicativo, mas também ao seu registro e função no Azure AD nas "conversas" de autenticação/autorização em tempo de execução. Por definição, um aplicativo pode funcionar em uma função de [cliente](active-directory-dev-glossary.md#client-application) (consumindo um recurso), uma função de [servidor de recursos](active-directory-dev-glossary.md#resource-server) (expondo APIs a clientes) ou ambos. O protocolo de conversa é definido por um [fluxo de Concessão de Autorização OAuth 2.0](active-directory-dev-glossary.md#authorization-grant), permitindo que o cliente/recurso acesse/proteja dados de um recurso, respectivamente. Agora, vamos nos aprofundar e ver como o modelo de aplicativo do Azure AD representa um aplicativo no tempo de design e no tempo de execução. 

## <a name="application-registration"></a>Registro de aplicativo
Quando você registra um aplicativo do Azure AD no [Portal do Azure][AZURE-Portal], dois objetos são criados no locatário do Azure AD: um objeto de aplicativo e um objeto de entidade de serviço.

#### <a name="application-object"></a>Objeto de aplicativo
Um aplicativo do Azure AD é definido por seu único objeto de aplicativo, que reside no locatário do Azure AD em que o aplicativo foi registrado, sendo conhecido como o locatário "inicial" do aplicativo. A [entidade de Aplicativo][AAD-Graph-App-Entity] do Azure AD Graph define o esquema para as propriedades de um objeto de aplicativo. 

#### <a name="service-principal-object"></a>Objeto de entidade de serviço
O objeto de entidade de serviço define a política e as permissões para o uso de um aplicativo em um locatário específico, fornecendo a base para que uma entidade de segurança represente o aplicativo em tempo de execução. A [entidade ServicePrincipal][AAD-Graph-Sp-Entity] do Azure AD Graph define o esquema para as propriedades do objeto de uma entidade de serviço. 

#### <a name="application-and-service-principal-relationship"></a>Relação do aplicativo e a entidade de serviço
Você pode considerar o aplicativo como a representação *global* de seu aplicativo para uso em todos os locatários, e a entidade de serviço como a representação *local* para uso em um locatário específico. O objeto de aplicativo serve como o modelo do qual as propriedades comuns e padrão são *derivadas* para uso na criação de objetos de entidade de serviço correspondentes. Portanto, um objeto de aplicativo tem uma relação de um para um com o aplicativo de software e uma relação de um para muitos com seus objetos de entidade de serviço correspondentes.

Uma entidade de serviço deve ser criada em cada locatário no qual o aplicativo é usado, permitindo o estabelecimento de uma identidade para entrada e/ou acesso aos recursos que estão sendo protegidos pelo locatário. Um aplicativo de locatário único tem apenas uma entidade de serviço (em seu locatário inicial), criado e com consentimento para uso durante o registro do aplicativo. Uma API/aplicativo Web multilocatário também tem uma entidade de serviço criada em cada locatário no qual um usuário consentiu com o seu uso.  

> [!NOTE]
> As alterações feitas no objeto de aplicativo também são refletidas apenas no objeto de entidade de serviço do locatário inicial do aplicativo (o locatário em que ele foi registrado). Para aplicativos multilocatário, as alterações no objeto do aplicativo não serão refletidas em objetos de entidade de serviço dos locatários de qualquer consumidor até que o acesso seja removido por meio do [Painel de Acesso do Aplicativo](https://myapps.microsoft.com) e concedido novamente.
><br>  
> Observe também que os aplicativos nativos são registrados como multilocatários, por padrão.
> 
> 

## <a name="example"></a>Exemplo
O diagrama a seguir ilustra o relacionamento entre o objeto de aplicativo de um aplicativo e os objetos de entidade de serviço correspondentes, no contexto de um aplicativo multilocatário de exemplo chamado **aplicativo de RH**. Há três locatários do Azure AD nesse cenário: 

* **Adatum** - o locatário usado pela empresa que desenvolveu o **aplicativo de HR**
* **Contoso** - locatário utilizado pela empresa Contoso, que é consumidora do **aplicativo de HR**
* **Fabrikam** - o locatário usado pela organização Fabrikam, que também consome o **aplicativo de HR**

![Relação entre um objeto de aplicativo e um objeto de entidade de serviço](./media/active-directory-application-objects/application-objects-relationship.png)

No diagrama acima, a Etapa 1 é o processo de criação do aplicativo e dos objetos de entidade de serviço no locatário inicial do aplicativo.

Na Etapa 2, quando os administradores da Contoso e da Fabrikam concluem o consentimento, um objeto de entidade de serviço é criado no locatário do Azure AD da empresa e recebe as permissões concedidas pelo administrador. Observe também que o aplicativo de RH pode ser configurado/projetado para permitir o consentimento pelos usuários para uso individual.

Na etapa 3, cada um dos locatários do consumidor do aplicativo de RH (Contoso e Fabrikam) tem seu próprio objeto de entidade de serviço. Cada um deles representa o uso de uma instância do aplicativo em tempo de execução, controlado pelas permissões concedidas pelo respectivo administrador.

## <a name="next-steps"></a>Próximas etapas
Um objeto de aplicativo do aplicativo pode ser acessado por meio da API do Graph do Azure AD, do editor de manifesto do aplicativo do [Portal do Azure][AZURE-Portal] ou dos [cmdlets do Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azureadps-2.0) conforme representado por sua [Entidade de aplicativo][AAD-Graph-App-Entity] do OData.

Um objeto de entidade de serviço do aplicativo pode ser acessado por meio da API do Graph do Azure AD ou dos [cmdlets do Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azureadps-2.0), conforme representado por sua [entidade ServicePrincipal][AAD-Graph-Sp-Entity] do OData.

O [Azure AD Graph Explorer](https://graphexplorer.azurewebsites.net/) é útil para consultar o aplicativo e os objetos de entidade de serviço.

<!--Image references-->

<!--Reference style links -->
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AZURE-Portal]: https://portal.azure.com
