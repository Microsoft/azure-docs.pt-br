---
title: Implantar extensões de VM com o modelo
description: Saiba como implantar extensões da máquina virtual com modelos do ARM (modelos do Azure Resource Manager).
author: mumian
ms.date: 03/26/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 320908ab4b24c43e8bd5209d4f32ef0211036958
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105628417"
---
# <a name="tutorial-deploy-virtual-machine-extensions-with-arm-templates"></a>Tutorial: Implantar extensões de máquina virtual com modelos do ARM

Saiba como usar [extensões de máquina virtual do Azure](../../virtual-machines/extensions/features-windows.md) para executar tarefas de configuração e automação pós-implantação em VMs do Azure. Muitas extensões de VM diferentes estão disponíveis para uso com as VMs do Azure. Neste tutorial, você implantará uma extensão de script personalizado com base em um modelo do ARM (modelo do Azure Resource Manager) para executar um script do PowerShell em uma VM do Windows. O script instala o servidor Web na VM.

Este tutorial cobre as seguintes tarefas:

> [!div class="checklist"]
> * Preparar um script do PowerShell
> * Abrir um modelo de início rápido
> * Editar o modelo
> * Implantar o modelo

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, você precisa do seguinte:

* Visual Studio Code com a extensão de Ferramentas do Resource Manager. Confira [Início Rápido: Criar modelos do ARM com o Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md).
* Para aumentar a segurança, use uma senha gerada para a conta de administrador da máquina virtual. Veja um exemplo para gerar uma senha:

    ```console
    openssl rand -base64 32
    ```

    O Azure Key Vault é projetado para proteger chaves de criptografia e outros segredos. Para saber mais, confira [Tutorial: Integrar o Azure Key Vault na sua implantação de modelo do ARM](./template-tutorial-use-key-vault.md). Também recomendamos que você atualize sua senha a cada três meses.

## <a name="prepare-a-powershell-script"></a>Preparar um script do PowerShell

Você pode usar um script do PowerShell embutido ou um arquivo de script. Este tutorial mostra como usar um arquivo de script. Um script do PowerShell com o seguinte conteúdo é compartilhado no [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-vm-extension/installWebServer.ps1):

```azurepowershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Se você optar por publicar o arquivo em seu próprio local, atualize o elemento `fileUri` no modelo posteriormente no tutorial.

## <a name="open-a-quickstart-template"></a>Abrir um modelo de início rápido

Modelos de Início Rápido do Azure é um repositório de modelos do ARM. Em vez de criar um modelo do zero, você pode encontrar um exemplo de modelo e personalizá-lo. O modelo usado neste tutorial é chamado [Implantar uma VM Windows simples](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

1. No Visual Studio Code, escolha **Arquivo** > **Abrir Arquivo**.
1. Na caixa **Nome do arquivo**, cole a seguinte URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

1. Para abrir o arquivo, selecione **Abrir**.
    O modelo define cinco recursos:

   * [**Microsoft.Storage/storageAccounts**](/azure/templates/Microsoft.Storage/storageAccounts).
   * [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses).
   * [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups).
   * [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks).
   * [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces).
   * [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines).

     É útil ter algumas noções básicas do modelo antes de personalizá-lo.

1. Salve uma cópia do arquivo em seu computador local com o nome *azuredeploy.json* selecionando **Arquivo** > **Salvar como**.

## <a name="edit-the-template"></a>Editar o modelo

Adicione um recurso de extensão de máquina virtual ao modelo existente com o seguinte conteúdo:

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "apiVersion": "2020-12-01",
  "name": "[concat(variables('vmName'),'/', 'InstallWebServer')]",
  "location": "[parameters('location')]",
  "dependsOn": [
      "[concat('Microsoft.Compute/virtualMachines/',variables('vmName'))]"
  ],
  "properties": {
      "publisher": "Microsoft.Compute",
      "type": "CustomScriptExtension",
      "typeHandlerVersion": "1.7",
      "autoUpgradeMinorVersion":true,
      "settings": {
        "fileUris": [
          "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-vm-extension/installWebServer.ps1"
        ],
        "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File installWebServer.ps1"
      }
  }
}
```

Para obter mais informações sobre essa definição de recurso, veja [referência de extensão](/azure/templates/microsoft.compute/virtualmachines/extensions). Abaixo estão alguns elementos importantes:

* `name`: como o recurso de extensão é um recurso filho do objeto de máquina virtual, o nome deve ter o prefixo do nome da máquina virtual. Confira [Definir o nome e o tipo de recursos filho](child-resource-name-type.md).
* `dependsOn`: Crie o recurso de extensão depois de criar a máquina virtual.
* `fileUris`: os locais em que os arquivos de script são armazenados. Se você optar por não usar o local fornecido, precisará atualizar os valores.
* `commandToExecute`: esse comando chama o script.

Para usar um script embutido, remova `fileUris` e atualize `commandToExecute` para:

```powershell
powershell.exe Install-WindowsFeature -name Web-Server -IncludeManagementTools && powershell.exe remove-item 'C:\\inetpub\\wwwroot\\iisstart.htm' && powershell.exe Add-Content -Path 'C:\\inetpub\\wwwroot\\iisstart.htm' -Value $('Hello World from ' + $env:computername)
```

Esse script embutido também atualiza o conteúdo de _iisstart.html_.

Você também deve abrir a porta HTTP para que possa acessar o servidor Web.

1. Encontre `securityRules` no modelo.
1. Adicione a regra a seguir ao lado de **default-allow-3389**.

    ```json
    {
      "name": "AllowHTTPInBound",
      "properties": {
        "priority": 1010,
        "access": "Allow",
        "direction": "Inbound",
        "destinationPortRange": "80",
        "protocol": "Tcp",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "*"
      }
    }
    ```

## <a name="deploy-the-template"></a>Implantar o modelo

Para o procedimento de implantação, veja a seção **Implantar o modelo** do [Tutorial: Criar modelos do ARM com recursos dependentes](./template-tutorial-create-templates-with-dependent-resources.md#deploy-the-template). É recomendável usar uma senha gerada para a conta de administrador da máquina virtual. Veja a seção [Pré-requisitos](#prerequisites) deste artigo.

No Cloud Shell, execute o seguinte comando para recuperar o endereço IP público da VM:

```azurepowershell
(Get-AzPublicIpAddress -ResourceGroupName $resourceGroupName).IpAddress
```

Cole o endereço IP em um navegador da Web. A página de boas-vindas do IIS (Serviços de Informações da Internet) padrão é aberta:

![A página de boas-vindas dos Serviços de Informações da Internet](./media/template-tutorial-deploy-vm-extensions/resource-manager-template-deploy-extensions-customer-script-web-server.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Quando você não precisa mais dos recursos do Azure que implantou, limpe-os exclui o grupo de recursos.

1. No portal do Azure, no painel esquerdo, selecione **Grupo de recursos**.
2. Na caixa **Filtrar por nome**, insira o nome do grupo de recursos.
3. Selecione o nome do grupo de recursos.
    Seis recursos serão exibidos no grupo de recursos.
4. No menu do grupo, selecione **Excluir grupo de recursos**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você implantou uma máquina virtual e uma extensão da máquina virtual. A extensão instalou o servidor Web do IIS na máquina virtual. Para saber como usar a extensão do Banco de Dados SQL do Azure para importar um arquivo BACPAC, confira:

> [!div class="nextstepaction"]
> [Implantar extensões de SQL](./template-tutorial-deploy-sql-extensions-bacpac.md)
