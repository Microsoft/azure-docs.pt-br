---
title: Configurar o ambiente de desenvolvimento para scripts de implantação em modelos | Microsoft Docs
description: Configure o ambiente de desenvolvimento para scripts de implantação em modelos de Azure Resource Manager (modelos ARM).
services: azure-resource-manager
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 12/14/2020
ms.author: jgao
ms.openlocfilehash: b2e1ffb3cbd513766945864e33589c46284bf942
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102200930"
---
# <a name="configure-development-environment-for-deployment-scripts-in-arm-templates"></a>Configurar o ambiente de desenvolvimento para scripts de implantação em modelos ARM

Saiba como criar um ambiente de desenvolvimento para desenvolver e testar scripts de implantação de modelo ARM com uma imagem de script de implantação. Você pode criar uma [instância de contêiner do Azure](../../container-instances/container-instances-overview.md) ou usar o [Docker](https://docs.docker.com/get-docker/). As duas opções são abordadas neste artigo.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="azure-powershell-container"></a>Contêiner de Azure PowerShell

Se você não tiver um script de implantação Azure PowerShell, poderá criar um arquivo de *hello.ps1* usando o seguinte conteúdo:

```powershell
param([string] $name)
$output = 'Hello {0}' -f $name
Write-Output $output
$DeploymentScriptOutputs = @{}
$DeploymentScriptOutputs['text'] = $output
```

### <a name="azure-cli-container"></a>Contêiner de CLI do Azure

Para uma imagem de contêiner CLI do Azure, você pode criar um arquivo *Hello.sh* usando o seguinte conteúdo:

```bash
firstname=$1
lastname=$2
output="{\"name\":{\"displayName\":\"$firstname $lastname\",\"firstName\":\"$firstname\",\"lastName\":\"$lastname\"}}"
echo -n "Hello "
echo $output | jq -r '.name.displayName'
```

> [!NOTE]
> Quando você executa um script de implantação CLI do Azure, uma variável de ambiente chamada `AZ_SCRIPTS_OUTPUT_PATH` armazena o local do arquivo de saída de script. A variável de ambiente não está disponível no contêiner do ambiente de desenvolvimento. Para obter mais informações sobre como trabalhar com CLI do Azure saídas, consulte [trabalhar com saídas do script da CLI](deployment-script-template.md#work-with-outputs-from-cli-script).

## <a name="use-azure-powershell-container-instance"></a>Usar Azure PowerShell instância de contêiner

Para criar seus scripts em seu computador, é necessário que você crie uma conta de armazenamento e monte a conta de armazenamento na instância do contêiner. Para que você possa carregar o script na conta de armazenamento e executar o script na instância do contêiner.

> [!NOTE]
> A conta de armazenamento que você cria para testar seu script não é a mesma conta de armazenamento que o serviço de script de implantação usa para executar o script. O serviço de script de implantação cria um nome exclusivo como um compartilhamento de arquivos em cada execução.

### <a name="create-an-azure-powershell-container-instance"></a>Criar uma instância de contêiner de Azure PowerShell

O modelo de Azure Resource Manager a seguir (modelo ARM) cria uma instância de contêiner e um compartilhamento de arquivos e, em seguida, monta o compartilhamento de arquivos para a imagem de contêiner.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "projectName": {
      "type": "string",
      "metadata": {
        "description": "Specify a project name that is used for generating resource names."
      }
    },
    "containerImage": {
      "type": "string",
      "defaultValue": "mcr.microsoft.com/azuredeploymentscripts-powershell:az5.2",
      "metadata": {
        "description": "Specify the container image."
      }
    },
    "mountPath": {
      "type": "string",
      "defaultValue": "/mnt/azscripts/azscriptinput",
      "metadata": {
        "description": "Specify the mount path."
      }
    }
  },
  "variables": {
    "storageAccountName": "[tolower(concat(parameters('projectName'), 'store'))]",
    "fileShareName": "[concat(parameters('projectName'), 'share')]",
    "containerGroupName": "[concat(parameters('projectName'), 'cg')]",
    "containerName": "[concat(parameters('projectName'), 'container')]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "name": "[variables('storageAccountName')]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
      },
      "kind": "StorageV2",
      "properties": {
        "accessTier": "Hot"
      }
    },
    {
      "type": "Microsoft.Storage/storageAccounts/fileServices/shares",
      "apiVersion": "2019-06-01",
      "name": "[concat(variables('storageAccountName'), '/default/', variables('fileShareName'))]",
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
      ]
    },
    {
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2019-12-01",
      "name": "[variables('containerGroupName')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
      ],
      "properties": {
        "containers": [
          {
            "name": "[variables('containerName')]",
            "properties": {
              "image": "[parameters('containerImage')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "protocol": "TCP",
                  "port": 80
                }
              ],
              "volumeMounts": [
                {
                  "name": "filesharevolume",
                  "mountPath": "[parameters('mountPath')]"
                }
              ],
              "command": [
                "/bin/sh",
                "-c",
                "pwsh -c 'Start-Sleep -Seconds 1800'"
              ]
            }
          }
        ],
        "osType": "Linux",
        "volumes": [
          {
            "name": "filesharevolume",
            "azureFile": {
              "readOnly": false,
              "shareName": "[variables('fileShareName')]",
              "storageAccountName": "[variables('storageAccountName')]",
              "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').keys[0].value]"
            }
          }
        ]
      }
    }
  ]
}
```

O valor padrão para o caminho de montagem é `/mnt/azscripts/azscriptinput` . Esse é o caminho na instância de contêiner onde ele é montado no compartilhamento de arquivos.

A imagem de contêiner padrão especificada no modelo é **MCR.Microsoft.com/azuredeploymentscripts-PowerShell:az5.2**. Consulte uma lista de todas as [versões de Azure PowerShell com suporte](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list).

O modelo suspende a instância de contêiner após 1.800 segundos. Você tem 30 minutos antes que a instância do contêiner entre em um estado encerrado e a sessão termine.

Para implantar o modelo:

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateFile = Read-Host -Prompt "Enter the template file path and file name"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Location $location -name $resourceGroupName
New-AzResourceGroupDeployment -resourceGroupName $resourceGroupName -TemplateFile $templatefile -projectName $projectName
```

### <a name="upload-the-deployment-script"></a>Carregar o script de implantação

Carregue o script de implantação na conta de armazenamento. Aqui está um exemplo de um script do PowerShell:

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name that you used earlier"
$fileName = Read-Host -Prompt "Enter the deployment script file name with the path"

$resourceGroupName = "${projectName}rg"
$storageAccountName = "${projectName}store"
$fileShareName = "${projectName}share"

$context = (Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName).Context
Set-AzStorageFileContent -Context $context -ShareName $fileShareName -Source $fileName -Force
```

Você também pode carregar o arquivo usando o portal do Azure ou o CLI do Azure.

### <a name="test-the-deployment-script"></a>Testar o script de implantação

1. No portal do Azure, abra o grupo de recursos em que você implantou a instância de contêiner e a conta de armazenamento.
2. Abra o grupo de contêineres. O nome do grupo de contêineres padrão é o nome do projeto anexado com o *CG*. A instância de contêiner está em estado de **execução** .
3. No menu de recursos, selecione **contêineres**. O nome da instância de contêiner é o nome do projeto anexado com o *contêiner*.

    ![Captura de tela da instância de contêiner do script de implantação Connect no portal do Azure.](./media/deployment-script-template-configure-dev/deployment-script-container-instance-connect.png)

4. Selecione **conectar** e, em seguida, selecione **conectar**. Se você não conseguir se conectar à instância de contêiner, reinicie o grupo de contêineres e tente novamente.
5. No painel de console, execute os seguintes comandos:

    ```console
    cd /mnt/azscripts/azscriptinput
    ls
    pwsh ./hello.ps1 "John Dole"
    ```

    A saída é **Hello John giros**.

    ![Captura de tela da saída de teste da instância do contêiner de conectar script de implantação no console.](./media/deployment-script-template-configure-dev/deployment-script-container-instance-test.png)

## <a name="use-an-azure-cli-container-instance"></a>Usar uma instância de contêiner CLI do Azure

Para criar seus scripts em seu computador, crie uma conta de armazenamento e monte a conta de armazenamento na instância de contêiner. Em seguida, você pode carregar o script na conta de armazenamento e executar o script na instância de contêiner.

> [!NOTE]
> A conta de armazenamento que você cria para testar seu script não é a mesma conta de armazenamento que o serviço de script de implantação usa para executar o script. O serviço de script de implantação cria um nome exclusivo como um compartilhamento de arquivos em cada execução.

### <a name="create-an-azure-cli-container-instance"></a>Criar uma instância de contêiner de CLI do Azure

O modelo ARM a seguir cria uma instância de contêiner e um compartilhamento de arquivos e, em seguida, monta o compartilhamento de arquivos para a imagem de contêiner:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "projectName": {
      "type": "string",
      "metadata": {
        "description": "Specify a project name that is used for generating resource names."
      }
    },
    "containerImage": {
      "type": "string",
      "defaultValue": "mcr.microsoft.com/azure-cli:2.9.1",
      "metadata": {
        "description": "Specify the container image."
      }
    },
    "mountPath": {
      "type": "string",
      "defaultValue": "/mnt/azscripts/azscriptinput",
      "metadata": {
        "description": "Specify the mount path."
      }
    }
  },
  "variables": {
    "storageAccountName": "[tolower(concat(parameters('projectName'), 'store'))]",
    "fileShareName": "[concat(parameters('projectName'), 'share')]",
    "containerGroupName": "[concat(parameters('projectName'), 'cg')]",
    "containerName": "[concat(parameters('projectName'), 'container')]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "name": "[variables('storageAccountName')]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
      },
      "kind": "StorageV2",
      "properties": {
        "accessTier": "Hot"
      }
    },
    {
      "type": "Microsoft.Storage/storageAccounts/fileServices/shares",
      "apiVersion": "2019-06-01",
      "name": "[concat(variables('storageAccountName'), '/default/', variables('fileShareName'))]",
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
      ]
    },
    {
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2019-12-01",
      "name": "[variables('containerGroupName')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
      ],
      "properties": {
        "containers": [
          {
            "name": "[variables('containerName')]",
            "properties": {
              "image": "[parameters('containerImage')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "protocol": "TCP",
                  "port": 80
                }
              ],
              "volumeMounts": [
                {
                  "name": "filesharevolume",
                  "mountPath": "[parameters('mountPath')]"
                }
              ],
              "command": [
                "/bin/bash",
                "-c",
                "echo hello; sleep 1800"
              ]
            }
          }
        ],
        "osType": "Linux",
        "volumes": [
          {
            "name": "filesharevolume",
            "azureFile": {
              "readOnly": false,
              "shareName": "[variables('fileShareName')]",
              "storageAccountName": "[variables('storageAccountName')]",
              "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').keys[0].value]"
            }
          }
        ]
      }
    }
  ]
}
```

O valor padrão para o caminho de montagem é `/mnt/azscripts/azscriptinput` . Esse é o caminho na instância de contêiner onde ele é montado no compartilhamento de arquivos.

A imagem de contêiner padrão especificada no modelo é **MCR.Microsoft.com/Azure-CLI:2.9.1**. Consulte uma lista de [versões de CLI do Azure com suporte](https://mcr.microsoft.com/v2/azure-cli/tags/list).

> [!IMPORTANT]
> O script de implantação usa as imagens da CLI disponíveis do registro de contêiner da Microsoft (MCR). Leva cerca de um mês para certificar uma imagem da CLI para um script de implantação. Não use as versões da CLI que foram lançadas dentro de 30 dias. Para localizar as datas de lançamento das imagens, consulte as [notas de versão da CLI do Azure](/cli/azure/release-notes-azure-cli). Se você usar uma versão sem suporte, a mensagem de erro listará as versões com suporte.

O modelo suspende a instância de contêiner após 1.800 segundos. Você tem 30 minutos antes que a instância do contêiner entre em um estado de terminal e a sessão termine.

Para implantar o modelo:

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateFile = Read-Host -Prompt "Enter the template file path and file name"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Location $location -name $resourceGroupName
New-AzResourceGroupDeployment -resourceGroupName $resourceGroupName -TemplateFile $templatefile -projectName $projectName
```

### <a name="upload-the-deployment-script"></a>Carregar o script de implantação

Carregue o script de implantação na conta de armazenamento. Este é um exemplo do PowerShell:

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name that you used earlier"
$fileName = Read-Host -Prompt "Enter the deployment script file name with the path"

$resourceGroupName = "${projectName}rg"
$storageAccountName = "${projectName}store"
$fileShareName = "${projectName}share"

$context = (Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName).Context
Set-AzStorageFileContent -Context $context -ShareName $fileShareName -Source $fileName -Force
```

Você também pode carregar o arquivo usando o portal do Azure ou o CLI do Azure.

### <a name="test-the-deployment-script"></a>Testar o script de implantação

1. No portal do Azure, abra o grupo de recursos em que você implantou a instância de contêiner e a conta de armazenamento.
1. Abra o grupo de contêineres. O nome do grupo de contêineres padrão é o nome do projeto anexado com o *CG*. A instância de contêiner é mostrada no estado **executando** .
1. No menu de recursos, selecione **contêineres**. O nome da instância de contêiner é o nome do projeto anexado com o *contêiner*.

    ![script de implantação conectar instância de contêiner](./media/deployment-script-template-configure-dev/deployment-script-container-instance-connect.png)

1. Selecione **conectar** e, em seguida, selecione **conectar**. Se você não conseguir se conectar à instância de contêiner, reinicie o grupo de contêineres e tente novamente.
1. No painel de console, execute os seguintes comandos:

    ```console
    cd /mnt/azscripts/azscriptinput
    ls
    ./hello.sh John Dole
    ```

    A saída é **Hello John giros**.

    ![teste de instância de contêiner de script de implantação](./media/deployment-script-template-configure-dev/deployment-script-container-instance-test-cli.png)

## <a name="use-docker"></a>Usar o Docker

Você pode usar uma imagem de contêiner pré-configurada do Docker como seu ambiente de desenvolvimento de script de implantação. Para instalar o Docker, consulte [obter Docker](https://docs.docker.com/get-docker/).
Você também precisa configurar o compartilhamento de arquivos para montar o diretório, que contém os scripts de implantação no contêiner do Docker.

1. Efetuar pull da imagem de contêiner do script de implantação para o computador local:

    ```command
    docker pull mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3
    ```

    O exemplo usa o cmdlet do PowerShell de versão.

    Para efetuar pull de uma imagem da CLI de um MCR:

    ```command
    docker pull mcr.microsoft.com/azure-cli:2.0.80
    ```

    Este exemplo usa a versão 2.0.80 da CLI. O script de implantação usa as imagens de contêineres da CLI padrão encontradas [aqui](https://hub.docker.com/_/microsoft-azure-cli).

1. Execute a imagem do Docker localmente.

    ```command
    docker run -v <host drive letter>:/<host directory name>:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3
    ```

    Substitua a **&lt;letra da unidade do host>** e o **&lt;nome do diretório de host>** com uma pasta existente na unidade compartilhada. Ele mapeia a pasta para a pasta _/data_ no contêiner. Por exemplo, para mapear _D:\docker_:

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3
    ```

    **—** significa manter a imagem de contêiner ativa.

    Um exemplo de CLI:

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azure-cli:2.0.80
    ```

1. A captura de tela a seguir mostra como executar um script do PowerShell, Considerando que você tem um arquivo de *helloworld.ps1* na unidade compartilhada.

    ![Cmd do Docker do script de implantação do modelo do Resource Manager](./media/deployment-script-template/resource-manager-deployment-script-docker-cmd.png)

Depois que o script for testado com êxito, você poderá usá-lo como um script de implantação em seus modelos.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu como usar os scripts de implantação. Para percorrer um tutorial de script de implantação:

> [!div class="nextstepaction"]
> [Tutorial: usar scripts de implantação em modelos ARM](./template-tutorial-deployment-script.md)
