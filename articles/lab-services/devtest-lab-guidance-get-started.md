---
title: Cenários populares para usar o Azure DevTest Labs
description: Este artigo fornece os principais cenários para usar o Azure DevTest Labs e dois caminhos gerais para começar a usar o serviço em sua organização.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 8736ba4c24ac4c8f8d84345028d1cadfdef38697
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60773801"
---
# <a name="popular-scenarios-for-using-azure-devtest-labs"></a>Cenários populares para usar o Azure DevTest Labs
Dependendo das necessidades de uma empresa, o DevTest Labs pode ser configurado para atender aos requisitos diferentes.  Este artigo aborda os cenários populares. Cada cenário aborda os benefícios trazidos por meio de laboratórios de desenvolvimento/teste e recursos para usar para implementar esses cenários.  

- Áreas de trabalho de desenvolvedor
- Ambientes de teste
- Sessões de treinamento, laboratórios práticos e hackathons
- Investigações em área restrita
- Laboratórios de sala de aula

## <a name="developer-desktops"></a>Áreas de trabalho de desenvolvedor
Os desenvolvedores geralmente têm requisitos diferentes para os computadores de desenvolvimento de projetos diferentes. Com o DevTest Labs, os desenvolvedores podem ter acesso às máquinas virtuais sob demanda que estão configurados para atender a seus cenários mais comuns. O DevTest Labs oferece os seguintes benefícios:

- As organizações podem fornecer garantindo a consistência entre as equipes de computadores de desenvolvimento comuns.
- Os desenvolvedores podem provisionar rapidamente seus computadores de desenvolvimento sob demanda ou [declaração de uma máquina pré-configurada existente](devtest-lab-add-claimable-vm.md).
- Os desenvolvedores podem provisionar recursos de uma maneira de autoatendimento sem precisar de permissões de nível de assinatura.
- IT ou os administradores podem [Pre-define a topologia de rede](devtest-lab-configure-vnet.md) e os desenvolvedores podem diretamente usá-lo de uma maneira simple e intuitiva sem a necessidade de qualquer acesso especial.
- Os desenvolvedores podem facilmente [personalizar](devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) seu desenvolvimento máquinas conforme necessário.
- Os administradores podem controlar os custos garantindo que:
    - Os desenvolvedores [não é possível obter mais VMs](devtest-lab-set-lab-policy.md#set-virtual-machines-per-user) do que o necessário para o desenvolvimento
    - [VMs são desligadas](devtest-lab-set-lab-policy.md#set-auto-shutdown) quando não estiver em uso
    - Somente [permitindo que um subconjunto de tamanhos de instância VM](devtest-lab-set-lab-policy.md#set-allowed-virtual-machine-sizes) para os laboratórios específicos
    - [Gerenciando as metas de custo e notificações](devtest-lab-configure-cost-management.md) para cada laboratório.

Para ler mais, veja [uso do Azure DevTest Labs para desenvolvedores](devtest-lab-developer-lab.md). 

## <a name="test-environments"></a>Ambientes de teste
Criar e gerenciar ambientes de teste em toda a empresa pode exigir um esforço significativo. Com o DevTest Labs, ambientes de teste podem ser facilmente criados, atualizados ou duplicados. Ele permite que as equipes acessar um ambiente totalmente configurado, quando for necessário. Neste cenário, o DevTest Labs oferece os seguintes benefícios:

- As organizações podem fornecer ambientes de teste comuns, garantindo a consistência entre as equipes.
- Os testadores podem testar a versão mais recente de seu aplicativo ao provisionar rapidamente ambientes Windows e Linux usando modelos reutilizáveis.
- Os administradores possam conectar o laboratório de DevOps do Azure para habilitar cenários de DevOps
- Os proprietários de laboratório podem controlar os custos, garantindo que:
    - [Máquinas virtuais em ambientes são desligados](devtest-lab-set-lab-policy.md#set-auto-shutdown) quando não estiver em uso
    - Somente [permitindo que um subconjunto de tamanhos de instância VM para](devtest-lab-set-lab-policy.md#set-allowed-virtual-machine-sizes) os laboratórios específicos
    - [Gerenciando as metas de custo e notificações](devtest-lab-configure-cost-management.md) para cada laboratório.

Para obter mais informações, consulte [ambientes de teste de uso do Azure DevTest Labs para VM e PaaS](devtest-lab-test-env.md).

## <a name="sandboxed-investigations"></a>Investigações em área restrita
Os desenvolvedores geralmente investigar tecnologias diferentes ou design de infraestrutura. Por padrão, todos os ambientes criados com o DevTest Labs são criados em seu próprio grupo de recursos. O usuário do DevTest Labs obtém apenas acesso de leitura a esses recursos. No entanto, para os desenvolvedores que precisam de mais controle, uma configuração de laboratório pode ser atualizada para fornecer [direitos de Colaborador](https://azure.microsoft.com/updates/azure-devtest-labs-view-and-set-access-rights-to-an-environment-rg/) para o usuário de DevTest Labs de origem para cada ambiente, eles criam.  Com o DevTest Labs, os desenvolvedores podem receber permissão de Colaborador automaticamente para ambientes que eles criam no laboratório.  Esse cenário permite aos desenvolvedores adicionar e/ou alterar os recursos do Azure que precisam para seus ambientes de desenvolvimento ou teste. O [custo por recurso](devtest-lab-configure-cost-management.md#view-cost-by-resource) página permite que os proprietários de laboratório controlar o custo de cada ambiente usado para investigações.

Para obter mais informações, consulte [conjunto de direitos de acesso a um grupo de recursos do ambiente](https://aka.ms/dtl-sandbox).

## <a name="trainings-hands-on-labs-and-hackathons"></a>Treinamento, laboratórios práticos e hackathons 
Um laboratório no Azure DevTest Labs atua como um contêiner excelente para atividades transitórios como workshops, laboratórios práticos, treinamentos ou hackathons.  O serviço permite que você crie um laboratório onde pode fornecer modelos personalizados que cada estagiário pode usar para criar ambientes idênticos e isolados para treinamento. Neste cenário, o DevTest Labs oferece os seguintes benefícios:

- [Políticas de](devtest-lab-set-lab-policy.md) garantir que os estagiários apenas obtém o número de recursos, como máquinas virtuais, o que eles precisam.
- São pré-configurados e criadas máquinas [solicitadas](devtest-lab-add-claimable-vm.md) com uma ação única de estagiário.
- Laboratórios são compartilhados com os estagiários acessando [URL para o laboratório](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab).
- [Datas de validade](devtest-lab-add-vm.md#steps-to-add-a-vm-to-a-lab-in-azure-devtest-labs) em máquinas virtuais, verifique se que as máquinas são excluídas depois que eles não são mais necessários.
- É fácil [excluir um laboratório](devtest-lab-delete-lab-vm.md#delete-a-lab) e todos os [recursos relacionados](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) quando o está sobre o treinamento.

Para obter mais informações, consulte [uso do Azure DevTest Labs para treinamento](devtest-lab-training-lab.md).  

## <a name="proof-of-concept-vs-scaled-deployment"></a>Prova de conceito vs. implantação em escala
Depois que você decidir explore os DevTest Labs, há dois caminhos gerais para frente: Prova de conceito vs implantação de escala.  

Uma **implantação dimensionada** consiste em semanas/meses de revisão e planejamento com a intenção de implantar o DevTest Labs em toda a empresa que possui centenas ou milhares de desenvolvedores.

Um **prova de conceito** implantação concentra-se em um esforço concentrado de uma única equipe para estabelecer o valor organizacional. Embora possa ser tentador pensar em uma implantação dimensionada, a abordagem tende a falhar com mais frequência do que a opção de prova de conceito. Portanto, recomendamos que você comece pelo mais simples, aprenda com a primeira equipe, repita a mesma abordagem com outras duas ou três equipes e planeje uma implantação dimensionada com base no conhecimento adquirido. Para que sua prova de conceito seja bem-sucedida, recomendamos escolher uma ou duas equipes e identificar seus cenários (ambiente de desenvolvimento versus ambientes de teste), documentar seus casos de uso atuais e implantar o DevTest Labs.

## <a name="next-steps"></a>Próximas etapas
Leia os seguintes artigos:

- [Conceitos dos Laboratórios de Desenvolvimento/Teste](devtest-lab-concepts.md)
- [Perguntas frequentes do DevTest Labs](devtest-lab-faq.md)

