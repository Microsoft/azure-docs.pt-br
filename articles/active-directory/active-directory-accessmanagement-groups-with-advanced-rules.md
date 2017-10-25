---
title: Popular grupos dinamicamente com base em atributos de objeto no Azure Active Directory | Microsoft Docs
description: "Instruções para criar regras avançadas para associação de grupo, incluindo parâmetros e operadores de regra de expressões com suporte."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 04813a42-d40a-48d6-ae96-15b7e5025884
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2017
ms.author: curtand
ms.reviewer: kairaz.contractor
ms.custom: oldportal
ms.openlocfilehash: 168829494de33a21df68d3fc5e2a174f435c5ad5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="populate-groups-dynamically-based-on-object-attributes"></a>Popular grupos dinamicamente com base em atributos de objeto
O portal clássico do Azure possibilita habilitar associações dinâmicas baseadas em atributos mais complexas de grupos do Azure AD (Azure Active Directory).  

Quando os atributos de um usuário ou um dispositivo são alterados, o sistema avalia todas as regras de grupo dinâmicas em um diretório para ver se a alteração dispararia adições ou remoções de grupo. Se um usuário ou dispositivo atender a uma regra em um grupo, ele será adicionado como membro desse grupo. Se não atenderem mais à regra, eles serão removidos.

> [!NOTE]
> - Você pode configurar uma regra de associação dinâmica em grupos de segurança ou em grupos do Office 365.
>
> - Este recurso exige uma licença Azure AD Premium P1 para cada membro de usuário adicionado a pelo menos um grupo dinâmico.
>
> - Você pode criar um grupo dinâmico para usuários ou dispositivos, mas não pode criar uma regra que contenha objetos de usuário e de dispositivo.

> - Nesse momento não é possível criar um grupo de dispositivos com base em atributos do usuário proprietário. As regras de associação de dispositivo só podem fazer referência a atributos imediatos dos objetos de dispositivo no diretório.

## <a name="to-create-an-advanced-rule"></a>Para criar uma regra avançada
1. No [portal clássico do Azure](https://manage.windowsazure.com), selecione **Active Directory**e abra o diretório da sua organização.
2. Selecione a guia **Grupos** e, em seguida, abra o grupo que deseja editar.
3. Selecione a guia **Configurar**, selecione a opção **Regra avançada** e, em seguida, digite a regra avançada na caixa de texto.

## <a name="constructing-the-body-of-an-advanced-rule"></a>Construção do corpo de uma regra avançada
A regra avançada que você pode criar para os membros dinâmicos para grupos é essencialmente uma expressão binária que consiste em três partes e resulta em um resultado verdadeiro ou falso. As três partes são:

* Parâmetro da esquerda
* Operador binário
* Constante à direita

Uma regra avançada completa é semelhante a esta: (leftParameter binaryOperator "RightConstant"), em que o parêntese de abertura e fechamento são necessários para toda a expressão binária, aspas duplas são necessárias para a constante à direita e a sintaxe do parâmetro esquerdo é user.property. Uma regra avançada pode consistir em mais de uma expressão binária separada pelos operadores lógicos -and, -or e -not.
A seguir é exemplos de uma regra avançada construída de maneira adequada:

* (user.department -eq "Sales") -or (user.department -eq "Marketing")
* (user.department -eq "Sales") -and -not (user.jobTitle -contains "SDE")

Para a lista completa de parâmetros com suporte e operadores de regra de expressão, consulte as seções a seguir.


Observe que a propriedade deve ser prefixada com o tipo de objeto correto: usuário ou dispositivo.
A regra a seguir falhará na validação: mail –ne null

A regra correta seria:

user.mail –ne null

O comprimento total do corpo da sua regra avançada não pode exceder 2048 caracteres.

> [!NOTE]
> Operações de cadeia de caracteres e regex diferenciam maiúsculas de minúsculas.
> As cadeias de caracteres que contém aspas " devem ser ignoradas usando caracteres ', por exemplo, user.department -eq \`"Sales".
> Use as aspas somente para valores do tipo string e use apenas as aspas inglesas.
>
>

## <a name="supported-expression-rule-operators"></a>Operadores de regra de expressão com suporte
A tabela a seguir lista todos os operadores de regra de expressão com suporte e sua sintaxe a ser usada no corpo da regra avançada:

| Operador | Sintaxe |
| --- | --- |
| Não é igual a |-ne |
| É igual a |-eq |
| Não começa com |-notStartsWith |
| Começa com |-startsWith |
| Não contém |-notContains |
| Contém: |-contains |
| Não corresponde |-notMatch |
| Corresponde |-match |
| Nesse | -in |
| Não está em | -notIn |

## <a name="operator-precedence"></a>Precedência do operador

Todos os operadores estão listados abaixo da menor precedência para a maior; os operadores na mesma linha têm a mesma precedência. -any -all -or -and -not -eq -ne -startsWith -notStartsWith -contains -notContains -match –notMatch -in -notIn

Todos os operadores podem ser usados com ou sem o prefixo de hífen.

Observe que os parênteses nem sempre serão necessários, você só precisará adicionar os parênteses quando a precedência não atender às suas necessidades. Por exemplo:

   user.department –eq "Marketing" –and user.country –eq "US"

é equivalente a:

   (user.department –eq "Marketing") –and (user.country –eq "US")

## <a name="using-the--in-and--notin-operators"></a>Usando os operadores -In e -notIn

Se quiser comparar o valor de um atributo de usuário com um número de valores diferentes, você pode usar os operadores -In ou -notIn. Veja um exemplo de uso do operador -In:

    user.department -In [ "50001", "50002", "50003", “50005”, “50006”, “50007”, “50008”, “50016”, “50020”, “50024”, “50038”, “50039”, “51100” ]

Observe o uso de "[" e "]" no início e no final da lista de valores. Essa condição é avaliada como verdadeira se o valor de user.department for igual a um dos valores na lista.

## <a name="query-error-remediation"></a>Correção do erro de consulta
A seguinte tabela relacionará os possíveis erros e como corrigi-los, se ocorrerem

| Erro de análise de consulta | Erros de uso | Uso corrigido |
| --- | --- | --- |
| Erro: O atributo não tem suportado. |(user.invalidProperty -eq "Valor") |(user.department -eq "value") A propriedade <br/>deve corresponder a uma na [lista de propriedades com suporte](#supported-properties). |
| Erro: Operador não é tem suportada no atributo. |(user.accountEnabled -contains true) |(user.accountEnabled -eq true) A propriedade <br/>é do tipo booliano. Use os operadores com suporte (-eq or -ne) em um tipo booliano da lista acima. |
| Erro: Erro de compilação de consulta. |(user.department -eq "Sales") -and (user.department -eq "Marketing")(user.userPrincipalName -match "*@domain.ext") |(user.department -eq "Sales") -and (user.department -eq "Marketing")<br/>O operador lógico deve corresponder a uma das propriedades com suporte listadas acima. (user.userPrincipalName -match ".*@domain.ext")or(user.userPrincipalName -match "@domain.ext$")Error na expressão regular. |
| Erro: Expressão binária não está no formato correto. |user.department – eq ("Vendas") user.department - eq ("Vendas") user.department-eq ("Vendas") |(user.accountEnabled -eq true) -and (user.userPrincipalName -contains "alias@domain")<br/>A consulta tem vários erros. Parênteses não no lugar certo. |
| Erro: Ocorreu um erro desconhecido durante a configuração de membros dinâmicos. |(user.accountEnabled -eq "True" AND user.userPrincipalName -contains "alias@domain") |(user.accountEnabled -eq true) -and (user.userPrincipalName -contains "alias@domain")<br/>A consulta tem vários erros. Parênteses não no lugar certo. |

## <a name="supported-properties"></a>Propriedades com suporte
Estas são todas as propriedades do usuário que você pode usar na regra avançada:

### <a name="properties-of-type-boolean"></a>Propriedades de tipo booliano
Operadores permitidos

* -eq
* -ne

| Propriedades | Valores permitidos | Uso |
| --- | --- | --- |
| accountEnabled |verdadeiro, falso |user.accountEnabled -eq true |
| dirSyncEnabled |verdadeiro, falso |user.dirSyncEnabled -eq true |

### <a name="properties-of-type-string"></a>Propriedades do tipo cadeia de caracteres
Operadores permitidos

* -eq
* -ne
* -notStartsWith
* -startsWith
* -contains
* -notContains
* -match
* -notMatch
* -in
* -notIn

| Propriedades | Valores permitidos | Uso |
| --- | --- | --- |
| city |Qualquer valor de cadeia de caracteres ou $null |(user.city -eq "valor") |
| country |Qualquer valor de cadeia de caracteres ou $null |(user.country -eq "valor") |
| companyName | Qualquer valor de cadeia de caracteres ou $null | (user.companyName -eq "value") |
| department |Qualquer valor de cadeia de caracteres ou $null |(user.department -eq "value") A propriedade  |
| displayName |Um valor de cadeia de caracteres. |(user. DisplayName -eq "valor") |
| facsimileTelephoneNumber |Qualquer valor de cadeia de caracteres ou $null |user.facsimileTelephoneNumber -eq ("valor") |
| givenName |Qualquer valor de cadeia de caracteres ou $null |user.givenName -eq ("valor") |
| jobTitle |Qualquer valor de cadeia de caracteres ou $null |(user.jobTitle - eq "valor") |
| mail |Qualquer valor de cadeia de caracteres ou $null (endereço SMTP do usuário) |(user.mail - eq "valor") |
| mailNickName |Qualquer valor de cadeia de caracteres (alias de email do usuário) |(user.mailNickName - eq "valor") |
| Serviço Móvel |Qualquer valor de cadeia de caracteres ou $null |(user.mobile -eq "valor") |
| ID do objeto |GUID do objeto de usuário |(user.objectId - eq "1111111-1111-1111-1111-111111111111") |
| onPremisesSecurityIdentifier | SID (ID de segurança) local para usuários que foram sincronizados do local para a nuvem. |(user.onPremisesSecurityIdentifier -eq "S-1-1-11-1111111111-1111111111-1111111111-1111111") |
| passwordPolicies |None DisableStrongPassword DisablePasswordExpiration DisablePasswordExpiration, DisableStrongPassword |(user.passwordPolicies -eq "DisableStrongPassword") |
| physicalDeliveryOfficeName |Qualquer valor de cadeia de caracteres ou $null |(user.physicalDeliveryOfficeName -eq "valor") |
| postalCode |Qualquer valor de cadeia de caracteres ou $null |(user.postalCode - eq "valor") |
| preferredLanguage |ISO 639-1 code |(user.preferredLanguage - eq "en-US") |
| sipProxyAddress |Qualquer valor de cadeia de caracteres ou $null |(user.sipProxyAddress -eq "valor") |
| state |Qualquer valor de cadeia de caracteres ou $null |(user.state -eq "valor") |
| streetAddress |Qualquer valor de cadeia de caracteres ou $null |(user.streetAddress -eq "valor") |
| sobrenome |Qualquer valor de cadeia de caracteres ou $null |(user.surname -eq "valor") |
| telephoneNumber |Qualquer valor de cadeia de caracteres ou $null |(user.telephoneNumber -eq "valor") |
| usageLocation |Código do país indicados dois |(user.usageLocation -eq "EUA") |
| userPrincipalName |Um valor de cadeia de caracteres. |(user.userPrincipalName -eq "alias@domain") |
| userType |member guest $null |(ser.userType -eq "Membro") |

### <a name="properties-of-type-string-collection"></a>Propriedades de coleção de cadeias de caracteres de tipo
Operadores permitidos

* -contains
* -notContains

| Propriedades | Valores permitidos | Uso |
| --- | --- | --- |
| otherMails |Um valor de cadeia de caracteres. |(user.otherMails -contains "alias@domain") |

| proxyAddresses |SMTP: alias@domain smtp: alias@domain |(user.proxyAddresses -contains "SMTP: alias@domain") |

## <a name="multi-value-properties"></a>Propriedades de vários valores
Operadores permitidos

* -any (satisfeita quando pelo menos um item na coleção corresponde à condição)
* -all (satisfeita quando todos os itens na coleção correspondem à condição)

| Propriedades | Valores | Uso |
| --- | --- | --- |
| assignedPlans |Cada objeto na coleção expõe as seguintes propriedades de cadeia de caracteres: capabilityStatus, service, servicePlanId |user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -and assignedPlan.capabilityStatus -eq "Enabled") |

As propriedades de vários valores são coleções de objetos do mesmo tipo. Você pode usar os operadores -any e -all para aplicar uma condição a um ou todos os itens na coleção, respectivamente. Por exemplo:

assignedPlans é uma propriedade de valores múltiplos que lista todos os planos de serviço atribuídos ao usuário. A expressão abaixo selecionará os usuários que tenham o plano do serviço Exchange Online (Plano 2) e que também estejam no estado Habilitado:

```
user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -and assignedPlan.capabilityStatus -eq "Enabled")
```

(O identificador GUID identifica o plano de serviço Exchange Online (Plano 2)).

> [!NOTE]
> Isso é útil se você deseja identificar todos os usuários para os quais uma funcionalidade do Office 365 (ou outro serviço da Microsoft Online) tenha sido habilitada, por exemplo, para direcioná-los com um determinado conjunto de políticas.

A expressão a seguir selecionará todos os usuários com qualquer plano de serviço que esteja associado com o serviço do Intune (identificado pelo nome do serviço "SCO"):
```
user.assignedPlans -any (assignedPlan.service -eq "SCO" -and assignedPlan.capabilityStatus -eq "Enabled")
```

## <a name="use-of-null-values"></a>Uso de valores nulos

Para especificar um valor nulo em uma regra, você pode usar "null" ou $null. Exemplo:

   user.mail –ne null é equivalente a user.mail –ne $null

## <a name="extension-attributes-and-custom-attributes"></a>Atributos de extensão e atributos personalizados
Os atributos de extensão e os atributos personalizados têm suporte das regras de associação dinâmica.

Os atributos de extensão são sincronizados no AD do Windows Server local e têm o formato "ExtensionAttributeX", em que X é igual a 1 a 15.
Um exemplo de uma regra que usa um atributo de extensão:

(user.extensionAttribute15 -eq "Marketing")

Os Atributos Personalizados são sincronizados do AD do Windows Server local ou de um aplicativo SaaS conectado e têm formato "user.extension[GUID]\__[Atributo]", em que [GUID] é o identificador exclusivo no AAD para o aplicativo que criou o atributo no AAD e [Atributo] é o nome do atributo como ele foi criado.
Um exemplo de uma regra que usa um atributo personalizado:

user.extension_c272a57b722d4eb29bfe327874ae79cb__OfficeNumber  

O nome do atributo personalizado pode ser encontrado no diretório por meio da consulta do atributo de um usuário, usando o Graph Explorer e procurando o nome do atributo.
No momento, não oferecemos suporte a atributos com valores múltiplos sincronizados a partir do Active Directory local.

## <a name="direct-reports-rule"></a>Regra de "subordinados diretos"
Você pode criar um grupo contendo todos os subordinados diretos de um gerente. Quando os subordinados diretos do gerente forem alterados no futuro, a associação do grupo será ajustada automaticamente.

> [!NOTE]
> 1. Para que a regra funcione, verifique se a propriedade **ID do Gerenciador** está definida corretamente nos usuários em seu locatário. Você pode verificar o valor atual de um usuário na respectiva **guia Perfil**.
> 2. Essa regra só dá suporte a subordinados **diretos**. No momento não é possível criar um grupo para uma hierarquia aninhada, por exemplo, um grupo que inclua os subordinados diretos e os subordinados deles.

**Para configurar o grupo**

1. Siga as etapas de 1 a 5 da seção [Para criar a regra avançada](#to-create-the-advanced-rule) e selecione o **Tipo de associação** como **Usuário Dinâmico**.
2. Na folha **Regras de associação dinâmica** , insira a regra com a seguinte sintaxe:

    *Subordinados diretos para "{obectID_of_manager}"*

    Um exemplo de uma regra válida:
```
                    Direct Reports for "62e19b97-8b3d-4d4a-a106-4ce66896a863"
```
    where “62e19b97-8b3d-4d4a-a106-4ce66896a863” is the objectID of the manager. The object ID can be found on manager's **Profile tab**.
3. Depois de salvar a regra, todos os usuários com o valor da ID do Gerenciador especificada serão adicionados ao grupo.

## <a name="using-attributes-to-create-rules-for-device-objects"></a>Usando atributos para criar regras para objetos de dispositivo
Você também pode criar uma regra que seleciona objetos de dispositivo para associação em um grupo. Os seguintes atributos de dispositivo podem ser usados:

| Propriedades              | Valores permitidos                     | Uso                                                       |
|-------------------------|------------------------------------|-------------------------------------------------------------|
| accountEnabled          | verdadeiro, falso                         | (device.accountEnabled -eq true)                            |
| displayName             | Um valor de cadeia de caracteres.                   | (device.displayName -eq "Rob Iphone”)                       |
| deviceOSType            | Um valor de cadeia de caracteres.                   | (device.deviceOSType -eq "Android")                         |
| deviceOSVersion         | Um valor de cadeia de caracteres.                   | (device.OSVersion -eq "9.1")                                |
| deviceCategory          | o nome de uma categoria de dispositivo válida       | (device.deviceCategory -eq "BYOD")                          |
| deviceManufacturer      | Um valor de cadeia de caracteres.                   | (device.deviceManufacturer -eq "Samsung")                   |
| deviceModel             | Um valor de cadeia de caracteres.                   | (device.deviceModel -eq "iPad Air”)                         |
| deviceOwnership         | Pessoal, empresa                  | (device.deviceOwnership -eq "Company")                      |
| domainName              | Um valor de cadeia de caracteres.                   | (device.domainName -eq "contoso.com")                       |
| enrollmentProfileName   | Nome do perfil de registro do dispositivo da Apple    | (device.enrollmentProfileName -eq "DEP iPhones")       |
| isRooted                | verdadeiro, falso                         | (device.isRooted -eq true)                              |
| managementType          | "MDM" para dispositivos móveis, "Computador" para computadores gerenciado por meio do agente de computador do Intune    | (device.managementType -eq "MDM")                  |
| organizationalUnit      | qualquer valor de cadeia de caracteres correspondente à unidade organizacional definida pelo Active Directory local | (device.organizationalUnit -eq "US PCs")      |
| deviceId                | um deviceId Intune válido                | (device.deviceId -eq "d4fe7726-5966-431c-b3b8-cddc8fdb717d") |
| ID do objeto                | uma objectId do Azure AD válida            | (device.objectId -eq "76ad43c9-32c5-45e8-a272-7b58b58f596d") |

> [!NOTE]
> As regras de dispositivo não podem ser criadas usando o menu suspenso "regra simples" no portal clássico do Azure.
>
>

## <a name="changing-dynamic-membership-to-static-and-vice-versa"></a>Alterando a associação dinâmica para estática e vice-versa
É possível alterar como a associação é gerida em um grupo. Isso é útil quando você quer manter o mesmo ID e nome de grupo no sistema, então, todas as referências existentes no grupo ainda serão válidas; criar um novo grupo exigiria atualizar essas referências.

> [!WARNING]
> Ao alterar um grupo estático existente para um grupo dinâmico, todos os membros existentes serão removidos do grupo e, em seguida, a regra de associação será processada para adicionar novos membros. Se o grupo for utilizado para controlar o acesso a aplicativos ou recursos, os membros originais poderão perder o acesso até que a regra de associação seja totalmente processada.
>
> É uma prática recomendada testar previamente a nova regra de associação para garantir que a nova associação no grupo seja conforme o esperado.

1. No [Portal Clássico do Azure](https://manage.windowsazure.com), abra o grupo.
2. Selecione a guia **Configurar** para visualizar o estado atual da associação dinâmica.
3. Para tornar um grupo estático, simplesmente alterne **Habilitar associações dinâmicas** definindo para **NÃO**. Para confirmar, clique no botão **Salvar** na barra de ferramentas abaixo. Os membros existentes serão mantidos no grupo e, a partir de agora, a regra de associação não será processada.
4. Para tornar um grupo dinâmico, alterne a configuração para **SIM**, especifique a regra de associação desejada e clique em **Salvar**. Os membros existentes serão removidos e a nova regra iniciará o processamento para adicionar novos membros.

## <a name="next-steps"></a>Próximas etapas
Esses artigos fornecem mais informações sobre o Active Directory do Azure.

* [Solucionando problemas de associações dinâmicas a grupos](active-directory-accessmanagement-troubleshooting.md)
* [Gerenciamento de acesso a recursos com grupos do Active Directory do Azure](active-directory-manage-groups.md)
* [Cmdlets do Azure Active Directory para definir configurações de grupo](active-directory-accessmanagement-groups-settings-cmdlets.md)
* [Índice de artigos para Gerenciamento de Aplicativos no Active Directory do Azure](active-directory-apps-index.md)
* [Integração de suas identidades locais com o Active Directory do Azure](active-directory-aadconnect.md)
