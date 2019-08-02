---
title: Vincular uma conta do Azure à ID de parceiro | Microsoft Docs
description: Acompanhar os contratos com clientes do Azure por meio da vinculação de ID de parceiro à conta de usuário que você usa para gerenciar funcionalidades do cliente.
services: billing
author: dhirajgandhi
manager: dhgandhi
ms.author: banders
ms.date: 03/12/2019
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 97866a1f1299c028cdc8f86245308ae4a8b5db88
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67502686"
---
# <a name="link-a-partner-id-to-your-azure-accounts"></a>Vincular ID de parceiro a suas contas do Azure

Parceiros da Microsoft fornecem serviços que ajudam os clientes atingir os objetivos de negócios e missão usando produtos da Microsoft. Ao agir em nome do cliente de gerenciamento, configuração e suporte a serviços do Azure, os usuários do parceiro precisará ter acesso ao ambiente do cliente. Usando o Link de administração de parceiro, parceiros podem associar sua ID de rede do parceiro com as credenciais usadas para fornecimento de serviços.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="get-access-from-your-customer"></a>Obter o acesso do cliente

Antes de vincular sua ID de parceiro, o cliente deve oferecer acesso a seus recursos do Azure, usando uma das seguintes opções:

- **Usuário convidado**: Seu cliente pode adicioná-lo como um usuário convidado e atribuir funções de controle de acesso baseado em função (RBAC). Para obter mais informações, consulte [Adicionar usuários convidados de outro diretório](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).

- **Conta de diretório**: Seu cliente pode criar uma conta de usuário para você no próprio diretório dele e atribuir qualquer função RBAC.

- **Entidade de serviço**: Seu cliente pode adicionar um aplicativo ou script da sua organização no diretório dele e atribuir qualquer função RBAC. A identidade do aplicativo ou script é conhecida como entidade de serviço.

## <a name="link-to-a-partner-id"></a>Vincular à uma ID de parceiro

Quando você tem acesso aos recursos do cliente, use o portal do Azure, o PowerShell ou a CLI do Azure para vincular sua ID da Microsoft Partner Network (ID da MPN) à sua ID de usuário ou entidade de serviço. Vincule a ID de parceiro em cada locatário do cliente.

### <a name="use-the-azure-portal-to-link-to-a-new-partner-id"></a>Use o portal do Azure para vincular a uma nova ID de parceiro

1. Vá para [Vincular a uma ID de parceiro](https://portal.azure.com/#blade/Microsoft_Azure_Billing/managementpartnerblade) no portal do Azure.

2. Entre no Portal do Azure.

3. Insira a ID de parceiro da Microsoft. A ID do parceiro é a ID do [Microsoft Partner Network](https://partner.microsoft.com/) da sua organização.

   ![Captura de tela que mostra o vínculo a uma ID de parceiro](./media/billing-link-partner-id/link-partner-ID.PNG)

4. Para vincular a ID de parceiro a outro cliente, alterne o diretório. Em **Mudar diretório**, selecione o seu diretório.

   ![Captura de tela que mostra Mudar diretório](./media/billing-link-partner-id/directory-switcher.png)

### <a name="use-powershell-to-link-to-a-new-partner-id"></a>Use o PowerShell para vincular a uma nova ID de parceiro

1. Instalar o módulo [AzureRM.ManagementPartner](https://www.powershellgallery.com/packages/AzureRM.ManagementPartner) do PowerShell.

2. Entre no locatário do cliente com a conta de usuário ou a entidade de serviço. Para obter mais informações, veja [Entrar com o PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps).

   ```azurepowershell-interactive
    C:\> Connect-AzAccount -TenantId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
   ```

3. Vincular a nova ID de parceiro. A ID do parceiro é a ID do [Microsoft Partner Network](https://partner.microsoft.com/) da sua organização.

    ```azurepowershell-interactive
    C:\> new-AzManagementPartner -PartnerId 12345
    ```

#### <a name="get-the-linked-partner-id"></a>Obter a ID de parceiro vinculada
```azurepowershell-interactive
C:\> get-AzManagementPartner
```

#### <a name="update-the-linked-partner-id"></a>Atualizar a ID de parceiro vinculada
```azurepowershell-interactive
C:\> Update-AzManagementPartner -PartnerId 12345
```
#### <a name="delete-the-linked-partner-id"></a>Excluir a ID de parceiro vinculada
```azurepowershell-interactive
C:\> remove-AzManagementPartner -PartnerId 12345
```

### <a name="use-the-azure-cli-to-link-to-a-new-partner-id"></a>Use a CLI do Azure para vincular a uma nova ID de parceiro
1. Instale a extensão de CLI do Azure.

    ```azurecli-interactive
    C:\ az extension add --name managementpartner
    ```

2. Entre no locatário do cliente com a conta de usuário ou a entidade de serviço. Para obter mais informações, veja [Entrar com a CLI do Azure](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

    ```azurecli-interactive
    C:\ az login --tenant <tenant>
    ```

3. Vincular a nova ID de parceiro. A ID do parceiro é a ID do [Microsoft Partner Network](https://partner.microsoft.com/) da sua organização.

     ```azurecli-interactive
     C:\ az managementpartner create --partner-id 12345
      ```  

#### <a name="get-the-linked-partner-id"></a>Obter a ID de parceiro vinculada
```azurecli-interactive
C:\ az managementpartner show
```

#### <a name="update-the-linked-partner-id"></a>Atualizar a ID de parceiro vinculada
```azurecli-interactive
C:\ az managementpartner update --partner-id 12345
```

#### <a name="delete-the-linked-partner-id"></a>Excluir a ID de parceiro vinculada
```azurecli-interactive
C:\ az managementpartner delete --partner-id 12345
```

## <a name="next-steps"></a>Próximas etapas

Participe da discussão da [Comunidade de Parceiros da Microsoft](https://aka.ms/PALdiscussion) para receber atualizações ou enviar comentários.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

**Quem pode vincular a ID de parceiro?**

Qualquer usuário da organização do parceiro que gerencia os recursos do cliente pode vincular a ID de parceiro à conta.

**Uma ID de parceiro pode ser alterada depois que estiver vinculada?**

Sim. Uma ID de parceiro vinculada pode ser alterada, adicionada ou removida.

**E se um usuário tiver uma conta em vários locatários do cliente?**

A vinculação entre a ID de parceiro e a conta é feita para cada locatário do cliente. Vincule a ID de parceiro em cada locatário do cliente.

**Outros parceiros ou clientes podem editar ou remover a vinculação à ID de parceiro?**

A vinculação está associada no nível da conta de usuário. Só você pode editar ou remover a vinculação à ID de parceiro. O cliente e outros parceiros não podem alterar a vinculação à ID de parceiro.


**Qual ID MPN devo usar se a minha empresa possui vários?**

Contas de local de parceiro e associado identificações do MPN devem ser usadas para vincular a ID de parceiro.  Saiba mais sobre [contas de parceiros](https://docs.microsoft.com/partner-center/account-structure)

**Onde posso encontrar relatórios para a ID de parceiro vinculada de receita influenciou?**

Relatório de desempenho do produto com a nuvem está disponível para parceiros no Partner Center em [painel de informações de Meus](https://partner.microsoft.com/membership/reports/myinsights). Você precisa selecionar o Link de administração de parceiro como o tipo de associação de parceiro.

**Por que não consigo ver meu cliente nos relatórios?**

Não é possível ver o cliente nos relatórios seguintes motivos

1. A conta de usuário vinculado não tem [acesso baseado em função](https://docs.microsoft.com/azure/role-based-access-control/overview) em todos os recursos ou assinatura do Azure do cliente.

2. A assinatura do Azure no qual o usuário tem [acesso baseado em função](https://docs.microsoft.com/azure/role-based-access-control/overview) access não terá qualquer uso.

**Vincular a parceiro que ID funciona com o Azure Stack?**

Sim, você pode vincular sua ID de parceiro para o Azure Stack.
