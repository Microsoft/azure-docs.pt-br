---
title: Executar scripts personalizados em VMs do Linux no Azure | Microsoft Docs
description: "Automatizar tarefas de configuração de VM do Linux usando a extensão de script personalizado"
services: virtual-machines-linux
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: cf17ab2b-8d7e-4078-b6df-955c6d5071c2
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: nepeters
ms.openlocfilehash: 1dde64aac72c11ccfccf4fdb676279692befaadd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="using-the-azure-custom-script-extension-with-linux-virtual-machines"></a>Uso da extensão de script personalizado do Azure com máquinas virtuais do Linux
A extensão de script personalizado baixa e executa scripts em máquinas virtuais do Azure. Essa extensão é útil para a configuração de implantação de postagem, instalação de software ou qualquer outra configuração/tarefa de gerenciamento. Scripts podem ser baixados do armazenamento do Azure ou de outra localização acessível da Internet ou fornecidos para o tempo de execução da extensão. A extensão de script personalizado se integra com modelos do Azure Resource Manager e também pode ser executada usando a CLI do Azure, o PowerShell, o portal do Azure ou a API REST da máquina virtual do Azure.

Este documento fornece detalhes sobre como usar a extensão de script personalizado na CLI do Azure e um modelo do Azure Resource Manager e também fornece detalhes sobre as etapas de solução de problemas em sistemas Linux.

## <a name="extension-configuration"></a>Configuração da extensão
A configuração de extensão de script personalizado especifica itens como localização de script e o comando a ser executado. Essa configuração pode ser armazenada em arquivos de configuração, especificada na linha de comando ou em um modelo do Azure Resource Manager. Dados confidenciais podem ser armazenados em uma configuração protegida, que é criptografada e descriptografada somente dentro da máquina virtual. A configuração protegida é útil quando o comando de execução inclui segredos, como uma senha.

### <a name="public-configuration"></a>Configuração pública
Esquema:

**Observação** – esses nomes de propriedade diferenciam maiúsculas de minúsculas. Use os nomes conforme mostrado abaixo para evitar problemas de implantação.

* **commandToExecute**: (obrigatório, cadeia de caracteres) o script de ponto de entrada a ser executado
* **fileUris**: (opcional, matriz de cadeia de caracteres) as URLs de arquivos a serem baixados.
* **timestamp** (opcional, inteiro) use este campo somente para disparar uma nova execução do script, alterando o valor desse campo.

```json
{
  "fileUris": ["<url>"],
  "commandToExecute": "<command-to-execute>"
}
```

### <a name="protected-configuration"></a>Configuração protegida
Esquema:

**Observação** – esses nomes de propriedade diferenciam maiúsculas de minúsculas. Use os nomes conforme mostrado abaixo para evitar problemas de implantação.

* **commandToExecute**: (opcional, cadeia de caracteres) o script de ponto de entrada a ser executado. Use este campo se o comando tiver segredos, como senhas.
* **storageAccountName**: (opcional, cadeia de caracteres) o nome da conta de armazenamento. Se você especificar credenciais de armazenamento, todos os fileUris deverão ser URLs para Blobs do Azure.
* **storageAccountKey**: (opcional, cadeia de caracteres) a tecla de acesso da conta de armazenamento.

```json
{
  "commandToExecute": "<command-to-execute>",
  "storageAccountName": "<storage-account-name>",
  "storageAccountKey": "<storage-account-key>"
}
```

## <a name="azure-cli"></a>CLI do Azure
Ao usar a CLI do Azure para executar a extensão de script personalizado, crie um arquivo de configuração ou arquivos que contêm pelo menos o URI do arquivo e o comando de execução do script.

```azurecli
az vm extension set --resource-group myResourceGroup --vm-name myVM --name customScript --publisher Microsoft.Azure.Extensions --settings ./script-config.json
```

As configurações também podem ser especificadas no comando como uma cadeia de caracteres formatada em JSON. Isso permite especificar a configuração durante a execução e sem um arquivo de configuração separado.

```azurecli
az vm extension set '
  --resource-group exttest `
  --vm-name exttest `
  --name customScript `
  --publisher Microsoft.Azure.Extensions `
  --settings '{"fileUris": ["https://raw.githubusercontent.com/neilpeterson/test-extension/master/test.sh"],"commandToExecute": "./test.sh"}'
```

### <a name="azure-cli-examples"></a>Exemplos de CLI do Azure

**Exemplo 1** – Configuração pública com o arquivo de script.

```json
{
  "fileUris": ["https://raw.githubusercontent.com/neilpeterson/test-extension/master/test.sh"],
  "commandToExecute": "./test.sh"
}
```

Comando CLI do Azure:

```azurecli
az vm extension set --resource-group myResourceGroup --vm-name myVM --name customScript --publisher Microsoft.Azure.Extensions --settings ./script-config.json
```

**Exemplo 2** – Configuração pública sem nenhum arquivo de script.

```json
{
  "commandToExecute": "apt-get -y update && apt-get install -y apache2"
}
```

Comando CLI do Azure:

```azurecli
az vm extension set --resource-group myResourceGroup --vm-name myVM --name customScript --publisher Microsoft.Azure.Extensions --settings ./script-config.json
```

**Exemplo 3** – Um arquivo de configuração pública é usado para especificar o URI do arquivo de script, e um arquivo de configuração protegida é usado para especificar o comando a ser executado.

Arquivo de configuração pública:

```json
{
  "fileUris": ["https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"]
}
```

Arquivo de configuração protegida:  

```json
{
  "commandToExecute": "./hello.sh <password>"
}
```

Comando CLI do Azure:

```azurecli
az vm extension set --resource-group myResourceGroup --vm-name myVM --name customScript --publisher Microsoft.Azure.Extensions --settings ./script-config.json --protected-settings ./protected-config.json
```

## <a name="resource-manager-template"></a>Modelo do Resource Manager
A extensão de script personalizado do Azure pode ser executada no momento da implantação de máquina virtual usando um modelo do Resource Manager. Para fazer isso, adicione JSON formatado corretamente ao modelo de implantação.

### <a name="resource-manager-examples"></a>Exemplos do Resource Manager
**Exemplo 1** – Configuração pública.

```json
{
    "name": "scriptextensiondemo",
    "type": "extensions",
    "location": "[resourceGroup().location]",
    "apiVersion": "2015-06-15",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', parameters('scriptextensiondemoName'))]"
    ],
    "tags": {
        "displayName": "scriptextensiondemo"
    },
    "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
      "settings": {
        "fileUris": [
          "https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"
        ],
        "commandToExecute": "sh hello.sh"
      }
    }
}
```

**Exemplo 2** – Comando de execução na configuração protegida.

```json
{
  "name": "config-app",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"
      ]              
    },
    "protectedSettings": {
      "commandToExecute": "sh hello.sh <password>"
    }
  }
}
```

Consulte a Demonstração da Loja de Música do .Net Core para ver um exemplo completo – [Demonstração da Loja de Música](https://github.com/neilpeterson/nepeters-azure-templates/tree/master/dotnet-core-music-linux-vm-sql-db).

## <a name="troubleshooting"></a>Solucionar problemas
Quando a extensão de script personalizado é executada, o script é criado ou baixado em um diretório semelhante ao exemplo a seguir. A saída do comando também é salva nesse diretório no arquivo `stdout` e `stderr`.

```bash
/var/lib/waagent/custom-script/download/0/
```

A extensão de script do Azure produz um log, que pode ser encontrado aqui.

```bash
/var/log/azure/custom-script/handler.log
```

O estado de execução da extensão de script personalizado também pode ser recuperado com a CLI do Azure.

```azurecli
az vm extension list -g myResourceGroup --vm-name myVM
```

A saída se parece com o seguinte texto:

```azurecli
info:    Executing command vm extension get
+ Looking up the VM "scripttst001"
data:    Publisher                   Name                                      Version  State
data:    --------------------------  ----------------------------------------  -------  ---------
data:    Microsoft.Azure.Extensions  CustomScript                              2.0      Succeeded
data:    Microsoft.OSTCExtensions    Microsoft.Insights.VMDiagnosticsSettings  2.3      Succeeded
info:    vm extension get command OK
```

## <a name="next-steps"></a>Próximas etapas
Para obter informações sobre outras extensões de script de VM, consulte [Visão geral da extensão de script do Azure para Linux](extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

