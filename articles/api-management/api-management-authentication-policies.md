---
title: Políticas de autenticação de Gerenciamento de API do Azure | Microsoft Docs
description: Saiba mais sobre as políticas de autenticação disponíveis para uso no Gerenciamento de API do Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 061702a7-3a78-472b-a54a-f3b1e332490d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/27/2017
ms.author: apimpm
ms.openlocfilehash: c0f8da779ca656cf357c418b8766a53307643695
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63764351"
---
# <a name="api-management-authentication-policies"></a>Políticas de autenticação de Gerenciamento de API
Este tópico fornece uma referência para as políticas de Gerenciamento de API a seguir. Para obter mais informações sobre como adicionar e configurar políticas, consulte [Políticas de Gerenciamento de API](https://go.microsoft.com/fwlink/?LinkID=398186).  

##  <a name="AuthenticationPolicies"></a> Políticas de autenticação  
  
-   [Autenticar com o Basic](api-management-authentication-policies.md#Basic) - Autenticar com um serviço de back-end usando a autenticação Básica.  
  
-   [Autenticar com o certificado de cliente](api-management-authentication-policies.md#ClientCertificate) - Autenticar com um serviço de back-end usando certificados de cliente.  

-   [Autenticar com identidade gerenciada](api-management-authentication-policies.md#ManagedIdentity) -autenticar com o [identidade gerenciada](../active-directory/managed-identities-azure-resources/overview.md) para o serviço de gerenciamento de API.  
  
##  <a name="Basic"></a> Autenticar com o Basic  
 Use a política `authentication-basic` para autenticar com um serviço de back-end usando a autenticação do Basic. Essa política define efetivamente o cabeçalho de autorização HTTP para o valor correspondente às credenciais fornecidas na política.  
  
### <a name="policy-statement"></a>Declaração de política  
  
```xml  
<authentication-basic username="username" password="password" />  
```  
  
### <a name="example"></a>Exemplo  
  
```xml  
<authentication-basic username="testuser" password="testpassword" />  
```  
  
### <a name="elements"></a>Elementos  
  
|NOME|DESCRIÇÃO|Obrigatório|  
|----------|-----------------|--------------|  
|authentication-basic|Elemento raiz.|Sim|  
  
### <a name="attributes"></a>Atributos  
  
|NOME|DESCRIÇÃO|Obrigatório|Padrão|  
|----------|-----------------|--------------|-------------|  
|Nome de Usuário|Especifica o nome de usuário da credencial do Basic.|Sim|N/D|  
|Senha|Especifica a senha da credencial do Basic.|Sim|N/D|  
  
### <a name="usage"></a>Uso  
 Essa política pode ser usada nas [seções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos [escopos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) da política a seguir.  
  
-   **Seções de política:** de entrada  
  
-   **Escopos de política:** API  
  
##  <a name="ClientCertificate"></a> Autenticar com o certificado de cliente  
 Use a política `authentication-certificate` para autenticar com um serviço de back-end usando um certificado de cliente. O certificado precisa ser [instalado no Gerenciamento de API](https://go.microsoft.com/fwlink/?LinkID=511599) primeiro e é identificado por sua impressão digital.  
  
### <a name="policy-statement"></a>Declaração de política  
  
```xml  
<authentication-certificate thumbprint="thumbprint" certificate-id="resource name"/>  
```  
  
### <a name="examples"></a>Exemplos  
  
Este exemplo de cliente certificado é identificado por sua impressão digital.
```xml  
<authentication-certificate thumbprint="CA06F56B258B7A0D4F2B05470939478651151984" />  
``` 
Neste exemplo o certificado do cliente é identificado por nome de recurso.
```xml  
<authentication-certificate certificate-id="544fe9ddf3b8f30fb490d90f" />  
```  

### <a name="elements"></a>Elementos  
  
|NOME|DESCRIÇÃO|Obrigatório|  
|----------|-----------------|--------------|  
|authentication-certificate|Elemento raiz.|Sim|  
  
### <a name="attributes"></a>Atributos  
  
|NOME|DESCRIÇÃO|Obrigatório|Padrão|  
|----------|-----------------|--------------|-------------|  
|impressão digital|A impressão digital do certificado do cliente.|Tanto `thumbprint` ou `certificate-id` deve estar presente.|N/D|  
|certificate-id|O nome do recurso de certificado.|Tanto `thumbprint` ou `certificate-id` deve estar presente.|N/D|  
  
### <a name="usage"></a>Uso  
 Essa política pode ser usada nas [seções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos [escopos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) da política a seguir.  
  
-   **Seções de política:** de entrada  
  
-   **Escopos de política:** API  

##  <a name="ManagedIdentity"></a> Autenticar com identidade gerenciada  
 Use o `authentication-managed-identity` diretiva para autenticar com um serviço de back-end usando a identidade gerenciada do serviço de gerenciamento de API. Esta política usa efetivamente a identidade gerenciada para obter um token de acesso do Azure Active Directory para acessar o recurso especificado. 
  
### <a name="policy-statement"></a>Declaração de política  
  
```xml  
<authentication-managed-identity resource="resource" output-token-variable-name="token-variable" ignore-error="true|false"/>  
```  
  
### <a name="example"></a>Exemplo  
  
```xml  
<authentication-managed-identity resource="https://graph.windows.net" output-token-variable-name="test-access-token" ignore-error="true" /> 
```  
  
### <a name="elements"></a>Elementos  
  
|NOME|DESCRIÇÃO|Obrigatório|  
|----------|-----------------|--------------|  
|identidade gerenciada autenticação |Elemento raiz.|Sim|  
  
### <a name="attributes"></a>Atributos  
  
|NOME|DESCRIÇÃO|Obrigatório|Padrão|  
|----------|-----------------|--------------|-------------|  
|recurso|Cadeia de caracteres. O URI de ID do aplicativo de destino API web (recurso seguro) no Azure Active Directory.|Sim|N/D|  
|saída-token-variable-name|Cadeia de caracteres. Nome da variável de contexto que receberá o valor do token como um tipo de objeto `string`.|Não |N/D|  
|ignore-error|Booliano. Se definido como `true`, o pipeline de apólices dará continuidade à execução mesmo se não for obtido um token de acesso.|Não |falso|  
  
### <a name="usage"></a>Uso  
 Essa política pode ser usada nas [seções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos [escopos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) da política a seguir.  
  
-   **Seções de política:** de entrada  
  
-   **Escopos de política:** global, produto, API, operação  

## <a name="next-steps"></a>Próximos passos
Para obter mais informações sobre como trabalhar com políticas, consulte:

+ [Políticas no Gerenciamento de API](api-management-howto-policies.md)
+ [Transformar APIs](transform-api.md)
+ [Referência de Política](api-management-policy-reference.md) para uma lista completa das instruções de política e suas configurações
+ [Exemplos de política](policy-samples.md)   
