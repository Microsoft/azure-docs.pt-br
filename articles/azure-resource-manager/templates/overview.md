---
title: Visão geral de modelos
description: Descreve os benefícios usando modelos de Azure Resource Manager (modelos ARM) para a implantação de recursos.
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: 14bcbbd7a7ae7315dbb8e9d3e7e44ce0ffe0a4b4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103419976"
---
# <a name="what-are-arm-templates"></a>O que são modelos ARM?

Com a mudança para a nuvem, muitas equipes adotaram métodos de desenvolvimento Agile. Essas equipes iteram rapidamente. Eles precisam implantar repetidamente suas soluções na nuvem e saber que sua infraestrutura está em um estado confiável. À medida que a infraestrutura se tornou parte do processo iterativo, a divisão entre operações e desenvolvimento desapareceu. As equipes precisam gerenciar a infraestrutura e o código do aplicativo por meio de um processo unificado.

Para atender a esses desafios, você pode automatizar implantações e usar a prática da infraestrutura como código. No código, você define a infraestrutura que precisa ser implantada. O código de infraestrutura torna-se parte do seu projeto. Assim como o código do aplicativo, você armazena o código de infraestrutura em um repositório de origem e a versão. Qualquer um em sua equipe pode executar o código e implantar ambientes semelhantes.

Para implementar a infraestrutura como código para suas soluções do Azure, use modelos de Azure Resource Manager (modelos ARM). O modelo é um arquivo JavaScript Object Notation (JSON) que define a infraestrutura e a configuração do seu projeto. O modelo usa a sintaxe declarativa, que permite declarar o que você pretende implantar sem precisar gravar a sequência de comandos de programação para criá-lo. No modelo, você especifica os recursos a serem implantados e as propriedades desses recursos.

Apresentamos uma nova linguagem para o desenvolvimento de modelos ARM. O idioma é denominado bicep e está em visualização no momento. Os modelos bicep e JSON oferecem os mesmos recursos. Você pode converter o modelo entre os dois idiomas. O bicep fornece uma sintaxe que é mais fácil de usar para a criação de modelos. Para obter mais informações, consulte [o que é bicep (versão prévia)?](bicep-overview.md).

Para saber mais sobre como você pode começar a usar os modelos do ARM, consulte o vídeo a seguir.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Enablement/How-and-why-to-learn-about-ARM-templates/player]

## <a name="why-choose-arm-templates"></a>Por que escolher modelos de ARM?

Se você estiver tentando decidir entre usar modelos ARM e uma das outras infraestruturas como serviços de código, considere as seguintes vantagens de usar modelos:

* **Sintaxe declarativa**: os modelos ARM permitem criar e implantar uma infraestrutura inteira do Azure de forma declarativa. Por exemplo, você pode implantar não apenas máquinas virtuais, mas também a infraestrutura de rede, os sistemas de armazenamento e quaisquer outros recursos que você possa precisar.

* **Resultados repetíveis**: implante repetidamente sua infraestrutura em todo o ciclo de vida de desenvolvimento e tenha confiança de que seus recursos sejam implantados de maneira consistente. Os modelos são idempotentes, o que significa que você pode implantar o mesmo modelo muitas vezes e obter os mesmos tipos de recursos no mesmo estado. Você pode desenvolver um modelo que representa o estado desejado, em vez de desenvolver muitos modelos separados para representar atualizações.

* **Orquestração**: você não precisa se preocupar com as complexidades das operações de ordenação. O Resource Manager orquestra a implantação de recursos interdependentes para que eles sejam criados na ordem correta. Quando possível, o Resource Manager implanta recursos em paralelo para que suas implantações sejam concluídas mais rapidamente do que as implantações seriais. Você implanta o modelo por meio de um comando, em vez de vários comandos imperativos.

   ![Comparação de Implantação de modelo](./media/overview/template-processing.png)

* **Arquivos modulares**: você pode dividir seus modelos em componentes menores e reutilizáveis e vinculá-los no momento da implantação. Você também pode aninhar um modelo dentro de outros modelos.

* **Criar qualquer recurso do Azure**: você pode usar imediatamente novos serviços e recursos do Azure em modelos. Assim que um provedor de recursos apresenta novos recursos, você pode implantar esses recursos por meio de modelos. Você não precisa aguardar que as ferramentas ou os módulos sejam atualizados antes de usar os novos serviços.

* **Extensibilidade**: com [scripts de implantação](deployment-script-template.md), você pode adicionar scripts do PowerShell ou bash aos seus modelos. Os scripts de implantação estendem sua capacidade de configurar recursos durante a implantação. Um script pode ser incluído no modelo ou armazenado em uma fonte externa e referenciado no modelo. Os scripts de implantação oferecem a capacidade de concluir a configuração de seu ambiente de ponta a ponta em um único modelo ARM.

* **Teste**: você pode certificar-se de que seu modelo segue as diretrizes recomendadas testando-o com o ARM-TTK (Kit de ferramentas de modelo do braço). Este kit de teste é um script do PowerShell que você pode baixar do [GitHub](https://github.com/Azure/arm-ttk). O kit de ferramentas facilita o desenvolvimento de conhecimentos usando a linguagem de modelo.

* **Visualizar alterações**: você pode usar a [operação What-If](template-deploy-what-if.md) para obter uma visualização das alterações antes de implantar o modelo. Com What-If, você verá quais recursos serão criados, atualizados ou excluídos, e quaisquer propriedades de recurso que serão alteradas. A operação What-if verifica o estado atual do seu ambiente e elimina a necessidade de gerenciar o estado.

* **Validação interna**: seu modelo é implantado somente após a aprovação da validação. O Gerenciador de recursos verifica o modelo antes de iniciar a implantação para garantir que a implantação terá sucesso. Sua implantação é menos provável de parar em um estado de meia-conclusão.

* **Implantações controladas**: no portal do Azure, você pode examinar o histórico de implantação e obter informações sobre a implantação do modelo. Você pode ver o modelo que foi implantado, os valores de parâmetro passados e todos os valores de saída. Outra infraestrutura como serviços de código não é rastreada por meio do Portal.

   ![Histórico de implantações](./media/overview/deployment-history.png)

* **Política como código**: [Azure Policy](../../governance/policy/overview.md) é uma política como estrutura de código para automatizar a governança. Se você estiver usando políticas do Azure, a correção de política será feita em recursos sem conformidade quando implantada por meio de modelos.

* **Plantas de implantação**: você pode aproveitar os [planos gráficos](../../governance/blueprints/overview.md) fornecidos pela Microsoft para atender aos padrões normativos e de conformidade. Esses planos gráficos incluem modelos predefinidos para várias arquiteturas.

* **Integração de CI/CD**: você pode integrar modelos em suas ferramentas de integração contínua e implantação contínua (CI/CD), que podem automatizar seus pipelines de versão para atualizações rápidas e confiáveis de aplicativos e de infraestrutura. Usando o Azure DevOps e a tarefa de modelo do Resource Manager, você pode usar Azure Pipelines para criar e implantar continuamente projetos de modelo ARM. Para saber mais, veja [projeto do vs com pipelines](add-template-to-azure-pipelines.md) e [tutorial: integração contínua de modelos de Azure Resource Manager com Azure pipelines](./deployment-tutorial-pipeline.md).

* **Código exportável**: você pode obter um modelo para um grupo de recursos existente exportando o estado atual do grupo de recursos ou exibindo o modelo usado para uma implantação específica. A exibição do [modelo exportado](export-template-portal.md) é uma maneira útil de saber mais sobre a sintaxe do modelo.

* **Ferramentas de criação**: você pode criar modelos com [Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md) e a extensão de ferramenta de modelo. Você Obtém o IntelliSense, o realce de sintaxe, a ajuda online e muitas outras funções de linguagem. Além de Visual Studio Code, você também pode usar o [Visual Studio](create-visual-studio-deployment-project.md).

## <a name="template-file"></a>Arquivo de modelo

Em seu modelo, você pode escrever [expressões de modelo](template-expressions.md) que estendam os recursos do JSON. Essas expressões fazem uso das [funções](template-functions.md) fornecidas pelo Resource Manager.

O modelo tem as seguintes seções:

* [Parâmetros](template-parameters.md) – forneça valores durante a implantação que permitem que o mesmo modelo seja usado com ambientes diferentes.

* [Variáveis](template-variables.md) – defina valores que são reutilizados em seus modelos. Eles podem ser construídos com base em valores de parâmetro.

* [Funções definidas pelo usuário](template-user-defined-functions.md) – crie funções personalizadas que simplificam seu modelo.

* [Recursos](resource-declaration.md) -especifique os recursos a serem implantados.

* [Saídas](template-outputs.md) – retornam valores dos recursos implantados.

## <a name="template-deployment-process"></a>Processo de Implantação de modelo

Quando você implanta um modelo, o Resource Manager converte o modelo em operações da API REST. Por exemplo, quando o Gerenciador de Recursos recebe um modelo com a seguinte definição de recurso:

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2019-04-01",
    "name": "mystorageaccount",
    "location": "westus",
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "StorageV2",
    "properties": {}
  }
]
```

Converte a definição para a seguinte operação de API REST, que é enviada para o provedor de recursos Microsoft.Storage:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/mystorageaccount?api-version=2019-04-01
REQUEST BODY
{
  "location": "westus",
  "sku": {
    "name": "Standard_LRS"
  },
  "kind": "StorageV2",
  "properties": {}
}
```

Observe que o **apiVersion** que você definiu no modelo para o recurso é usado como a versão de API para a operação REST. Você pode implantar o modelo repetidamente e ter confiança de que ele continuará a funcionar. Usando a mesma versão de API, você não precisa se preocupar com alterações significativas que podem ser introduzidas em versões posteriores.

Para implantar um modelo, use qualquer uma das seguintes opções:

* [Azure portal](deploy-portal.md)
* [CLI do Azure](deploy-cli.md)
* [PowerShell](deploy-powershell.md)
* [REST API](deploy-rest.md)
* [Botão no repositório do GitHub](deploy-to-azure-button.md)
* [Azure Cloud Shell](deploy-cloud-shell.md)

## <a name="template-design"></a>Design de modelo

Como você define grupos de recursos e modelos é de sua responsabilidade e de como você deseja gerenciar a sua solução. Por exemplo, você pode implantar seu aplicativo de três camadas por meio de um único modelo para um único grupo de recursos.

![modelo de três camadas](./media/overview/3-tier-template.png)

Mas, você não precisa definir toda a sua infraestrutura em um único modelo. Muitas vezes, faz sentido dividir seus requisitos de implantação em um conjunto de modelos com destinação e fins específicos. Você pode reutilizar esses modelos facilmente para soluções diferentes. Para implantar uma solução específica, você cria um modelo principal que vincula todos os modelos necessários. A imagem a seguir mostra como implantar uma solução de três camadas com o modelo pai que inclui três modelos aninhados.

![modelo de camadas aninhadas](./media/overview/nested-tiers-template.png)

Ao prever suas camadas com ciclos de vida separados, você pode implantar os três níveis para separar grupos de recursos. Observe que os recursos ainda podem ser vinculados aos recursos em outros grupos de recursos.

![modelo de camada](./media/overview/tier-templates.png)

Para obter informações sobre modelos aninhados, confira [Usando modelos vinculados com o Azure Resource Manager](linked-templates.md).

## <a name="share-templates"></a>Compartilhar modelos

Depois de criar seu modelo, talvez você queira compartilhá-lo com outros usuários em sua organização. As [especificações de modelo](template-specs.md) permitem que você armazene um modelo como um tipo de recurso. Você usa o controle de acesso baseado em função para gerenciar o acesso à especificação do modelo. Os usuários com acesso de leitura à especificação do modelo podem implantá-lo, mas não alterar o modelo.

Essa abordagem significa que você pode compartilhar com segurança modelos que atendam aos padrões da sua organização.

## <a name="next-steps"></a>Próximas etapas

* Para obter um tutorial passo a passo que orienta você durante o processo de criação de um modelo, confira [Tutorial: criar e implantar seu primeiro modelo do ARM](template-tutorial-create-first-template.md).
* Para saber mais sobre os modelos do ARM por meio de um conjunto guiado de módulos no Microsoft Learn, confira [Implantar e gerenciar recursos no Azure usando modelos do ARM](/learn/paths/deploy-manage-resource-manager-templates/).
* Para obter informações sobre as propriedades em arquivos de modelo, consulte [entender a estrutura e a sintaxe de modelos ARM](template-syntax.md).
* Para saber mais sobre como exportar modelos, consulte [início rápido: criar e implantar modelos de ARM usando o portal do Azure](quickstart-create-templates-use-the-portal.md).
* Para obter respostas a perguntas comuns, consulte perguntas frequentes [sobre modelos de ARM](frequently-asked-questions.md).
