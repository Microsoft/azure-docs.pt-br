---
title: Executar scripts personalizados em VMs do Linux no Azure | Microsoft Docs
description: Automatizar tarefas de configuração de VM do Linux usando a extensão de script personalizado v1
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/14/2018
ms.author: danis
ms.openlocfilehash: f4920cde64ae951fa5f234f6ad6d7423429bb907
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706044"
---
# <a name="use-the-azure-custom-script-extension-version-1-with-linux-virtual-machines"></a>Usar a Versão 1 da Extensão de Script Personalizado do Azure com máquinas virtuais do Linux

[!INCLUDE [virtual-machines-extensions-deprecation-statement](../../../includes/virtual-machines-extensions-deprecation-statement.md)]

A Versão 1 da Extensão de Script Personalizado faz o download e executa scripts em máquinas virtuais do Azure. Essa extensão é útil para a configuração de implantação de postagem, instalação de software ou qualquer outra configuração/tarefa de gerenciamento. Você pode fazer o download de scripts a partir do Armazenamento do Microsoft Azure ou outro local acessível da internet, ou você pode fornecê-los para o tempo de execução da extensão.

A extensão de Script personalizado se integra com os modelos do Azure Resource Manager. Você também pode executá-la usando a CLI do Azure, o PowerShell, o portal do Azure ou a API de REST de máquinas virtuais do Azure.

Este artigo fornece detalhes sobre como usar a extensão de Script personalizado da CLI do Azure, e como executar a extensão usando um modelo do Azure Resource Manager. Este artigo também fornece as etapas de solução de problemas para os sistemas do Linux.

Há duas Extensões do Script Personalizado do Linux:

* Versão 1 - Microsoft.OSTCExtensions.CustomScriptForLinux

* Versão 2 - Microsoft.Azure.Extensions.CustomScript

Por favor troque as implantações novas e existentes para usar a nova versão ([Microsoft.Azure.Extensions.CustomScript](custom-script-linux.md)) em vez disso. A nova versão tem o objetivo de ser uma substituição perfeita. Assim, a migração é tão fácil quanto alterar o nome e versão. Você não precisa alterar a configuração de extensão.

### <a name="operating-system"></a>Sistema operacional

Distribuições Linux com suporte:

* CentOS 6.5 e superior
* Debian 8 e superior
  * Debian 8.7 não envia com Python2 nas últimas imagens, o que interrompe CustomScriptForLinux.
* FreeBSD
* OpenSUSE 13.1 e superior
* Oracle Linux 6.4 e superior
* SUSE Linux Enterprise Server 11 SP3 e superior
* Ubuntu 12.04 e superior

### <a name="script-location"></a>Local do script

Você pode utilizar a extensão para usar suas credenciais do Armazenamento de Blobs do Azure para acessar esse armazenamento. Como alternativa, o local do script pode ser qualquer lugar, desde que a VM possa rotear para esse ponto de extremidade, como GitHub, servidor de arquivos interno, etc.

### <a name="internet-connectivity"></a>Conectividade com a Internet

Se você precisar fazer o download um script externamente, como do GitHub ou do Armazenamento do Azure, será necessário abrir portas adicionais do firewall ou do Grupo de Segurança de Rede. Por exemplo, se o script estiver localizado no Armazenamento do Azure, você poderá permitir acesso usando Marcas de Serviço do NSG do Azure para [Armazenamento](../../virtual-network/security-overview.md#service-tags).

Se o script estiver em um servidor local, ainda poderá ser necessário abrir portas adicionais do firewall ou do Grupo de Segurança de Rede.

### <a name="tips-and-tricks"></a>Dicas e truques

* A taxa de falha mais alta para esta extensão acontece devido a erros de sintaxe no script. Teste as execuções de script sem erros e também insira um registro em log adicional no script para facilitar a localização da falha.
* Escreva scripts idempotentes, para que se forem executados mais de uma vez por acidente, eles não causem alterações no sistema.
* Verifique se os scripts não exigem entrada do usuário quando são executados.
* É permitido que o script seja executado em até 90 minutos. Um período mais longo resultará em falha na provisão da extensão.
* Não coloque reinicializações no script, pois isso causará problemas com outras extensões que estão sendo instaladas e, após a reinicialização, a extensão será interrompida. 
* Se você tiver um script que causará uma reinicialização, então instale aplicativos e execute scripts etc. Você deve agendar a reinicialização usando um trabalho Cron ou usando ferramentas como extensões de Puppet DSC ou Chef.
* A extensão executará um script somente uma vez. Se quiser executar um script em cada inicialização, então você poderá usar [imagem de inicialização de nuvem](../linux/using-cloud-init.md) e um módulo [Scripts Por Inicialização](https://cloudinit.readthedocs.io/en/latest/topics/modules.html#scripts-per-boot). Como alternativa, é possível usar o script para criar uma unidade de serviço Systemd.
* Se quiser agendar quando um script será executado, você deverá usar a extensão para criar um trabalho Cron.
* Quando o script for executado, você só verá um status da extensão 'em transição' no portal do Azure ou no CLI. Se quiser atualizações de status mais frequentes de um script em execução, você precisará criar sua própria solução.
* Extensão de Script Personalizado não oferece nativamente suporte a servidores proxy. No entanto, é possível usar uma ferramenta de transferência de arquivos que oferece suporte a servidores proxy no seu script, como *Curl*.
* Lembre-se de locais de diretório não padrão nos quais seus scripts ou comandos podem confiar e ter lógica para lidar com isso.

## <a name="extension-schema"></a>Esquema de extensão

A configuração de extensão de script personalizado especifica itens como localização de script e o comando a ser executado. Você pode armazenar essa configuração em arquivos de configuração, especificá-la na linha de comando ou especificá-la em um modelo do Azure Resource Manager. 

Você pode armazenar dados confidenciais em uma configuração protegida, que é criptografada e descriptografada somente dentro da máquina virtual. A configuração protegida é útil quando o comando de execução inclui segredos, como uma senha.

Esses itens devem ser tratados como dados confidenciais e especificados na configuração de definição protegida por extensões. Os dados de configuração protegidos pela extensão da VM do Azure são criptografados, sendo descriptografados apenas na máquina virtual de destino.

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
    "publisher": "Microsoft.OSTCExtensions",
    "type": "CustomScriptForLinux",
    "typeHandlerVersion": "1.5",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "<url>"
      ],
      "enableInternalDNSCheck": true
    },
    "protectedSettings": {
      "storageAccountName": "<storage-account-name>",
      "storageAccountKey": "<storage-account-key>",
      "commandToExecute": "<command>"
    }
  }
}
```

### <a name="property-values"></a>Valores de propriedade

| NOME | Valor/Exemplo | Tipo de dados |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.OSTCExtensions | string |
| type | CustomScriptForLinux | string |
| typeHandlerVersion | 1.5 | int |
| fileUris (por exemplo) | https://github.com/MyProject/Archive/MyPythonScript.py | array |
| commandToExecute (por exemplo) | python MyPythonScript.py \<my-param1\> | string |
| enableInternalDNSCheck | true | boolean |
| storageAccountName (por exemplo) | examplestorageacct | string |
| storageAccountKey (por exemplo) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | string |

### <a name="property-value-details"></a>Detalhes de valor de propriedade

* `fileUris`: (opcional, cadeia de caracteres) a lista uri dos scripts
* `enableInternalDNSCheck`: (opcional, bool) padrão é True, defina para False para desabilitar a verificação de DNS.
* `commandToExecute`: (opcional, cadeia de caracteres) o script de ponto de entrada para executar
* `storageAccountName`: (opcional, cadeia de caracteres) o nome da conta de armazenamento
* `storageAccountKey`: (opcional, cadeia de caracteres) a chave de acesso da conta de armazenamento

Os valores a seguir podem ser definidos nas configurações públicas ou protegidas, você não deve ter estes valores abaixo definidos tanto nas configurações protegidas quanto nas públicas.

* `commandToExecute`

Usar as configurações públicas pode ser útil para depuração, mas é altamente recomendável usar as configurações protegidas.

As configurações públicas são enviadas em texto não criptografado para a VM na qual o script será executado.  As configurações protegidas são criptografadas usando uma chave conhecida apenas pelo Azure e pela VM. As configurações são salvas na VM no estado em que foram enviadas, ou seja, se foram criptografadas, elas serão salvas criptografadas na VM. O certificado usado para descriptografar os valores criptografados é armazenado na VM e usado para descriptografar as configurações (se necessário) no tempo de execução.

## <a name="template-deployment"></a>Implantação de modelo

Extensões de VM do Azure podem ser implantadas com modelos do Azure Resource Manager. O esquema JSON detalhado na seção anterior pode ser usado em um modelo do Azure Resource Manager para executar a Extensão de Script Personalizado durante uma implantação de modelo do Azure Resource Manager.

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
    "publisher": "Microsoft.OSTCExtensions",
    "type": "CustomScriptForLinux",
    "typeHandlerVersion": "1.5",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": ["http://MyAccount.blob.core.windows.net/vhds/MyShellScript.sh"]
    },
    "protectedSettings": {
      "storageAccountName": "MyAccount",
      "storageAccountKey": "<storage-account-key>",
      "commandToExecute": "sh MyShellScript.sh"
    }
  }
}
```

>[!NOTE]
>Esses nomes de propriedade diferenciam maiúsculas de minúsculas. Para evitar problemas de implantação, use os nomes conforme mostrado aqui.

## <a name="azure-cli"></a>CLI do Azure

Quando você estiver usando a CLI do Azure para executar a extensão de Script personalizado, crie um arquivo ou arquivos de configuração. No mínimo, você deve ter 'commandToExecute'.

```azurecli
az vm extension set -n VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.5 \
  --vm-name MyVm --resource-group MyResourceGroup \
  --protected-settings '{"commandToExecute": "echo hello"}'
```

Opcionalmente, você pode especificar as configurações no comando como uma cadeia de caracteres do formato JSON. Isso permite especificar a configuração durante a execução e sem um arquivo de configuração separado.

```azurecli
az vm extension set \
  --resource-group exttest \
  --vm-name exttest \
  --name CustomScriptForLinux \
  --publisher Microsoft.OSTCExtensions \
  --settings '{"fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],"commandToExecute": "./config-music.sh"}'
```

### <a name="azure-cli-examples"></a>Exemplos de CLI do Azure

#### <a name="public-configuration-with-no-script-file"></a>Configuração pública sem arquivo de script

```json
{
  "commandToExecute": "apt-get -y update && apt-get install -y apache2"
}
```

Comando CLI do Azure:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name CustomScriptForLinux \
  --publisher Microsoft.OSTCExtensions \
  --settings ./script-config.json
```

#### <a name="public-and-protected-configuration-files"></a>Arquivos de configuração protegida e pública

Você usa um arquivo de configuração pública para especificar o URI do arquivo de script. Você usa um arquivo de configuração protegida para especificar o comando a ser executado.

Arquivo de configuração pública:

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"]
}
```

Arquivo de configuração protegida:  

```json
{
  "commandToExecute": "./config-music.sh <param1>"
}
```

Comando CLI do Azure:

```azurecli
az vm extension set
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name CustomScriptForLinux \
  --publisher Microsoft.OSTCExtensions \
  --settings ./script-config.json \
  --protected-settings ./protected-config.json
```

## <a name="troubleshooting"></a>Solução de problemas

Quando a extensão de script personalizado é executada, o script é criado ou baixado em um diretório semelhante ao exemplo a seguir. A saída do comando também é salva nesse diretório nos arquivos `stdout` e `stderr`.

```bash
/var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-<version>/download/1
```

Para solucionar problemas, primeiro verifique o Log de Agente do Linux, confira a extensão executada, verifique:

```bash
/var/log/waagent.log
```

Você deve procurar a execução da extensão, a exibição será algo assim:

```text
2018/04/26 15:29:44.835067 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Target handler state: enabled
2018/04/26 15:29:44.867625 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] [Enable] current handler state is: notinstalled
2018/04/26 15:29:44.959605 INFO Event: name=Microsoft.OSTCExtensions.CustomScriptForLinux, op=Download, message=Download succeeded, duration=59
2018/04/26 15:29:44.993269 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Initialize extension directory
2018/04/26 15:29:45.022972 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Update settings file: 0.settings
2018/04/26 15:29:45.051763 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Install extension [customscript.py -install]
2018/04/26 15:29:45 CustomScriptForLinux started to handle.
2018/04/26 15:29:45 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] cwd is /var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2
2018/04/26 15:29:45 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Change log file to /var/log/azure/Microsoft.OSTCExtensions.CustomScriptForLinux/1.5.2.2/extension.log
2018/04/26 15:29:46.088212 INFO Event: name=Microsoft.OSTCExtensions.CustomScriptForLinux, op=Install, message=Launch command succeeded: customscript.py -install, duration=1005
2018/04/26 15:29:46.133367 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Enable extension [customscript.py -enable]
2018/04/26 15:29:46 CustomScriptForLinux started to handle.
..
2018/04/26 15:29:47.178163 INFO Event: name=Microsoft.OSTCExtensions.CustomScriptForLinux, op=Enable, message=Launch command succeeded: customscript.py -enable, duration=1012
```

Alguns pontos a serem observados:

1. Habilitar é quando o comando é iniciado.
1. O download está relacionado ao download do pacote de extensão CustomScript do Azure, não aos arquivos de script especificados no fileUris.
1. Você também pode ver qual arquivo de log estiver gravando no `/var/log/azure/Microsoft.OSTCExtensions.CustomScriptForLinux/1.5.2.2/extension.log`

A próxima etapa é fazer uma verificação do arquivo de log, este é o formato:

```bash
/var/log/azure/<extension-name>/<version>/extension.log file.
```

Você deve procurar a execução individual, ele será algo parecido com:

```text
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Enable,transitioning,0,Launching the script...
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] sequence number is 0
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] setting file path is/var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2/config/0.settings
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] JSON config: {"runtimeSettings": [{"handlerSettings": {"protectedSettings": "MIIB0AYJKoZIhvcNAQcDoIIBwTCCAb0CAQAxggF+hnEXRtFKTTuKiFC8gTfHKupUSs7qI0zFYRya", "publicSettings": {"fileUris": ["https://dannytesting.blob.core.windows.net/demo/myBash.sh"]}, "protectedSettingsCertThumbprint": "4385AB21617C2452FF6998C0A37F71A0A01C8368"}}]}
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Config decoded correctly.
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Will try to download files, number of retries = 10, wait SECONDS between retrievals = 20s
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Downloading,transitioning,0,Downloading files...
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] No azure storage account and key specified in protected settings. Downloading scripts from external links...
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Converting /var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2/download/0/myBash.sh from DOS to Unix formats: Done
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Removing BOM of /var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2/download/0/myBash.sh: Done
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Succeeded to download files, retry count = 0
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Internal DNS is ready, retry count = 0
2018/04/26 15:29:47 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Command is finished.
2018/04/26 15:29:47 ---stdout---
2018/04/26 15:29:47
2018/04/26 15:29:47 ---errout---
2018/04/26 15:29:47
2018/04/26 15:29:47
2018/04/26 15:29:47 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Daemon,success,0,Command is finished.
2018/04/26 15:29:47 ---stdout---
2018/04/26 15:29:47
2018/04/26 15:29:47 ---errout---
2018/04/26 15:29:47
2018/04/26 15:29:47
```

Aqui, você pode ver:

* O início do comando Habilitar é este log
* As configurações passadas para a extensão
* A extensão baixando o arquivo e o resultado disso.
* O comando em execução e o resultado.

Você também pode recuperar o estado de execução da extensão de Script personalizado usando a CLI do Azure:

```azurecli
az vm extension list -g myResourceGroup --vm-name myVM
```

A saída se parece com o seguinte texto:

```azurecli
Name                  ProvisioningState    Publisher                   Version  AutoUpgradeMinorVersion
--------------------  -------------------  ------------------------  ---------  -------------------------
CustomScriptForLinux  Succeeded            Microsoft.OSTCExtensions        1.5  True
```

## <a name="next-steps"></a>Próximas etapas

Para ver o código, os problemas atuais e as versões, consulte [CustomScript Extension repo](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript).
