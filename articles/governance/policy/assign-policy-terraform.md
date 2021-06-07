---
title: 'Início Rápido: Nova atribuição de política com o Terraform'
description: Neste início rápido, você usará o Terraform e a sintaxe HCL para criar uma atribuição de política para identificar recursos que não estão em conformidade.
ms.date: 03/31/2021
ms.topic: quickstart
ms.openlocfilehash: 6c1a43010c48f71872d935a8fb52f608b479da8a
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106092329"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-terraform"></a>Início Rápido: Criar uma atribuição de política para identificar um recurso que não está em conformidade usando o Terraform

A primeira etapa para compreender a conformidade no Azure é identificar o status de seus recursos.
Este guia de início rápido orienta você no processo de criação de uma atribuição de política para identificar máquinas virtuais que não estão usando discos gerenciados.

No final deste processo, você identificará com êxito quais máquinas virtuais não estão usando discos gerenciados. Eles _não estão em conformidade_ com a atribuição da política.

## <a name="prerequisites"></a>Pré-requisitos

- Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.
- [Terraform](https://www.terraform.io/) versão 0.12.0 ou superior configurado em seu ambiente.
  Para obter instruções, confira [Configurar o Terraform usando o Azure Cloud Shell](/azure/developer/terraform/get-started-cloud-shell).
- Este guia de início rápido exige que você execute a CLI do Azure versão 2.13.0 ou posterior. Para saber qual é a versão, execute `az --version`. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="create-the-terraform-configuration-variable-and-output-file"></a>Criar a configuração, a variável e o arquivo de saída do Terraform

Neste guia de início rápido, você cria uma atribuição de política e atribui a definição **Auditar VMs que não usam discos gerenciados** (`06a78e20-9358-41c9-923c-fb736d382a4d`). Esta definição de política identifica recursos que não estão em conformidade com as condições configuradas na definição de política.

Primeiro, configure a configuração, a variável e os arquivos de saída do Terraform. Os recursos do Terraform para Azure Policy usam o [Provedor do Azure](https://www.terraform.io/docs/providers/azurerm/index.html).

1. Crie uma pasta chamada `policy-assignment` e altere os diretórios nela.

1. Crie `main.tf` com o seguinte código:

   ```hcl
   provider "azurerm" {
       version = "~>2.0"
       features {}
   }
   
   resource "azurerm_policy_assignment" "auditvms" {
       name = "audit-vm-manageddisks"
       scope = var.cust_scope
       policy_definition_id = "/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d"
       description = "Shows all virtual machines not using managed disks"
       display_name = "Audit VMs without managed disks Assignment"
   }
   ```
1. Crie `variables.tf` com o seguinte código:

   ```hcl
   variable "cust_scope" {
       default = "{scope}"
   }
   ```

   Um escopo determina em quais recursos ou agrupamento de recursos a atribuição de política é imposta. Ele pode variar de um grupo de gerenciamento a um recurso individual. Substitua `{scope}` por um dos seguintes padrões:

   - Assinatura: `/subscriptions/{subscriptionId}`
   - Grupo de recursos: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`
   - Recurso: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/[{parentResourcePath}/]`

1. Crie `output.tf` com o seguinte código:

   ```hcl
   output "assignment_id" {
       value = azurerm_policy_assignment.auditvms.id
   }
   ```

## <a name="initialize-terraform-and-create-plan"></a>Inicializar o Terraform e criar o plano

Em seguida, inicialize o Terraform para baixar os provedores necessários e, em seguida, crie um plano.

1. Executar o comando [terraform init](https://www.terraform.io/docs/commands/init.html). Esse comando baixa os módulos do Azure necessários para criar os recursos do Azure na configuração do Terraform.

   ```bash
   terraform init
   ```

   :::image type="content" source="./media/assign-policy-terraform/terraform-initialize.png" alt-text="Captura de tela da execução do comando terraform init que mostra o download do módulo azurerm e uma mensagem de êxito.":::

1. Faça a autenticação com [CLI do Azure](/cli/azure/) para o Terraform. Para obter mais informações, confira o [Provedor do Azure: fazer a autenticação usando a CLI do Azure](https://www.terraform.io/docs/providers/azurerm/guides/azure_cli.html).

   ```bash
   az login
   ```

1. Crie o plano de execução com o comando [terraform plan](https://www.terraform.io/docs/commands/plan.html) e o parâmetro **out**.

   ```bash
   terraform plan -out assignment.tfplan
   ```

   :::image type="content" source="./media/assign-policy-terraform/terraform-plan-out.png" alt-text="Captura de tela da execução do comando terraform plan e do parâmetro out para mostrar o recurso do Azure que seria criado.":::

   > [!NOTE]
   > Para obter informações sobre como persistir planos de execução e segurança, confira [Plano do Terraform: Aviso de Segurança](https://www.terraform.io/docs/commands/plan.html#security-warning).

## <a name="apply-the-terraform-execution-plan"></a>Aplicar o plano de execução Terraform

Por fim, aplique o plano de execução.

Execute o comando [terraform apply](https://www.terraform.io/docs/commands/apply.html) e especifique o `assignment.tfplan` já criado.

```bash
terraform apply assignment.tfplan
```

:::image type="content" source="./media/assign-policy-terraform/terraform-apply.png" alt-text="Captura de tela da execução do comando terraform apply e a criação do recurso resultante.":::

Com a mensagem "Aplicação concluída! Recursos: 1 adicionado, 0 alterado, 0 destruído.", a atribuição de política agora é criada. Como definimos o arquivo `outputs.tf`, a _ID\_de atribuição_ também foi retornada.

## <a name="identify-non-compliant-resources"></a>Identificar recursos fora de conformidade

Para exibir os recursos que não estão em conformidade nessa nova atribuição, use a _ID\_de atribuição_ retornada por `terraform apply`. Com ela, execute o seguinte comando para obter as IDs de recurso dos recursos que não estão em conformidade produzidos em um arquivo JSON:

```console
armclient post "/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$filter=IsCompliant eq false and PolicyAssignmentId eq '<policyAssignmentID>'&$apply=groupby((ResourceId))" > <json file to direct the output with the resource IDs into>
```

Seus resultados devem se parecer com o exemplo a seguir:

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
    "@odata.count": 3,
    "value": [{
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachineId>"
        },
        {
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine2Id>"
        },
        {
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionName>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine3ID>"
        }

    ]
}
```

Os resultados são comparáveis aos que você geralmente vê listados em **Recursos não compatível** na exibição do Portal do Azure.

## <a name="clean-up-resources"></a>Limpar os recursos

Para remover a atribuição criada, use a CLI do Azure ou inverta o plano de execução do Terraform com `terraform destroy`.

- CLI do Azure

  ```azurecli-interactive
  az policy assignment delete --name 'audit-vm-manageddisks' --scope '/subscriptions/<subscriptionID>/<resourceGroupName>'
  ```

- Terraform

  ```bash
  terraform destroy assignment.tfplan
  ```

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você atribuiu uma definição de política para identificar recursos sem conformidade em seu ambiente do Azure.

Para saber mais sobre a atribuição de políticas para validar que novos recursos estejam em conformidade, continue com o tutorial para:

> [!div class="nextstepaction"]
> [Criando e gerenciando políticas](./tutorials/create-and-manage.md)
