---
title: Implantar ambientes de modelo aninhados no Azure DevTest Labs
description: Saiba como implantar modelos de Azure Resource Manager aninhados para fornecer ambientes com Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 39002e286fafd4f813333a14ed86256a517897e9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85481333"
---
# <a name="deploy-nested-azure-resource-manager-templates-for-testing-environments"></a>Implantar modelos de Azure Resource Manager aninhados para ambientes de teste
Uma implantação aninhada permite executar outros modelos de Azure Resource Manager de dentro de um modelo principal do Resource Manager. Ele permite decompor sua implantação em um conjunto de modelos de destino e específicos de finalidade. Ele fornece benefícios em termos de teste, reutilização e legibilidade. O artigo [usando modelos vinculados ao implantar recursos do Azure](../azure-resource-manager/templates/linked-templates.md) fornece uma boa visão geral dessa solução com vários exemplos de código. Este artigo fornece um exemplo específico para Azure DevTest Labs. 

## <a name="key-parameters"></a>Parâmetros de chave
Embora você possa criar seu próprio modelo do Resource Manager do zero, é recomendável usar o [projeto do grupo de recursos do Azure](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) no Visual Studio, o que torna mais fácil desenvolver e depurar modelos. Quando você adiciona um recurso de implantação aninhado a azuredeploy.jsno, o Visual Studio adiciona vários itens para tornar o modelo mais flexível. Esses itens incluem a subpasta com o modelo secundário e o arquivo de parâmetros, nomes de variáveis dentro do arquivo de modelo principal e dois parâmetros para o local de armazenamento para os novos arquivos. Os **_artifactsLocation** e **_artifactsLocationSasToken** são os principais parâmetros usados pelo DevTest Labs. 

Se você não estiver familiarizado com o modo como os DevTest Labs funcionam com ambientes, consulte [criar ambientes de várias VMs e recursos de PaaS com modelos de Azure Resource Manager](devtest-lab-create-environment-from-arm.md). Seus modelos são armazenados no repositório vinculado ao laboratório no DevTest Labs. Quando você cria um novo ambiente com esses modelos, os arquivos são movidos para um contêiner de armazenamento do Azure no laboratório. Para poder identificar e copiar os arquivos aninhados, o DevTest Labs identifica os parâmetros _artifactsLocation e _artifactsLocationSasToken e copia as subpastas até o contêiner de armazenamento. Em seguida, ele insere automaticamente o local e o token SaS (assinatura de acesso compartilhado) em parâmetros. 

## <a name="nested-deployment-example"></a>Exemplo de implantação aninhada
Veja um exemplo simples de uma implantação aninhada:

```json

"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
"contentVersion": "1.0.0.0",
"parameters": {
    "_artifactsLocation": {
        "type": "string"
    },
    "_artifactsLocationSasToken": {
        "type": "securestring"
    }},
"variables": {
    "NestOneTemplateFolder": "nestedtemplates",
    "NestOneTemplateFileName": "NestOne.json",
    "NestOneTemplateParametersFileName": "NestOne.parameters.json"},
    "resources": [
    {
        "name": "NestOne",
        "type": "Microsoft.Resources/deployments",
        "apiVersion": "2016-09-01",
        "dependsOn": [ ],
        "properties": {
            "mode": "Incremental",
            "templateLink": {
                "uri": "[concat(parameters('_artifactsLocation'), '/', variables('NestOneTemplateFolder'), '/', variables('NestOneTemplateFileName'), parameters('_artifactsLocationSasToken'))]",
                "contentVersion": "1.0.0.0"
            },
            "parametersLink": {
                "uri": "[concat(parameters('_artifactsLocation'), '/', variables('NestOneTemplateFolder'), '/', variables('NestOneTemplateParametersFileName'), parameters('_artifactsLocationSasToken'))]",
                "contentVersion": "1.0.0.0"
            }
        }    
    }],
"outputs": {}
```

A pasta no repositório que contém este modelo tem uma subpasta `nestedtemplates` com os arquivos **NestOne.js** e **NestOne.parameters.js**. No **azuredeploy.jsem**, o URI para o modelo é criado usando o local dos artefatos, a pasta de modelo aninhada, o nome do arquivo de modelo aninhado. Da mesma forma, o URI para os parâmetros é criado usando o local de artefatos, a pasta de modelo aninhada e o arquivo de parâmetro para o modelo aninhado. 

Aqui está a imagem da mesma estrutura de projeto no Visual Studio: 

![Estrutura do projeto no Visual Studio](./media/deploy-nested-template-environments/visual-studio-project-structure.png)

Você pode adicionar mais pastas na pasta primária, mas não em um único nível. 

## <a name="next-steps"></a>Próximas etapas
Consulte os seguintes artigos para obter detalhes sobre ambientes: 

- [Criar ambientes de várias VMs e recursos de PaaS com modelos do Azure Resource Manager](devtest-lab-create-environment-from-arm.md)
- [Configurar e usar ambientes públicos no Azure DevTest Labs](devtest-lab-configure-use-public-environments.md)
- [Conectar um ambiente à rede virtual do seu laboratório no Azure DevTest Labs](connect-environment-lab-virtual-network.md)