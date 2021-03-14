---
title: Executar extensão de script personalizado em VMs do Linux no Azure
description: Automatizar tarefas de configuração de VM do Linux usando a Extensão de Script Personalizado v2
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
ms.author: amjads
author: amjads1
ms.collection: linux
ms.date: 04/25/2018
ms.openlocfilehash: 094e5f4b1bf1611f2d418d3a7b8db15ec5d58878
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102563567"
---
# <a name="use-the-azure-custom-script-extension-version-2-with-linux-virtual-machines"></a>Usar a Versão 2 da Extensão de Script Personalizado do Azure com máquinas virtuais do Linux
A Versão 2 da Extensão de Script Personalizado baixa e executa scripts em máquinas virtuais do Azure. Essa extensão é útil para a configuração de implantação de postagem, instalação de software ou qualquer outra configuração/tarefa de gerenciamento. Você pode fazer o download de scripts a partir do Armazenamento do Microsoft Azure ou outro local acessível da internet, ou você pode fornecê-los para o runtime da extensão. 

A extensão de Script personalizado se integra com os modelos do Azure Resource Manager. Você também pode executá-la usando a CLI do Azure, o PowerShell, o portal do Azure ou a API de REST de máquinas virtuais do Azure.

Este artigo fornece detalhes sobre como usar a extensão de Script personalizado da CLI do Azure, e como executar a extensão usando um modelo do Azure Resource Manager. Este artigo também fornece as etapas de solução de problemas para os sistemas do Linux.


Há duas Extensões do Script Personalizado do Linux:
* Versão 1 - Microsoft.OSTCExtensions.CustomScriptForLinux
* Versão 2 - Microsoft.Azure.Extensions.CustomScript

Alterne entre implantações novas e existentes para usar a nova versão 2. A nova versão tem o objetivo de ser uma substituição perfeita. Assim, a migração é tão fácil quanto alterar o nome e versão. Você não precisa alterar a configuração de extensão.


### <a name="operating-system"></a>Sistema operacional

A extensão de script personalizado para Linux será executada na extensão do so de extensão com suporte, para obter mais informações, consulte este [artigo](../linux/endorsed-distros.md).

### <a name="script-location"></a>Local do script

Você pode utilizar a extensão para usar suas credenciais do Armazenamento de Blobs do Azure para acessar esse armazenamento. Como alternativa, o local do script pode ser qualquer lugar, desde que a VM possa rotear para esse ponto de extremidade, como GitHub, servidor de arquivos interno, etc.

### <a name="internet-connectivity"></a>Conectividade com a Internet
Se você precisar fazer o download um script externamente, como do GitHub ou do Armazenamento do Azure, será necessário abrir portas adicionais do firewall ou do Grupo de Segurança de Rede. Por exemplo, se o seu script estiver localizado no armazenamento do Azure, você poderá permitir o acesso usando as marcas do serviço NSG do Azure para [armazenamento](../../virtual-network/network-security-groups-overview.md#service-tags).

Se o script estiver em um servidor local, ainda poderá ser necessário abrir portas adicionais do firewall ou do Grupo de Segurança de Rede.

### <a name="tips-and-tricks"></a>Dicas e truques
* A taxa de falha mais alta para esta extensão acontece devido a erros de sintaxe no script. Teste as execuções de script sem erros e também insira um registro em log adicional no script para facilitar a localização da falha.
* Escreva scripts idempotentes, para que se forem executados mais de uma vez por acidente, eles não causem alterações no sistema.
* Verifique se os scripts não exigem entrada do usuário quando são executados.
* É permitido que o script seja executado em até 90 minutos. Um período mais longo resultará em falha na provisão da extensão.
* Não coloque reinicializações no script, pois isso causará problemas com outras extensões que estão sendo instaladas e, após a reinicialização, a extensão será interrompida. 
* Não é recomendável executar um script que causará uma parada ou atualização do agente de VM. Isso pode deixar a extensão em um estado de transição e levar a um tempo limite.
* Se você tiver um script que causará uma reinicialização, instale aplicativos e execute scripts, etc. Você deve agendar a reinicialização usando um trabalho cron ou usando ferramentas como DSC, ou chefe, Puppet Extensions.
* A extensão executará um script somente uma vez. Se quiser executar um script em cada inicialização, então você poderá usar [imagem de inicialização de nuvem](../linux/using-cloud-init.md) e um módulo [Scripts Por Inicialização](https://cloudinit.readthedocs.io/en/latest/topics/modules.html#scripts-per-boot). Como alternativa, você pode usar o script para criar uma unidade de serviço do sistema.
* Você só pode ter uma versão de uma extensão aplicada à VM. Para executar um segundo script personalizado, você pode atualizar a extensão existente com a nova configuração. Como alternativa, você pode remover a extensão de script personalizado e reaplicá-la novamente com o script atualizado.
* Se quiser agendar quando um script será executado, você deverá usar a extensão para criar um trabalho Cron. 
* Quando o script for executado, você só verá um status da extensão 'em transição' no portal do Azure ou no CLI. Se quiser atualizações de status mais frequentes de um script em execução, você precisará criar sua própria solução.
* A extensão de script personalizado não dá suporte nativo a servidores proxy, no entanto, você pode usar uma ferramenta de transferência de arquivo que dá suporte a servidores proxy em seu script, como *ondulação*. 
* Lembre-se de locais de diretório não padrão nos quais seus scripts ou comandos podem confiar e ter lógica para lidar com isso.
*  Ao implantar o script personalizado em instâncias de VMSS de produção, é recomendável implantar por meio do modelo JSON e armazenar sua conta de armazenamento de script onde você tem controle sobre o token SAS. 


## <a name="extension-schema"></a>Esquema de extensão

A configuração de extensão de script personalizado especifica itens como localização de script e o comando a ser executado. Você pode armazenar essa configuração em arquivos de configuração, especificá-la na linha de comando ou especificá-la em um modelo do Azure Resource Manager. 

Você pode armazenar dados confidenciais em uma configuração protegida, que é criptografada e descriptografada somente dentro da máquina virtual. A configuração protegida é útil quando o comando de execução inclui segredos, como uma senha.

Esses itens devem ser tratados como dados confidenciais e especificados na configuração de definição protegida por extensões. Os dados de configuração protegidos pela extensão da VM do Azure são criptografados, sendo descriptografados apenas na máquina virtual de destino.

```json
{
  "name": "config-app",
  "type": "Extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2019-03-01",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.1",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "skipDos2Unix":false,
      "timestamp":123456789          
    },
    "protectedSettings": {
       "commandToExecute": "<command-to-execute>",
       "script": "<base64-script-to-execute>",
       "storageAccountName": "<storage-account-name>",
       "storageAccountKey": "<storage-account-key>",
       "fileUris": ["https://.."],
       "managedIdentity" : "<managed-identity-identifier>"
    }
  }
}
```

>[!NOTE]
> A propriedade managedIdentity **não pode** ser usada em conjunto com as propriedades storageAccountName ou storageAccountKey

### <a name="property-values"></a>Valores de propriedade

| Nome | Valor/Exemplo | Tipo de Dados | 
| ---- | ---- | ---- |
| apiVersion | 2019-03-01 | date |
| publicador | Microsoft.Compute.Extensions | string |
| type | CustomScript | string |
| typeHandlerVersion | 2.1 | INT |
| fileUris (por exemplo) | `https://github.com/MyProject/Archive/MyPythonScript.py` | matriz |
| commandToExecute (por exemplo) | MyPythonScript.py Python \<my-param1> | string |
| Script | IyEvYmluL3NoCmVjaG8gIlVwZGF0aW5nIHBhY2thZ2VzIC4uLiIKYXB0IHVwZGF0ZQphcHQgdXBncmFkZSAteQo= | string |
| skipDos2Unix (exemplo) | false | booleano |
| carimbo de data/hora (exemplo) | 123456789 | Inteiro de 32 bits |
| storageAccountName (por exemplo) | examplestorageacct | string |
| storageAccountKey (por exemplo) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | string |
| managedIdentity (por exemplo) | { } ou { "clientId": "31b403aa-c364-4240-a7ff-d85fb6cd7232" } ou { "objectId": "12dd289c-0583-46e5-b9b4-115d5c19ef4b" } | objeto JSON |

### <a name="property-value-details"></a>Detalhes de valor de propriedade
* `apiVersion`: O apiVersion mais atualizado pode ser encontrado usando o [Gerenciador de recursos](https://resources.azure.com/) ou de CLI do Azure usando o comando a seguir `az provider list -o json`
* `skipDos2Unix`: (opcional, booliano) ignore a conversão de dos2unix das URLs de arquivos baseado no script ou do script.
* `timestamp`(opcional, inteiro de 32 bits) use este campo somente para disparar uma nova execução do script ao alterar o valor desse campo.  Qualquer valor inteiro é aceitável. Só deve ser diferente do valor anterior.
* `commandToExecute`(**obrigatório** se o script não for definido, cadeia de caracteres) o script de ponto de entrada a ser executado. Use este campo se o comando tiver segredos, como senhas.
* `script`: (**necessário** se commandToExecute não for definido, cadeia de caracteres) um script codificado em Base64 (e opcionalmente compactado com Gzip) executado pelo /bin/sh.
* `fileUris`: (opcional, matriz de cadeia de caracteres) as URLs dos arquivos a serem baixados.
* `storageAccountName`: (opcional, cadeia de caracteres) o nome da conta de armazenamento. Se você especificar credenciais de armazenamento, todos os `fileUris` deverão ser URLs para Blobs do Azure.
* `storageAccountKey`: (opcional, cadeia de caracteres) a chave de acesso da conta de armazenamento
* `managedIdentity`: (opcional, objeto JSON) a [identidade gerenciada](../../active-directory/managed-identities-azure-resources/overview.md) para baixar arquivos
  * `clientId`: (opcional, cadeia de caracteres) a ID do cliente da identidade gerenciada
  * `objectId`: (opcional, cadeia de caracteres) a ID de objeto da identidade gerenciada


Os valores a seguir podem ser definidos nas configurações públicas ou protegidas. A extensão rejeitará qualquer configuração em que os valores abaixo estejam definidos tanto nas configurações protegidas quanto nas públicas.
* `commandToExecute`
* `script`
* `fileUris`

Usar as configurações públicas pode ser útil para depuração, mas é altamente recomendável usar as configurações protegidas.

As configurações públicas são enviadas em texto não criptografado para a VM na qual o script será executado.  As configurações protegidas são criptografadas usando uma chave conhecida apenas pelo Azure e pela VM. As configurações são salvas na VM no estado em que foram enviadas, ou seja, se foram criptografadas, elas serão salvas criptografadas na VM. O certificado usado para descriptografar os valores criptografados é armazenado na VM e usado para descriptografar as configurações (se necessário) no runtime.

#### <a name="property-skipdos2unix"></a>Propriedade: skipDos2Unix

O valor padrão é false, o que significa que a conversão de dos2unix **foi** executada.

A versão anterior do CustomScript, Microsoft.OSTCExtensions.CustomScriptForLinux, seria converter automaticamente arquivos DOS para arquivos do UNIX ao traduzir `\r\n` para `\n`. Essa translação ainda existe e está ativada por padrão. Essa conversão é aplicada a todos os arquivos baixados do fileUris ou à configuração de script com base em qualquer um dos critérios a seguir.

* Se a extensão for uma dos `.sh`, `.txt`, `.py` ou `.pl`, ela será convertida. A configuração de script sempre corresponderá a esse critério, pois ele é considerado um script executado com /bin/sh e é salvo como script.sh na VM.
* Se o arquivo começar com `#!`.

A conversão de dos2unix poderá ser ignorada ao definir o skipDos2Unix como true.

```json
{
  "fileUris": ["<url>"],
  "commandToExecute": "<command-to-execute>",
  "skipDos2Unix": true
}
```

####  <a name="property-script"></a>Propriedade: script

CustomScript dá suporte à execução de um script definido pelo usuário. As configurações de script para combinar commandToExecute e fileUris em uma única configuração. Em vez de ter que configurar um arquivo para download do armazenamento do Azure ou do gist do GitHub, você pode simplesmente codificar o script como uma configuração. O script pode ser usado para commandToExecute e fileUris substituídos.

O script **deve** ser codificado em Base64.  O script pode, **opcionalmente**, ser compactado com Gzip. A configuração de script pode ser usada nas configurações públicas ou protegidas. O tamanho máximo dos dados do parâmetro do script é 256 KB. Se o script exceder esse tamanho, não será executado.

Por exemplo, considerando o seguinte script salvo no arquivo /script.sh/.

```sh
#!/bin/sh
echo "Updating packages ..."
apt update
apt upgrade -y
```

A configuração correta do script de CustomScript deve ser construída ao utilizar a saída do comando a seguir.

```sh
cat script.sh | base64 -w0
```

```json
{
  "script": "IyEvYmluL3NoCmVjaG8gIlVwZGF0aW5nIHBhY2thZ2VzIC4uLiIKYXB0IHVwZGF0ZQphcHQgdXBncmFkZSAteQo="
}
```

O script pode, opcionalmente, ser compactado com Gzip para reduzir o tamanho (na maioria dos casos). (CustomScript detecta automaticamente o uso de compactação gzip.)

```sh
cat script | gzip -9 | base64 -w 0
```

CustomScript usa o algoritmo a seguir para executar um script.

 1. declarar que o comprimento do valor do script não excede 256 KB.
 1. Base64 decodifica o valor do script
 1. _tentativa_ de efetuar gunzip no valor decodificado em Base64
 1. gravar o valor decodificado (e opcionalmente descompactado) para o disco (/var/lib/waagent/custom-script/#/script.sh)
 1. execute o script usando _/bin/sh -c /var/lib/waagent/custom-script/#/script.sh.

####  <a name="property-managedidentity"></a>Propriedade: managedIdentity
> [!NOTE]
> Essa propriedade **precisa** ser especificada somente em configurações protegidas.

O CustomScript (versão 2,1 em diante) dá suporte à [identidade gerenciada](../../active-directory/managed-identities-azure-resources/overview.md) para baixar arquivo (s) de URLs fornecidas na configuração "fileuris". Ele permite que o CustomScript acesse contêineres ou blobs privados do Armazenamento do Azure sem que o usuário precise passar segredos como tokens SAS ou chaves de conta de armazenamento.

Para usar esse recurso, o usuário precisa adicionar uma identidade [atribuída pelo sistema](../../app-service/overview-managed-identity.md?tabs=dotnet#add-a-system-assigned-identity) ou [atribuída pelo usuário](../../app-service/overview-managed-identity.md?tabs=dotnet#add-a-user-assigned-identity) à VM ou VMSS em que se espera que o CustomScript seja executado e [permitir acesso de identidade gerenciada ao blob ou contêiner do Armazenamento do Azure](../../active-directory/managed-identities-azure-resources/tutorial-vm-windows-access-storage.md#grant-access).

Para usar a identidade atribuída pelo sistema na VM/VMSS de destino, defina o campo "managedidentity" como um objeto JSON vazio. 

> Exemplo:
>
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.sh"],
>   "commandToExecute": "sh script1.sh",
>   "managedIdentity" : {}
> }
> ```

Para usar a identidade atribuída pelo usuário na VM/VMSS de destino, configure o campo "managedidentity" com a ID do cliente ou a ID de objeto da identidade gerenciada.

> Exemplos:
>
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.sh"],
>   "commandToExecute": "sh script1.sh",
>   "managedIdentity" : { "clientId": "31b403aa-c364-4240-a7ff-d85fb6cd7232" }
> }
> ```
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.sh"],
>   "commandToExecute": "sh script1.sh",
>   "managedIdentity" : { "objectId": "12dd289c-0583-46e5-b9b4-115d5c19ef4b" }
> }
> ```

> [!NOTE]
> A propriedade managedIdentity **não pode** ser usada em conjunto com as propriedades storageAccountName ou storageAccountKey

## <a name="template-deployment"></a>Implantação de modelo
Extensões de VM do Azure podem ser implantadas com modelos do Azure Resource Manager. O esquema JSON detalhado na seção anterior pode ser usado em um modelo do Azure Resource Manager para executar a Extensão de Script Personalizado durante uma implantação de modelo do Azure Resource Manager. Um modelo de exemplo que inclui a Extensão de Script Personalizado pode ser encontrado aqui, [GitHub](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).


```json
{
  "name": "config-app",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2019-03-01",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.1",
    "autoUpgradeMinorVersion": true,
    "settings": {
      },
    "protectedSettings": {
      "commandToExecute": "sh hello.sh <param2>",
      "fileUris": ["https://github.com/MyProject/Archive/hello.sh"
      ]  
    }
  }
}
```

>[!NOTE]
>Esses nomes de propriedade diferenciam maiúsculas de minúsculas. Para evitar problemas de implantação, use os nomes conforme mostrado aqui.

## <a name="azure-cli"></a>CLI do Azure
Quando você estiver usando a CLI do Azure para executar a extensão de Script personalizado, crie um arquivo ou arquivos de configuração. No mínimo, você deve ter 'commandToExecute'.

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --protected-settings ./script-config.json
```

Opcionalmente, você pode especificar as configurações no comando como uma cadeia de caracteres do formato JSON. Isso permite especificar a configuração durante a execução e sem um arquivo de configuração separado.

```azurecli
az vm extension set \
  --resource-group exttest \
  --vm-name exttest \
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --protected-settings '{"fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],"commandToExecute": "./config-music.sh"}'
```

### <a name="azure-cli-examples"></a>Exemplos de CLI do Azure

#### <a name="public-configuration-with-script-file"></a>Configuração pública com o arquivo de script

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],
  "commandToExecute": "./config-music.sh"
}
```

Comando CLI do Azure:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings ./script-config.json
```

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
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
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
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \ 
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings ./script-config.json \
  --protected-settings ./protected-config.json
```

## <a name="troubleshooting"></a>Solução de problemas
Quando a extensão de script personalizado é executada, o script é criado ou baixado em um diretório semelhante ao exemplo a seguir. A saída do comando também é salva nesse diretório nos arquivos `stdout` e `stderr`.

```bash
/var/lib/waagent/custom-script/download/0/
```

Para solucionar problemas, primeiro verifique o Log de Agente do Linux, confira a extensão executada, verifique:

```bash
/var/log/waagent.log 
```

Você deve procurar a execução da extensão, a exibição será algo assim:

```output
2018/04/26 17:47:22.110231 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] [Enable] current handler state is: notinstalled
2018/04/26 17:47:22.306407 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Download, message=Download succeeded, duration=167
2018/04/26 17:47:22.339958 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Initialize extension directory
2018/04/26 17:47:22.368293 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Update settings file: 0.settings
2018/04/26 17:47:22.394482 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Install extension [bin/custom-script-shim install]
2018/04/26 17:47:23.432774 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Install, message=Launch command succeeded: bin/custom-script-shim install, duration=1007
2018/04/26 17:47:23.476151 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Enable extension [bin/custom-script-shim enable]
2018/04/26 17:47:24.516444 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Enable, message=Launch command succeeded: bin/custom-sc
```

Alguns pontos a serem observados:
1. Habilitar é quando o comando é iniciado.
2. O download está relacionado ao download do pacote de extensão CustomScript do Azure, não aos arquivos de script especificados no fileUris.


A extensão de script do Azure produz um log, que pode ser encontrado aqui:

```bash
/var/log/azure/custom-script/handler.log
```

Você deve procurar a execução individual; ela terá uma aparência semelhante a:

```output
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=start
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=pre-check
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="comparing seqnum" path=mrseq
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="seqnum saved" path=mrseq
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="reading configuration"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="read configuration"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="validating json schema"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="json schema valid"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="parsing configuration json"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="parsed configuration json"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="validating configuration logically"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="validated configuration"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="creating output directory" path=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="created output directory"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 files=1
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 file=0 event="download start"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 file=0 event="download complete" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="executing command" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="executing protected commandToExecute" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="executed command" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=enabled
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=end
```

Aqui, você pode ver:
* O início do comando Habilitar é este log
* As configurações passadas para a extensão
* A extensão baixando o arquivo e o resultado disso.
* O comando em execução e o resultado.

Você também pode recuperar o estado de execução da extensão de script personalizado, incluindo os argumentos reais passados como o `commandToExecute` usando CLI do Azure:

```azurecli
az vm extension list -g myResourceGroup --vm-name myVM
```

A saída se parece com o seguinte texto:

```output
[
  {
    "autoUpgradeMinorVersion": true,
    "forceUpdateTag": null,
    "id": "/subscriptions/subscriptionid/resourceGroups/rgname/providers/Microsoft.Compute/virtualMachines/vmname/extensions/customscript",
    "resourceGroup": "rgname",
    "settings": {
      "commandToExecute": "sh script.sh > ",
      "fileUris": [
        "https://catalogartifact.azureedge.net/publicartifacts/scripts/script.sh",
        "https://catalogartifact.azureedge.net/publicartifacts/scripts/script.sh"
      ]
    },
    "tags": null,
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "typeHandlerVersion": "2.0",
    "virtualMachineExtensionType": "CustomScript"
  },
  {
    "autoUpgradeMinorVersion": true,
    "forceUpdateTag": null,
    "id": "/subscriptions/subscriptionid/resourceGroups/rgname/providers/Microsoft.Compute/virtualMachines/vmname/extensions/OmsAgentForLinux",
    "instanceView": null,
    "location": "eastus",
    "name": "OmsAgentForLinux",
    "protectedSettings": null,
    "provisioningState": "Succeeded",
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "resourceGroup": "rgname",
    "settings": {
      "workspaceId": "workspaceid"
    },
    "tags": null,
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "typeHandlerVersion": "1.0",
    "virtualMachineExtensionType": "OmsAgentForLinux"
  }
]
```

## <a name="next-steps"></a>Próximas etapas
Para ver o código, os problemas atuais e as versões, consulte [custom-script-extension-linux repo](https://github.com/Azure/custom-script-extension-linux).
