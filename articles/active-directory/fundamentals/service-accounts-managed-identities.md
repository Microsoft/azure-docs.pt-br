---
title: Protegendo identidades gerenciadas no Azure Active Directory
description: Explicação de como localizar, avaliar e aumentar a segurança de identidades gerenciadas.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 3/1/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 88a7600239d6e960fa2e635c9e7d9049a7c02db3
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102032341"
---
# <a name="securing-managed-identities"></a>Protegendo identidades gerenciadas

Os desenvolvedores costumam ser desafiados pelo gerenciamento de segredos e credenciais usadas para proteger a comunicação entre diferentes serviços. Identidades gerenciadas são identidades de Azure Active Directory seguras (Azure AD) criadas para fornecer identidades para recursos do Azure.

## <a name="benefits-of-using-managed-identities-for-azure-resources"></a>Benefícios do uso de identidades gerenciadas para recursos do Azure

Veja a seguir os benefícios do uso de identidades gerenciadas:

* Você não precisa gerenciar credenciais. Com identidades gerenciadas, as credenciais são totalmente gerenciadas, giradas e protegidas pelo Azure. As identidades são fornecidas e excluídas automaticamente com os recursos do Azure. Identidades gerenciadas permitem que recursos do Azure se comuniquem com todos os serviços que dão suporte à autenticação do Azure AD.

* Ninguém (incluindo qualquer administrador global) tem acesso às credenciais, portanto, elas não podem ser vazadas acidentalmente, por exemplo, sendo incluídas no código.

## <a name="when-to-use-managed-identities"></a>Quando usar identidades gerenciadas?

Identidades gerenciadas são mais bem usadas para comunicações entre serviços que dão suporte à autenticação do Azure AD. 

Um sistema de origem solicita acesso a um serviço de destino. Qualquer recurso do Azure pode ser um sistema de origem. Por exemplo, uma VM do Azure, instância de função do Azure e instâncias de serviços Azure Apps dão suporte a identidades gerenciadas.

[!VIDEO https://www.youtube.com/embed/5lqayO_oeEo]

### <a name="how-authentication-and-authorization-work"></a>Como funciona a autenticação e a autorização

Com identidades gerenciadas, o sistema de origem pode obter um token do Azure AD sem o proprietário da fonte que precisa gerenciar credenciais. O Azure gerencia as credenciais. O token obtido pelo sistema de origem é apresentado ao sistema de destino para autenticação. 

O sistema de destino precisa autenticar (identificar) e autorizar o sistema de origem antes de permitir o acesso. Quando o serviço de destino dá suporte à autenticação baseada no Azure AD, ele aceita um token de acesso emitido pelo Azure AD. 

O Azure tem um plano de controle e um plano de dados. No plano de controle, você cria recursos e, no plano de dados, você os acessa. Por exemplo, você cria um banco de dados Cosmos no plano de controle, mas o consulta no plano Data.

Depois que o sistema de destino aceita o token para autenticação, ele pode dar suporte a diferentes mecanismos de autorização para seu plano de controle e plano de dados.

Todas as operações do plano de controle do Azure são gerenciadas pelo [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/management/overview) e usam o [controle de acesso baseado em função do Azure](https://docs.microsoft.com/azure/role-based-access-control/overview). No plano de dados,, cada sistema de destino tem seu próprio mecanismo de autorização. O armazenamento do Azure dá suporte ao RBAC do Azure no plano de dados. Por exemplo, os aplicativos que usam Azure App serviços podem ler dados do armazenamento do Azure, e os aplicativos que usam o serviço kubernetes do Azure podem ler segredos armazenados em Azure Key Vault.

Para obter mais informações sobre os planos de controle e de dados, consulte [plano de controle e operações de plano de dados-Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/management/control-plane-and-data-plane).

Todos os serviços do Azure eventualmente terão suporte a identidades gerenciadas. Para obter mais informações, consulte [serviços que dão suporte a identidades gerenciadas para recursos do Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities).

##  

## <a name="types-of-managed-identities"></a>Tipos de identidades gerenciadas

Há dois tipos de identidades gerenciadas: atribuído pelo sistema e atribuído pelo usuário.

A identidade gerenciada atribuída pelo sistema tem as seguintes propriedades:

* Eles têm uma relação de 1:1 com o recurso do Azure. Por exemplo, há uma identidade gerenciada exclusiva associada a cada VM.

* Eles estão vinculados ao ciclo de vida dos recursos do Azure. Quando o recurso é excluído, a identidade gerenciada associada a ele é excluída automaticamente, eliminando o risco associado a contas órfãs. 

As identidades gerenciadas atribuídas pelo usuário têm as seguintes propriedades:

* O ciclo de vida dessas identidades é independente de um recurso do Azure e você deve gerenciar o ciclo de vida. Quando o recurso do Azure é excluído, a identidade gerenciada atribuída pelo usuário atribuída não é excluída automaticamente para você.

* Uma única identidade gerenciada atribuída pelo usuário pode ser atribuída a zero ou mais recursos do Azure.

* Eles podem ser criados antes do tempo e, em seguida, atribuídos a um recurso.

## <a name="find-managed-identity-service-principals-in-azure-ad"></a>Localizar entidades de serviço de identidade gerenciadas no Azure AD

Há várias maneiras pelas quais você pode encontrar identidades gerenciadas:

* Usando a página aplicativos empresariais no portal do Azure

* Usando Microsoft Graph

### <a name="using-the-azure-portal"></a>Usando o portal do Azure

1. No Azure AD, selecione aplicativo empresarial.

2. Selecione o filtro para "identidades gerenciadas" 

   ![Imagem da tela todos os aplicativos com o menu suspenso tipo de aplicativo realçando "identidades gerenciadas".](./media/securing-service-accounts/service-accounts-managed-identities.png)

 

### <a name="using-microsoft-graph"></a>Usando Microsoft Graph

Você pode obter uma lista de todas as identidades gerenciadas em seu locatário com a seguinte solicitação GET para Microsoft Graph:

`https://graph.microsoft.com/v1.0/servicePrincipals?$filter=(servicePrincipalType eq 'ManagedIdentity') `

Você pode filtrar essas solicitações. Para obter mais informações, consulte a documentação do grafo para [obter o servicePrincipalName](/graph/api/serviceprincipal-get?view=).

## <a name="assess-the-security-of-managed-identities"></a>Avaliar a segurança de identidades gerenciadas 

Você pode avaliar a segurança de identidades gerenciadas das seguintes maneiras:

* Examine os privilégios e verifique se o modelo com privilégios mínimos está selecionado. Use o seguinte cmdlet do PowerShell para obter as permissões atribuídas às suas identidades gerenciadas.

   ` Get-AzureADServicePrincipal | % { Get-AzureADServiceAppRoleAssignment -ObjectId $_ }`

 
* Verifique se a identidade gerenciada não faz parte de grupos privilegiados, como um grupo de administradores.  
Você pode fazer isso enumerando os membros de seus grupos altamente privilegiados com o PowerShell.

   `Get-AzureADGroupMember -ObjectId <String> [-All <Boolean>] [-Top <Int32>] [<CommonParameters>]`

* [Verifique se você sabe quais recursos a identidade gerenciada está acessando](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-list-powershell).

## <a name="move-to-managed-identities"></a>Mover para identidades gerenciadas

Se você estiver usando uma entidade de serviço ou uma conta de usuário do Azure AD, avalie se você pode usar um gerenciado para eliminar a necessidade de proteger, girar e gerenciar credenciais. 

## <a name="next-steps"></a>Próximas etapas

**Para obter informações sobre como criar identidades gerenciadas, consulte:** 

[Crie uma identidade gerenciada atribuída pelo usuário](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal). 

[Habilitar uma identidade gerenciada atribuída pelo sistema durante a criação do recurso](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)

[Habilitar a identidade gerenciada atribuída pelo sistema em um recurso existente](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)

**Para obter mais informações sobre contas de serviço, consulte:**

[Introdução às contas de serviço Azure Active Directory](service-accounts-introduction-azure.md)

[Protegendo entidades de serviço](service-accounts-principal.md)

[Governando contas de serviço do Azure](service-accounts-governing-azure.md)

[Introdução a contas de serviço locais](service-accounts-on-premises.md)

 

 

 
