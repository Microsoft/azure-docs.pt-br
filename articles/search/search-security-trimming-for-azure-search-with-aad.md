---
title: Filtros de segurança para filtragem de resultados usando o Active Directory - Azure Search
description: Controle de acesso sobre o conteúdo do Azure Search usando filtros de segurança e identidades do AAD (Azure Active Directory).
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.topic: conceptual
ms.date: 11/07/2017
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: 410727022b092e2dd8ab8b05e628e25fd60ab833
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61282197"
---
# <a name="security-filters-for-trimming-azure-search-results-using-active-directory-identities"></a>Filtros de segurança para a restrição dos resultados do Azure Search usando identidades do Active Directory

Este artigo mostra como usar as identidades de segurança do Azure Active Directory (AAD) junto com os filtros no Azure Search para restringir o acesso a documentos com base na associação de grupo do usuário.

Este artigo aborda as seguintes tarefas:
> [!div class="checklist"]
> - Criar usuários e grupos do AAD
> - Associar o usuário ao grupo que você criou
> - Cache dos novos grupos
> - Indexar documentos com grupos associados
> - Emitir uma solicitação de pesquisa com filtro de identificadores de grupo
> 
> [!NOTE]
> Os snippets de código de exemplo neste artigo são escritos em C#. Você pode encontrar o código-fonte completo [no GitHub](https://aka.ms/search-dotnet-howto). 

## <a name="prerequisites"></a>Pré-requisitos

O índice no Azure Search deve ter um [campo segurança](search-security-trimming-for-azure-search.md) para armazenar a lista de identidades de grupo que têm acesso de leitura ao documento. Esse caso de uso pressupõe uma correspondência individual entre um item protegível (como um aplicativo de admissão de faculdade de um indivíduo) e um campo de segurança especificando quem tem acesso a esse item (equipe de admissão).

Você deve ter permissões de administrador do AAD, necessárias neste passo a passo para a criação de usuários, grupos e associações no AAD.

O aplicativo também deve ser registrado com o AAD, conforme descrito no procedimento a seguir.

### <a name="register-your-application-with-aad"></a>Registre seu aplicativo com o AAD

Essa etapa integra o seu aplicativo com o AAD com a finalidade de aceitação de entradas de usuário e contas de grupo. Se você não for um administrador do AAD na sua organização, você pode precisar [criar um novo locatário](https://docs.microsoft.com/azure/active-directory/develop/active-directory-howto-tenant) para executar as etapas a seguir.

1. Acesse o [**Portal de Registro do Aplicativo**](https://apps.dev.microsoft.com) >  **Aplicativo convergente** > **Adicionar um aplicativo**.
2. Insira o nome para o seu aplicativo e clique em **Criar**. 
3. Selecione seu aplicativo recentemente registrado na página Meus aplicativos.
4. Na página de registro do aplicativo > **Plataformas** > **Adicionar plataforma**, escolha **API da Web**.
5. Ainda na página de registro do aplicativo, acesse > **Permissões do Microsoft Graph** > **Adicionar**.
6. Em Selecionar Permissões, adicione as seguintes permissões delegadas e, em seguida, clique em **Ok**:

   + **Directory.ReadWrite.All**
   + **Group.ReadWrite.All**
   + **User.ReadWrite.All**

O Microsoft Graph fornece uma API que permite o acesso programático ao AAD por meio de uma REST API. O exemplo de código para este passo a passo usa as permissões para chamar a API do Microsoft Graph para a criação de grupos, usuários e associações. As APIs também são usadas para o cache de identificadores de grupo para uma filtragem mais rápida.

## <a name="create-users-and-groups"></a>Criar usuários e grupos

Se você estiver adicionando pesquisa a um aplicativo estabelecido, você pode ter um usuário existente e identificadores de grupo no AAD. Nesse caso, você pode ignorar as próximas três etapas. 

No entanto, se você não tiver os usuários existentes, você pode usar as APIs do Microsoft Graph para criar entidades de segurança. Os snippets de código a seguir demonstram como gerar identificadores que se tornam os valores de dados para o campo de segurança em seu índice do Azure Search. Em nosso hipotético aplicativo de admissão de faculdade, isso seria os identificadores de segurança para a equipe de admissão.

A associação de grupo e de usuário pode ser muito flexível, especialmente em organizações de grandes porte. O código que cria as identidades de usuário e de grupo deve ser executado com frequência suficiente para acompanhar as alterações na associação da organização. Da mesma forma, o seu índice do Azure Search requer uma agenda de atualização semelhante para refletir o status atual de recursos e usuários permitidos.

### <a name="step-1-create-aad-grouphttpsdocsmicrosoftcomgraphapigroup-post-groupsviewgraph-rest-10"></a>Etapa 1: Criar [Grupo do AAD](https://docs.microsoft.com/graph/api/group-post-groups?view=graph-rest-1.0) 
```csharp
// Instantiate graph client 
GraphServiceClient graph = new GraphServiceClient(new DelegateAuthenticationProvider(...));
Group group = new Group()
{
    DisplayName = "My First Prog Group",
    SecurityEnabled = true,
    MailEnabled = false,
    MailNickname = "group1"
}; 
Group newGroup = await graph.Groups.Request().AddAsync(group);
```
   
### <a name="step-2-create-aad-userhttpsdocsmicrosoftcomgraphapiuser-post-usersviewgraph-rest-10"></a>Etapa 2: Criar [Usuário do AAD](https://docs.microsoft.com/graph/api/user-post-users?view=graph-rest-1.0)
```csharp
User user = new User()
{
    GivenName = "First User",
    Surname = "User1",
    MailNickname = "User1",
    DisplayName = "First User",
    UserPrincipalName = "User1@FirstUser.com",
    PasswordProfile = new PasswordProfile() { Password = "********" },
    AccountEnabled = true
};
User newUser = await graph.Users.Request().AddAsync(user);
```

### <a name="step-3-associate-user-and-group"></a>Etapa 3: Associar usuário e grupo
```csharp
await graph.Groups[newGroup.Id].Members.References.Request().AddAsync(newUser);
```

### <a name="step-4-cache-the-groups-identifiers"></a>Etapa 4: Armazenar em cache os identificadores de grupo
Opcionalmente, para reduzir a latência de rede, você pode armazenar em cache as associações de grupo de usuários para que quando uma solicitação de pesquisa for emitida, os grupos sejam retornados do cache, economizando uma ida e volta ao AAD. Você pode usar [AAD Batch API](https://developer.microsoft.com/graph/docs/concepts/json_batching) para enviar uma única solicitação HTTP com vários usuários e criar o cache.

O Microsoft Graph foi projetado para lidar com um grande volume de solicitações. Caso haja um número excessivo de solicitações, o Microsoft Graph apresenta uma falha na solicitação com o código de status HTTP 429. Para saber mais, veja [Limitação de mecanismo do Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/throttling).

## <a name="index-document-with-their-permitted-groups"></a>Documento do índice com seus grupos permitidos

As operações de consulta no Azure Search são executadas em um índice do Azure Search. Nesta etapa, uma operação de indexação importa dados pesquisáveis em um índice, incluindo os identificadores usados como filtros de segurança. 

O Azure Search não autentica identidades de usuário ou fornece lógica para estabelecer que conteúdo um usuário tem permissão para visualizar. O caso de uso para as restrições de segurança pressupõe que você forneça a associação entre um documento confidencial e o identificador do grupo que tem acesso a esse documento, importado intacto a um índice de pesquisa. 

No exemplo hipotético, o corpo da solicitação PUT em um índice do Azure Search incluiria um ensaio de faculdade de um candidato ou transcrição junto com o identificador do grupo que tem permissão para visualizar esse conteúdo. 

No exemplo genérico usado na amostra de código para este passo a passo, a ação de índice será semelhante ao seguinte:

```csharp
var actions = new IndexAction<SecuredFiles>[]
              {
                  IndexAction.Upload(
                  new SecuredFiles()
                  {
                      FileId = "1",
                      Name = "secured_file_a",
                      GroupIds = new[] { groups[0] }
                  }),
              ...
             };

var batch = IndexBatch.New(actions);

_indexClient.Documents.Index(batch);  
```

## <a name="issue-a-search-request"></a>Emitir uma solicitação de pesquisa

Para fins de restrições de segurança, os valores no seu campo de segurança no índice são valores estáticos usados para incluir ou excluir documentos nos resultados da pesquisa. Por exemplo, se o identificador de grupo para Admissões for "A11B22C33D44-E55F66G77-H88I99JKK", todos os documentos em um índice do Azure Search com esse identificador no campo de segurança serão incluídos (ou excluídos) nos resultados da pesquisa enviados de volta ao solicitante.

Para filtrar os documentos retornados nos resultados da pesquisa com base nos grupos de usuários que emitirem a solicitação, analise as etapas a seguir.

### <a name="step-1-retrieve-users-group-identifiers"></a>Etapa 1: Recuperar os identificadores de grupo do usuário

Se os grupos do usuário já não estavam em cache ou se o cache tiver expirado, emita a solicitação ade [grupos](https://docs.microsoft.com/graph/api/directoryobject-getmembergroups?view=graph-rest-1.0)
```csharp
private static void RefreshCacheIfRequired(string user)
{
    if (!_groupsCache.ContainsKey(user))
    {
        var groups = GetGroupIdsForUser(user).Result;
        _groupsCache[user] = groups;
    }
}

private static async Task<List<string>> GetGroupIdsForUser(string userPrincipalName)
{
    List<string> groups = new List<string>();
    var allUserGroupsRequest = graph.Users[userPrincipalName].GetMemberGroups(true).Request();

    while (allUserGroupsRequest != null) 
    {
        IDirectoryObjectGetMemberGroupsRequestBuilder allUserGroups = await allUserGroupsRequest.PostAsync();
        groups = allUserGroups.ToList();
        allUserGroupsRequest = allUserGroups.NextPageRequest;
    }
    return groups;
}
``` 

### <a name="step-2-compose-the-search-request"></a>Etapa 2: Redigir a solicitação de pesquisa

Supondo que você tenha associação de grupos do usuário, você pode emitir a solicitação de pesquisa com os valores de filtro apropriados.

```csharp
string filter = String.Format("groupIds/any(p:search.in(p, '{0}'))", string.Join(",", groups.Select(g => g.ToString())));
SearchParameters parameters = new SearchParameters()
             {
                 Filter = filter,
                 Select = new[] { "application essays" }
             };

DocumentSearchResult<SecuredFiles> results = _indexClient.Documents.Search<SecuredFiles>("*", parameters);
```
### <a name="step-3-handle-the-results"></a>Etapa 3: Manipular os resultados

A resposta inclui uma lista filtrada de documentos, que consiste naqueles que o usuário tem permissão para visualizar. Dependendo de como você criar a página de resultados da pesquisa, você talvez queira incluir dicas visuais para refletir o conjunto de resultados filtrados.

## <a name="conclusion"></a>Conclusão

Neste passo a passo, você aprendeu técnicas para usar logons do AAD para filtrar documentos nos resultados do Azure Search, restringir os resultados de documentos que não coincidem com o filtro fornecido na solicitação.

## <a name="see-also"></a>Consulte também

+ [Controle de acesso baseado na identidade usando filtros do Azure Search](search-security-trimming-for-azure-search.md)
+ [Filtros no Azure Search](search-filters.md)
+ [Controle de acesso e segurança de dados no Azure Search](search-security-overview.md)
