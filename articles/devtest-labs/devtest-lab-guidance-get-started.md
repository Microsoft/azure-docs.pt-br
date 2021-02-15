---
title: Cenários populares para usar o Azure DevTest Labs
description: Este artigo fornece os principais cenários para usar Azure DevTest Labs e dois caminhos gerais para começar a usar o serviço em sua organização.
ms.topic: article
ms.date: 06/20/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 1adb9b009a250942bab7e9b0e3b4b3e52840341c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85481588"
---
# <a name="popular-scenarios-for-using-azure-devtest-labs"></a>Cenários populares para usar o Azure DevTest Labs
Dependendo das necessidades de uma empresa, os DevTest Labs podem ser configurados para atender a diferentes requisitos.  Este artigo aborda os cenários populares. Cada cenário aborda os benefícios trazidos usando o DevTest Labs e os recursos a serem usados para implementar esses cenários.  

- Áreas de trabalho de desenvolvedor
- Ambientes de teste
- Sessões de treinamento, laboratórios práticos e hackathons
- Investigações em área restrita
- Laboratórios de sala de aula

## <a name="developer-desktops"></a>Áreas de trabalho de desenvolvedor
Os desenvolvedores geralmente têm requisitos diferentes para os computadores de desenvolvimento de projetos diferentes. Com o DevTest Labs, os desenvolvedores podem ter acesso a máquinas virtuais sob demanda que são configuradas para se adequarem a seus cenários mais comuns. O DevTest Labs oferece os seguintes benefícios:

- As organizações podem fornecer máquinas de desenvolvimento comuns garantindo a consistência entre as equipes.
- Os desenvolvedores podem provisionar rapidamente suas máquinas de desenvolvimento sob demanda ou [reivindicar uma máquina pré-configurada existente](devtest-lab-add-claimable-vm.md).
- Os desenvolvedores podem provisionar recursos de forma automática sem precisar de permissões em nível de assinatura.
- A ti ou os administradores podem [predefinir a topologia de rede](devtest-lab-configure-vnet.md) e os desenvolvedores podem usá-la diretamente de maneira simples e intuitiva, sem a necessidade de qualquer acesso especial.
- Os desenvolvedores podem [Personalizar](devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) facilmente suas máquinas de desenvolvimento, conforme necessário.
- Os administradores podem controlar os custos garantindo que:
    - Os desenvolvedores [não podem obter mais VMs](devtest-lab-set-lab-policy.md#set-virtual-machines-per-user) do que precisam para o desenvolvimento
    - As [VMs são desligadas](devtest-lab-set-lab-policy.md#set-auto-shutdown) quando não estão em uso
    - [Permitindo apenas um subconjunto de tamanhos de instância de VM](devtest-lab-set-lab-policy.md#set-allowed-virtual-machine-sizes) para os laboratórios específicos
    - [Gerenciamento de metas de custo e notificações](devtest-lab-configure-cost-management.md) para cada laboratório.

Para leitura adicional, consulte [usar Azure DevTest Labs para desenvolvedores](devtest-lab-developer-lab.md). 

## <a name="test-environments"></a>Ambientes de teste
Criar e gerenciar ambientes de teste em toda a empresa pode exigir um esforço significativo. Com o DevTest Labs, os ambientes de teste podem ser facilmente criados, atualizados ou duplicados. Ele permite que as equipes acessem um ambiente totalmente configurado quando necessário. Neste cenário, o DevTest Labs oferece os seguintes benefícios:

- As organizações podem fornecer ambientes de teste comuns, garantindo a consistência entre as equipes.
- Os testadores podem testar a versão mais recente do aplicativo ao provisionar rapidamente ambientes Windows e Linux usando modelos reutilizáveis.
- Os administradores podem conectar o laboratório ao DevOps do Azure para habilitar cenários de DevOps
- Os proprietários de laboratório podem controlar os custos garantindo que:
    - As [VMs em ambientes são desligadas](devtest-lab-set-lab-policy.md#set-auto-shutdown) quando não estão em uso
    - [Permitindo apenas um subconjunto de tamanhos de instância de VM para](devtest-lab-set-lab-policy.md#set-allowed-virtual-machine-sizes) os laboratórios específicos
    - [Gerenciamento de metas de custo e notificações](devtest-lab-configure-cost-management.md) para cada laboratório.

Para obter mais informações, consulte [usar Azure DevTest Labs para ambientes de teste de VM e PaaS](devtest-lab-test-env.md).

## <a name="sandboxed-investigations"></a>Investigações em área restrita
Os desenvolvedores geralmente investigam diferentes tecnologias ou design de infraestrutura. Por padrão, todos os ambientes criados com o DevTest Labs são criados em seu próprio grupo de recursos. O usuário do DevTest Labs obtém apenas acesso de leitura a esses recursos. No entanto, para os desenvolvedores que precisam de mais controle, uma configuração em todo o laboratório pode ser atualizada para fornecer [direitos de colaborador](https://azure.microsoft.com/updates/azure-devtest-labs-view-and-set-access-rights-to-an-environment-rg/) ao usuário original do DevTest Labs para cada ambiente criado.  Com o DevTest Labs, os desenvolvedores podem receber a permissão de colaborador automaticamente para ambientes que criam no laboratório.  Esse cenário permite que os desenvolvedores adicionem e/ou alterem recursos do Azure conforme eles precisam para seus ambientes de desenvolvimento ou teste. A página [custo por recurso](devtest-lab-configure-cost-management.md#view-cost-by-resource) permite que os proprietários do laboratório acompanhem o custo de cada ambiente usado para investigações.

Para obter mais informações, consulte [definir direitos de acesso a um grupo de recursos de ambiente](https://aka.ms/dtl-sandbox).

## <a name="trainings-hands-on-labs-and-hackathons"></a>Treinamentos, laboratórios práticos e hackathons 
Um laboratório no Azure DevTest Labs atua como um excelente contêiner para atividades transitórias, como workshops, laboratórios práticos, treinamentos ou hackathons.  O serviço permite que você crie um laboratório onde pode fornecer modelos personalizados que cada estagiário pode usar para criar ambientes idênticos e isolados para treinamento. Neste cenário, o DevTest Labs oferece os seguintes benefícios:

- [As políticas](devtest-lab-set-lab-policy.md) garantem que os próprios usuários obtenham apenas o número de recursos, como máquinas virtuais, de que precisam.
- As máquinas pré-configuradas e criadas são [reivindicadas](devtest-lab-add-claimable-vm.md) com uma ação única do estagiário.
- Os laboratórios são compartilhados com os estagiários acessando [a URL do laboratório](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab).
- [As datas de expiração](devtest-lab-add-vm.md#steps-to-add-a-vm-to-a-lab-in-azure-devtest-labs) nas máquinas virtuais garantem que as máquinas sejam excluídas depois que não forem mais necessárias.
- É fácil [excluir um laboratório](devtest-lab-delete-lab-vm.md#delete-a-lab) e todos os [recursos relacionados](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) quando o treinamento terminar.

Para obter mais informações, consulte [usar Azure DevTest Labs para treinamento](devtest-lab-training-lab.md).  

## <a name="proof-of-concept-vs-scaled-deployment"></a>Prova de conceito versus implantação em escala
Depois de decidir explorar o DevTest Labs, há dois caminhos gerais para frente: prova de conceito versus implantação em escala.  

Uma **implantação dimensionada** consiste em semanas/meses de revisão e planejamento com a intenção de implantar o DevTest Labs em toda a empresa que possui centenas ou milhares de desenvolvedores.

Uma implantação **de prova de conceito** concentra-se em um esforço concentrado de uma única equipe para estabelecer o valor organizacional. Embora possa ser tentador pensar em uma implantação dimensionada, a abordagem tende a falhar com mais frequência do que a opção de prova de conceito. Portanto, recomendamos que você comece pelo mais simples, aprenda com a primeira equipe, repita a mesma abordagem com outras duas ou três equipes e planeje uma implantação dimensionada com base no conhecimento adquirido. Para que sua prova de conceito seja bem-sucedida, recomendamos escolher uma ou duas equipes e identificar seus cenários (ambiente de desenvolvimento versus ambientes de teste), documentar seus casos de uso atuais e implantar o DevTest Labs.

## <a name="next-steps"></a>Próximas etapas
Leia os seguintes artigos:

- [Conceitos dos Laboratórios de Desenvolvimento/Teste](devtest-lab-concepts.md)
- [Perguntas frequentes do DevTest Labs](devtest-lab-faq.md)

