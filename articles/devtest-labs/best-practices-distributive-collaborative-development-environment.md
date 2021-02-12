---
title: Desenvolvimento colaborativo distribuído de recursos de Azure DevTest Labs
description: Fornece as práticas recomendadas para configurar um ambiente de desenvolvimento distribuído e colaborativo para desenvolver recursos do DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 1ef6d7aa7d3cfd4fcc64eaa45259684dfcb9ccee
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97592357"
---
# <a name="best-practices-for-distributed-and-collaborative-development-of-azure-devtest-labs-resources"></a>Práticas recomendadas para desenvolvimento distribuído e colaborativo de recursos de Azure DevTest Labs
O desenvolvimento colaborativo distribuído permite que diferentes equipes ou pessoas desenvolvam e mantenham uma base de código. Para ter êxito, o processo de desenvolvimento depende da capacidade de criar, compartilhar e integrar informações. Esse princípio de desenvolvimento de chave pode ser usado em Azure DevTest Labs. Há vários tipos de recursos em um laboratório que são normalmente distribuídos entre diferentes laboratórios em uma empresa. Os diferentes tipos de recursos estão concentrados em duas áreas:

- Recursos que são armazenados internamente no laboratório (baseado em laboratório)
- Recursos que são armazenados em [repositórios externos que estão conectados ao laboratório](devtest-lab-add-artifact-repo.md) (com base no repositório de código). 

Este documento descreve algumas das práticas recomendadas que permitem a colaboração e a distribuição em várias equipes, garantindo a personalização e a qualidade em todos os níveis.

## <a name="lab-based-resources"></a>Recursos baseados em laboratório

### <a name="custom-virtual-machine-images"></a>Imagens de máquina virtual personalizada
Você pode ter uma fonte comum de imagens personalizadas que são implantadas em laboratórios à noite. Para obter informações detalhadas, consulte [fábrica de imagens](image-factory-create.md).    

### <a name="formulas"></a>Fórmulas
As [fórmulas](devtest-lab-manage-formulas.md) são específicas do laboratório e não têm um mecanismo de distribuição. Os membros do laboratório fazem todo o desenvolvimento de fórmulas. 

## <a name="code-repository-based-resources"></a>Recursos baseados no repositório de código
Há dois recursos diferentes que se baseiam em repositórios de código, artefatos e ambientes. Este artigo vai além dos recursos e como configurar de forma mais eficiente os repositórios e o fluxo de trabalho para permitir a capacidade de personalizar os artefatos e ambientes disponíveis no nível da organização ou da equipe.  Este fluxo de trabalho é baseado na [estratégia de ramificação do controle de código-fonte](/azure/devops/repos/tfvc/branching-strategies-with-tfvc?view=azure-devops)padrão. 

### <a name="key-concepts"></a>Principais conceitos
As informações de origem dos artefatos incluem metadados, scripts. As informações de origem dos ambientes incluem os modelos de metadados e do Resource Manager com quaisquer arquivos de suporte, como scripts do PowerShell, scripts de DSC, arquivos zip e assim por diante.  

### <a name="repository-structure"></a>Estrutura do repositório  
A configuração mais comum para o SCC (controle de código-fonte) é configurar uma estrutura de várias camadas para armazenar arquivos de código (modelos, metadados e scripts do Resource Manager) que são usados no nos laboratórios. Especificamente, crie repositórios diferentes para armazenar recursos que são gerenciados pelos diferentes níveis da empresa:   

- Recursos de toda a empresa.
- Recursos de unidade de negócios/divisão
- Recursos específicos da equipe.

Cada um desses níveis é vinculado a um repositório diferente em que a ramificação principal deve ser da qualidade de produção. As [ramificações](/azure/devops/repos/git/git-branching-guidance?view=azure-devops) em cada repositório seriam para o desenvolvimento desses recursos específicos (artefatos ou modelos). Essa estrutura se alinha bem com os DevTest Labs, pois você pode facilmente conectar vários repositórios e várias ramificações ao mesmo tempo para os laboratórios da organização. O nome do repositório está incluído na interface do usuário para evitar confusão quando há nomes, descrições e publicadores idênticos.
     
O diagrama a seguir mostra dois repositórios: um repositório da empresa que é mantido pela divisão de ti e um repositório de divisão mantido pela divisão R&D.

![Um distributivo de exemplo e ambiente de desenvolvimento colaborativo](./media/best-practices-distributive-collaborative-dev-env/distributive-collaborative-dev-env.png)
   
Essa estrutura em camadas permite o desenvolvimento e, ao mesmo tempo, mantém um nível mais alto de qualidade na ramificação principal, enquanto que vários repositórios conectados a um laboratório proporcionam maior flexibilidade.

## <a name="next-steps"></a>Próximas etapas    
Veja os artigos a seguir:

- Adicionar um repositório a um laboratório usando o [portal do Azure](devtest-lab-add-artifact-repo.md) ou por meio do [modelo de gerenciamento de recursos do Azure](add-artifact-repository.md)
- [Artefatos do DevTest Labs](devtest-lab-artifact-author.md)
- [Ambientes do DevTest Labs](devtest-lab-create-environment-from-arm.md).
