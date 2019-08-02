---
title: Usar recursos de declaração no Azure DevTest Labs | Microsoft Docs
description: Saiba mais sobre cenários diferentes para usar os recursos de declaração/desdeclaração de Azure DevTest Labs
services: devtest-lab
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2019
ms.author: spelluru
ms.openlocfilehash: a15148260bccadc59966c86031100f0e0332b0f9
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67861424"
---
# <a name="use-claim-capabilities-in-azure-devtest-labs"></a>Usar recursos de declaração no Azure DevTest Labs
O serviço de Azure DevTest Labs melhora a eficácia e a eficiência de desenvolvedores e testadores. Este artigo se concentra na capacidade de solicitar ou desreivindicar máquinas virtuais no Azure DevTest Labs. Ele também lista várias maneiras pelas quais esse recurso melhora a experiência do usuário. Antes de examinar cenários diferentes em que esse recurso pode ser usado, vejamos o que  é reivindicação e como ele funciona.

## <a name="claimable-machines"></a>Computadores declaráveis
Uma máquina claimável é uma VM (máquina virtual) que é criada em um laboratório sem proprietário. Depois que o computador for reivindicado, o usuário terá uma gama completa de opções para essa VM. Quando um usuário alega um computador, algumas alterações são feitas. A VM é movida da lista de **máquinas virtuais claimáveis** para a lista **minhas máquinas virtuais** no portal do Azure. 

O usuário pode se conectar à VM, personalizar artefatos, reiniciar, parar ou cancelar a declaração do computador. Há duas maneiras de tornar uma VM declarável:

- Crie um computador e dessolicite-o para que ele se mova para o pool de declaração. 
- Crie uma VM e coloque-a no pool compartilhado usando [as configurações avançadas](https://azure.microsoft.com/updates/azure-devtest-labs-claim-lab-vms-from-a-shared-pool/).

Há dois casos em que os recursos de reivindicação/cancelamento de declaração podem ser usados com eficiência. O primeiro caso requer mais previsão e planejamento, para ser projetado e executado corretamente. E a segunda é mais situação. Veja a seguir alguns exemplos dos diferentes casos.

## <a name="designed-use-of-claimable-machines"></a>Uso projetado de computadores declaráveis

- **Desenvolvimento/teste de software:** Permita que os desenvolvedores ou testadores sejam mais produtivos Configurando computadores prontos e em um estado não reivindicado. Ter um conjunto de VMs com configurações diferentes, ferramentas necessárias e com o código mais recente permite que os usuários declarem uma VM e comecem a trabalhar sem precisar gastar tempo para configurar um computador. Antes que as VMs sejam reivindicadas, as máquinas são provisionadas, mas estão desligadas minimizando o custo de ter computadores usados com menos frequência. Quando as VMs são necessárias, um usuário simplesmente declara a VM, que inicia a máquina. A opção de desdeclaração não é tão útil nesse caso, já que criar uma nova VM é geralmente mais fácil e barato.
- **Sala de aula/laboratórios:** Ter VMs pré-configuradas para uma classe ou um laboratório para que os alunos possam se conectar imediatamente a um computador usando o portal do Azure.  Quando um aluno alega uma VM, o laboratório garante que ninguém possa reivindicar a mesma máquina. Automatizar esse processo garante que o número necessário de computadores com o ambiente especificado esteja disponível. Se os alunos não aparecerem ou estiverem em execução tardiamente, os computadores não reivindicados poderão ser mantidos disponíveis até que a sessão fique acima com o mínimo de custo. A opção de desdeclaração não é tão eficaz nesse cenário, pois a VM está em um estado desconhecido quando o usuário anterior é concluído.
- **Demonstrações** Use computadores para demonstrações, em que os computadores do laboratório são configurados com ambientes específicos. Esse recurso é útil quando várias pessoas podem estar dando uma demonstração ao mesmo tempo ou em momentos aleatórios, como em uma conferência. A opção Cancelar declaração pode ser útil nessa situação, pois a demonstração não deve alterar o estado do computador, permitindo que os usuários retornem uma VM de volta para o pool alegado para a próxima demonstração. Com o computador não reclamado sendo desprovisionado e incorrer em custo mínimo, as VMs podem ser deixadas no laboratório por períodos de tempo mais longos.
- **Funcionários temporários/de contrato:** Permitir que os usuários usem um computador. Quando eles deixam, eles retornam a VM para o pool claimável sem perda de dados. Com a VM desreivindicada, outro usuário pode reivindicar a VM e continuar ou examinar a máquina para obter informações adicionais.
- **Em geral:** A capacidade de ter uma única fonte configurada automaticamente e implantar VMs, em uma cadência específica, é útil em muitas situações diferentes. Há várias situações diferentes em que o recurso de declaração/desdeclaração ajuda os usuários a serem mais eficientes, tendo um processo automatizado para criar as VMs em um estado não reivindicado com uma configuração definida. As configurações podem incluir diferentes sistemas operacionais, idiomas, discos ou [outros softwares (artefatos)](devtest-lab-artifact-author.md) , dependendo de suas necessidades. A capacidade de reivindicar uma VM do laboratório permite que o usuário do laboratório obtenha um sistema configurado corretamente sem gastar tempo ou esforço na configuração da máquina. O Lab Manager pode usar o estado solicitado das VMs para melhorar o número de máquinas geradas, limpar computadores e determinar a prioridade das configurações. A [fábrica de imagens](image-factory-create.md) é um bom exemplo de um processo automatizado para criar VMs e imagens para vários laboratórios. Os scripts podem ser modificados para executar qualquer uma das seguintes situações com as alterações apropriadas ou serem usadas como uma referência para a criação de um sistema personalizado.

## <a name="situational-use-of-claimable-machines"></a>Uso da situação de computadores declaráveis

- Use o recurso de declaração/cancelamento de declaração que permite aos usuários passar o controle de computadores de um para outro e não ter de saber explicitamente quem será pegar o computador em seguida.
- Desenvolvimento, teste e depuração de um cenário em que uma configuração de computador específica pode reproduzir um bug, o computador pode ser desreivindicado, permitindo que outro desenvolvedor possa reivindicar o computador e continuar o trabalho. Esse recurso é especialmente útil, pois mais pessoas estão trabalhando remotamente em diferentes áreas do mundo. 
- Os membros da equipe podem trabalhar com um único ambiente. Por exemplo, você pode configurar manualmente um ambiente complexo que não pode ser automatizado ou criar recursos que só podem lidar com modificações para uma única entrada como imagens. No passado, esse problema foi resolvido com um computador dedicado em funcionamento. O recurso claimável é uma melhoria em relação ao processo manual, tendo o controle de acesso de usuário interno e a identificação visual quando disponível. Quando não reclamado, a VM é desprovisionada para reduzir os custos.
- Ter um disco de dados anexado a uma VM. Cada disco de até ~ 1 TB de dados permite que um grande volume de dados seja passado sem a necessidade de copiar ou duplicar os dados. A VM seria inicialmente criada com um disco anexado que tinha o grande volume de dados.  Qualquer usuário poderia reivindicar o computador e acessar os dados. Quando terminar, cancele a declaração da VM para permitir que outros usuários no computador.

Há algumas limitações no uso de máquinas claimáveis, mais comumente em relação à obtenção de acesso ao computador. Se o computador estiver ingressado no domínio, o usuário que alega a máquina precisará já ter recebido acesso, geralmente é feito concedendo acesso a um grupo que abrange todos os usuários do laboratório quando a VM é criada. Se o computador não estiver ingressado no domínio, o artefato de **senha de VM** de redefinição no repositório público deverá ser executado para adicionar o usuário como administrador.  Os artefatos podem ser aplicados mesmo após o computador ter sido iniciado ou reivindicado.

## <a name="next-steps"></a>Próximas etapas
Consulte o seguinte artigo: [Criar e gerenciar VMs declaráveis no Azure DevTest Labs](devtest-lab-add-claimable-vm.md)
