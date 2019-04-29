---
title: Usar o Azure DevTest Labs para treinamento | Microsoft Docs
description: Saiba como usar o Azure DevTest Labs para cenários de treinamento.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.assetid: 57ff4e30-7e33-453f-9867-e19b3fdb9fe2
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 0a7ce1640636c6fba246584d098043a91990b9a0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60622824"
---
# <a name="use-azure-devtest-labs-for-training"></a>Usar o Azure DevTest Labs para treinamento
O Azure DevTest Labs pode ser usado para implementar muitos cenários essenciais além de desenvolvimento e teste. Um desses cenários é configurar um laboratório para treinamento. O Azure DevTest Labs permite que você crie um laboratório onde pode fornecer modelos personalizados que cada estagiário pode usar para criar ambientes idênticos e isolados para treinamento. Você pode aplicar políticas para garantir que os ambientes de treinamento estejam disponíveis para cada estagiário apenas quando necessário e contém recursos suficientes, como máquinas virtuais, para o treinamento. Por fim, você pode compartilhar facilmente o laboratório com estagiários, que podem acessá-lo com apenas um clique.

![Usar o DevTest Labs para treinamento](./media/devtest-lab-training-lab/devtest-lab-training.png)

O Azure DevTest Labs atende aos seguintes requisitos necessários para realizar treinamento em qualquer ambiente virtual: 

* Os estagiários não conseguem ver VMs criadas por outros estagiários
* Todas as máquinas de treinamento devem ser idênticas
* Os estagiários podem provisionar seus ambientes de treinamento rapidamente
* Controle os custos fazendo com que os estagiários não possam obter mais VMs do que o necessário para o treinamento e desligando VMs quando não estiverem em uso
* Compartilhe facilmente o laboratório de treinamento com os estagiários
* Reutilize o laboratório de treinamento várias vezes

Neste artigo, você aprenderá sobre os diversos recursos do Azure DevTest Labs que podem ser usados para atender aos requisitos de treinamento descritos anteriormente e as etapas detalhadas a seguir para configurar um laboratório para treinamento.  

## <a name="implementing-training-with-azure-devtest-labs"></a>Implementação de treinamento com Azure DevTest Labs
1. **Criar o laboratório** 
   
    Os laboratórios são o ponto de partida no Azure DevTest Labs. Depois de criar um laboratório, você pode executar tarefas como adicionar usuários (estagiários) ao laboratório, definir políticas para controlar custos, definir imagens da VM que podem criar rapidamente e muito mais.   
   
    Saiba mais clicando nos links na tabela abaixo:
   
   | Tarefa | O que você aprenderá |
   | --- | --- |
   | [Criar Laboratórios de Desenvolvimento/Teste do Azure](devtest-lab-create-lab.md) |Saiba como criar um laboratório no Azure DevTest Labs no portal do Azure. |
2. **Criar VMs de treinamento em minutos usando imagens prontas do marketplace e imagens personalizadas** 
   
    Você pode escolher imagens prontas de uma ampla variedade de imagens no Azure Marketplace e torná-las disponíveis para os estagiários no laboratório. Se as imagens prontas não atenderem às suas necessidades, você poderá criar uma imagem personalizada criando um laboratório de VM com uma imagem pronta do Azure Marketplace, instalando todos os softwares necessários para o treinamento e salvando a VM como uma imagem personalizada no laboratório. 
   
    Saiba mais clicando nos links na tabela abaixo:
   
   | Tarefa | O que você aprenderá |
   | --- | --- |
   | [Configurar imagens do Azure Marketplace](devtest-lab-configure-marketplace-images.md) |Saiba como você pode colocar imagens do Azure Marketplace na lista de permissões, disponibilizando para seleção apenas as imagens você deseja para o treinamento. |
   | [Criar uma imagem personalizada](devtest-lab-create-template.md) |Crie uma imagem personalizada instalando previamente o software de que você precisa para o treinamento, para que os estagiários possam criar rapidamente uma máquina virtual usando a imagem personalizada. |
3. **Criar modelos reutilizáveis para máquinas de treinamento** 
   
    Uma fórmula no Azure DevTest Labs é uma lista de valores de propriedade padrão usados para criar uma VM. Você pode criar uma fórmula no laboratório selecionando uma imagem, um tamanho de VM (uma combinação de CPU e RAM) e uma rede virtual. Cada estagiário pode ver a fórmula no laboratório e usá-la para criar uma máquina virtual. 
   
    Saiba mais clicando nos links na tabela abaixo:
   
   | Tarefa | O que você aprenderá |
   | --- | --- |
   | [Gerenciar fórmulas de Laboratórios de Desenvolvimento/Teste para criar VMs](devtest-lab-manage-formulas.md) |Saiba como você pode criar uma fórmula selecionando uma imagem, um tamanho de VM (uma combinação de CPU e RAM) e uma rede virtual. |
4. **Controlar os custos**
   
    O Azure DevTest Labs permite que você defina uma política no laboratório para especificar o número máximo de máquinas virtuais que podem ser criadas por um estagiário no laboratório. 
   
    Se você estiver realizando um treinamento de vários dias e quiser interromper todas as VMs em determinado momento do dia para reiniciá-las no dia seguinte, poderá fazer isso facilmente definindo políticas de desligamento automático e início automático no laboratório. 
   
    Finalmente, quando o treinamento estiver concluído, você poderá excluir todas as VMs ao mesmo tempo executando um único script do PowerShell. 
   
    Saiba mais clicando nos links na tabela abaixo:
   
   | Tarefa | O que você aprenderá |
   | --- | --- |
   | [Definir políticas de laboratório](devtest-lab-set-lab-policy.md) |Controle os custos definindo políticas no laboratório. |
   | [Excluir todas as VMs do laboratório usando um script do PowerShell](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) |Exclua todos os laboratórios em uma única operação quando o treinamento for concluído. |
5. **Compartilhar o laboratório com todos os estagiários**
   
    Os laboratórios podem ser acessados diretamente usando um link que você compartilha com seus estagiários. Os estagiários não precisam ter uma conta do Azure, desde que eles tenham um [conta da Microsoft](devtest-lab-faq.md#what-is-a-microsoft-account). Os estagiários não conseguem ver VMs criadas por outros estagiários.  
   
    Saiba mais clicando nos links na tabela abaixo:
   
   | Tarefa | O que você aprenderá |
   | --- | --- |
   | [Adicionar um estagiário a um laboratório no Azure DevTest Labs](devtest-lab-add-devtest-user.md) |Use o portal do Azure para adicionar estagiários ao seu laboratório de treinamento. |
   | [Adicionar estagiários ao laboratório usando um script do PowerShell](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell) |Use o PowerShell para automatizar a adição de estagiários ao seu laboratório de treinamento. |
   | [Obter um link para o laboratório](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab) |Saiba como um laboratório pode ser acessado diretamente por meio de um hiperlink. |
6. **Reutilizar o laboratório várias vezes** 
   
    Você pode automatizar a criação de laboratórios, incluindo configurações personalizadas, com a criação de um modelo do Resource Manager e usá-lo para criar laboratórios idênticos várias vezes. 
   
    Saiba mais clicando nos links na tabela abaixo:
   
   | Tarefa | O que você aprenderá |
   | --- | --- |
   | [Criar um laboratório usando um modelo do Resource Manager](devtest-lab-faq.md#how-do-i-create-a-lab-from-a-resource-manager-template) |Crie laboratórios no Azure DevTest Labs usando modelos do Resource Manager. |

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

