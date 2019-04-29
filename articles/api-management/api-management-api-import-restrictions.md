---
title: Restrições e problemas conhecidos na importação de API do Gerenciamento de API do Azure | Microsoft Docs
description: Detalhes de problemas conhecidos e restrições na importação para o Gerenciamento de API do Azure usando os formatos Open API, WSDL ou WADL.
services: api-management
documentationcenter: ''
author: vladvino
manager: vlvinogr
editor: ''
ms.assetid: 7a5a63f0-3e72-49d3-a28c-1bb23ab495e2
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.author: apipm
ms.openlocfilehash: 251caa840446e75ff13d9b4dcebcbae3a36473c8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60657502"
---
# <a name="api-import-restrictions-and-known-issues"></a>Restrições de importação de API e problemas conhecidos
## <a name="about-this-list"></a>Sobre esta lista
Ao importar uma API, você pode se deparar com algumas restrições ou identificar problemas que precisam ser corrigidos antes de poder importar com êxito. Este artigo documenta essas informações, organizadas pelo formato de importação da API.

## <a name="open-api"> </a>OpenAPI/Swagger

Caso esteja recebendo erros ao importar seu documento da OpenAPI, verifique se você o validou com antecedência. Você pode fazer isso usando o designer no portal do Azure (Design – Front-End – Editor de especificação de OpenAPI) ou com uma ferramenta de terceiros, como o <a href="https://editor.swagger.io">Editor do Swagger</a>.

### <a name="open-api-general"> </a>Geral

* Os parâmetros obrigatórios para o caminho e a consulta precisam ter nomes exclusivos. (Na OpenAPI, um nome de parâmetro só precisa ser exclusivo em uma localização, por exemplo, caminho, consulta, cabeçalho. No entanto, no Gerenciamento de API, permitimos que as operações sejam discriminadas por parâmetros de caminho e de consulta [o que não é compatível com a OpenAPI]. Por isso é necessário que os nomes de parâmetro sejam exclusivos dentro de todo o modelo de URL.)
* Ponteiros **$ref** não podem referenciar a arquivos externos.
* **x-ms-paths** e **x-servers** são as únicas extensões com suporte.
* As extensões personalizadas são ignoradas na importação e não são salvas ou preservadas para exportação.
* **Recursão** – o Gerenciamento de API não dá suporte a definições definidas recursivamente (por exemplo, esquemas fazendo referência a si mesmos).
* A URL do arquivo de origem (se disponível) é aplicada a URLs de servidor relativo.

### <a name="open-api-v2"> </a>OpenAPI versão 2

* Somente o formato JSON tem suporte.

### <a name="open-api-v3"> </a>OpenAPI versão 3

* Se muitos **servidores** forem especificados, o Gerenciamento de API tentará selecionar a primeira URL HTTPs. Se não houver URLs HTTPs, a primeira URL HTTP. Se não houver URLs HTTP, a URL do servidor ficará vazia.
* **Exemplos** não têm suporte, mas **exemplo** tem.
* **Partes múltiplas/dados de formulário** não tem suporte.

> [!IMPORTANT]
> Consulte este [documento](https://blogs.msdn.microsoft.com/apimanagement/2018/04/11/important-changes-to-openapi-import-and-export/) para obter dicas e informações importantes relacionadas à importação do OpenAPI.

## <a name="wsdl"> </a>WSDL
Arquivos WSDL são usados para gerar as APIs de passagem SOAP ou servir como o back-end de uma API de SOAP para REST.
* **Associações SOAP** - Apenas as associações SOAP de estilo "documento" e codificação "literal" têm suporte. Não há suporte para estilo "rpc" ou SOAP-Encoding.
* **WSDL:Import** – não há suporte para esse atributo. Os clientes devem mesclar as importações em um documento.
* **Mensagens com várias partes** – não há suporte para esses tipos de mensagens.
* **WCF wsHttpBinding** – serviços SOAP criados com Windows Communication Foundation devem usar basicHttpBinding – não há suporte para wsHttpBinding.
* **MTOM** - Serviços usando MTOM <em>podem</em> funcionar. No momento, não oferecemos suporte oficial.
* **Recursão** - Tipos que são definidos recursivamente (por exemplo, referem-se a uma matriz deles) não têm suporte pelo APIM.
* **Vários Namespaces** – vários namespaces podem ser usados em um esquema, mas apenas o namespace de destino pode ser usado para definir partes da mensagem. Namespaces que não sejam o destino usados para definir outros elementos de entrada ou saída não são preservados. Embora um documento WSDL desse tipo possa ser importado, na exportação, todas as partes da mensagem terão o namespace de destino do WSDL.

## <a name="wadl"> </a>WADL
Atualmente, não há problemas de importação de WADL conhecidos.
