---
title: Solucionar Problemas de erros comuns de implantação
description: Descreve como resolver erros comuns ao implantar recursos no Azure usando o Azure Resource Manager.
tags: top-support-issue
ms.topic: troubleshooting
ms.date: 01/20/2021
ms.openlocfilehash: 40e6317a1d879704ef00e928a971ae08fc9e0f72
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105564340"
---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-resource-manager"></a>Solução de erros comuns de implantação do Azure com o Azure Resource Manager

Este artigo descreve alguns erros comuns de implantação do Azure e fornece informações para resolver os erros. Se não encontrar o código de erro para o erro de implantação, confira [Localizar código de erro](#find-error-code).

Se você estiver procurando informações sobre um código de erro e se essas informações não forem fornecidas neste artigo, informe-nos. Na parte inferior desta página, você pode deixar comentários. Os comentários são acompanhados com problemas do GitHub.

## <a name="error-codes"></a>Códigos do Erro

| Código do erro | Atenuação | Mais informações |
| ---------- | ---------- | ---------------- |
| AccountNameInvalid | Siga as restrições de nomenclatura para as contas de armazenamento. | [Resolver o nome da conta de armazenamento](error-storage-account-name.md) |
| AccountPropertyCannotBeSet | Verifique as propriedades da conta de armazenamento disponível. | [storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |
| AllocationFailed | O cluster ou a região não tem recursos disponíveis ou não é compatível com o tamanho solicitado de VM. Repita a solicitação mais tarde ou solicite um tamanho de VM diferente. | [Problemas de provisionamento e alocação para Linux](/troubleshoot/azure/virtual-machines/troubleshoot-deployment-new-vm-linux), [Problemas de provisionamento e alocação para Windows](/troubleshoot/azure/virtual-machines/troubleshoot-deployment-new-vm-windows) e [Solucionar problemas de falhas de alocação](/troubleshoot/azure/virtual-machines/allocation-failure)|
| AnotherOperationInProgress | Aguarde a conclusão da operação simultânea. | |
| AuthorizationFailed | Sua conta ou entidade de serviço não tem acesso suficiente para concluir a implantação. Verifique a função a que sua conta pertence e seu acesso para o escopo da implantação.<br><br>Você pode receber esse erro quando um provedor de recursos necessário não está registrado. | [RBAC do Azure (controle de acesso baseado em função do Azure)](../../role-based-access-control/role-assignments-portal.md)<br><br>[Resolver registro](error-register-resource-provider.md) |
| BadRequest | Você enviou valores de implantação que não coincidem com o que é esperado pelo Resource Manager. Verifique a mensagem de status interna para obter ajuda com a solução de problemas. | [Referência de modelos](/azure/templates/) e [Locais com suporte](resource-location.md) |
| Conflito | Você está solicitando uma operação não permitida no estado atual do recurso. Por exemplo, o redimensionamento do disco é permitido apenas ao criar uma VM ou quando a VM é desalocada. | |
| DeploymentActiveAndUneditable | Aguarde a conclusão da implantação simultânea nesse grupo de recursos. | |
| DeploymentFailedCleanUp | Quando você implanta no modo completo, todos os recursos que não estão no modelo são excluídos. Você recebe esse erro quando não tem as permissões adequadas para excluir todos os recursos que não estão no modelo. Para evitar o erro, altere o modo de implantação para incremental. | [Modos de implantação do Azure Resource Manager](deployment-modes.md) |
| DeploymentNameInvalidCharacters | O nome da implantação só pode conter letras, dígitos, '-', '. ' ou ' _ '. | |
| DeploymentNameLengthLimitExceeded | Os nomes de implantação são limitados a 64 caracteres.  | |
| DeploymentFailed | O erro DeploymentFailed é um erro geral que não fornece os detalhes necessários para resolvê-lo. Examine os detalhes do erro em busca de um código de erro que fornece mais informações. | [Encontrar código do erro](#find-error-code) |
| DeploymentQuotaExceeded | Caso você atinja o limite de 800 implantações por grupo de recursos, exclua do histórico as implantações que não são mais necessárias. | [Resolver erro quando a contagem de implantação exceder 800](deployment-quota-exceeded.md) |
| DeploymentJobSizeExceeded | Simplifique seu modelo para reduzir o tamanho. | [Resolver erros de tamanho do modelo](error-job-size-exceeded.md) |
| DnsRecordInUse | O nome do registro DNS deve ser exclusivo. Insira um nome diferente. | |
| ImageNotFound | Verifique as configurações de imagem da VM. |  |
| InUseSubnetCannotBeDeleted | Você pode receber esse erro ao tentar atualizar um recurso, e a solicitação é processada excluindo e criando o recurso. Certifique-se de especificar todos os valores inalterados. | [Atualizar recurso](/azure/architecture/guide/azure-resource-manager/advanced-templates/update-resource) |
| InvalidAuthenticationTokenTenant | Obtenha o token de acesso do locatário correto. Você só pode obter o token do locatário a que sua conta pertence. | |
| InvalidContentLink | Você provavelmente tentou vincular a um modelo aninhado que não está disponível. Verifique uma segunda vez o URI que você forneceu para o modelo aninhado. Caso o modelo exista em uma conta de armazenamento, verifique se o URI está acessível. Talvez seja necessário passar um token SAS. No momento, não é possível vincular a um modelo que está em uma conta de armazenamento por trás de um [Firewall de armazenamento do Azure](../../storage/common/storage-network-security.md). Considere mover o modelo para outro repositório, como o GitHub. | [Modelos vinculados](linked-templates.md) |
| InvalidDeploymentLocation | Ao implantar no nível de assinatura, você forneceu um local diferente para um nome de implantação usado anteriormente. | [Implantações de nível de assinatura](deploy-to-subscription.md) |
| InvalidParameter | Um dos valores que você forneceu para um recurso não corresponde ao valor esperado. Esse erro pode ser decorrente de várias condições diferentes. Por exemplo, uma senha pode ser insuficiente ou um nome de blob pode estar incorreto. A mensagem de erro deve indicar qual valor precisa ser corrigido. | |
| InvalidRequestContent | Os valores de implantação incluem valores que não são reconhecidos ou valores necessários estão ausentes. Confirme se os valores para seu tipo de recurso. | [Referência de modelo](/azure/templates/) |
| InvalidRequestFormat | Habilite o log de depuração ao executar a implantação e verifique o conteúdo da solicitação. | [Log de depuração](#enable-debug-logging) |
| InvalidResourceNamespace | Verifique o namespace do recurso especificado na propriedade **type**. | [Referência de modelo](/azure/templates/) |
| InvalidResourceReference | O recurso ainda não existe ou foi referenciado incorretamente. Verifique se você precisa adicionar uma dependência. Verifique se o uso da função **referência** inclui os parâmetros necessários para seu cenário. | [Resolver dependências](error-not-found.md) |
| InvalidResourceType | Verifique o tipo de recurso do recurso especificado na propriedade **type**. | [Referência de modelo](/azure/templates/) |
| InvalidSubscriptionRegistrationState | Registre sua assinatura no provedor de recursos. | [Resolver registro](error-register-resource-provider.md) |
| InvalidTemplate | Verifique se há erros na sintaxe do modelo. | [Resolver modelo inválido](error-invalid-template.md) |
| InvalidTemplateCircularDependency | Remova as dependências desnecessárias. | [Resolver as dependências circulares](error-invalid-template.md#circular-dependency) |
| JobSizeExceeded | Simplifique seu modelo para reduzir o tamanho. | [Resolver erros de tamanho do modelo](error-job-size-exceeded.md) |
| LinkedAuthorizationFailed | Verifique se sua conta pertence ao mesmo locatário que o grupo de recursos no qual você está implantando. | |
| LinkedInvalidPropertyId | A ID de um recurso não está sendo resolvida corretamente. Verifique se você forneceu todos os valores necessários para a ID do recurso, incluindo a ID de assinatura, nome do grupo de recursos, o tipo de recurso, o nome do recurso pai (se necessário) e o nome de recurso. | |
| LocationRequired | Forneça um local para o recurso. | [Definir local](resource-location.md) |
| MismatchingResourceSegments | Verifique se o recurso aninhado tem o número correto de segmentos de nome e tipo. | [Resolver segmentos de recurso](error-invalid-template.md#incorrect-segment-lengths)
| MissingRegistrationForLocation | Verifique o status de registro do provedor de recursos e os locais com suporte. | [Resolver registro](error-register-resource-provider.md) |
| MissingSubscriptionRegistration | Registre sua assinatura no provedor de recursos. | [Resolver registro](error-register-resource-provider.md) |
| NoRegisteredProviderFound | Verifique o status do registro do provedor de recursos. | [Resolver registro](error-register-resource-provider.md) |
| NotFound | Você pode estar tentando implantar um recurso dependente em paralelo com um recurso pai. Verifique se você precisa adicionar uma dependência. | [Resolver dependências](error-not-found.md) |
| OperationNotAllowed | A implantação está tentando uma operação que ultrapassa a cota da assinatura, do grupo de recursos ou da região. Se possível, revise sua implantação para permanecer dentro das cotas. Caso contrário, considere solicitar uma alteração de suas cotas. | [Resolver cotas](error-resource-quota.md) |
| ParentResourceNotFound | Verifique se há um recurso pai antes de criar os recursos filho. | [Resolver recurso pai](error-parent-resource.md) |
| PasswordTooLong | Você pode ter selecionado uma senha com muitos caracteres ou convertido o valor da senha em uma cadeia de caracteres segura antes de passá-lo como um parâmetro. Se o modelo inclui um parâmetro **cadeia de caracteres segura**, não é necessário converter o valor em uma cadeia de caracteres segura. Forneça o valor da senha como texto. |  |
| PrivateIPAddressInReservedRange | O endereço IP especificado contém um intervalo de endereços exigido pelo Azure. Altere o endereço IP para evitar o intervalo reservado. | [Endereços IP](../../virtual-network/public-ip-addresses.md) |
| PrivateIPAddressNotInSubnet | O endereço IP especificado está fora do intervalo da sub-rede. Altere o endereço IP para que ele fique dentro do intervalo de sub-rede. | [Endereços IP](../../virtual-network/public-ip-addresses.md) |
| PropertyChangeNotAllowed | Algumas propriedades não podem ser alteradas em um recurso implantado. Ao atualizar um recurso, limite suas alterações às propriedades permitidas. | [Atualizar recurso](/azure/architecture/guide/azure-resource-manager/advanced-templates/update-resource) |
| RequestDisallowedByPolicy | Sua assinatura inclui uma política de recursos que impede uma ação que você está tentando executar durante a implantação. Localize a política que bloqueia a ação. Se possível, altere a implantação para atender às limitações da política. | [Resolver políticas](error-policy-requestdisallowedbypolicy.md) |
| ReservedResourceName | Forneça um nome de recurso que não inclua um nome reservado. | [Nomes de recurso reservados](error-reserved-resource-name.md) |
| ResourceGroupBeingDeleted | Aguarde a conclusão da exclusão. | |
| ResourceGroupNotFound | Verifique o nome do grupo de recursos de destino para a implantação. O grupo de recursos de destino já deve existir em sua assinatura. Verifique o contexto de sua assinatura. | [CLI do Azure, ](/cli/azure/account?#az-account-set) [PowerShell](/powershell/module/Az.Accounts/Set-AzContext) |
| ResourceNotFound | Sua implantação referencia um recurso que não pode ser resolvido. Verifique se o uso da função de **referência** inclui os parâmetros necessários para seu cenário. | [Resolver referências](error-not-found.md) |
| ResourceQuotaExceeded | A implantação está tentando criar recursos que ultrapassam a cota da assinatura, do grupo de recursos ou da região. Se possível, revise sua infraestrutura para permanecer dentro das cotas. Caso contrário, considere solicitar uma alteração de suas cotas. | [Resolver cotas](error-resource-quota.md) |
| SkuNotAvailable | Selecione o SKU (por exemplo, o tamanho da VM) disponível para a localização que você selecionou. | [Resolver SKU](error-sku-not-available.md) |
| StorageAccountAlreadyExists | Forneça um nome exclusivo para a conta de armazenamento. | [Resolver o nome da conta de armazenamento](error-storage-account-name.md)  |
| StorageAccountAlreadyTaken | Forneça um nome exclusivo para a conta de armazenamento. | [Resolver o nome da conta de armazenamento](error-storage-account-name.md) |
| StorageAccountNotFound | Verifique a assinatura, o grupo de recursos e o nome da conta de armazenamento que você está tentando usar. | |
| SubnetsNotInSameVnet | Uma máquina virtual pode ter apenas uma rede virtual. Ao implantar várias NICs, certifique-se de que elas pertencem à mesma rede virtual. | [Várias NICs](../../virtual-machines/windows/multiple-nics.md) |
| SubscriptionNotFound | Uma assinatura especificada para implantação não pode ser acessada. Pode ser que a ID da assinatura esteja incorreta, o usuário que está implantando o modelo não tem as permissões adequadas para implantar na assinatura ou a ID da assinatura está no formato incorreto. Ao usar implantações aninhadas para [implantar entre escopos](./deploy-to-resource-group.md), forneça o GUID para a assinatura. | |
| SubscriptionNotRegistered | Ao implantar um recurso, o provedor de recursos deve ser registrado para sua assinatura. Quando você usa um modelo de Azure Resource Manager para implantação, o provedor de recursos é registrado automaticamente na assinatura. Às vezes, o registro automático não é concluído no tempo. Para evitar esse erro intermitente, registre o provedor de recursos antes da implantação. | [Resolver registro](error-register-resource-provider.md) |
| TemplateResourceCircularDependency | Remova as dependências desnecessárias. | [Resolver as dependências circulares](error-invalid-template.md#circular-dependency) |
| TooManyTargetResourceGroups | Reduza o número de grupos de recursos para uma única implantação. | [Implantação entre escopos](./deploy-to-resource-group.md) |

## <a name="find-error-code"></a>Encontrar código do erro

Há dois tipos de erros que você pode receber:

* erros de validação
* erros de implantação

Erros de validação ocorrem em cenários que podem ser determinados antes da implantação. Eles incluem erros de sintaxe no modelo ou a tentativa de implantar recursos que excederão suas cotas de assinatura. Os erros de implantação surgem de condições que ocorrem durante o processo de implantação. Eles incluem a tentativa de acessar um recurso que está sendo implantado em paralelo.

Ambos os tipos de erro retornam um código de erro que você pode usar para solucionar os problemas de implantação. Os dois tipos de erro aparecem no [log de atividades](../management/view-activity-logs.md). No entanto, os erros de validação não aparecem no seu histórico de implantações, porque a implantação nunca foi iniciada.

### <a name="validation-errors"></a>Erros de validação

Durante a implantação por meio do portal, você vê um erro de validação depois de enviar os valores.

![mostrar erro de validação no portal](./media/common-deployment-errors/validation-error.png)

Selecione a mensagem para obter mais detalhes. Na imagem a seguir, você vê um erro **InvalidTemplateDeployment** e uma mensagem que indica uma implantação bloqueada por política.

![mostrar detalhes da validação](./media/common-deployment-errors/validation-details.png)

### <a name="deployment-errors"></a>Erros de implantação

Quando a operação aprova a validação, mas falha durante a implantação, você recebe um erro de implantação.

Para ver os códigos de erro de implantação e as mensagens com o PowerShell, use:

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -DeploymentName exampledeployment -ResourceGroupName examplegroup).Properties.statusMessage
```

Para ver os códigos de erro de implantação e as mensagens com a CLI do Azure, use:

```azurecli-interactive
az deployment operation group list --name exampledeployment -g examplegroup --query "[*].properties.statusMessage"
```

No portal, selecione a notificação.

![erro de notificação](./media/common-deployment-errors/notification.png)

Você verá mais detalhes sobre a implantação. Selecione a opção para obter mais informações sobre o erro.

![falha na implantação](./media/common-deployment-errors/deployment-failed.png)

Você verá a mensagem de erro e os códigos de erro. Observe que há dois códigos de erro. O primeiro código de erro (**DeploymentFailed**) é um erro geral que não fornece os detalhes necessários para resolver o erro. O segundo código de erro (**StorageAccountNotFound**) fornece os detalhes necessários.

![detalhes do erro](./media/common-deployment-errors/error-details.png)

## <a name="enable-debug-logging"></a>Habilitar o log de depuração

Às vezes, você precisa obter mais informações sobre a solicitação e a resposta para descobrir o que deu errado. Durante a implantação, você pode solicitar que informações adicionais sejam registradas durante a implantação.

### <a name="powershell"></a>PowerShell

No PowerShell, defina o parâmetro **DeploymentDebugLogLevel** como All, ResponseContent ou RequestContent.

```powershell
New-AzResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName examplegroup `
  -TemplateFile c:\Azure\Templates\storage.json `
  -DeploymentDebugLogLevel All
```

Examine o conteúdo da solicitação com o seguinte cmdlet:

```powershell
(Get-AzResourceGroupDeploymentOperation `
-DeploymentName exampledeployment `
-ResourceGroupName examplegroup).Properties.request `
| ConvertTo-Json
```

Ou o conteúdo da resposta com:

```powershell
(Get-AzResourceGroupDeploymentOperation `
-DeploymentName exampledeployment `
-ResourceGroupName examplegroup).Properties.response `
| ConvertTo-Json
```

Essas informações podem ajudá-lo a determinar se um valor no modelo está sendo definido incorretamente.

### <a name="azure-cli"></a>CLI do Azure

Atualmente, CLI do Azure não dá suporte para ativar o log de depuração, mas você pode recuperar o log de depuração.

Examine as operações de implantação, com o comando a seguir:

```azurecli
az deployment operation group list \
  --resource-group examplegroup \
  --name exampledeployment
```

Examine o conteúdo da solicitação com o seguinte comando:

```azurecli
az deployment operation group list \
  --name exampledeployment \
  -g examplegroup \
  --query [].properties.request
```

Examine o conteúdo da resposta com o seguinte comando:

```azurecli
az deployment operation group list \
  --name exampledeployment \
  -g examplegroup \
  --query [].properties.response
```

### <a name="nested-template"></a>Modelo aninhado

Para registrar informações de depuração de um modelo aninhado, use o elemento **debugSetting**.

```json
{
  "type": "Microsoft.Resources/deployments",
  "apiVersion": "2020-10-01",
  "name": "nestedTemplate",
  "properties": {
    "mode": "Incremental",
    "templateLink": {
      "uri": "{template-uri}",
      "contentVersion": "1.0.0.0"
    },
    "debugSetting": {
       "detailLevel": "requestContent, responseContent"
    }
  }
}
```

## <a name="create-a-troubleshooting-template"></a>Criar um modelo de solução de problemas

Em alguns casos, a maneira mais fácil de solucionar problemas do seu modelo é testar partes dele. Você pode criar um modelo simplificado que permite a você se concentrar na parte que acredita estar causando o erro. Por exemplo, suponha que você esteja recebendo um erro ao referenciar um recurso. Em vez de lidar com um modelo inteiro, crie um modelo que retorne a parte que pode estar causando o problema. Ele pode ajudá-lo a determinar se você está transmitindo os parâmetros certos, usando funções de modelo corretamente e obtendo os recursos desejados.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
  "storageName": {
    "type": "string"
  },
  "storageResourceGroup": {
    "type": "string"
  }
  },
  "variables": {},
  "resources": [],
  "outputs": {
  "exampleOutput": {
    "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageName')), '2016-05-01')]",
    "type" : "object"
  }
  }
}
```

Ou, suponha que você esteja obtendo erros de implantação que você acredita que estão relacionados a dependências definidas incorretamente. Teste seu modelo dividindo-o em modelos simplificados. Primeiro, crie um modelo que implanta um único recurso (como um SQL Server). Quando você tiver certeza de que esse recurso está definido corretamente, adicione um recurso que dependa dele (como um banco de dados SQL). Quando esses dois recursos estiverem definidos corretamente, adicione outros recursos dependentes (como políticas de auditoria). Entre cada implantação de teste, exclua o grupo de recursos para garantir o teste adequado das dependências.

## <a name="next-steps"></a>Próximas etapas

* Para percorrer um tutorial de solução de problemas, consulte [tutorial: solucionar problemas de implantações de modelo do Resource Manager](template-tutorial-troubleshoot.md)
* Para saber sobre as ações de auditoria, consulte [Auditar operações com o Gerenciador de Recursos](../management/view-activity-logs.md).
* Para saber sobre as ações para determinar os erros durante a implantação, consulte [Exibir operações de implantação](deployment-history.md).