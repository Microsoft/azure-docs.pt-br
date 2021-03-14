---
title: Configurar um indexador do SharePoint Online (versão prévia)
titleSuffix: Azure Cognitive Search
description: Configure um indexador do SharePoint Online para automatizar a indexação do conteúdo da biblioteca de documentos no Azure Pesquisa Cognitiva.
manager: luisca
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/01/2021
ms.openlocfilehash: 5a44c40838b7f7fa9ca499ade49317ff9ce828fe
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102498890"
---
# <a name="how-to-configure-sharepoint-online-indexing-in-cognitive-search-preview"></a>Como configurar a indexação do SharePoint Online no Pesquisa Cognitiva (versão prévia)

> [!IMPORTANT] 
> O suporte do SharePoint Online está atualmente em uma **Visualização pública restrita**. Você pode solicitar acesso à visualização restrita preenchendo [este formulário](https://aka.ms/azure-cognitive-search/indexer-preview).
>
> A funcionalidade de versão prévia é fornecida sem um Contrato de Nível de Serviço e, portanto, não é recomendada para cargas de trabalho de produção. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
> 
> A [API REST versão 2020-06-30-Preview](search-api-preview.md) fornece esse recurso. No momento, não há suporte para Portal ou SDK.

> [!NOTE]
> O SharePoint Online dá suporte a um modelo de autorização granular que determina o acesso por usuário no nível do documento. O indexador do SharePoint Online não recebe essas permissões no índice de pesquisa e Pesquisa Cognitiva não oferece suporte à autorização em nível de documento. Quando um documento é indexado do SharePoint Online em um serviço de pesquisa, o conteúdo fica disponível para qualquer pessoa que tenha acesso de leitura ao índice. Se você precisar de permissões em nível de documento, investigue os filtros de segurança para cortar os resultados de conteúdo não autorizado. Para obter mais informações, consulte [remoção de segurança usando identidades de Active Directory](search-security-trimming-for-azure-search-with-aad.md).

Este artigo descreve como usar o Pesquisa Cognitiva do Azure para indexar documentos (como PDFs, Microsoft Office documentos e vários outros formatos comuns) armazenados em bibliotecas de documentos do SharePoint Online em um índice de Pesquisa Cognitiva do Azure. Primeiro, ele explica as noções básicas de configuração e configuração do indexador. Em seguida, ele explora mais profundamente os comportamentos e cenários que você pode encontrar.

## <a name="functionality"></a>Funcionalidade
Um indexador no Azure Pesquisa Cognitiva é um rastreador que extrai dados pesquisáveis e metadados de uma fonte de dados. O indexador do SharePoint Online se conectará ao seu site do SharePoint Online e indexar documentos de uma ou mais bibliotecas de documentos. O indexador fornece a seguinte funcionalidade:
+ Indexe o conteúdo de uma ou mais bibliotecas de documentos do SharePoint Online.
+ Indexe conteúdo de bibliotecas de documentos do SharePoint Online que estão no mesmo locatário que o serviço de Pesquisa Cognitiva do Azure. O indexador não funcionará com sites do SharePoint que estão em um locatário diferente do serviço de Pesquisa Cognitiva do Azure. 
+ O indexador dará suporte à indexação incremental, o que significa que ele identificará qual conteúdo na biblioteca de documentos foi alterado e indexará apenas o conteúdo atualizado em execuções futuras de indexação. Por exemplo, se 5 PDFs são originalmente indexados pelo indexador, 1 é atualizado e, em seguida, o indexador é executado novamente, o indexador indexa apenas o 1 PDF que foi atualizado.
+ As imagens de texto e normalizadas serão extraídas por padrão dos documentos que são indexados. Opcionalmente, um qualificable pode ser adicionado ao pipeline para aprimorar o conteúdo. Mais informações sobre o habilidades podem ser encontradas no artigo [conceitos do Skills no Azure pesquisa cognitiva](cognitive-search-working-with-skillsets.md).

## <a name="supported-document-formats"></a>Formatos de documento com suporte

O indexador do Azure Pesquisa Cognitiva SharePoint Online pode extrair texto dos seguintes formatos de documento:

[!INCLUDE [search-document-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="incremental-indexing-and-deletion-detection"></a>Indexação incremental e detecção de exclusão 
Por padrão, o indexador do SharePoint Online dá suporte à indexação incremental, o que significa que ele identificará qual conteúdo na biblioteca de documentos foi alterado e indexará apenas o conteúdo atualizado em execuções de indexação futuras. Por exemplo, se 5 documentos do Word forem originalmente indexados pelo indexador, 1 for atualizado, o indexador será executado novamente, o indexador só reindexa o documento 1 do Word que foi atualizado.

A detecção de exclusão também é suportada por padrão. Isso significa que, se um documento for excluído de uma biblioteca de documentos do SharePoint Online, o indexador detectará a exclusão durante uma execução futura do indexador e removerá o documento do índice.

## <a name="setting-up-sharepoint-online-indexing"></a>Configurando a indexação do SharePoint Online
Para configurar o indexador do SharePoint Online, você precisará executar algumas ações no portal do Azure e algumas ações usando a API REST de visualização. Essa versão prévia não é suportada pelo SDK.

### <a name="step-1-enable-system-assigned-managed-identity"></a>Etapa 1: habilitar a identidade gerenciada atribuída pelo sistema
Quando uma identidade gerenciada atribuída pelo sistema é habilitada, o Azure cria uma identidade para o serviço de pesquisa que pode ser usada pelo indexador.

![Habilitar a identidade gerenciada atribuída pelo sistema](media/search-howto-index-sharepoint-online/enable-managed-identity.png "Habilitar a identidade gerenciada atribuída pelo sistema")

Depois de selecionar **Salvar**, você verá uma ID do objeto que foi atribuída ao serviço de pesquisa.

![Identidade gerenciada atribuída ao sistema](media/search-howto-index-sharepoint-online/system-assigned-managed-identity.png "Identidade gerenciada atribuída pelo sistema")

### <a name="step-2-create-an-aad-application"></a>Etapa 2: criar um aplicativo do AAD
O indexador do SharePoint online usará esse aplicativo do AAD para autenticação. 

1.  Navegue até o [Portal do Azure](https://portal.azure.com/).

1.  Abra o menu no lado esquerdo da página principal e selecione **Azure Active Directory** , em seguida, selecione **registros de aplicativo**. Selecione **+ novo registro**.
    1.  Forneça um nome para seu aplicativo.
    2.  Selecione **um único locatário**.
    3.  Nenhum URI de redirecionamento necessário.
    4.  Escolha **Registrar**

1.  Selecione **permissões de API** no menu à esquerda, em seguida **, adicione uma permissão** e, em seguida, **Microsoft Graph** **permissões delegadas**. Adicione as seguintes permissões de API: 
    1.  **Delegued-files. Read. All** 
    2.  **Delegado-sites. Read. All** 
    3.  **Delegado-User. Read**

    ![Permissões de API delegadas](media/search-howto-index-sharepoint-online/delegated-api-permissions.png "Permissões de API delegadas")

    O uso de permissões delegadas significa que o indexador acessará o site do SharePoint no contexto do usuário. Assim, quando você executar o indexador, ele só terá acesso ao conteúdo ao qual o usuário conectado tem acesso. O logon de usuário ocorre ao criar o indexador ou atualizar a fonte de dados. A etapa de logon é descrita posteriormente neste artigo.

1.  Selecione a guia **autenticação** . Defina **permitir fluxos de cliente público** como **Sim** e, em seguida, selecione **salvar**.

1.  Selecione **+ Adicionar uma plataforma** e, em seguida, **aplicativos móveis e de área de trabalho**, em seguida, marque `https://login.microsoftonline.com/common/oauth2/nativeclient` e **Configurar**.

    ![Configuração de autenticação de aplicativo do AAD](media/search-howto-index-sharepoint-online/aad-app-authentication-configuration.png "Configuração de autenticação de aplicativo do AAD")

1.  Dê consentimento ao administrador (necessário somente para determinados locatários).

    Alguns locatários são bloqueados de tal forma que o consentimento do administrador é necessário para essas permissões de API delegadas. Se esse for o caso, você precisará ter um administrador concede consentimento de administrador para este aplicativo do AAD antes de criar o indexador. 

    Como nem todos os locatários têm esse requisito, recomendamos ignorar essa etapa primeiro e continuar com as instruções. Você saberá se precisa de consentimento de administrador se ao criar o indexador, a autenticação falhar informando que você precisa de um administrador para aprovar a autenticação. Nesse caso, peça a um administrador de locatários que conceda consentimento usando o botão abaixo.

    ![Consentimento do administrador de concessão de aplicativo do AAD](media/search-howto-index-sharepoint-online/aad-app-grant-admin-consent.png "Consentimento do administrador de concessão de aplicativo do AAD")

<a name="create-data-source"></a>

### <a name="step-3-create-data-source"></a>Etapa 3: criar uma fonte de dados
> [!IMPORTANT] 
> A partir desta seção, você precisa usar a API REST de visualização para as etapas restantes. Se você não estiver familiarizado com a API REST do Azure Pesquisa Cognitiva, sugerimos dar uma olhada neste guia de [início rápido](search-get-started-rest.md).

Uma fonte de dados especifica quais dados indexar, as credenciais necessárias para acessar os dados e as políticas que identificam com eficiência as alterações nos dados (linhas novas, modificadas ou excluídas). Uma fonte de dados pode ser usada por vários indexadores no mesmo serviço de pesquisa.

Para a indexação do SharePoint, a fonte de dados deve ter as seguintes propriedades necessárias:
+ **name** é o nome exclusivo da fonte de dados dentro de seu serviço de pesquisa.
+ o **tipo** deve ser "SharePoint". Isso diferencia maiúsculas de minúsculas.
+ as **credenciais** fornecem o ponto de extremidade do SharePoint Online e a ID do aplicativo (cliente) do AAD. Um ponto de extremidade do SharePoint Online de exemplo é `https://microsoft.sharepoint.com/teams/MySharePointSite` . Você pode obter o ponto de extremidade do SharePoint Online navegando até a home page do site do SharePoint e copiando a URL do navegador.
+ **contêiner** especifica qual biblioteca de documentos indexar. Mais informações sobre como criar o contêiner podem ser encontradas na seção [controlando quais documentos são indexados](#controlling-which-documents-are-indexed) deste documento.

Para criar uma fonte de dados:

```http
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    "name" : "sharepoint-datasource",
    "type" : "sharepoint",
    "credentials" : { "connectionString" : "SharePointOnlineEndpoint=[SharePoint Online site url];ApplicationId=[AAD App ID]" },
    "container" : { "name" : "defaultSiteLibrary", "query" : null }
}
```

### <a name="step-4-create-an-index"></a>Etapa 4: criar um índice
O índice especifica os campos em um documento, atributos e outras construções que modelam a experiência de pesquisa.

Veja como criar um índice com um campo de conteúdo pesquisável para armazenar o texto extraído de documentos em uma biblioteca de documentos:

```http
POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "sharepoint-index",
    "fields": [
        { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
        { "name": "metadata_spo_item_name", "type": "Edm.String", "key": false, "searchable": true, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_spo_item_path", "type": "Edm.String", "key": false, "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_spo_item_content_type", "type": "Edm.String", "key": false, "searchable": false, "filterable": true, "sortable": false, "facetable": true },
        { "name": "metadata_spo_item_last_modified", "type": "Edm.DateTimeOffset", "key": false, "searchable": false, "filterable": false, "sortable": true, "facetable": false },
        { "name": "metadata_spo_item_size", "type": "Edm.Int64", "key": false, "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
    ]
}

```

Para obter mais informações, consulte [criar índice (API REST)](https://docs.microsoft.com/rest/api/searchservice/create-index).

### <a name="step-5-create-an-indexer"></a>Etapa 5: criar um indexador
Um indexador conecta uma fonte de dados a um índice de pesquisa de destino e fornece um agendamento para automatizar a atualização de dados. Depois que o índice e a fonte de dados tiverem sido criados, você estará pronto para criar o indexador!

Durante esta seção, você será solicitado a fazer logon com as credenciais da sua organização que têm acesso ao site do SharePoint. Se possível, é recomendável criar uma nova conta de usuário organizacional e dar a esse novo usuário as permissões exatas que você deseja que o indexador tenha.

Há algumas etapas para criar o indexador:

1.  Envie uma solicitação para tentar criar o indexador.

    ```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    
        {
          "name" : "sharepoint-indexer",
          "dataSourceName" : "sharepoint-datasource",
          "targetIndexName" : "sharepoint-index",
          "fieldMappings" : [
            { 
              "sourceFieldName" : "metadata_spo_site_library_item_id", 
              "targetFieldName" : "id", 
              "mappingFunction" : { 
                "name" : "base64Encode" 
              } 
            }
          ]
        }
    
    ```

1.  Ao criar o indexador pela primeira vez, ele falhará e você verá o erro a seguir. Vá para o link na mensagem de erro. Se você não acessar o link em 10 minutos, o código expirará e você precisará recriar a fonte de [dados](#create-data-source).

    ```http
    {
        "error": {
            "code": "",
            "message": "Error with data source: To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code <CODE> to authenticate.  Please adjust your data source definition in order to proceed."
        }
    }
    ```

1.  Forneça o código que foi fornecido na mensagem de erro.

    ![Insira o código do dispositivo](media/search-howto-index-sharepoint-online/enter-device-code.png "Insira o código do dispositivo")

1.  O indexador do SharePoint acessará o conteúdo do SharePoint como o usuário conectado. O usuário que faz logon durante esta etapa será o usuário conectado. Portanto, se você fizer logon com uma conta de usuário que não tenha acesso a um documento na biblioteca de documentos que você deseja indexar, o indexador não terá acesso a esse documento.

    Se possível, é recomendável criar uma nova conta de usuário e dar a esse novo usuário as permissões exatas que você deseja que o indexador tenha.

1.  Aprove as permissões que estão sendo solicitadas.

    ![Aprovar permissões de API](media/search-howto-index-sharepoint-online/aad-app-approve-api-permissions.png "Aprovar permissões de API")

1.  Reenvie a solicitação de criação do indexador. Desta vez, a solicitação deve ter sucesso. 

    ```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    
    {
        "name" : "sharepoint-indexer",
        "dataSourceName" : "sharepoint-datasource",
        "targetIndexName" : "sharepoint-index"
    }
    ```

### <a name="step-6-check-the-indexer-status"></a>Etapa 6: verificar o status do indexador
Depois que o indexador tiver sido criado, você poderá verificar o status do indexador fazendo a seguinte solicitação.

```http
GET https://[service name].search.windows.net/indexers/sharepoint-indexer/status?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]
```

Mais informações sobre o status do indexador podem ser encontradas aqui: [obter o status do indexador](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status).

## <a name="updating-the-data-source"></a>Atualizando a fonte de dados
Se não houver nenhuma atualização para o objeto de fonte de dados, o indexador poderá ser executado em um agendamento sem qualquer interação do usuário. No entanto, toda vez que o objeto de fonte de dados do Azure Pesquisa Cognitiva for atualizado, você precisará fazer logon novamente para que o indexador seja executado. Por exemplo, se você alterar a consulta de fonte de dados, será necessário fazer logon novamente usando o `https://microsoft.com/devicelogin` e um novo código.

Depois que a fonte de dados tiver sido atualizada, siga as etapas abaixo:

1.  Iniciar manualmente uma execução do indexador.

    ```http
    POST https://[service name].search.windows.net/indexers/sharepoint-indexer/run?api-version=2020-06-30-Preview  
    Content-Type: application/json
    api-key: [admin key]
    ```

    Mais informações sobre a solicitação de execução do indexador podem ser encontradas aqui: [executar o indexador](https://docs.microsoft.com/rest/api/searchservice/run-indexer).

1.  Verifique o status do indexador. Se a execução do último indexador tiver um erro informando-o, acesse `https://microsoft.com/devicelogin` essa página e forneça o novo código. 

    ```http
    GET https://[service name].search.windows.net/indexers/sharepoint-indexer/status?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    ```

    Mais informações sobre o status do indexador podem ser encontradas aqui: [obter o status do indexador](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status).

1.  Logon

1.  Inicie manualmente um indexador e verifique o status do indexador. Desta vez, a execução do indexador deve ser iniciada com êxito.

## <a name="indexing-document-metadata"></a>Indexando metadados de documento
Se você tiver definido o indexador para indexar metadados de documento, os metadados a seguir estarão disponíveis para indexá-los.

> [!NOTE]
> Os metadados personalizados não estão incluídos na versão atual da visualização.

| Identificador | Type | Descrição | 
| ------------- | -------------- | ----------- |
| metadata_spo_site_library_item_id | Edm.String | A chave de combinação da ID do site, ID da biblioteca e ID do item que identifica exclusivamente um item em uma biblioteca de documentos para um site. |
| metadata_spo_site_id | Edm.String | A ID do site do SharePoint Online. |
| metadata_spo_library_id | Edm.String | A ID da biblioteca de documentos. |
| metadata_spo_item_id | Edm.String | A ID do item (documento) na biblioteca. |
| metadata_spo_item_last_modified | Edm.DateTimeOffset | A data/hora da última modificação (UTC) do item. |
| metadata_spo_item_name | Edm.String | O nome do item. |
| metadata_spo_item_size | Edm.Int64 | O tamanho (em bytes) do item. | 
| metadata_spo_item_content_type | Edm.String | O tipo de conteúdo do item. | 
| metadata_spo_item_extension | Edm.String | A extensão do item. |
| metadata_spo_item_weburi | Edm.String | O URI do item. |
| metadata_spo_item_path | Edm.String | A combinação do caminho pai e o nome do item. | 

O indexador do SharePoint Online também dá suporte a metadados específicos para cada tipo de documento. Mais informações podem ser encontradas nas [Propriedades de metadados de conteúdo usadas no Azure pesquisa cognitiva](search-blob-metadata-properties.md).

<a name="controlling-which-documents-are-indexed"></a>

## <a name="controlling-which-documents-are-indexed"></a>Controlando quais documentos são indexados
Um único indexador do SharePoint Online pode indexar conteúdo de uma ou mais bibliotecas de documentos. Use o parâmetro de *contêiner* ao criar sua fonte de dados para indicar as bibliotecas de documentos que você deseja indexar. O *contêiner* de fonte de dados tem duas propriedades: *nome* e *consulta*. 

### <a name="name"></a>Nome
A propriedade *Name* é necessária e deve ter um destes três valores:
+ *defaultSiteLibrary*
    + Indexe todo o conteúdo da biblioteca de documentos padrão de sites.
+   *allSiteLibraries*
    + Indexe todo o conteúdo de todas as bibliotecas de documentos em um site. Isso não indexará bibliotecas de documentos de um subsite. No entanto, eles podem ser especificados na *consulta* .
+   *useQuery*
    + Somente o conteúdo de índice definido na *consulta*.

### <a name="query"></a>Consulta
A propriedade de *consulta* é composta por pares de palavra-chave/valor. A seguir estão as palavras-chave que podem ser usadas. Os valores são URLs de site ou URLs de biblioteca de documentos.

> [!NOTE]
> Para obter o valor de uma palavra-chave específica, é recomendável abrir o SharePoint Online em um navegador, navegar até a biblioteca de documentos que você está tentando incluir/excluir e copiar o URI do navegador. Essa é a maneira mais fácil de obter o valor a ser usado com uma palavra-chave na consulta.

| Palavra-chave | Descrição da consulta | Exemplo |
| ------------- | -------------- | ----------- |
| nulo | Se for nulo ou vazio, indexe a biblioteca de documentos padrão ou todas as bibliotecas de documentos, dependendo do nome do contêiner. | Indexar todo o conteúdo da biblioteca de sites padrão: <br><br>  ``` "container" : { "name" : "defaultSiteLibrary", "query" : null } ``` |
| includeLibrariesInSite | Indexe o conteúdo de todas as bibliotecas no site definido na cadeia de conexão. Eles são limitados a subsites do seu site <br><br> O valor da *consulta* para essa palavra-chave deve ser o URI do site ou subsite. | Indexe todo o conteúdo de todas as bibliotecas de documentos no meusite. <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrariesInSite=https://mycompany.sharepoint.com/mysite" } ``` |
| includeLibrary | Indexe o conteúdo desta biblioteca. <br><br> O valor da *consulta* para essa palavra-chave deve estar em um dos seguintes formatos: <br><br> Exemplo 1: <br><br> *includeLibrary = [site ou subsite]/[biblioteca de documentos]* <br><br> Exemplo 2: <br><br> URI copiado do seu navegador. | Indexar todo o conteúdo de MyDocumentLibrary: <br><br> Exemplo 1: <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrary=https://mycompany.sharepoint.com/mysite/MyDocumentLibrary" } ``` <br><br> Exemplo 2: <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrary=https://mycompany.sharepoint.com/teams/mysite/MyDocumentLibrary/Forms/AllItems.aspx" } ``` |
| excludeLibrary |  Não indexe o conteúdo desta biblioteca. <br><br> O valor da *consulta* para essa palavra-chave deve estar em um dos seguintes formatos: <br><br> Exemplo 1: <br><br> *excludeLibrary = [URI do site ou subsite]/[biblioteca de documentos]* <br><br> Exemplo 2: <br><br> URI copiado do seu navegador. | Indexe todo o conteúdo de todas as minhas bibliotecas, exceto para MyDocumentLibrary: <br><br> Exemplo 1: <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrariesInSite=https://mysite.sharepoint.com/subsite1; excludeLibrary=https://mysite.sharepoint.com/subsite1/MyDocumentLibrary" } ``` <br><br> Exemplo 2: <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrariesInSite=https://mycompany.sharepoint.com/teams/mysite; excludeLibrary=https://mycompany.sharepoint.com/teams/mysite/MyDocumentLibrary/Forms/AllItems.aspx" } ``` |

## <a name="index-by-file-type"></a>Indexar por tipo de arquivo
Você pode controlar quais documentos são indexados e que são ignorados.

### <a name="include-documents-having-specific-file-extensions"></a>Incluir documentos com extensões de arquivo específicas
Você pode indexar apenas os documentos com as extensões de nome de arquivo especificadas usando o `indexedFileNameExtensions` parâmetro de configuração do indexador. O valor é uma cadeia de caracteres que contém uma lista separada por vírgulas das extensões de arquivo (com um ponto à esquerda). Por exemplo, para indexar apenas o. PDF e. Documentos DOCX, faça o seguinte:

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    ... other parts of indexer definition
    "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
}
```

### <a name="exclude-documents-having-specific-file-extensions"></a>Excluir documentos com extensões de arquivo específicas
Você pode excluir documentos com extensões de nome de arquivo específicas da indexação usando o `excludedFileNameExtensions` parâmetro de configuração. O valor é uma cadeia de caracteres que contém uma lista separada por vírgulas das extensões de arquivo (com um ponto à esquerda). Por exemplo, para indexar todo o conteúdo, exceto aqueles com o. PNG e. Extensões JPEG, faça o seguinte:

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    ... other parts of indexer definition
    "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
}
```

Se ambos `indexedFileNameExtensions` os `excludedFileNameExtensions` parâmetros e estiverem presentes, o Azure pesquisa cognitiva primeiro examinará `indexedFileNameExtensions` , em seguida, em `excludedFileNameExtensions` . Isso significa que, se a mesma extensão de arquivo estiver presente nas duas listas, ela será excluída da indexação.

## <a name="handling-errors"></a>Tratando erros
Por padrão, o indexador do SharePoint Online é interrompido assim que encontra um documento com um tipo de conteúdo sem suporte (por exemplo, uma imagem). Evidentemente, você pode usar o parâmetro `excludedFileNameExtensions` para ignorar alguns tipos de conteúdo. No entanto, talvez seja necessário indexar documentos sem conhecer todos os tipos de conteúdo possíveis com antecedência. Para continuar a indexação quando um tipo de conteúdo sem suporte for encontrado, defina o `failOnUnsupportedContentType` parâmetro de configuração como false:

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    ... other parts of indexer definition
    "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false } }
}
```

Para alguns documentos, o Azure Pesquisa Cognitiva não pode determinar o tipo de conteúdo ou não pode processar um documento de tipo de conteúdo com suporte de outra forma. Para ignorar este modo de falha, defina o parâmetro de configuração `failOnUnprocessableDocument` como false:

```http
"parameters" : { "configuration" : { "failOnUnprocessableDocument" : false } }
```

O Azure Pesquisa Cognitiva limita o tamanho dos documentos que são indexados. Esses limites são documentados em [limites de serviço no Azure pesquisa cognitiva](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity). Documentos superdimensionados são tratados como erros por padrão. No entanto, você ainda poderá indexar metadados de armazenamento de documentos superdimensionados se definir o `indexStorageMetadataOnlyForOversizedDocuments` parâmetro de configuração como true:

```http
"parameters" : { "configuration" : { "indexStorageMetadataOnlyForOversizedDocuments" : true } }
```

Você também pode continuar a indexação se ocorrerem erros em qualquer ponto de processamento, durante a análise de documentos ou ao adicionar documentos a um índice. Para ignorar um número específico de erros, defina os parâmetros de configuração `maxFailedItems` e `maxFailedItemsPerBatch` como os valores desejados. Por exemplo:

```http
{
    ... other parts of indexer definition
    "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 10 }
}
```

## <a name="see-also"></a>Confira também
+ [Indexadores na Pesquisa Cognitiva do Azure](search-indexer-overview.md)
+ [Propriedades de metadados de conteúdo usadas no Azure Pesquisa Cognitiva](search-blob-metadata-properties.md)
