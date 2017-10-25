---
title: "Práticas recomendadas para criação de modelos do Resource Manager | Microsoft Docs"
description: Diretrizes para simplificar seus modelos do Azure Resource Manager.
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 31b10deb-0183-47ce-a5ba-6d0ff2ae8ab3
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: tomfitz
ms.openlocfilehash: a23301ba88279af3f7bf4d353ae808e9eeb0900d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="best-practices-for-creating-azure-resource-manager-templates"></a>Práticas recomendadas para criação de modelos do Azure Resource Manager
Estas diretrizes podem ajudar a criar modelos do Azure Resource Manager que sejam confiáveis e fáceis de usar. As diretrizes são apenas sugestões. Elas não são requisitos, exceto onde apontadas. Seu cenário pode exigir uma variação de uma das abordagens ou dos exemplos que se seguem.

## <a name="resource-names"></a>Nomes de recurso
Normalmente, você trabalha com três tipos de nome de recurso no Resource Manager:

* Os nomes de recurso devem ser exclusivos.
* Nomes de recurso que não precisam ser exclusivos, mas você opta por fornecer um nome que possa ajudar a identificar um recurso com base no contexto.
* Nomes de recursos que podem ser genéricos.

 Para obter informações sobre restrições de nome de recurso, confira [Recursos recomendados de convenções de nomenclatura do Azure](../guidance/guidance-naming-conventions.md).

### <a name="unique-resource-names"></a>Nomes de recurso exclusivos
Você deve fornecer um nome de recurso exclusivo para qualquer tipo de recurso que tenha um ponto de extremidade de acesso de dados. Entre os tipos comuns de recurso que exigem um nome exclusivo estão:

* Armazenamento do Azure<sup>1</sup> 
* Recurso de Aplicativos Web do Serviço de Aplicativo do Azure
* SQL Server
* Cofre da Chave do Azure
* Cache Redis do Azure
* Lote do Azure
* Gerenciador de Tráfego do Azure
* Pesquisa do Azure
* Azure HDInsight

<sup>1</sup> Os nomes de conta de armazenamento devem estar em letras minúsculas, ter até 24 caracteres e não incluir hifens.

Se você fornecer um parâmetro para um nome de recurso, será preciso fornecer um nome exclusivo na implantação do recurso. Como opção, você pode criar uma variável que use a função [uniqueString()](resource-group-template-functions-string.md#uniquestring) para gerar um nome. 

Também convém adicionar um prefixo ou sufixo ao resultado **uniqueString**. Modificar o nome exclusivo pode ajudar você a identificar mais facilmente o tipo de recurso pelo nome. Por exemplo, você pode gerar um nome exclusivo para uma conta de armazenamento usando a seguinte variável:

```json
"variables": {
    "storageAccountName": "[concat(uniqueString(resourceGroup().id),'storage')]"
}
```

### <a name="resource-names-for-identification"></a>Nomes de recurso para identificação
Alguns tipos de recurso que talvez você queira nomear não precisam que seus nomes sejam exclusivos. Para esses tipos de recurso, você pode fornecer um nome que identifique o contexto do recurso e o tipo de recurso. Forneça um nome descritivo que ajude a identificar o recurso em uma lista de recursos. Se precisar usar um nome de recurso diferente para diferentes implantações, é possível usar um parâmetro para o nome:

```json
"parameters": {
    "vmName": { 
        "type": "string",
        "defaultValue": "demoLinuxVM",
        "metadata": {
            "description": "The name of the VM to create."
        }
    }
}
```

Se não for necessário passar um nome durante a implantação, você poderá usar uma variável: 

```json
"variables": {
    "vmName": "demoLinuxVM"
}
```

Também é possível usar um valor embutido em código:

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "name": "demoLinuxVM",
  ...
}
```

### <a name="generic-resource-names"></a>Nomes de recurso genéricos
Para os tipos de recurso que você mais acessa por meio de um recurso diferente, é possível usar um nome genérico que seja embutido em código no modelo. Por exemplo, você pode definir um nome genérico padrão para regras de firewall em um servidor SQL:

```json
{
    "type": "firewallrules",
    "name": "AllowAllWindowsAzureIps",
    ...
}
```

## <a name="parameters"></a>parâmetros
As seguintes informações podem ser úteis quando você trabalha com parâmetros:

* Minimize o uso de parâmetros. Sempre que possível, use uma variável ou um valor literal. Use parâmetros somente para estes cenários:
   
   * Configurações em que queira usar variações de acordo com o ambiente (SKU, tamanho e capacidade).
   * Nomes de recurso que queira especificar para fácil identificação.
   * Valores que usa com frequência para concluir outras tarefas (como um nome de usuário de administrador).
   * Segredos (como senhas).
   * O número ou a matriz de valores a ser usado durante a criação de várias instâncias de um tipo de recurso.
* Use minúsculas concatenadas para nomes de parâmetro.
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

* Defina valores padrão para parâmetros (exceto senhas e chaves SSH):
   
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

* Use **SecureString** para todos os segredos e senhas: 
   
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

* Sempre que possível, não use um parâmetro para especificar o local. Em vez disso, use a propriedade **location** do grupo de recursos. Ao usar a expressão **resourceGroup().location** para todos os seus recursos, os recursos no modelo são implantados no mesmo local do grupo de recursos:
   
   ```json
   "resources": [
     {
         "name": "[variables('storageAccountName')]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2016-01-01",
         "location": "[resourceGroup().location]",
         ...
     }
   ]
   ```
   
   Se um tipo de recurso tiver suporte em apenas um número limitado de locais, talvez seja conveniente especificar um local válido diretamente no modelo. Se você precisar usar um parâmetro **location**, compartilhe esse valor de parâmetro o máximo possível com os recursos que provavelmente estarão no mesmo local. Isso minimiza o número de vezes que os usuários são solicitados a fornecer informações de local.
* Evite usar um parâmetro ou variável para a versão de API de um tipo de recurso. Os valores e propriedades do recurso podem variar de acordo com o número de versão. O IntelliSense em um editor de código não pode determinar o esquema correto quando a versão da API é definida como um parâmetro ou uma variável. Em vez disso, codifique a versão da API no modelo.

## <a name="variables"></a>variáveis
As seguintes informações podem ser úteis quando você trabalha com variáveis:

* Use variáveis para valores que você precisa usar mais de uma vez em um modelo. Se um valor for usado apenas uma vez, um valor embutido em código facilita a leitura do modelo.
* Não é possível usar a função [reference](resource-group-template-functions-resource.md#reference) na seção de **variables** do modelo. A função **reference** deriva seu valor do estado de tempo de execução do recurso. No entanto, as variáveis são resolvidas durante a análise inicial do modelo. Construa valores que precisam da função **reference** diretamente na seção **resources** ou **outputs** do modelo.
* Inclua variáveis em nomes de recurso que devem ser exclusivos, conforme descrito em [Nomes de recurso](#resource-names).
* Você pode agrupar variáveis em objetos complexos. Use o formato **variable.subentry** para fazer referência a um valor de um objeto complexo. O agrupamento de variáveis pode ajudar a rastrear variáveis relacionadas. Ele também melhora a legibilidade do modelo. Aqui está um exemplo:
   
   ```json
   "variables": {
       "storage": {
           "name": "[concat(uniqueString(resourceGroup().id),'storage')]",
           "type": "Standard_LRS"
       }
   },
   "resources": [
     {
         "type": "Microsoft.Storage/storageAccounts",
         "name": "[variables('storage').name]",
         "apiVersion": "2016-01-01",
         "location": "[resourceGroup().location]",
         "sku": {
             "name": "[variables('storage').type]"
         },
         ...
     }
   ]
   ```
   
   > [!NOTE]
   > Um objeto complexo não pode conter uma expressão que faça referência a um valor de um objeto complexo. Defina uma variável separada para essa finalidade.
   > 
   > 
   
     Para obter exemplos avançados de como usar objetos complexos como variáveis, confira [Compartilhar estado nos modelos do Azure Resource Manager](best-practices-resource-manager-state.md).

## <a name="resources"></a>Recursos
As seguintes informações podem ser úteis quando você trabalha com recursos:

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

* Você pode usar marcação para adicionar metadados aos recursos. Use metadados para adicionar informações sobre os recursos. Por exemplo, você pode adicionar metadados para registrar detalhes de cobrança para um recurso. Para obter mais informações, veja [Usando marcas para organizar os recursos do Azure](resource-group-using-tags.md).
* Se você usar um *ponto de extremidade público* em seu modelo (como um ponto de extremidade público de Armazenamento de Blobs do Azure), o namespace *não deverá ser embutido em código*. Use a função **reference** para recuperar dinamicamente o namespace. Você pode usar essa abordagem para implantar o modelo em ambientes de namespace públicos diferentes, sem alterar manualmente o ponto de extremidade no modelo. Defina a versão da API para a mesma versão que você está usando para a conta de armazenamento em seu modelo:
   
   ```json
   "osDisk": {
       "name": "osdisk",
       "vhd": {
           "uri": "[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
       }
   }
   ```
   
   Se a conta de armazenamento for implantada no mesmo modelo que você estiver criando, não será preciso especificar o namespace do provedor quando referenciar o recurso. Esta é a sintaxe simplificada:
   
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
Se você usar um modelo para criar endereços IP públicos, inclua uma seção **outputs** que retorne detalhes do endereço IP e o FQDN (nome de domínio totalmente qualificado). É possível usar valores de saída para recuperar facilmente detalhes sobre endereços IP públicos e FQDNs após a implantação. Ao fazer referência ao recurso, use a versão da API que foi usada para criá-lo: 

```json
"outputs": {
    "fqdn": {
        "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName')), '2016-07-01').dnsSettings.fqdn]",
        "type": "string"
    },
    "ipaddress": {
        "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName')), '2016-07-01').ipAddress]",
        "type": "string"
    }
}
```

## <a name="single-template-vs-nested-templates"></a>Modelo único vs. modelos aninhados
Para implantar sua solução, você pode usar um único modelo, ou um modelo principal com vários modelos aninhados. Os modelos aninhados são comuns em cenários mais avançados. Usar um modelo aninhado proporciona as seguintes vantagens:

* Você pode dividir uma solução em componentes direcionados.
* Você pode reutilizar modelos aninhados com diferentes modelos principais.

Caso opte por usar modelos aninhados, as diretrizes a seguir podem ajudar a padronizar o design do modelo. Essas diretrizes se baseiam nos [padrões de design para modelos do Azure Resource Manager](best-practices-resource-manager-design-templates.md). É recomendável um design que tenha os seguintes modelos:

* **Modelo principal** (azuredeploy.json). Use para os parâmetros de entrada.
* **Modelo de recursos compartilhados**. Use para implantar recursos compartilhados que todos os outros recursos usam (por exemplo, rede virtual e conjuntos de disponibilidade). Use a expressão **dependsOn** para garantir que esse modelo seja implantado antes de outros modelos.
* **Modelo de recursos opcionais**. Use para implantar condicionalmente recursos baseados em um parâmetro (por exemplo, um jumpbox).
* **Modelo de recursos de membro**. Cada tipo de instância dentro de uma camada de aplicativo tem sua própria configuração. Dentro de uma camada, você pode definir tipos diferentes de instância. (Por exemplo, a primeira instância cria um cluster e as instâncias adicionais são incluídas no cluster existente.) Cada tipo de instância tem seu próprio modelo de implantação.
* **Scripts**. Scripts amplamente reutilizáveis são aplicáveis a cada tipo de instância (por exemplo, inicializar e formatar discos adicionais). Os scripts personalizados que você cria para uma finalidade específica de personalização são diferentes, com base no tipo de instância.

![Modelo aninhado](./media/resource-manager-template-best-practices/nestedTemplateDesign.png)

Para saber mais, confira [Usando modelos vinculados com o Azure Resource Manager](resource-group-linked-templates.md).

## <a name="conditionally-link-to-nested-templates"></a>Criar link condicional para modelos aninhados
Você pode usar um parâmetro para criar link condicional para modelos aninhados. O parâmetro torna-se parte do URI do modelo:

```json
"parameters": {
    "newOrExisting": {
        "type": "String",
        "allowedValues": [
            "new",
            "existing"
        ]
    }
},
"variables": {
    "templatelink": "[concat('https://raw.githubusercontent.com/Contoso/Templates/master/',parameters('newOrExisting'),'StorageAccount.json')]"
},
"resources": [
    {
        "apiVersion": "2015-01-01",
        "name": "nestedTemplate",
        "type": "Microsoft.Resources/deployments",
        "properties": {
            "mode": "incremental",
            "templateLink": {
                "uri": "[variables('templatelink')]",
                "contentVersion": "1.0.0.0"
            },
            "parameters": {
            }
        }
    }
]
```

## <a name="template-format"></a>Formato de modelo
É uma boa prática passar o modelo por meio de um validador JSON. Um validador pode ajudar a remover vírgulas, parênteses e colchetes irrelevantes que podem gerar um erro durante a implantação. Experimente o [JSONLint](http://jsonlint.com/) ou um pacote linter para seu ambiente de edição favorito (Visual Studio Code, Atom, Sublime Text, Visual Studio).

Também convém formatar o JSON para melhorar a legibilidade. Você pode usar um pacote de formatador JSON para seu editor local. No Visual Studio, para formatar o documento, pressione **Ctrl + K, Ctrl + D**. No Visual Studio Code, pressione **Alt + Shift + F**. Se o seu editor local não formata o documento, use um [formatador online](https://www.bing.com/search?q=json+formatter).

## <a name="next-steps"></a>Próximas etapas
* Para obter diretrizes sobre a arquitetura de sua solução para máquinas virtuais, confira [Run a Windows VM in Azure](../guidance/guidance-compute-single-vm.md) (Executar uma VM Windows no Azure) e [Run a Linux VM in Azure](../guidance/guidance-compute-single-vm-linux.md) (Executar uma VM Linux no Azure).
* Para obter orientação sobre como configurar uma conta de armazenamento, confira [Lista de verificação de desempenho e escalabilidade do armazenamento do Azure](../storage/common/storage-performance-checklist.md).
* Para saber mais sobre como uma empresa pode usar o Resource Manager para gerenciar assinaturas com eficiência, confira [Andaime empresarial do Azure: governança de assinatura prescritiva](resource-manager-subscription-governance.md).

