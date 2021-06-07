---
title: Usar uma identidade gerenciada de atribuída pelo usuário da VM do Linux para acessar o Azure Resource Manager
description: Um tutorial que orienta você durante o processo de uso de uma identidade gerenciada atribuída pelo usuário em uma VM do Linux para acessar o Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2020
ms.author: barclayn
ROBOTS: NOINDEX,NOFOLLOW
ms.collection: M365-identity-device-management
ms.openlocfilehash: e9c7555235283e892741234b74ddb80ce3a13051
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784705"
---
# <a name="tutorial-use-a-user-assigned-managed-identity-on-a-linux-vm-to-access-azure-resource-manager"></a>Tutorial: usar uma identidade gerenciada atribuída pelo usuário em uma VM do Linux para acessar o Azure Resource Manager

Este tutorial explica como criar uma identidade gerenciada atribuída pelo usuário, atribuí-la a uma VM (Máquina Virtual) do Linux e, em seguida, usar essa identidade para acessar a API do Azure Resource Manager. Identidades gerenciadas para recursos do Azure são gerenciadas automaticamente pelo Azure. Elas permitem que a autenticação para serviços compatíveis com a autenticação do Azure AD, sem a necessidade de inserir as credenciais em seu código. 

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma identidade gerenciada atribuída ao usuário
> * Atribuir a identidade gerenciada atribuída pelo usuário a uma VM do Linux 
> * Conceder à identidade gerenciada atribuída pelo usuário acesso a um Grupo de Recursos no Azure Resource Manager 
> * Obtenha um token de acesso usando a identidade gerenciada atribuída pelo usuário e use-o para chamar o Azure Resource Manager 

## <a name="prerequisites"></a>Pré-requisitos

- Conhecimento sobre Identidades Gerenciadas. Se você não estiver familiarizado com as identidades gerenciadas para funcionalidades de recursos do Azure, veja esta [visão geral](overview.md). 
- Uma conta do Azure, [inscreva-se em uma conta gratuita](https://azure.microsoft.com/free/).
- Você também precisa de uma máquina virtual do Linux. Se precisar criar uma máquina virtual para este tutorial, siga o artigo intitulado [Criar uma máquina virtual do Linux com o portal do Azure](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)
- Para executar os scripts de exemplo, você tem duas opções:
    - Use o [Azure Cloud Shell](../../cloud-shell/overview.md), que você pode abrir usando o botão **Experimentar** no canto superior direito dos blocos de código.
    - Execute os scripts localmente instalando a versão mais recente da [CLI do Azure](/cli/azure/install-azure-cli) e, em seguida, entre no Azure usando [az login](/cli/azure/reference-index#az_login).

## <a name="create-a-user-assigned-managed-identity"></a>Criar uma identidade gerenciada atribuída ao usuário

Crie uma identidade gerenciada atribuída ao usuário usando [az identity create](/cli/azure/identity#az_identity_create). O parâmetro `-g` especifica o grupo de recursos em que a identidade gerenciada atribuída pelo usuário é criada e o parâmetro `-n` especifica o nome. Substitua os valores de parâmetro `<RESOURCE GROUP>` e `<UAMI NAME>` pelos seus próprios valores:
    
[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <UAMI NAME>
```

A resposta contém detalhes para a identidade gerenciada atribuída pelo usuário criada, semelhante ao exemplo a seguir. Anote o valor `id` para sua identidade gerenciada atribuída pelo usuário, pois ela será usada na próxima etapa:

```json
{
"clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
"clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<UAMI NAME>/credentials?tid=5678&oid=9012&aid=12344643-8088-4d70-9532-c3a0fdc190fz",
"id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<UAMI NAME>",
"location": "westcentralus",
"name": "<UAMI NAME>",
"principalId": "9012",
"resourceGroup": "<RESOURCE GROUP>",
"tags": {},
"tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
"type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

## <a name="assign-an-identity-to-your-linux-vm"></a>Atribuir uma identidade à VM do Linux

Uma identidade gerenciada atribuída pelo usuário pode ser usada por clientes em vários recursos do Azure. Use os comandos a seguir para atribuir a identidade gerenciada atribuída pelo usuário a uma única VM. Use a propriedade `Id` retornada na etapa anterior para o valor do parâmetro `-IdentityID`.

Atribuir a identidade gerenciada atribuída pelo usuário à sua VM do Linux usando [az vm identity assign](/cli/azure/vm). Substitua os valores de parâmetro `<RESOURCE GROUP>` e `<VM NAME>` pelos seus próprios valores. Use a propriedade `id` retornada na etapa anterior para o valor do parâmetro `--identities`.

```azurecli-interactive
az vm identity assign -g <RESOURCE GROUP> -n <VM NAME> --identities "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<UAMI NAME>"
```

## <a name="grant-access-to-a-resource-group-in-azure-resource-manager"></a>Permitir acesso a um Grupo de Recursos no Azure Resource Manager

Identidades gerenciadas para recursos do Azure fornecem identidades que seu código pode usar para solicitar tokens de acesso para autenticação em APIs de recursos que dão suporte à autenticação do Microsoft Azure AD. Neste tutorial, seu código acessará a API do Azure Resource Manager.  

Antes que seu código possa acessar a API, você precisa conceder o acesso de identidade a um recurso no Azure Resource Manager. Nesse caso, o grupo de recursos no qual a VM está contida. Atualize o valor de `<SUBSCRIPTION ID>` e `<RESOURCE GROUP>` conforme apropriado para seu ambiente. Além disso, substitua `<UAMI PRINCIPALID>` pela propriedade `principalId` retornada pelo comando `az identity create` em [Criar uma identidade gerenciada atribuída pelo usuário](#create-a-user-assigned-managed-identity):

```azurecli-interactive
az role assignment create --assignee <UAMI PRINCIPALID> --role 'Reader' --scope "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP> "
```

A resposta contém detalhes para a atribuição de função criada, semelhante ao exemplo a seguir:

```json
{
  "id": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Authorization/roleAssignments/b402bd74-157f-425c-bf7d-zed3a3a581ll",
  "name": "b402bd74-157f-425c-bf7d-zed3a3a581ll",
  "properties": {
    "principalId": "f5fdfdc1-ed84-4d48-8551-999fb9dedfbl",
    "roleDefinitionId": "/subscriptions/<SUBSCRIPTION ID>/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "scope": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>"
  },
  "resourceGroup": "<RESOURCE GROUP>",
  "type": "Microsoft.Authorization/roleAssignments"
}

```

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-resource-manager"></a>Obter um token de acesso utilizando a identidade da VM e usá-la para chamar o Resource Manager 

Para o restante do tutorial, trabalharemos na VM criada anteriormente.

Para concluir essas etapas, você precisará do cliente SSH. Se você estiver usando o Windows, poderá usar o cliente SSH no [Subsistema do Windows para Linux](/windows/wsl/about). 

1. Entre no [Portal](https://portal.azure.com) do Azure.
2. No portal, navegue até **Máquinas Virtuais** e vá para a máquina virtual do Linux e, em **Visão geral**, clique em **Conectar**. Copie a cadeia de caracteres para conectar-se à VM.
3. Conecte-se à VM com um cliente SSH de sua escolha. Se você estiver usando o Windows, poderá usar o cliente SSH no [Subsistema do Windows para Linux](/windows/wsl/about). Se precisar de ajuda para configurar as chaves do cliente SSH, confira [Como usar chaves SSH com o Windows no Azure](~/articles/virtual-machines/linux/ssh-from-windows.md), ou [Como criar e usar um par de chaves SSH pública e privada para VMs Linux no Azure](~/articles/virtual-machines/linux/mac-create-ssh-keys.md).
4. Na janela de terminal, usando a CURL, faça uma solicitação para o ponto de extremidade do Serviço de Metadados de Instância (IMDS) do Azure para obter um token de acesso para o Azure Resource Manager.  

   A solicitação CURL para adquirir um token de acesso é mostrada no exemplo a seguir. Substitua `<CLIENT ID>` pela propriedade `clientId` retornada pelo comando `az identity create` em [Criar uma identidade gerenciada atribuída pelo usuário](#create-a-user-assigned-managed-identity): 
    
   ```bash
   curl -H Metadata:true "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com/&client_id=<UAMI CLIENT ID>"   
   ```
    
    > [!NOTE]
    > O valor do parâmetro `resource` deve ser uma correspondência exata para o que é esperado pelo Azure AD. Ao usar a ID de recurso do Resource Manager, você deve incluir a barra à direita no URI. 
    
    A resposta inclui o token de acesso que você precisa para acessar o Azure Resource Manager. 
    
    Exemplo de resposta:  

    ```bash
    {
    "access_token":"eyJ0eXAiOi...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://management.azure.com",
    "token_type":"Bearer"
    } 
    ```

5. Use o token de acesso para acessar o Azure Resource Manager e leia as propriedades do grupo de recursos ao qual você concedeu anteriormente o acesso à identidade gerenciada atribuída pelo usuário. Substitua `<SUBSCRIPTION ID>` e `<RESOURCE GROUP>` pelos valores que você especificou anteriormente e `<ACCESS TOKEN>` pelo token retornado na etapa anterior.

    > [!NOTE]
    > A URL diferencia maiúsculas de minúsculas, portanto, verifique se você usou letras maiúsculas e minúsculas exatamente da mesma forma que você usou anteriormente ao nomear o grupo de recursos, incluindo a letra maiúscula "G" em `resourceGroups`.  

    ```bash 
    curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>?api-version=2016-09-01 -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```

    A resposta contém as informações de grupo de recursos específicas, semelhantes ao exemplo a seguir: 

    ```bash
    {
    "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/DevTest",
    "name":"DevTest",
    "location":"westus",
    "properties":{"provisioningState":"Succeeded"}
    } 
    ```
    
## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a criar uma identidade gerenciada atribuída pelo usuário e a anexá-la a uma máquina virtual do Linux para acessar a API do Azure Resource Manager.  Para saber mais sobre o Azure Resource Manager, confira:

> [!div class="nextstepaction"]
>[Azure Resource Manager](../../azure-resource-manager/management/overview.md)
