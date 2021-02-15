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
ms.topic: article
ms.date: 01/27/2021
ms.author: apimpm
ms.openlocfilehash: 22d2960801cac2222f868c384a55b4bf436bc75b
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99492596"
---
# <a name="api-management-authentication-policies"></a>Políticas de autenticação de Gerenciamento de API
Este tópico fornece uma referência para as políticas de Gerenciamento de API a seguir. Para obter mais informações sobre como adicionar e configurar políticas, consulte [Políticas de Gerenciamento de API](./api-management-policies.md).

##  <a name="authentication-policies"></a><a name="AuthenticationPolicies"></a> Políticas de autenticação

-   [Autenticar com o Basic](api-management-authentication-policies.md#Basic) - Autenticar com um serviço de back-end usando a autenticação Básica.

-   [Autenticar com o certificado de cliente](api-management-authentication-policies.md#ClientCertificate) - Autenticar com um serviço de back-end usando certificados de cliente.

-   [Autenticar com a identidade gerenciada](api-management-authentication-policies.md#ManagedIdentity) – autentique com a [identidade gerenciada](../active-directory/managed-identities-azure-resources/overview.md) para o serviço de gerenciamento de API.

##  <a name="authenticate-with-basic"></a><a name="Basic"></a> Autenticar com o Basic
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

|Nome|Descrição|Obrigatório|
|----------|-----------------|--------------|
|authentication-basic|Elemento raiz.|Sim|

### <a name="attributes"></a>Atributos

|Nome|Descrição|Obrigatório|Padrão|
|----------|-----------------|--------------|-------------|
|Nome de Usuário|Especifica o nome de usuário da credencial do Basic.|Sim|N/D|
|password|Especifica a senha da credencial do Basic.|Sim|N/D|

### <a name="usage"></a>Uso
 Essa política pode ser usada nas [seções](./api-management-howto-policies.md#sections) e nos [escopos](./api-management-howto-policies.md#scopes) da política a seguir.

-   **Seções de política:** de entrada

-   **Escopos da política:** todos os escopos

##  <a name="authenticate-with-client-certificate"></a><a name="ClientCertificate"></a> Autenticar com o certificado de cliente
 Use a `authentication-certificate` política para autenticar com um serviço de back-end usando um certificado de cliente. O certificado precisa ser [instalado no gerenciamento de API](./api-management-howto-mutual-certificates.md) primeiro e é identificado por sua impressão digital ou ID do certificado (nome do recurso). 

> [!CAUTION]
> Se o certificado fizer referência a um certificado armazenado em Azure Key Vault, identifique-o usando a ID do certificado. Quando um certificado do Key Vault for girado, sua impressão digital no gerenciamento de API será alterada e a política não resolverá o novo certificado se ele for identificado pela impressão digital.

### <a name="policy-statement"></a>Declaração de política

```xml
<authentication-certificate thumbprint="thumbprint" certificate-id="resource name"/>
```

### <a name="examples"></a>Exemplos

Neste exemplo, o certificado do cliente é identificado pela ID do certificado:

```xml  
<authentication-certificate certificate-id="544fe9ddf3b8f30fb490d90f" />  
``` 

Neste exemplo, o certificado do cliente é identificado por sua impressão digital:

```xml
<authentication-certificate thumbprint="CA06F56B258B7A0D4F2B05470939478651151984" />
```
Neste exemplo, o certificado do cliente é definido na política em vez de ser recuperado do repositório de certificados interno:

```xml
<authentication-certificate body="@(context.Variables.GetValueOrDefault<byte[]>("byteCertificate"))" password="optional-certificate-password" />
```

### <a name="elements"></a>Elementos  
  
|Nome|Descrição|Obrigatório|  
|----------|-----------------|--------------|  
|authentication-certificate|Elemento raiz.|Sim|  
  
### <a name="attributes"></a>Atributos  
  
|Nome|Descrição|Obrigatório|Padrão|  
|----------|-----------------|--------------|-------------|  
|thumbprint|A impressão digital do certificado do cliente.|`thumbprint`Ou `certificate-id` deve estar presente.|N/D|
|ID do certificado|O nome do recurso do certificado.|`thumbprint`Ou `certificate-id` deve estar presente.|N/D|
|body|Certificado de cliente como uma matriz de bytes.|Não|N/D|
|password|Senha do certificado do cliente.|Usado se o certificado especificado em `body` for protegido por senha.|N/D|
  
### <a name="usage"></a>Uso  
 Essa política pode ser usada nas [seções](./api-management-howto-policies.md#sections) e nos [escopos](./api-management-howto-policies.md#scopes) da política a seguir.  
  
-   **Seções de política:** de entrada  
  
-   **Escopos da política:** todos os escopos  

##  <a name="authenticate-with-managed-identity"></a><a name="ManagedIdentity"></a> Autenticar com identidade gerenciada  
 Use a `authentication-managed-identity` política para autenticar com um serviço de back-end usando a identidade gerenciada. Essa política usa basicamente a identidade gerenciada para obter um token de acesso de Azure Active Directory para acessar o recurso especificado. Depois de obter o token com êxito, a política definirá o valor do token no `Authorization` cabeçalho usando o `Bearer` esquema.

Tanto a identidade atribuída pelo sistema quanto qualquer uma das várias identidades atribuídas pelo usuário podem ser usadas para solicitar o token. Se `client-id` não for fornecida, a identidade atribuída pelo sistema será assumida. Se a `client-id` variável for fornecida, o token será solicitado para essa identidade atribuída pelo usuário de Azure Active Directory
  
### <a name="policy-statement"></a>Declaração de política  
  
```xml  
<authentication-managed-identity resource="resource" client-id="clientid of user-assigned identity" output-token-variable-name="token-variable" ignore-error="true|false"/>  
```  
  
### <a name="example"></a>Exemplo  
#### <a name="use-managed-identity-to-authenticate-with-a-backend-service"></a>Usar identidade gerenciada para autenticar com um serviço de back-end
```xml  
<authentication-managed-identity resource="https://graph.microsoft.com"/> 
```
```xml  
<authentication-managed-identity resource="https://management.azure.com/"/> <!--Azure Resource Manager-->
```
```xml  
<authentication-managed-identity resource="https://vault.azure.net"/> <!--Azure Key Vault-->
```
```xml  
<authentication-managed-identity resource="https://servicebus.azure.net/"/> <!--Azure Service Bus-->
```
```xml  
<authentication-managed-identity resource="https://storage.azure.com/"/> <!--Azure Blob Storage-->
```
```xml  
<authentication-managed-identity resource="https://database.windows.net/"/> <!--Azure SQL-->
```

```xml
<authentication-managed-identity resource="api://Client_id_of_Backend"/> <!--Your own Azure AD Application-->
```

#### <a name="use-managed-identity-and-set-header-manually"></a>Usar identidade gerenciada e definir o cabeçalho manualmente

```xml
<authentication-managed-identity resource="api://Client_id_of_Backend"
   output-token-variable-name="msi-access-token" ignore-error="false" /> <!--Your own Azure AD Application-->
<set-header name="Authorization" exists-action="override">
   <value>@("Bearer " + (string)context.Variables["msi-access-token"])</value>
</set-header>
```

#### <a name="use-managed-identity-in-send-request-policy"></a>Usar identidade gerenciada na política de solicitação de envio
```xml  
<send-request mode="new" timeout="20" ignore-error="false">
    <set-url>https://example.com/</set-url>
    <set-method>GET</set-method>
    <authentication-managed-identity resource="ResourceID"/>
</send-request>
```

### <a name="elements"></a>Elementos  
  
|Nome|Descrição|Obrigatório|  
|----------|-----------------|--------------|  
|autenticação-gerenciada-identidade |Elemento raiz.|Sim|  
  
### <a name="attributes"></a>Atributos  
  
|Nome|Descrição|Obrigatório|Padrão|  
|----------|-----------------|--------------|-------------|  
|recurso|Cadeia de caracteres. A ID do aplicativo da API Web de destino (recurso protegido) em Azure Active Directory.|Sim|N/D|
|ID do cliente|Cadeia de caracteres. A ID do aplicativo da identidade atribuída pelo usuário no Azure Active Directory.|Não|identidade atribuída pelo sistema|
|saída-token-variável-nome|Cadeia de caracteres. Nome da variável de contexto que receberá o valor de token como um tipo de objeto `string` . |Não|N/D|  
|ignore-error|Booliano. Se definido como `true` , o pipeline de política continuará a ser executado mesmo se um token de acesso não for obtido.|Não|false|  
  
### <a name="usage"></a>Uso  
 Essa política pode ser usada nas [seções](./api-management-howto-policies.md#sections) e nos [escopos](./api-management-howto-policies.md#scopes) da política a seguir.  
  
-   **Seções de política:** de entrada  
  
-   **Escopos da política:** todos os escopos

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre como trabalhar com políticas, consulte:

+ [Políticas no Gerenciamento de API](api-management-howto-policies.md)
+ [Transformar APIs](transform-api.md)
+ [Referência de Política](./api-management-policies.md) para uma lista completa das instruções de política e suas configurações
+ [Exemplos de política](./policy-reference.md)