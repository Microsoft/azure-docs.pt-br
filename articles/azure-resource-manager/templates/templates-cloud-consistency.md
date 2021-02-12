---
title: Reutilizar modelos entre nuvens
description: Desenvolva modelos de Azure Resource Manager (modelos ARM) que funcionam consistentemente para diferentes ambientes de nuvem. Crie modelos ou atualize modelos existentes para o Azure Stack.
author: marcvaneijk
ms.topic: conceptual
ms.date: 12/09/2018
ms.author: mavane
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 806556a8da97ec84fe8141b95198b4a7da95c062
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2020
ms.locfileid: "96928351"
---
# <a name="develop-arm-templates-for-cloud-consistency"></a>Desenvolver modelos ARM para consistência de nuvem

[!INCLUDE [requires-azurerm](../../../includes/requires-azurerm.md)]

Um dos principais benefícios do Azure é a consistência. Os investimentos de desenvolvimento em um local são reutilizáveis em outro. Um modelo de Azure Resource Manager (modelo ARM) torna suas implantações consistentes e reproduzíveis entre ambientes, incluindo as nuvens globais do Azure, do Azure soberanas e Azure Stack. No entanto, para reutilizar modelos entre nuvens, você precisa considerar dependências específicas da nuvem, como explica este guia.

A Microsoft oferece serviços de nuvem inteligentes e prontos para a empresa em muitos locais, incluindo:

* A plataforma Azure global com suporte de uma rede cada vez maior de datacenters gerenciados pela Microsoft em regiões no mundo todo.
* Nuvens soberanas isoladas como Azure Alemanha, Azure governamental e Azure China 21Vianet. As nuvens soberanas fornecem uma plataforma consistente com a maioria dos mesmos recursos excelentes aos quais os clientes do Azure global têm acesso.
* Azure Stack, uma plataforma de nuvem híbrida que possibilita entregar serviços do Azure por meio do datacenter de sua empresa. As empresas podem configurar o Azure Stack em seus próprios datacenters ou consumir os Serviços do Azure por meio de provedores de serviço, executando o Azure Stack em suas instalações (também conhecidas como regiões hospedadas).

No centro de todas essas nuvens, o Azure Resource Manager fornece uma API que permite a comunicação de uma ampla variedade de interfaces do usuário com a plataforma Azure. Essa API oferece funcionalidades avançadas de infraestrutura como código. Qualquer tipo de recurso que está disponível na plataforma de nuvem do Azure pode ser implantado e configurado com o Azure Resource Manager. Com um único modelo, você pode implantar e configurar seu aplicativo completo para um estado final operacional.

![Ambientes do Azure](./media/templates-cloud-consistency/environments.png)

A consistência do Azure global, as nuvens soberanas, as nuvens hospedadas e uma nuvem em seu datacenter ajuda você a se beneficiar do Azure Resource Manager. Você pode reutilizar seus investimentos de desenvolvimento nessas nuvens quando definir a implantação e a configuração de recursos baseados em modelos.

No entanto, embora as nuvens globais, soberanas, hospedadas e híbridas forneçam serviços consistentes, nem todas as nuvens são idênticas. Como resultado, você pode criar um modelo com dependências dos recursos disponíveis apenas em uma nuvem específica.

O restante deste guia descreve as áreas a serem consideradas ao planejar desenvolver novos modelos de ARM existentes ou atualizá-los para Azure Stack. Em geral, sua lista de verificação deve incluir os seguintes itens:

* Verifica se as funções, os pontos de extremidade, os serviços e outros recursos no modelo estão disponíveis nos locais de implantação de destino.
* Armazene modelos aninhados e artefatos de configuração em locais acessíveis, garantindo o acesso entre nuvens.
* Use referências dinâmicas em vez de elementos e vínculos hard-coding.
* Garanta que os parâmetros de modelo usados funcionam nas nuvens de destino.
* Verifique se as propriedades específicas do recurso estão disponíveis nas nuvens de destino.

Para obter uma introdução aos modelos do ARM, consulte [implantação de modelo](overview.md).

## <a name="ensure-template-functions-work"></a>Garantir o funcionamento das funções de modelo

A sintaxe básica de um modelo ARM é JSON. Os modelos usam um superconjunto do JSON, estendendo a sintaxe com expressões e funções. O processador de linguagem do modelo é frequentemente atualizado para dar suporte a funções de modelo adicionais. Para obter uma explicação detalhada das funções de modelo disponíveis, consulte [ARM template Functions](template-functions.md).

As novas funções de modelo introduzidas no Azure Resource Manager não ficam imediatamente disponíveis nas nuvens soberanas ou no Azure Stack. Para implantar um modelo com êxito, todas as funções referenciadas no modelo precisam estar disponíveis na nuvem de destino.

As funcionalidades do Azure Resource Manager sempre serão introduzidas primeiro no Azure global. Use o seguinte script do PowerShell para verificar se as funções de modelo recém-introduzidas também estão disponíveis no Azure Stack:

1. Faça um clone do repositório GitHub: [https://github.com/marcvaneijk/arm-template-functions](https://github.com/marcvaneijk/arm-template-functions) .

1. Quando você tiver um clone local do repositório, conecte-se ao Azure Resource Manager do destino com o PowerShell.

1. Importe o módulo psm1 e execute o cmdlet Test-AzureRmTemplateFunctions:

   ```powershell
   # Import the module
   Import-module <path to local clone>\AzTemplateFunctions.psm1

   # Execute the Test-AzureRmTemplateFunctions cmdlet
   Test-AzureRmTemplateFunctions -path <path to local clone>
   ```

O script implanta vários modelos minimizados, cada um contendo apenas funções de modelo exclusivas. A saída do script relata as funções de modelo compatíveis e não disponíveis.

## <a name="working-with-linked-artifacts"></a>Trabalhando com artefatos vinculados

Um modelo pode conter referências a artefatos vinculados e conter um recurso de implantação que é vinculado a outro modelo. Os modelos vinculados (também conhecidos como modelos aninhados) são recuperados pelo Resource Manager em runtime. Um modelo também pode conter referências a artefatos para extensões de VM (máquina virtual). Esses artefatos são recuperados pela extensão de VM em execução na VM para configuração da extensão de VM durante a implantação de modelo.

As seções a seguir descrevem considerações para consistência de nuvem durante o desenvolvimento de modelos que incluem artefatos que são externos ao modelo de implantação principal.

### <a name="use-nested-templates-across-regions"></a>Usar modelos aninhados entre regiões

Os modelos podem ser decompostos em modelos pequenos e reutilizáveis, cada um tendo uma finalidade específica e podendo ser reutilizado em vários cenários de implantação. Para executar uma implantação, especifique um único modelo, conhecido como o modelo principal ou mestre. Ele especifica os recursos a serem implantados, como redes virtuais, VMs e aplicativos Web. O modelo principal também pode conter um vínculo para outro modelo, o que significa que você pode aninhar modelos. Da mesma forma, um modelo aninhado pode conter vínculos para outros modelos. Você pode aninhar modelos com até cinco níveis de profundidade.

O seguinte código mostra como o parâmetro templateLink refere-se a um modelo aninhado:

```json
"resources": [
  {
     "type": "Microsoft.Resources/deployments",
     "apiVersion": "2017-05-10",
     "name": "linkedTemplate",
     "properties": {
       "mode": "incremental",
       "templateLink": {
          "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/vNet.json",
          "contentVersion":"1.0.0.0"
       }
     }
  }
]
```

O Azure Resource Manager avalia o modelo principal em runtime e recupera e avalia cada modelo aninhado. Depois que todos os modelos aninhados são recuperados, o modelo é nivelado e o processamento adicional é iniciado.

### <a name="make-linked-templates-accessible-across-clouds"></a>Tornar os modelos vinculados acessíveis entre nuvens

Considere o local e a forma de armazenamento dos modelos vinculados usados. Em runtime, o Azure Resource Manager busca os modelos vinculados e, portanto, exige o acesso direto a eles. Uma prática comum é usar o GitHub para armazenar os modelos aninhados. Um repositório GitHub pode conter arquivos que são acessíveis publicamente por meio de uma URL. Embora essa técnica funcione bem para a nuvem pública e as nuvens soberanas, um ambiente do Azure Stack pode estar localizado em uma rede corporativa ou em um local remoto desconectado, sem nenhum acesso de saída à Internet. Nesses casos, o Azure Resource Manager não consegue recuperar os modelos aninhados.

Uma melhor prática para implantações entre nuvens é armazenar os modelos vinculados em um local acessível para a nuvem de destino. O ideal é que todos os artefatos de implantação sejam mantidos em um pipeline de CI/CD (integração contínua/desenvolvimento contínuo) e implantados por meio dele. Como alternativa, você pode armazenar os modelos aninhados em um contêiner de armazenamento de blobs, do qual o Azure Resource Manager possa recuperá-los.

Como o Armazenamento de Blobs em cada nuvem usa um FQDN (nome de domínio totalmente qualificado) de um ponto de extremidade diferente, configure o modelo com o local dos modelos vinculados com dois parâmetros. Os parâmetros podem aceitar a entrada do usuário no momento da implantação. Normalmente, os modelos são criados e compartilhados por várias pessoas; portanto, uma melhor prática é usar um nome padrão para esses parâmetros. As convenções de nomenclatura ajudam a tornar os modelos mais reutilizáveis entre regiões, nuvens e autores.

No código a seguir, `_artifactsLocation` é usado para apontar para um único local, que contém todos os artefatos relacionados à implantação. Observe que um valor padrão é fornecido. No momento da implantação, se nenhum valor de entrada for especificado para `_artifactsLocation`, o valor padrão será usado. O `_artifactsLocationSasToken` é usado como entrada para o `sasToken`. O valor padrão deve ser uma cadeia de caracteres vazia para cenários em que o `_artifactsLocation` não é protegido – por exemplo, um repositório GitHub público.

```json
"parameters": {
  "_artifactsLocation": {
    "type": "string",
    "metadata": {
      "description": "The base URI where artifacts required by this template are located."
    },
    "defaultValue": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-custom-script-windows/"
  },
  "_artifactsLocationSasToken": {
    "type": "securestring",
    "metadata": {
      "description": "The sasToken required to access _artifactsLocation."
    },
    "defaultValue": ""
  }
}
```

Em todo o modelo, os vínculos são gerados pela combinação do URI base (do parâmetro `_artifactsLocation`) com um caminho relativo ao artefato e o `_artifactsLocationSasToken`. O seguinte código mostra como especificar o vínculo para o modelo aninhado usando a função de modelo URI:

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2019-10-01",
    "name": "shared",
    "properties": {
      "mode": "Incremental",
      "templateLink": {
        "uri": "[uri(parameters('_artifactsLocation'), concat('nested/vnet.json', parameters('_artifactsLocationSasToken')))]",
        "contentVersion": "1.0.0.0"
      }
    }
  }
]
```

Usando essa abordagem, o valor padrão para o parâmetro `_artifactsLocation` é usado. Se os modelos vinculados precisarem ser recuperados de outro local, o parâmetro de entrada poderá ser usado no momento da implantação para substituir o valor padrão – nenhuma alteração ao modelo em si é necessária.

### <a name="use-_artifactslocation-instead-of-hardcoding-links"></a>Usar _artifactsLocation em vez de vínculos hard-coding

Além de ser usada para modelos aninhados, a URL no parâmetro `_artifactsLocation` é usada como base de todos os artefatos relacionados de um modelo de implantação. Algumas extensões de VM incluem um vínculo para um script armazenado fora do modelo. Para essas extensões, você não deve embutir os vínculos em código. Por exemplo, as extensões Script Personalizado e DSC do PowerShell podem ser vinculadas a um script externo no GitHub, conforme mostrado abaixo:

```json
"properties": {
  "publisher": "Microsoft.Compute",
  "type": "CustomScriptExtension",
  "typeHandlerVersion": "1.9",
  "autoUpgradeMinorVersion": true,
  "settings": {
    "fileUris": [
      "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
    ]
  }
}
```

A codificação em código dos vínculos para o script potencialmente impede que o modelo seja implantado com êxito em outro local. Durante a configuração do recurso de VM, o agente de VM em execução dentro da VM inicia um download de todos os scripts vinculados na extensão de VM e, em seguida, armazena os scripts no disco local da VM. Essa abordagem funciona como os vínculos de modelo aninhado explicados anteriormente na seção "Usar modelos aninhados entre regiões".

O Resource Manager recupera modelos aninhados em runtime. Para extensões de VM, a recuperação de artefatos externos é executada pelo agente de VM. Além do iniciador diferente da recuperação de artefato, a solução na definição do modelo é a mesma. Use o parâmetro _artifactsLocation com um valor padrão do caminho base, no qual todos os artefatos são armazenados (incluindo os scripts de extensão de VM) e o parâmetro `_artifactsLocationSasToken` para a entrada do sasToken.

```json
"parameters": {
  "_artifactsLocation": {
    "type": "string",
    "metadata": {
      "description": "The base URI where artifacts required by this template are located."
    },
    "defaultValue": "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/"
  },
  "_artifactsLocationSasToken": {
    "type": "securestring",
    "metadata": {
      "description": "The sasToken required to access _artifactsLocation."
    },
    "defaultValue": ""
  }
}
```

Para construir o URI absoluto de um artefato, o método preferencial é usar a função de modelo URI, em vez da função de modelo CONCAT. Substituindo os vínculos embutidos em código para os scripts na extensão de VM pela função de modelo URI, essa funcionalidade no modelo é configurada para consistência de nuvem.

```json
"properties": {
  "publisher": "Microsoft.Compute",
  "type": "CustomScriptExtension",
  "typeHandlerVersion": "1.9",
  "autoUpgradeMinorVersion": true,
  "settings": {
    "fileUris": [
      "[uri(parameters('_artifactsLocation'), concat('scripts/configure-music-app.ps1', parameters('_artifactsLocationSasToken')))]"
    ]
  }
}
```

Com essa abordagem, todos os artefatos de implantação, incluindo scripts de configuração, podem ser armazenados no mesmo local com o próprio modelo. Para alterar o local de todos os links, você só precisa especificar uma URL base diferente para os _parâmetros artifactsLocation_.

## <a name="factor-in-differing-regional-capabilities"></a>Incluir funcionalidades regionais diferentes

Com o desenvolvimento ágil e o fluxo contínuo de atualizações e novos serviços introduzidos no Azure, [as regiões podem ser distintas](https://azure.microsoft.com/regions/services/) na disponibilidade de serviços ou atualizações. Após um rigoroso teste interno, os novos serviços ou as atualizações dos serviços existentes geralmente são apresentados para um público pequeno de clientes que participam de um programa de validação. Após a validação bem-sucedida dos clientes, os serviços ou as atualizações são disponibilizados em um subconjunto de regiões do Azure e, em seguida, introduzidos em mais regiões, distribuídos para nuvens soberanas e potencialmente disponibilizados para os clientes do Azure Stack também.

Sabendo que as nuvens e as regiões do Azure podem ser diferentes em seus serviços disponíveis, você pode tomar algumas decisões proativas sobre seus modelos. Um bom lugar para começar é examinar os provedores de recursos disponíveis para uma nuvem. Um provedor de recursos informa o conjunto de recursos e de operações disponíveis para um serviço do Azure.

Um modelo implanta e configura recursos. Um tipo de recurso é fornecido por um provedor de recursos. Por exemplo, o provedor de recursos de computação (Microsoft.Compute) fornece vários tipos de recurso, como virtualMachines e availabilitySets. Cada provedor de recursos fornece uma API ao Azure Resource Manager definida por um contrato comum, permitindo uma experiência de criação consistente e unificada em todos os provedores de recursos. No entanto, um provedor de recursos disponível no Azure global pode não estar disponível em uma nuvem soberana ou uma região do Azure Stack.

![Provedores de recursos](./media/templates-cloud-consistency/resource-providers.png)

Para verificar se os provedores de recursos disponíveis em determinada nuvem, execute o seguinte script na CLI ([interface de linha de comando](/cli/azure/install-azure-cli)) do Azure:

```azurecli-interactive
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

Use também o seguinte cmdlet do PowerShell para ver os provedores de recursos disponíveis:

```azurepowershell-interactive
Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
```

### <a name="verify-the-version-of-all-resource-types"></a>Verificar a versão de todos os tipos de recurso

Um conjunto de propriedades é comum a todos os tipos de recursos, mas cada recurso também tem suas próprias propriedades específicas. Periodicamente, novos recursos e propriedades relacionadas são adicionados aos tipos de recurso existentes por meio de uma nova versão de API. Um recurso em um modelo tem sua própria propriedade de versão de API – `apiVersion`. Esse controle de versão garante que uma configuração de recurso existente em um modelo não seja afetada por alterações na plataforma.

Novas versões de API introduzidas em tipos de recurso existentes no Azure global podem não estar imediatamente disponíveis em todas as regiões, nas nuvens soberanas ou no Azure Stack. Para exibir uma lista dos provedores de recursos, dos tipos de recurso e das versões de API disponíveis para uma nuvem, use o Resource Explorer no portal do Azure. Pesquise Resource Explorer no menu Todos os Serviços. Expanda o nó Provedores no Resource Explorer para retornar todos os provedores de recursos disponíveis, seus tipos de recursos e as versões de API nessa nuvem.

Para listar a versão de API disponível para todos os tipos de recurso em determinada nuvem na CLI do Azure, execute o seguinte script:

```azurecli-interactive
az provider list --query "[].{namespace:namespace, resourceType:resourceType[]}"
```

Use também o seguinte cmdlet do PowerShell:

```azurepowershell-interactive
Get-AzureRmResourceProvider | select-object ProviderNamespace -ExpandProperty ResourceTypes | ft ProviderNamespace, ResourceTypeName, ApiVersions
```

### <a name="refer-to-resource-locations-with-a-parameter"></a>Referir-se a recursos locais com um parâmetro

Um modelo sempre é implantado em um grupo de recursos que reside em uma região. Além da implantação propriamente dita, cada recurso em um modelo também tem uma propriedade de local usada para especificar a região na qual implantar. Para desenvolver seu modelo para consistência de nuvem, você precisa de uma maneira dinâmica para se referir aos locais de recursos, pois cada Azure Stack pode conter nomes de local exclusivos. Normalmente, os recursos são implantados na mesma região do grupo de recursos, mas para dar suporte a cenários como disponibilidade de aplicativos entre regiões, pode ser útil distribuir os recursos entre as regiões.

Mesmo que você possa codificar em código os nomes de região ao especificar as propriedades de recurso em um modelo, essa abordagem não garante que o modelo possa ser implantado em outros ambientes do Azure Stack, porque o nome da região muito provavelmente não existe nesse local.

Para acomodar diferentes regiões, adicione um local de parâmetro de entrada ao modelo com um valor padrão. O valor padrão será usado se nenhum valor for especificado durante a implantação.

A função de modelo `[resourceGroup()]` retorna um objeto que contém os seguintes pares chave/valor:

```json
{
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
  "tags": {
  },
  "properties": {
    "provisioningState": "{status}"
  }
}
```

Referenciando a chave do local do objeto no defaultValue do parâmetro de entrada, em runtime, o Azure Resource Manager substituirá a função de modelo `[resourceGroup().location]` pelo nome do local do grupo de recursos no qual o modelo é implantado.

```json
"parameters": {
  "location": {
    "type": "string",
    "metadata": {
      "description": "Location the resources will be deployed to."
    },
    "defaultValue": "[resourceGroup().location]"
  }
},
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2015-06-15",
    "name": "storageaccount1",
    "location": "[parameters('location')]",
    ...
```

Com essa função de modelo, você pode implantar seu modelo em qualquer nuvem, sem mesmo saber os nomes da região antecipadamente. Além disso, um local para um recurso específico no modelo pode ser diferente do local do grupo de recursos. Nesse caso, você pode configurá-lo usando parâmetros de entrada adicionais para esse recurso específico, enquanto os outros recursos no mesmo modelo ainda usam o parâmetro de entrada do local inicial.

### <a name="track-versions-using-api-profiles"></a>Controlar as versões usando perfis de API

Pode ser um grande desafio controlar todos os provedores de recursos disponíveis e as versões de API relacionadas que estão presentes no Azure Stack. Por exemplo, no momento da gravação, a última versão de API para **Microsoft.Compute/availabilitySets** no Azure é `2018-04-01`, enquanto a versão de API disponível comum ao Azure e ao Azure Stack é `2016-03-30`. A versão de API comum para **Microsoft.Storage/storageAccounts** compartilhada entre todos os locais do Azure e do Azure Stack é `2016-01-01`, enquanto a última versão de API no Azure é `2018-02-01`.

Por esse motivo, o Resource Manager introduziu o conceito de perfis de API para modelos. Sem perfis de API, cada recurso em um modelo é configurado com um elemento `apiVersion` que descreve a versão de API desse recurso específico.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "metadata": {
          "description": "Location the resources will be deployed to."
      },
      "defaultValue": "[resourceGroup().location]"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-01-01",
      "name": "mystorageaccount",
      "location": "[parameters('location')]",
      "properties": {
        "accountType": "Standard_LRS"
      }
    },
    {
      "type": "Microsoft.Compute/availabilitySets",
      "apiVersion": "2016-03-30",
      "name": "myavailabilityset",
      "location": "[parameters('location')]",
      "properties": {
        "platformFaultDomainCount": 2,
        "platformUpdateDomainCount": 2
      }
    }
  ],
  "outputs": {}
}
```

Uma versão de perfil de API funciona como um alias para uma única versão de API por tipo de recurso comum ao Azure e ao Azure Stack. Em vez de especificar uma versão de API para cada recurso em um modelo, especifique somente a versão de perfil de API em um novo elemento raiz chamado `apiProfile` e omita o elemento `apiVersion` dos recursos individuais.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "apiProfile": "2018–03-01-hybrid",
    "parameters": {
        "location": {
            "type": "string",
            "metadata": {
                "description": "Location the resources will be deployed to."
            },
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "mystorageaccount",
            "location": "[parameters('location')]",
            "properties": {
                "accountType": "Standard_LRS"
            }
        },
        {
            "type": "Microsoft.Compute/availabilitySets",
            "name": "myavailabilityset",
            "location": "[parameters('location')]",
            "properties": {
                "platformFaultDomainCount": 2,
                "platformUpdateDomainCount": 2
            }
        }
    ],
    "outputs": {}
}
```

O perfil de API garante que as versões de API estejam disponíveis nos locais, de modo que você não precise verificar manualmente as apiVersions disponíveis em um local específico. Para garantir que as versões de API referenciadas pelo perfil de API estejam presentes em um ambiente do Azure Stack, os operadores do Azure Stack precisam manter a solução atualizada com base na política para suporte. Se um sistema estiver desatualizado há mais de seis meses, ele será considerado fora de conformidade e o ambiente precisará ser atualizado.

O perfil de API não é um elemento necessário em um modelo. Mesmo se você adicionar o elemento, ele só será usado para obter recursos para os quais nenhuma `apiVersion` foi especificada. Esse elemento permite alterações graduais, mas não exige alterações nos modelos existentes.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "apiProfile": "2018–03-01-hybrid",
    "parameters": {
        "location": {
            "type": "string",
            "metadata": {
                "description": "Location the resources will be deployed to."
            },
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2016-01-01",
            "name": "mystorageaccount",
            "location": "[parameters('location')]",
            "properties": {
                "accountType": "Standard_LRS"
            }
        },
        {
            "type": "Microsoft.Compute/availabilitySets",
            "name": "myavailabilityset",
            "location": "[parameters('location')]",
            "properties": {
                "platformFaultDomainCount": 2,
                "platformUpdateDomainCount": 2
            }
        }
    ],
    "outputs": {}
}
```

## <a name="check-endpoint-references"></a>Verificar referências de ponto de extremidade

Os recursos podem ter referências a outros serviços na plataforma. Por exemplo, um IP público pode ter um nome DNS público atribuído a ele. A nuvem pública, as nuvens soberanas e as soluções do Azure Stack têm seus próprios namespaces de ponto de extremidade distintos. Na maioria dos casos, um recurso exige somente um prefixo como entrada no modelo. Durante o runtime, o Azure Resource Manager acrescenta o valor de ponto de extremidade a ele. Alguns valores de ponto de extremidade precisam ser especificados explicitamente no modelo.

> [!NOTE]
> Para desenvolver modelos para consistência de nuvem, não embuta em código namespaces de ponto de extremidade.

Os dois seguintes exemplos são namespaces de ponto de extremidade comuns que precisam ser especificados explicitamente durante a criação de um recurso:

* Contas de armazenamento (blob, fila, tabela e arquivo)
* Cadeias de conexão para bancos de dados e o Cache do Azure para Redis

Namespaces de ponto de extremidade também pode ser usados na saída de um modelo como informações para o usuário quando a implantação é concluída. Estes são exemplos comuns:

* Contas de armazenamento (blob, fila, tabela e arquivo)
* Cadeias de conexão (MySql, SQLServer, SQLAzure, Custom, NotificationHub, ServiceBus, EventHub, ApiHub, DocDb, RedisCache, PostgreSQL)
* Gerenciador de Tráfego
* domainNameLabel de um endereço IP público
* Serviços de Nuvem

Em geral, evite pontos de extremidade embutidos em código em um modelo. A melhor prática é usar a função de modelo de referência para recuperar os pontos de extremidade dinamicamente. Por exemplo, o ponto de extremidade mais geralmente embutido em código é o namespace de ponto de extremidade para contas de armazenamento. Cada conta de armazenamento tem um FQDN exclusivo que é construído pela concatenação do nome da conta de armazenamento com o namespace de ponto de extremidade. Uma conta de Armazenamento de Blobs chamada mystorageaccount1 resulta em FQDNs diferentes, dependendo da nuvem:

* `mystorageaccount1.blob.core.windows.net` Quando criado na nuvem global do Azure.
* `mystorageaccount1.blob.core.chinacloudapi.cn` Quando criado na nuvem da 21Vianet do Azure na China.

A seguinte função de modelo de referência recupera o namespace de ponto de extremidade do provedor de recursos de armazenamento:

```json
"diskUri":"[concat(reference(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))).primaryEndpoints.blob, 'container/myosdisk.vhd')]"
```

Substituindo o valor embutido em código do ponto de extremidade da conta de armazenamento pela função de modelo `reference`, você pode usar o mesmo modelo para fazer implantações bem-sucedidas em ambientes diferentes, sem fazer nenhuma alteração à referência de ponto de extremidade.

### <a name="refer-to-existing-resources-by-unique-id"></a>Referir-se aos recursos existentes pela ID exclusiva

Você também pode se referir a um recurso existente do mesmo ou de outro grupo de recursos, e na mesma assinatura ou de outra assinatura, no mesmo locatário na mesma nuvem. Para recuperar as propriedades do recurso, use o identificador exclusivo para o recurso propriamente dito. A função de modelo `resourceId` recupera a ID exclusiva de um recurso, como SQL Server, como mostra o seguinte código:

```json
"outputs": {
  "resourceId":{
    "type": "string",
    "value": "[resourceId('otherResourceGroup', 'Microsoft.Sql/servers', parameters('serverName'))]"
  }
}
```

Em seguida, você pode usar a função `resourceId` dentro da função de modelo `reference` para recuperar as propriedades de um banco de dados. O objeto de retorno contém a propriedade `fullyQualifiedDomainName` que contém o valor de ponto de extremidade completo. Esse valor é recuperado em runtime e fornece o namespace de ponto de extremidade específico do ambiente de nuvem. Para definir a cadeia de conexão sem embutir em código o namespace de ponto de extremidade, refira-se à propriedade do objeto de retorno diretamente na cadeia de conexão, conforme mostrado:

```json
"[concat('Server=tcp:', reference(resourceId('sql', 'Microsoft.Sql/servers', parameters('test')), '2015-05-01-preview').fullyQualifiedDomainName, ',1433;Initial Catalog=', parameters('database'),';User ID=', parameters('username'), ';Password=', parameters('pass'), ';Encrypt=True;')]"
```

## <a name="consider-resource-properties"></a>Considerar as propriedades de recursos

Recursos específicos em ambientes do Azure Stack têm propriedades exclusivas, que você precisa considerar em seu modelo.

### <a name="ensure-vm-images-are-available"></a>Garantir que as imagens de VM estão disponíveis

O Azure fornece uma seleção rica de imagens de VM. Essas imagens são criadas e preparadas para implantação pela Microsoft e por parceiros. As imagens formam a base para as VMs na plataforma. No entanto, um modelo consistente com a nuvem deve se referir apenas aos parâmetros disponíveis – em particular, o fornecedor, a oferta e o SKU das imagens de VM disponíveis para o Azure global, as nuvens soberanas do Azure ou uma solução do Azure Stack.

Para recuperar uma lista das imagens de VM disponíveis em um local, execute o seguinte comando da CLI do Azure:

```azurecli-interactive
az vm image list -all
```

Recupere a mesma lista com o cmdlet [Get-AzureRmVMImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) do Azure PowerShell e especifique o local desejado com o parâmetro `-Location`. Por exemplo:

```azurepowershell-interactive
Get-AzureRmVMImagePublisher -Location "West Europe" | Get-AzureRmVMImageOffer | Get-AzureRmVMImageSku | Get-AzureRmVMImage
```

Esse comando leva alguns minutos para retornar todas as imagens disponíveis na região da Europa Ocidental da nuvem do Azure global.

Se você disponibilizou essas imagens de VM para o Azure Stack, o armazenamento disponível é consumido. Para acomodar até mesmo a menor unidade de escala, o Azure Stack permite que você selecione as imagens que deseja adicionar a um ambiente.

O exemplo de código a seguir mostra uma abordagem consistente para referir-se aos parâmetros de Publicador, oferta e SKU em seus modelos de ARM:

```json
"storageProfile": {
    "imageReference": {
    "publisher": "MicrosoftWindowsServer",
    "offer": "WindowsServer",
    "sku": "2016-Datacenter",
    "version": "latest"
    }
}
```

### <a name="check-local-vm-sizes"></a>Verificar os tamanhos de VM locais

Para desenvolver seu modelo para consistência de nuvem, você precisa garantir que o tamanho de VM desejado esteja disponível em todos os ambientes de destino. Os tamanhos de VM são um agrupamento de funcionalidades e características de desempenho. Alguns tamanhos de VM dependem do hardware no qual a VM é executada. Por exemplo, caso você deseje implantar uma VM otimizada para GPU, o hardware que executa o hipervisor precisa ter as GPUs de hardware.

Quando a Microsoft introduz um novo tamanho de VM que tem determinadas dependências de hardware, o tamanho de VM geralmente é disponibilizado pela primeira vez em um pequeno subconjunto de regiões na nuvem do Azure. Posteriormente, ele é disponibilizado para outras regiões e nuvens. Para garantir que o tamanho de VM existe em cada nuvem na qual é feita a implantação, recupere os tamanhos disponíveis com o seguinte comando da CLI do Azure:

```azurecli-interactive
az vm list-sizes --location "West Europe"
```

Para o Azure PowerShell, use:

```azurepowershell-interactive
Get-AzureRmVMSize -Location "West Europe"
```

Para obter uma lista completa dos serviços disponíveis, confira [Produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?cdn=disable).

### <a name="check-use-of-azure-managed-disks-in-azure-stack"></a>Verificar o uso do Azure Managed Disks no Azure Stack

Os discos gerenciados cuidam do armazenamento para um locatário do Azure. Em vez de criar explicitamente uma conta de armazenamento e especificar o URI para um VHD (disco rígido virtual), você pode usar discos gerenciados para executar implicitamente essas ações ao implantar uma VM. Os discos gerenciados melhoram a disponibilidade, colocando todos os discos de VMs no mesmo conjunto de disponibilidade em diferentes unidades de armazenamento. Além disso, os VHDs existentes podem ser convertidos do armazenamento Standard para Premium com muito menos tempo de inatividade.

Embora os discos gerenciados estejam no roteiro do Azure Stack, atualmente, não há suporte para eles. Até isso acontecer, você pode desenvolver modelos consistentes com a nuvem para o Azure Stack especificando explicitamente os VHDs usando o elemento `vhd` no modelo para o recurso de VM, conforme mostrado:

```json
"storageProfile": {
  "imageReference": {
    "publisher": "MicrosoftWindowsServer",
    "offer": "WindowsServer",
    "sku": "[parameters('windowsOSVersion')]",
    "version": "latest"
  },
  "osDisk": {
    "name": "osdisk",
    "vhd": {
      "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2015-06-15').primaryEndpoints.blob, 'vhds/osdisk.vhd')]"
    },
    "caching": "ReadWrite",
    "createOption": "FromImage"
  }
}
```

Por outro lado, para especificar uma configuração de disco gerenciado em um modelo, remova o elemento `vhd` da configuração do disco.

```json
"storageProfile": {
  "imageReference": {
    "publisher": "MicrosoftWindowsServer",
    "offer": "WindowsServer",
    "sku": "[parameters('windowsOSVersion')]",
    "version": "latest"
  },
  "osDisk": {
    "caching": "ReadWrite",
    "createOption": "FromImage"
  }
}
```

As mesmas alterações também se aplicam aos [discos de dados](../../virtual-machines/using-managed-disks-template-deployments.md).

### <a name="verify-that-vm-extensions-are-available-in-azure-stack"></a>Verificar se as extensões de VM estão disponíveis no Azure Stack

Outra consideração a ser feita sobre a consistência de nuvem é o uso de [extensões de máquina virtual](../../virtual-machines/extensions/features-windows.md) para configurar os recursos dentro de uma VM. Nem todas as extensões de VM estão disponíveis no Azure Stack. Um modelo pode especificar os recursos dedicados para a extensão de VM, criando dependências e condições dentro do modelo.

Por exemplo, caso você deseje configurar uma VM que executa o Microsoft SQL Server, a extensão de VM poderá configurar o SQL Server como parte da implantação de modelo. Considere o que acontecerá se o modelo de implantação também contiver um servidor de aplicativos configurado para criar um banco de dados na VM que executa o SQL Server. Além de também usar uma extensão de VM para os servidores de aplicativos, você pode configurar a dependência do servidor de aplicativos em um retorno bem-sucedido do recurso de extensão de VM do SQL Server. Essa abordagem garante que a VM que executa o SQL Server esteja configurada e disponível quando o servidor de aplicativos for instruído a criar o banco de dados.

A abordagem declarativa do modelo permite que você defina o estado final dos recursos e de suas inter-dependências, enquanto a plataforma se encarrega da lógica necessária para as dependências.

#### <a name="check-that-vm-extensions-are-available"></a>Verificar se as extensões de VM estão disponíveis

Existem muitos tipos de extensões de VM. Ao desenvolver um modelo para consistência de nuvem, use apenas as extensões que estão disponíveis em todas as regiões de destino do modelo.

Para recuperar uma lista das extensões de VM que estão disponíveis para uma região específica (neste exemplo, `myLocation`), execute o seguinte comando da CLI do Azure:

```azurecli-interactive
az vm extension image list --location myLocation
```

Execute também o cmdlet [Get-AzureRmVmImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) do Azure PowerShell e use `-Location` para especificar o local da imagem de máquina virtual. Por exemplo:

```azurepowershell-interactive
Get-AzureRmVmImagePublisher -Location myLocation | Get-AzureRmVMExtensionImageType | Get-AzureRmVMExtensionImage | Select Type, Version
```

#### <a name="ensure-that-versions-are-available"></a>Garantir que as versões estejam disponíveis

Como as extensões de VM são recursos internos do Resource Manager, elas têm suas próprias versões de API. Como mostra o código a seguir, o tipo de extensão de VM é um recurso aninhado no provedor de recursos Microsoft.Compute.

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "apiVersion": "2015-06-15",
    "name": "myExtension",
    "location": "[parameters('location')]",
    ...
```

A versão de API do recurso de extensão de VM precisa estar presente em todos os locais que você pretende ter como destino com o modelo. A dependência de local funciona como a disponibilidade da versão de API do provedor de recursos abordada anteriormente na seção "Verificar a versão de todos os tipos de recurso".

Para recuperar uma lista das versões de API disponíveis para o recurso de extensão de VM, use o cmdlet [Get-AzureRmResourceProvider](/powershell/module/az.resources/get-azresourceprovider) com o provedor de recursos **Microsoft.Compute**, conforme mostrado abaixo:

```azurepowershell-interactive
Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Compute" | Select-Object -ExpandProperty ResourceTypes | Select ResourceTypeName, Locations, ApiVersions | where {$_.ResourceTypeName -eq "virtualMachines/extensions"}
```

Use também extensões de VM em conjuntos de dimensionamento de máquinas virtuais. As mesmas condições de local se aplicam. Para desenvolver seu modelo para consistência de nuvem, garanta que as versões de API estejam disponíveis em todos os locais nos quais você pretende implantá-lo. Para recuperar as versões de API do recurso de extensão de VM para conjuntos de dimensionamento, use o mesmo cmdlet como antes, mas especifique o tipo de recurso dos conjuntos de dimensionamento de máquinas virtuais conforme mostrado abaixo:

```azurepowershell-interactive
Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Compute" | Select-Object -ExpandProperty ResourceTypes | Select ResourceTypeName, Locations, ApiVersions | where {$_.ResourceTypeName -eq "virtualMachineScaleSets/extensions"}
```

Cada extensão específica também tem um controle de versão. Essa versão é mostrada na propriedade `typeHandlerVersion` da extensão de VM. Garanta que a versão especificada no elemento `typeHandlerVersion` das extensões de VM do modelo esteja disponível nos locais em que você pretende implantar o modelo. Por exemplo, o seguinte código especifica a versão 1.7:

```json
{
    "type": "extensions",
    "apiVersion": "2016-03-30",
    "name": "MyCustomScriptExtension",
    "location": "[parameters('location')]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]"
    ],
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.7",
        ...
```

Para recuperar uma lista das versões disponíveis de uma extensão de VM específica, use o cmdlet [Get-AzureRmVMExtensionImage](/powershell/module/az.compute/get-azvmextensionimage). O seguinte exemplo recupera as versões disponíveis para a extensão de VM de DSC (Desired State Configuration) do PowerShell de **myLocation**:

```azurepowershell-interactive
Get-AzureRmVMExtensionImage -Location myLocation -PublisherName Microsoft.PowerShell -Type DSC | FT
```

Para ver uma lista de fornecedores, use o comando [Get-AzureRmVmImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher). Para solicitar um tipo, use o comando [Get-AzureRmVMExtensionImageType](/powershell/module/az.compute/get-azvmextensionimagetype).

## <a name="tips-for-testing-and-automation"></a>Dicas para testes e automação

É um desafio manter o controle de todas as configurações relacionadas, as funcionalidades e as limitações durante a criação de um modelo. A abordagem comum é desenvolver e testar modelos em uma única nuvem antes de direcioná-los a outros locais. No entanto, quanto mais cedo os testes forem executados no processo de criação, menos solução de problemas e reescrita de código precisarão ser feitas por sua equipe de desenvolvimento. Pode ser demorado solucionar problemas de implantações que falham devido a dependências de local. É por isso que recomendamos o teste automatizado o mais cedo possível no ciclo de criação. Por fim, você precisará de menos tempo de desenvolvimento e menos recursos, e seus artefatos consistentes com a nuvem se tornarão ainda mais valiosos.

A imagem a seguir mostra um exemplo típico de um processo de desenvolvimento para uma equipe usando um IDE (ambiente de desenvolvimento integrado). Em estágios diferentes na linha do tempo, diferentes tipos de teste são executados. Aqui, dois desenvolvedores estão trabalhando na mesma solução, mas esse cenário se aplica igualmente a um único desenvolvedor ou a uma equipe grande. Normalmente, cada desenvolvedor cria uma cópia local de um repositório central, permitindo que cada um trabalhe na cópia local sem afetar os outros que podem estar trabalhando nos mesmos arquivos.

![O diagrama mostra dois conjuntos de testes de unidade e testes de integração em paralelo no local I D E, que mesclam o fluxo de desenvolvimento C I/C D em testes de unidade, em seguida, os testes de integração, a implantação de teste e a implantação.](./media/templates-cloud-consistency/workflow.png)

Considere as seguintes dicas para testes e automação:

* Utilize ferramentas de teste. Por exemplo, o Visual Studio Code e o Visual Studio incluem o IntelliSense e outros recursos que podem ajudá-lo a validar os modelos.
* Para melhorar a qualidade do código durante o desenvolvimento no IDE local, faça a análise de código estático com testes de unidade e testes de integração.
* Para obter uma experiência ainda melhor durante o desenvolvimento inicial, os testes de unidade e os testes de integração só deverão avisar quando um problema for encontrado; portanto, continue com os testes. Dessa forma, você pode identificar os problemas a serem resolvidos e priorizar a ordem das alterações, também conhecido como TDD (implantação orientada por testes).
* Lembre-se de que alguns testes podem ser executados sem uma conexão com o Azure Resource Manager. Outros, como o teste de implantação de modelo, exigem que o Resource Manager execute determinadas ações que não podem ser executadas offline.
* O teste de um modelo de implantação na API de validação não é igual a uma implantação real. Além disso, mesmo se você implantar um modelo de um arquivo local, as referências aos modelos aninhados no modelo serão recuperadas pelo Resource Manager diretamente, e os artefatos referenciados pelas extensões de VM serão recuperados pelo agente de VM em execução dentro da VM implantada.

## <a name="next-steps"></a>Próximas etapas

* [Considerações sobre o modelo do Azure Resource Manager](/azure-stack/user/azure-stack-develop-templates)
* [Práticas recomendadas para modelos de ARM](template-syntax.md)
