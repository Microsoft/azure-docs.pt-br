---
title: "Sincronização de identidades e resiliência do atributo duplicado | Microsoft Docs"
description: "Novo comportamento de como lidar com objetos com conflitos de UPN ou ProxyAddress durante a sincronização de diretórios usando o Azure AD Connect."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 537a92b7-7a84-4c89-88b0-9bce0eacd931
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: markvi
ms.openlocfilehash: 7a8700e70f64851a0c5e5e8c6b31ec7a6884a96c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="identity-synchronization-and-duplicate-attribute-resiliency"></a>Sincronização de identidades e resiliência do atributo duplicado 
A Resiliência do Atributo Duplicado é um recurso do Azure Active Directory que eliminará o atrito causado por conflitos de **UserPrincipalName** e **ProxyAddress** ao executar uma das ferramentas de sincronização da Microsoft.

Esses dois atributos geralmente precisam ser exclusivos em todos os objetos **User**, **Group** ou **Contact** em um determinado locatário do Azure Active Directory.

> [!NOTE]
> Somente os Usuários podem ter UPNs.
> 
> 

O novo comportamento permite que esse recurso esteja na parte de nuvem do pipeline de sincronização, portanto, é cliente independente e relevante para qualquer produto de sincronização da Microsoft, incluindo o Azure AD Connect, o DirSync e o MIM + Conector. O termo genérico "cliente de sincronização" é usado neste documento para representar qualquer um desses produtos.

## <a name="current-behavior"></a>Comportamento atual
Se houver uma tentativa de provisionar um novo objeto com um valor ProxyAddress ou UPN que viola a restrição de exclusividade, o Azure Active Directory impedirá que esse objeto seja criado. Da mesma forma, se um objeto for atualizado com um UPN ou ProxyAddress não exclusivo, a atualização falhará. A tentativa de provisionamento ou a atualização é repetida pelo cliente de sincronização em cada ciclo de exportação e continua a falhar até que o conflito seja resolvido. Um email de relatório de erro é gerado após cada tentativa e o erro é registrado pelo cliente de sincronização.

## <a name="behavior-with-duplicate-attribute-resiliency"></a>Comportamento com Resiliência do Atributo Duplicado
Em vez de falhar completamente em provisionar ou atualizar um objeto com um atributo duplicado, o Azure Active Directory "coloca em quarentena" o atributo duplicado que viola a restrição de exclusividade. Se esse atributo for necessário para o provisionamento, como um UserPrincipalName, o serviço atribuirá um valor de espaço reservado. O formato desses valores temporários é  
“***<OriginalPrefix>+<4DigitNumber>@<InitialTenantDomain>.onmicrosoft.com***”.  
Se o atributo não for necessário, como um **ProxyAddress**, o Azure Active Directory simplesmente colocará em quarentena o atributo em conflito e prosseguirá com a criação ou atualização do objeto.

Ao colocar em quarentena o atributo, as informações sobre o conflito são enviadas no mesmo email de relatório de erro usado no antigo comportamento. No entanto, essas informações só aparecem no relatório de erro uma vez, quando ocorre a quarentena; elas não continuam a ser registradas em log em emails futuros. Além disso, uma vez que a exportação deste objeto foi bem-sucedida, o cliente de sincronização não registra em log um erro nem tenta repetir a operação para criar/atualizar nos ciclos de sincronização subsequentes.

Para dar suporte a esse comportamento, foi adicionado um novo atributo às classes de objeto User, Group e Contact:   
**DirSyncProvisioningErrors**

Este é um atributo com valores múltiplos usado para armazenar os atributos conflitantes que violariam a restrição de exclusividade, caso adicionados normalmente. Uma tarefa de temporizador em segundo plano foi habilitada no Azure Active Directory, que é executada a cada hora para procurar por conflitos de atributo duplicado que foram resolvidos e remove automaticamente os atributos em questão da quarentena.

### <a name="enabling-duplicate-attribute-resiliency"></a>Habilitando a Resiliência do Atributo Duplicado
Resiliência do Atributo Duplicado será o novo comportamento padrão em todos os locatários do Azure Active Directory. Ele estará ativado por padrão em todos os locatários que habilitaram a sincronização pela primeira vez em 22 de agosto de 2016 ou posteriormente. Os locatários que habilitaram a sincronização antes dessa data terão o recurso habilitado em lotes. Essa distribuição começará em setembro de 2016, e uma notificação por email será enviada para o contato de notificação técnica de cada locatário com a data específica de habilitação do recurso.

> [!NOTE]
> Depois que a opção Resiliência do Atributo Duplicado for habilitada, ela não poderá ser desabilitada.

Para verificar se o recurso está habilitado para o seu locatário, você poderá baixar a versão mais recente do módulo do PowerShell do Azure Active Directory e executar:

`Get-MsolDirSyncFeatures -Feature DuplicateUPNResiliency`

`Get-MsolDirSyncFeatures -Feature DuplicateProxyAddressResiliency`

> [!NOTE]
> Você não pode mais usar o cmdlet Set-MsolDirSyncFeature para habilitar proativamente o recurso de Duplicar Resiliência de Atributo antes que ele seja ativado para o seu locatário. Para ser capaz de testar o recurso, você precisará criar um novo locatário do Azure Active Directory.

## <a name="identifying-objects-with-dirsyncprovisioningerrors"></a>Identificação de objetos com DirSyncProvisioningErrors
Atualmente, existem dois métodos para identificar os objetos que têm esses erros devido a conflitos de propriedade duplicada: o Azure Active Directory PowerShell e o Portal de Administração do Office 365. No futuro, há planos de estender para relatórios adicionais baseados no portal.

### <a name="azure-active-directory-powershell"></a>Azure Active Directory PowerShell
Para os cmdlets do PowerShell neste tópico, o seguinte é verdadeiro:

* Todos os cmdlets a seguir diferenciam maiúsculas de minúsculas.
* O **–ErrorCategory PropertyConflict** sempre deve ser incluído. Atualmente, há outros tipos de **ErrorCategory**, mas isso pode ser estendido no futuro.

Primeiro, comece executando **Connect-MsolService** e inserindo as credenciais para um administrador de locatário.

Em seguida, use os cmdlets e os operadores a seguir para exibir os erros de maneiras diferentes:

1. [Ver tudo](#see-all)
2. [Por Tipo de Propriedade](#by-property-type)
3. [Por Valor Conflitante](#by-conflicting-value)
4. [Usando uma Pesquisa da Cadeia de Caracteres](#using-a-string-search)
5. [Classificado](#sorted)
6. [Em uma Quantidade Limitada ou Todos](#in-a-limited-quantity-or-all)

#### <a name="see-all"></a>Ver tudo
Uma vez conectado, para ver uma lista geral dos erros de provisionamento do atributo no locatário, execute:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict`

Isso produz um resultado semelhante ao seguinte:   
 ![Get-MsolDirSyncProvisioningError](./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/1.png "Get-MsolDirSyncProvisioningError")  

#### <a name="by-property-type"></a>Por Tipo de Propriedade
Para ver os erros por tipo de propriedade, adicione o sinalizador **-PropertyName** com o argumento **UserPrincipalName** ou **ProxyAddresses**:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName UserPrincipalName`

Ou

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName ProxyAddresses`

#### <a name="by-conflicting-value"></a>Por Valor Conflitante
Para ver os erros relacionados a uma propriedade específica, adicione o sinalizador **-PropertyValue** (**-PropertyName** também deve ser usado ao adicionar esse sinalizador):

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyValue User@domain.com -PropertyName UserPrincipalName`

#### <a name="using-a-string-search"></a>Usando uma Pesquisa da Cadeia de Caracteres
Para fazer uma pesquisa ampla da cadeia de caracteres, use o sinalizador **-SearchString** . Isso pode ser usado independentemente de todos os sinalizadores acima, com exceção de **-ErrorCategory PropertyConflict**, que é sempre necessário:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -SearchString User`

#### <a name="in-a-limited-quantity-or-all"></a>Em uma Quantidade Limitada ou Todos
1. **MaxResults<Int>** pode ser usado para limitar a consulta a um número específico de valores.
2. **All** pode ser usado para garantir que todos os resultados sejam recuperados, no caso de existir um grande número de erros.

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -MaxResults 5`

## <a name="office-365-admin-portal"></a>Portal de administração do Office 365
Você pode exibir os erros de sincronização de diretórios no centro de administração do Office 365. O relatório no portal do Office 365 exibe apenas os objetos **User** que têm esses erros. Ele não mostra informações sobre conflitos entre **Groups** e **Contacts**.

![Usuários ativos](./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/1234.png "Usuários ativos")

Para obter instruções sobre como exibir erros de sincronização de diretórios no centro de administração do Office 365, confira [Identificar erros de sincronização de diretório no Office 365](https://support.office.com/en-us/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067).

### <a name="identity-synchronization-error-report"></a>Relatório de erros de sincronização de identidades
Quando um objeto com um conflito de atributo duplicado é tratado com esse novo comportamento, uma notificação é incluída no email padrão do Relatório de Erros de Sincronização de Identidades enviado para o contato de Notificação Técnica do locatário. No entanto, há uma alteração importante nesse comportamento. No passado, as informações sobre um conflito de atributo duplicado eram incluídas em todos os relatórios de erro subsequentes até o conflito ser resolvido. Com esse novo comportamento, a notificação de erro para determinado conflito só aparece uma vez, no momento em que o atributo conflitante está de quarentena.

Este é exemplo da aparência da notificação por email de um conflito ProxyAddress:   
    ![Usuários ativos](./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/6.png "Usuários ativos")  

## <a name="resolving-conflicts"></a>Resolução de conflitos
A estratégia da solução de problemas e as táticas de resolução desses erros não devem diferir da maneira como os erros de atributo duplicado eram tratados no passado. A única diferença é que a tarefa de temporizador examina o locatário no lado do serviço para adicionar automaticamente o atributo em questão ao devido objeto, assim que o conflito é resolvido.

O seguinte artigo descreve diversas estratégias de solução de problemas: [Atributos inválidos ou duplicados impedem a sincronização de diretórios no Office 365](https://support.microsoft.com/kb/2647098).

## <a name="known-issues"></a>Problemas conhecidos
Nenhum desses problemas conhecidos causa degradação do serviço nem a perda de dados. Muitos são estéticos, outros fazem com que os erros de atributo duplicado de "*pré-resiliência*" padrão sejam gerados, em vez de colocarem em quarentena o atributo em conflito e também fazem com que certos erros exijam uma correção manual extra.

**Comportamento básico:**

1. Os objetos com configurações de atributo específicas continuam a receber erros de exportação em vez de colocar os atributos duplicados em quarentena.  
   Por exemplo:
   
    a. Um novo usuário é criado no AD com o UPN **Joe@contoso.com** e o ProxyAddress **smtp:Joe@contoso.com**
   
    b. Propriedades desse conflito de objetos com um Group existente, em que o ProxyAddress é **SMTP:Joe@contoso.com**.
   
    c. Ao exportar, um erro de **conflito ProxyAddress** é gerado, em vez de colocar os atributos em conflito de quarentena. A operação é repetida em cada ciclo de sincronização subsequente, como era antes do recurso de resiliência ser habilitado.
2. Se dois Groups forem criados localmente com o mesmo endereço SMTP, um falhará ao provisionar na primeira tentativa com um erro de **ProxyAddress** duplicado padrão. No entanto, o valor duplicado é devidamente colocado em quarentena após o próximo ciclo de sincronização.

**Relatório do Portal do Office**:

1. A mensagem de erro detalhada para dois objetos em um conjunto de conflitos UPN é a mesma. Isso indica que ambos tiveram seus UPNS alterados/colocados em quarentena, quando, na verdade, apenas um deles teve os dados alterados.
2. A mensagem de erro detalhada de um conflito UPN mostra o displayName incorreto para um usuário que teve seu UPN alterado/colocado em quarentena. Por exemplo:
   
    a. O **Usuário A** é sincronizado primeiro com **UPN = User@contoso.com**.
   
    b. O **Usuário B** tenta ser sincronizado em seguida com **UPN = User@contoso.com**.
   
    c. O UPN do **Usuário B** é alterado para **User1234@contoso.onmicrosoft.com** e **User@contoso.com** é adicionado a **DirSyncProvisioningErrors**.
   
    d. A mensagem de erro para o **Usuário B** deve indicar que o **Usuário A** já tem **User@contoso.com** como um UPN, mas mostra o próprio displayName do **Usuário B**.

**Relatório de erros de sincronização de identidades**:

O link para as *etapas sobre como resolver esse problema* está incorreto:  
    ![Usuários ativos](./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/6.png "Usuários ativos")  

Ele deve apontar para [https://aka.ms/duplicateattributeresiliency](https://aka.ms/duplicateattributeresiliency).

## <a name="see-also"></a>Consulte também
* [Sincronização do Azure AD Connect](active-directory-aadconnectsync-whatis.md)
* [Integração de suas identidades locais com o Active Directory do Azure](active-directory-aadconnect.md)
* [Identificar erros de sincronização de diretório no Office 365](https://support.office.com/en-us/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067)

