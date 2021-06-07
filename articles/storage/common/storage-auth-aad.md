---
title: Autorizar o acesso a BLOBs e filas usando Active Directory
titleSuffix: Azure Storage
description: Autorize o acesso a BLOBs e filas do Azure usando o Azure Active Directory (Azure AD). Atribua funções do Azure para direitos de acesso. Acesse dados com uma conta do Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 9d03496634c5d30d30b23a76b5b47b1e810af288
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94635391"
---
# <a name="authorize-access-to-blobs-and-queues-using-azure-active-directory"></a>Autorizar o acesso a BLOBs e filas usando Azure Active Directory

O armazenamento do Azure dá suporte ao uso do Azure Active Directory (AD do Azure) para autorizar solicitações de armazenamento de BLOBs e filas. Com o Azure AD, você pode usar o Azure RBAC (controle de acesso baseado em função) para conceder permissões a uma entidade de segurança, que pode ser um usuário, grupo ou entidade de serviço de aplicativo. A entidade de segurança é autenticada pelo AD do Azure para retornar um token 2,0 do OAuth. O token pode ser usado para autorizar uma solicitação no armazenamento de BLOB ou de fila.

A autorização de solicitações no armazenamento do Azure com o Azure AD fornece segurança superior e facilidade de uso sobre a autenticação de chave compartilhada. A Microsoft recomenda usar a autorização do Azure AD com seus aplicativos de BLOB e fila quando possível para minimizar possíveis vulnerabilidades de segurança inerentes à chave compartilhada.

A autorização com o Azure AD está disponível para todas as contas de armazenamento de BLOBs e de uso geral em todas as regiões públicas e nuvens nacionais. Somente contas de armazenamento criadas com o modelo de implantação Azure Resource Manager dão suporte à autorização do Azure AD.

O armazenamento de BLOBs também dá suporte à criação de assinaturas de acesso compartilhado (SAS) que são assinadas com as credenciais do Azure AD. Para obter mais informações, consulte [conceder acesso limitado a dados com assinaturas de acesso compartilhado](storage-sas-overview.md).

Os arquivos do Azure dão suporte à autorização com AD (visualização) ou AD DS do Azure (GA) sobre SMB apenas para VMs ingressadas no domínio. Para saber mais sobre como usar o AD (versão prévia) ou o Azure AD DS (GA) sobre o SMB para arquivos do Azure, confira [visão geral do suporte à autenticação baseada em identidade de arquivos do Azure para acesso SMB](../files/storage-files-active-directory-overview.md).

Não há suporte para a autorização com o Azure AD no armazenamento de tabelas do Azure. Use a chave compartilhada para autorizar solicitações ao armazenamento de tabelas.

## <a name="overview-of-azure-ad-for-blobs-and-queues"></a>Visão geral do Azure AD para BLOBs e filas

Quando uma entidade de segurança (um usuário, grupo ou aplicativo) tenta acessar um recurso de BLOB ou fila, a solicitação deve ser autorizada, a menos que seja um blob disponível para acesso anônimo. Com o Azure AD, o acesso a um recurso é um processo de duas etapas. Primeiro, a identidade da entidade de segurança é autenticada e um token OAuth 2,0 é retornado. Em seguida, o token é passado como parte de uma solicitação para o BLOB ou serviço Fila e usado pelo serviço para autorizar o acesso ao recurso especificado.

A etapa de autenticação requer que um aplicativo solicite um token de acesso OAuth 2,0 em tempo de execução. Se um aplicativo estiver sendo executado de dentro de uma entidade do Azure, como uma VM do Azure, um conjunto de dimensionamento de máquinas virtuais ou um aplicativo Azure Functions, ele poderá usar uma [identidade gerenciada](../../active-directory/managed-identities-azure-resources/overview.md) para acessar BLOBs ou filas. Para saber como autorizar solicitações feitas por uma identidade gerenciada para o blob do Azure ou serviço Fila, consulte [autorizar o acesso a BLOBs e filas com Azure Active Directory e identidades gerenciadas para recursos do Azure](storage-auth-aad-msi.md).

A etapa de autorização requer que uma ou mais funções do Azure sejam atribuídas à entidade de segurança. O armazenamento do Azure fornece funções do Azure que abrangem conjuntos comuns de permissões para dados de BLOB e fila. As funções atribuídas a uma entidade de segurança determinam as permissões que o principal terá. Para saber mais sobre como atribuir funções do Azure para o armazenamento do Azure, consulte [gerenciar direitos de acesso aos dados de armazenamento com o Azure RBAC](./storage-auth-aad-rbac-portal.md).

Aplicativos nativos e aplicativos Web que fazem solicitações para o blob do Azure ou serviço Fila também podem autorizar o acesso com o Azure AD. Para saber como solicitar um token de acesso e usá-lo para autorizar solicitações de BLOB ou dados de fila, consulte [autorizar o acesso ao armazenamento do Azure com o Azure AD de um aplicativo de armazenamento do Azure](storage-auth-aad-app.md).

## <a name="assign-azure-roles-for-access-rights"></a>Atribuir funções do Azure para direitos de acesso

O Azure Active Directory (AD do Azure) autoriza os direitos de acesso a recursos protegidos por meio do [controle de acesso baseado em função do Azure (RBAC do Azure)](../../role-based-access-control/overview.md). O armazenamento do Azure define um conjunto de funções internas do Azure que abrangem conjuntos comuns de permissões usadas para acessar dados de BLOB e de fila. Você também pode definir funções personalizadas para acesso a dados de BLOB e de fila.

Quando uma função do Azure é atribuída a uma entidade de segurança do Azure AD, o Azure concede acesso a esses recursos para essa entidade de segurança. O escopo do acesso pode ser definido para o nível de assinatura, o grupo de recursos, a conta de armazenamento ou um contêiner ou fila individual. Uma entidade de segurança do Azure AD pode ser um usuário, um grupo, uma entidade de serviço de aplicativo ou uma [identidade gerenciada para recursos do Azure](../../active-directory/managed-identities-azure-resources/overview.md).

### <a name="azure-built-in-roles-for-blobs-and-queues"></a>Funções internas do Azure para BLOBs e filas

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

Para saber como atribuir uma função interna do Azure a uma entidade de segurança, consulte um dos seguintes artigos:

- [Usar o portal do Azure para atribuir uma função do Azure para acesso aos dados de blob e de fila](storage-auth-aad-rbac-portal.md)
- [Use o CLI do Azure para atribuir uma função do Azure para acesso aos dados de BLOB e de fila](storage-auth-aad-rbac-cli.md)
- [Usar o módulo Azure PowerShell para atribuir uma função do Azure para acesso aos dados de BLOB e de fila](storage-auth-aad-rbac-powershell.md)

Para obter mais informações sobre como as funções internas são definidas para o Armazenamento do Microsoft Azure, consulte [Compreender as definições de função](../../role-based-access-control/role-definitions.md#management-and-data-operations). Para obter informações sobre como criar funções personalizadas do Azure, consulte [funções personalizadas do Azure](../../role-based-access-control/custom-roles.md).

### <a name="access-permissions-for-data-operations"></a>Permissões de acesso para operações de dados

Para obter detalhes sobre as permissões necessárias para chamar operações de BLOB ou serviço Fila específicas, consulte [permissões para chamar operações de BLOB e de dados de fila](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

## <a name="resource-scope"></a>Escopo do recurso

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="access-data-with-an-azure-ad-account"></a>Acessar dados com uma conta do Azure AD

O acesso a dados de BLOB ou de fila por meio do portal do Azure, PowerShell ou CLI do Azure pode ser autorizado usando a conta do Azure AD do usuário ou usando as chaves de acesso da conta (autorização de chave compartilhada).

### <a name="data-access-from-the-azure-portal"></a>Acesso a dados do portal do Azure

O portal do Azure pode usar sua conta do Azure AD ou as chaves de acesso da conta para acessar dados de BLOB e de fila em uma conta de armazenamento do Azure. O esquema de autorização que o portal do Azure usa depende das funções do Azure atribuídas a você.

Quando você tenta acessar dados de BLOB ou de fila, a portal do Azure primeiro verifica se você recebeu uma função do Azure com **Microsoft. Storage/storageAccounts/listkeys/Action**. Se você tiver recebido uma função com essa ação, o portal do Azure usará a chave de conta para acessar dados de BLOB e de fila por meio da autorização de chave compartilhada. Se você não tiver recebido uma função com essa ação, a portal do Azure tentará acessar os dados usando sua conta do Azure AD.

Para acessar dados de BLOB ou de fila do portal do Azure usando sua conta do Azure AD, você precisa de permissões para acessar dados de BLOB e de fila e também precisa de permissões para navegar pelos recursos da conta de armazenamento no portal do Azure. As funções internas fornecidas pelo Armazenamento do Microsoft Azure concedem acesso aos recursos de blob e fila, mas não concedem permissões aos recursos da conta de armazenamento. Por esse motivo, o acesso ao portal também requer a atribuição de uma função do Azure Resource Manager, como a função [Leitor](../../role-based-access-control/built-in-roles.md#reader) no escopo do nível da conta de armazenamento ou superior. A função **leitor** concede as permissões mais restritas, mas outra função de Azure Resource Manager que concede acesso aos recursos de gerenciamento da conta de armazenamento também é aceitável. Para saber mais sobre como atribuir permissões a usuários para acesso a dados no portal do Azure com uma conta do Azure AD, consulte [usar o portal do Azure para atribuir uma função do Azure para acesso a dados de BLOB e de fila](storage-auth-aad-rbac-portal.md).

O portal do Azure indica qual esquema de autorização está em uso quando você navega para um contêiner ou uma fila. Para obter mais informações sobre o acesso a dados no portal, consulte [escolher como autorizar o acesso a dados de blob no portal do Azure](../blobs/authorize-data-operations-portal.md) e [escolher como autorizar o acesso aos dados da fila no portal do Azure](../queues/authorize-data-operations-portal.md).

### <a name="data-access-from-powershell-or-azure-cli"></a>Acesso a dados do PowerShell ou CLI do Azure

O CLI do Azure e o PowerShell dão suporte à entrada com as credenciais do Azure AD. Depois de entrar, sua sessão é executada sob essas credenciais. Para saber mais, confira [executar comandos do CLI do Azure ou do PowerShell com as credenciais do Azure ad para acessar dados de BLOB ou fila](../blobs/authorize-data-operations-powershell.md).

## <a name="next-steps"></a>Próximas etapas

- [Autorizar o acesso a BLOBs e filas com Azure Active Directory e identidades gerenciadas para recursos do Azure](storage-auth-aad-msi.md)
- [Autorizar com Azure Active Directory de um aplicativo para acesso a BLOBs e filas](storage-auth-aad-app.md)
- [Suporte do armazenamento do Azure para Azure Active Directory controle de acesso baseado em disponibilidade geral](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/)