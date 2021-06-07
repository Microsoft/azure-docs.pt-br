---
title: Implantação segura entre regiões – Azure Gerenciador de Implantação
description: Saiba como implantar um serviço em várias regiões com o Azure Gerenciador de Implantação e sobre práticas de implantação seguras.
ms.topic: conceptual
ms.date: 11/21/2019
ms.custom: seodec18
ms.openlocfilehash: 63553b0bbca031faa44e0d88480fcc08950a3e2c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99627492"
---
# <a name="enable-safe-deployment-practices-with-azure-deployment-manager-public-preview"></a>Habilitar práticas de implantação segura com o Azure Gerenciador de Implantação (visualização pública)

Para implantar seu serviço em várias regiões e garantir que ele esteja sendo executado conforme o esperado em cada região, você pode usar o Azure Deployment Manager para coordenar uma implementação gradual do serviço. Assim como você faria em qualquer implantação do Azure, defina os recursos para seu serviço em [ modelos do Resource Manager ](template-syntax.md). Depois de criar os modelos, use o Deployment Manager para descrever a topologia do seu serviço e como ele deve ser implementado.

Gerenciador de implantação é um recurso do Gerenciador de recursos. Ele expande seus recursos durante a implantação. Use o Gerenciador de implantação quando você tiver um serviço complexo que precisa ser implantado em várias regiões. Ao preparar a distribuição do seu serviço, você poderá encontrar problemas em potencial antes de ele ter sido implantado para todas as regiões. Se você não precisar das precauções extras de um lançamento gradual, use as [opções de implantação](deploy-portal.md) padrão para o Gerenciador de recursos. O Deployment Manager integra-se perfeitamente a todas as ferramentas de terceiros existentes que oferecem suporte a implantações do Resource Manager, como ofertas de integração contínua e entrega contínua (CI/CD).

O Gerenciador de Implantação do Azure está em versão prévia. Ajude-nos a melhorar o recurso fornecendo [comentários](https://aka.ms/admfeedback).

Para usar o Deployment Manager, você precisa criar quatro arquivos:

* Modelo de topologia.
* Modelo de distribuição.
* Arquivo de parâmetro para a topologia.
* Arquivo de parâmetro para distribuição.

Você implanta o modelo de topologia antes de implantar o modelo de implementação.

Recursos adicionais:

* a [referência da API REST do Gerenciador de Implantação do Azure](/rest/api/deploymentmanager/).
* [Tutorial: usar o Azure Gerenciador de implantação com modelos do Resource Manager](./deployment-manager-tutorial.md).
* [Tutorial: Usar a verificação de integridade no Gerenciador de Implantação do Azure](./deployment-manager-tutorial-health-check.md).
* [Exemplo de Gerenciador de implantação do Azure](https://github.com/Azure-Samples/adm-quickstart).

## <a name="identity-and-access"></a>Identidade e acesso

Com o Gerenciador de implantação, uma [atribuída ao usuário a identidade gerenciada](../../active-directory/managed-identities-azure-resources/overview.md) executa as ações de implantação. Você cria essa identidade antes de iniciar sua implantação. Deve ter acesso à assinatura para a qual você está implantando o serviço e permissão suficiente para concluir a implantação. Para obter informações sobre as ações concedidas por meio de funções, consulte [funções internas do Azure](../../role-based-access-control/built-in-roles.md).

A identidade deve residir no mesmo local que a distribuição.

## <a name="topology-template"></a>Modelo de topologia

O modelo de topologia descreve os recursos do Azure que formam seu serviço e onde implantá-los. A imagem a seguir mostra a topologia para um serviço de exemplo:

![Hierarquia da topologia de serviço para serviços para unidades de serviço](./media/deployment-manager-overview/service-topology.png)

O modelo de topologia inclui os seguintes recursos:

* Origem do artefato-onde os modelos e parâmetros do Resource Manager são armazenados.
* Topologia de serviço – aponta para a origem do artefato.
  * Serviços-especifica a localização e a ID da assinatura do Azure.
    * Unidades de serviço-especifica o grupo de recursos, o modo de implantação e o caminho para os arquivos de modelo e parâmetro.

Para entender o que acontece em cada nível, é útil ver quais valores você fornece.

![Valores para cada nível](./media/deployment-manager-overview/topology-values.png)

### <a name="artifact-source-for-templates"></a>Origem do artefato para modelos

Em seu modelo de topologia, você cria uma fonte de artefato que contém os arquivos de modelos e parâmetros. A origem do artefato é uma maneira de extrair os arquivos para implementação. Você verá outra fonte de artefatos para binários posteriormente neste artigo.

O exemplo a seguir mostra o formato geral da origem do artefato.

```json
{
  "type": "Microsoft.DeploymentManager/artifactSources",
  "apiVersion": "2018-09-01-preview",
  "name": "<artifact-source-name>",
  "location": "<artifact-source-location>",
  "properties": {
    "sourceType": "AzureStorage",
    "artifactRoot": "<root-folder-for-templates>",
    "authentication": {
      "type": "SAS",
      "properties": {
        "sasUri": "<SAS-URI-for-storage-container>"
      }
    }
  }
}
```

Para obter mais informações, consulte [referência do modelo artifactSources](/azure/templates/Microsoft.DeploymentManager/artifactSources).

### <a name="service-topology"></a>Topologia de serviço

O exemplo a seguir mostra o formato geral do recurso de topologia de serviço. Você fornece o ID do recurso da origem do artefato que contém os modelos e arquivos de parâmetros. A topologia de serviço inclui todos os recursos de serviço. Verifique se a origem do artefato está disponível porque a topologia do serviço depende dela.

```json
{
  "type": "Microsoft.DeploymentManager/serviceTopologies",
  "apiVersion": "2018-09-01-preview",
  "name": "<topology-name>",
  "location": "<topology-location>",
  "dependsOn": [
    "<artifact-source>"
  ],
  "properties": {
    "artifactSourceId": "<resource-ID-artifact-source>"
  },
  "resources": [
    {
      "type": "services",
        ...
        }
    ]
}
```

Para obter mais informações, consulte [referência do modelo serviceTopologies](/azure/templates/Microsoft.DeploymentManager/serviceTopologies).

### <a name="services"></a>Serviços

O exemplo a seguir mostra o formato geral do recurso de serviços. Em cada serviço, você fornece o local e o ID de assinatura do Azure a ser usado para implantar seu serviço. Para implantar em várias regiões, você define um serviço para cada região. O serviço depende da topologia de serviço.

```json
{
  "type": "services",
  "apiVersion": "2018-09-01-preview",
  "name": "<service-name>",
  "location": "<service-location>",
  "dependsOn": [
      "<service-topology>"
  ],
  "properties": {
    "targetSubscriptionId": "<subscription-ID>",
    "targetLocation": "<location-of-deployed-service>"
  },
  "resources": [
    {
      "type": "serviceUnits",
            ...
        }
    ]
}
```

Para obter mais informações, consulte [referência de modelo de serviços](/azure/templates/Microsoft.DeploymentManager/serviceTopologies/services).

### <a name="service-units"></a>Unidades de Serviço

O exemplo a seguir mostra o formato geral do recurso de unidades de serviço. Em cada unidade de serviço, você especifica o grupo de recursos, o [modo de implantação](deployment-modes.md) a ser usado na implantação e o caminho para o modelo e o arquivo de parâmetros. Se você especificar um caminho relativo para o modelo e os parâmetros, o caminho completo será construído a partir da pasta raiz na origem dos artefatos. Você pode especificar um caminho absoluto para o modelo e os parâmetros, mas perde a capacidade de editar facilmente seus lançamentos. A unidade de serviço depende do serviço.

```json
{
  "type": "serviceUnits",
  "apiVersion": "2018-09-01-preview",
  "name": "<service-unit-name>",
  "location": "<service-unit-location>",
  "dependsOn": [
    "<service>"
  ],
  "tags": {
    "serviceType": "Service West US Web App"
  },
  "properties": {
    "targetResourceGroup": "<resource-group-name>",
    "deploymentMode": "Incremental",
    "artifacts": {
      "templateArtifactSourceRelativePath": "<relative-path-to-template>",
      "parametersArtifactSourceRelativePath": "<relative-path-to-parameter-file>"
    }
  }
}
```

Cada modelo deve incluir os recursos relacionados que você deseja implantar em uma etapa. Por exemplo, uma unidade de serviço pode ter um modelo que implanta todos os recursos para o front end de seu serviço.

Para obter mais informações, consulte [referência de modelo serviceUnits](/azure/templates/Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits).

## <a name="rollout-template"></a>Modelo de distribuição

O modelo de lançamento descreve as etapas a serem seguidas ao implantar seu serviço. Você especifica a topologia de serviço para usar e definir a ordem de implantação de unidades de serviço. Inclui uma fonte de artefato para armazenar binários para a implementação. Em seu modelo de rollout, você define a seguinte hierarquia:

* Origem do artefato.
* Etapa.
* Profundo.
  * Grupos de etapas.
    * Operações de implantação.

A imagem a seguir mostra a hierarquia do modelo de lançamento:

![Hierarquia de distribuição às etapas](./media/deployment-manager-overview/Rollout.png)

Cada distribuição pode ter muitos grupos de etapa. Cada grupo de etapas tem uma operação de implantação que aponta para uma unidade de serviço na topologia de serviço.

### <a name="artifact-source-for-binaries"></a>Origem do artefato para binários

No modelo de implementação, você cria uma fonte de artefato para os binários que você precisa implantar no serviço. Essa origem de artefato é semelhante à fonte de [artefatos para modelos](#artifact-source-for-templates), exceto pelo fato de conter scripts, páginas da Web, códigos compilados ou outros arquivos necessários ao seu serviço.

### <a name="steps"></a>Etapas

Você pode definir uma etapa a ser executada antes ou depois de sua operação de implantação. Atualmente, somente a `wait` etapa e a `healthCheck` etapa estão disponíveis.

A `wait` etapa pausa a implantação antes de continuar. Ele permite que você verifique se seu serviço está sendo executado conforme o esperado antes de implantar a próxima unidade de serviço. O exemplo a seguir mostra o formato geral de uma `wait` etapa.

```json
{
    "type": "Microsoft.DeploymentManager/steps",
    "apiVersion": "2018-09-01-preview",
    "name": "waitStep",
    "location": "<step-location>",
    "properties": {
        "stepType": "wait",
        "attributes": {
          "duration": "PT1M"
        }
    }
},
```

A propriedade duration usa [padrão ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Durations). O exemplo anterior Especifica uma espera de um minuto.

Para obter mais informações sobre verificações de integridade, consulte [introduzir a distribuição de integração de integridade para o Azure Gerenciador de implantação](./deployment-manager-health-check.md) e [tutorial: usar a verificação de integridade no Gerenciador de implantação do Azure](./deployment-manager-tutorial-health-check.md).

Para obter mais informações, consulte [as etapas de referência de modelo](/azure/templates/Microsoft.DeploymentManager/steps).

### <a name="rollouts"></a>Distribuições

Verifique se a origem do artefato está disponível porque a distribuição depende dela. O rollout define grupos de etapas para cada unidade de serviço implantada. Você pode definir ações a serem executadas antes ou depois da implantação. Por exemplo, você pode especificar a implantação a ser esperada após a implantação da unidade de serviço. Você pode definir a ordem dos grupos de etapa.

O objeto de identidade especifica a [identidade gerenciada atribuída pelo usuário](#identity-and-access) que executa as ações de implantação.

O exemplo a seguir mostra o formato geral da distribuição.

```json
{
  "type": "Microsoft.DeploymentManager/rollouts",
  "apiVersion": "2018-09-01-preview",
  "name": "<rollout-name>",
  "location": "<rollout-location>",
  "Identity": {
    "type": "userAssigned",
    "identityIds": [
      "<managed-identity-ID>"
    ]
  },
  "dependsOn": [
    "<artifact-source>"
  ],
  "properties": {
    "buildVersion": "1.0.0.0",
    "artifactSourceId": "<artifact-source-ID>",
    "targetServiceTopologyId": "<service-topology-ID>",
    "stepGroups": [
      {
        "name": "stepGroup1",
        "dependsOnStepGroups": ["<step-group-name>"],
        "preDeploymentSteps": ["<step-ID>"],
        "deploymentTargetId":
          "<service-unit-ID>",
        "postDeploymentSteps": ["<step-ID>"]
      },
            ...
        ]
    }
}
```

Para obter mais informações, consulte [referência de modelo de distribuições](/azure/templates/Microsoft.DeploymentManager/rollouts).

## <a name="parameter-file"></a>Arquivo de parâmetro.

Você cria dois arquivos de parâmetro. Um arquivo de parâmetro é usado ao implantar a topologia de serviço e o outro é usado para a implantação de rollout. Existem alguns valores que você precisa para ter certeza de que são os mesmos em ambos os arquivos de parâmetros.

## <a name="containerroot-variable"></a>variável de containerRoot

Com implantações com versão, o caminho para seus artefatos muda a cada nova versão. Na primeira vez que você executa uma implementação, o caminho pode ser `https://<base-uri-blob-container>/binaries/1.0.0.0`. A segunda vez pode ser `https://<base-uri-blob-container>/binaries/1.0.0.1`. O Deployment Manager simplifica a obtenção do caminho raiz correto para a implementação atual usando a variável `$containerRoot`. Esse valor muda com cada versão e não é conhecido antes da implantação.

Use a `$containerRoot` variável no arquivo de parâmetro para o modelo que implanta os recursos do Azure. No momento da implementação, essa variável é substituída pelos valores reais do rollout.

Por exemplo, durante o rollout, você cria uma fonte de artefatos para os artefatos binários.

```json
{
  "type": "Microsoft.DeploymentManager/artifactSources",
  "apiVersion": "2018-09-01-preview",
  "name": "[variables('rolloutArtifactSource').name]",
  "location": "[parameters('azureResourceLocation')]",
  "properties": {
    "sourceType": "AzureStorage",
      "artifactRoot": "[parameters('binaryArtifactRoot')]",
      "authentication" :
        {
          "type": "SAS",
          "properties": {
            "sasUri": "[parameters('artifactSourceSASLocation')]"
          }
        }
  }
},
```

Observe que o `artifactRoot` e `sasUri` propriedades. A raiz do artefato pode ser configurada para um valor como `binaries/1.0.0.0`. O URI do SAS é o URI para seu contêiner de armazenamento com um token SAS para acesso. O Deployment Manager constrói automaticamente o valor da variável `$containerRoot`. Ele combina esses valores no formato `<container>/<artifactRoot>`.

Seu modelo e arquivo de parâmetros precisam conhecer o caminho correto para obter os binários com versão. Por exemplo, para implantar arquivos para um aplicativo Web, crie o seguinte arquivo de parâmetro com a `$containerRoot` variável. Você deve usar duas barras invertidas (`\\`) para o caminho, porque o primeiro é um caractere de escape.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "deployPackageUri": {
      "value": "$containerRoot\\helloWorldWebAppWUS.zip"
    }
  }
}
```

Em seguida, use esse parâmetro em seu modelo:

```json
{
  "name": "MSDeploy",
  "apiVersion": "2015-08-01",
  "type": "extensions",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Web/sites/', parameters('WebAppName'))]"
  ],
  "tags": {
    "displayName": "WebAppMSDeploy"
  },
  "properties": {
    "packageUri": "[parameters('deployPackageURI')]"
  }
}
```

Você gerencia implantações com versão criando novas pastas e passando esse caminho raiz durante a distribuição. O caminho fluem para o modelo que implanta os recursos.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre o Deployment Manager. Vá para o próximo artigo para saber como implantar com o Gerenciador de implantação.

> [!div class="nextstepaction"]
> [Tutorial: Usar Gerenciador de implantação do Azure com modelos do Resource Manager](./deployment-manager-tutorial.md)
>
> [Início rápido: Experimente o Azure Gerenciador de Implantação em apenas alguns minutos](https://github.com/Azure-Samples/adm-quickstart)
