---
title: Filtros de segurança para cortar os resultados usando Active Directory
titleSuffix: Azure Cognitive Search
description: Saiba como implementar privilégios de segurança no nível do documento para os resultados do Azure Pesquisa Cognitiva Search, usando os filtros de segurança e as identidades do Azure Active Directory (AD).
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/16/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 5788585b2365b12a90a508e5a972b61f73e48c15
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97629503"
---
# <a name="security-filters-for-trimming-azure-cognitive-search-results-using-active-directory-identities"></a>Filtros de segurança para aparar os resultados de Pesquisa Cognitiva do Azure usando identidades Active Directory

Este artigo demonstra como usar identidades de segurança do Azure Active Directory (AD) junto com filtros no Azure Pesquisa Cognitiva para cortar os resultados da pesquisa com base na associação do grupo de usuários.

Este artigo aborda as seguintes tarefas:
> [!div class="checklist"]
> - Criar grupos e usuários do Azure AD
> - Associar o usuário ao grupo que você criou
> - Cache dos novos grupos
> - Indexar documentos com grupos associados
> - Emitir uma solicitação de pesquisa com filtro de identificadores de grupo
> 
> [!NOTE]
> Os snippets de código de exemplo neste artigo são escritos em C#. Você pode encontrar o código-fonte completo [no GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started). 

## <a name="prerequisites"></a>Pré-requisitos

Seu índice no Azure Pesquisa Cognitiva deve ter um [campo de segurança](search-security-trimming-for-azure-search.md) para armazenar a lista de identidades de grupo com acesso de leitura ao documento. Esse caso de uso pressupõe uma correspondência individual entre um item protegível (como um aplicativo de admissão de faculdade de um indivíduo) e um campo de segurança especificando quem tem acesso a esse item (equipe de admissão).

Você deve ter permissões de administrador do Azure AD, necessárias neste passo a passos para criar usuários, grupos e associações. 

Seu aplicativo também deve ser registrado com o Azure AD como um aplicativo multilocatário, conforme descrito no procedimento a seguir.

### <a name="register-your-application-with-azure-active-directory"></a>Registrar seu aplicativo no Azure Active Directory

Esta etapa integra seu aplicativo ao Azure AD com a finalidade de aceitar entradas de contas de usuário e de grupo. Se você não for um administrador de locatário em sua organização, talvez seja necessário [criar um novo locatário](../active-directory/develop/quickstart-create-new-tenant.md) para executar as etapas a seguir.

1. Em [portal do Azure](https://portal.azure.com), localize o recurso de Azure Active Directory para sua assinatura.

1. À esquerda, em **gerenciar**, selecione **registros de aplicativo** e, em seguida, selecione **novo registro**.

1. Dê um nome ao registro, talvez um nome semelhante ao nome do aplicativo de pesquisa. Selecione **Registrar**.

1. Depois que o registro do aplicativo for criado, copie a ID do aplicativo. Você precisará fornecer essa cadeia de caracteres para seu aplicativo.

   Se você estiver percorrendo o [DotNetHowToSecurityTrimming](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK), Cole esse valor no arquivo **app.config** .

   Repita para a ID do locatário.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-application-id.png" alt-text="ID do aplicativo na seção Essentials":::

1. À esquerda, selecione **permissões de API** e, em seguida, selecione **Adicionar uma permissão**. 

1. Selecione **Microsoft Graph** e, em seguida, selecione **permissões delegadas**.

1. Pesquise e adicione as seguintes permissões delegadas:

   - **Directory.ReadWrite.All**
   - **Group.ReadWrite.All**
   - **User.ReadWrite.All**

Microsoft Graph fornece uma API que permite o acesso programático ao Azure AD por meio de uma API REST. O exemplo de código para este passo a passo usa as permissões para chamar a API do Microsoft Graph para a criação de grupos, usuários e associações. As APIs também são usadas para o cache de identificadores de grupo para uma filtragem mais rápida.

## <a name="create-users-and-groups"></a>Criar usuários e grupos

Se você estiver adicionando a pesquisa a um aplicativo estabelecido, você poderá ter identificadores de usuário e grupo existentes no Azure AD. Nesse caso, você pode ignorar as próximas três etapas. 

No entanto, se você não tiver os usuários existentes, você pode usar as APIs do Microsoft Graph para criar entidades de segurança. Os trechos de código a seguir demonstram como gerar identificadores, que se tornam valores de dados para o campo de segurança no índice de Pesquisa Cognitiva do Azure. Em nosso hipotético aplicativo de admissão de faculdade, isso seria os identificadores de segurança para a equipe de admissão.

A associação de grupo e de usuário pode ser muito flexível, especialmente em organizações de grandes porte. O código que cria as identidades de usuário e de grupo deve ser executado com frequência suficiente para acompanhar as alterações na associação da organização. Da mesma forma, seu índice de Pesquisa Cognitiva do Azure requer uma agenda de atualização semelhante para refletir o status atual de usuários e recursos permitidos.

### <a name="step-1-create-group"></a>Etapa 1: [Criar grupo](/graph/api/group-post-groups) 

```csharp
private static Dictionary<Group, List<User>> CreateGroupsWithUsers(string tenant)
{
    Group group = new Group()
    {
        DisplayName = "My First Prog Group",
        SecurityEnabled = true,
        MailEnabled = false,
        MailNickname = "group1"
    };
```

### <a name="step-2-create-user"></a>Etapa 2: [criar usuário](/graph/api/user-post-users)

```csharp
User user1 = new User()
{
    GivenName = "First User",
    Surname = "User1",
    MailNickname = "User1",
    DisplayName = "First User",
    UserPrincipalName = String.Format("user1@{0}", tenant),
    PasswordProfile = new PasswordProfile() { Password = "********" },
    AccountEnabled = true
};
```

### <a name="step-3-associate-user-and-group"></a>Etapa 3: Associar o usuário e o grupo

```csharp
List<User> users = new List<User>() { user1, user2 };
Dictionary<Group, List<User>> groups = new Dictionary<Group, List<User>>() { { group, users } };
```

### <a name="step-4-cache-the-groups-identifiers"></a>Etapa 4: Armazenar em Cache os identificadores de grupos

Opcionalmente, para reduzir a latência de rede, você pode armazenar em cache as associações do grupo de usuários para que, quando uma solicitação de pesquisa for emitida, os grupos sejam retornados do cache, salvando uma viagem de ida e volta ao Azure AD. Você pode usar a [API do lote do Azure ad](/graph/json-batching) para enviar uma única solicitação HTTP com vários usuários e criar o cache.

O Microsoft Graph foi projetado para lidar com um grande volume de solicitações. Caso haja um número excessivo de solicitações, o Microsoft Graph apresenta uma falha na solicitação com o código de status HTTP 429. Para saber mais, veja [Limitação de mecanismo do Microsoft Graph](/graph/throttling).

## <a name="index-document-with-their-permitted-groups"></a>Documento do índice com seus grupos permitidos

As operações de consulta no Azure Pesquisa Cognitiva são executadas em um índice de Pesquisa Cognitiva do Azure. Nesta etapa, uma operação de indexação importa dados pesquisáveis em um índice, incluindo os identificadores usados como filtros de segurança. 

O Azure Pesquisa Cognitiva não autentica identidades de usuário ou fornece lógica para estabelecer qual conteúdo um usuário tem permissão para exibir. O caso de uso para as restrições de segurança pressupõe que você forneça a associação entre um documento confidencial e o identificador do grupo que tem acesso a esse documento, importado intacto a um índice de pesquisa. 

No exemplo hipotético, o corpo da solicitação PUT em um índice de Pesquisa Cognitiva do Azure incluiria uma dissertação de faculdade ou transcrição do candidato junto com o identificador de grupo com permissão para exibir esse conteúdo. 

No exemplo genérico usado na amostra de código para este passo a passo, a ação de índice será semelhante ao seguinte:

```csharp
private static void IndexDocuments(string indexName, List<string> groups)
{
    IndexDocumentsBatch<SecuredFiles> batch = IndexDocumentsBatch.Create(
        IndexDocumentsAction.Upload(
            new SecuredFiles()
            {
                FileId = "1",
                Name = "secured_file_a",
                GroupIds = new[] { groups[0] }
            }),
              ...
            };

IndexDocumentsResult result = searchClient.IndexDocuments(batch);
```

## <a name="issue-a-search-request"></a>Emitir uma solicitação de pesquisa

Para fins de restrições de segurança, os valores no seu campo de segurança no índice são valores estáticos usados para incluir ou excluir documentos nos resultados da pesquisa. Por exemplo, se o identificador de grupo para admissões for "A11B22C33D44-E55F66G77-H88I99JKK", todos os documentos em um índice de Pesquisa Cognitiva do Azure com esse identificador no arquivamento de segurança serão incluídos (ou excluídos) nos resultados da pesquisa enviados de volta ao solicitante.

Para filtrar os documentos retornados nos resultados da pesquisa com base nos grupos de usuários que emitirem a solicitação, analise as etapas a seguir.

### <a name="step-1-retrieve-users-group-identifiers"></a>Etapa 1: Recuperar os identificadores de grupo do usuário

Se os grupos do usuário ainda não tiverem sido armazenados em cache ou se o cache tiver expirado, emita a solicitação de [grupos](/graph/api/directoryobject-getmembergroups) .

```csharp
private static async void RefreshCache(IEnumerable<User> users)
{
    HttpClient client = new HttpClient();
    var userGroups = await _microsoftGraphHelper.GetGroupsForUsers(client, users);
    _groupsCache = new ConcurrentDictionary<string, List<string>>(userGroups);
}
```

### <a name="step-2-compose-the-search-request"></a>Etapa 2: Criar a solicitação de pesquisa

Supondo que você tenha associação de grupos do usuário, você pode emitir a solicitação de pesquisa com os valores de filtro apropriados.

```csharp
private static void SearchQueryWithFilter(string user)
{
    // Using the filter below, the search result will contain all documents that their GroupIds field   
    // contain any one of the Ids in the groups list
    string filter = String.Format("groupIds/any(p:search.in(p, '{0}'))", string.Join(",", String.Join(",", _groupsCache[user])));
    SearchOptions searchOptions =
        new SearchOptions()
        {
            Filter = filter
        };
    searchOptions.Select.Add("name");

    SearchResults<SecuredFiles> results = searchClient.Search<SecuredFiles>("*", searchOptions);

    Console.WriteLine("Results for groups '{0}' : {1}", _groupsCache[user], results.GetResults().Select(r => r.Document.Name));
}
```

### <a name="step-3-handle-the-results"></a>Etapa 3: Lidar com os resultados

A resposta inclui uma lista filtrada de documentos, que consiste naqueles que o usuário tem permissão para visualizar. Dependendo de como você criar a página de resultados da pesquisa, você talvez queira incluir dicas visuais para refletir o conjunto de resultados filtrados.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu um padrão para usar entradas do Azure AD para filtrar documentos nos resultados de Pesquisa Cognitiva do Azure, aparando os resultados de documentos que não correspondem ao filtro fornecido na solicitação. Para obter um padrão alternativo que possa ser mais simples, ou para revisitar outros recursos de segurança, consulte os links a seguir.

- [Filtros de segurança para os resultados de corte](search-security-trimming-for-azure-search.md)
- [Segurança no Azure Pesquisa Cognitiva](search-security-overview.md)