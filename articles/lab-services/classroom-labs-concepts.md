---
title: Conceitos de laboratórios-Azure Lab Services | Microsoft Docs
description: Aprenda os conceitos básicos do Lab Services e como ele pode facilitar a criação e o gerenciamento de laboratórios.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: fa3a8dad195b4b3cbf0786c8923c8b330d148898
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96435511"
---
# <a name="labs-concepts"></a>Conceitos dos laboratórios

A lista a seguir contém as principais definições e conceitos do Lab Services:

## <a name="quota"></a>Quota

Cota é o limite de tempo (em horas) que um educador pode definir para um aluno usar uma VM de laboratório. Ele pode ser definida como 0 ou um número específico de horas. Se a cota for definida como 0, um aluno só poderá usar a máquina virtual quando uma agenda estiver em execução ou quando um educador ligar manualmente a máquina virtual do aluno.  

As horas de cota são contadas quando o próprio aluno inicia VM de laboratório.  Se um educador iniciar manualmente a VM de laboratório para um aluno, as horas de cota não serão usadas para esse aluno.

## <a name="schedules"></a>Agendas

Agendas são os slots de tempo que um educador pode criar para a classe para que as VMs dos alunos estejam disponíveis para o tempo de aula.  As agendas podem ser de uma única vez ou recorrentes.  As horas de cota não serão usadas quando uma agenda estiver em execução.

Há três tipos de agendas: Standard, Somente iniciar e Somente parar.

- **Standard**.  Essa agenda iniciará todas as VMs de aluno na hora de início especificada e desligará todas as VMs de aluno na hora de parada especificada.
- **Somente iniciar**.   Essa agenda iniciará todas as VMs de aluno na hora especificada.  As VMs de aluno não serão paradas até que um aluno pare a VM dele por meio do portal do Azure Lab Services ou que ocorra uma agenda Somente parar.
- **Somente parar**.  Essa agenda vai parar todas as VMs de aluno na hora especificada.  

## <a name="template-virtual-machine"></a>Máquina virtual de modelo

Uma máquina virtual de modelo em um laboratório é uma imagem básica da máquina virtual com base na qual as máquinas virtuais de todos os usuários são criadas. Os treinadores/criadores de laboratório instalam a máquina virtual de modelo e as configuram com o software que eles desejam fornecer para treinar os participantes com o intuito de fazer os laboratórios. Quando você publica uma VM de modelo, o Azure Lab Services cria ou atualiza as VMs de laboratório com base na VM de modelo.

## <a name="user-profiles"></a>Perfis do usuário

Este artigo descreve os diferentes perfis de usuário no Azure Lab Services.

### <a name="lab-account-owner"></a>Proprietário da conta do laboratório

Normalmente, um administrador de TI dos recursos de nuvem da organização, que tem a assinatura do Azure, atua como um proprietário de conta de laboratório e realiza as seguintes tarefas:

- Define uma conta de laboratório para sua organização.
- Gerencia e configura as políticas em todos os laboratórios.
- Concede permissões para as pessoas na organização para criarem um laboratório usando a conta do laboratório.

### <a name="educator"></a>Educador

Normalmente, os usuários como professor ou instrutor online criam laboratórios em uma conta de laboratório. Um educador executa as seguintes tarefas:

- Cria um laboratório de sala de aula.
- Cria máquinas virtuais no laboratório.
- Instala o software apropriado em máquinas virtuais.
- Especifica quem pode acessar o laboratório.
- Fornece um link de registro para o laboratório para alunos.

### <a name="student"></a>Aluno

Um estudante executa as seguintes tarefas:

- Usa o link de registro que o usuário do laboratório recebe de um criador do laboratório para registrar com o laboratório.
- Conecta-se a uma máquina virtual no laboratório e usa-a para fazer trabalhos, tarefas e projetos em sala de aula.

## <a name="next-steps"></a>Próximas etapas

Introdução à configuração de uma conta de laboratório necessária para criar um laboratório de sala de aula usando o Azure Lab Services:

- [Configurar uma conta de laboratório](tutorial-setup-lab-account.md)
