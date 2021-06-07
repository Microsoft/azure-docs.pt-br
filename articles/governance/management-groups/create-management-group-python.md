---
title: 'Início rápido: Criar um grupo de gerenciamento com o Python'
description: Neste guia de início rápido, você usará o Python para criar um grupo de gerenciamento a fim de organizar seus recursos em uma hierarquia de recursos.
ms.date: 01/29/2021
ms.topic: quickstart
ms.custom:
- devx-track-python
- mode-api
ms.openlocfilehash: 9aec47e067ca62f4902df2dafb6a5d6d50a26d0e
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533167"
---
# <a name="quickstart-create-a-management-group-with-python"></a>Início rápido: Criar um grupo de gerenciamento com o Python

Grupos de gerenciamento são contêineres que o ajudarão a gerenciar o acesso, a política e a conformidade entre várias assinaturas. Criar esses contêineres para criar uma hierarquia eficaz e eficiente que pode ser usada com o [Azure Policy](../policy/overview.md) e os [Controles de Acesso Baseados em Função do Azure](../../role-based-access-control/overview.md). Para obter mais informações sobre grupos de gerenciamento, consulte [Organizar seus recursos com grupos de gerenciamento do Azure](overview.md).

O primeiro grupo de gerenciamento criado no diretório pode levar até 15 minutos para ser concluído. Há processos que são executados pela primeira vez para configurar o serviço de grupos de gerenciamento no Azure para seu diretório. Você recebe uma notificação quando o processo é concluído. Para obter mais informações, confira [Configuração inicial dos grupos de gerenciamento](./overview.md#initial-setup-of-management-groups).

## <a name="prerequisites"></a>Pré-requisitos

- Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

- Qualquer usuário do Azure AD no locatário poderá criar um grupo de gerenciamento sem ter recebido a permissão de gravação do grupo de gerenciamento se a [proteção de hierarquia](./how-to/protect-resource-hierarchy.md#setting---require-authorization) não estiver habilitada. Esse novo grupo de gerenciamento passa a ser um filho do grupo de gerenciamento raiz ou o [grupo de gerenciamento padrão](./how-to/protect-resource-hierarchy.md#setting---default-management-group), e o criador recebe uma atribuição de função "Proprietário". O serviço de grupo de gerenciamento permite essa capacidade para que as atribuições de função não sejam necessárias no nível raiz. Nenhum usuário tem acesso ao grupo de gerenciamento raiz quando ele é criado. Para evitar problemas de localização dos administradores globais do Azure AD para começar a usar os grupos de gerenciamento, permitimos a criação dos grupos de gerenciamento iniciais no nível raiz.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-resource-graph-library"></a>Adicionar a biblioteca do Resource Graph

Para permitir que o Python gerencie grupos de gerenciamento, a biblioteca precisará ser adicionada. Essa biblioteca funciona onde quer que o Python possa ser usado, incluindo o [bash no Windows 10](/windows/wsl/install-win10) ou instalado localmente.

1. Verifique se o Python mais recente está instalado (pelo menos a versão **3.8**). Se ele ainda não tiver sido instalado, baixe-o em [Python.org](https://www.python.org/downloads/).

1. Verifique se a CLI do Azure mais recente está instalada (pelo menos a versão **2.5.1**). Se ela ainda não tiver sido instalada, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

   > [!NOTE]
   > A CLI do Azure é necessária para habilitar o Python a usar a **autenticação baseada na CLI** do nos exemplos a seguir. Para obter informações sobre outras opções, confira [Autenticar-se usando as bibliotecas de gerenciamento do Azure para Python](/azure/developer/python/azure-sdk-authenticate).

1. Autentique-se por meio da CLI do Azure.

   ```azurecli
   az login
   ```

1. No ambiente do Python de sua escolha, instale as bibliotecas necessárias para os grupos de gerenciamento:

   ```bash
   # Add the management groups library for Python
   pip install azure-mgmt-managementgroups

   # Add the Resources library for Python
   pip install azure-mgmt-resource

   # Add the CLI Core library for Python for authentication (development only!)
   pip install azure-cli-core
   ```

   > [!NOTE]
   > Se o Python estiver instalado para todos os usuários, esse comando precisará ser executado em um console com privilégios elevados.

1. Confirme se as bibliotecas foram instaladas. `azure-mgmt-managementgroups` deve ser **0.2.0** ou superior, `azure-mgmt-resource` deve ser **9.0.0** ou superior e `azure-cli-core` deve ser **2.5.0** ou superior.

   ```bash
   # Check each installed library
   pip show azure-mgmt-managementgroups azure-mgmt-resource azure-cli-core
   ```

## <a name="create-the-management-group"></a>Criar o grupo de gerenciamento

1. Crie o script Python e salve o seguinte código-fonte como `mgCreate.py`:

   ```python
   # Import management group classes
   from azure.mgmt.managementgroups import ManagementGroupsAPI
   
   # Import specific methods and models from other libraries
   from azure.common.credentials import get_azure_cli_credentials
   from azure.common.client_factory import get_client_from_cli_profile
   from azure.mgmt.resource import ResourceManagementClient, SubscriptionClient
   
   # Wrap all the work in a function
   def createmanagementgroup( strName ):
       # Get your credentials from Azure CLI (development only!) and get your subscription list
       subsClient = get_client_from_cli_profile(SubscriptionClient)
       subsRaw = []
       for sub in subsClient.subscriptions.list():
           subsRaw.append(sub.as_dict())
       subsList = []
       for sub in subsRaw:
           subsList.append(sub.get('subscription_id'))
       
       # Create management group client and set options
       mgClient = get_client_from_cli_profile(ManagementGroupsAPI)
       mg_request = {'name': strName, 'display_name': strName}
       
       # Create management group
       mg = mgClient.management_groups.create_or_update(group_id=strName,create_management_group_request=mg_request)
       
       # Show results
       print(mg)
   
   createmanagementgroup("MyNewMG")
   ```

1. Autentique-se na CLI do Azure com `az login`.

1. Insira o seguinte comando no terminal:

   ```bash
   py mgCreate.py
   ```

O resultado da criação do grupo de gerenciamento é a saída para o console como um objeto `LROPoller`.

## <a name="clean-up-resources"></a>Limpar os recursos

Se desejar remover as bibliotecas instaladas do seu ambiente do Python, você poderá fazer isso usando o seguinte comando:

```bash
# Remove the installed libraries from the Python environment
pip uninstall azure-mgmt-managementgroups azure-mgmt-resource azure-cli-core
```

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você criou um grupo de gerenciamento para organizar sua hierarquia de recursos. O grupo de gerenciamento pode conter assinaturas ou outros grupos de gerenciamento.

Para saber mais sobre grupos de gerenciamento e como gerenciar sua hierarquia de recursos, prossiga para:

> [!div class="nextstepaction"]
> [Gerenciar seus recursos com grupos de gerenciamento](./manage.md)
