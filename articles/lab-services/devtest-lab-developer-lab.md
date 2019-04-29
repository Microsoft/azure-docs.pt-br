---
title: Usar o Azure DevTest Labs para desenvolvedores | Microsoft Docs
description: Saiba como usar o Azure DevTest Labs para cenários de desenvolvedor.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 22e070e5-3d1a-49fe-9d4c-5e07cb0b7fe2
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 5a293946e4672e7737f912f42511ad0907ba4a81
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61293923"
---
# <a name="use-azure-devtest-labs-for-developers"></a>Usar o Azure DevTest Labs para desenvolvedores
O Azure DevTest Labs pode ser usado para implementar vários cenários importantes, mas um dos principais cenários envolve o uso DevTest Labs para hospedar computadores de desenvolvimento para desenvolvedores. Neste cenário, o DevTest Labs oferece estes benefícios:

- Os desenvolvedores podem provisionar rapidamente seus computadores de desenvolvimento sob demanda.
- Os desenvolvedores podem personalizar facilmente seus computadores de desenvolvimento sempre que for necessário.
- Os administradores podem controlar os custos garantindo que:
  - Os desenvolvedores não podem utilizar mais VMs do que o necessário para o desenvolvimento.
  - As VMs ficam fechadas quando não estão em uso. 

![Usar o DevTest Labs para treinamento](./media/devtest-lab-developer-lab/devtest-lab-developer-lab.png)

Neste artigo, você aprenderá sobre os diversos recursos do Azure DevTest Labs que podem ser usados para atender aos requisitos de desenvolvedor e as etapas detalhadas a seguir para configurar um laboratório.

## <a name="implementing-developer-environments-with-azure-devtest-labs"></a>Implementando ambientes de desenvolvedor com o Azure DevTest Labs
1. **Criar o laboratório** 
   
    Os laboratórios são o ponto de partida no Azure DevTest Labs. Depois de criar um laboratório, você pode executar tarefas como adicionar usuários (desenvolvedores) ao laboratório, definir políticas para controlar custos, definir imagens da VM que podem criar rapidamente e muito mais.  
   
    Saiba mais clicando nos links na tabela abaixo:
   
   | Tarefa | O que você aprenderá |
   | --- | --- |
   | [Criar Laboratórios de Desenvolvimento/Teste do Azure](devtest-lab-create-lab.md) |Saiba como criar um laboratório no Azure DevTest Labs no portal do Azure. |
2. **Criar VMs em minutos usando imagens prontas do marketplace e imagens personalizadas** 
   
    Você pode escolher imagens prontas de uma ampla variedade de imagens no Azure Marketplace e torná-las disponíveis no laboratório. Se as imagens prontas não atenderem às suas necessidades, você poderá criar uma imagem personalizada criando um laboratório de VM com uma imagem pronta do Azure Marketplace, instalando todos os softwares necessários e salvando a VM como uma imagem personalizada no laboratório.

    Se você estiver usando imagens personalizadas, use uma fábrica de imagem para criar e distribuir suas imagens. Uma fábrica de imagem é uma solução de configuração como código que normalmente cria e distribui suas imagens configuradas automaticamente. Isso economiza o tempo necessário para configurar manualmente o sistema depois que uma máquina virtual foi criada com o sistema operacional base.
  
    Saiba mais clicando nos links na tabela abaixo:
   
   | Tarefa | O que você aprenderá |
   | --- | --- |
   | [Configurar imagens do Azure Marketplace](devtest-lab-configure-marketplace-images.md) |Saiba como você pode colocar imagens do Azure Marketplace na lista de permissões, disponibilizando para seleção apenas as imagens você deseja para os desenvolvedores.|
   | [Criar uma imagem personalizada](devtest-lab-create-template.md) |Crie uma imagem personalizada instalando previamente o software de que você precisa para que os desenvolvedores possam criar rapidamente uma máquina virtual usando a imagem personalizada.|
   | [Saiba mais sobre a fábrica de imagem](https://blogs.msdn.microsoft.com/devtestlab/2017/04/17/video-custom-image-factory-with-azure-devtest-labs/) |Assista a um vídeo que descreve como configurar e usar uma fábrica de imagem.|

3. **Criar modelos reutilizáveis para máquinas de desenvolvedor** 
   
    Uma fórmula no Azure DevTest Labs é uma lista de valores de propriedade padrão usados para criar uma VM. Você pode criar uma fórmula no laboratório selecionando uma imagem, um tamanho de VM (uma combinação de CPU e RAM) e uma rede virtual. Cada desenvolvedor pode ver a fórmula no laboratório e usá-la para criar uma máquina virtual. 
   
    Saiba mais clicando nos links na tabela abaixo:
   
   | Tarefa | O que você aprenderá |
   | --- | --- |
   | [Gerenciar fórmulas de Laboratórios de Desenvolvimento/Teste para criar VMs](devtest-lab-manage-formulas.md) |Saiba como você pode criar uma fórmula selecionando uma imagem, um tamanho de VM (uma combinação de CPU e RAM) e uma rede virtual.|

4. **Criar artefatos para permitir a personalização de VM flexível**

   Artefatos são usados para implantar e configurar seu aplicativo após o provisionamento de uma VM. Os artefatos podem ser:

   - Ferramentas que você deseja instalar na VM, por exemplo, agentes, Fiddler e Visual Studio.
   - Ações que você deseja executar na VM - como clonar um repositório.
   - Aplicativos que você deseja testar.

   Muitos artefatos já estão disponíveis prontos. Você pode criar seus próprios artefatos personalizados se quiser mais personalização para suas necessidades específicas.

   Saiba mais clicando nos links na tabela abaixo:
   
   | Tarefa | O que você aprenderá |
   | --- | --- |
   | [Criar artefatos personalizados para suas VM dos Laboratórios de Desenvolvimento/Teste](devtest-lab-artifact-author.md) |Crie seus próprio artefatos personalizados para as máquinas virtuais em seu laboratório.|
   | [Adicionar um repositório Git para armazenar os artefatos personalizados e modelos do Azure Resource Manager para uso no Azure DevTest Labs](devtest-lab-add-artifact-repo.md) |Saiba como armazenar os artefatos personalizados em seu próprio repositório de Git privado.|

5. **Controlar os custos**
   
    O Azure DevTest Labs permite que você defina uma política no laboratório para especificar o número máximo de máquinas virtuais que podem ser criadas por um desenvolvedor no laboratório. 
   
    Se sua equipe de desenvolvedores tiver definido uma agenda de trabalho e se você quiser interromper todas as VMs em determinado momento do dia para reiniciá-las no dia seguinte, poderá fazer isso facilmente definindo políticas de desligamento automático e início automático no laboratório. 
   
    Finalmente, quando o desenvolvimento de aplicativo estiver concluído, você poderá excluir todas as VMs ao mesmo tempo executando um único script do PowerShell. 
   
    Saiba mais clicando nos links na tabela abaixo:
   
   | Tarefa | O que você aprenderá |
   | --- | --- |
   | [Definir políticas de laboratório](devtest-lab-set-lab-policy.md) |Controle os custos definindo políticas no laboratório. |
   | [Excluir todas as VMs do laboratório usando um script do PowerShell](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) |Exclua todos os laboratórios em uma única operação quando o desenvolvimento for concluído.|

1. **Adicionar uma rede virtual a uma máquina virtual** 
   
    O DevTest Labs cria uma nova rede virtual (VNET) sempre que um laboratório for criado. Se você tiver configurado sua própria rede virtual, por exemplo, usando o ExpressRoute ou VPN site a site, pode adicionar essa rede virtual para as configurações de rede virtual do laboratório para que ele esteja disponível ao criar VMs.

    Além disso, há um artefato de associação de domínio do Azure Active Directory disponível que unirá uma máquina virtual a um domínio quando a VM estiver sendo criada. 
   
    Saiba mais clicando nos links na tabela abaixo:
   
   | Tarefa | O que você aprenderá |
   | --- | --- |
   | [Configurar uma rede virtual no Azure DevTest Labs](devtest-lab-configure-vnet.md) |Saiba como configurar uma rede virtual no Azure DevTest Labs usando o Portal do Azure.|

6. **Compartilhar o laboratório com todos os desenvolvedores**
   
    Os laboratórios podem ser acessados diretamente usando um link que você compartilha com seus desenvolvedores. Eles não precisam ter uma conta do Azure, desde que eles tenham um [conta da Microsoft](devtest-lab-faq.md#what-is-a-microsoft-account). Os desenvolvedores não conseguem ver VMs criadas por outros desenvolvedores.  
   
    Saiba mais clicando nos links na tabela abaixo:
   
   | Tarefa | O que você aprenderá |
   | --- | --- |
   | [Adicionar um desenvolvedor a um laboratório no Azure DevTest Labs](devtest-lab-add-devtest-user.md) |Use o Portal do Azure para adicionar os desenvolvedores ao laboratório.|
   | [Adicionar desenvolvedores ao laboratório usando um script do PowerShell](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell) |Use o PowerShell para automatizar a adição de desenvolvedores ao seu laboratório. |
   | [Obter um link para o laboratório](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab) |Saiba como os desenvolvedores podem acessar diretamente um laboratório por meio de um hiperlink.|

7. **Automatizar a criação de laboratório para mais equipes** 
   
    Você pode automatizar a criação de laboratórios, incluindo configurações personalizadas, com a criação de um modelo do Resource Manager e usá-lo para criar laboratórios idênticos várias vezes. 
   
    Saiba mais clicando nos links na tabela abaixo:
   
   | Tarefa | O que você aprenderá |
   | --- | --- |
   | [Criar um laboratório usando um modelo do Resource Manager](devtest-lab-faq.md#how-do-i-create-a-lab-from-a-resource-manager-template) |Crie laboratórios no Azure DevTest Labs usando modelos do Resource Manager. |

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

