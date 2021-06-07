---
title: Regras para associação de grupos preenchidos dinamicamente – Azure AD | Microsoft Docs
description: Como criar regras de associação para preencher automaticamente os grupos e uma referência de regra.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: overview
ms.date: 02/18/2021
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c25504e3313234ac6b6f80a6e00c77fce28b1400
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102174522"
---
# <a name="dynamic-membership-rules-for-groups-in-azure-active-directory"></a>Associação dinâmica do Azure Active Directory para grupos

No Active Directory do Azure (Azure AD), você pode criar regras complexas baseadas em atributos para habilitar associações dinâmicas para grupos. Associação de grupos dinâmica reduz a sobrecarga administrativa de adicionar e remover usuários. Este artigo detalha as propriedades e a sintaxe para criar regras de associação dinâmica para usuários ou dispositivos. Você pode configurar uma regra de associação dinâmica em grupos de segurança ou em grupos do Microsoft 365.

Quando os atributos de um usuário ou um dispositivo são alterados, o sistema avalia todas as regras de grupo dinâmicas em um diretório para ver se a alteração dispararia adições ou remoções de grupo. Se um usuário ou dispositivo atender a uma regra em um grupo, ele será adicionado como membro desse grupo. Se não atenderem mais à regra, eles serão removidos. Manualmente, não é possível adicionar nem remover um membro de um grupo dinâmico.

- Você pode criar um grupo dinâmico para dispositivos ou usuários, mas não pode criar uma regra que contenha tanto usuários quanto dispositivos.
- Você não pode criar um grupo de dispositivos com base em atributos os proprietários do dispositivo. Regras de associação de dispositivo só podem fazer referência a atributos do dispositivo.

> [!NOTE]
> Este recurso requer uma licença do Azure AD Premium P1 para cada usuário exclusivo que for um membro de um ou mais grupos dinâmicos. Você não precisa atribuir licenças aos usuários para que eles sejam membros de grupos dinâmicos, mas é necessário ter o número mínimo de licenças na organização do Azure AD para cobrir todos esses usuários. Por exemplo, se você tiver o total de 1.000 usuários exclusivos em todos os grupos dinâmicos na organização, serão necessárias pelo menos 1.000 licenças do Azure AD Premium P1 para atender ao requisito de licença.
> Nenhuma licença é necessária para dispositivos que são membros de um grupo de dispositivos dinâmico.

## <a name="rule-builder-in-the-azure-portal"></a>Construtor de regras no portal do Azure

O Azure AD fornece um construtor de regras para criar e atualizar suas regras importantes mais rapidamente. O construtor de regras dá suporte à construção de até cinco expressões. O construtor de regras torna mais fácil formar uma regra com algumas expressões simples, no entanto, ela não pode ser usada para reproduzir todas as regras. Se o construtor de regras não oferecer suporte à regra que você deseja criar, você poderá usar a caixa de texto.

Aqui estão alguns exemplos de regras ou sintaxe avançadas para as quais recomendamos que você construa usando a caixa de texto:

- Regra com mais de cinco expressões
- A regra de Subordinados diretos
- Como definir a [precedência do operador](#operator-precedence)
- [Regras com expressões complexas](#rules-with-complex-expressions); por exemplo `(user.proxyAddresses -any (_ -contains "contoso"))`

> [!NOTE]
> O construtor de regras pode não conseguir exibir algumas regras construídas na caixa de texto. Você poderá ver uma mensagem quando o construtor de regras não puder exibir a regra. O construtor de regras não altera a sintaxe com suporte, a validação nem o processamento de regras de grupo dinâmicas de modo algum.

Para obter instruções passo a passo, confira [Criar ou atualizar um grupo dinâmico](groups-create-rule.md).

![Adicionar regra de associação a um grupo dinâmico](./media/groups-dynamic-membership/update-dynamic-group-rule.png)

### <a name="rule-syntax-for-a-single-expression"></a>Sintaxe de regra para uma única expressão

Uma única expressão é a forma mais simples de uma regra de associação e só tem as três partes mencionadas acima. Uma regra com uma única expressão é semelhante a este: `Property Operator Value`, em que a sintaxe para a propriedade é o nome do object.property.

Este é um exemplo de uma regra de associação construída de maneira adequada com uma única expressão:

```
user.department -eq "Sales"
```

Parênteses são opcionais para uma única expressão. O comprimento total do corpo da sua regra de associação não pode exceder 2048 caracteres.

## <a name="constructing-the-body-of-a-membership-rule"></a>Construção do corpo de uma regra de associação

Uma regra de associação que preenche automaticamente um grupo de usuários ou dispositivos é uma expressão binária que resulta em um resultado verdadeiro ou falso. As três partes de uma regra simples são:

- Propriedade
- Operador
- Valor

A ordem das partes dentro de uma expressão é importante para evitar erros de sintaxe.

## <a name="supported-properties"></a>Propriedades com suporte

Há três tipos de propriedades que podem ser usadas para construir uma regra de associação.

- Boolean
- String
- Coleção de Cadeias de Caracteres

Estas são todas as propriedades do usuário que você pode usar para criar uma expressão única.

### <a name="properties-of-type-boolean"></a>Propriedades de tipo booliano

| Propriedades | Valores permitidos | Uso |
| --- | --- | --- |
| accountEnabled |verdadeiro, falso |user.accountEnabled -eq true |
| dirSyncEnabled |verdadeiro, falso |user.dirSyncEnabled -eq true |

### <a name="properties-of-type-string"></a>Propriedades do tipo cadeia de caracteres

| Propriedades | Valores permitidos | Uso |
| --- | --- | --- |
| city |Qualquer valor de cadeia de caracteres ou *null* |(user.city -eq "valor") |
| country |Qualquer valor de cadeia de caracteres ou *null* |(user.country -eq "valor") |
| companyName | Qualquer valor de cadeia de caracteres ou *null* | (user.companyName -eq "value") |
| department |Qualquer valor de cadeia de caracteres ou *null* |(user.department -eq "value") A propriedade |
| displayName |Qualquer valor de cadeia de caracteres |(user. DisplayName -eq "valor") |
| employeeId |Qualquer valor de cadeia de caracteres |(user.employeeId -eq "valor")<br>(user.employeeId -ne *null*) |
| facsimileTelephoneNumber |Qualquer valor de cadeia de caracteres ou *null* |user.facsimileTelephoneNumber -eq ("valor") |
| givenName |Qualquer valor de cadeia de caracteres ou *null* |user.givenName -eq ("valor") |
| jobTitle |Qualquer valor de cadeia de caracteres ou *null* |(user.jobTitle - eq "valor") |
| mail |Qualquer valor de cadeia de caracteres ou *null* (endereço SMTP do usuário) |(user.mail - eq "valor") |
| mailNickName |Qualquer valor de cadeia de caracteres (alias de email do usuário) |(user.mailNickName - eq "valor") |
| Serviço Móvel |Qualquer valor de cadeia de caracteres ou *null* |(user.mobile -eq "valor") |
| objectId |GUID do objeto de usuário |(user.objectId -eq "11111111-1111-1111-1111-111111111111") |
| onPremisesSecurityIdentifier | SID (ID de segurança) local para usuários que foram sincronizados do local para a nuvem. |(user.onPremisesSecurityIdentifier -eq "S-1-1-11-1111111111-1111111111-1111111111-1111111") |
| passwordPolicies |None DisableStrongPassword DisablePasswordExpiration DisablePasswordExpiration, DisableStrongPassword |(user.passwordPolicies -eq "DisableStrongPassword") |
| physicalDeliveryOfficeName |Qualquer valor de cadeia de caracteres ou *null* |(user.physicalDeliveryOfficeName -eq "valor") |
| postalCode |Qualquer valor de cadeia de caracteres ou *null* |(user.postalCode - eq "valor") |
| preferredLanguage |ISO 639-1 code |(user.preferredLanguage - eq "en-US") |
| sipProxyAddress |Qualquer valor de cadeia de caracteres ou *null* |(user.sipProxyAddress -eq "valor") |
| state |Qualquer valor de cadeia de caracteres ou *null* |(user.state -eq "valor") |
| streetAddress |Qualquer valor de cadeia de caracteres ou *null* |(user.streetAddress -eq "valor") |
| sobrenome |Qualquer valor de cadeia de caracteres ou *null* |(user.surname -eq "valor") |
| telephoneNumber |Qualquer valor de cadeia de caracteres ou *null* |(user.telephoneNumber -eq "valor") |
| usageLocation |Código de duas letras do país/região |(user.usageLocation -eq "EUA") |
| userPrincipalName |Qualquer valor de cadeia de caracteres |(user.userPrincipalName -eq "alias@domain") |
| userType |member guest *null* |(ser.userType -eq "Membro") |

### <a name="properties-of-type-string-collection"></a>Propriedades de coleção de cadeias de caracteres de tipo

| Propriedades | Valores permitidos | Uso |
| --- | --- | --- |
| otherMails |Qualquer valor de cadeia de caracteres |(user.otherMails -contains "alias@domain") |
| proxyAddresses |SMTP:alias@domainsmtp:alias@domain |(user.proxyAddresses -contains "SMTP: alias@domain") |

Para as propriedades usadas para regras de dispositivo, consulte [Regras para dispositivos](#rules-for-devices).

## <a name="supported-expression-operators"></a>Operadores de expressão com suporte

A tabela a seguir lista os operadores com suporte e sua sintaxe para uma única expressão. Os operadores podem ser usados com ou sem o prefixo de hífen (-).

| Operador | Sintaxe |
| --- | --- |
| Não é igual a |-ne |
| É igual a |-eq |
| Não começa com |-notStartsWith |
| Começa com |-startsWith |
| Não contém |-notContains |
| Contém |-contains |
| Não corresponde |-notMatch |
| Corresponder a |-match |
| No | -in |
| Não está em | -notIn |

### <a name="using-the--in-and--notin-operators"></a>Usando os operadores -in e -notIn

Se quiser comparar o valor de um atributo de usuário com um número de valores diferentes, você poderá usar os operadores -in ou -notIn. Usar os símbolos entre colchetes "[" e "]" para iniciar e finalizar a lista de valores.

 No exemplo a seguir, a expressão avalia como verdadeira se o valor de user.department for igual a um dos valores na lista:

```
   user.department -in ["50001","50002","50003","50005","50006","50007","50008","50016","50020","50024","50038","50039","51100"]
```


### <a name="using-the--match-operator"></a>Usando o operador -match 
O operador **-match** é usado para corresponder qualquer expressão regular. Exemplos:

```
user.displayName -match "Da.*"   
```
Da, Dav, David são avaliados como true, aDa é avaliado como false.

```
user.displayName -match ".*vid"
```
David é avaliado como true, Da é avaliado como false.

## <a name="supported-values"></a>Valores com suporte

Os valores usados em uma expressão podem consistir em vários tipos, incluindo:

* Cadeias de caracteres
* Booliano – verdadeiro, falso
* Números
* Matrizes – matriz de número, matriz de cadeia de caracteres

Ao especificar um valor dentro de uma expressão é importante usar a sintaxe correta para evitar erros. Algumas dicas de sintaxe são:

* Aspas duplas são opcionais, a menos que o valor seja uma cadeia de caracteres.
* Operações de cadeia de caracteres e regex não diferenciam maiúsculas de minúsculas.
* Quando um valor de cadeia de caracteres contiver aspas duplas, ambas as aspas devem ser ignoradas usando o \` caracteres, por exemplo, User. Department - eq \`"Vendas\`" é a sintaxe adequada quando o valor "Vendas".
* Você também pode executar os valores de Null, usando null como valor, por exemplo, `user.department -eq null`.

### <a name="use-of-null-values"></a>Uso de valores nulos

Para especificar um valor nulo em uma regra, você pode usar o valor *null*. 

* Use -eq ou -ne quando comparado o valor de *null* em uma expressão.
* Usa cotações em torno da palavra *null* apenas se você quiser que seja interpretado como valor de cadeia de caracteres literal.
* O operador -not não pode ser usado como um operador comparativo para nulo. Se for utilizado, você receberá um erro se usar nulo ou $null.

A maneira correta para referenciar o valor nulo é da seguinte maneira:

```
   user.mail –ne null
```

## <a name="rules-with-multiple-expressions"></a>Regras com várias expressões

Uma regra de associação de grupo pode consistir de mais de uma expressão única conectada pelos operadores lógicos -e, -ou e -não. Operadores lógicos também podem ser usados em combinação. 

A seguir estão exemplos de regras de associação construídas de maneira adequada com várias expressões:

```
(user.department -eq "Sales") -or (user.department -eq "Marketing")
(user.department -eq "Sales") -and -not (user.jobTitle -contains "SDE")
```

### <a name="operator-precedence"></a>Precedência do operador

Todos os operadores estão listados abaixo na ordem de precedência da mais alta para a mais baixa. Os operadores na mesma linha têm a mesma precedência:

```
-eq -ne -startsWith -notStartsWith -contains -notContains -match –notMatch -in -notIn
-not
-and
-or
-any -all
```

Este é um exemplo de precedência de operador em que as duas expressões são sendo avaliadas para o usuário:

```
   user.department –eq "Marketing" –and user.country –eq "US"
```

Parênteses são necessários somente quando a precedência não atender às suas necessidades. Por exemplo, se desejar que o departamento a seja avaliada pela primeira vez, a seguir mostra como os parênteses podem ser usados para determinar a ordem:

```
   user.country –eq "US" –and (user.department –eq "Marketing" –or user.department –eq "Sales")
```

## <a name="rules-with-complex-expressions"></a>Regras com expressões complexas

Uma regra de associação pode consistir em expressões complexas, onde as propriedades, operadores e valores assumem formas mais complexas. Expressões são consideradas complexas quando qualquer uma das seguintes opções for verdadeira:

* A propriedade consiste em uma coleção de valores; Especificamente, as propriedades com vários valores
* As expressões usam -qualquer e - todos os operadores
* O valor da expressão pode ser uma ou mais expressão

## <a name="multi-value-properties"></a>Propriedades de vários valores

As propriedades de vários valores são coleções de objetos do mesmo tipo. Eles podem ser usados para criar regras de associação usando a opção - any e - todos os operadores lógicos.

| Propriedades | Valores | Uso |
| --- | --- | --- |
| assignedPlans | Cada objeto na coleção expõe as seguintes propriedades de cadeia de caracteres: capabilityStatus, service, servicePlanId |user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -and assignedPlan.capabilityStatus -eq "Enabled") |
| proxyAddresses| SMTP:alias@domainsmtp:alias@domain | (user.proxyAddresses -qualquer (\_ -contém "contoso")) |

### <a name="using-the--any-and--all-operators"></a>Usando os operadores -qualquer e -todos

Você pode usar os operadores -any e -all para aplicar uma condição a um ou todos os itens na coleção, respectivamente.

* -any (satisfeita quando pelo menos um item na coleção corresponde à condição)
* -all (satisfeita quando todos os itens na coleção correspondem à condição)

#### <a name="example-1"></a>Exemplo 1

assignedPlans é uma propriedade de valores múltiplos que lista todos os planos de serviço atribuídos ao usuário. A expressão abaixo selecionará os usuários que tenham o plano do serviço Exchange Online (Plano 2) (como o valor GUID) e que também estejam no estado Habilitado:

```
user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -and assignedPlan.capabilityStatus -eq "Enabled")
```

Uma regra como esta pode ser usada para agrupar todos os usuários para os quais uma funcionalidade do Microsoft 365 (ou de outro Serviço Online da Microsoft) está habilitada. Você poderia aplicar com um conjunto de políticas ao grupo.

#### <a name="example-2"></a>Exemplo 2

A expressão a seguir seleciona todos os usuários que possuem algum plano de serviço associado ao serviço do Intune (identificado pelo nome de serviço "SCO"):

```
user.assignedPlans -any (assignedPlan.service -eq "SCO" -and assignedPlan.capabilityStatus -eq "Enabled")
```

#### <a name="example-3"></a>Exemplo 3

A seguinte expressão seleciona todos os usuários que não têm nenhum plano de serviço atribuído:

```
user.assignedPlans -all (assignedPlan.servicePlanId -eq "")
```

### <a name="using-the-underscore-_-syntax"></a>Usando a sintaxe de sublinhado (\_)

A sintaxe de sublinhado (\_) corresponde a ocorrências de um valor específico em uma das propriedades de coleção de cadeias de valores variados para adicionar usuários ou dispositivos a um grupo dinâmico. Ele é usado com-qualquer ou - todos os operadores.

Aqui está um exemplo do uso do sublinhado (\_) em uma regra para adicionar membros com base em user.proxyAddress (funciona da mesma forma para user.otherMails). Essa regra adiciona qualquer usuário com endereço de proxy que contenha "contoso" ao grupo.

```
(user.proxyAddresses -any (_ -contains "contoso"))
```

## <a name="other-properties-and-common-rules"></a>Outras propriedades e as regras comuns

### <a name="create-a-direct-reports-rule"></a>Criar regra de "relatórios diretos”

Você pode criar um grupo contendo todos os subordinados diretos de um gerente. Quando os subordinados diretos do gerente forem alterados no futuro, a associação do grupo será ajustada automaticamente.

Os relatórios diretos são construídos usando a sintaxe a seguir:

```
Direct Reports for "{objectID_of_manager}"
```

Aqui está um exemplo de regra válida em que "62e19b97-8b3d-4d4a-a106-4ce66896a863" é a objectID do gerente:

```
Direct Reports for "62e19b97-8b3d-4d4a-a106-4ce66896a863"
```

As dicas a seguir podem ajudá-lo a usar a regra corretamente.

- O **Manager ID** é a ID de objeto do Gerenciador. Ele pode ser encontrado no Gerenciador de **Perfil**.
- Para que a regra funcione, verifique se a propriedade **ID do Gerenciador** está definida corretamente nos usuários em sua organização. Você pode verificar o valor atual no **Perfil** do usuário.
- Essa regra dá suporte a apenas os relatórios de diretos do gerente. Em outras palavras, é possível criar um grupo com subordinados diretos do gerente *e* seus relatórios.
- Esta regra não pode ser combinada com nenhuma outra regra avançada.

### <a name="create-an-all-users-rule"></a>Criar uma regra de "Todos os usuários"

Você pode criar um grupo contendo todos os usuários dentro de uma organização usando uma regra de associação. Quando os usuários são adicionados ou removidos da organização no futuro, a associação do grupo é ajustada automaticamente.

A regra "Todos os usuários" é construída usando a única expressão usando o operador -ne e o valor nulo. Essa regra adiciona usuários convidados de B2B, bem como os usuários de membro ao grupo.

```
user.objectId -ne null
```
Se você quiser que o grupo exclua os usuários convidados e inclua somente os membros da sua organização, poderá usar a seguinte sintaxe:

```
(user.objectId -ne null) -and (user.userType -eq "Member")
```

### <a name="create-an-all-devices-rule"></a>Criar uma regra "Todos os dispositivos"

Você pode criar um grupo contendo todos os dispositivos dentro de uma organização usando uma regra de associação. Quando os dispositivos são adicionados ou removidos da organização no futuro, a associação do grupo é ajustada automaticamente.

A regra "Todos os Dispositivos" é construída usando a única expressão usando o operador -ne e o valor nulo:

```
device.objectId -ne null
```

## <a name="extension-properties-and-custom-extension-properties"></a>As propriedades de extensão e as propriedades de extensão personalizado

Os atributos de extensão e as propriedades de extensão personalizada têm suporte como propriedades de cadeia de caracteres nas regras de associação dinâmica. Os [atributos de extensão](/graph/api/resources/onpremisesextensionattributes) são sincronizados no AD do Windows Server local e têm o formato "ExtensionAttributeX", em que X é igual a 1 a 15. Um exemplo de uma regra que usa um atributo de extensão como propriedade:

```
(user.extensionAttribute15 -eq "Marketing")
```

As [propriedades de extensão personalizadas](../hybrid/how-to-connect-sync-feature-directory-extensions.md) são sincronizadas no AD do Windows Server local ou de um aplicativo SaaS conectado e estão no formato de `user.extension_[GUID]_[Attribute]`, em que:

* [GUID] é o identificador exclusivo no Microsoft Azure Active Directory para o aplicativo que criou a propriedade do Microsoft Azure Active Directory
* [Atributo] é o nome da propriedade como ele foi criado

Um exemplo de uma regra que usa um atributo personalizado:

```
user.extension_c272a57b722d4eb29bfe327874ae79cb_OfficeNumber -eq "123"
```

O nome da propriedade personalizada pode ser encontrado no diretório por meio da consulta do atributo de um usuário, usando o Graph Explorer e procurando o nome do atributo. Ainda, agora você pode selecionar o link **Obter propriedades de extensão personalizadas** no construtor de regra dinâmico de grupo dos usuários para inserir uma ID do aplicativo exclusiva e receber a lista completa das propriedades de extensão personalizadas para usar ao criar uma regra de associação dinâmica. Essa lista também pode ser atualizada para obter as novas propriedades de extensão personalizadas do aplicativo.

## <a name="rules-for-devices"></a>Regras para dispositivos

Você também pode criar uma regra que seleciona objetos de dispositivo para associação em um grupo. Você não pode ter usuários e dispositivos como membros do grupo. 

> [!NOTE]
> O atributo **organizationalUnit** não está mais listado e não deve ser usado. Essa cadeia de caracteres é definida pelo Intune em casos específicos, mas não é reconhecida pelo Azure AD, portanto, não há dispositivos são adicionados aos grupos com base nesse atributo.

> [!NOTE]
> systemlabels é um atributo somente leitura que não pode ser definido com o Intune.
>
> Para o Windows 10, o formato correto do atributo deviceOSVersion é: (device.deviceOSVersion- eq "10.0.17763"). A formatação pode ser validada com o cmdlet Get-MsolDevice do PowerShell.

Os seguintes atributos de dispositivo podem ser usados.

 Atributo do dispositivo  | Valores | Exemplo
 ----- | ----- | ----------------
 accountEnabled | verdadeiro, falso | (device.accountEnabled -eq true)
 displayName | qualquer valor de cadeia de caracteres |(device.displayName -eq "Rob iPhone")
 deviceOSType | qualquer valor de cadeia de caracteres | (device.deviceOSType -eq "iPad") -or (device.deviceOSType -eq "iPhone")<br>(device.deviceOSType -contains "AndroidEnterprise")<br>(device.deviceOSType -eq "AndroidForWork")<br>(device.deviceOSType -eq "Windows")
 deviceOSVersion | qualquer valor de cadeia de caracteres | (device.deviceOSVersion -eq "9.1")<br>(device.deviceOSVersion -eq "10.0.17763.0")
 deviceCategory | o nome de uma categoria de dispositivo válida | (device.deviceCategory -eq "BYOD")
 deviceManufacturer | qualquer valor de cadeia de caracteres | (device.deviceManufacturer -eq "Samsung")
 deviceModel | qualquer valor de cadeia de caracteres | (device.deviceModel -eq "iPad Air")
 deviceOwnership | Pessoal, Empresa, Desconhecido | (device.deviceOwnership -eq "Company")
 enrollmentProfileName | Nome do Perfil de Registro de Dispositivo Apple, nome do Perfil de Registro de dispositivo dedicado de propriedade do Android Enterprise Corporativo ou nome do perfil do Windows Autopilot | (device.enrollmentProfileName -eq "DEP iPhones")
 isRooted | verdadeiro, falso | (device.isRooted -eq true)
 managementType | MDM (para dispositivos móveis)<br>PC (para computadores gerenciados pelo agente de PC do Intune) | (device.managementType -eq "MDM")
 deviceId | uma ID de dispositivo do Azure AD válida | (device.deviceId -eq "d4fe7726-5966-431c-b3b8-cddc8fdb717d")
 objectId | uma ID de objeto do Azure AD válida |  (device.objectId -eq "76ad43c9-32c5-45e8-a272-7b58b58f596d")
 devicePhysicalIds | qualquer valor de cadeia de caracteres usado pelo AutoPilot, como todos os dispositivos Autopilot, OrderID ou PurchaseOrderID  | (device.devicePhysicalIDs -any _ -contains "[ZTDId]") (device.devicePhysicalIds -any _ -eq "[OrderID]:179887111881") (device.devicePhysicalIds -any _ -eq "[PurchaseOrderId]:76222342342")
 systemLabels | qualquer cadeia de caracteres correspondente à propriedade de dispositivo do Intune para marcação de dispositivos de Local de Trabalho Moderno | (device.systemLabels -contains "M365Managed")

> [!Note]  
> Para o deviceOwnership ao criar grupos dinâmicos para dispositivos, é necessário definir o valor igual a "Company". No Intune, a propriedade do dispositivo é representada como Corporate. Consulte [OwnerTypes](/intune/reports-ref-devices#ownertypes) para obter mais detalhes. 

## <a name="next-steps"></a>Próximas etapas

Esses artigos fornecem mais informações sobre grupos no Azure Active Directory.

- [Consultar grupos existentes](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Criar um novo grupo e adicionando membros](../fundamentals/active-directory-groups-create-azure-portal.md)
- [Gerenciar configurações de um grupo](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Gerenciar associações de um grupo](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Gerenciar regras dinâmicas para usuários em um grupo](groups-create-rule.md)
