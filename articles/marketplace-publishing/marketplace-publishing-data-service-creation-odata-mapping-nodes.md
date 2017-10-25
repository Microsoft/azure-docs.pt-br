---
title: "Guia para a criação de um Serviço de Dados para o Marketplace | Microsoft Docs"
description: "Instruções detalhadas sobre como criar, certificar e implantar um Serviço de Dados para compra no Azure Marketplace."
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 107baab2-5828-4158-abdf-59a580c80d37
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/26/2016
ms.author: hascipio; avikova
ms.openlocfilehash: 8ff76ea21ba684ae2a2afcb74d66b4912d7be053
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="understanding-the-nodes-schema-for-mapping-an-existing-web-service-to-odata-through-csdl"></a>Noções básicas sobre o esquema de nós para mapear um serviço web existente para OData por meio de CSDL
> [!IMPORTANT]
> **Neste momento, não estamos mais realizando a integração de novos editores de Serviço de Dados. Novos serviços de dados não serão ser aprovados para listagem.** Se você tiver um aplicativo de negócios de SaaS que quer publicar no AppSource, encontre mais informações [aqui](https://appsource.microsoft.com/partners). Se você tiver aplicativos de IaaS ou serviços de desenvolvedor para publicar no Azure Marketplace, encontre mais informações [aqui](https://azure.microsoft.com/marketplace/programs/certified/).
>
>

Este documento ajudará a esclarecer a estrutura de nó para o mapeamento de um protocolo OData para CSDL. É importante observar que a estrutura de nó é um XML bem estruturado. Então o esquema raiz, pai e filho é aplicável ao desenhar o seu mapeamento de OData.

## <a name="ignored-elements"></a>Elementos ignorados
A seguir estão os elementos CSDL de alto nível (nós XML) que não serão usados pelo back-end do Azure Marketplace durante a importação de metadados do serviço web. Eles podem estar presentes, mas serão ignorados.

| Elemento | Escopo |
| --- | --- |
| Usando o elemento |O nó, sub-nós e todos os atributos |
| Elemento de documentação |O nó, sub-nós e todos os atributos |
| ComplexType |O nó, sub-nós e todos os atributos |
| Elemento de associação |O nó, sub-nós e todos os atributos |
| Propriedade estendida |O nó, sub-nós e todos os atributos |
| EntityContainer |Somente os seguintes atributos são ignorados: *extends* e *AssociationSet* |
| Esquema |Somente os seguintes atributos são ignorados: *Namespace* |
| FunctionImport |Somente os seguintes atributos são ignorados: *Mode* (o valor padrão de ln é considerado) |
| EntityType |Os seguintes sub-nós são ignorados: *Key* e *PropertyRef* |

A seguir está a descrição detalhada das alterações (elementos adicionados e ignorados) em vários nós de XML CSDL.

## <a name="functionimport-node"></a>Nó FunctionImport
Um nó FunctionImport representa uma URL (ponto de entrada) que expõe um serviço para o usuário final. O nó permite descrever como a URL é abordada, quais parâmetros estão disponíveis para o usuário final e como esses parâmetros são fornecidos.

Há detalhes sobre esse nó [aqui][MSDNFunctionImportLink](https://msdn.microsoft.com/library/cc716710.aspx)

A seguir estão os atributos adicionais (ou adições aos atributos) que são expostos pelo nó FunctionImport:

**d:BaseUri** - O modelo de URI do recurso REST que é exposto no Marketplace. O Marketplace usa o modelo para construir consultas ao serviço web de REST. O modelo de URI contém espaços reservados para os parâmetros na forma {parameterName}, onde o parameterName é o nome do parâmetro. Ex.: apiVersion={apiVersion}.
Os parâmetros podem ser exibidos como parâmetros de URI ou como parte do caminho do URI. É obrigatórios que sempre apareçam no caminho (não podem ser marcados como anuláveis). *Exemplo:* `d:BaseUri="http://api.MyWeb.com/Site/{url}/v1/visits?start={start}&amp;end={end}&amp;ApiKey=3fadcaa&amp;Format=XML"`

**Nome** - o nome da função importada.  Não pode ser o mesmo que outros nomes definidos no CSDL.  Ex.: Name="GetModelUsageFile"

**EntitySet** *(opcional)* - se a função retorna uma coleção de tipos de entidade, o valor de **EntitySet** devem ser o conjunto de entidades ao qual pertence a coleção. Caso contrário, o atributo **EntitySet** não deve ser usado. *Exemplo:* `EntitySet="GetUsageStatisticsEntitySet"`

**ReturnType** *(opcional)* -Especifica o tipo de elementos retornados por um URI.  Não use esse atributo se a função não retorna um valor. A seguir estão os tipos compatíveis:

* **Coleção (<Entity type name>)**: especifica uma coleção de tipos de entidade definidos. O nome está presente no atributo Name do nó EntityType. Um exemplo é Collection(WXC.HourlyResult).
* **Brutos (<mime type>)**: especifica um documento/blob bruto que é retornado para o usuário. Um exemplo é Raw(image/jpeg) outros exemplos:

  * ReturnType="Raw(text/plain)"
  * ReturnType="Collection(sage.DeleteAllUsageFilesEntity)"*

**d:Paging** - Especifica como a paginação é tratada pelo recurso REST. Os valores de parâmetro são usados entre chave, por exemplo, page={$page}&itemsperpage={$size} As opções disponíveis são:

* **None:** nenhuma paginação está disponível
* **Skip:** a paginação é expressa por meio de uma lógica "skip" e "take" (superior). Skip pula os elementos de M e take retorna os elementos N seguintes. Parameter value: $skip
* **Take:** Take retorna os elementos N seguintes. Parameter value: $take
* **PageSize:** a paginação é expressa por meio de uma página lógica e o tamanho (itens por página). A página representa a página atual que é retornada. Parameter value: $page
* **Size:** tamanho representa o número de itens retornados para cada página. Parameter value: $size

**d:AllowedHttpMethods** *(Opcional)* -Especifica qual verbo é tratado pelo recurso REST. Além disso, restringe o verbo aceito para o valor especificado.  Padrão = POST.  *Exemplo:* `d:AllowedHttpMethods="GET"` As opções disponíveis são:

* **GET:** normalmente é usado para retornar dados
* **POST:** normalmente é usado para inserir novos dados
* **PUT:** normalmente é usado para atualizar dados
* **DELETE:** usado para excluir dados

Os nós filho adicionais (não cobertos pela documentação CSDL) dentro do nó FunctionImport são:

**d:RequestBody** *(Opcional)* - O corpo da solicitação é usado para indicar que a solicitação espera que o corpo da mesma seja enviado. Os parâmetros podem ser fornecidos no corpo da solicitação. Eles são expressos entre chaves, por exemplo, {parameterName}. Esses parâmetros são mapeados da entrada do usuário para o corpo da solicitação que é transferido para o serviço do provedor de conteúdo. O elemento requestBody possui um atributo com nome httpMethod. O atributo permite dois valores:

* **POST:** usado se a solicitação é um HTTP POST
* **GET:** usado se a solicitação é um HTTP GET

    Exemplo:

        `<d:RequestBody d:httpMethod="POST">
        <![CDATA[
        <req1:Request xmlns:r1="http://schemas.mysite.com//generic/requests/1" Version="1.0">
        <req1:Query>{Query}</req1:Query><req1:AppId>D453474</req1:AppId>
        <req:DestinationSchemas><req1:Schema>Generic.RequestResponse[1.0]</req1:Schema>
        </req1:DestinationSchemas>
        </req1: Request>
        ]]>
        </d:RequestBody>`

**d:Namespaces** e **d:Namespace** - esse nó descreve os namespaces que são definidos no XML que é retornado pela função de importação (ponto de extremidade do URI). O XML retornado pelo serviço de back-end pode conter qualquer número de namespaces para diferenciar o conteúdo que é retornado. **Todos esses namespaces, se usados em consultas d:Map ou d:Match XPath precisam ser listados.** O nó d:Namespaces contém um conjunto/lista  de nós d:Namespace. Cada um deles lista um namespace usado na resposta do serviço de back-end. A seguir estão os atributos do nó d:Namespace:

* **d:Prefix:** O prefixo para o namespace, conforme mostrado nos resultados XML retornados pelo serviço, por exemplo, f:FirstName, f:LastName, onde f é o prefixo.
* **d:Uri:** O URI completo do namespace usado no documento de resultado. Representa o valor resolvido do prefixo em tempo de execução.

**d:ErrorHandling** *(Opcional)* - Esse nó contém condições para tratamento de erros. Cada uma das condições é validada em relação ao resultado que é retornado pelo serviço do provedor de conteúdo. Se uma condição corresponde ao código de erro HTTP proposto uma mensagem de erro é retornada para o usuário final.

**d:ErrorHandling** *(Opcional)* e **d:Condition** *(Opcional)* - Um nó de condição contém uma condição que é verificada no resultado retornado pelo serviço do provedor de conteúdo. Os atributos a seguir são **obrigatórios** :

* **d:Match:** Uma expressão XPath que valida se um determinado nó/valor está presente no XML de saída do provedor de conteúdo. O XPath é executado em relação à saída e deve retornar true se a condição é uma correspondência ou false se não houver uma correspondência.
* **d:HttpStatusCode:** O código de status HTTP que deve ser retornado pelo Marketplace no caso da condição ter uma correspondência. O Marketplace sinaliza os erros ao usuário por meio de códigos de status HTTP. Uma lista de códigos de status HTTP estão disponíveis em https://pt.wikipedia.org/wiki/Lista_de_c%C3%B3digos_de_estado_HTTP
* **d:ErrorMessage:** A mensagem de erro é retornada – com o código de status HTTP – para o usuário final. Essa deve ser uma mensagem de erro amigável que não contém nenhum segredo.

**d:Title** *(Opcional)* - Permite descrever o título da função. O valor para o título é proveniente de

* O atributo de mapa opcional (um xpath) que especifica onde encontrar o título na resposta retornada da solicitação de serviço.
* -Ou - o título especificado como o valor do nó.

**d:Rights** *(Opcional)* - Os direitos (por exemplo, direitos autorais) associados à função. O valor para os direitos é proveniente de:

* O atributo de mapa opcional (um xpath) que especifica onde encontrar os direitos na resposta retornada da solicitação de serviço.
* -Ou - Os direitos especificados como o valor do nó.

**d:Description** *(Opcional)* - Uma breve descrição para a função. O valor para a descrição é proveniente de

* O atributo de mapa opcional (um xpath) que especifica onde encontrar a descrição na resposta retornada da solicitação de serviço.
* -Ou - A descrição especificada como o valor do nó.

**d:EmitSelfLink** - *Veja o exemplo acima "FunctionImport para 'Paginação' através dos dados retornados"*

**d:EncodeParameterValue** - Extensão opcional do OData

**d:QueryResourceCost** - Extensão opcional do OData

**d:Map** - Extensão opcional do OData

**d:Headers** - Extensão opcional do OData

**d:Headers** - Extensão opcional do OData

**d:Value** - Extensão opcional do OData

**d:HttpStatusCode** - Extensão opcional do OData

**d:ErrorMessage** - Extensão opcional do OData

## <a name="parameter-node"></a>Nó do parâmetro
Esse nó representa um parâmetro que é exposto como parte do modelo de URI / corpo da solicitação que foi especificado no nó FunctionImport.

Uma página de documento detalhada muito útil sobre o nó de "Elemento do parâmetro" pode ser encontrada [aqui](http://msdn.microsoft.com/library/ee473431.aspx) (use a lista suspensa **Outra versão** para selecionar uma versão diferente, se necessário, para exibir a documentação). *Exemplo:* `<Parameter Name="Query" Nullable="false" Mode="In" Type="String" d:Description="Query" d:SampleValues="Rudy Duck" d:EncodeParameterValue="true" MaxLength="255" FixedLength="false" Unicode="false" annotation:StoreGeneratedPattern="Identity"/>`

| Atributo de parâmetro | Obrigatório | Valor |
| --- | --- | --- |
| Nome |Sim |O nome do parâmetro. Diferencia maiúsculas de minúsculas!  Corresponde o caso de BaseUri. **Exemplo:** `<Property Name="IsDormant" Type="Byte" />` |
| Tipo |Sim |O tipo de parâmetro. O valor deve ser um **EDMSimpleType** ou um tipo complexo que está dentro do escopo do modelo. Para obter mais informações, consulte "Os 6 tipos de propriedade/parâmetro compatíveis".  (Diferencia maiúsculas de minúsculas! O primeiro caractere é maiúsculo, os demais são minúsculos.)  Confira também [Tipos de modelo conceituais (CSDL)][MSDNParameterLink](http://msdn.microsoft.com/library/bb399548.aspx). **Exemplo:** `<Property Name="LimitedPartnershipID " Type="Int32" />` |
| Mode |Não |**In**, Out ou InOut dependendo se o parâmetro é uma entrada, saída ou parâmetro de entrada/saída. (Somente "IN" está disponível no Azure Marketplace.) **Exemplo:** `<Parameter Name="StudentID" Mode="In" Type="Int32" />` |
| MaxLength |Não |O comprimento máximo permitido do parâmetro. **Exemplo:** `<Property Name="URI" Type="String" MaxLength="100" FixedLength="false" Unicode="false" />` |
| Precisão |Não |A precisão do parâmetro. **Exemplo:** `<Property Name="PreviousDate" Type="DateTime" Precision="0" />` |
| Escala |Não |A escala do parâmetro. **Exemplo:** `<Property Name="SICCode" Type="Decimal" Precision="10" Scale="0" />` |

Estes são os atributos que foram adicionados à especificação do CSDL:

| Atributo de parâmetro | Descrição |
| --- | --- |
| **d:Regex** *(Opcional)* |Uma instrução de regex usada para validar o valor de entrada para o parâmetro. Se o valor de entrada não corresponde à instrução, o valor será rejeitado. Isso também permite especificar um conjunto de possíveis valores, por exemplo, ^[0-9]+? $ para permitir somente números. **Exemplo:** `<Nome do Parâmetro="name" Modo="In" Tipo="String" d:Nullable="false" d:Regex="^[a-zA-Z]*$" d:Descrição="Um nome que não pode conter espaços ou caracteres não alfanumérico e não inglês" d:SampleValues="George |
| **d:Enum** *(Opcional)* |Uma lista separada por barra vertical de valores válidos para o parâmetro. O tipo dos valores precisa coincidir com o tipo do parâmetro definido. Exemplo: `inglês |
| **d:Nullable** *(Opcional)* |Permite definir se um parâmetro pode ser nulo. O padrão é true. No entanto, os parâmetros que são expostos como parte do caminho do modelo de URI não podem ser nulos. Quando o atributo é definido como false para esses parâmetros – a entrada do usuário é substituída. **Exemplo:** `<Parameter Name="BikeType" Type="String" Mode="In" Nullable="false"/>` |
| **d:SampleValue** *(Opcional)* |Um valor de exemplo para exibir como uma observação para o cliente na interface do usuário.  É possível adicionar vários valores usando uma lista separada por pipe, ou seja `a |

## <a name="entitytype-node"></a>Nó EntityType
Esse nó representa um dos tipos que são retornados do Marketplace para o usuário final. Ele também contém o mapeamento da saída que é retornado pelo serviço do provedor de conteúdo para os valores que são retornados para o usuário final.

Os detalhes sobre esse nó podem ser encontrados [aqui](http://msdn.microsoft.com/library/bb399206.aspx) (use a lista suspensa **Outra versão** para selecionar uma versão diferente, se necessário, para exibir a documentação).

| Nome do atributo | Obrigatório | Valor |
| --- | --- | --- |
| Nome |Sim |O nome do tipo de entidade. **Exemplo:** `<EntityType Name="ListOfAllEntities" d:Map="//EntityModel">` |
| BaseType |Não |O nome de outro tipo de entidade que é o tipo básico do tipo de entidade que está sendo definido. **Exemplo:** `<EntityType Name="PhoneRecord" BaseType="dqs:RequestRecord">` |

Estes são os atributos que foram adicionados à especificação do CSDL:

**d:Map** - Uma expressão XPath executada em relação à saída do serviço. O pressuposto é de que a saída do serviço contenha um conjunto de elementos que se repitam, como ATOM feed onde há um conjunto de nós de entrada que se repetem. Cada um desses nós de repetição contém um registro. O XPath é especificado, em seguida, para apontar para o nó de repetição específico no resultado do serviço do provedor de conteúdo que contém os valores para um registro específico. Exemplo de saída do serviço:

        `<foo>
          <bar> … content … </bar>
          <bar> … content … </bar>
          <bar> … content … </bar>
        </foo>`

A expressão XPath deve ser /foo/bar porque cada nó de barra é o nó repetido na saída e contém o conteúdo real que é retornado para o usuário final.

**Key** - Esse atributo é ignorado pelo Marketplace. Em geral, os serviços web baseado em REST não expõem uma chave primária.

## <a name="property-node"></a>Nó de propriedade
Esse nó contém uma propriedade do registro.

Os detalhes sobre esse nó podem ser encontrados em [http://msdn.microsoft.com/library/bb399546.aspx](http://msdn.microsoft.com/library/bb399546.aspx) (Use a lista suspensa **Outra versão** para selecionar uma versão diferente, se for necessário, para exibir a documentação). *Exemplo:* `<EntityType Name="MetaDataEntityType" d:Map="/MyXMLPath">
        <Property Name="Name"     Type="String" Nullable="true" d:Map="./Service/Name" d:IsPrimaryKey="true" DefaultValue=”Joe Doh” MaxLength="25" FixedLength="true" />
        ...
        </EntityType>`

| AttributeName | obrigatórios | Valor |
| --- | --- | --- |
| Nome |Sim |O nome da propriedade. |
| Tipo |Sim |O tipo do valor da propriedade. O tipo de valor de propriedade deve ser um **EDMSimpleType** ou um tipo complexo (indicado por um nome totalmente qualificado) que está dentro do escopo do modelo. Para obter mais informações, consulte os tipos de modelo conceituais (CSDL). |
| Nullable |Não |**True** (o valor padrão) ou **False**, dependendo se a propriedade pode ter um valor nulo. Observação: na versão do CSDL indicado pelo namespace [http://schemas.microsoft.com/ado/2006/04/edm](http://schemas.microsoft.com/ado/2006/04/edm) , uma propriedade de tipo complexo deve ser Nullable="False". |
| DefaultValue |Não |Você deve usar o valor padrão da propriedade. |
| MaxLength |Não |O comprimento máximo do valor da propriedade. |
| FixedLength |Não |**True** ou **False** dependendo se o valor da propriedade será armazenado como uma cadeia de caracteres de comprimento de fixo. |
| Precisão |Não |Indica o número máximo de dígitos para manter o valor numérico. |
| Escala |Não |Número máximo de casas decimais para manter o valor numérico. |
| Unicode |Não |**True** ou **False**, dependendo se o valor da propriedade é armazenado como uma cadeia de caracteres Unicode. |
| Collation |Não |Uma cadeia de caracteres que especifica a sequência de agrupamento a ser usada na fonte de dados. |
| ConcurrencyMode |Não |**Nenhum** (o valor padrão) ou **Fixo**. Se o valor for definido como **Fixo**, o valor da propriedade será usado na verificação de simultaneidade otimista. |

Estes são os atributos adicionais que foram adicionados à especificação do CSDL:

**d:Map** - A expressão XPath executada em relação ao serviço de saída e extrai uma propriedade da saída. O XPath especificado é relativo ao nó de repetição que foi selecionado no XPath do nó EntityType. Também é possível especificar um XPath absoluto para permitir a inclusão de um recurso estático em cada um dos nós de saída, como por exemplo uma declaração de direitos autorais que é encontrada apenas uma vez no serviço original de saída, mas deve estar presente em cada uma das linhas na saída do OData. Exemplo do serviço:

        `<foo>
          <bar>
           <baz0>… value …</baz0>
           <baz1>… value …</baz1>
           <baz2>… value …</baz2>
          </bar>
        </foo>`

A expressão XPath aqui seria ./bar/baz0 para obter o nó baz0 do serviço do provedor de conteúdo.

**d:CharMaxLength** - Para o tipo de cadeia de caracteres, você pode especificar o comprimento máximo. Exemplo de CSDL de DataService

**d:IsPrimaryKey** - Indica se a coluna é a chave primária na tabela/exibição. Exemplo de CSDL de DataService.

**d:isExposed** - Determina se o esquema da tabela é exposto (geralmente true). Exemplo de CSDL de DataService

**d:IsView** *(Opcional)* - true se for baseado em um modo de exibição em vez de uma tabela.  Exemplo de CSDL de DataService

**d:Tableschema** - Consulte o exemplo de CSDL de DataService

**d:ColumnName** - É o nome da coluna na tabela/exibição.  Exemplo de CSDL de DataService

**d:IsReturned** - É o valor booliano que determina se o serviço expõe esse valor ao cliente.  Exemplo de CSDL de DataService

**d:IsQueryable** – é o booliano que determina se a coluna pode ser usada em uma consulta de banco de dados.   Exemplo de CSDL de DataService

**d:OrdinalPosition** - É a posição da coluna numérica de aparência, x, na tabela ou exibição, onde x vai de 1 até o número de colunas na tabela.  Exemplo de CSDL de DataService

**d:DatabaseDataType** - É o tipo de dados da coluna no banco de dados, ou seja, o tipo de dados SQL. Exemplo de CSDL de DataService

## <a name="supported-parametersproperty-types"></a>Tipos de parâmetros/propriedade compatíveis
A seguir estão os tipos compatíveis para parâmetros e propriedades. (Diferencia maiúsculas de minúsculas)

| Tipos primitivos | Descrição |
| --- | --- |
| Nulo |Representa a ausência de um valor |
| Booliano |Representa o conceito matemático de lógica de valores binários |
| Byte |Valor inteiro não assinado de 8 bits |
| DateTime |Representa a data e hora com valores que variam de 00:00:00, meia-noite de 1º de janeiro de 1753 D.C. até 23:59:59, dezembro de 9999 D.C. |
| Decimal |Representa valores numéricos com precisão e escala fixas. Esse tipo pode descrever um valor numérico de 10^255 + 1 negativo a 10^-255 - 1 positivo |
| Duplo |Representa um número de ponto flutuante com precisão de 15 dígitos que pode representar valores de intervalo aproximado de ± 2, 23E -308 a ± 1, 79E +308. **Use decimais devido ao problema de exportação do Excel** |
| Single |Representa um número de ponto flutuante com precisão de 7 dígitos que pode representar valores de intervalo aproximado de ± 1,18E -38 a ± 3,40E +38. |
| Guid |Representa um valor de identificador exclusivo de 16 bytes (128 bits) |
| Int16 |Representa um valor inteiro assinado de 16 bits |
| Int32 |Representa um valor inteiro assinado de 32 bits |
| Int64 |Representa um valor inteiro assinado de 64 bits |
| Cadeia de caracteres |Representa dados de caracteres de comprimento fixo ou variável |

## <a name="see-also"></a>Consulte também
* Se estiver interessado em entender o processo e a finalidade geral do mapeamento de OData, leia este artigo [Mapeamento OData de Serviço de Dados](marketplace-publishing-data-service-creation-odata-mapping.md) para examinar as definições, as estruturas e as instruções.
* Se estiver interessado em examinar exemplos, leia este artigo [Exemplos de mapeamento OData de Serviço de Dados](marketplace-publishing-data-service-creation-odata-mapping-examples.md) para ver um código de exemplo e compreender a sintaxe do código e o contexto.
* Para retornar ao caminho indicado para a publicação de um Serviço de Dados no Azure Marketplace, leia este artigo [Guia de publicação de Serviço de Dados](marketplace-publishing-data-service-creation.md).
