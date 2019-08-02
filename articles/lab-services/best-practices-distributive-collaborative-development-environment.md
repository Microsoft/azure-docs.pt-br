---
title: Distribuído desenvolvimento colaborativo de recursos do Azure DevTest Labs | Microsoft Docs
description: Fornece as práticas recomendadas para configurar um ambiente de desenvolvimento distribuído e de colaboração para desenvolver recursos do DevTest Labs.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2019
ms.author: spelluru
ms.openlocfilehash: 8ffc8ed3f84284ff69e9515cba0982790b823a37
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543762"
---
# <a name="best-practices-for-distributed-and-collaborative-development-of-azure-devtest-labs-resources"></a>Práticas recomendadas para o desenvolvimento distribuído e de colaboração de recursos do Azure DevTest Labs
Desenvolvimento colaborativo distribuído permite que diferentes equipes ou pessoas para desenvolver e manter um código de base. Para ter êxito, o processo de desenvolvimento depende da capacidade para criar, compartilhar e integrar as informações. Esse princípio de desenvolvimento principal pode ser usado no Azure DevTest Labs. Há vários tipos de recursos em um laboratório que normalmente são distribuídos entre diferentes laboratórios em uma empresa. Os diferentes tipos de recursos se concentram em duas áreas:

- Recursos que são armazenados internamente no laboratório (baseado no laboratório)
- Recursos que são armazenados em [repositórios externos que estão conectados ao laboratório](devtest-lab-add-artifact-repo.md) (com base no repositório de código). 

Este documento descreve algumas práticas recomendadas que permitem a colaboração e a distribuição em várias equipes, garantindo a personalização e a qualidade em todos os níveis.

## <a name="lab-based-resources"></a>Recursos baseados em laboratório

### <a name="custom-virtual-machine-images"></a>Imagens de máquina virtual personalizada
Você pode ter uma fonte comum de imagens personalizadas que são implantados em laboratórios em uma base noturna. Para obter informações detalhadas, consulte [fábrica de imagem](image-factory-create.md).    

### <a name="formulas"></a>Fórmulas
[Fórmulas](devtest-lab-manage-formulas.md) são específicas do laboratório e não tiver um mecanismo de distribuição. Os membros de laboratório fazer todo o desenvolvimento de fórmulas. 

## <a name="code-repository-based-resources"></a>Recursos de baseada em repositório de código
Há dois recursos diferentes que se baseiam em ambientes, artefatos e repositórios de código. Este artigo percorre os recursos e como definir com mais eficiência repositórios e fluxo de trabalho para permitir que a capacidade de personalizar os artefatos disponíveis e os ambientes no nível da organização ou equipe.  Esse fluxo de trabalho se baseia no padrão [estratégia de ramificação de controle do código-fonte](/azure/devops/repos/tfvc/branching-strategies-with-tfvc?view=azure-devops). 

### <a name="key-concepts"></a>Principais conceitos
As informações de fonte de artefatos incluem metadados, scripts. As informações de origem para ambientes incluem metadados e modelos do Resource Manager com os arquivos de suporte, como scripts do PowerShell, DSC scripts, arquivos Zip e assim por diante.  

### <a name="repository-structure"></a>Estrutura do repositório  
A configuração mais comum para controle do código-fonte (SCC) é configurar uma estrutura de várias camada para armazenar arquivos de código (modelos do Resource Manager, metadados e scripts) que são usados para nos laboratórios. Especificamente, crie diferentes repositórios para armazenar recursos que são gerenciados pelos diferentes níveis da empresa:   

- Recursos de toda a empresa.
- Recursos de divisão/unidade-wide Business
- Recursos específicos da equipe.

Cada um desses níveis vincular a um repositório diferente, onde o branch mestre deve ser a qualidade de produção. O [ramificações](/azure/devops/repos/git/git-branching-guidance?view=azure-devops) em cada repositório seria para o desenvolvimento desses recursos específicos (artefatos ou modelos). Essa estrutura esteja bem alinhada ao DevTest Labs, você pode conectar facilmente vários repositórios e várias ramificações ao mesmo tempo para laboratórios da organização. O nome do repositório está incluído na interface do usuário (IU) para evitar confusão quando há nomes idênticos, a descrição e o publicador.
     
O diagrama a seguir mostra dois repositórios: um repositório de empresa que é mantido pela divisão de TI e um repositório de divisão mantido pela divisão do p & D.

![Um ambiente de desenvolvimento distributiva e de colaboração de exemplo](./media/best-practices-distributive-collaborative-dev-env/distributive-collaborative-dev-env.png)
   
Essa estrutura em camadas permite o desenvolvimento, mantendo um nível mais alto de qualidade no branch mestre enquanto ter vários repositórios conectados a um laboratório permite maior flexibilidade.

## <a name="next-steps"></a>Próximas etapas    
Confira os seguintes artigos:

- Adicionar um repositório para um laboratório usando o [portal do Azure](devtest-lab-add-artifact-repo.md) ou por meio de [modelo de gerenciamento de recursos do Azure](add-artifact-repository.md)
- [Artefatos de laboratórios de desenvolvimento/teste](devtest-lab-artifact-author.md)
- [Ambientes do DevTest Labs](devtest-lab-create-environment-from-arm.md).
