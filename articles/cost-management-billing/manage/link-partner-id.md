---
title: Vincular uma conta do Azure à ID de parceiro
description: Acompanhar os contratos com clientes do Azure por meio da vinculação de ID de parceiro à conta de usuário que você usa para gerenciar funcionalidades do cliente.
author: dhirajgandhi
ms.reviewer: dhgandhi
ms.author: banders
ms.date: 10/05/2020
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.openlocfilehash: a214e91307308e191ce92b6461c1454d2cc7dd2b
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100370471"
---
# <a name="link-a-partner-id-to-your-azure-accounts"></a>Vincular ID de parceiro a suas contas do Azure

Os parceiros da Microsoft oferecem serviços que ajudam os clientes a alcançar objetivos de negócios e de missão usando produtos da Microsoft. Ao agir em nome do cliente que gerencia, configura e dá suporte aos serviços do Azure, os usuários do parceiro precisarão de acesso ao ambiente do cliente. Usando o PAL (Link do Partner Admin), os parceiros podem associar sua ID de rede de parceiro às credenciais usadas para entrega do serviço.

O PAL permite que a Microsoft identifique e reconheça os parceiros que impulsionam o sucesso dos clientes do Azure. A Microsoft pode atribuir a influência e a receita consumida do Azure à sua organização com base nas permissões da conta (função do Azure) e no escopo (assinatura, grupo de recursos, recursos).

## <a name="get-access-from-your-customer"></a>Obter o acesso do cliente

Antes de vincular sua ID de parceiro, o cliente deve oferecer acesso a seus recursos do Azure, usando uma das seguintes opções:

- **Usuário convidado**: Seu cliente pode adicionar você como usuário convidado e atribuir qualquer função do Azure. Para obter mais informações, consulte [Adicionar usuários convidados de outro diretório](../../active-directory/external-identities/what-is-b2b.md).

- **Conta de diretório**: Seu cliente pode criar uma conta de usuário para você no próprio diretório dele e atribuir qualquer função do Azure.

- **Entidade de serviço**: Seu cliente pode adicionar um aplicativo ou script da organização no diretório dele e atribuir qualquer função do Azure. A identidade do aplicativo ou script é conhecida como entidade de serviço.

- **Azure Lighthouse**: seu cliente pode delegar uma assinatura (ou um grupo de recursos) para que os usuários possam trabalhar nela no seu locatário. Para obter mais informações, confira [Gerenciamento de recursos delegados do Azure](../../lighthouse/concepts/azure-delegated-resource-management.md).

## <a name="link-to-a-partner-id"></a>Vincular à uma ID de parceiro

Quando você tem acesso aos recursos do cliente, use o portal do Azure, o PowerShell ou a CLI do Azure para vincular sua ID da Microsoft Partner Network (ID da MPN) à sua ID de usuário ou entidade de serviço. Vincule a ID de parceiro em cada locatário do cliente.

### <a name="use-the-azure-portal-to-link-to-a-new-partner-id"></a>Use o portal do Azure para vincular a uma nova ID de parceiro

1. Vá para [Vincular a uma ID de parceiro](https://portal.azure.com/#blade/Microsoft_Azure_Billing/managementpartnerblade) no portal do Azure.

2. Entre no portal do Azure.

3. Insira a ID de parceiro da Microsoft. A ID do parceiro é a ID do [Microsoft Partner Network](https://partner.microsoft.com/) da sua organização. Use a **ID do MPN Associado** mostrada em seu perfil de parceiro.

   ![Captura de tela que mostra o vínculo a uma ID de parceiro](./media/link-partner-id/link-partner-id01.png)

4. Para vincular a ID de parceiro a outro cliente, alterne o diretório. Em **Mudar diretório**, selecione o seu diretório.

   ![Captura de tela que mostra Mudar diretório](./media/link-partner-id/directory-switcher.png)

### <a name="use-powershell-to-link-to-a-new-partner-id"></a>Use o PowerShell para vincular a uma nova ID de parceiro

1. Instale o módulo do PowerShell [Az.ManagementPartner](https://www.powershellgallery.com/packages/Az.ManagementPartner/).

2. Entre no locatário do cliente com a conta de usuário ou a entidade de serviço. Para obter mais informações, veja [Entrar com o PowerShell](/powershell/azure/authenticate-azureps).

   ```azurepowershell-interactive
    C:\> Connect-AzAccount -TenantId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
   ```

3. Vincular a nova ID de parceiro. A ID do parceiro é a ID do [Microsoft Partner Network](https://partner.microsoft.com/) da sua organização. Use a **ID do MPN Associado** mostrada em seu perfil de parceiro.


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

2. Entre no locatário do cliente com a conta de usuário ou a entidade de serviço. Para obter mais informações, veja [Entrar com a CLI do Azure](/cli/azure/authenticate-azure-cli).

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

No entanto, se estiver gerenciando recursos do cliente por meio do Azure Lighthouse, você precisará criar o link em seu locatário do provedor de serviço usando uma conta que tenha acesso aos recursos do cliente. Para obter mais informações, confira [Vincular a ID de parceiro para acompanhar seu impacto em recursos delegados](../../lighthouse/how-to/partner-earned-credit.md).

**Outros parceiros ou clientes podem editar ou remover a vinculação à ID de parceiro?**

A vinculação está associada no nível da conta de usuário. Só você pode editar ou remover a vinculação à ID de parceiro. O cliente e outros parceiros não podem alterar a vinculação à ID de parceiro.

**Qual ID MPN deverei usar se minha empresa tiver várias?**

Use a **ID do MPN Associado** mostrada em seu perfil de parceiro.

**Onde posso encontrar relatórios de receita influenciados para a ID de parceiro vinculada?**

O relatório de Desempenho do Produto em Nuvem está disponível para parceiros no Partner Center no [painel Meus Insights](https://partner.microsoft.com/membership/reports/myinsights). É necessário selecionar o Link de Partner Admin como o tipo de associação de parceiro.

**Por que não consigo ver meu cliente nos relatórios?**

Não é possível ver o cliente nos relatórios pelos seguintes motivos

1. A conta de usuário vinculada não tem o [Azure RBAC (controle de acesso baseado em função)](../../role-based-access-control/overview.md) em nenhuma assinatura ou recurso do Azure do cliente.

2. A assinatura do Azure em que o usuário tem acesso ao [Azure RBAC (controle de acesso baseado em função)](../../role-based-access-control/overview.md) não tem nenhum uso.

**A ID do parceiro de link funciona com Azure Stack?**

Sim, você pode vincular sua ID de parceiro para o Azure Stack.

**Como fazer para vincular minha ID de Parceiro se a minha empresa usar o [Azure Lighthouse](../../lighthouse/overview.md) para acessar os recursos do cliente?**

Para as atividades do Azure Lighthouse serem reconhecidas, você precisará associar sua ID MPN a pelo menos uma conta de usuário que tenha acesso a cada uma das suas assinaturas integradas. Observe que você precisará fazer isso no locatário do provedor de serviços, e não no locatário da cada cliente. Para simplificar, recomendamos criar uma conta de entidade de serviço no locatário, associando-a à sua ID do MPN e, em seguida, conceder a ela acesso a todos os clientes que você integrar com uma [função interna do Azure qualificada para crédito obtido pelo parceiro](/partner-center/azure-roles-perms-pec). Para obter mais informações, confira [Vincular a ID de parceiro para acompanhar seu impacto em recursos delegados](../../lighthouse/how-to/partner-earned-credit.md).

**Como fazer para explicar o PAL (Link de Partner Admin) ao meu cliente?**

O PAL (link de Partner Admin) permite que a Microsoft identifique e reconheça os parceiros que estão ajudando os clientes a alcançar os objetivos de negócios e a perceber valor na nuvem. Os clientes precisam primeiro fornecer acesso ao recurso do Azure deles a um parceiro. Quando o acesso é concedido, a ID da MPN (ID da Microsoft Partner Network) do parceiro é associada. Essa associação ajuda a Microsoft a entender o ecossistema dos provedores de serviços de TI e a refinar as ferramentas e os programas necessários para dar um melhor suporte a nossos clientes comuns.

**Quais dados são coletados pelo PAL?**

A associação do PAL às credenciais existentes não fornece novos dados do cliente à Microsoft. Ela simplesmente fornece a telemetria à Microsoft na qual um parceiro está ativamente envolvido no ambiente do Azure de um cliente. A Microsoft pode atribuir a influência e a receita consumida do Azure do ambiente do cliente à organização do parceiro com base nas permissões da conta (função do Azure) e no escopo (grupo de gerenciamento, assinatura, grupo de recursos, recursos) fornecidos ao parceiro pelo cliente. 

**Isso afeta a segurança do ambiente do Azure de um cliente?**

A associação do PAL somente adiciona a ID da MPN do parceiro à credencial já provisionada e não altera nenhuma permissão (função do Azure) nem fornece dados de serviço do Azure adicionais para o parceiro nem para a Microsoft.