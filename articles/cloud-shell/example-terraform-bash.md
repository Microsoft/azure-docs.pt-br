---
title: Implantar com Terraform de Azure Cloud Shell | Microsoft Docs
description: Implantar com Terraform de Azure Cloud Shell
services: Azure
documentationcenter: ''
author: tomarchermsft
manager: routlaw
tags: azure-cloud-shell
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: tarcher
ms.custom: devx-track-terraform
ms.openlocfilehash: e13e6d9ac2f4600e41f221efd23997f712dffc54
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92745441"
---
# <a name="deploy-with-terraform-from-bash-in-azure-cloud-shell"></a>Implantar com Terraform do Bash no Azure Cloud Shell
Este artigo mostra as etapas para criar um grupo de recursos com o [provedor do Terraform do AzureRM](https://www.terraform.io/docs/providers/azurerm/index.html).

O [Hashicorp Terraform](https://www.terraform.io/) é uma ferramenta de software livre que codifica APIs em arquivos de configuração declarativa que podem ser compartilhados entre os membros da equipe para ser editado, revisado e ter controle de versão. O provedor do Microsoft AzureRM é usado para interagir com os recursos com suporte pelo Azure Resource Manager por meio das APIs do AzureRM.

## <a name="automatic-authentication"></a>Autenticação automática
O Terraform é instalado em Bash no Cloud Shell por padrão. Além disso, o Cloud Shell autentica automaticamente sua assinatura da CLI do Azure padrão para implantar recursos por meio dos módulos do Terraform Azure.

Terraform usa a assinatura de CLI padrão do Azure que está definida. Para atualizar assinaturas padrão, execute:

```azurecli-interactive
az account set --subscription mySubscriptionName
```

## <a name="walkthrough"></a>Passo a passo
### <a name="launch-bash-in-cloud-shell"></a>Inicie o Bash no Cloud Shell
1. Inicie o Cloud Shell do seu local preferencial
2. Verifique se sua assinatura preferencial está definida

```azurecli-interactive
az account show
```

### <a name="create-a-terraform-template"></a>Crie um modelo do Terraform
Crie um novo modelo do Terraform nomeado main.tf com seu editor de texto preferido.

```
vim main.tf
```

Copie/cole o código a seguir no Cloud Shell.

```
resource "azurerm_resource_group" "myterraformgroup" {
    name = "myRgName"
    location = "West US"
}
```

Salve o arquivo e saia do editor de texto.

### <a name="terraform-init"></a>Terraform init
Comece executando o `terraform init`.

```
justin@Azure:~$ terraform init

Initializing provider plugins...

The following providers do not have any version constraints in configuration,
so the latest version was installed.

To prevent automatic upgrades to new major versions that may contain breaking
changes, it is recommended to add version = "..." constraints to the
corresponding provider blocks in configuration, with the constraint strings
suggested below.

* provider.azurerm: version = "~> 0.2"

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.
```

O [comando terraform init](https://www.terraform.io/docs/commands/init.html) é usado para inicializar um diretório de trabalho contendo os arquivos de configuração do Terraform. O comando `terraform init` é o primeiro comando que deve ser executado depois de gravar uma nova configuração do Terraform ou de clonar uma existente do controle de versão. É seguro executar esse comando várias vezes.

### <a name="terraform-plan"></a>Plano do Terraform
Visualize os recursos a serem criados pelo modelo do Terraform com `terraform plan`.

```
justin@Azure:~$ terraform plan
Refreshing Terraform state in-memory prior to plan...
The refreshed state will be used to calculate this plan, but will not be
persisted to local or remote state storage.


------------------------------------------------------------------------

An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  + azurerm_resource_group.demo
      id:       <computed>
      location: "westus"
      name:     "myRGName"
      tags.%:   <computed>


Plan: 1 to add, 0 to change, 0 to destroy.

------------------------------------------------------------------------

Note: You didn't specify an "-out" parameter to save this plan, so Terraform
can't guarantee that exactly these actions will be performed if
"terraform apply" is subsequently run.
```

O [comando de plano do terraform](https://www.terraform.io/docs/commands/plan.html) é usado para criar um plano de execução. O Terraform executa uma atualização, a menos que isso esteja explicitamente desabilitado, e depois determina quais ações são necessárias para obter o estado desejado especificado nos arquivos de configuração. O plano pode ser salvo usando -out e depois fornecido ao terraform apply para garantir que apenas as ações previamente planejadas sejam executadas.

### <a name="terraform-apply"></a>Terraform apply
Provisionar os recursos do Azure com `terraform apply`.

```
justin@Azure:~$ terraform apply
azurerm_resource_group.demo: Creating...
  location: "" => "westus"
  name:     "" => "myRGName"
  tags.%:   "" => "<computed>"
azurerm_resource_group.demo: Creation complete after 0s (ID: /subscriptions/mySubIDmysub/resourceGroups/myRGName)

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

O [comando terraform apply](https://www.terraform.io/docs/commands/apply.html) é usado para aplicar as alterações necessárias para alcançar o estado desejado da configuração.

### <a name="verify-deployment-with-azure-cli"></a>Verificar a implantação com a CLI do Azure
Execute `az group show -n myRgName` para verificar se o recurso foi provisionado com êxito.

```azurecli-interactive
az group show -n myRgName
```

### <a name="clean-up-with-terraform-destroy"></a>Limpar com o terraform destroy
Limpe o grupo de recursos criado com o [comando Terraform destroy](https://www.terraform.io/docs/commands/destroy.html) para limpar a infraestrutura criada do Terraform.

```
justin@Azure:~$ terraform destroy
azurerm_resource_group.demo: Refreshing state... (ID: /subscriptions/mySubID/resourceGroups/myRGName)

An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  - destroy

Terraform will perform the following actions:

  - azurerm_resource_group.demo


Plan: 0 to add, 0 to change, 1 to destroy.

Do you really want to destroy?
  Terraform will destroy all your managed infrastructure, as shown above.
  There is no undo. Only 'yes' will be accepted to confirm.

  Enter a value: yes

azurerm_resource_group.demo: Destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName)
azurerm_resource_group.demo: Still destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName, 10s elapsed)
azurerm_resource_group.demo: Still destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName, 20s elapsed)
azurerm_resource_group.demo: Still destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName, 30s elapsed)
azurerm_resource_group.demo: Still destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName, 40s elapsed)
azurerm_resource_group.demo: Destruction complete after 45s

Destroy complete! Resources: 1 destroyed.
```

Você criou um recurso do Azure com êxito por meio do Terraform. Visite as próximas etapas para continuar a saber mais sobre o Cloud Shell.

## <a name="next-steps"></a>Próximas etapas
[Saiba mais sobre o provedor do Terraform do Azure](https://www.terraform.io/docs/providers/azurerm/#)<br>
[Início rápido do Bash no Cloud Shell](quickstart.md)
