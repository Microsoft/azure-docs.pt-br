---
title: Criar um ambiente centrado no aplicativo-Azure
description: Este artigo demonstra como criar um ambiente centrado em aplicativos com Cloud Shell colônia formigas e Microsoft Azure.
ms.topic: how-to
ms.date: 11/26/2020
ms.openlocfilehash: 88244f268d5ed038e9bb7082d3d5cc1179e5ec4e
ms.sourcegitcommit: f6236e0fa28343cf0e478ab630d43e3fd78b9596
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/19/2020
ms.locfileid: "94918061"
---
# <a name="create-an-application-centric-environment"></a>Criar um ambiente centrado no aplicativo

A [CloudShell colônia formigas da quali](https://azuremarketplace.microsoft.com/marketplace/apps/quali_systems.cloudshell_colony?tab=Overview) é uma plataforma SaaS para fornecer automação de infraestrutura em escala para ambientes complexos centrados em aplicativos em tecnologias de nuvem, incluindo Azure e kubernetes. CloudShell colônia formigas complementa Azure DevTest Labs para ajudar as equipes de desenvolvedores a implantar aplicativos complexos em todo o fluxo de valor, até a produção.

Este artigo demonstra como criar um ambiente centrado em aplicativos com CloudShell colônia formigas e Microsoft Azure.

## <a name="set-up-the-environment-with-cloudshell-colony-and-microsoft-azure"></a>Configurar o ambiente com CloudShell colônia formigas e Microsoft Azure

1. Inscreva-se para sua avaliação gratuita do [colônia formigas](https://azuremarketplace.microsoft.com/marketplace/apps/quali_systems.cloudshell_colony?tab=Overview).

    :::image type="content" source="./media/create-application-centric-environment/free-trial.png" alt-text="Inscreva-se em uma avaliação gratuita":::    
1. Vincule sua conta do Azure ([Exibir etapas aqui](https://colonysupport.quali.com/hc/articles/360008222234)).

    :::image type="content" source="./media/create-application-centric-environment/welcome.png" alt-text="Bem-vindo ao colônia formigas":::     
1. Convide usuários em seu espaço.
1. Crie seu primeiro plano gráfico usando um arquivo YAML (veja[as etapas aqui](https://colonysupport.quali.com/hc/articles/360001680807-Steps-to-Developing-a-Blueprint)).
    1. Vincule seu repositório de plantas no GitHub ou BitBucket ao colônia formigas.
    1. Use um plano gráfico de exemplo colônia formigas como a base e modifique conforme apropriado.

        :::image type="content" source="./media/create-application-centric-environment/performance-stress-tests.png" alt-text="Teste de estresse":::    
    1. Publique seu plano gráfico para outras pessoas usarem.
1. Inicie o ambiente do aplicativo em uma área restrita usando o colônia formigas.

    :::image type="content" source="./media/create-application-centric-environment/blueprints.png" alt-text="Inicie o ambiente de aplicativo em uma área restrita usando colônia formigas":::    

> [!NOTE]
> Você também pode integrar seu plano gráfico como parte de um fluxo de trabalho de CI/CD no Azure DevOps (veja[as etapas aqui](https://colonysupport.quali.com/hc/articles/360008464234)).

:::image type="content" source="./media/create-application-centric-environment/devops-pipeline.png" alt-text="Conectar-se ao pipeline DevOps do Azure":::    

## <a name="next-steps"></a>Próximas etapas

[Solicitar uma demonstração de colônia formigas](https://info.quali.com/cloudshell-colony-demo-request)
