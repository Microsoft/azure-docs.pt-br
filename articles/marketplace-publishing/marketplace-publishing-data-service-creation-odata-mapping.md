---
title: "Guia para a criação de um Serviço de Dados para o Marketplace | Microsoft Docs"
description: "Instruções detalhadas sobre como criar, certificar e implantar um Serviço de Dados para compra no Azure Marketplace."
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 3a632825-db5b-49ec-98bd-887138798bc4
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/26/2016
ms.author: hascipio; avikova
ms.openlocfilehash: a853b4dbd1952ba4ea8ee68ea3ca98f588bb71a2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="mapping-an-existing-web-service-to-odata-through-csdl"></a>Mapeamento de um serviço Web existente para OData por meio de CSDL
> [!IMPORTANT]
> **Neste momento, não estamos mais realizando a integração de novos editores de Serviço de Dados. Novos serviços de dados não serão ser aprovados para listagem.** Se você tiver um aplicativo de negócios de SaaS que quer publicar no AppSource, encontre mais informações [aqui](https://appsource.microsoft.com/partners). Se você tiver aplicativos de IaaS ou serviços de desenvolvedor para publicar no Azure Marketplace, encontre mais informações [aqui](https://azure.microsoft.com/marketplace/programs/certified/).
> 
> 

Este artigo fornece uma visão geral sobre como usar uma CSDL para mapear um serviço existente para um serviço OData compatível. Ele explica como criar o documento de mapeamento (CSDL) que transforma a solicitação de entrada do cliente por meio de uma chamada de serviço e a saída (dados) de volta para o cliente por meio de um feed OData compatível. O Microsoft Azure Marketplace expõe serviços aos usuários finais usando o protocolo OData. Os serviços expostos pelos provedores de conteúdo (Proprietários de Dados) são expostos de diversas formas, como REST, SOAP etc.

## <a name="what-is-a-csdl-and-its-structure"></a>O que é uma CSDL e sua estrutura?
Uma CSDL (Linguagem de Definição de Esquema Conceitual) é uma especificação que define como descrever o serviço Web ou o serviço de banco de dados em argumentação XML comum no Azure Marketplace.

Visão geral simples do **fluxo de solicitação:**

  `Client -> Azure Marketplace -> Content Provider’s Web Service (Get, Post, Delete, Put)`

O **fluxo de dados** ocorre na direção oposta:

  `Client <- Azure Marketplace <- Content Provider’s WebService`

**Figura 1** mostra como um cliente obtém dados de um provedor de conteúdo (seu serviço) por meio do Azure Marketplace.  A CSDL é usada pelo componente de Mapeamento/Transformação para manipular a solicitação e a transmissão de dados entre os serviços do provedor de conteúdo e o cliente solicitante.

*Figura 1: Fluxo detalhado do cliente solicitante para o provedor de conteúdo por meio do Azure Marketplace*

  ![desenho](media/marketplace-publishing-data-service-creation-odata-mapping/figure-1.png)

Para saber mais sobre Atom, o Atom Pub e o protocolo OData sobre os quais as extensões do Azure Marketplace são compiladas, consulte: [http://msdn.microsoft.com/library/ff478141.aspx](http://msdn.microsoft.com/library/ff478141.aspx)

Trecho do link acima: *"A finalidade do protocolo Open Data (citado a partir de agora como OData) é fornecer um protocolo baseado em REST para operações ao estilo CRUD (Criar, Ler, Atualizar e Excluir) em recursos expostos como serviços de dados. Um "serviço de dados" é um ponto de extremidade no qual há dados expostos de uma ou mais "coleções", cada um com zero ou mais "entradas", compostas por pares de valor nomeado digitados. O OData é publicado pela Microsoft em Padrões OASIS (Organização para o Avanço de Padrões em Informação Estruturadas) para que qualquer pessoa que quiser possa criar servidores, clientes ou ferramentas sem royalties ou restrições".*

### <a name="three-critical-pieces-that-have-to-be-defined-by-the-csdl-are"></a>As Três Partes Essenciais que precisam ser definidas pela CSDL são:
* O **ponto de extremidade** do Provedor de Serviço, o URI (Endereço da Web) do Serviço
* Os **parâmetros de dados** passados como entrada para o Provedor de Serviços. As definições dos parâmetros enviados ao serviço do Provedor de Conteúdo até o tipo de dados.
* **Esquema** dos dados retornados ao Serviço Solicitante. O esquema dos dados que está sendo entregue pelo serviço do Provedor de Conteúdo, incluindo o contêiner, as coleções/tabelas, as variáveis/colunas e os tipos de dados.

O diagrama a seguir mostra uma visão geral do fluxo de onde o cliente insere a declaração OData (chamada para o serviço Web do provedor de conteúdo) a fim de obter os resultados/dados de volta.

  ![desenho](media/marketplace-publishing-data-service-creation-odata-mapping/figure-2.png)

### <a name="steps"></a>Etapas:
1. O cliente envia a solicitação por meio da chamada do Serviço, completa com Parâmetros de Entrada definidos no XML, para o Azure Marketplace
2. A CSDL é usada para validar a chamada de Serviço.
   * Em seguida, a Chamada de Serviço Formatada é enviada para o Serviço de Provedores de Conteúdo pelo Azure Marketplace
3. O Serviço Web executa a ação do Verbo Http (isto é, GET). Os dados são retornados ao Azure Marketplace, onde os dados solicitados (se houver) são expostos no formato XML para o cliente usando o mapeamento definido na CSDL.
4. Os dados (se houver) são enviados ao Cliente em formato XML ou JSON

## <a name="definitions"></a>Definições
### <a name="odata-atom-pub"></a>ATOM pub OData
Uma extensão para ATOM pub onde cada entrada representa uma linha de um conjunto de resultados. A parte do conteúdo da entrada foi aprimorada para conter os valores da linha, como pares de chave-valor. Encontre mais informações aqui: [https://www.odata.org/documentation/odata-version-3-0/atom-format/](https://www.odata.org/documentation/odata-version-3-0/atom-format/)

### <a name="csdl---conceptual-schema-definition-language"></a>CSDL - Linguagem de Definição de Esquema Conceitual
Permite definir funções (SPROCs) e entidades expostas por meio de um banco de dados. Encontre mais informações aqui: [http://msdn.microsoft.com/library/bb399292.aspx](http://msdn.microsoft.com/library/bb399292.aspx)  

> [!TIP]
> Clique na lista suspensa **outras versões** e selecione uma versão, se você não vir o artigo.
> 
> 

### <a name="edm---entry-data-model"></a>EDM - Modelo de Dados de Entrada
* Visão geral: [http://msdn.microsoft.com/library/vstudio/ee382825(v=vs.100).aspx][OverviewLink]

[OverviewLink]:http://msdn.microsoft.com/library/vstudio/ee382825(v=vs.100).aspx
* Visualização: [http://msdn.microsoft.com/library/aa697428(v=vs.80).aspx][PreviewLink]

[PreviewLink]:http://msdn.microsoft.com/library/aa697428(v=vs.80).aspx
* Tipos de dados: [http://msdn.microsoft.com/library/bb399548(v=VS.100).aspx][DataTypesLink]

[DataTypesLink]:http://msdn.microsoft.com/library/bb399548(v=VS.100).aspx

Veja a seguir o fluxo detalhado da Esquerda para a Direita de onde o cliente insere a declaração do OData (chamada ao serviço Web do provedor de conteúdo) a fim de obter os resultados/dados de volta:

  ![desenho](media/marketplace-publishing-data-service-creation-odata-mapping/figure-3.png)

## <a name="csdl-basics"></a>Noções básicas de CSDL
Uma CSDL (Linguagem de Definição de Esquema Conceitual) é uma especificação que define como descrever o serviço Web ou o serviço de banco de dados em argumentação XML comum no Azure Marketplace. A CSDL descreve as partes críticas que **possibilitam a transferência de dados da Fonte de Dados para o Azure Marketplace.** As principais partes estão descritas aqui:

* Informações de interface que descrevem todas as funções disponíveis publicamente (Nó FunctionImport)
* Informações de tipo de dados para todas as solicitações (entrada) e respostas (saídas) de mensagem (nós EntitySet/EntityContainer/EntityType)
* Informações de associação sobre o protocolo de transporte a ser usado (Nó de Cabeçalho)
* Informações de endereço para localizar o serviço especificado (atributo BaseURI)

Em resumo, a CSDL representa um contrato de independente de plataforma, e de linguagem, entre o solicitante do serviço e o provedor do serviço. Usando a CSDL, um cliente pode localizar um serviço Web/banco de dados e chamar qualquer uma de suas funções disponíveis publicamente.

### <a name="relating-a-csdl-to-a-database-or-a-collection"></a>Relacionando uma CSDL a um banco de dados ou a uma coleção
**A especificação da CSDL**

A CSDL é uma gramática XML para descrição de um serviço Web. A própria especificação é dividida em quatro elementos principais: EntitySet, FunctionImport; NameSpace e EntityType.

Para facilitar a compreensão dessa abstração, vamos relacionar uma CSDL com uma tabela.

Lembre-se;

  a CSDL representa um contrato independente de plataforma, e de linguagem, entre o **solicitante do serviço** e o **provedor do serviço**. Com a CSDL, um **cliente** pode localizar um **serviço Web/serviço de banco de dados** e chamar suas **funções** disponíveis publicamente.

Para um Serviço de Dados, as quatro partes de uma CSDL podem ser vistas em termos de um Banco de Dados, uma Tabela, uma Coluna e um Procedimento Armazenado.

Relacione essas partes da seguinte maneira para um Serviço de Dados:

* EntityContainer ~= Database
* EntitySet ~= Table
* EntityType ~= Columns
* FunctionImport ~= Stored Procedure

**Verbos HTTP permitidos**

* GET – retorna valores do banco de dados (retorna uma Coleção)
* POST – usado para passar dados e, opcionalmente, retornar valores do banco de dados (criar uma nova entrada na coleção, URI/id de retorno)
* DELETE – exclui dados do banco de dados (exclui uma coleção)
* PUT – atualizar dados em um banco de dados (substituir uma coleção ou criar uma)

## <a name="metadatamapping-document"></a>Documento de metadados/mapeamento
O documento de metadados/mapeamento é usado para mapear os serviços Web existentes de um provedor de conteúdo para que possam ser expostos como um serviço Web do OData pelo sistema do Azure Marketplace. Ele se baseia na CSDL e implementa algumas extensões na CSDL a fim de acomodar as necessidades de REST com base em serviços Web expostos por meio do Azure Marketplace. As extensões podem ser encontradas no namespace [http://schemas.microsoft.com/dallas/2010/04](http://schemas.microsoft.com/dallas/2010/04) .

Veja a seguir um exemplo de CSDL: (copie e cole o exemplo de CSDL abaixo em um editor de XML e altere-o para corresponder ao seu Serviço.  Em seguida, cole no Mapeamento de CSDL, na guia DataService, ao criar seu serviço no [Portal de Publicação do Azure Marketplace](https://publish.windowsazure.com)).

**Termos:** relacionar os termos de CSDL aos termos da PPUI (interface do usuário do [Portal de Publicação](https://publish.windowsazure.com) ).

* O "Título" da oferta na PPUI está relacionado a MyWebOffer
* MyCompany na PPUI está relacionado ao **Nome de Exibição do Publicador** na interface do usuário do [Microsoft Developer Center](http://dev.windows.com/registration?accountprogram=azure)
* A API está relacionado a um Serviço Web ou de Dados (um Plano na PPUI)

**Hierarquia:** uma Empresa (Provedor de Conteúdo) tem Ofertas com Planos, ou seja, Serviços, que se alinham a uma API.

### <a name="webservice-csdl-example"></a>Exemplo de CSDL de Serviço Web
Conecta-se a um serviço que expõe um ponto de extremidade de aplicativo Web (como um aplicativo C#)

        <?xml version="1.0" encoding="utf-8"?>
        <!-- The namespace attribute below is used by our system to generate C#. You can change “MyCompany.MyOffer” to something that makes sense for you, but change “MyOffer” consistently throughout the document. -->
        <Schema Namespace="MyCompany.MyWebOffer" Alias="MyOffer" xmlns="http://schemas.microsoft.com/ado/2009/08/edm" xmlns:d="http://schemas.microsoft.com/dallas/2010/04" >
        <!-- EntityContainer groups all the web service calls together into a single offering. Every web service call has a FunctionImport definition. -->
          <EntityContainer Name="MyOffer">
        <!-- EntitySet is defined for CSDL compatibility reasons, not required for ReturnType=”Raw”
        @Name is used as reference by FunctionImport @EntitySet. And is used in the customer facing UI as name of the Service.
        @EntityType is used to point at the type definition near the bottom of this file. -->
            <EntitySet Name="MyEntities" EntityType="MyOffer.MyEntityType" />
        <!-- Add a FunctionImport for every service method. Multiple FunctionImports can share a single return type (EntityType). -->
        <!-- ReturnType is either Raw() for a stream or Collection() for an Atom feed. Ex. of Raw: ReturnType=”Raw(text/plain)” -->
        <!—EntitySet is the entityset defined above, and is needed if ReturnType is not Raw -->
        <!-- BaseURI attribute defines the service call, replace & with the encode value (&amp;).
        In the input name value pairs {param} represents passed in value.
        Or the value can be hard coded as with AccountKey. -->
        <!-- AllowedHttpMethods optional (default = “GET”), allows the CSDL to specifically specify the verb of the service, “Get”, “Post”, “Put”, or “Delete”. -->
        <!-- EncodeParameterValues, True encodes the parameter values, false does not. -->
        <!-- BaseURI is translated into an URITemplate which defines how the web service call is exposed to marketplace customers.
        Ex. https://api.datamarket.azure.com/mycompany/MyOfferPlan?name={name}
        BaseURI is XML encoded, the {...} point to the parameters defined below.
        Marketplace will read the parameters from this URITemplate and fill the values into the corresponding parameters of the BaseUri or RequestBody (below) during calls to your service.  
        It is okay for @d:BaseUri to include information only for Marketplace consumption, it will not be exposed to end users. i.e. the hardcoded AccountKey in the above BaseURI does not show up in the client facing URITemplate. -->
            <FunctionImport Name="MyWebServiceMethod"
                            EntitySet="MyEntities"
                            ReturnType="Collection(MyOffer.MyEntityType)"
        d:AllowedHttpMethods="GET"
        d:EncodeParameterValues="true"
        d:BaseUri="http://services.organization.net/MyServicePath?name={name}&amp;AccountKey=22AC643">
        <!-- Definition of the RequestBody is only required for HTTP POST requests and is optional for HTTP GET requests. -->
        <d:RequestBody d:httpMethod="POST">
                <!-- Use {} for placeholders to insert parameters. -->
                <!-- This example uses SOAP formatting, but any POST body can be used. -->
            <!-- This example shows how to pass userid and password via the header -->
                <![CDATA[<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:MyOffer="http://services.organization.net/MyServicePath">
                  <soapenv:Header/>
                  <soapenv:Body>
                    <MyOffer:ws_MyWebServiceMethod>
                      <myWebServiceMethodRequest>
                        <!--This information is not exposed to end users. -->
                        <UserId>userid</UserId>
                        <Password>password</Password>
                        <!-- {name} is replaced with the value read from @d:UriTemplate above -->
                        <Name>{name}</Name>
                        <!-- Parameters can be used more than once and are not limited to appearing as the value of an element -->
                        <CustomField Name="{name}" />
                        <MyField>Static content</MyField>
                      </myWebServiceMethodRequest>
                    </MyOffer:ws_MyWebServiceMethod>
                  </soapenv:Body>
                </soapenv:Envelope>      
              ]]>
        </d:RequestBody>
        <!-- Title, Rights and Description are optional and used to specify values to insert into the ATOM feed returned to the end user.  You can specify the element to contain a fixed message by providing a value for the element (this is the default value).  @d:Map is an XPath expression that points into the response returned by your service and is optional.  -->
        <d:Title d:Map="/MyResponse/Title">Default title.</d:Title>
        <d:Rights>© My copyright. This is a fixed response. It is okay to also add a d:Map attribute to override this text.</d:Rights>
        <d:Description d:Map="/MyResponse/Description"></d:Description>
        <d:Namespaces>
        <d:Namespace d:Prefix="p"  d:Uri="http://schemas.organization.net/2010/04/myNamespace" />
        <d:Namespace d:Prefix="p2" d:Uri="http://schemas.organization.net/2010/04/MyNamespace2" />
        </d:Namespaces>
        <!-- Parameters of the web service call:
        @Name should match exactly (case sensitive) the {…} placeholders in the @d:BaseUri, @d:UriTemplate, and d:RequestBody, i.e. “name” parameter in above BaseURI.
        @Mode is always "In", compatibility with CSDL
        @Type is the EDM.SimpleType of the parameter, see http://msdn.microsoft.com/library/bb399548(v=VS.100).aspx
        @d:Nullable indicates whether the parameter is required.
        @d:Regex - optional, attribute to describe the string, limiting unwanted input at the entry of the system
        @d:Description - optional, is used by Service Explorer as help information
        @d:SampleValues - optional, is used by Service Explorer as help information. Multiple Sample values are separated by '|', e.g. "804735132|234534224|23409823234"
        @d:Enum - optional for string type. Contains an enumeration of possible values separated by a '|', e.g. d:enum="english|metric|raw". Will be converted in a dropdown list in the Service Explorer.
        -->
        <Parameter name="name" Mode="In" Type="String" d:Nullable="false" d:Regex="^[a-zA-Z]*$" d:Description="A name that cannot contain any spaces or non-alpha non-English characters"
        d:Enum="George|John|Thomas|James"
        d:SampleValues="George"/>
        <Parameter Name=" AccountKey" Mode="In" Type="String" d:Nullable="false" />

        <!-- d:ErrorHandling is an optional element. Use it define standardized errors by evaluating the service response. -->
        <d:ErrorHandling>
        <!-- Any number of d:Condition elements are allowed, they are evaluated in the order listed.
        @d:Match is an Xpath query on the service response, it should return true or false where true indicates an error.
        @d:httpStatusCode is the error code to return if an response matches the error.
        @d:errorMessage is the user friendly message to return when an error occurs.
        -->
        <d:Condition d:Match="/Result/ErrorMessage[text()='Invalid token']" d:HttpStatusCode="403" d:ErrorMessage="User cannot connect to the service." />
        </d:ErrorHandling>
           </FunctionImport>

            <!-- The EntityContainer defines the output data schema -->
        </EntityContainer>
        <!-- The EntityType @d:Map defines the repeating node (an XPath query) in the response (output data schema). -->
        <!-- If these nodes are outside a namespace, add the prefix in the xpath. -->
        <!--
        @Name - define your user readable name, will become an XML element in the ATOM feed, so comply with the XML element naming restrictions (no spaces or other illegal characters).
        @Type is the EDM.SimpleType of the parameter, see http://msdn.microsoft.com/library/bb399548(v=VS.100).aspx.
        @d:Map uses an Xpath query to point at the location to extract the content from your services response.
        The "." is relative to the repeating node in the EntityType @d:Map Xpath expression.
        -->
            <EntityType Name="MyEntityType" d:Map="/MyResponse/MyEntities">
        <Property Name="ID"    d:IsPrimaryKey="True" Type="Int32"    Nullable="false" d:Map="./Remaining[@Amount]"/>
        <Property Name="Amount"    Type="Double"    Nullable="false" d:Map="./Remaining[@Amount]"/>
        <Property Name="City"    Type="String"    Nullable="false" d:Map="./City"/>
        <Property Name="State"    Type="String"    Nullable="false" d:Map="./State"/>
        <Property Name="Zip"    Type="Int32"    Nullable="false" d:Map="./Zip"/>
        <Property Name="Updated"    Type="DateTime"    Nullable="false" d:Map="./Updated"/>
        <Property Name="AdditionalInfo" Type="String" Nullable="true"
        d:Map="./Info/More[1]"/>
            </EntityType>
        </Schema>

> [!TIP]
> Veja mais exemplos de Serviço Web CSDL no artigo [Exemplos de mapeamento de um serviço Web existente para OData por meio de CSDLs](marketplace-publishing-data-service-creation-odata-mapping-examples.md)
> 
> 

### <a name="dataservice-csdl-example"></a>Exemplo de CSDL de DataService
Conecta-se a um serviço que expõe uma tabela ou modo de exibição de banco de dados como um ponto de extremidade. O exemplo abaixo mostra duas APIs de banco de dados com base na CSDL da API (pode usar modos de exibição em vez de tabelas).

        <?xml version="1.0"?>
        <!-- The namespace attribute below is used by our system to generate C#. You can change “MyCompany.MyOffer” to something that makes sense for you, but change “MyOffer” consistently throughout the document. -->
        <Schema Namespace="MyCompany.MyDataOffer" Alias="MyOffer" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ado/2009/08/edm">
        <!-- EntityContainer groups all the data service calls together into a single offering. Every web service call has a FunctionImport definition. -->
        <EntityContainer Name="MyOfferContainer">
        <!-- EntitySet is defined for CSDL compatibility reasons, not required for ReturnType=”Raw”
            Think of the EntitySet as a Service
        @Name is used in the customer facing UI as name of the Service.
        @EntityType is used to point at the type definition (returned set of table columns). -->
        <EntitySet Name="CompanyInfoEntitySet" EntityType="MyOffer.CompanyInfo" />
        <EntitySet Name="ProductInfoEntitySet" EntityType="MyOffer.ProductInfo" />
        </EntityContainer>
        <!-- EntityType defines result (output); the table (or view) and columns to be returned by the data service.)
            Map is the schema.tabel or schema.view
            dals.TableName is the table Name
            Name is the name identifier for the EntityType and the Name of the service exposed to the client via the UI.
            dals:IsExposed determines if the table schema is exposed (generally true).
            dals:IsView (optional) true if this is based on a view rather than a table
            dals:TableSchema is the schema name of the table/view
        -->
        <EntityType
        Map="[dbo].[CompanyInfo]"
        dals:TableName="CompanyInfo"
        Name="CompanyInfo"
        dals:IsExposed="true"
        dals:IsView="false"
        dals:TableSchema="dbo"
        xmlns:dals="http://schemas.microsoft.com/dallas/2010/04">
        <!-- Property defines the column properties and the output of the service.
            dals:ColumnName is the name of the column in the table /view.
            Type is the emd.SimpleType
            Nullable determines if NULL is a valid output value
            dals.CharMaxLenght is the maximum length of the output value
            Name is the name of the Property and is exposed to the client facing UI
            dals:IsReturned is the Boolean that determines if the Service exposes this value to the client.
            IsQueryable is the Boolean that determines if the column can be used in a database query
            (For data Services: To improve Performance make sure that columns marked ISQueryable=”true” are in an index.)
            dals:OrdinalPosition is the numerical position x in the table or the View, where x is from 1 to the number of columns in the table.
            dals:DatabaseDataType is the data type of the column in the database, i.e. SQL data type dals:IsPrimaryKey indicates if the column is the Primary key in the table/view.  (The columns marked ISPrimaryKey are used in the Order by clause when returning data.)
        -->
        <Property dals:ColumnName="data" Type="String" Nullable="true" dals:CharMaxLength="-1" Name="data" dals:IsReturned="true" dals:IsQueryable="false" dals:IsPrimaryKey="false" dals:OrdinalPosition="3" dals:DatabaseDataType="nvarchar" />
        <Property dals:ColumnName="id" Type="Int32" Nullable="false" Name="id" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="true" dals:OrdinalPosition="1" dals:NumericPrecision="10" dals:DatabaseDataType="int" />
        <Property dals:ColumnName="ticker" Type="String" Nullable="true" dals:CharMaxLength="10" Name="ticker" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="false" dals:OrdinalPosition="2" dals:DatabaseDataType="nvarchar" />
        </EntityType>
        <EntityType Map="[dbo].[ProductInfo]" dals:TableName="ProductInfo" Name="ProductInfo" dals:IsExposed="true" dals:IsView="false" dals:TableSchema="dbo" xmlns:dals="http://schemas.microsoft.com/dallas/2010/04">
        <Property dals:ColumnName="companyid" Type="Int32" Nullable="true" Name="companyid" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="false" dals:OrdinalPosition="2" dals:NumericPrecision="10" dals:DatabaseDataType="int" />
        <Property dals:ColumnName="id" Type="Int32" Nullable="false" Name="id" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="true" dals:OrdinalPosition="1" dals:NumericPrecision="10" dals:DatabaseDataType="int" />
        <Property dals:ColumnName="product" Type="String" Nullable="true" dals:CharMaxLength="50" Name="product" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="false" dals:OrdinalPosition="3" dals:DatabaseDataType="nvarchar" />
        </EntityType>
        </Schema>

## <a name="see-also"></a>Consulte também
* Se estiver interessado em aprender e em compreender os nós específicos e seus parâmetros, leia este artigo [Nós do mapeamento OData de Serviço de Dados](marketplace-publishing-data-service-creation-odata-mapping-nodes.md) para obter definições, explicações, exemplos e contexto de casos de uso.
* Se estiver interessado em examinar exemplos, leia este artigo [Exemplos de mapeamento OData de Serviço de Dados](marketplace-publishing-data-service-creation-odata-mapping-examples.md) para ver um código de exemplo e compreender a sintaxe do código e o contexto.
* Para retornar ao caminho indicado para a publicação de um Serviço de Dados no Azure Marketplace, leia este artigo [Guia de publicação de Serviço de Dados](marketplace-publishing-data-service-creation.md).

