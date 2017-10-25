---
title: "Noções básicas sobre o manifesto de aplicativo do Azure Active Directory | Microsoft Docs"
description: "Cobertura detalhada do manifesto do aplicativo do Azure Active Directory, que representa a configuração de identidade de um aplicativo em um locatário do AD do Azure, e é usado para facilitar a autorização do OAuth, experiência de consentimento e muito mais."
services: active-directory
documentationcenter: 
author: sureshja
manager: mbaldwin
editor: 
ms.assetid: 4804f3d4-0ff1-4280-b663-f8f10d54d184
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/20/2017
ms.author: sureshja
ms.custom: aaddev
ms.reviewer: elisol
ms.openlocfilehash: d5e18f41d6eb69ccb7eafaa4de2646c4c38df5e2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="understanding-the-azure-active-directory-application-manifest"></a>Noções básicas sobre o manifesto de aplicativo do Active Directory do Azure
Os aplicativos que se integram com o Active Directory do Azure (AD) devem ser registrados em um locatário do Azure AD, fornecendo uma configuração persistente de identidade para o aplicativo. Essa configuração é consultada em tempo de execução, permitindo cenários que permitem que um aplicativo terceirize e agencie autenticação/autorização por meio do Azure AD. Para saber mais sobre o modelo de aplicativo Azure AD, consulte o artigo [Adição, atualização e remoção de um aplicativo][ADD-UPD-RMV-APP].

## <a name="updating-an-applications-identity-configuration"></a>Atualizando a configuração de identidade de um aplicativo
Na verdade, várias opções estão disponíveis para atualizar propriedades de configuração de identidade do aplicativo, que variam em recursos e graus de dificuldade, incluindo o seguinte:

* A **interface de usuário da Web do [Portal do Azure][AZURE-PORTAL]** permite que você atualize as propriedades mais comuns de um aplicativo. Essa é a maneira mais rápida e menos propensa a erro de atualizar as propriedades do aplicativo, mas não lhe dá acesso total a todas as propriedades como os dois métodos a seguir.
* Para cenários mais avançados em que é necessário atualizar as propriedades que não são expostas no portal clássico do Azure, você pode modificar o **manifesto do aplicativo**. Esse é o foco deste artigo e será discutido mais detalhadamente, começando na próxima seção.
* Também é possível **escrever um aplicativo que usa a [Graph API][GRAPH-API]** para atualizar seu aplicativo, o que requer maior esforço. Isso pode ser uma opção atraente se você estiver gravando o software de gerenciamento ou precisar atualizar as propriedades do aplicativo regularmente de forma automática.

## <a name="using-the-application-manifest-to-update-an-applications-identity-configuration"></a>Usando o manifesto do aplicativo para atualizar a configuração de identidade do aplicativo
Por meio do [Portal do Azure][AZURE-PORTAL], você pode gerenciar a configuração da identidade do seu aplicativo atualizando o manifesto do aplicativo usando o editor de manifesto embutido. Também é possível baixar e carregar o manifesto do aplicativo como um arquivo JSON. Nenhum arquivo real é armazenado no diretório. O manifesto do aplicativo é meramente uma operação HTTP GET na entidade Aplicativo do da API do Graph do Azure AD e o carregamento é uma operação HTTP PATCH na entidade Aplicativo.

Como resultado, para entender o formato e as propriedades do manifesto do aplicativo, você precisará consultar a documentação da [Entidade de aplicativo][APPLICATION-ENTITY] da API do Graph. Exemplos de atualizações que podem ser executadas através do carregamento do manifesto do aplicativo incluem:

* **Declarar escopos de permissão (oauth2Permissions)** expostos pela sua API Web. Veja o tópico "Expor APIs Web para outros aplicativos" em [Integração de aplicativos com o Azure Active Directory][INTEGRATING-APPLICATIONS-AAD] para saber mais sobre como implementar a representação de usuário usando o escopo de permissões delegadas do oauth2Permissions. Conforme mencionado anteriormente, todas as propriedades da entidade de aplicativo estão documentadas no artigo de referência [Entidade e Tipo Complexo][APPLICATION-ENTITY] da API do Graph, incluindo a propriedade oauth2Permissions, que é uma coleção do tipo [OAuth2Permission][APPLICATION-ENTITY-OAUTH2-PERMISSION].
* **Declarar funções de aplicativo (appRoles) expostas pelo seu aplicativo**. A propriedade appRoles da entidade Application é uma coleção do tipo [AppRole][APPLICATION-ENTITY-APP-ROLE]. Veja o artigo [Controle de acesso com base em função nos aplicativos em nuvem usando o Azure AD][RBAC-CLOUD-APPS-AZUREAD] para obter um exemplo de implementação.
* **Declarar aplicativos cliente conhecidos (knownClientApplications)**, que permitem vincular de modo lógico o consentimento dos aplicativos cliente especificados à API de recurso/Web.
* **Solicitar ao AD do Azure que emita uma declaração de associações de grupo** para o usuário conectado (groupMembershipClaims).  Isso também pode ser configurado para emitir declarações sobre as associações de funções de diretório do usuário. Consulte o artigo [Autorização em aplicativos na nuvem usando grupos do AD][AAD-GROUPS-FOR-AUTHORIZATION] para obter um exemplo de implementação.
* **Permitir que o aplicativo ofereça suporte aos fluxos de concessão implícitos do OAuth 2.0** (oauth2AllowImplicitFlow). Esse tipo de fluxo de concessão é usado com páginas da Web JavaScript ou SPA (Aplicativos de Única Página) inseridos. Para obter mais informações sobre a concessão de autorização implícita, consulte [Noções básicas sobre o fluxo de concessão implícita OAuth2 no Azure Active Directory][IMPLICIT-GRANT].
* **Habilitar o uso dos certificados X509 como chave secreta** (keyCredentials). Consulte os artigos [Compilar aplicativos de serviço e daemon no Office 365][O365-SERVICE-DAEMON-APPS] e [Guia do desenvolvedor para autenticação com a API do Azure Resource Manager][DEV-GUIDE-TO-AUTH-WITH-ARM] para ver exemplos de implementação.
* **Adicionar um novo URI de ID do Aplicativo** para seu aplicativo (identifierURIs[]). URIs de ID do aplicativo são usados para identificar com exclusividade um aplicativo em seu locatário do Azure AD (ou em vários locatários do Azure AD, para cenários de multilocatário quando qualificados por meio do domínio personalizado verificado). São usados ao serem solicitadas permissões para um aplicativo de recurso ou ser adquirido um token de acesso para um aplicativo de recurso. Quando você atualiza esse elemento, a mesma atualização é feita na coleção de servicePrincipalNames[] da entidade de serviço correspondente, que reside no locatário inicial do aplicativo.

O manifesto do aplicativo também fornece uma boa maneira de controlar o estado de seu registro de aplicativo. Porque ele está disponível no formato JSON, a representação de arquivo pode ser inserida no seu controle de origem, junto com o código-fonte do aplicativo.

## <a name="step-by-step-example"></a>Exemplo passo a passo
Agora, vamos percorrer as etapas necessárias para atualizar a configuração de identidade do aplicativo por meio do manifesto do aplicativo. Destacaremos um dos exemplos anteriores, mostrando como declarar um novo escopo de permissão em um aplicativo de recurso:

1. Entre no [Portal do Azure][AZURE-PORTAL].
2. Depois de autenticado, escolha o locatário do Azure AD selecionando-o no canto superior direito da página.
3. Escolha a extensão **Azure Active Directory** no painel de navegação esquerdo e clique em **Registros de Aplicativo**.
4. Localize o aplicativo que você quer atualizar na lista e clique nele.
5. Na página do aplicativo, clique em **Manifesto** para abrir o editor de manifesto embutido. 
6. Você pode editar diretamente o manifesto usando esse editor. Observe que o manifesto segue o esquema da [entidade Aplicativo][APPLICATION-ENTITY], como mencionamos anteriormente. Por exemplo, supondo que queremos implementar/expor uma nova permissão chamada "Employees.Read.All" em nosso aplicativo de recurso (API). Para isso, basta adicionar um elemento novo/secundário à coleção oauth2Permissions, isto é:
   
        "oauth2Permissions": [
        {
        "adminConsentDescription": "Allow the application to access MyWebApplication on behalf of the signed-in user.",
        "adminConsentDisplayName": "Access MyWebApplication",
        "id": "aade5b35-ea3e-481c-b38d-cba4c78682a0",
        "isEnabled": true,
        "type": "User",
        "userConsentDescription": "Allow the application to access MyWebApplication on your behalf.",
        "userConsentDisplayName": "Access MyWebApplication",
        "value": "user_impersonation"
        },
        {
        "adminConsentDescription": "Allow the application to have read-only access to all Employee data.",
        "adminConsentDisplayName": "Read-only access to Employee records",
        "id": "2b351394-d7a7-4a84-841e-08a6a17e4cb8",
        "isEnabled": true,
        "type": "User",
        "userConsentDescription": "Allow the application to have read-only access to your Employee data.",
        "userConsentDisplayName": "Read-only access to your Employee records",
        "value": "Employees.Read.All"
        }
        ],
   
    A entrada deve ser exclusiva e, portanto, você deve gerar um novo GUID (identificador global exclusivo) para a propriedade `"id"` . Nesse caso, como especificamos `"type": "User"`, essa permissão pode ser consentida por qualquer conta autenticada pelo locatário do AD do Azure no qual o aplicativo de recurso/API está registrado. Isso concede ao aplicativo cliente a permissão para acessá-lo em nome da conta. As cadeias de caracteres de descrição e nome de exibição são usadas durante o consentimento e para exibição no Portal do Azure.
6. Quando terminar de atualizar o manifesto, clique em **Salvar** para salvar o manifesto.  
   
Agora que o manifesto está salvo, você pode dar acesso a um aplicativo cliente registrado à a nova permissão que adicionamos acima. Neste momento, você pode usar a interface de usuário da Web do Portal do Azure em vez de editar o manifesto do aplicativo cliente:  

1. Primeiramente, vá para a folha **Configurações** do aplicativo cliente ao qual você quer adicionar acesso à nova API, clique em **Permissões Necessárias** e escolha **Selecionar uma API**.
2. Em seguida, você verá uma lista de aplicativos de recurso registrados (APIs) no locatário. Clique no aplicativo de recurso para selecioná-lo ou digite o nome do aplicativo na caixa de pesquisa. Quando tiver encontrado o aplicativo, clique em **Selecionar**.  
3. Isso redirecionará você para a página **Selecionar Permissões**, que mostrará a lista de Permissões do Aplicativo e Permissões Delegadas disponíveis para o aplicativo de recurso. Selecione a nova permissão para adicioná-la à lista de permissões solicitadas do cliente. Essa nova permissão será armazenada na configuração da identidade do aplicativo cliente, na propriedade da coleção "requiredResourceAccess".


É isso. Agora seus aplicativos serão executados usando a nova configuração de identidade.

## <a name="next-steps"></a>Próximas etapas
* Para obter mais detalhes sobre a relação entre os objetos Entidade de Serviço e Aplicativo do aplicativo, veja [Objetos Aplicativo e Entidade de Serviço no Azure AD][AAD-APP-OBJECTS].
* Veja as definições do [Glossário de desenvolvedor do Azure AD][AAD-DEVELOPER-GLOSSARY] de alguns dos conceitos de desenvolvedor do Azure Active Directory (AD).

Use a seção de comentários abaixo para nos dar sua opinião e ajudar a refinar e moldar nosso conteúdo.

<!--article references -->
[AAD-APP-OBJECTS]: active-directory-application-objects.md
[AAD-DEVELOPER-GLOSSARY]: active-directory-dev-glossary.md
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]: active-directory-integrating-applications.md
[APPLICATION-ENTITY]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[APPLICATION-ENTITY-APP-ROLE]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type
[APPLICATION-ENTITY-OAUTH2-PERMISSION]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type
[AZURE-PORTAL]: https://portal.azure.com
[DEV-GUIDE-TO-AUTH-WITH-ARM]: http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/
[GRAPH-API]: active-directory-graph-api.md
[IMPLICIT-GRANT]: active-directory-dev-understanding-oauth2-implicit-grant.md
[INTEGRATING-APPLICATIONS-AAD]: https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
[O365-PERM-DETAILS]: https://msdn.microsoft.com/office/office365/HowTo/application-manifest
[O365-SERVICE-DAEMON-APPS]: https://msdn.microsoft.com/office/office365/howto/building-service-apps-in-office-365
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/

