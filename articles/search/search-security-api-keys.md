---
title: Autenticação de chave de API
titleSuffix: Azure Cognitive Search
description: Uma chave de API controla o acesso de entrada ao ponto de extremidade de serviço. Chaves de administração concedem acesso de gravação. As chaves de consulta podem ser criadas para acesso somente leitura.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/03/2021
ms.openlocfilehash: 8b2e85744923fb2e7e474e049df1536aebc56f3c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99536926"
---
# <a name="create-and-manage-api-keys-for-an-azure-cognitive-search-service"></a>Criar e gerenciar chaves de API para um serviço de Pesquisa Cognitiva do Azure

Todas as solicitações para um serviço de pesquisa precisam de uma chave de API somente leitura que foi gerada especificamente para seu serviço. A chave de API é o único mecanismo para autenticar solicitações de entrada para o ponto de extremidade do serviço de pesquisa e é necessário em cada solicitação. 

+ Em [soluções REST](search-get-started-rest.md), o `api-key` normalmente é especificado em um cabeçalho de solicitação

+ Em [soluções .net](search-howto-dotnet-sdk.md), uma chave é geralmente especificada como uma definição de configuração e, em seguida, passada como um [AzureKeyCredential](/dotnet/api/azure.azurekeycredential)

Você pode exibir e gerenciar as chaves de API no [portal do Azure](https://portal.azure.com), ou por meio do [PowerShell](/powershell/module/az.search), do [CLI do Azure](/cli/azure/search)ou da [API REST](/rest/api/searchmanagement/).

:::image type="content" source="media/search-manage/azure-search-view-keys.png" alt-text="Página do portal, configurações de recuperação, seção de chaves" border="false":::

## <a name="what-is-an-api-key"></a>O que é uma chave de API?

Uma chave de API é uma cadeia de caracteres exclusiva composta de números e letras gerados aleatoriamente que é passada em cada solicitação para o serviço de pesquisa. O serviço aceitará a solicitação, se a solicitação em si e a chave forem válidas. 

Dois tipos de chaves são usados para acessar seu serviço de pesquisa: administrador (leitura-gravação) e consulta (somente leitura).

|Chave|Descrição|Limites|  
|---------|-----------------|------------|  
|Admin|Concede direitos totais para todas as operações, incluindo a capacidade de gerenciar o serviço, criar e excluir índices, indexadores e fontes de dados.<br /><br /> Duas chaves de API de administrador, chamadas de chaves *primária* e *secundária* no portal, são geradas quando o serviço é criado e podem ser regeneradas individualmente sob demanda. Ter duas chaves permite que você substitua uma chave enquanto usa a segunda chave de acesso contínuo para o serviço.<br /><br /> As chaves de administrador são especificadas somente nos cabeçalhos de solicitação HTTP. Não é possível inserir uma chave de API de administração em uma URL.|Máximo de dois por serviço|  
|Consulta|Concede acesso somente leitura a índices e documentos e normalmente são distribuídas para aplicativos cliente que emitem solicitações de pesquisa.<br /><br /> As chaves de consulta são criadas sob demanda.<br /><br /> As chaves de consulta podem ser especificadas em um cabeçalho de solicitação HTTP para pesquisa, sugestões ou operação de pesquisa. Como alternativa, você pode passar uma chave de consulta como um parâmetro em uma URL. Dependendo de como seu aplicativo cliente formula a solicitação, pode ser mais fácil passar a chave como um parâmetro de consulta:<br /><br /> `GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2020-06-30&api-key=[query key]`|50 por serviço|  

 Visualmente, não há nenhuma distinção entre as chaves de administrador ou de consulta. As duas chaves são cadeias de caracteres compostas de 32 caracteres alfanuméricos gerados aleatoriamente. Se você perder o controle de qual tipo de chave é especificado em seu aplicativo, poderá [verificar os valores de chave no portal](https://portal.azure.com).  

> [!NOTE]  
> É considerada uma prática de segurança inadequada para passar dados confidenciais, como um `api-key` no URI de solicitação. Por esse motivo, o Azure Pesquisa Cognitiva aceita apenas uma chave de consulta como uma `api-key` na cadeia de caracteres de consulta, e você deve evitar fazer isso, a menos que o conteúdo do índice esteja disponível publicamente. Em vez disso, recomendamos passar a `api-key` como um cabeçalho de solicitação.  

## <a name="find-existing-keys"></a>Localizar chaves existentes

Você pode obter chaves de acesso no portal ou por meio do [PowerShell](/powershell/module/az.search), [CLI do Azure](/cli/azure/search)ou [API REST](/rest/api/searchmanagement/).

1. Entre no [portal do Azure](https://portal.azure.com).
1. Liste os [serviços de pesquisa](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) para sua assinatura.
1. Selecione o serviço e, na página Visão geral, clique em **configurações**  > **chaves** para exibir as chaves de administrador e consulta.

   :::image type="content" source="media/search-security-overview/settings-keys.png" alt-text="Página do portal, configurações de exibição, seção chaves" border="false":::

## <a name="create-query-keys"></a>Criar chaves de consulta

As chaves de consulta são usadas para acesso somente leitura a documentos dentro de um índice para operações direcionadas a uma coleção de documentos. Consultas de pesquisa, filtro e sugestão são operações que usam uma chave de consulta. Qualquer operação somente leitura que retorna dados do sistema ou definições de objeto, como uma definição de índice ou um status de indexador, requer uma chave de administração.

Restringir o acesso e as operações em aplicativos cliente é essencial para proteger os ativos de pesquisa em seu serviço. Sempre use uma chave de consulta em vez de uma chave de administração para qualquer consulta proveniente de um aplicativo cliente.

1. Entre no [portal do Azure](https://portal.azure.com).
2. Liste os [serviços de pesquisa](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) para sua assinatura.
3. Selecione o serviço e, na página Visão geral, clique em **configurações**  > **chaves**.
4. Clique em **gerenciar chaves de consulta**.
5. Use a chave de consulta já gerada para seu serviço ou crie até 50 novas chaves de consulta. A chave de consulta padrão não é nomeada, mas chaves de consulta adicionais podem ser nomeadas para capacidade de gerenciamento.

   :::image type="content" source="media/search-security-overview/create-query-key.png" alt-text="Criar ou usar uma chave de consulta" border="false":::

> [!Note]
> Um exemplo de código que mostra o uso da chave de consulta pode ser encontrado em [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).

<a name="regenerate-admin-keys"></a>

## <a name="regenerate-admin-keys"></a>Regenerar chaves de administrador

Duas chaves de administração são criadas para cada serviço para que você possa girar uma chave primária usando a chave secundária para continuidade dos negócios.

1. Na página   > **chaves** de configurações, copie a chave secundária.
2. Para todos os aplicativos, atualize as configurações de chave de API para usar a chave secundária.
3. Regenere a chave primária.
4. Atualize todos os aplicativos para usar a nova chave primária.

Se você regenerar inadvertidamente as duas chaves ao mesmo tempo, todas as solicitações de cliente que usam essas chaves falharão com HTTP 403 Proibido. No entanto, o conteúdo não é excluído e você não está bloqueado permanentemente. 

Você ainda pode acessar o serviço por meio do portal ou programaticamente. As funções de gerenciamento estão funcionando por meio de uma ID de assinatura que não é uma chave de API de serviço e, assim, ainda ficam disponíveis mesmo que suas chaves de API não sejam. 

Depois de criar novas chaves por meio do portal ou da camada de gerenciamento, o acesso é restaurado para seu conteúdo (índices, indexadores, fontes de dados, mapas de sinônimos) quando você tem as novas chaves e fornece essas chaves em solicitações.

## <a name="secure-api-keys"></a>Proteger chaves de API

Por meio de [permissões baseadas em função](search-security-rbac.md), você pode excluir ou ler as chaves, mas não pode substituir uma chave por uma senha definida pelo usuário ou usar o Active Directory como a metodologia de autenticação primária para acessar operações de pesquisa. 

A chave de segurança é protegida restringindo o acesso por meio do portal ou interfaces do Gerenciador de Recursos (PowerShell ou interface de linha de comando). Como observado, os administradores de assinatura podem exibir e regenerar todas as chaves de API. Como precaução, revise as atribuições de função para entender quem tem acesso às chaves admin.

+ No painel de serviços, clique em **Controle de Acesso (IAM)** e, em seguida, na guia **Atribuições de função** para exibir as atribuições de função do serviço.

Membros das funções a seguir podem exibir e regenerar chaves: Proprietário, Colaborador, [Colaboradores do Serviço de Pesquisa](../role-based-access-control/built-in-roles.md#search-service-contributor)

## <a name="see-also"></a>Veja também

+ [Segurança no Azure Pesquisa Cognitiva](search-security-overview.md)
+ [Controle de acesso baseado em função do Azure no Azure Pesquisa Cognitiva](search-security-rbac.md)
+ [Gerenciar usando o PowerShell](search-manage-powershell.md) 