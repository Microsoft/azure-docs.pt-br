---
title: "Guia de Início Rápido para a API do Azure AD Graph | Microsoft Docs"
description: "A Graph API do Active Directory do Azure fornece acesso programático ao AD do Azure por meio de pontos de extremidade da API REST OData. Os aplicativos podem usar a Graph API para realizar operações de criação, leitura, atualização e exclusão (CRUD) nos objetos e dados do diretório."
services: active-directory
documentationcenter: n/a
author: viv-liu
manager: mbaldwin
editor: 
tags: 
ms.assetid: 9dc268a9-32e8-402c-a43f-02b183c295c5
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/28/2017
ms.author: viviali
ms.custom: aaddev
ms.openlocfilehash: fad5c315a247673b7a2ad52b4a78b49c567a997a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="quickstart-for-the-azure-ad-graph-api"></a>Guia de início rápido para a Graph API do AD do Azure
A Graph API do Active Directory do Azure fornece acesso programático ao AD do Azure por meio de pontos de extremidade OData REST API. Os aplicativos podem usar a Graph API para realizar operações de criação, leitura, atualização e exclusão (CRUD) nos objetos e dados do diretório. Por exemplo, você pode usar a Graph API para criar um novo usuário, exibir ou atualizar as propriedades do usuário, alterar a senha do usuário, verificar a associação de grupo para acesso baseado em função, desabilitar ou excluir o usuário. Para obter mais informações sobre os recursos da API do Graph e cenários de aplicativo, consulte a [API do Graph do Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) e [Pré-requisitos da API do Graph do Azure AD](https://msdn.microsoft.com/library/hh974476.aspx). 

> [!IMPORTANT]
> É altamente recomendável usar o [Microsoft Graph](https://developer.microsoft.com/graph) em vez da API do Azure AD Graph para acessar recursos do Azure Active Directory. Nossos esforços de desenvolvimento agora estão concentrados no Microsoft Graph e não há planejamento de novas melhorias para a API do Azure AD Graph. Há um número muito limitado de cenários para os quais a API do Azure AD Graph ainda pode ser apropriada; para obter mais informações, consulte a postagem no blog [Microsoft Graph ou o Azure AD Graph](https://dev.office.com/blogs/microsoft-graph-or-azure-ad-graph) no Centro de Desenvolvimento do Office.
> 
> 

## <a name="how-to-construct-a-graph-api-url"></a>Como criar uma URL da Graph API
Na API do Graph, para acessar dados de diretório e objetos (em outras palavras, recursos ou entidades) em relação aos quais deseja realizar operações CRUD, você pode usar URLs com base no Protocolo OData (Open Data). As URLs usadas na Graph API consistem em quatro partes principais: serviço raiz, identificador de locatário, caminho de recurso e opções de cadeia de caracteres de consulta: `https://graph.windows.net/{tenant-identifier}/{resource-path}?[query-parameters]`. Veja o exemplo da seguinte URL: `https://graph.windows.net/contoso.com/groups?api-version=1.6`.

* **Raiz de Serviço**: na API do Azure AD Graph, a raiz do serviço é sempre https://graph.windows.net.
* **Identificador de locatário**: esta seção pode ser um nome de domínio (registrado) verificado; no exemplo anterior, contoso.com. Também pode ser uma ID de objeto de locatário ou o alias "myorganization" ou "me". Para saber mais, confira [Abordando entidades e operações na Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-operations-overview)).
* **Caminho do recurso**: essa seção de uma URL identifica o recurso com o qual interagir (usuários, grupos, um usuário específico ou um grupo específico etc.) No exemplo acima, trata-se de "grupos" de nível superior para abordar esse conjunto de recursos. Você também pode abordar uma entidade específica, por exemplo "users/{objectId}" ou "users/userPrincipalName".
* **Parâmetros de consulta**: um ponto de interrogação (?) separa a seção de caminho de recurso da seção de parâmetros de consulta. O parâmetro de consulta "api-version" é necessário em todas as solicitações na Graph API. A API do Graph também dá suporte às seguintes opções de consulta OData: **$filter**, **$orderby**, **$expand**, **$top** e **$format**. Atualmente não há suporte às seguintes opções de consulta: **$count**, **$inlinecount** e **$skip**. Para saber mais, confira [Suporte a consultas, filtros e opções de paginação na API do Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options).

## <a name="graph-api-versions"></a>Versões da Graph API
Você especifica a versão de uma solicitação da Graph API no parâmetro de consulta "api-version". Para a versão 1.5 e posterior, você usa um valor numérico de versão; api-version=1.6. Para versões anteriores, você usa uma cadeia de caracteres de data que segue o formato AAAA-MM-DD; por exemplo, api-version=2013-11-08. Para recursos de visualização, use a cadeia de caracteres "beta"; por exemplo, api-version=beta. Para obter mais informações sobre as diferenças entre as versões da Graph API, consulte [Controle de versão da Graph API do AD do Azure](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-versioning).

## <a name="graph-api-metadata"></a>Metadados da Graph API
Para retornar o arquivo de metadados da Graph API, adicione o segmento "$metadata" após o identificador de locatário na URL. Por exemplo, a seguinte URL retorna metadados para a empresa de demonstração: `https://graph.windows.net/GraphDir1.OnMicrosoft.com/$metadata?api-version=1.6`. Você pode inserir esta URL na barra de endereços de um navegador da Web para ver os metadados. O documento de metadados CSDL retornado descreve as entidades e os tipos complexos, suas propriedades e as funções e ações expostas pela versão da Graph API solicitada. A omissão do parâmetro api-version retorna metadados da versão mais recente.

## <a name="common-queries"></a>Consultas comuns
[Consultas comuns da API do Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options#CommonQueries) lista as consultas comuns que podem ser usadas com a Graph do AD do Azure, incluindo consultas que podem ser usadas para acessar recursos de nível superior no diretório e consultas para executar operações em seu diretório.

Por exemplo, `https://graph.windows.net/contoso.com/tenantDetails?api-version=1.6` retorna informações de empresa para o diretório contoso.com.

Ou `https://graph.windows.net/contoso.com/users?api-version=1.6` lista todos os objetos de usuário no diretório contoso.com.

## <a name="using-the-graph-explorer"></a>Usando o Gerenciador do Graph
Você pode usar o Gerenciador do Graph para a Graph API do AD do Azure para consultar os dados de diretório ao criar seu aplicativo.

A seguir está a saída que você verá se navegar para o Gerenciador do Graph, entrar e inserir `https://graph.windows.net/GraphDir1.OnMicrosoft.com/users?api-version=1.6` para exibir todos os usuários no diretório do usuário conectados:

![Gerenciador de a Graph API do AD do Azure](./media/active-directory-graph-api-quickstart/graph_explorer.png)

**Carregar o Gerenciador do Graph**: para carregar a ferramenta, navegue até [https://graphexplorer.azurewebsites.net/](https://graphexplorer.azurewebsites.net/). Clique em **Entrar** e entre com suas credenciais de conta do AD do Azure para executar o Gerenciador do Graph no seu locatário. Se executar o Explorador do Graph em seu próprio locatário, você ou o administrador precisarão dar permissão durante o logon. Se tiver uma assinatura do Office 365, você automaticamente terá um locatário do AD do Azure. As credenciais usadas para entrar no Office 365 são, na verdade, contas do AD do Azure, e você pode usar essas credenciais com o Gerenciador do Graph.

**Executar uma consulta**: para executar uma consulta, digite a consulta na caixa de texto de solicitação e clique em **GET** ou clique na tecla **enter**. Os resultados são exibidos na caixa de resposta. Por exemplo, `https://graph.windows.net/myorganization/groups?api-version=1.6` lista todos os objetos de grupo no diretório do usuário conectado.

Observe os seguintes recursos e limitações do Gerenciador do Graph:

* Funcionalidade de preenchimento automático em conjuntos de recursos. Para ver essa funcionalidade, clique em na caixa de texto de solicitação (em que a URL da empresa é exibida). Você pode selecionar um conjunto de recursos na lista suspensa.
* Dá suporte aos aliasesde endereçamento "me" e "myorganization". Por exemplo, você pode usar `https://graph.windows.net/me?api-version=1.6` para retornar o objeto de usuário do usuário conectado ou `https://graph.windows.net/myorganization/users?api-version=1.6` para retornar todos os usuários no diretório atual.
* Uma seção de cabeçalhos de resposta. Essa seção pode ser usada para ajudar a solucionar problemas que ocorrem ao executar consultas.
* Um visualizador JSON para a resposta com recursos de expandir e recolher.
* Não há suporte para a exibição de uma foto em miniatura.

## <a name="using-fiddler-to-write-to-the-directory"></a>Usando o Fiddler para gravar no diretório
Para os fins deste Guia de Início Rápido, você poderá usar o Depurador da Web Fiddler para executar operações de 'gravação' em relação ao diretório do Azure AD. Para obter mais informações e para instalar o Fiddler, consulte [http://www.telerik.com/fiddler](http://www.telerik.com/fiddler).

No exemplo a seguir, o Depurador da Web Fiddler é usado para criar um novo grupo de segurança 'MyTestGroup' no diretório do Azure AD.

**Obter um token de acesso**: para acessar o Graph do AD do Azure, os clientes devem se autenticar no AD do Azure primeiro. Para obter mais informações, consulte [Cenários de autenticação para o Azure AD](active-directory-authentication-scenarios.md).

**Compor e executar uma consulta**: execute as etapas a seguir:

1. Abra o Depurador da Web Fiddler e alterne para a guia **Composer** .
2. Como você deseja criar um novo grupo de segurança, selecione **Post** como o método HTTP no menu suspenso. Para saber mais sobre operações e permissões em um objeto de grupo, confira [Grupo](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#GroupEntity) na [Referência da Graph REST API do Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).
3. No campo próximo a **Post**, digite o seguinte como a URL de solicitação:`https://graph.windows.net/mytenantdomain/groups?api-version=1.6` .
   
   > [!NOTE]
   > Você deve substituir mytenantdomain pelo nome de domínio de seu próprio diretório do AD do Azure.
   > 
   > 
4. No campo diretamente abaixo do meny suspenso Post, digite o seguinte:
   
    ```
   Host: graph.windows.net
   Authorization: Bearer <your access token>
   Content-Type: application/json
   ```
   
   > [!NOTE]
   > Substitua &lt;seu token de acesso&gt; pelo token de acesso para seu diretório do Azure AD.
   > 
   > 
5. No campo **Corpo da solicitação** , digite o seguinte:
   
    ```
        {
            "displayName":"MyTestGroup",
            "mailNickname":"MyTestGroup",
            "mailEnabled":"false",
            "securityEnabled": true
        }
   ```
   
    Para saber mais sobre como criar grupos, confira [Criar um grupo](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/groups-operations#CreateGroup).

Para saber mais sobre entidades e tipos do AD do Azure que são expostos pelo Graph e informações sobre as operações que podem ser executadas neles com o Graph, confira [Referência da Graph REST API do AD do Azure](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre a [API do Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)
* Saiba mais sobre [Escopos de Permissão da API do Graph do Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes)

