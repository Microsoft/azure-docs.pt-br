---
title: Solucionar problemas do RBAC do Azure
description: Solucionar problemas com o RBAC do Azure (controle de acesso baseado em função do Azure).
services: azure-portal
documentationcenter: na
author: rolyon
manager: mtillman
ms.assetid: df42cca2-02d6-4f3c-9d56-260e1eb7dc44
ms.service: role-based-access-control
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/10/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: seohack1, devx-track-azurecli
ms.openlocfilehash: d77468619fcd67887273b2fbd452b37add1e19b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100555887"
---
# <a name="troubleshoot-azure-rbac"></a>Solucionar problemas do RBAC do Azure

Este artigo responde a algumas perguntas comuns sobre o RBAC do Azure (controle de acesso baseado em função do Azure), para que você saiba o que esperar ao usar as funções e possa solucionar problemas de acesso.

## <a name="azure-role-assignments-limit"></a>Limite de atribuições de função do Azure

O Azure dá suporte a até **2.000** atribuições de função por assinatura. Esse limite inclui atribuições de função na assinatura, no grupo de recursos e nos escopos de recursos. Se você receber a mensagem de erro "Nenhuma outra atribuição de função pode ser criada (código: RoleAssignmentLimitExceeded)" ao tentar atribuir uma função, experimente reduzir o número de atribuições de função na assinatura.

> [!NOTE]
> O limite de **2.000** atribuições de função por assinatura é fixo e não pode ser aumentado.

Se você estiver se aproximando desse limite, veja algumas formas de reduzir o número de atribuições de função:

- Adicione usuários a grupos e atribua funções aos grupos. 
- Combine várias funções internas com uma função personalizada. 
- Faça atribuições de função comuns em um escopo mais alto, como assinatura ou grupo de gerenciamento.
- Se você tiver o Azure AD Premium P2, torne as atribuições de função qualificadas no [Azure AD Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md) em vez de atribuídas permanentemente. 
- Adicione uma assinatura extra. 

Para obter o número de atribuições de função, você pode ver o [gráfico na página IAM (controle de acesso)](role-assignments-list-portal.md#list-number-of-role-assignments) no portal do Azure. Você também pode usar os seguintes comandos do Azure PowerShell:

```azurepowershell
$scope = "/subscriptions/<subscriptionId>"
$ras = Get-AzRoleAssignment -Scope $scope | Where-Object {$_.scope.StartsWith($scope)}
$ras.Count
```

## <a name="problems-with-azure-role-assignments"></a>Problemas com as atribuições de função do Azure

- Se você não consegue atribuir uma função no portal do Azure no **IAM (controle de acesso)** porque a opção **Adicionar** > **Adicionar atribuição de função** está desabilitada ou porque recebe o erro de permissões "O cliente com a ID de objeto não tem autorização para executar a ação", verifique se está atualmente conectado como um usuário com atribuição de função que tenha a permissão `Microsoft.Authorization/roleAssignments/write`, como [Proprietário](built-in-roles.md#owner) ou [Administrador de Acesso do Usuário](built-in-roles.md#user-access-administrator), no escopo ao qual você está tentando atribuir a função.
- Se você estiver usando uma entidade de serviço para atribuir funções, poderá obter o erro "Privilégios insuficientes para concluir a operação". Por exemplo, digamos que você tenha uma entidade de serviço à qual tenha sido atribuída a função Proprietário e tente criar a seguinte atribuição de função como entidade de serviço usando a CLI do Azure:

    ```azurecli
    az login --service-principal --username "SPNid" --password "password" --tenant "tenantid"
    az role assignment create --assignee "userupn" --role "Contributor"  --scope "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}"
    ```

    Se você receber o erro "Privilégios insuficientes para concluir a operação", é provável que a CLI do Azure esteja tentando pesquisar a identidade do destinatário no Azure AD e a entidade de serviço não consegue ler o Azure AD por padrão.

    Há duas maneiras possíveis de resolver esse erro. A primeira delas é atribuir a função [Leitores de Diretório](../active-directory/roles/permissions-reference.md#directory-readers) à entidade de serviço para que possa ler dados no diretório.

    A segunda maneira de resolver esse erro é criar a atribuição de função usando o parâmetro `--assignee-object-id` em vez de `--assignee`. Usando `--assignee-object-id`, a CLI do Azure ignora a pesquisa do Azure AD. Você precisa obter a ID de objeto do usuário, grupo ou aplicativo ao qual deseja atribuir a função. Para obter mais informações, confira [Atribuir funções do Azure usando a CLI do Azure](role-assignments-cli.md#assign-a-role-for-a-new-service-principal-at-a-resource-group-scope).

    ```azurecli
    az role assignment create --assignee-object-id 11111111-1111-1111-1111-111111111111  --role "Contributor" --scope "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}"
    ```
- Se você tentar remover a última atribuição de função do proprietário de uma assinatura, poderá ver o erro "Não é possível excluir a última atribuição de administrador RBAC". Não há suporte para a remoção da atribuição de função do proprietário de uma assinatura para evitar que ela fique órfã. Se você quiser cancelar sua assinatura, confira [Cancelar sua assinatura do Azure](../cost-management-billing/manage/cancel-azure-subscription.md).

## <a name="problems-with-custom-roles"></a>Problemas com funções personalizadas

- Se você precisa consultar etapas para criar uma função personalizada, confira os tutoriais de função personalizada usando o [portal do Azure](custom-roles-portal.md) (em versão prévia no momento), o [Azure PowerShell](tutorial-custom-role-powershell.md) ou a [CLI do Azure](tutorial-custom-role-cli.md).
- Se não for possível atualizar uma função personalizada existente, verifique se você está atualmente conectado como um usuário com atribuição de função que tenha a permissão `Microsoft.Authorization/roleDefinition/write`, como [Proprietário](built-in-roles.md#owner) ou [Administrador de Acesso do Usuário](built-in-roles.md#user-access-administrator).
- A impossibilidade de excluir uma função personalizada e a mensagem de erro "Existem atribuições de função que fazem referência à função (código: RoleDefinitionHasAssignments)" significam que há atribuições de função que ainda estão usando a função personalizada. Remova essas atribuições de função e tente excluir a função personalizada novamente.
- Se você receber a mensagem de erro "Limite de definição de função excedido. Nenhuma outra definição de função pode ser criada (código: RoleDefinitionLimitExceeded)" ao tentar criar uma função personalizada, exclua as funções personalizadas que não estão sendo usadas. O Azure dá suporte a até **5.000** funções personalizadas em um diretório. (Para o Azure Alemanha e o Azure China 21Vianet, o limite é de 2.000 funções personalizadas.)
- Se você receber um erro semelhante a "O cliente tem permissão para executar a ação 'Microsoft.Authorization/roleDefinitions/write' no escopo '/subscriptions/{subscriptionid}', mas a assinatura vinculada não foi encontrada" ao tentar atualizar uma função personalizada, verifique se um ou mais [escopos atribuíveis](role-definitions.md#assignablescopes) foram excluídos do diretório. Se o escopo tiver sido excluído, crie um tíquete de suporte, pois não há nenhuma solução de autoatendimento disponível no momento.

## <a name="custom-roles-and-management-groups"></a>Funções personalizadas e grupos de gerenciamento

- Você só pode definir um grupo de gerenciamento em `AssignableScopes` de uma função personalizada. A adição de um grupo de gerenciamento a `AssignableScopes` está em versão prévia no momento.
- Funções personalizadas com `DataActions` não podem ser atribuídas no escopo do grupo de gerenciamento.
- O Azure Resource Manager não valida a existência do grupo de gerenciamento no escopo atribuível da definição de função.
- Para obter mais informações sobre funções personalizadas e grupos de gerenciamento, confira [Organizar seus recursos com grupos de gerenciamento do Azure](../governance/management-groups/overview.md#azure-custom-role-definition-and-assignment).

## <a name="transferring-a-subscription-to-a-different-directory"></a>Transferência de uma assinatura para um diretório diferente

- Se você precisar consultar etapas sobre como transferir uma assinatura a um diretório diferente do Azure AD, confira [Transferir uma assinatura do Azure para um diretório diferente do Azure AD](transfer-subscription.md).
- Quando você transfere uma assinatura a um diretório diferente do Azure AD, todas as atribuições de função são excluídas **permanentemente** do diretório de origem do Azure AD e não são migradas para o diretório de destino do Azure AD. Você precisa recriar as atribuições de função no diretório de destino. Você também precisa recriar manualmente as identidades gerenciadas para recursos do Azure. Para obter mais informações, confira [Perguntas frequentes e problemas conhecidos com identidades gerenciadas](../active-directory/managed-identities-azure-resources/known-issues.md).
- Se você é Administrador global do Azure AD e perdeu o acesso a uma assinatura quando ela foi transferida entre diretórios, use a alternância do **Gerenciamento de acesso para recursos do Azure** para [elevar seu acesso](elevate-access-global-admin.md) temporariamente e ter acesso à assinatura.

## <a name="issues-with-service-admins-or-co-admins"></a>Problemas com os administradores de serviço ou coadministradores

- Se você estiver tendo problemas com o Administrador de serviços ou os coadministradores, confira [Adicionar ou alterar administradores de assinatura do Azure](../cost-management-billing/manage/add-change-subscription-administrator.md) e [Funções de administrador da assinatura clássica, funções do Azure e funções do Azure AD](rbac-and-directory-admin-roles.md).

## <a name="access-denied-or-permission-errors"></a>Acesso negado ou erros de permissão

- Se você receber o erro de permissões "O cliente com o ID de objeto não tem autorização para executar a ação no escopo (código: AuthorizationFailed)" ao tentar criar um recurso, verifique se está atualmente conectado como um usuário com atribuição de função que tenha permissão de gravação para o recurso no escopo selecionado. Por exemplo, para gerenciar máquinas virtuais em um grupo de recursos, você deverá ter a função [Colaborador da Máquina Virtual](built-in-roles.md#virtual-machine-contributor) no grupo de recursos (ou escopo pai). Para obter uma lista das permissões de cada função interna, confira [Funções internas do Azure](built-in-roles.md).
- Se receber o erro de permissões "Você não tem permissão para criar uma solicitação de suporte" ao tentar criar ou atualizar um tíquete de suporte, verifique se você está atualmente conectado como um usuário com atribuição de função que tenha atribuída a permissão `Microsoft.Support/supportTickets/write`, como [Colaborador de Solicitação de Suporte](built-in-roles.md#support-request-contributor).

## <a name="move-resources-with-role-assignments"></a>Mover recursos com atribuições de função

Se você mover um recurso que tenha uma função do Azure atribuída diretamente ao recurso (ou a um recurso filho), a atribuição de função não será movida e se tornará órfã. Após a movimentação, você precisa recriar a atribuição de função. Por fim, a atribuição de função órfã será removida automaticamente, mas a melhor prática é remover a atribuição de função antes de mover o recurso.

Para obter informações sobre como mover recursos, confira [Mover recursos para um novo grupo de recursos ou assinatura](../azure-resource-manager/management/move-resource-group-and-subscription.md).

## <a name="role-assignments-with-identity-not-found"></a>Atribuições de função com identidade não encontrada

Na lista de atribuições de função para o portal do Azure, você pode observar que a entidade de segurança (usuário, grupo, entidade de serviço ou identidade gerenciada) está listada como **Identidade não encontrada** com um tipo **Desconhecido**.

![Identidade não encontrada listada nas atribuições de função do Azure](./media/troubleshooting/unknown-security-principal.png)

A identidade pode não ser encontrada por dois motivos:

- Você convidou recentemente um usuário ao criar uma atribuição de função
- Você excluiu uma entidade de segurança que tinha uma atribuição de função

Se você convidou recentemente um usuário ao criar uma atribuição de função, essa entidade de segurança ainda pode estar no processo de replicação entre regiões. Nesse caso, aguarde alguns instantes e atualize a lista de atribuições de função.

No entanto, se essa entidade de segurança não for um usuário convidado recentemente, ela poderá ser uma entidade de segurança excluída. Se você atribuir uma função a uma entidade de segurança e posteriormente excluí-la sem primeiro remover a atribuição de função, a entidade de segurança será listada como **Identidade não encontrada** e um tipo **Desconhecido**.

Se você listar essa atribuição de função usando o Azure PowerShell, poderá ver um `DisplayName` vazio e um `ObjectType` definido como **Desconhecido**. Por exemplo, [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) retorna uma atribuição de função semelhante à seguinte saída:

```
RoleAssignmentId   : /subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
Scope              : /subscriptions/11111111-1111-1111-1111-111111111111
DisplayName        :
SignInName         :
RoleDefinitionName : Storage Blob Data Contributor
RoleDefinitionId   : ba92f5b4-2d11-453d-a403-e96b0029c9fe
ObjectId           : 33333333-3333-3333-3333-333333333333
ObjectType         : Unknown
CanDelegate        : False
```

Da mesma forma, se você listar essa atribuição de função usando a CLI do Azure, poderá ver um `principalName` vazio. Por exemplo, [az role assignment list](/cli/azure/role/assignment#az-role-assignment-list) retorna uma atribuição de função semelhante à seguinte saída:

```
{
    "canDelegate": null,
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222",
    "name": "22222222-2222-2222-2222-222222222222",
    "principalId": "33333333-3333-3333-3333-333333333333",
    "principalName": "",
    "roleDefinitionId": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/ba92f5b4-2d11-453d-a403-e96b0029c9fe",
    "roleDefinitionName": "Storage Blob Data Contributor",
    "scope": "/subscriptions/11111111-1111-1111-1111-111111111111",
    "type": "Microsoft.Authorization/roleAssignments"
}
```

Não é um problema deixar essas atribuições de função onde a entidade de segurança foi excluída. Se desejar, você poderá remover essas atribuições de função usando etapas semelhantes a outras atribuições de função. Para obter informações sobre como remover atribuições de função, confira [Remover atribuições de função do Azure](role-assignments-remove.md).

No PowerShell, se você tentar remover as atribuições de função usando a ID de objeto e o nome de definição de função e mais de uma atribuição de função corresponder aos parâmetros, você receberá a mensagem de erro: "As informações fornecidas não são mapeadas para uma atribuição de função". A seguinte saída mostra um exemplo da mensagem de erro:

```
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor"

Remove-AzRoleAssignment : The provided information does not map to a role assignment.
At line:1 char:1
+ Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo          : CloseError: (:) [Remove-AzRoleAssignment], KeyNotFoundException
+ FullyQualifiedErrorId : Microsoft.Azure.Commands.Resources.RemoveAzureRoleAssignmentCommand
```

Se você receber essa mensagem de erro, especifique também os parâmetros `-Scope` ou `-ResourceGroupName`.

```
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor" - Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="role-assignment-changes-are-not-being-detected"></a>Não estão sendo detectadas alterações de atribuição de função

Às vezes, o Azure Resource Manager armazena em cache configurações e dados para melhorar o desempenho. Quando você atribui funções ou remove atribuições de função, pode levar até 30 minutos para que as alterações entrem em vigor. Se estiver usando o portal do Azure, o Azure PowerShell ou a CLI do Azure, será possível forçar uma atualização das alterações de atribuição de função, saindo e entrando novamente. Se estiver fazendo alterações de atribuição de função com chamadas à API REST, poderá forçar uma atualização atualizando o token de acesso.

Se você for adicionar ou remover uma atribuição de função no escopo do grupo de gerenciamento e a função tiver `DataActions`, o acesso no plano de dados poderá não ser atualizado por várias horas. Isso se aplica somente ao escopo do grupo de gerenciamento e ao plano de dados.

## <a name="web-app-features-that-require-write-access"></a>Recursos de aplicativo Web que exigem acesso para gravação

Se você conceder a um usuário o acesso somente leitura a um aplicativo Web, para sua surpresa, alguns recursos estarão desabilitados. As funcionalidades de gerenciamento a seguir exigem o acesso de **gravação** em um aplicativo Web (Colaborador ou Proprietário) e não estão disponíveis em um cenário somente leitura.

* Comandos (como iniciar, parar, etc.)
* Alterar configurações como configuração geral, configurações de escala, configurações de backup e configurações de monitoramento.
* Acessar credenciais de publicação e outros segredos como configurações de aplicativos e cadeias de conexão.
* Logs de streaming
* Configuração de logs de recursos
* Console (prompt de comando)
* Ativo e implantações recentes (para a implantação contínua do git local)
* Gasto estimado
* Testes da Web
* Rede virtual (somente visível para um leitor se uma rede virtual foi anteriormente configurada por um usuário com acesso para gravação).

Se você não conseguir acessar nenhum desses blocos, precisará solicitar ao administrador o acesso de Colaborador ao aplicativo Web.

## <a name="web-app-resources-that-require-write-access"></a>Recursos de aplicativos Web que exigem acesso para gravação

Os aplicativos Web são complicados pela presença de alguns recursos diferentes que interagem. Aqui está um grupo de recursos típico com alguns sites:

![Grupo de recursos do aplicativo Web](./media/troubleshooting/website-resource-model.png)

Como resultado, se você conceder a alguém o acesso somente ao aplicativo Web, muitas das funcionalidades na folha do site no portal do Azure estarão desabilitadas.

Estes itens exigem acesso para **gravação** no **Plano do Serviço de Aplicativo** que corresponde ao seu site:  

* Exibindo o tipo de preço do aplicativo Web (Gratuito ou Standard)  
* Configuração de escala (número instâncias, tamanho da máquina virtual, configurações de escalonamento automático)  
* Cotas (armazenamento, largura de banda, CPU)  

Estes itens exigem acesso para **gravação** no **Grupo de recursos** inteiro que contém o seu site:  

* Associações e Certificados TLS/SSL (os certificados TLS/SSL podem ser compartilhados entre sites no mesmo grupo de recursos e localização geográfica)  
* Regras de alerta  
* Configurações de autoescala  
* Componentes do Application insights  
* Testes da Web  

## <a name="virtual-machine-features-that-require-write-access"></a>Recursos da máquina virtual que exigem acesso para gravação

Semelhante aos aplicativos Web, alguns recursos na folha da máquina virtual exigem acesso para gravação à máquina virtual ou a outros recursos no grupo de recursos.

As máquinas virtuais são relacionadas a nomes de domínio, redes virtuais, contas de armazenamento e regras de alerta.

Estes itens exigem acesso para **gravação** na **Máquina virtual**:

* Pontos de extremidade  
* Endereços IP  
* Discos  
* Extensões  

Estes exigem acesso para **gravação** tanto na **Máquina virtual** quanto no **Grupo de recursos** (juntamente com o Nome de domínio) encontrados em:  

* Conjunto de disponibilidade  
* Conjunto de balanceamento de carga  
* Regras de alerta  

Se você não conseguir acessar nenhum desses blocos, solicite ao administrador o acesso de Colaborador ao Grupo de recursos.

## <a name="azure-functions-and-write-access"></a>Azure Functions e acesso para gravação

Alguns recursos do [Azure Functions](../azure-functions/functions-overview.md) exigem acesso de gravação. Por exemplo, se um usuário receber a função [Reader](built-in-roles.md#reader), ele não poderá ver as funções em um aplicativo de funções. O portal exibirá **(Sem acesso)**.

![Aplicativos de funções sem acesso](./media/troubleshooting/functionapps-noaccess.png)

Um leitor pode clicar na guia **Recursos da plataforma** e, em seguida, clicar em **Todas as configurações** para exibir algumas configurações relacionadas a um aplicativo de funções (semelhante a um aplicativo Web), mas não pode modificar essas configurações. Para acessar esses recursos, você precisará da função [Colaborador](built-in-roles.md#contributor).

## <a name="next-steps"></a>Próximas etapas

- [Solucionar problemas de usuários convidados](role-assignments-external-users.md#troubleshoot)
- [Atribuir funções do Azure usando o portal do Azure](role-assignments-portal.md)
- [Exibir as alterações do RBAC do Azure nos logs de atividades](change-history-report.md)
