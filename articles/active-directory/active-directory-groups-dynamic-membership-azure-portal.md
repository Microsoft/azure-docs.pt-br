---
title: "Associação dinâmica de grupo baseada em atributos no Azure Active Directory | Microsoft Docs"
description: "Como criar regras avançadas para uma associação de grupo dinâmico, incluindo parâmetros e operadores de regra de expressões com suporte."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: fb434cc2-9a91-4ebf-9753-dd81e289787e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.author: curtand
ms.reviewer: piotrci
ms.custom: H1Hack27Feb2017;it-pro
ms.openlocfilehash: 3ff347ab23c9150246940f563e562c8de92be45d
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-attribute-based-rules-for-dynamic-group-membership-in-azure-active-directory"></a>Criar regras baseadas em atributo para associação dinâmica de grupo no Azure Active Directory
No Azure AD (Azure Active Directory), você pode criar regras avançadas para habilitar associações dinâmicas baseadas em atributos complexas para grupos. Este artigo detalha os atributos e a sintaxe para criar regras de associação dinâmica para usuários ou dispositivos.

Quando os atributos de um usuário ou um dispositivo são alterados, o sistema avalia todas as regras de grupo dinâmicas em um diretório para ver se a alteração dispararia adições ou remoções de grupo. Se um usuário ou dispositivo atender a uma regra em um grupo, ele será adicionado como membro desse grupo. Se não atenderem mais à regra, eles serão removidos.

> [!NOTE]
> - Você pode configurar uma regra de associação dinâmica em grupos de segurança ou em grupos do Office 365.
>
> - Este recurso exige uma licença Azure AD Premium P1 para cada membro de usuário adicionado a pelo menos um grupo dinâmico.
>
> - Você pode criar um grupo dinâmico para usuários ou dispositivos, mas não pode criar uma regra que contenha objetos de usuário e de dispositivo.

> - Nesse momento não é possível criar um grupo de dispositivos com base em atributos do usuário proprietário. As regras de associação de dispositivo só podem fazer referência a atributos imediatos dos objetos de dispositivo no diretório.

## <a name="to-create-an-advanced-rule"></a>Para criar uma regra avançada
1. Entre no [centro de administração do Azure AD](https://aad.portal.azure.com) com uma conta que seja um administrador global ou um administrador da conta de usuário.
2. Selecione **Usuários e grupos**.
3. Selecione **Todos os grupos** e selecione **Novo grupo**.

   ![Adicione o novo grupo](./media/active-directory-groups-dynamic-membership-azure-portal/new-group-creation.png)

4. Na folha **Grupo** , insira um nome e uma descrição para o novo grupo. Selecione o **Tipo de associação** **Usuário Dinâmico** ou **Dispositivo Dinâmico**, dependendo se você deseja criar uma regra para usuários ou dispositivos e, em seguida, selecione **Adicionar consulta dinâmica**. Você pode usar o construtor de regra para criar uma regra simples ou gravar uma regra avançada por conta própria. Este artigo contém mais informações sobre os atributos de usuário e dispositivo disponíveis, bem como exemplos de regras avançadas.

   ![Adicionar regra de associação dinâmica](./media/active-directory-groups-dynamic-membership-azure-portal/add-dynamic-group-rule.png)

5. Depois de criar a regra, selecione **Adicionar consulta** na parte inferior da folha.
6. Selecione **Criar** on the **Grupo** para criar o grupo.

> [!TIP]
> A criação de grupo pode falhar se a regra avançada que você inseriu estava incorreta. Será exibida uma notificação no canto superior direito do portal; ela contém uma explicação do porquê da regra não ter sido aceita pelo sistema. Leia com cuidado para entender como você precisa ajustar a regra para torná-la válida.

## <a name="constructing-the-body-of-an-advanced-rule"></a>Construção do corpo de uma regra avançada
A regra avançada que você pode criar para os membros dinâmicos para grupos é essencialmente uma expressão binária que consiste em três partes e resulta em um resultado verdadeiro ou falso. As três partes são:

* Parâmetro da esquerda
* Operador binário
* Constante à direita

Uma regra avançada completa é semelhante a esta: (leftParameter binaryOperator "RightConstant"), em que os parênteses de abertura e fechamento são opcionais para toda a expressão binária, as aspas duplas também são opcionais, sendo necessárias somente para a constante à direita quando ela for uma cadeia de caracteres e a sintaxe do parâmetro esquerdo é user.property. Uma regra avançada pode consistir em mais de uma expressão binária separada pelos operadores lógicos -and, -or e -not.

A seguir é exemplos de uma regra avançada construída de maneira adequada:
```
(user.department -eq "Sales") -or (user.department -eq "Marketing")
(user.department -eq "Sales") -and -not (user.jobTitle -contains "SDE")
```
Para a lista completa de parâmetros com suporte e operadores de regra de expressão, consulte as seções a seguir. Para os atributos usados para regras de dispositivo, consulte [Usando atributos para criar regras para objetos de dispositivo](#using-attributes-to-create-rules-for-device-objects).

O comprimento total do corpo da sua regra avançada não pode exceder 2048 caracteres.

> [!NOTE]
> Operações de cadeia de caracteres e regex não diferenciam maiúsculas de minúsculas. Você também pode executar verificações de Null, usando $null como uma constante, por exemplo, user.department - eq $null.
> As cadeias de caracteres que contém aspas " devem ser ignoradas usando caracteres ', por exemplo, user.department -eq \`"Sales".

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

Todos os operadores estão listados segundo sua precedência, de baixa para alta. Operadores na mesma linha têm a mesma precedência:
````
-any -all
-or
-and
-not
-eq -ne -startsWith -notStartsWith -contains -notContains -match –notMatch -in -notIn
````
Todos os operadores podem ser usados com ou sem o prefixo de hífen. Parênteses são necessários somente quando a precedência não atender às suas necessidades.
Por exemplo:
```
   user.department –eq "Marketing" –and user.country –eq "US"
```
é equivalente a:
```
   (user.department –eq "Marketing") –and (user.country –eq "US")
```
## <a name="using-the--in-and--notin-operators"></a>Usando os operadores -In e -notIn

Se quiser comparar o valor de um atributo de usuário com um número de valores diferentes, você pode usar os operadores -In ou -notIn. Veja um exemplo de uso do operador -In:
```
    user.department -In [ "50001", "50002", "50003", “50005”, “50006”, “50007”, “50008”, “50016”, “50020”, “50024”, “50038”, “50039”, “51100” ]
```
Observe o uso de "[" e "]" no início e no final da lista de valores. Essa condição é avaliada como verdadeira se o valor de user.department for igual a um dos valores na lista.


## <a name="query-error-remediation"></a>Correção do erro de consulta
A seguinte tabela relacionará os erros comuns e como corrigi-los

| Erro de análise de consulta | Erros de uso | Uso corrigido |
| --- | --- | --- |
| Erro: O atributo não tem suportado. |(user.invalidProperty -eq "Valor") |(user.department -eq "value") A propriedade <br/><br/>Verifique se o atributo está na [lista de propriedades com suporte](#supported-properties). |
| Erro: Operador não é tem suportada no atributo. |(user.accountEnabled -contains true) |(user.accountEnabled -eq true) A propriedade <br/><br/>Não há suporte para o operador usado para o tipo de propriedade (neste exemplo, -contains não pode ser usado no tipo booliano). Use os operadores corretos para o tipo de propriedade. |
| Erro: Erro de compilação de consulta. |1. (user.department -eq "Sales") (user.department -eq "Marketing")<br/><br/>2. (user.userPrincipalName -match "*@domain.ext") |1. Operador ausente. Use -and ou -or para unir predicados<br/><br/>(user.department -eq "Sales") -or (user.department -eq "Marketing")<br/><br/>2.Erro na expressão regular usada com -match<br/><br/>(user.userPrincipalName -match ".*@domain.ext"), como alternativa: (user.userPrincipalName -match "@domain.ext$")|

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
| proxyAddresses |SMTP:alias@domainsmtp:alias@domain |(user.proxyAddresses -contains "SMTP: alias@domain") |

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
```
   user.mail –ne null
```
é equivalente a
```
   user.mail –ne $null
   ```

## <a name="extension-attributes-and-custom-attributes"></a>Atributos de extensão e atributos personalizados
Os atributos de extensão e os atributos personalizados têm suporte das regras de associação dinâmica.

Os atributos de extensão são sincronizados no AD do Windows Server local e têm o formato "ExtensionAttributeX", em que X é igual a 1 a 15.
Um exemplo de uma regra que usa um atributo de extensão:
```
(user.extensionAttribute15 -eq "Marketing")
```
Os Atributos Personalizados são sincronizados do AD do Windows Server local ou de um aplicativo SaaS conectado e têm formato "user.extension[GUID]\__[Atributo]", em que [GUID] é o identificador exclusivo no AAD para o aplicativo que criou o atributo no AAD e [Atributo] é o nome do atributo como ele foi criado.
Um exemplo de uma regra que usa um atributo personalizado:
```
user.extension_c272a57b722d4eb29bfe327874ae79cb__OfficeNumber  
```
O nome do atributo personalizado pode ser encontrado no diretório por meio da consulta do atributo de um usuário, usando o Graph Explorer e procurando o nome do atributo.

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
Você também pode criar uma regra que seleciona objetos de dispositivo para associação em um grupo. Os seguintes atributos de dispositivo podem ser usados.

 Atributo do dispositivo  | Valores | Exemplo
 ----- | ----- | ----------------
 accountEnabled | verdadeiro, falso | (device.accountEnabled -eq true)
 displayName | Um valor de cadeia de caracteres. |(device.displayName -eq "Rob Iphone”)
 deviceOSType | Um valor de cadeia de caracteres. | (device.deviceOSType -eq "iPad") -or (device.deviceOSType -eq "iPhone")
 deviceOSVersion | Um valor de cadeia de caracteres. | (device.OSVersion -eq "9.1")
 deviceCategory | o nome de uma categoria de dispositivo válida | (device.deviceCategory -eq "BYOD")
 deviceManufacturer | Um valor de cadeia de caracteres. | (device.deviceManufacturer -eq "Samsung")
 deviceModel | Um valor de cadeia de caracteres. | (device.deviceModel -eq "iPad Air")
 deviceOwnership | Pessoal, Empresa, Desconhecido | (device.deviceOwnership -eq "Company")
 domainName | Um valor de cadeia de caracteres. | (device.domainName -eq "contoso.com")
 enrollmentProfileName | Nome do perfil de registro do dispositivo da Apple | (device.enrollmentProfileName -eq "DEP iPhones")
 isRooted | verdadeiro, falso | (device.isRooted -eq true)
 managementType | MDM (para dispositivos móveis)<br>PC (para computadores gerenciados pelo agente de PC do Intune) | (device.managementType -eq "MDM")
 organizationalUnit | qualquer valor de cadeia de caracteres correspondente ao nome da unidade organizacional definido por um Active Directory local | (device.organizationalUnit -eq "US PCs")
 deviceId | uma ID de dispositivo do Azure AD válida | (device.deviceId -eq "d4fe7726-5966-431c-b3b8-cddc8fdb717d")
 ID do objeto | uma ID de objeto do Azure AD válida |  (device.objectId -eq 76ad43c9-32c5-45e8-a272-7b58b58f596d")



## <a name="changing-dynamic-membership-to-static-and-vice-versa"></a>Alterando a associação dinâmica para estática e vice-versa
É possível alterar como a associação é gerida em um grupo. Isso é útil quando você quer manter o mesmo ID e nome de grupo no sistema, então, todas as referências existentes no grupo ainda serão válidas; criar um novo grupo exigiria atualizar essas referências.

Estamos atualizando o portal do Azure para oferecer suporte a essa funcionalidade. Enquanto isso, você pode usar o [portal clássico do Azure](https://manage.windowsazure.com) (siga as instruções [aqui](active-directory-accessmanagement-groups-with-advanced-rules.md#changing-dynamic-membership-to-static-and-vice-versa)) ou use o cmdlets do PowerShell, conforme mostrado abaixo.

> [!WARNING]
> Ao alterar um grupo estático existente para um grupo dinâmico, todos os membros existentes serão removidos do grupo e, em seguida, a regra de associação será processada para adicionar novos membros. Se o grupo for utilizado para controlar o acesso a aplicativos ou recursos, os membros originais poderão perder o acesso até que a regra de associação seja totalmente processada.
>
> É uma prática recomendada testar previamente a nova regra de associação para garantir que a nova associação no grupo seja conforme o esperado.

**Usando o PowerShell para alterar o gerenciamento de associação em um grupo**

> [!NOTE]
> Para alterar as propriedades de grupo dinâmico, você precisará usar os cmdlets da [versão 2 do PowerShell do Azure AD](https://docs.microsoft.com/en-us/powershell/azure/active-directory/install-adv2?view=azureadps-2.0). Você pode instalá-lo clicando [aqui](https://www.powershellgallery.com/packages/AzureADPreview).

Aqui está um exemplo de funções que alternam o gerenciamento de associação em um grupo existente. Observe que toma-se cuidado ao manipular a propriedade GroupTypes corretamente e preservar quaisquer valores que possam existir, não relacionados à associação dinâmica.

```
#The moniker for dynamic groups as used in the GroupTypes property of a group object
$dynamicGroupTypeString = "DynamicMembership"

function ConvertDynamicGroupToStatic
{
    Param([string]$groupId)

    #existing group types
    [System.Collections.ArrayList]$groupTypes = (Get-AzureAdMsGroup -Id $groupId).GroupTypes

    if($groupTypes -eq $null -or !$groupTypes.Contains($dynamicGroupTypeString))
    {
        throw "This group is already a static group. Aborting conversion.";
    }


    #remove the type for dynamic groups, but keep the other type values
    $groupTypes.Remove($dynamicGroupTypeString)

    #modify the group properties to make it a static group: i) change GroupTypes to remove the dynamic type, ii) pause execution of the current rule
    Set-AzureAdMsGroup -Id $groupId -GroupTypes $groupTypes.ToArray() -MembershipRuleProcessingState "Paused"
}

function ConvertStaticGroupToDynamic
{
    Param([string]$groupId, [string]$dynamicMembershipRule)

    #existing group types
    [System.Collections.ArrayList]$groupTypes = (Get-AzureAdMsGroup -Id $groupId).GroupTypes

    if($groupTypes -ne $null -and $groupTypes.Contains($dynamicGroupTypeString))
    {
        throw "This group is already a dynamic group. Aborting conversion.";
    }
    #add the dynamic group type to existing types
    $groupTypes.Add($dynamicGroupTypeString)

    #modify the group properties to make it a static group: i) change GroupTypes to add the dynamic type, ii) start execution of the rule, iii) set the rule
    Set-AzureAdMsGroup -Id $groupId -GroupTypes $groupTypes.ToArray() -MembershipRuleProcessingState "On" -MembershipRule $dynamicMembershipRule
}
```
Para tornar um grupo estático:
```
ConvertDynamicGroupToStatic "a58913b2-eee4-44f9-beb2-e381c375058f"
```
Para tornar um grupo estático:
```
ConvertStaticGroupToDynamic "a58913b2-eee4-44f9-beb2-e381c375058f" "user.displayName -startsWith ""Peter"""
```
## <a name="next-steps"></a>Próximas etapas
Esses artigos fornecem mais informações sobre grupos no Azure Active Directory.

* [Consultar grupos existentes](active-directory-groups-view-azure-portal.md)
* [Criar um novo grupo e adicionando membros](active-directory-groups-create-azure-portal.md)
* [Gerenciar configurações de um grupo](active-directory-groups-settings-azure-portal.md)
* [Gerenciar associações de um grupo](active-directory-groups-membership-azure-portal.md)
* [Gerenciar regras dinâmicas para usuários em um grupo](active-directory-groups-dynamic-membership-azure-portal.md)
