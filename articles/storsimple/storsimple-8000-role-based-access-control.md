---
title: Usar o controle de acesso baseado em função do Azure para StorSimple | Microsoft Docs
description: Descreve como usar o controle de acesso baseado em função do Azure (RBAC do Azure) no contexto do StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: jconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/11/2017
ms.author: alkohli
ms.openlocfilehash: 49c38e23ddbbfe983ff82ad25363c744292d4d69
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2020
ms.locfileid: "92518969"
---
# <a name="azure-role-based-access-control-for-storsimple"></a>Controle de acesso baseado em função do Azure para StorSimple

Este artigo fornece uma breve descrição de como o controle de acesso baseado em função do Azure (RBAC do Azure) pode ser usado para seu dispositivo StorSimple. O RBAC do Azure oferece gerenciamento de acesso refinado para o Azure. Use o RBAC do Azure para conceder apenas a quantidade certa de acesso aos usuários do StorSimple para fazer seus trabalhos em vez de conceder a todos acesso irrestrito. Para obter mais informações sobre os fundamentos do gerenciamento de acesso no Azure, consulte [o que é o Azure RBAC (controle de acesso baseado em função do Azure)](../role-based-access-control/overview.md).

Este artigo se aplica a dispositivos da série StorSimple 8000 executando Atualização 3.0 ou posterior no portal do Azure.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-roles-for-storsimple"></a>Funções do Azure para StorSimple

O RBAC do Azure pode ser atribuído com base nas funções. As funções garantem determinados níveis de permissão com base nos recursos disponíveis no ambiente. Há dois tipos de funções que os usuários do StorSimple podem escolher: internas ou personalizadas.

* **Funções internas** – funções internas podem ser de proprietário, colaborador, leitor ou administrador de acesso do usuário. Para obter mais informações, consulte [funções internas para o controle de acesso baseado em função do Azure](../role-based-access-control/built-in-roles.md).

* **Funções personalizadas** – se as funções internas não atenderem às suas necessidades, você poderá criar funções personalizadas do Azure para o StorSimple. Para criar uma função personalizada do Azure, comece com uma função interna, edite-a e importe-a de volta no ambiente. O download e o upload da função são gerenciados usando o Azure PowerShell ou a CLI do Azure. Para obter mais informações, consulte [Criar funções personalizadas para controle de acesso baseado em função](../role-based-access-control/custom-roles.md).

Para exibir as diferentes funções disponíveis para um usuário do dispositivo StorSimple no portal do Azure, vá para o serviço do Gerenciador de Dispositivos do StorSimple e, em seguida, para **Controle de acesso (IAM) > Funções**.


## <a name="create-a-custom-role-for-storsimple-infrastructure-administrator"></a>Crie uma função personalizada para o Administrador de Infraestrutura do StorSimple

No exemplo a seguir, vamos começar com a função interna de **Leitor** que permite aos usuários exibir todos os escopos de recursos, mas não editá-los nem criar novos. Em seguida, estendemos essa função para criar um novo administrador de infraestrutura do StorSimple de função personalizada. Essa função é atribuída aos usuários que podem gerenciar a infraestrutura para os dispositivos StorSimple.

1. Execute o Windows PowerShell como um administrador.

2. Fazer logon no Azure.

    `Connect-AzAccount`

3. Exporte a função de Leitor como um modelo JSON em seu computador.

    ```powershell
    Get-AzRoleDefinition -Name "Reader"

    Get-AzRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\ssrbaccustom.json
    ```

4. Abra o arquivo JSON no Visual Studio. Você verá que uma função típica do Azure consiste em três seções principais, **ações**, não **ações**e **AssignableScopes**.

    Na seção **Action**, são listadas todas as operações permitidas para essa função. Cada ação é atribuída de um provedor de recursos. Para um administrador de infraestrutura do StorSimple, use o provedor de recursos `Microsoft.StorSimple`.

    Use o PowerShell para ver todos os provedores de recursos disponíveis e registrados em sua assinatura.

    `Get-AzResourceProvider`

    Você também pode verificar os todos os cmdlets do PowerShell disponíveis para gerenciar os provedores de recursos.

    Nas seções não- **ações** , todas as ações restritas para uma função específica do Azure são listadas. Neste exemplo, nenhuma ação é restrita.
    
    Em **AssignableScopes**, as IDs de assinatura são listadas. Verifique se a função do Azure contém a ID de assinatura explícita onde ela é usada. Se a ID de assinatura correta não for especificada, você não terá permissão para importar a função para sua assinatura.

    Edite o arquivo tendo em mente as considerações anteriores.

    ```json
    {
        "Name":  "StorSimple Infrastructure Admin",
        "Id":  "<guid>",
        "IsCustom":  true,
        "Description":  "Lets you view everything, but not make any changes except for Clear alerts, Clear settings, install, download etc.",
        "Actions":  [
                        "Microsoft.StorSimple/managers/alerts/read",
                        "Microsoft.StorSimple/managers/devices/volumeContainers/read",
                        "Microsoft.StorSimple/managers/devices/jobs/read",
                        "Microsoft.StorSimple/managers/devices/alertSettings/read",
                        "Microsoft.StorSimple/managers/devices/alertSettings/write",
                        "Microsoft.StorSimple/managers/clearAlerts/action",
                        "Microsoft.StorSimple/managers/devices/networkSettings/read",
                        "Microsoft.StorSimple/managers/devices/publishSupportPackage/action",
                        "Microsoft.StorSimple/managers/devices/scanForUpdates/action",
                        "Microsoft.StorSimple/managers/devices/metrics/read"

                    ],
        "NotActions":  [

                    ],
        "AssignableScopes":  [
                                "/subscriptions/<subscription_ID>/"
                            ]
    }
    ```

6. Importe a função personalizada do Azure de volta para o ambiente.

    `New-AzRoleDefinition -InputFile "C:\ssrbaccustom.json"`


Essa função agora deve aparecer na lista de funções na folha **Controle de acesso**.

![Exibir funções do Azure](./media/storsimple-8000-role-based-access-control/rbac-role-types.png)

Para obter mais informações, consulte [Funções personalizadas](../role-based-access-control/custom-roles.md).

### <a name="sample-output-for-custom-role-creation-via-the-powershell"></a>Saída de exemplo para a criação de função personalizada por meio do PowerShell

```powershell
Connect-AzAccount
```

```Output
Environment           : AzureCloud
Account               : john.doe@contoso.com
TenantId              : <tenant_ID>
SubscriptionId        : <subscription_ID>
SubscriptionName      : Internal Consumption
CurrentStorageAccount :
```

```powershell
Get-AzRoleDefinition -Name "Reader"
```

```Output
Name             : Reader
Id               : <guid>
IsCustom         : False
Description      : Lets you view everything, but not make any changes.
Actions          : {*/read}
NotActions       : {}
AssignableScopes : {/}
```

```powershell
Get-AzRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\ssrbaccustom.json
New-AzRoleDefinition -InputFile "C:\ssrbaccustom.json"
```

```Output
Name             : StorSimple Infrastructure Admin
Id               : <tenant_ID>
IsCustom         : True
Description      : Lets you view everything, but not make any changes except for Clear alerts, Clear settings, install,
                   download etc.
Actions          : {Microsoft.StorSimple/managers/alerts/read,
                   Microsoft.StorSimple/managers/devices/volumeContainers/read,
                   Microsoft.StorSimple/managers/devices/jobs/read,
                   Microsoft.StorSimple/managers/devices/alertSettings/read...}
NotActions       : {}
AssignableScopes : {/subscriptions/<subscription_ID>/}
```

## <a name="add-users-to-the-custom-role"></a>Adicionar usuários à função personalizada

Conceda acesso de dentro do recurso, do grupo de recursos ou da assinatura que é o escopo da atribuição de função. Ao fornecer acesso, tenha em mente que o acesso concedido no nó pai é herdado pelo filho. Para obter mais informações, vá para [controle de acesso baseado em função do Azure (RBAC do Azure)](../role-based-access-control/overview.md).

1. Vá para **controle de acesso (iam)**. Selecione **+ Adicionar** na folha Controle de acesso.

    ![Adicionar acesso à função do Azure](./media/storsimple-8000-role-based-access-control/rbac-add-role.png)

2. Selecione a função que você deseja atribuir, neste caso, **Administrador de infraestrutura do StorSimple**.

3. Selecione o usuário, o grupo ou o aplicativo ao qual você deseja conceder acesso. Você pode pesquisar o diretório por nomes para exibição, endereços de email e identificadores de objeto.

4. Selecione **Salvar** para criar a atribuição.

    ![Adicionar permissões à função do Azure](./media/storsimple-8000-role-based-access-control/rbac-create-role-infra-admin.png)

Uma notificação de **Adicionando usuário** controla o progresso. Depois que o usuário é adicionado com êxito, a lista de usuários no controle de Acesso é atualizada.

## <a name="view-permissions-for-the-custom-role"></a>Exibir permissões para a função personalizada

Quando essa função está criada, você pode exibir as permissões associadas a ela no portal do Azure.

1. Para exibir as permissões associadas a essa função, vá para **controle de acesso (iam) > funções > administrador de infraestrutura do StorSimple**. A lista de usuários nesta função é exibida.

2. Selecione um usuário Administrador de Infraestrutura do StorSimple e clique em **Permissões**.

    ![Exibir permissões para a função de Administrador Infraestrutura do StorSimple](./media/storsimple-8000-role-based-access-control/rbac-roles-view-permissions.png)

3. As permissões associadas a essa função são exibidas.

    ![Exibir usuários na função de Administrador de Infraestrutura do StorSimple](./media/storsimple-8000-role-based-access-control/rbac-infra-admin-permissions1.png)


## <a name="next-steps"></a>Próximas etapas

Saiba como [Atribuir funções personalizadas para usuários internos e externos](../role-based-access-control/role-assignments-external-users.md).
