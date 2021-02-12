---
title: Atribuir uma política de acesso de Azure Key Vault (Portal)
description: Como usar o portal do Azure para atribuir uma política de acesso de Key Vault a uma entidade de serviço ou a uma identidade de aplicativo.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/27/2020
ms.author: mbaldwin
ms.openlocfilehash: 910b8dae10036cc2e396be13495fd28363dc971d
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97934553"
---
# <a name="assign-a-key-vault-access-policy-using-the-azure-portal"></a>Atribuir uma política de acesso de Key Vault usando o portal do Azure

Uma política de acesso Key Vault determina se uma determinada entidade de serviço, ou seja, um aplicativo ou grupo de usuários, pode executar operações diferentes em Key Vault [segredos](../secrets/index.yml), [chaves](../keys/index.yml)e [certificados](../certificates/index.yml). Você pode atribuir políticas de acesso usando o portal do Azure (este artigo), o [CLI do Azure](assign-access-policy-cli.md)ou [Azure PowerShell](assign-access-policy-powershell.md).

[!INCLUDE [key-vault-access-policy-limits.md](../../../includes/key-vault-access-policy-limits.md)]

Para obter mais informações sobre como criar grupos em Azure Active Directory por meio do portal do Azure, consulte [criar um grupo básico e adicionar membros](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)

## <a name="assign-an-access-policy"></a>Atribuir uma política de acesso

1.  Na [portal do Azure](https://portal.azure.com), navegue até o recurso Key Vault. 

1.  Em **configurações**, selecione **políticas de acesso** e, em seguida, selecione **Adicionar política de acesso**:

    ![Selecionar políticas de acesso, selecionando Adicionar atribuição de função](../media/authentication/assign-policy-portal-01.png)

1.  Selecione as permissões desejadas em **permissões de certificado**, permissões de **chave** e permissões de **segredo**. Você também pode selecionar um modelo que contém combinações de permissões comuns:

    ![Especificando permissões de política de acesso](../media/authentication/assign-policy-portal-02.png)

1. Em **selecionar entidade de segurança**, escolha o link **nenhum selecionado** para abrir o painel seleção **principal** . Insira o nome do aplicativo ou da entidade de serviço no campo de pesquisa, selecione o resultado apropriado e escolha **selecionar**.

    ![Selecionando a entidade de serviço para a política de acesso](../media/authentication/assign-policy-portal-03.png)

    Se você estiver usando uma identidade gerenciada para o aplicativo, procure e selecione o nome do aplicativo em si. (Para obter mais informações sobre identidades gerenciadas e entidades de serviço, consulte [Key Vault autenticação – identidade do aplicativo e entidades de serviço](authentication.md#app-identity-and-security-principals).)
 
1.  De volta ao painel **Adicionar política de acesso** , selecione **Adicionar** para salvar a política de acesso.

    ![Adicionando a política de acesso com a entidade de serviço atribuída](../media/authentication/assign-policy-portal-04.png)

1. De volta à página **políticas de acesso** , verifique se sua política de acesso está listada em políticas de **acesso atuais** e, em seguida, selecione **salvar**. As políticas de acesso não são aplicadas até você salvá-las.

    ![Salvando as alterações da política de acesso](../media/authentication/assign-policy-portal-05.png)


## <a name="next-steps"></a>Próximas etapas

- [Segurança do Azure Key Vault: Gerenciamento de identidades e acesso](security-overview.md#identity-management)
- [Proteja seu cofre de chaves](secure-your-key-vault.md).
- [Guia do desenvolvedor do Azure Key Vault](developers-guide.md)