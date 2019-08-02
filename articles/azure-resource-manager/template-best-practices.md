---
title: Práticas recomendadas para modelos do Azure Resource Manager
description: Descreve as abordagens recomendadas para a criação de modelos do Azure Resource Manager. Oferece sugestões para evitar problemas comuns ao usar os modelos.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/12/2019
ms.author: tomfitz
ms.openlocfilehash: cdec216187050a449f23f72474e0265acce14c5f
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67867385"
---
# <a name="azure-resource-manager-template-best-practices"></a>Práticas recomendadas para modelos do Azure Resource Manager

Este artigo fornece recomendações sobre como criar o modelo do Resource Manager. Essas recomendações ajudam você a evitar problemas comuns ao usar um modelo para implantar uma solução.

Para obter recomendações sobre como controlar suas assinaturas do Azure, consulte [Azure enterprise scaffold: Governança de assinatura prescritiva](/azure/architecture/cloud-adoption/appendix/azure-scaffold?toc=%2Fen-us%2Fazure%2Fazure-resource-manager%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json).

Para obter recomendações sobre como criar modelos que funcionam em todos os ambientes de nuvem do Azure, consulte [Desenvolver modelos do Azure Resource Manager para manter a consistência nuvem](templates-cloud-consistency.md).

## <a name="template-limits"></a>Limites de modelo

Limite o tamanho do modelo para 4 MB e cada arquivo de parâmetro para 64 KB. O limite de 4 MB se aplica ao estado final do modelo depois que ele é expandido com definições de recursos iterativos e valores para variáveis e parâmetros. 

Você também está limitado a:

* 256 parâmetros
* 256 variáveis
* 800 recursos (incluindo a contagem de cópias)
* 64 valores de saída
* 24.576 caracteres em uma expressão de modelo

Você pode exceder alguns limites de modelo usando um modelo aninhado. Para saber mais, confira [Uso de modelos vinculados ao implantar recursos do Azure](resource-group-linked-templates.md). Para reduzir o número de parâmetros, variáveis ou saídas, você pode combinar vários valores em um objeto. Para saber mais, veja [Objetos como parâmetros](resource-manager-objects-as-parameters.md).

## <a name="resource-group"></a>Grupo de recursos

Quando você implanta recursos em um grupo de recursos, o grupo de recursos armazena metadados sobre os recursos. Os metadados são armazenados no local do grupo de recursos.

Se a região do grupo de recursos está temporariamente indisponível, você não pode atualizar os recursos no grupo de recursos porque os metadados não estão disponíveis. Os recursos em outras regiões ainda funcionarão conforme o esperado, mas não será possível atualizá-los. Para minimizar o risco, localize seu grupo de recursos e recursos na mesma região.

## <a name="parameters"></a>parâmetros
As informações nesta seção podem ser úteis quando você trabalha com [parâmetros](resource-group-authoring-templates.md#parameters).

### <a name="general-recommendations-for-parameters"></a>Recomendações gerais para parâmetros

* Minimize o uso de parâmetros. Em vez disso, use variáveis ou valores literais para propriedades que não precisam ser especificados durante a implantação.

* Use minúsculas concatenadas para nomes de parâmetro.

* Use parâmetros para as configurações que variam de acordo com o ambiente, como o SKU, tamanho ou capacidade.

* Use parâmetros para nomes de recurso que queira especificar para fácil identificação.

* Forneça uma descrição de cada parâmetro nos metadados:

   ```json
   "parameters": {
       "storageAccountType": {
           "type": "string",
           "metadata": {
               "description": "The type of the new storage account created to store the VM disks."
           }
       }
   }
   ```

* Defina valores padrão para parâmetros que não são confidenciais. Especificando um valor padrão, é mais fácil de implantar o modelo e os usuários do seu modelo veem um exemplo de um valor apropriado. Qualquer valor padrão para um parâmetro deve ser válido para todos os usuários na configuração de implantação padrão. 
   
   ```json
   "parameters": {
        "storageAccountType": {
            "type": "string",
            "defaultValue": "Standard_GRS",
            "metadata": {
                "description": "The type of the new storage account created to store the VM disks."
            }
        }
   }
   ```

* Para especificar um parâmetro opcional, não use uma cadeia de caracteres vazia como um valor padrão. Em vez disso, use um valor literal ou uma expressão de linguagem para construir um valor.

   ```json
   "storageAccountName": {
     "type": "string",
     "defaultValue": "[concat('storage', uniqueString(resourceGroup().id))]",
     "metadata": {
       "description": "Name of the storage account"
     }
   },
   ```

* Não use um parâmetro para a versão de API de um tipo de recurso. Os valores e propriedades do recurso podem variar de acordo com o número de versão. O IntelliSense em um editor de código não pode determinar o esquema correto quando a versão da API é definida como um parâmetro. Em vez disso, codifique a versão da API no modelo.

* Use `allowedValues` com moderação. Usá-lo somente quando você deve verificar se alguns valores não estão incluídos nas opções permitidas. Se você usar `allowedValues` muito em larga escala, você pode bloquear implantações válidas por não manter sua lista atualizada.

* Quando um nome de parâmetro em seu modelo corresponde a um parâmetro no comando de implantação do PowerShell, o Resource Manager resolve esse conflito de nomeação adicionando o sufixo **FromTemplate** para o parâmetro de modelo. Por exemplo, se você incluir um parâmetro nomeado **ResourceGroupName** no modelo, ele entrará em conflito com o parâmetro **ResourceGroupName** no cmdlet [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment). Durante a implantação, você recebe uma solicitação para fornecer um valor para **ResourceGroupNameFromTemplate**.

### <a name="security-recommendations-for-parameters"></a>Recomendações de segurança para parâmetros

* Sempre use parâmetros para nomes de usuário e senhas (ou segredos).

* Use `securestring` para todos os segredos e senhas. Se você passar dados confidenciais em um objeto JSON, use o tipo `secureObject`. Os parâmetros de modelo com os tipos securestring ou secure object não podem ser lidos após a implantação de recursos. 
   
   ```json
   "parameters": {
       "secretValue": {
           "type": "securestring",
           "metadata": {
               "description": "The value of the secret to store in the vault."
           }
       }
   }
   ```

* Não forneça valores padrão para nomes de usuário, senhas ou qualquer valor que requer um tipo `secureString`.

* Não forneça valores padrão para as propriedades que aumentam a área de superfície de ataque do aplicativo.

### <a name="location-recommendations-for-parameters"></a>Recomendações de local para parâmetros

* Use um parâmetro para especificar o local de recursos e defina o valor padrão para `resourceGroup().location`. Fornecer um parâmetro de local permite que os usuários do modelo especificam um local que eles tenham permissão para implantar.

   ```json
   "parameters": {
     "location": {
       "type": "string",
       "defaultValue": "[resourceGroup().location]",
       "metadata": {
         "description": "The location in which the resources should be deployed."
       }
     }
   },
   ```

* Não especifique `allowedValues` para o parâmetro de local. Os locais que você especificar podem não estar disponíveis em todas as nuvens.

* Use o valor do parâmetro de local para recursos que provavelmente estarão no mesmo local. Essa abordagem minimiza o número de vezes que os usuários são solicitados a fornecer informações de local.

* Para obter recursos que não estão disponíveis em todos os locais, use um parâmetro separado ou especifique um valor de local literal.

## <a name="variables"></a>Variáveis

As seguintes informações podem ser úteis quando você trabalha com [variáveis](resource-group-authoring-templates.md#variables):

* Use variáveis para valores que você precisa usar mais de uma vez em um modelo. Se um valor for usado apenas uma vez, um valor embutido em código facilita a leitura do modelo.

* Use variáveis para valores que você construir de um arranjo complexo de funções de modelo. O modelo é mais fácil de ler quando a expressão complexa aparece somente em variáveis.

* Não use variáveis para `apiVersion` em um recurso. A versão da API determina o esquema do recurso. Muitas vezes, é possível alterar a versão sem alterar as propriedades do recurso.

* Não é possível usar a função [reference](resource-group-template-functions-resource.md#reference) na seção de **variáveis** do modelo. A função **reference** deriva seu valor do estado de tempo de execução do recurso. No entanto, as variáveis são resolvidas durante a análise inicial do modelo. Construa valores que precisam da função **reference** diretamente na seção **resources** ou **outputs** do modelo.

* Inclua variáveis em nomes de recurso que devem ser exclusivos.

* Use uma [loop de cópia em variáveis](resource-group-create-multiple.md#variable-iteration) para criar um padrão repetido de objetos JSON.

* Remover variáveis não utilizadas.

## <a name="resource-dependencies"></a>Dependências de recursos

Ao decidir quais são as [dependências](resource-group-define-dependencies.md) a serem definidas, use as seguintes diretrizes:

* Use a função **reference** e transmita o nome de recurso para definir dependências implícita entre os recursos que precisam compartilhar uma propriedade. Não adicione elemento `dependsOn` explícito quando você já tiver definido uma dependência implícita. Essa abordagem reduz o risco de ter dependências desnecessárias.

* Defina um recurso filho como dependente do recurso pai.

* Recursos com o [elemento condition](resource-group-authoring-templates.md#condition) definido como false são removidos automaticamente da ordem de dependência. Defina as dependências como se o recurso estivesse sempre implantado.

* Coloque as dependências em cascata sem defini-las explicitamente. Por exemplo, sua máquina virtual depende de uma interface de rede virtual e a interface de rede virtual depende de uma rede virtual e de endereços IP públicos. Portanto, a máquina virtual é implantados depois que todos os três recursos, mas não definir explicitamente a máquina virtual como todos os três recursos dependentes. Essa abordagem esclarece a ordem de dependência e facilita a alteração do modelo mais tarde.

* Se um valor pode ser determinado antes da implantação, tente implantar o recurso sem uma dependência. Por exemplo, se um valor de configuração precisa do nome de outro recurso, talvez não seja necessário uma dependência. Este guia nem sempre funciona porque alguns recursos verificar a existência de outro recurso. Se você receber um erro, adicione uma dependência.

## <a name="resources"></a>Recursos

As seguintes informações podem ser úteis quando você trabalha com [recursos](resource-group-authoring-templates.md#resources):

* Para ajudar outros colaboradores a entender a finalidade do recurso, especifique **comentários** para cada recurso no modelo:
   
   ```json
   "resources": [
     {
         "name": "[variables('storageAccountName')]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2016-01-01",
         "location": "[resourceGroup().location]",
         "comments": "This storage account is used to store the VM disks.",
         ...
     }
   ]
   ```

* Se você usar um *ponto de extremidade público* em seu modelo (como um ponto de extremidade público de Armazenamento de Blobs do Azure), o namespace *não deverá ser embutido em código*. Use a função **reference** para recuperar dinamicamente o namespace. Você pode usar essa abordagem para implantar o modelo em ambientes de namespace públicos diferentes, sem alterar manualmente o ponto de extremidade no modelo. Defina a versão de API como a mesma versão que você está usando para a conta de armazenamento no modelo:
   
   ```json
   "osDisk": {
       "name": "osdisk",
       "vhd": {
           "uri": "[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
       }
   }
   ```
   
   Se a conta de armazenamento for implantada no mesmo modelo que você estiver criando, você não precisará especificar o namespace do provedor quando referenciar o recurso. O exemplo a seguir mostra a sintaxe simplificada:
   
   ```json
   "osDisk": {
       "name": "osdisk",
       "vhd": {
           "uri": "[concat(reference(variables('storageAccountName'), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
       }
   }
   ```
   
   Se você tiver outros valores no seu modelo que sejam configurados para usar um namespace público, altere esses valores para refletir a mesma função **reference**. Por exemplo, a propriedade **storageUri** do perfil de diagnóstico da máquina virtual:
   
   ```json
   "diagnosticsProfile": {
       "bootDiagnostics": {
           "enabled": "true",
           "storageUri": "[reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob]"
       }
   }
   ```
   
   Você também pode fazer referência a uma conta de armazenamento existente que esteja em um grupo de recursos diferente:

   ```json
   "osDisk": {
       "name": "osdisk", 
       "vhd": {
           "uri":"[concat(reference(resourceId(parameters('existingResourceGroup'), 'Microsoft.Storage/storageAccounts/', parameters('existingStorageAccountName')), '2016-01-01').primaryEndpoints.blob,  variables('vmStorageAccountContainerName'), '/', variables('OSDiskName'),'.vhd')]"
       }
   }
   ```

* Atribua endereços IP públicos a uma máquina virtual somente quando um aplicativo exigir. Para se conectar a uma VM (máquina virtual) para depuração, ou para fins administrativos ou de gerenciamento, use regras NAT de entrada, um gateway de rede virtual ou um jumpbox.
   
     Para saber mais sobre como se conectar às máquinas virtuais, confira:
   
   * [Executar VMs para uma arquitetura de N camadas no Azure](../guidance/guidance-compute-n-tier-vm.md)
   * [Configurar acesso WinRM para VMs no Azure Resource Manager](../virtual-machines/windows/winrm.md)
   * [Permitir acesso externo à sua VM usando o portal do Azure](../virtual-machines/windows/nsg-quickstart-portal.md)
   * [Permitir acesso externo à sua VM usando o PowerShell](../virtual-machines/windows/nsg-quickstart-powershell.md)
   * [Permitir acesso externo à sua VM Linux usando a CLI do Azure](../virtual-machines/virtual-machines-linux-nsg-quickstart.md)

* A propriedade **domainNameLabel** para endereços IP públicos deve ser exclusiva. O valor de **domainNameLabel** deve ter entre 3 e 63 caracteres e seguir as regras especificadas por essa expressão regular: `^[a-z][a-z0-9-]{1,61}[a-z0-9]$`. Como a função **uniqueString** gera uma cadeia de caracteres com tamanho de 13 caracteres, o parâmetro **dnsPrefixString** é limitado a 50 caracteres:

   ```json
   "parameters": {
       "dnsPrefixString": {
           "type": "string",
           "maxLength": 50,
           "metadata": {
               "description": "The DNS label for the public IP address. It must be lowercase. It should match the following regular expression, or it will raise an error: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$"
           }
       }
   },
   "variables": {
       "dnsPrefix": "[concat(parameters('dnsPrefixString'),uniquestring(resourceGroup().id))]"
   }
   ```

* Quando você adiciona uma senha a uma extensão de script personalizada, use a propriedade **commandToExecute** na propriedade **protectedSettings**:
   
   ```json
   "properties": {
       "publisher": "Microsoft.Azure.Extensions",
       "type": "CustomScript",
       "typeHandlerVersion": "2.0",
       "autoUpgradeMinorVersion": true,
       "settings": {
           "fileUris": [
               "[concat(variables('template').assets, '/lamp-app/install_lamp.sh')]"
           ]
       },
       "protectedSettings": {
           "commandToExecute": "[concat('sh install_lamp.sh ', parameters('mySqlPassword'))]"
       }
   }
   ```
   
   > [!NOTE]
   > Para garantir que os segredos sejam criptografados quando passados como parâmetros para VMs e extensões, use a propriedade **protectedSettings** das extensões relevantes.
   > 
   > 

## <a name="outputs"></a>outputs

Se você usar um modelo para criar endereços IP públicos, inclua uma [seção outputs](resource-group-authoring-templates.md#outputs) que retorne detalhes do endereço IP e o FQDN (nome de domínio totalmente qualificado). É possível usar valores de saída para recuperar facilmente detalhes sobre endereços IP públicos e FQDNs após a implantação.

```json
"outputs": {
    "fqdn": {
        "value": "[reference(parameters('publicIPAddresses_name')).dnsSettings.fqdn]",
        "type": "string"
    },
    "ipaddress": {
        "value": "[reference(parameters('publicIPAddresses_name')).ipAddress]",
        "type": "string"
    }
}
```

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre a estrutura do arquivo de modelo do Resource Manager, confira [Noções básicas de estrutura e sintaxe dos modelos do Azure Resource Manager](resource-group-authoring-templates.md).
* Para obter recomendações sobre como criar modelos que funcionam em todos os ambientes de nuvem do Azure, consulte [Desenvolver modelos do Azure Resource Manager para manter a consistência nuvem](templates-cloud-consistency.md).
