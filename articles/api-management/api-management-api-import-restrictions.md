---
title: Restrições e detalhes de suporte a formatos de API
titleSuffix: Azure API Management
description: Detalhes de problemas conhecidos e restrições sobre os formatos Open API, WSDL e WADL dão suporte ao gerenciamento de API do Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: vlvinogr
editor: ''
ms.assetid: 7a5a63f0-3e72-49d3-a28c-1bb23ab495e2
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/02/2020
ms.author: apimpm
ms.openlocfilehash: 6a53cc2b2ec6d46b4bde54af58b4e5542ff6cf79
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91932627"
---
# <a name="api-import-restrictions-and-known-issues"></a>Restrições de importação de API e problemas conhecidos

## <a name="about-this-list"></a>Sobre esta lista

Ao importar uma API, você pode ter algumas restrições ou identificar problemas que precisam ser corrigidos antes de poder executar a importação com êxito. Este artigo documenta essas limitações, organizadas pelo formato de importação da API. Ele também descreve como o OpenAPI Export funciona.

## <a name="openapiswagger-import-limitations"></a><a name="open-api"> </a>Limitações de importação do openapi/Swagger

Caso esteja recebendo erros ao importar seu documento da OpenAPI, verifique se você o validou com antecedência. Você pode fazer isso usando o designer no portal do Azure (Design – Front-End – Editor de especificação de OpenAPI) ou com uma ferramenta de terceiros, como o <a href="https://editor.swagger.io">Editor do Swagger</a>.

### <a name="general"></a><a name="open-api-general"> </a>Geral

-   Os parâmetros obrigatórios para o caminho e a consulta precisam ter nomes exclusivos. (Na OpenAPI, um nome de parâmetro só precisa ser exclusivo em uma localização, por exemplo, caminho, consulta, cabeçalho. No entanto, no Gerenciamento de API, permitimos que as operações sejam discriminadas por parâmetros de caminho e de consulta [o que não é compatível com a OpenAPI]. Por isso é necessário que os nomes de parâmetro sejam exclusivos dentro de todo o modelo de URL.)
-   `\$ref` ponteiros não podem referenciar arquivos externos.
-   `x-ms-paths` e `x-servers` são as únicas extensões com suporte.
-   As extensões personalizadas são ignoradas na importação e não são salvas ou preservadas para exportação.
-   `Recursion` -O gerenciamento de API não dá suporte a definições definidas recursivamente (por exemplo, esquemas que se referim a si mesmos).
-   A URL do arquivo de origem (se disponível) é aplicada a URLs de servidor relativo.
-   As definições de segurança são ignoradas.
-   Não há suporte para definições de esquema embutidas para operações de API. As definições de esquema são definidas no escopo da API e podem ser referenciadas em escopos de resposta ou solicitação de operações de API.
-   Um parâmetro de URL definido precisa fazer parte do modelo de URL.
-   `server` Não há suporte para o objeto no nível de operação da API.
-   `Produces` a palavra-chave, que descreve os tipos MIME retornados por uma API, não tem suporte. 

### <a name="openapi-version-2"></a><a name="open-api-v2"> </a>OpenAPI versão 2

-   Somente o formato JSON tem suporte.

### <a name="openapi-version-3"></a><a name="open-api-v3"> </a>OpenAPI versão 3

-   Se muitos `servers` forem especificados, o gerenciamento de API tentará selecionar a primeira URL https. Se não houver URLs HTTPs, a primeira URL HTTP. Se não houver URLs HTTP, a URL do servidor ficará vazia.
-   `Examples` Não tem suporte, mas `example` é.

## <a name="openapi-import-update-and-export-mechanisms"></a>Mecanismos de importação, atualização e exportação do OpenAPI

### <a name="general"></a><a name="open-import-export-general"> </a>Geral

-   As definições de API exportadas do serviço de gerenciamento de API são destinadas principalmente a aplicativos externos ao serviço de gerenciamento de API que precisam chamar a API hospedada no serviço de gerenciamento de API. As definições de API exportadas não devem ser importadas novamente para o mesmo serviço de gerenciamento de API ou diferente. Para o gerenciamento de configuração de API defiitions em diferentes serviços/envionments, consulte a documentação sobre como usar o serviço de gerenciamento de API com o git. 

### <a name="add-new-api-via-openapi-import"></a>Adicionar nova API por meio da importação de OpenAPI

Para cada operação encontrada no documento OpenAPI, uma nova operação será criada com o nome do recurso do Azure e o nome de exibição definido como `operationId` e, `summary` respectivamente. `operationId` o valor é normalizado seguindo as regras descritas abaixo. `summary` o valor é importado como está e seu comprimento é limitado a 300 caracteres.

Se `operationId` não for especificado (ou seja, não estiver presente, `null` ou vazio), o valor do nome de recurso do Azure será gerado pela combinação do método http e do modelo de caminho, por exemplo, `get-foo` .

Se `summary` não for especificado (ou seja, não estiver presente, `null` ou vazio), o `display name` valor será definido como `operationId` . Se `operationId` não for especificado, o valor do nome de exibição será gerado pela combinação do método http e do modelo de caminho, por exemplo, `Get - /foo` .

### <a name="update-an-existing-api-via-openapi-import"></a>Atualizar uma API existente por meio da importação do OpenAPI

Durante a importação, a API existente é alterada para corresponder à API descrita no documento OpenAPI. Cada operação no documento OpenAPI é correspondida à operação existente, comparando seu `operationId` valor com o nome de recurso do Azure da operação existente.

Se uma correspondência for encontrada, as propriedades da operação existente serão atualizadas "in-loco".

Se uma correspondência não for encontrada, uma nova operação será criada usando as regras descritas na seção acima. Para cada nova operação, a importação tentará copiar políticas de uma operação existente com o mesmo método e modelo de caminho HTTP.

Todas as operações incompatíveis existentes serão excluídas.

Para tornar a importação mais previsível, siga estas diretrizes:

- Certifique-se de especificar a `operationId` propriedade para cada operação.
- Evite alterar `operationId` após a importação inicial.
- Nunca altere `operationId` e o modelo de método ou caminho http ao mesmo tempo.

### <a name="export-api-as-openapi"></a>Exportar API como OpenAPI

Para cada operação, seu nome de recurso do Azure será exportado como um `operationId` , e o nome de exibição será exportado como um `summary` .
Regras de normalização para operationId

- Converter para letras minúsculas.
- Substituir cada sequência de caracteres não alfanuméricos por um único traço, por exemplo, `GET-/foo/{bar}?buzz={quix}` será transformado em `get-foo-bar-buzz-quix-` .
- Os traços de corte em ambos os `get-foo-bar-buzz-quix-` lados, por exemplo, ficarão `get-foo-bar-buzz-quix`
- Truncar para se ajustar a 76 caracteres, com quatro caracteres menores que o limite máximo para um nome de recurso.
- Use os quatro caracteres restantes para um sufixo de eliminação de duplicação, se necessário, na forma de `-1, -2, ..., -999` .


## <a name="wsdl"></a><a name="wsdl"> </a>WSDL

Os arquivos WSDL são usados para criar APIs de passagem SOAP e SOAP para REST.

-   **Associações SOAP** - Apenas as associações SOAP de estilo "documento" e codificação "literal" têm suporte. Não há suporte para estilo "rpc" ou SOAP-Encoding.
-   **WSDL:Import** – não há suporte para esse atributo. Os clientes devem mesclar as importações em um documento.
-   **Mensagens com várias partes** – não há suporte para esses tipos de mensagens.
-   **WCF wsHttpBinding** – serviços SOAP criados com Windows Communication Foundation devem usar basicHttpBinding – não há suporte para wsHttpBinding.
-   **MTOM** - Serviços usando MTOM <em>podem</em> funcionar. No momento, não oferecemos suporte oficial.
-   **Recursão** - Tipos que são definidos recursivamente (por exemplo, referem-se a uma matriz deles) não têm suporte pelo APIM.
-   **Vários Namespaces** – vários namespaces podem ser usados em um esquema, mas apenas o namespace de destino pode ser usado para definir partes da mensagem. Os namespaces diferentes do destino, que são usados para definir outros elementos de entrada ou saída, não são preservados. Embora um documento WSDL desse tipo possa ser importado, na exportação, todas as partes da mensagem terão o namespace de destino do WSDL.
-   **Matrizes** – a transformação SOAP-to-REST dá suporte apenas a matrizes encapsuladas mostradas no exemplo abaixo:

```xml
    <complexType name="arrayTypeName">
        <sequence>
            <element name="arrayElementValue" type="arrayElementType" minOccurs="0" maxOccurs="unbounded"/>
        </sequence>
    </complexType>
    <complexType name="typeName">
        <sequence>
            <element name="element1" type="someTypeName" minOccurs="1" maxOccurs="1"/>
            <element name="element2" type="someOtherTypeName" minOccurs="0" maxOccurs="1" nillable="true"/>
            <element name="arrayElement" type="arrayTypeName" minOccurs="1" maxOccurs="1"/>
        </sequence>
    </complexType>
```

## <a name="wadl"></a><a name="wadl"> </a>WADL

Atualmente, não há problemas de importação de WADL conhecidos.
