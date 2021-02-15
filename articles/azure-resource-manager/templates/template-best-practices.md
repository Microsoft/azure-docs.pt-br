---
title: Práticas recomendadas para modelos
description: Descreve as abordagens recomendadas para a criação de modelos de Azure Resource Manager (modelos ARM). Oferece sugestões para evitar problemas comuns ao usar os modelos.
ms.topic: conceptual
ms.date: 12/01/2020
ms.openlocfilehash: 583a113df9cdb1951daf1002dd69531f050cfb54
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99257990"
---
# <a name="arm-template-best-practices"></a>Práticas recomendadas do modelo ARM

Este artigo mostra como usar as práticas recomendadas ao construir seu modelo de Azure Resource Manager (modelo ARM). Essas recomendações ajudam a evitar problemas comuns ao usar um modelo ARM para implantar uma solução.

## <a name="template-limits"></a>Limites de modelo

Limite o tamanho do modelo para 4 MB e cada arquivo de parâmetro para 64 KB. O limite de 4 MB se aplica ao estado final do modelo depois que ele é expandido com definições de recursos iterativos e valores para variáveis e parâmetros.

Você também está limitado a:

* 256 parâmetros
* 256 variáveis
* 800 recursos (incluindo a contagem de cópias)
* 64 valores de saída
* 24.576 caracteres em uma expressão de modelo

Você pode exceder alguns limites de modelo usando um modelo aninhado. Para obter mais informações, consulte [usando modelos vinculados e aninhados ao implantar recursos do Azure](linked-templates.md). Para reduzir o número de parâmetros, variáveis ou saídas, você pode combinar vários valores em um objeto. Para saber mais, veja [Objetos como parâmetros](/azure/architecture/guide/azure-resource-manager/advanced-templates/objects-as-parameters).

## <a name="resource-group"></a>Resource group

Quando você implanta recursos em um grupo de recursos, o grupo de recursos armazena metadados sobre os recursos. Os metadados são armazenados no local do grupo de recursos.

Se a região do grupo de recursos está temporariamente indisponível, você não pode atualizar os recursos no grupo de recursos porque os metadados não estão disponíveis. Os recursos em outras regiões ainda funcionarão conforme o esperado, mas não será possível atualizá-los. Para minimizar o risco, localize seu grupo de recursos e recursos na mesma região.

## <a name="parameters"></a>Parâmetros

As informações nesta seção podem ser úteis quando você trabalha com [parâmetros](template-parameters.md).

### <a name="general-recommendations-for-parameters"></a>Recomendações gerais para parâmetros

* Minimize o uso de parâmetros. Em vez disso, use variáveis ou valores literais para propriedades que não precisam ser especificados durante a implantação.

* Use minúsculas concatenadas para nomes de parâmetro.

* Use parâmetros para configurações que variam de acordo com o ambiente, como o SKU, o tamanho ou a capacidade.

* Use parâmetros para nomes de recurso que queira especificar para fácil identificação.

* Forneça uma descrição de cada parâmetro nos metadados.

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
   }
   ```

* Use `allowedValues` com moderação. Usá-lo somente quando você deve verificar se alguns valores não estão incluídos nas opções permitidas. Se você usar `allowedValues` muito amplamente, poderá bloquear implantações válidas não mantendo sua lista atualizada.

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

* Use um parâmetro para especificar o local de recursos e defina o valor padrão para `resourceGroup().location`. Fornecer um parâmetro Location permite que os usuários do modelo especifiquem um local onde eles têm permissão para implantar recursos.

   ```json
   "parameters": {
     "location": {
       "type": "string",
       "defaultValue": "[resourceGroup().location]",
       "metadata": {
         "description": "The location in which the resources should be deployed."
       }
     }
   }
   ```

* Não especifique `allowedValues` para o parâmetro de local. Os locais que você especificar podem não estar disponíveis em todas as nuvens.

* Use o valor do parâmetro de local para recursos que provavelmente estarão no mesmo local. Essa abordagem minimiza o número de vezes que os usuários são solicitados a fornecer informações de local.

* Para obter recursos que não estão disponíveis em todos os locais, use um parâmetro separado ou especifique um valor de local literal.

## <a name="variables"></a>Variáveis

As seguintes informações podem ser úteis quando você trabalha com [variáveis](template-variables.md):

* Use o camel case para nomes de variáveis.

* Use variáveis para valores que você precisa usar mais de uma vez em um modelo. Se um valor for usado apenas uma vez, um valor embutido em código facilita a leitura do modelo.

* Use variáveis para valores que você construir de um arranjo complexo de funções de modelo. Seu modelo é mais fácil de ler quando a expressão complexa só aparece em variáveis.

* Você não pode usar a função de [referência](template-functions-resource.md#reference) na `variables` seção do modelo. A `reference` função deriva seu valor do estado de tempo de execução do recurso. No entanto, as variáveis são resolvidas durante a análise inicial do modelo. Construa valores que precisam da `reference` função diretamente na `resources` seção ou `outputs` do modelo.

* Inclua variáveis em nomes de recurso que devem ser exclusivos.

* Use uma [loop de cópia em variáveis](copy-variables.md) para criar um padrão repetido de objetos JSON.

* Remover variáveis não utilizadas.

## <a name="api-version"></a>Versão da API

Defina a `apiVersion` propriedade como uma versão de API embutida em código para o tipo de recurso. Ao criar um novo modelo, recomendamos que você use a versão mais recente da API para um tipo de recurso. Para determinar os valores disponíveis, consulte [referência de modelo](/azure/templates/).

Quando o modelo funciona conforme o esperado, recomendamos que você continue usando a mesma versão de API. Usando a mesma versão de API, você não precisa se preocupar com alterações significativas que podem ser introduzidas em versões posteriores.

Não use um parâmetro para a versão da API. As propriedades e os valores do recurso podem variar por versão da API. O IntelliSense em um editor de código não pode determinar o esquema correto quando a versão da API é definida como um parâmetro. Se você passar uma versão de API que não corresponde às propriedades em seu modelo, a implantação falhará.

Não use variáveis para a versão da API. Em particular, não use a [função de provedores](template-functions-resource.md#providers) para obter dinamicamente as versões da API durante a implantação. A versão da API recuperada dinamicamente pode não corresponder às propriedades em seu modelo.

## <a name="resource-dependencies"></a>Dependências de recursos

Ao decidir quais [dependências](define-resource-dependency.md) definir, use as seguintes diretrizes:

* Use a `reference` função e passe o nome do recurso para definir uma dependência implícita entre os recursos que precisam compartilhar uma propriedade. Não adicione elemento `dependsOn` explícito quando você já tiver definido uma dependência implícita. Essa abordagem reduz o risco de ter dependências desnecessárias. Para obter um exemplo de como definir uma dependência implícita, consulte [funções de referência e lista](define-resource-dependency.md#reference-and-list-functions).

* Defina um recurso filho como dependente do recurso pai.

* Recursos com o [elemento condition](conditional-resource-deployment.md) definido como false são removidos automaticamente da ordem de dependência. Defina as dependências como se o recurso estivesse sempre implantado.

* Coloque as dependências em cascata sem defini-las explicitamente. Por exemplo, sua máquina virtual depende de uma interface de rede virtual e a interface de rede virtual depende de uma rede virtual e de endereços IP públicos. Portanto, a máquina virtual é implantados depois que todos os três recursos, mas não definir explicitamente a máquina virtual como todos os três recursos dependentes. Essa abordagem esclarece a ordem de dependência e facilita a alteração do modelo mais tarde.

* Se um valor pode ser determinado antes da implantação, tente implantar o recurso sem uma dependência. Por exemplo, se um valor de configuração precisa do nome de outro recurso, talvez não seja necessário uma dependência. Este guia nem sempre funciona porque alguns recursos verificar a existência de outro recurso. Se você receber um erro, adicione uma dependência.

## <a name="resources"></a>Recursos

As seguintes informações podem ser úteis quando você trabalha com [recursos](template-syntax.md#resources):

* Para ajudar outros colaboradores a entender a finalidade do recurso, especifique `comments` para cada recurso no modelo.

    ```json
    "resources": [
      {
        "name": "[variables('storageAccountName')]",
        "type": "Microsoft.Storage/storageAccounts",
        "apiVersion": "2019-06-01",
        "location": "[resourceGroup().location]",
        "comments": "This storage account is used to store the VM disks.",
          ...
      }
    ]
    ```

* Se você usar um *ponto de extremidade público* em seu modelo (como um ponto de extremidade público de Armazenamento de Blobs do Azure), o namespace *não deverá ser embutido em código*. Use a `reference` função para recuperar dinamicamente o namespace. Você pode usar essa abordagem para implantar o modelo em ambientes de namespace públicos diferentes, sem alterar manualmente o ponto de extremidade no modelo. Defina a versão da API para a mesma versão que você está usando para a conta de armazenamento em seu modelo.

    ```json
    "diagnosticsProfile": {
      "bootDiagnostics": {
        "enabled": "true",
        "storageUri": "[reference(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').primaryEndpoints.blob]"
      }
    }
    ```

   Se a conta de armazenamento for implantada no mesmo modelo que você está criando e o nome da conta de armazenamento não for compartilhado com outro recurso no modelo, você não precisará especificar o namespace do provedor ou o `apiVersion` ao referenciar o recurso. O exemplo a seguir mostra a sintaxe simplificada.

    ```json
    "diagnosticsProfile": {
      "bootDiagnostics": {
        "enabled": "true",
        "storageUri": "[reference(variables('storageAccountName')).primaryEndpoints.blob]"
      }
    }
    ```

   Você também pode fazer referência a uma conta de armazenamento existente que esteja em um grupo de recursos diferente.

    ```json
    "diagnosticsProfile": {
      "bootDiagnostics": {
        "enabled": "true",
        "storageUri": "[reference(resourceId(parameters('existingResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('existingStorageAccountName')), '2019-06-01').primaryEndpoints.blob]"
      }
    }
    ```

* Atribua endereços IP públicos a uma máquina virtual somente quando um aplicativo exigir. Para se conectar a uma VM (máquina virtual) para depuração, ou para fins administrativos ou de gerenciamento, use regras NAT de entrada, um gateway de rede virtual ou um jumpbox.

     Para saber mais sobre como se conectar às máquinas virtuais, confira:

   * [Executar VMs para uma arquitetura de N camadas no Azure](/azure/architecture/reference-architectures/n-tier/n-tier-sql-server)
   * [Configurar acesso WinRM para VMs no Azure Resource Manager](../../virtual-machines/windows/winrm.md)
   * [Permitir acesso externo à sua VM usando o portal do Azure](../../virtual-machines/windows/nsg-quickstart-portal.md)
   * [Permitir acesso externo à sua VM usando o PowerShell](../../virtual-machines/windows/nsg-quickstart-powershell.md)
   * [Permitir acesso externo à sua VM Linux usando a CLI do Azure](../../virtual-machines/linux/nsg-quickstart.md)

* A `domainNameLabel` propriedade para endereços IP públicos deve ser exclusiva. O `domainNameLabel` valor deve ter entre 3 e 63 caracteres de comprimento e seguir as regras especificadas por esta expressão regular: `^[a-z][a-z0-9-]{1,61}[a-z0-9]$` . Como a `uniqueString` função gera uma cadeia de caracteres com 13 caracteres de comprimento, o `dnsPrefixString` parâmetro é limitado a 50 caracteres.

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

* Quando você adiciona uma senha a uma extensão de script personalizado, use a `commandToExecute` Propriedade na `protectedSettings` propriedade.

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
   > Para garantir que os segredos sejam criptografados quando forem passados como parâmetros para VMs e extensões, use a `protectedSettings` propriedade das extensões relevantes.

* Especifique valores explícitos para propriedades que têm valores padrão que podem mudar ao longo do tempo. Por exemplo, se você estiver implantando um cluster AKS, poderá especificar ou omitir a `kubernetesVersion` propriedade. Se você não especificá-lo, [o cluster será padronizado para a versão secundária N-1 e o patch mais recente](../../aks/supported-kubernetes-versions.md#azure-portal-and-cli-versions). Quando você implanta o cluster usando um modelo ARM, esse comportamento padrão pode não ser o esperado. Reimplantar o modelo pode fazer com que o cluster seja atualizado para uma nova versão do kubernetes inesperadamente. Em vez disso, considere especificar um número de versão explícito e, em seguida, alterá-lo manualmente quando estiver pronto para atualizar o cluster.

## <a name="use-test-toolkit"></a>Usar o kit de ferramentas de teste

O kit de ferramentas de teste do modelo ARM é um script que verifica se o modelo usa práticas recomendadas. Quando o modelo não está em conformidade com as práticas recomendadas, ele retorna uma lista de avisos com alterações sugeridas. O kit de ferramentas de teste pode ajudá-lo a aprender como implementar práticas recomendadas em seu modelo.

Depois de concluir o modelo, execute o kit de ferramentas de teste para ver se há maneiras de melhorar sua implementação. Para obter mais informações, consulte [usar o kit de ferramentas de teste do modelo ARM](test-toolkit.md).

## <a name="next-steps"></a>Próximas etapas

* Para obter informações sobre a estrutura do arquivo de modelo, consulte [entender a estrutura e a sintaxe de modelos ARM](template-syntax.md).
* Para obter recomendações sobre como criar modelos que funcionam em todos os ambientes de nuvem do Azure, consulte [desenvolver modelos de ARM para consistência de nuvem](templates-cloud-consistency.md).
