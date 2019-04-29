---
title: Práticas recomendadas de implantação segura entre regiões - Gerenciador de Implantação do Azure
description: Descreve como implantar um serviço em muitas regiões com o Azure Deployment Manager. Ele mostra as práticas de implantação segura para verificar a estabilidade da sua implantação antes de distribuir a todas as regiões.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/09/2018
ms.author: tomfitz
ms.custom: seodec18
ms.openlocfilehash: a615ab26e4ea046ced70ce2c154a0c304b741986
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60550865"
---
# <a name="enable-safe-deployment-practices-with-azure-deployment-manager-private-preview"></a>Ativar práticas de implantação seguras com o Azure Deployment Manager (visualização privada)

Para implantar seu serviço em várias regiões e garantir que ele esteja sendo executado conforme o esperado em cada região, você pode usar o Azure Deployment Manager para coordenar uma implementação gradual do serviço. Assim como você faria em qualquer implantação do Azure, defina os recursos para seu serviço em [ modelos do Resource Manager ](resource-group-authoring-templates.md). Depois de criar os modelos, use o Deployment Manager para descrever a topologia do seu serviço e como ele deve ser implementado.

Gerenciador de implantação é um recurso do Gerenciador de recursos. Ele expande seus recursos durante a implantação. Use o Gerenciador de implantação quando você tiver um serviço complexo que precisa ser implantado em várias regiões. Ao preparar a distribuição do seu serviço, você poderá encontrar problemas em potencial antes de ele ter sido implantado para todas as regiões. Se você não precisar das precauções extras de um lançamento gradual, use as [opções de implantação](resource-group-template-deploy-portal.md) padrão para o Gerenciador de recursos. O Deployment Manager integra-se perfeitamente a todas as ferramentas de terceiros existentes que oferecem suporte a implantações do Resource Manager, como ofertas de integração contínua e entrega contínua (CI/CD). 

O Gerenciador de implantação do Azure está em visualização particular. Para usar o Azure Deployment Manager, preencha o [formulário de inscrição](https://aka.ms/admsignup). Ajuda a melhorar o recurso, fornecendo [comentários](https://aka.ms/admfeedback).

Para usar o Deployment Manager, você precisa criar quatro arquivos:

* Modelo de topologia
* Modelo de distribuição
* Arquivo de parâmetro para a topologia
* Arquivo de parâmetro para distribuição

Você implanta o modelo de topologia antes de implantar o modelo de implementação.

A referência à API REST do Gerenciador de Implantação do Azure pode ser encontrada [aqui](https://docs.microsoft.com/rest/api/deploymentmanager/).

## <a name="supported-locations"></a>Locais com suporte

Para a visualização, os recursos do Gerenciador de Implantação são suportados no Central dos EUA e no Leste dos EUA 2. Ao definir recursos em sua topologia e modelos de lançamento, como unidades de serviço, fontes de artefatos e rollouts descritos neste artigo, você deve especificar uma dessas regiões para o local. No entanto, os recursos que você implanta para criar seu serviço, como máquinas virtuais, contas de armazenamento e aplicativos da web, são suportados em todos os [locais padrão](https://azure.microsoft.com/global-infrastructure/services/?products=all).  

## <a name="identity-and-access"></a>Identidade e acesso

Com o Gerenciador de implantação, uma [atribuída ao usuário a identidade gerenciada](../active-directory/managed-identities-azure-resources/overview.md) executa as ações de implantação. Você cria essa identidade antes de iniciar sua implantação. Deve ter acesso à assinatura para a qual você está implantando o serviço e permissão suficiente para concluir a implantação. Para obter informações sobre as ações concedidas por meio de funções, consulte [Funções internas dos recursos do Azure](../role-based-access-control/built-in-roles.md).

A identidade deve residir em um dos locais com suporte do Deployment Manager e deve residir no mesmo local que o rollout.

## <a name="topology-template"></a>Modelo de topologia

O modelo de topologia descreve os recursos do Azure que formam seu serviço e onde implantá-los. A imagem a seguir mostra a topologia para um serviço de exemplo:

![Hierarquia da topologia de serviço para serviços para unidades de serviço](./media/deployment-manager-overview/service-topology.png)

O modelo de topologia inclui os seguintes recursos:

* Origem do artefato - onde seus modelos do Resource Manager e os parâmetros são armazenados
* Topologia de serviço - aponta para uma fonte de artefato
  * Serviços - especifica o local e o ID de assinatura do Azure
    * Unidades de serviço - especifica o grupo de recursos, o modo de implementação e o caminho para o modelo e o arquivo de parâmetros

Para entender o que acontece em cada nível, é útil ver quais valores você fornece.

![Valores para cada nível](./media/deployment-manager-overview/topology-values.png)

### <a name="artifact-source-for-templates"></a>Origem do artefato para modelos

Em seu modelo de topologia, você cria uma fonte de artefato que contém os arquivos de modelos e parâmetros. A origem do artefato é uma maneira de extrair os arquivos para implementação. Você verá outra fonte de artefatos para binários posteriormente neste artigo.

O exemplo a seguir mostra o formato geral da origem do artefato.

```json
{
    "type": "Microsoft.DeploymentManager/artifactSources",
    "name": "<artifact-source-name>",
    "location": "<artifact-source-location>",
    "apiVersion": "2018-09-01-preview",
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

O exemplo a seguir mostra o formato geral do recurso de topologia de serviço. Você fornece o ID do recurso da origem do artefato que contém os modelos e arquivos de parâmetros. A topologia de serviço inclui todos os recursos de serviço. Para garantir que a fonte de artefato esteja disponível, a topologia de serviço depende disso.

```json
{
    "type": "Microsoft.DeploymentManager/serviceTopologies",
    "name": "<topology-name>",
    "location": "<topology-location>",
    "apiVersion": "2018-09-01-preview",
    "properties": {
        "artifactSourceId": "<resource-ID-artifact-source>"
    },
    "dependsOn": [
        "<artifact-source>"
    ],
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
    "name": "<service-name>",
    "location": "<service-location>",
    "apiVersion": "2018-09-01-preview",
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
    "name": "<service-unit-name>",
    "location": "<service-unit-location>",
    "apiVersion": "2018-09-01-preview",
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

* Origem do artefato
* Etapa
* Distribuição
  * Grupos de etapas
    * Operações de implantação

A imagem a seguir mostra a hierarquia do modelo de lançamento:

![Hierarquia de distribuição às etapas](./media/deployment-manager-overview/Rollout.png)

Cada distribuição pode ter muitos grupos de etapa. Cada grupo de etapas tem uma operação de implantação que aponta para uma unidade de serviço na topologia de serviço.

### <a name="artifact-source-for-binaries"></a>Origem do artefato para binários

No modelo de implementação, você cria uma fonte de artefato para os binários que você precisa implantar no serviço. Essa origem de artefato é semelhante à fonte de [artefatos para modelos](#artifact-source-for-templates), exceto pelo fato de conter scripts, páginas da Web, códigos compilados ou outros arquivos necessários ao seu serviço.

### <a name="steps"></a>Etapas

Você pode definir uma etapa a ser executada antes ou depois de sua operação de implantação. Atualmente, apenas a etapa `wait` está disponível. A etapa de espera faz uma pausa na implantação antes de continuar. Ele permite que você verifique se seu serviço está sendo executado conforme o esperado antes de implantar a próxima unidade de serviço. O exemplo a seguir mostra o formato geral de uma etapa de espera.

```json
{
    "apiVersion": "2018-09-01-preview",
    "type": "Microsoft.DeploymentManager/steps",
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

Para obter mais informações, consulte [as etapas de referência de modelo](/azure/templates/Microsoft.DeploymentManager/steps).

### <a name="rollouts"></a>Distribuições

Para garantir que a fonte do artefato esteja disponível, o lançamento depende disso. O rollout define grupos de etapas para cada unidade de serviço implantada. Você pode definir ações a serem executadas antes ou depois da implantação. Por exemplo, você pode especificar que a implantação de espera depois que a unidade do serviço foi implantada. Você pode definir a ordem dos grupos de etapa.

O objeto de identidade especifica a [identidade gerenciada atribuída pelo usuário](#identity-and-access) que executa as ações de implantação.

O exemplo a seguir mostra o formato geral da distribuição.

```json
{
    "type": "Microsoft.DeploymentManager/rollouts",
    "name": "<rollout-name>",
    "location": "<rollout-location>",
    "apiVersion": "2018-09-01-preview",
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

Use a variável `$containerRoot` no arquivo de parâmetros do modelo para implantar os recursos do Azure. No momento da implementação, essa variável é substituída pelos valores reais do rollout. 

Por exemplo, durante o rollout, você cria uma fonte de artefatos para os artefatos binários.

```json
{
    "type": "Microsoft.DeploymentManager/artifactSources",
    "name": "[variables('rolloutArtifactSource').name]",
    "location": "[parameters('azureResourceLocation')]",
    "apiVersion": "2018-09-01-preview",
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

Seu modelo e arquivo de parâmetros precisam conhecer o caminho correto para obter os binários com versão. Por exemplo, para implantar arquivos para um aplicativo da web, crie o seguinte arquivo de parâmetros com a variável $ containerRoot. Você deve usar duas barras invertidas (`\\`) para o caminho, porque o primeiro é um caractere de escape.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
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
    "type": "extensions",
    "location": "[parameters('location')]",
    "apiVersion": "2015-08-01",
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

Gerenciar implantações com controle de versão ao criar novas pastas e passar essa raiz durante a distribuição. O caminho fluem para o modelo que implanta os recursos.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre o Deployment Manager. Vá para o próximo artigo para saber como implantar com o Gerenciador de implantação.

> [!div class="nextstepaction"]
> [Tutorial: Usar Gerenciador de Implantação do Azure com modelos do Gerenciamento de Recursos](./deployment-manager-tutorial.md)