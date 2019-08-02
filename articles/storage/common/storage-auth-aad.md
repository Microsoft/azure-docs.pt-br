---
title: Autorizar o acesso a blobs do Azure e filas usando o Azure Active Directory | Microsoft Docs
description: Autorize o acesso a blobs do Azure e filas usando o Azure Active Directory.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/21/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 8033dda4059a52cea2b775fc8765a9f2a91b96dd
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67302427"
---
# <a name="authorize-access-to-azure-blobs-and-queues-using-azure-active-directory"></a>Autorizar o acesso a blobs do Azure e filas usando o Azure Active Directory

Armazenamento do Azure suporta usando o Azure Active Directory (AD) para autorizar solicitações de armazenamento de Blob e fila. Com o Azure AD, você pode usar o controle de acesso baseado em função (RBAC) para conceder permissões a uma entidade de segurança, que pode ser um usuário, grupo ou entidade de serviço de aplicativo. A entidade de segurança é autenticada pelo Azure AD para retornar um token OAuth 2.0. O token pode ser usado para autorizar uma solicitação para acessar um recurso no armazenamento de Blob ou fila.

Autorizando usuários ou aplicativos que usam um token OAuth 2.0 retornado pelo AD do Azure fornece segurança superior e facilidade de uso sobre a autorização de chave compartilhada e compartilhado (SAS) de assinaturas de acesso. Com o Azure AD, não é necessário para armazenar a chave de acesso de conta com seu código e os riscos possíveis vulnerabilidades de segurança. Enquanto você pode continuar a usar a autorização de chave compartilhada com seus aplicativos, usando o AD do Azure evita a necessidade de armazenar sua chave de acesso da conta com o seu código. Também é possível continuar a usar assinaturas de acesso compartilhado (SAS) para conceder acesso refinado a recursos em sua conta de armazenamento, mas o Azure AD oferece recursos semelhantes sem a necessidade de gerenciar tokens SAS ou se preocupar sobre revogar uma SAS comprometida. A Microsoft recomenda usar autorização do Azure AD com seus aplicativos de armazenamento do Azure sempre que possível.

Autorização com o Azure AD está disponível para todos os fins gerais e contas de armazenamento em todas as regiões públicas e nuvens nacionais de Blob. Somente contas de armazenamento criado com o suporte ao modelo de implantação do Azure Resource Manager autorização do Azure AD.

## <a name="overview-of-azure-ad-for-blobs-and-queues"></a>Visão geral do AD do Azure para blobs e filas

Quando uma entidade de segurança (um usuário, grupo ou aplicativo) tenta acessar um recurso de blob ou fila, a solicitação deve ser autorizada, a menos que ele é um blob disponível para acesso anônimo. Com o Azure AD, acesso a um recurso é um processo em duas etapas. Primeiro, identidade da entidade de segurança é autenticada e um token OAuth 2.0 é retornado. Em seguida, o token é passado como parte de uma solicitação para o serviço Blob ou fila e usado pelo serviço para autorizar o acesso ao recurso especificado.

A etapa de autenticação requer que um aplicativo solicitar um token de acesso OAuth 2.0 em tempo de execução. Se um aplicativo executado em uma entidade do Azure como uma VM do Azure, um conjunto de dimensionamento de máquina virtual ou um aplicativo de funções do Azure, ele poderá usar um [identidade gerenciada](../../active-directory/managed-identities-azure-resources/overview.md) para acessar blobs ou filas. Para saber como autorizar solicitações feitas por uma identidade gerenciada para o serviço de fila ou BLOBs do Azure, consulte [autorizar o acesso a blobs e filas com o Azure Active Directory e identidades gerenciadas para recursos do Azure](storage-auth-aad-msi.md).

A etapa de autorização requer que uma ou mais funções RBAC sejam atribuídas a entidade de segurança. Armazenamento do Azure fornece funções RBAC que abrangem os conjuntos de permissões para dados de blob e fila comuns. As funções que são atribuídas a uma entidade de segurança determinam as permissões que terão a entidade de segurança. Para saber mais sobre como atribuir funções RBAC para o armazenamento do Azure, consulte [gerenciar direitos de acesso aos dados do armazenamento com RBAC](storage-auth-aad-rbac.md).

Aplicativos nativos e aplicativos da web que fazem solicitações ao serviço de fila ou BLOBs do Azure também podem autorizar o acesso ao Azure AD. Para saber como solicitar um token de acesso e usá-la para autorizar solicitações de dados de blob ou fila, consulte [autorizar o acesso ao armazenamento do Azure com o Azure AD de um aplicativo de armazenamento do Azure](storage-auth-aad-app.md).

## <a name="assigning-rbac-roles-for-access-rights"></a>Atribuindo funções RBAC para direitos de acesso

Azure Active Directory (Azure AD) autoriza os direitos de acesso aos recursos protegidos por meio do [controle de acesso baseado em função (RBAC)](../../role-based-access-control/overview.md). O armazenamento do Azure define um conjunto de funções RBAC internas que abrangem os conjuntos de permissões usados para acessar dados de blob e fila comuns. Você também pode definir funções personalizadas para o acesso aos dados de blob e fila.

Quando uma função RBAC é atribuída a uma entidade de segurança do Azure AD, Azure concede acesso a esses recursos para essa entidade de segurança. O escopo do acesso pode ser definido para o nível de assinatura, o grupo de recursos, a conta de armazenamento ou um contêiner ou fila individual. Uma entidade de segurança do Azure AD pode ser um usuário, grupo, uma entidade de serviço de aplicativo, ou um [identidade de recursos do Azure gerenciado](../../active-directory/managed-identities-azure-resources/overview.md).

### <a name="built-in-rbac-roles-for-blobs-and-queues"></a>Funções RBAC internas para blobs e filas

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

Para saber como atribuir uma função RBAC interna a uma entidade de segurança, consulte um dos seguintes artigos:

- [Conceder acesso a dados do Azure blob e fila com o RBAC no portal do Azure](storage-auth-aad-rbac-portal.md)
- [Conceder acesso a dados de blob e fila do Azure com o RBAC usando a CLI do Azure](storage-auth-aad-rbac-cli.md)
- [Conceder acesso a dados de blob e fila do Azure com o RBAC, usando o PowerShell](storage-auth-aad-rbac-powershell.md)

Para obter mais informações sobre como as funções internas são definidas para o Armazenamento do Microsoft Azure, consulte [Compreender as definições de função](../../role-based-access-control/role-definitions.md#management-and-data-operations). Para obter informações sobre como criar funções de RBAC personalizadas, consulte [criar funções personalizadas para o controle de acesso baseado em função](../../role-based-access-control/custom-roles.md).

### <a name="access-permissions-for-data-operations"></a>Permissões de acesso para operações de dados

Para obter detalhes sobre as permissões necessárias para chamar operações de serviço Blob ou fila específicas, consulte [permissões para chamar o blob e fila de operações de dados](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

## <a name="resource-scope"></a>Escopo do recurso

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="access-data-with-an-azure-ad-account"></a>Acessar dados com uma conta do AD do Azure

Acesso aos dados de blob ou fila por meio do portal do Azure, PowerShell, ou a CLI do Azure pode ser autorizada usando a conta do usuário do Azure AD ou usando as teclas de acesso de conta (autorização de chave compartilhada).

### <a name="data-access-from-the-azure-portal"></a>Acesso a dados do portal do Azure

O portal do Azure pode usar sua conta do AD do Azure ou as chaves de acesso da conta para acessar dados de blob e fila em uma conta de armazenamento do Azure. O esquema de autorização que usa o portal do Azure depende das funções RBAC que são atribuídas a você.

Quando você tenta acessar dados blob ou fila, o portal do Azure verifica primeiro se você atribuiu uma função RBAC com **Microsoft.Storage/storageAccounts/listkeys/action**. Se você tiver sido atribuído a uma função com esta ação, o portal do Azure usa a chave de conta para acessar os dados de blob e fila por meio de autorização de chave compartilhada. Se você não tiver sido atribuído uma função com esta ação, o portal do Azure tenta acessar dados usando sua conta do AD do Azure.

Para acessar o blob ou fila de dados do portal do Azure usando sua conta do Azure AD, você precisa de permissões para acessar os dados de blob e fila, e você também precisa de permissões para navegar por meio dos recursos de conta de armazenamento no portal do Azure. As funções internas fornecidas pelo armazenamento do Azure concedem acesso a recursos de blob e fila, mas eles não conceder permissões aos recursos da conta de armazenamento. Por esse motivo, acessar o portal também requer a atribuição de uma função do Azure Resource Manager, como o [leitor](../../role-based-access-control/built-in-roles.md#reader) função, como escopo o nível da conta de armazenamento ou superior. O **leitor** função concede as permissões mais restritas, mas outra função do Azure Resource Manager que concede acesso a recursos de gerenciamento de conta de armazenamento também é aceitável. Para saber mais sobre como atribuir permissões aos usuários para acesso a dados no portal do Azure com uma conta do AD do Azure, consulte [conceder acesso a dados do Azure blob e fila com o RBAC no portal do Azure](storage-auth-aad-rbac-portal.md).

O portal do Azure indica qual esquema de autorização está em uso quando você navega para uma fila ou um contêiner. Para obter mais informações sobre o acesso a dados no portal, consulte [usar o portal do Azure para acessar os dados blob ou fila](storage-access-blobs-queues-portal.md).

### <a name="data-access-from-powershell-or-azure-cli"></a>Acesso a dados do PowerShell ou CLI do Azure

PowerShell e CLI do Azure dão suporte a entrar com credenciais do Azure AD. Depois de entrar, sua sessão é executado sob essas credenciais. Para obter mais informações, consulte [comandos de execução da CLI do Azure ou o PowerShell com credenciais do Azure AD para acessar dados de blob ou fila](storage-auth-aad-script.md).

## <a name="azure-ad-authorization-over-smb-for-azure-files"></a>Autorização do AD do Azure no SMB para arquivos do Azure

Os arquivos do Azure dá suporte a autorização com o AD do Azure no SMB para ingressado no domínio somente às VMs (visualização). Para saber mais sobre como usar o AD do Azure no SMB para arquivos do Azure, consulte [autorização de visão geral do Active Directory do Azure no SMB para arquivos do Azure (visualização)](../files/storage-files-active-directory-overview.md).

## <a name="next-steps"></a>Próximas etapas

- [Autorizar o acesso a blobs e filas com o Azure Active Directory e identidades gerenciadas para recursos do Azure](storage-auth-aad-msi.md)
- [Autenticar com o Azure Active Directory em um aplicativo para ter acesso a blobs e filas](storage-auth-aad-app.md)
- [Suporte de armazenamento do Azure Active Directory do Azure com base em disponibilidade geral do controle de acesso](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/)
