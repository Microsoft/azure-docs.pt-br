---
title: Como usar a API do Graph do Azure AD
description: A API do Graph do Azure AD (Azure Active Directory) fornece acesso programático ao Azure AD por meio de pontos de extremidade OData REST API. Aplicativos podem usar a API do Microsoft Azure AD Graph para realizar operações CRUD (criar, ler, atualizar e excluir) nos objetos e dados do diretório.
services: active-directory
documentationcenter: n/a
author: rwike77
manager: CelesteDG
editor: ''
tags: ''
ms.assetid: 9dc268a9-32e8-402c-a43f-02b183c295c5
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: sureshja
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a2ae83b655bfe607b1061168649ec74f02ab4064
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67483170"
---
# <a name="how-to-use-the-azure-ad-graph-api"></a>Como: Usar a API do Graph do Azure AD

A API do Graph do Azure AD (Azure Active Directory) fornece acesso programático ao Azure AD por meio de pontos de extremidade OData REST API. Aplicativos podem usar a API do Microsoft Azure AD Graph para realizar operações CRUD (criar, ler, atualizar e excluir) nos objetos e dados do diretório. Por exemplo, você pode usar a API do Microsoft Azure AD Graph para criar um novo usuário, exibir ou atualizar as propriedades do usuário, alterar a senha do usuário, verificar a associação de grupo para acesso baseado em função, desabilitar ou excluir o usuário. Para obter mais informações sobre os recursos da API do Microsoft Azure AD Graph e cenários de aplicativo, consulte a [API do Microsoft Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) e [Pré-requisitos da API do Microsoft Azure AD Graph](https://msdn.microsoft.com/library/hh974476.aspx).

Este artigo se aplica à API do Graph do Azure Active Directory. Para obter informações semelhantes relacionadas a API do Microsoft Graph, consulte [usar a API do Graph](https://developer.microsoft.com/graph/docs/concepts/use_the_api).

> [!IMPORTANT]
> É altamente recomendável usar o [Microsoft Graph](https://developer.microsoft.com/graph) em vez da API do Azure AD Graph para acessar recursos do Azure Active Directory. Nossos esforços de desenvolvimento agora estão concentrados no Microsoft Graph e não há planejamento de novas melhorias para a API do Azure AD Graph. Há um número muito limitado de cenários para os quais a API do Azure AD Graph ainda pode ser apropriada; para obter mais informações, consulte a postagem no blog [Microsoft Graph ou o Azure AD Graph](https://dev.office.com/blogs/microsoft-graph-or-azure-ad-graph) no Centro de Desenvolvimento do Office.

## <a name="how-to-construct-a-graph-api-url"></a>Como criar uma URL da API do Graph

Na API do Graph, para acessar dados de diretório e objetos (em outras palavras, recursos ou entidades) em relação aos quais deseja realizar operações CRUD, você pode usar URLs com base no Protocolo OData (Open Data). As URLs usadas na API do Graph consistem em quatro partes principais: serviço raiz, identificador de locatário, caminho de recurso e opções de cadeia de caracteres de consulta: `https://graph.windows.net/{tenant-identifier}/{resource-path}?[query-parameters]`. Veja o exemplo da seguinte URL: `https://graph.windows.net/contoso.com/groups?api-version=1.6`.

* **Raiz do serviço**: Na API do Graph do Azure AD, a raiz do serviço é sempre https://graph.windows.net.
* **Identificador de locatário**: Esta seção pode ser um nome de domínio verificado (registrado), no exemplo anterior, contoso.com. Também pode ser uma ID de objeto de locatário ou o alias "myorganization" ou "me". Para obter mais informações, consulte [Endereçamento de entidades e operações na API do Microsoft Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-operations-overview).
* **Caminho do recurso**: Esta seção de uma URL identifica o recurso a ser interagido (usuários, grupos, um usuário em particular ou um grupo específico, etc.) No exemplo acima, trata-se de "grupos" de nível superior para abordar esse conjunto de recursos. Você também pode abordar uma entidade específica, por exemplo "users/{objectId}" ou "users/userPrincipalName".
* **Parâmetros de consulta**: Um ponto de interrogação (?) separa a seção do caminho do recurso da seção de parâmetros de consulta. O parâmetro de consulta "api-version" é necessário em todas as solicitações na API do Microsoft Azure AD Graph. A API do Microsoft Azure AD Graph também fornece suporte às seguintes opções de consulta OData: **$filter**, **$orderby**, **$expand**, **$top** e **$format**. Atualmente não há suporte às seguintes opções de consulta: **$count**, **$inlinecount** e **$skip**. Para saber mais, confira [Suporte a consultas, filtros e opções de paginação na API do Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options).

## <a name="graph-api-versions"></a>Versões da API do Graph

Você especifica a versão de uma solicitação da API do Graph no parâmetro de consulta "api-version". Para a versão 1.5 e posterior, você usa um valor numérico de versão; api-version=1.6. Para versões anteriores, você usa uma cadeia de caracteres de data que segue o formato AAAA-MM-DD; por exemplo, api-version=2013-11-08. Para recursos de visualização, use a cadeia de caracteres "beta"; por exemplo, api-version=beta. Para obter mais informações sobre as diferenças entre as versões da API do Graph, consulte [Controle de versão da API do Graph do AD do Azure](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-versioning).

## <a name="graph-api-metadata"></a>Metadados da API do Graph

Para retornar o arquivo de metadados da API do Microsoft Azure AD Graph, adicione o segmento "$metadata" após o identificador de locatário na URL. Por exemplo, a seguinte URL retorna metadados para a empresa de demonstração: `https://graph.windows.net/GraphDir1.OnMicrosoft.com/$metadata?api-version=1.6`. Você pode inserir esta URL na barra de endereços de um navegador da Web para ver os metadados. O documento de metadados CSDL retornado descreve as entidades e os tipos complexos, suas propriedades e as funções e ações expostas pela versão da API do Graph solicitada. A omissão do parâmetro api-version retorna metadados da versão mais recente.

## <a name="common-queries"></a>Consultas comuns

[Consultas comuns da API do Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options#CommonQueries) lista as consultas comuns que podem ser usadas com a Graph do AD do Azure, incluindo consultas que podem ser usadas para acessar recursos de nível superior no diretório e consultas para executar operações em seu diretório.

Por exemplo, `https://graph.windows.net/contoso.com/tenantDetails?api-version=1.6` retorna informações de empresa para o diretório contoso.com.

Ou `https://graph.windows.net/contoso.com/users?api-version=1.6` lista todos os objetos de usuário no diretório contoso.com.

## <a name="using-the-azure-ad-graph-explorer"></a>Usar o Explorador do Microsoft Azure AD Graph
É possível utilizar o Explorador do Microsoft Azure AD Graph para a API do Microsoft Azure AD Graph para consultar os dados de diretório ao compilar o aplicativo.

A captura de tela a seguir é a saída que você veria se fosse navegar até o Explorador do Microsoft Azure AD Graph, entrar e inserir `https://graph.windows.net/GraphDir1.OnMicrosoft.com/users?api-version=1.6` para exibir todos os usuários no diretório do usuário conectado:

![Exemplo de saída no Gerenciador de API do Graph do AD do Azure](./media/active-directory-graph-api-quickstart/graph_explorer.png)

**Carregar o Gerenciador do Graph do Azure AD** : Para carregar a ferramenta, navegue até [https://graphexplorer.azurewebsites.net/](https://graphexplorer.azurewebsites.net/). Clique em **Logon** e entre com suas credenciais de conta do Microsoft Azure AD para executar o Explorador do Microsoft Azure AD Graph no locatário. Se executar o Explorador do Microsoft Azure AD Graph no seu próprio locatário, você ou o administrador precisará dar permissão durante a entrada. Se tiver uma assinatura do Office 365, você automaticamente terá um locatário do AD do Azure. As credenciais usadas para entrar no Office 365 são, na verdade, contas do Microsoft Azure AD e você pode usar essas credenciais com o Explorador do Microsoft Azure AD Graph.

**Executar uma consulta**: Para executar uma consulta, digite sua consulta na caixa de texto da solicitação e clique em **GET** ou em **inserir**. Os resultados são exibidos na caixa de resposta. Por exemplo, `https://graph.windows.net/myorganization/groups?api-version=1.6` lista todos os objetos de grupo no diretório do usuário conectado.

Observe os recursos e limitações a seguir do Explorador do Microsoft Azure AD Graph:

* Funcionalidade de preenchimento automático em conjuntos de recursos. Para ver essa funcionalidade, clique em na caixa de texto de solicitação (em que a URL da empresa é exibida). Você pode selecionar um conjunto de recursos na lista suspensa.
* Histórico de solicitações.
* Dá suporte aos aliasesde endereçamento "me" e "myorganization". Por exemplo, é possível usar `https://graph.windows.net/me?api-version=1.6` para retornar o objeto de usuário do usuário conectado ou `https://graph.windows.net/myorganization/users?api-version=1.6` retornar todos os usuários no diretório do usuário conectado.
* Forneça suporte a operações CRUD completas no próprio diretório usando `POST`, `GET`, `PATCH` e `DELETE`.
* Uma seção de cabeçalhos de resposta. Essa seção pode ser usada para ajudar a solucionar problemas que ocorrem ao executar consultas.
* Um visualizador JSON para a resposta com recursos de expandir e recolher.
* Não há suporte para exibir ou carregar uma foto em miniatura.

## <a name="using-fiddler-to-write-to-the-directory"></a>Usando o Fiddler para gravar no diretório

Para os fins deste Guia de Início Rápido, você poderá usar o Depurador da Web Fiddler para executar operações de 'gravação' em relação ao diretório do Azure AD. Por exemplo, é possível obter e carregar a foto do perfil de um usuário (o que não é possível com o Explorador do Microsoft Azure AD Graph). Para obter mais informações e para instalar o Fiddler, consulte [https://www.telerik.com/fiddler](https://www.telerik.com/fiddler).

No exemplo a seguir, o Depurador da Web Fiddler é usado para criar um novo grupo de segurança 'MyTestGroup' no diretório do Azure AD.

**Obter um token de acesso**: Para acessar o Graph do Azure AD, é necessário que os clientes primeiro autentiquem-se com êxito no Azure AD. Para obter mais informações, consulte [Cenários de autenticação para o Azure AD](authentication-scenarios.md).

**Redigir e executar uma consulta**: Conclua as seguintes etapas:

1. Abra o Depurador da Web Fiddler e alterne para a guia **Composer** .
2. Como você deseja criar um novo grupo de segurança, selecione **Post** como o método HTTP no menu suspenso. Para saber mais sobre operações e permissões em um objeto de grupo, confira [Grupo](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#group-entity) na [Referência da Graph REST API do Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).
3. No campo ao lado de **Postar**, digite a URL de solicitação a seguir: `https://graph.windows.net/{mytenantdomain}/groups?api-version=1.6`.
   
   > [!NOTE]
   > É necessário substituir {mytenantdomain} pelo nome de domínio do seu próprio diretório do Microsoft Azure AD.

4. No campo diretamente abaixo do menu suspenso Postar, digite o cabeçalho HTTP a seguir:
   
    ```
   Host: graph.windows.net
   Authorization: Bearer <your access token>
   Content-Type: application/json
   ```
   
   > [!NOTE]
   > Substitua &lt;seu token de acesso&gt; pelo token de acesso para seu diretório do Azure AD.

5. No campo **Corpo da solicitação**, digite o JSON a seguir:
   
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
