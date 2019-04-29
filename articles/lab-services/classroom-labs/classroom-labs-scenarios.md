---
title: Usar laboratórios de sala de aula para treinamentos - Azure Lab Services | Microsoft Docs
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
ms.date: 02/22/2019
ms.author: spelluru
ms.openlocfilehash: 4d2ba11181977f1976b5ae933e8b93a92424fa96
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60695263"
---
# <a name="use-classroom-labs-for-trainings"></a>Usar laboratórios de sala de aula para treinamentos
Você pode configurar um laboratório para treinamento. Laboratórios de sala de aula do Azure Lab Services permitem que você criar um laboratório para treinamento em que cada estagiário usa ambientes idênticos e isolados para treinamento. Você pode aplicar políticas para garantir que os ambientes de treinamento estejam disponíveis para cada estagiário apenas quando necessário e contém recursos suficientes, como máquinas virtuais, para o treinamento. 

![Laboratório de sala de aula](../media/classroom-labs-scenarios/classroom.png)

Laboratórios de sala de aula atende aos seguintes requisitos que são necessários para realizar treinamento em qualquer ambiente virtual: 

- Os estagiários podem provisionar seus ambientes de treinamento rapidamente
- Todas as máquinas de treinamento devem ser idênticas
- Os estagiários não conseguem ver VMs criadas por outros estagiários
- Controle os custos fazendo com que os estagiários não possam obter mais VMs do que o necessário para o treinamento e desligando VMs quando não estiverem em uso
- Compartilhe facilmente o laboratório de treinamento com os estagiários
- Reutilize o laboratório de treinamento várias vezes

Neste artigo, você aprenderá sobre os diversos recursos do Azure Lab Services que podem ser usados para atender aos requisitos de treinamento descritos anteriormente e as etapas detalhadas que você pode seguir para configurar um laboratório para treinamento.  

## <a name="create-the-lab-account-as-a-lab-account-administrator"></a>Criar a conta de laboratório como um administrador da conta de laboratório
A primeira etapa no uso do Azure Lab Services é criar uma conta de laboratório no portal do Azure. Depois que um administrador da conta de laboratório cria a conta de laboratório, o administrador adiciona os usuários que desejam criar laboratórios para o **criador de laboratório** função. Os treinadores criar laboratórios com máquinas virtuais para os alunos a fazer exercícios do curso ministrados por eles. Para obter detalhes, consulte [criar e gerenciar uma conta de laboratório](how-to-manage-lab-accounts.md).

## <a name="create-and-manage-classroom-labs"></a>Criar e gerenciar laboratórios de sala de aula
Um instrutor, que é um membro da função de criador de laboratório em uma conta de laboratório, pode criar um ou mais laboratórios na conta de laboratório. Criar e configurar um modelo de VM com todos os softwares necessários para realizar exercícios do seu curso. Escolher uma imagem pronta nas imagens disponíveis para a criação de um laboratório de sala de aula e, em seguida, personalizá-lo ao instalar o software necessário para o laboratório. Para obter detalhes, consulte [criar e gerenciar laboratórios de sala de aula](how-to-manage-classroom-labs.md).

## <a name="configure-usage-settings-and-policies"></a>Configurar políticas e configurações de uso
O criador de laboratório pode adicionar ou remover usuários de laboratório, obter o link de registro para enviar para os usuários do laboratório, definir políticas, como cotas individuais de configuração por usuário, atualize o número de VMs disponíveis no laboratório e muito mais. Para obter detalhes, consulte [configurar políticas e configurações de uso](how-to-configure-student-usage.md).

## <a name="create-and-manage-schedules"></a>Criar e gerenciar agendas
Agendamentos permitem que você configure um laboratório de sala de aula, de modo que as VMs no laboratório iniciam e desligam automaticamente em um horário especificado. Você pode definir um agendamento único ou recorrente. Para obter detalhes, consulte [criar e gerenciar agendas para laboratórios de sala de aula](how-to-create-schedules.md).

## <a name="set-up-and-publish-a-template-vm"></a>Configurar e publicar um modelo de VM
Um modelo em um laboratório é uma imagem básica da máquina virtual a partir do qual todas as máquinas virtuais de todos os usuários são criadas. Configure o modelo de VM para que ele seja configurado com exatamente o que você deseja fornecer para os participantes de treinamento. Você pode fornecer um nome e uma descrição do modelo exibido para os usuários do laboratório. Em seguida, publique o modelo para tornar as instâncias do modelo de VM disponíveis para os usuários do laboratório. Quando você publicar um modelo, o Azure Lab Services criará VMs no laboratório usando o modelo. O número de VMs criadas neste processo é o mesmo que o número máximo de usuários permitidos em um laboratório, que você pode definir na política de uso do laboratório. Todas as máquinas virtuais têm a mesma configuração que o modelo. Para obter detalhes, consulte [definir configurar e publicar as máquinas virtuais de modelo](how-to-create-manage-template.md). 

## <a name="use-vms-in-the-classroom-lab"></a>Usar VMs do laboratório de sala de aula
Um aluno ou participante treinamento registra no laboratório e conecta-se à VM para fazer exercícios do curso. Para obter detalhes, consulte [como acessar um laboratório de sala de aula](how-to-use-classroom-lab.md).

## <a name="next-steps"></a>Próximas etapas
Inicie com a criação de uma conta de laboratório nos laboratórios de sala de aula seguindo as instruções no artigo: [Tutorial: Configurar uma conta de laboratório com o Azure Lab Services](tutorial-setup-lab-account.md).