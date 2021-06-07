---
title: Perguntas frequentes sobre o modelo ARM
description: Perguntas frequentes sobre modelos de Azure Resource Manager (modelos ARM).
ms.topic: conceptual
ms.date: 03/03/2021
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: e64253aa79cfaeb2655bb091d038dc7c98b198bd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103419397"
---
# <a name="frequently-asked-questions-about-arm-templates"></a>Perguntas frequentes sobre modelos do ARM

Este artigo responde a perguntas frequentes sobre modelos de Azure Resource Manager (modelos ARM).

## <a name="getting-started"></a>Introdução

* **O que são modelos de ARM e por que devo usá-los?**

  Os modelos de ARM são arquivos JSON em que você define o que deseja implantar no Azure. Os modelos ajudam você a implementar uma solução de infraestrutura como código para o Azure. Sua organização pode implantar repetidamente e de forma confiável a infraestrutura necessária em ambientes diferentes.

  Para saber mais sobre como os modelos do ARM ajudam a gerenciar sua infraestrutura do Azure, consulte [o que são modelos de ARM?](overview.md)

* **Como fazer introdução aos modelos?**

  Para simplificar a criação de modelos de ARM, você precisa das ferramentas corretas. É recomendável instalar [Visual Studio Code](https://code.visualstudio.com/) e a [extensão de ferramentas de Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools). Para obter uma breve introdução a essas ferramentas, consulte [início rápido: criar modelos de ARM com Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md).

  Quando estiver pronto para saber mais sobre a criação de modelos ARM, inicie a [série de tutoriais iniciantes em modelos ARM](template-tutorial-create-first-template.md). Esses tutoriais levam você passo a passo pelo processo de construção de um modelo ARM. Você aprende sobre as diferentes seções do modelo e como elas funcionam em conjunto. Esse conteúdo também está disponível como um [módulo Microsoft Learn](/learn/modules/authoring-arm-templates/).

* **Devo usar modelos ARM ou Terraform para implantar no Azure?**

  Use a opção que você gostou melhor. Ambos os serviços ajudam você a automatizar implantações no Azure.

  Acreditamos que há benefícios em usar modelos de ARM em outros serviços de infraestrutura como código. Para saber mais sobre esses benefícios, confira [por que escolher modelos de ARM?](overview.md#why-choose-arm-templates)

## <a name="build-2020"></a>Build 2020

* **Eu perdi sua apresentação no Microsoft Build 2020. A apresentação está disponível para exibição?**

  Sim, [Assista a ele a qualquer momento](https://mybuild.microsoft.com/sessions/82984db4-37a4-4ed3-bf8b-13298841ed18?source=sessions).

* **Onde posso obter mais informações sobre os novos recursos que você anunciou na compilação?**

  Para obter informações gerais sobre os recursos que estamos trabalhando, junte-se ao [grupo do Yammer de implantações do Azure Advisor](https://aka.ms/ARMMeet).

  Para saber mais sobre a nova linguagem de modelo, [Inscreva-se para notificações](https://aka.ms/armLangUpdates).

  Para saber mais sobre as especificações de modelo, consulte [Azure Resource Manager especificações de modelo (versão prévia)](template-specs.md).

## <a name="creating-and-testing-templates"></a>Criando e testando modelos

* **Onde posso saber mais sobre as práticas recomendadas para modelos de ARM?**

  Para obter recomendações sobre como implementar seus modelos, consulte [práticas recomendadas do modelo ARM](template-best-practices.md). Depois de criar um modelo, execute o [Kit de ferramentas de teste ARM](https://github.com/azure/arm-ttk). Ele verifica se o modelo corresponde às práticas recomendadas.

* **Configurei meu ambiente por meio do Portal. Há alguma maneira de obter o modelo de um grupo de recursos existente?**

  Sim, você pode [exportar o modelo](export-template-portal.md) de um grupo de recursos. O modelo exportado é um bom ponto de partida para aprender sobre modelos, mas você provavelmente desejará revisá-lo antes de usá-lo em um ambiente de produção.

  Ao exportar o modelo, você pode selecionar os recursos que deseja incluir no modelo.

* **Posso criar um grupo de recursos em um modelo ARM e implantar recursos nele?**

  Sim, você pode criar um grupo de recursos em um modelo ao implantar o modelo no nível de sua assinatura do Azure. Para obter um exemplo de como criar um grupo de recursos e implantar recursos, consulte [grupo de recursos e recursos](deploy-to-subscription.md#resource-groups).

* **Posso criar uma assinatura em um modelo do ARM?**

  Sim, para obter mais informações, consulte [criar programaticamente assinaturas do Azure com as APIs mais recentes](../../cost-management-billing/manage/programmatically-create-subscription-enterprise-agreement.md).

* **Como posso testar meu modelo antes de implantá-lo?**

  É recomendável executar o [Kit de ferramentas de teste ARM](https://github.com/azure/arm-ttk) e a [operação What-If](template-deploy-what-if.md) em seus modelos antes de implantá-los. O kit de ferramentas de teste verifica se o modelo usa as práticas recomendadas. Ele fornece avisos quando identifica alterações que podem melhorar a forma como você implementou o modelo.

  A operação What-If mostra as alterações que seu modelo fará em seu ambiente. Você pode ver alterações não intencionais antes que elas sejam implantadas. O What-If também retorna quaisquer erros que possam ser detectados durante a validação de simulação. Por exemplo, se o modelo contiver um erro sintático, ele retornará esse erro. Ele também retorna qualquer erro que possa determinar sobre o estado final dos recursos implantados. Por exemplo, se o modelo implantar uma conta de armazenamento com um nome que já está em uso, What-If retornará esse erro.

* **Onde posso encontrar informações sobre as propriedades que estão disponíveis para cada tipo de recurso?**

  VS Code fornece IntelliSense para trabalhar com as propriedades do recurso. Você também pode exibir a [referência de modelo](/azure/templates/) para propriedades e descrições.

* **Preciso criar várias instâncias de um tipo de recurso. Como fazer criar um iterador em meu modelo?**

  Use o elemento Copy para especificar mais de uma instância. Você pode usar Copiar em [recursos](copy-resources.md), [Propriedades](copy-properties.md), [variáveis](copy-variables.md)e [saídas](copy-outputs.md).

## <a name="template-language"></a>Idioma do modelo

* **Ouvi dizer que você está trabalhando em uma nova linguagem de modelo. Onde posso saber mais sobre isso?**

  Para saber mais sobre a nova linguagem, consulte [o que é bicep (versão prévia)?](bicep-overview.md).

* **Há um plano para dar suporte à criação de modelos no YAML?**

  Atualmente, não há nenhum plano para dar suporte a YAML. Acreditamos que a nova linguagem de modelo oferecerá uma solução que é mais fácil de usar do que YAML ou JSON.

* **Ainda posso escrever modelos em JSON depois que a nova linguagem de modelo for lançada?**

  Sim, você pode continuar usando modelos JSON.

* **Você oferecerá uma ferramenta para converter meus modelos JSON para o novo idioma do modelo?**

  Sim. Consulte [convertendo modelos de ARM entre JSON e bicep](bicep-decompile.md).

## <a name="template-specs"></a>Especificações de Modelo

* **Como posso começar a usar a versão de visualização das especificações de modelo?**

  Instale a versão mais recente do PowerShell ou CLI do Azure. Para o Azure PowerShell, use a [versão 5.0.0 ou posterior](/powershell/azure/install-az-ps). Para a CLI do Azure, use a [versão 2.14.2 ou posterior](/cli/azure/install-azure-cli).

* **Como as especificações de modelo e os planos gráficos do Azure estão relacionados?**

  Os planos gráficos do Azure usarão as especificações de modelo em sua implementação, substituindo o `blueprint definition` recurso por um `template spec` recurso. Forneceremos um caminho de migração para converter a definição de Blueprint em uma especificação de modelo, mas as APIs de definição de plano gráfico ainda terão suporte. Não há nenhuma alteração no `blueprint assignment` recurso. Plantas permanecerão uma experiência do usuário para compor um ambiente controlado no Azure.

* **As especificações de modelo substituem modelos vinculados?**

  Não, mas as especificações de modelo foram projetadas para funcionar bem com modelos vinculados. Você não precisa mover o modelo vinculado para um ponto de extremidade publicamente acessível antes de implantar o modelo pai. Em vez disso, você empacota o modelo pai e seus artefatos juntos ao criar a especificação do modelo.

* **As especificações de modelo podem ser compartilhadas entre assinaturas?**

  Sim, eles podem ser usados em assinaturas, desde que o usuário tenha acesso de leitura à especificação do modelo. As especificações de modelo não podem ser usadas em locatários.

## <a name="scripts-in-templates"></a>Scripts em modelos

* **Posso incluir um script em meu modelo para executar tarefas que não são possíveis em um modelo?**

  Sim, use [scripts de implantação](deployment-script-template.md). Você pode incluir Azure PowerShell ou CLI do Azure scripts em seus modelos. O recurso está em versão prévia.

* **Ainda posso usar extensões de script personalizadas e a DSC (configuração de estado desejado)?**

  Essas opções ainda estão disponíveis e não foram alteradas. Os scripts de implantação são projetados para executar ações que não estão relacionadas à VM convidada. Se você precisar executar um script em um sistema operacional de host em uma VM, a extensão de script personalizado e/ou DSC seria uma opção melhor. No entanto, os scripts de implantação têm vantagens, como definir a duração do tempo limite.

* **Há suporte para scripts de implantação no Azure governamental?**

  Sim, você pode usar scripts de implantação em US Gov Arizona e US Gov-Virgínia.

## <a name="preview-changes-before-deployment"></a>Visualizar alterações antes da implantação

* **Posso visualizar as alterações que ocorrerão antes de implantar um modelo?**

  Sim, use o [recurso What-If](template-deploy-what-if.md). Ele avalia o estado atual do seu ambiente e o compara com o estado que existirá após a implantação. Você pode examinar as alterações resumidas para ter certeza de que o modelo não tem resultados inesperados.

* **Posso usar What-If com os modos incremental e completo?**

  Sim, há suporte para ambos os [modos de implantação](deployment-modes.md) . Para obter um exemplo de como usar o modo incremental, consulte [Executar operação What-If](template-deploy-what-if.md#run-what-if-operation). Para obter um exemplo de como usar o modo completo, consulte [Confirmar exclusão](template-deploy-what-if.md#confirm-deletion).

* **Funciona com modelos vinculados?**

  Sim, e-se avalia o estado do modelo pai e seus modelos vinculados.

* **Posso usar What-If em um pipeline do Azure?**

  Sim, você pode usar What-If para verificar se o pipeline deve continuar.

* **Quando uso What-If, vejo alterações nas propriedades que não estão no meu modelo. Esse "ruído" é esperado?**

  Estamos trabalhando para reduzir o ruído. Você nos ajuda a melhorar enviando problemas em nosso repositório GitHub aqui: https://aka.ms/WhatIfIssues

## <a name="template-visualizer"></a>Visualizador de modelos

* **Há uma maneira de Visualizar meu modelo de ARM e seus recursos?**

  Temos uma [extensão de vs Code contribuída pela Comunidade](https://aka.ms/ARMVisualizer) que faz um ótimo trabalho de visualização de seu modelo de ARM. Ele mostra os recursos que você está implantando e as relações entre eles.

* **Posso usar o Visualizador de modelos fora do VS Code?**

  O Visualizador de modelos está sendo visualizado no Portal. Para obter mais informações, Assista a esta [pequena sessão do Build](https://mybuild.microsoft.com/sessions/0525094b-1fd2-4f69-bfd6-6d2fff6ffe5f?source=sessions).

## <a name="deployment-limits"></a>Limites de implantação

* **Quantos grupos de recursos posso implantar em uma única operação de implantação?**

  No passado, esse limite era cinco grupos de recursos. Recentemente, ele aumentou para 800 grupos de recursos. Para obter mais informações, consulte [criar grupos de recursos e recursos no nível da assinatura](deploy-to-subscription.md).

* **Recebi um erro de limitação a 800 implantações no histórico de implantação. O que devo fazer?**

  Estamos alterando como o histórico de implantação de um grupo de recursos é mantido. No passado, era necessário excluir manualmente as implantações desse histórico para evitar esse erro. A partir de junho de 2020, excluiremos automaticamente as implantações do histórico à medida que você chegar perto do limite. Para obter mais informações, confira [Exclusões automáticas do histórico de implantações](deployment-history-deletions.md).

  A exclusão de uma implantação do histórico não afeta os recursos implantados.

## <a name="templates-and-devops"></a>Modelos e DevOps

* **Posso integrar modelos do ARM ao Azure Pipelines?**

  Sim. Para obter uma explicação de como usar o modelo e os pipelines, consulte [tutorial: integração contínua de modelos ARM com Azure pipelines](deployment-tutorial-pipeline.md) e [integrar modelos ARM com Azure pipelines](add-template-to-azure-pipelines.md).

* **Posso usar ações do GitHub para implantar um modelo?**

  Sim, consulte [implantar modelos de ARM usando ações do GitHub](deploy-github-actions.md).

## <a name="next-steps"></a>Próximas etapas

Para obter uma introdução aos modelos do ARM, consulte [o que são modelos de ARM?](overview.md).
