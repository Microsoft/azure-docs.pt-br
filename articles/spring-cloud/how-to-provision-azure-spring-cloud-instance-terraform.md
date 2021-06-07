---
title: Provisionar uma instância do Azure Spring Cloud com o Terraform
description: Provisionar uma instância do Azure Spring Cloud com o Terraform.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 06/26/2020
ms.custom: devx-track-java
ms.openlocfilehash: 060ef2d08b849706b47b24748142c608292971b5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104877754"
---
# <a name="provision-an-azure-spring-cloud-instance-with-terraform"></a>Provisionar uma instância do Azure Spring Cloud com o Terraform

**Este artigo aplica-se a:** ✔️ Java ✔️ C#

Este exemplo cria uma instância do Azure Spring Cloud usando o Terraform. Os procedimentos orientam você pela criação dos seguintes recursos:

> [!div class="checklist"]
> * Grupo de recursos
> * Instância do Azure Spring Cloud
> * Armazenamento do Azure para Log Analytics

> [!NOTE]
> Para obter suporte específico do Terraform, use um dos canais de suporte da comunidade da HashiCorp para o Terraform:
>
> * Perguntas, casos de uso e padrões úteis: [Seção do portal da comunidade da HashiCorp sobre o Terraform](https://discuss.hashicorp.com/c/terraform-core)
> * Perguntas relacionadas aos provedores: [Seção do portal da comunidade da HashiCorp sobre os provedores do Terraform](https://discuss.hashicorp.com/c/terraform-providers)

## <a name="prerequisites"></a>Pré-requisitos

- **Assinatura do Azure**: Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.

## <a name="create-configuration-file"></a>Criar arquivo de configuração

1. Entre no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Abra o [Azure Cloud Shell](../app-service/quickstart-java.md#use-azure-cloud-shell).

1. Inicie o editor do Cloud Shell:

    ```bash
    code main.tf
    ```

1. A configuração nesta etapa modela os recursos do Azure, incluindo um grupo de recursos do Azure e uma instância do Azure Spring Cloud.

    ```hcl
    provider "azurerm" {
        features {}
    }

    resource "azurerm_resource_group" "example" {
      name     = "username"
      location = "eastus"
    }

    resource "azurerm_spring_cloud_service" "example" {
      name                = "usernamesp"
      resource_group_name = azurerm_resource_group.example.name
      location            = azurerm_resource_group.example.location

      config_server_git_setting {
        uri          = "https://github.com/Azure-Samples/piggymetrics-config"
        label        = "master"
        search_paths = ["."]
      }
    }
    ```

1. Salve o arquivo ( **&lt;Ctrl>S**) e saia do editor ( **&lt;Ctrl>Q**).

## <a name="apply-the-configuration"></a>Aplicar a configuração

Nesta seção, use vários comandos do Terraform para executar a configuração.

1. O comando [terraform init](https://www.terraform.io/docs/commands/init.html) inicializa o diretório de trabalho. Execute o seguinte comando no Cloud Shell:

    ```bash
    terraform init
    ```

1. O comando [terraform plan](https://www.terraform.io/docs/commands/plan.html) é usado para validar a sintaxe de configuração. O parâmetro `-out` direciona os resultados para um arquivo. O arquivo de saída pode ser usado posteriormente para aplicar a configuração. Execute o seguinte comando no Cloud Shell:

    ```bash
    terraform plan --out plan.out
    ```

1. O comando [terraform apply](https://www.terraform.io/docs/commands/apply.html) é usado para aplicar a configuração. O comando especifica o arquivo de saída da etapa anterior. Este comando cria os recursos do Azure. Execute o seguinte comando no Cloud Shell:

    ```bash
    terraform apply plan.out
    ```

1. Para verificar os resultados dentro do portal do Azure, navegue até o novo grupo de recursos. A nova instância do **Azure Spring Cloud** é exibida no novo grupo de recursos.

## <a name="update-configuration-to-config-logs-and-metrics"></a>Atualizar a configuração para logs e métricas de configuração

Esta seção mostra como atualizar a configuração para habilitar o log e as métricas do Azure Spring Cloud com uma conta de Armazenamento do Azure.

1. Inicie o editor do Cloud Shell:

    ```bash
    code main.tf
    ```

1. Adicione a seguinte configuração ao final do arquivo:

    ```hcl
    resource "azurerm_storage_account" "example" {
      name                     = "usernamest"
      resource_group_name      = azurerm_resource_group.example.name
      location                 = azurerm_resource_group.example.location
      account_tier             = "Standard"
      account_replication_type = "GRS"
    }

    resource "azurerm_monitor_diagnostic_setting" "example" {
      name               = "example"
      target_resource_id = "${azurerm_spring_cloud_service.example.id}"
      storage_account_id = "${azurerm_storage_account.example.id}"

      log {
        category = "SystemLogs"
        enabled  = true

        retention_policy {
          enabled = false
        }
      }

      metric {
        category = "AllMetrics"

        retention_policy {
          enabled = false
        }
      }
    }
    ```

1. Salve o arquivo ( **&lt;Ctrl>S**) e saia do editor ( **&lt;Ctrl>Q**).

1. Como na seção anterior, execute o seguinte comando para fazer as alterações:

    ```bash
    terraform plan --out plan.out
    ```

1. Execute o comando `terraform apply` para aplicar a configuração.

    ```bash
    terraform apply plan.out
    ```

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não forem mais necessários, exclua os recursos criados neste artigo.

Execute o comando [terraform destroy](https://www.terraform.io/docs/commands/destroy.html) para remover os recursos do Azure criados neste exercício:

```bash
terraform destroy -auto-approve
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Instalar e configurar o Terraform para provisionar recursos do Azure](/azure/developer/terraform/getting-started-cloud-shell).