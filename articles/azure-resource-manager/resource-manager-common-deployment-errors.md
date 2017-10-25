---
title: "Solução de erros comuns de implantação do Azure | Microsoft Docs"
description: Descreve como resolver erros comuns ao implantar recursos no Azure usando o Azure Resource Manager.
services: azure-resource-manager
documentationcenter: 
tags: top-support-issue
author: tfitzmac
manager: timlt
editor: tysonn
keywords: "erro de implantação, implantação do azure, implante no azure"
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: support-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/14/2017
ms.author: tomfitz
ms.openlocfilehash: d6dc0ffac1a3e688f93af903f51f798270b63711
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-resource-manager"></a>Solução de erros comuns de implantação do Azure com o Azure Resource Manager

Este tópico descreve alguns erros comuns de implantação do Azure com que você pode se deparar e fornece informações para solucionar esses erros. Se não encontrar o código de erro para ser erro de implantação, consulte [Localizar código de erro](#find-error-code).

## <a name="error-codes"></a>Códigos do Erro

| Código do erro | Redução | Mais informações |
| ---------- | ---------- | ---------------- |
| AccountNameInvalid | Siga as restrições de nomenclatura para as contas de armazenamento. | [Resolver o nome da conta de armazenamento](resource-manager-storage-account-name-errors.md) |
| AccountPropertyCannotBeSet | Verifique as propriedades da conta de armazenamento disponível. | [storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |
| AnotherOperationInProgress | Aguarde a conclusão da operação simultânea. | |
| AuthorizationFailed | Sua conta ou entidade de serviço não tem acesso suficiente para concluir a implantação. Verifique a função a que sua conta pertence e seu acesso para o escopo da implantação. | [Controle de Acesso Baseado em Função do Azure](../active-directory/role-based-access-control-configure.md) |
| BadRequest | Você enviou valores de implantação que não coincidem com o que é esperado pelo Resource Manager. Verifique a mensagem de status interna para obter ajuda com a solução de problemas. | [Referência de modelos](/azure/templates/) e [Locais com suporte](resource-manager-template-location.md) |
| Conflito | Você está solicitando uma operação que não é permitida no estado atual do recurso. Por exemplo, o redimensionamento do disco é permitido apenas ao criar uma VM ou quando a VM é desalocada. | |
| DeploymentActive | Aguarde a conclusão da implantação simultânea nesse grupo de recursos. | |
| DnsRecordInUse | O nome do registro DNS deve ser exclusivo. Forneça um nome diferente ou modifique o registro existente. | |
| ImageNotFound | Verifique as configurações de imagem da VM. | [Solucionar problemas de imagens Linux](../virtual-machines/linux/troubleshoot-deployment-new-vm.md) e [Solucionar problemas imagens do Windows](../virtual-machines/windows/troubleshoot-deployment-new-vm.md) |
| InUseSubnetCannotBeDeleted | Você pode encontrar este erro quando tenta atualizar um recurso, mas a solicitação é processada excluindo e criando o recurso. Certifique-se de especificar todos os valores inalterados. | [Atualizar recurso](/azure/architecture/building-blocks/extending-templates/update-resource) |
| InvalidAuthenticationTokenTenant | Obtenha o token de acesso do locatário correto. Você só pode obter o token do locatário a que sua conta pertence. | |
| InvalidContentLink | Provavelmente você tentou vincular a um modelo aninhado que não está disponível. Verifique uma segunda vez o URI que você forneceu para o modelo aninhado. Caso o modelo exista em uma conta de armazenamento, verifique se o URI está acessível. Pode ser necessário passar um token SAS. | [Modelos vinculados](resource-group-linked-templates.md) |
| InvalidParameter | Um dos valores que você forneceu para um recurso não corresponde ao valor esperado. Esse erro pode ser decorrente de várias condições diferentes. Por exemplo, uma senha pode ser insuficiente ou um nome de blob pode estar incorreto. Verifique a mensagem de erro para determinar qual valor precisa ser corrigido. | |
| InvalidRequestContent | Os valores da implantação incluem valores que não são esperados ou valores necessários estão ausentes. Confirme se os valores para seu tipo de recurso. | [Referência de modelo](/azure/templates/) |
| InvalidRequestFormat | Habilitar o log da depuração ao executar a implantação e verifique o conteúdo da solicitação. | [Log de depuração](resource-manager-troubleshoot-tips.md#enable-debug-logging) |
| InvalidResourceNamespace | Verifique o namespace do recurso especificado na propriedade **type**. | [Referência de modelo](/azure/templates/) |
| InvalidResourceReference | O recurso ainda não existe ou foi referenciado incorretamente. Verifique se você precisa adicionar uma dependência. Verifique se o uso da função **referência** inclui os parâmetros necessários para seu cenário. | [Resolver dependências](resource-manager-not-found-errors.md) |
| InvalidResourceType | Verifique o tipo de recurso do recurso especificado na propriedade **type**. | [Referência de modelo](/azure/templates/) |
| InvalidTemplate | Verifique se há erros na sintaxe do modelo. | [Resolver modelo inválido](resource-manager-invalid-template-errors.md) |
| LinkedAuthorizationFailed | Verifique se sua conta pertence ao mesmo locatário que o grupo de recursos em que você está implantando. | |
| LinkedInvalidPropertyId | A ID de um recurso não está sendo resolvida corretamente. Verifique se você forneceu todos os valores necessários para a ID do recurso, incluindo a ID de assinatura, nome do grupo de recursos, o tipo de recurso, o nome do recurso pai (se necessário) e o nome de recurso. | |
| LocationRequired | Forneça um local para o recurso. | [Definir local](resource-manager-template-location.md) |
| MissingRegistrationForLocation | Verifique o status de registro do provedor de recursos e os locais com suporte. | [Resolver registro](resource-manager-register-provider-errors.md) |
| MissingSubscriptionRegistration | Registre sua assinatura no provedor de recursos. | [Resolver registro](resource-manager-register-provider-errors.md) |
| NoRegisteredProviderFound | Verifique o status do registro do provedor de recursos. | [Resolver registro](resource-manager-register-provider-errors.md) |
| NotFound | Você pode estar tentando implantar um recurso dependente em paralelo com o recurso pai. Verifique se você precisa adicionar uma dependência. | [Resolver dependências](resource-manager-not-found-errors.md) |
| OperationNotAllowed | A implantação está tentando uma operação que ultrapassa a cota da assinatura, do grupo de recursos ou da região. Se possível, revise sua implantação para permanecer dentro das cotas. Caso contrário, considere solicitar uma alteração de suas cotas. | [Resolver cotas](resource-manager-quota-errors.md) |
| ParentResourceNotFound | Verifique se há um recurso pai antes de criar os recursos filho. | [Resolver recurso pai](resource-manager-parent-resource-errors.md) |
| PrivateIPAddressInReservedRange | O endereço IP especificado contém um intervalo de endereços exigido pelo Azure. Altere o endereço IP para evitar o intervalo reservado. | [Endereços IP](../virtual-network/virtual-network-ip-addresses-overview-arm.md) |
| PrivateIPAddressNotInSubnet | O endereço IP especificado está fora do intervalo da sub-rede. Altere o endereço IP para que ele fique dentro do intervalo de sub-rede. | [Endereços IP](../virtual-network/virtual-network-ip-addresses-overview-arm.md) |
| PropertyChangeNotAllowed | Algumas propriedades não podem ser alteradas em um recurso implantado. Ao atualizar um recurso, limite suas alterações às propriedades permitidas. | [Atualizar recurso](/azure/architecture/building-blocks/extending-templates/update-resource) |
| RequestDisallowedByPolicy | Sua assinatura inclui uma política de recursos que impede uma ação que você está tentando executar durante a implantação. Localize a política que bloqueia a ação. Se possível, modifique sua implantação para atender às limitações da política. | [Resolver políticas](resource-manager-policy-requestdisallowedbypolicy-error.md) |
| ResourceGroupBeingDeleted | Aguarde a conclusão da exclusão. | |
| ResourceGroupNotFound | Verifique o nome do grupo de recursos de destino para a implantação. Ele já deve existir em sua assinatura. Verifique o contexto de sua assinatura. | [CLI do Azure](/cli/azure/account?#az_account_set), [PowerShell](/powershell/module/azurerm.profile/set-azurermcontext) |
| ResourceNotFound | Sua implantação faz referência a um recurso que não pode ser resolvido. Verifique se o uso da função de **referência** inclui os parâmetros necessários para seu cenário. | [Resolver referências](resource-manager-not-found-errors.md) |
| ResourceQuotaExceeded | A implantação está tentando criar recursos que ultrapassam a cota da assinatura, do grupo de recursos ou da região. Se possível, revise sua infraestrutura para permanecer dentro das cotas. Caso contrário, considere solicitar uma alteração de suas cotas. | [Resolver cotas](resource-manager-quota-errors.md) |
| SkuNotAvailable | Selecione a SKU (por exemplo, o tamanho da VM) que está disponível para o local que você selecionou. | [Resolver SKU](resource-manager-sku-not-available-errors.md) |
| StorageAccountAlreadyExists | Forneça um nome exclusivo para a conta de armazenamento. | [Resolver o nome da conta de armazenamento](resource-manager-storage-account-name-errors.md)  |
| StorageAccountAlreadyTaken | Forneça um nome exclusivo para a conta de armazenamento. | [Resolver o nome da conta de armazenamento](resource-manager-storage-account-name-errors.md) |
| StorageAccountNotFound | Verifique a assinatura, o grupo de recursos e o nome da conta de armazenamento que você está tentando usar. | |
| SubnetsNotInSameVnet | Uma máquina virtual pode ter apenas uma rede virtual. Ao implantar várias NICs, certifique-se de que elas pertencem à mesma rede virtual. | [Várias NICs](../virtual-machines/windows/multiple-nics.md) |

## <a name="find-error-code"></a>Encontrar código do erro

Quando você encontra um erro durante a implantação, o Resource Manager retorna um código de erro. Você pode ver a mensagem de erro por meio do portal, do PowerShell ou da CLI do Azure. A mensagem de erro externa pode ser muito geral para fins de solução de problemas. Procure a mensagem interna que contém informações detalhadas sobre o erro. Para obter mais informações, consulte [Determinar código de erro](resource-manager-troubleshoot-tips.md#determine-error-code).

## <a name="next-steps"></a>Próximas etapas
* Para saber sobre as ações de auditoria, consulte [Auditar operações com o Gerenciador de Recursos](resource-group-audit.md).
* Para saber sobre as ações para determinar os erros durante a implantação, consulte [Exibir operações de implantação](resource-manager-deployment-operations.md).
