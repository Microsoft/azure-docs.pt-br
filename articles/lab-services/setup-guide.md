---
title: Guia de configuração do laboratório acelerado para Azure Lab Services
description: Se você for um criador de laboratório, este guia poderá ajudá-lo a configurar rapidamente uma conta de laboratório em sua escola.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 07f0d92ebd926616f1318b430bec2de32f753f7c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "95021721"
---
# <a name="lab-setup-guide"></a>Guia de configuração do laboratório

Neste guia, você aprenderá a criar um laboratório para alunos da sua escola.

O processo de publicação de um laboratório para seus alunos pode levar várias horas. A quantidade de tempo de configuração depende do número de máquinas virtuais (VMs) que você deseja criar em seu laboratório. Aguarde pelo menos um dia para garantir que o laboratório esteja funcionando corretamente e para permitir tempo suficiente para publicar as VMs dos seus alunos.

## <a name="understand-the-lab-requirements-of-your-class"></a>Entenda os requisitos de laboratório de sua classe

Antes de configurar um novo laboratório, lembre-se de pensar no seguinte.

### <a name="what-software-requirements-does-the-class-have"></a>Quais são os requisitos de software para a classe?

Consulte os objetivos de aprendizado da sua classe ao decidir quais sistemas operacionais, aplicativos e ferramentas você precisa instalar nas VMs do laboratório. Para configurar VMs de laboratório, você tem três opções:

- **Usar uma de imagem do Azure Marketplace**: o Azure Marketplace fornece centenas de imagens que você pode usar ao criar um laboratório. Para algumas classes, uma dessas imagens já pode conter tudo o que você precisa.

- **Criar uma nova imagem personalizada**: você pode criar sua própria imagem personalizada usando uma imagem do Azure Marketplace como um ponto de partida. Em seguida, você pode personalizá-lo instalando software adicional e fazendo alterações de configuração.

- **Usar uma imagem personalizada existente**: você pode reutilizar imagens personalizadas criadas anteriormente ou imagens que foram criadas por outros administradores ou corpo docente na sua escola. Para usar imagens personalizadas, os administradores precisam configurar uma galeria de imagens compartilhada.  Uma galeria de imagens compartilhadas é um repositório que é usado para salvar imagens personalizadas.

> [!NOTE]
> Os administradores são responsáveis por habilitar as imagens personalizadas e as do Azure Marketplace para que você possa usá-las. Coordene com o departamento de ti para garantir que as imagens necessárias estejam habilitadas. As imagens personalizadas que você cria são habilitadas automaticamente para uso nos laboratórios que você possui.

### <a name="what-hardware-requirements-does-the-class-have"></a>Quais são os requisitos de hardware da classe?

Você pode escolher entre uma variedade de tamanhos de computação:

- **Tamanhos de virtualização aninhados**: permite que você dê aos alunos acesso a uma VM que pode hospedar várias VMs aninhadas. Por exemplo, você pode usar esse tamanho de computação para redes ou aulas de Ethical Hacking.

- **Tamanhos de GPU**: permite que os alunos usem tipos de aplicativos que usam muitos computadores. Por exemplo, essa opção é geralmente usada com inteligência artificial e aprendizado de máquina.

Para obter orientação sobre como selecionar o tamanho apropriado da VM, consulte:
- [Dimensionamento da VM](./administrator-guide.md#vm-sizing)
- [Mover de um laboratório físico para Azure Lab Services](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931)

> [!NOTE]
> Como a disponibilidade do tamanho da computação varia por região, menos tamanhos podem estar disponíveis para seu laboratório. Em geral, você deve selecionar o menor tamanho de computação que atenda às suas necessidades. Com o Azure Lab Services, você pode configurar um novo laboratório com uma capacidade de computação maior posteriormente, se necessário.

### <a name="what-dependencies-does-the-class-have-on-external-azure-or-network-resources"></a>Quais são as dependências da classe em recursos externos do Azure ou da rede?
Suas VMs de laboratório podem precisar de acesso a recursos externos, como um banco de dados, um compartilhamento de arquivos ou um servidor de licenciamento.  Para permitir que suas VMs de laboratório usem recursos externos, coordene com os administradores de ti.

> [!NOTE]
> Você deve considerar se pode reduzir a dependência do laboratório em recursos externos fornecendo recursos de rede diretamente na VM. Por exemplo, para eliminar a necessidade de ler dados de um banco de dados externo, você poderá instalar o banco de dados diretamente na VM.  

### <a name="how-will-you-control-costs"></a>Como você controlará os custos?
Os serviços de laboratório usam um modelo de preços pago conforme o uso, o que significa que você paga apenas pelo tempo em que uma VM de laboratório está em execução. Para controlar os custos, use uma ou todas as seguintes opções:

- **Agendamento**: Use agendas para controlar automaticamente quando suas VMs de laboratório são iniciadas e desligadas.
- **Cota**: Use cotas para controlar o número de horas que os alunos têm acesso a uma VM fora das horas agendadas.  Quando um aluno está usando uma VM e atinge uma cota, a VM é desligada automaticamente.  O aluno não pode reiniciar a VM, a menos que você aumente a cota.
- **Desligamento automático**: quando você habilita a configuração de desligamento automático, as VMs do Windows são desligadas automaticamente depois que um aluno é desconectado de uma sessão de protocolo RDP (RDP). Por padrão, essa configuração está desabilitada.

Para obter mais informações sobre como controlar os custos, consulte:
- [Estimar custos](./cost-management-guide.md#estimate-the-lab-costs)
- [Gerenciar os custos](./cost-management-guide.md#manage-costs)

### <a name="how-will-students-save-their-work"></a>Como os alunos salvarão seus trabalhos?
Cada aluno individual recebe uma VM durante o tempo de vida do laboratório. Os alunos podem salvar seu trabalho:

- Para a VM.
- Para um local externo, como OneDrive ou GitHub. É possível configurar o OneDrive automaticamente para alunos em suas VMs no laboratório.

> [!NOTE]
> Para garantir que os alunos tenham acesso contínuo ao trabalho salvo fora do laboratório e após o término da classe, recomendamos que eles salvem seu trabalho em um repositório externo.

### <a name="how-will-students-connect-to-their-vms"></a>Como os alunos irão se conectar às suas VMs?
Para conexões RDP com VMs do Windows, recomendamos que os alunos usem o [cliente área de trabalho remota da Microsoft](/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients). O cliente do Área de Trabalho Remota dá suporte a dispositivos Mac, Chromebook e Windows.

Para VMs do Linux, os alunos podem usar o protocolo Secure Shell (SSH) ou RDP. Para que os alunos se conectem usando o RDP, você deve instalar e configurar os pacotes de RDP e GUI (interface gráfica do usuário) necessários.

### <a name="will-students-also-use-microsoft-teams"></a>Os alunos também usarão as equipes da Microsoft?
O Azure Lab Services integra-se com as equipes da Microsoft para que os membros do corpo docente possam criar e gerenciar seus laboratórios em equipes.  Da mesma forma, os alunos podem acessar seus laboratórios em equipes.

Para obter mais informações, consulte [Azure Lab Services no Microsoft Teams](./lab-services-within-teams-overview.md).

## <a name="set-up-your-lab"></a>Configurar o laboratório

Depois de entender os requisitos para a criação do laboratório de sua classe, você estará pronto para configurá-lo. Para saber como, siga os links nesta seção. Também são fornecidas instruções para configurar laboratórios em equipes.

1. **Crie um laboratório**. Consulte os seguintes tutoriais:
    - [Criar um laboratório de sala de aula](./tutorial-setup-classroom-lab.md#create-a-classroom-lab)
    - [Criar um laboratório nas equipes](./how-to-get-started-create-lab-within-teams.md)

    > [!NOTE]
    > Se sua classe requer virtualização aninhada, consulte [habilitar virtualização aninhada](./how-to-enable-nested-virtualization-template-vm.md).

1. **Personalize imagens e publique VMs de laboratório**. Para se conectar a uma VM especial chamada VM de modelo, consulte:
    - [Criar e gerenciar uma VM modelo](./tutorial-setup-classroom-lab.md#publish-the-template-vm)
    - [Usar uma galeria de imagens compartilhada](./how-to-use-shared-image-gallery.md)

    > [!NOTE]
    > Se você estiver usando o Windows, consulte também [Configurar uma VM de modelo do Windows](./how-to-prepare-windows-template.md). Essas instruções incluem etapas para configurar o OneDrive e o Microsoft Office para seus alunos.

1. **Gerenciar a capacidade e o pool de VMs**. Você pode ampliar ou reduzir a capacidade da VM com facilidade, conforme necessário para sua classe. Tenha em mente que aumentar a capacidade da VM pode levar várias horas porque as novas VMs estão sendo configuradas. Veja os artigos a seguir:
    - [Configurar e gerenciar um pool de VMs](./how-to-set-virtual-machine-passwords.md)
    - [Gerenciar um pool de VMs em serviços de laboratório em equipes](./how-to-manage-vm-pool-within-teams.md)

1. **Adicionar e gerenciar usuários de laboratório**. Para adicionar usuários ao seu laboratório, consulte:
   - [Adicionar usuários ao laboratório](./tutorial-setup-classroom-lab.md#add-users-to-the-lab)
   - [Enviar convites aos usuários](./tutorial-setup-classroom-lab.md#send-invitation-emails-to-users)
   - [Gerenciar listas de usuários do Lab Services em equipes](./how-to-manage-user-lists-within-teams.md)

    Para obter informações sobre os tipos de contas que os alunos podem usar, consulte [contas de alunos](./how-to-configure-student-usage.md#student-accounts).
  
1. **Definir controles de custo**. Para definir uma agenda, estabelecer cotas e habilitar o desligamento automático, consulte os seguintes tutoriais:

   - [Definir um agendamento](./tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)

        > [!NOTE]
        > Dependendo do sistema operacional que você instalou, uma VM pode levar vários minutos para ser iniciada. Para garantir que uma VM de laboratório esteja pronta para ser usada durante as horas agendadas, recomendamos que você inicie 30 minutos de antecedência.

   - [Definir cotas para usuários](./how-to-configure-student-usage.md#set-quotas-for-users) e [definir cotas adicionais para usuários específicos](./how-to-configure-student-usage.md#set-additional-quotas-for-specific-users)
  
   - [Habilitar desligamento automático ao desconectar](./how-to-enable-shutdown-disconnect.md)

        > [!NOTE]
        > Agendas e cotas não se aplicam à VM de modelo, mas as configurações de desligamento automático se aplicam. 
        > 
        > Quando você cria um laboratório, a VM de modelo é criada, mas não iniciada. Você pode iniciar a VM de modelo, conectar-se a ela, instalar qualquer software de pré-requisito para o laboratório e, em seguida, publicá-la. Quando você publicar a VM de modelo, ela será desligada automaticamente para você se você ainda não tiver feito isso manualmente. 
        > 
        > As VMs de modelo incorrem em *custos* quando estão em execução, portanto, certifique-se de que a VM de modelo seja desligada quando você não precisar que ela esteja em execução.

    - [Criar e gerenciar agendas de serviços de laboratório em equipes](./how-to-create-schedules-within-teams.md) 

1. **Use o painel**. Para obter instruções, consulte [usar o painel de laboratório da sala de aula](./use-dashboard.md).

    > [!NOTE]
    > O custo estimado mostrado no painel é o custo máximo que você pode esperar incorrer para o uso do laboratório do aluno. Por exemplo, cotas não usadas por seus alunos *não* são cobradas. Os custos estimados *não* refletem nenhuma cobrança pelo uso da VM de modelo, da Galeria de imagens compartilhada ou quando o criador do laboratório inicia um computador do usuário.

## <a name="next-steps"></a>Próximas etapas

Como parte do gerenciamento de seus laboratórios, consulte os seguintes artigos:
- [Acompanhe o uso de laboratórios da sala de aula](tutorial-track-usage.md)  
- [Acessar um laboratório de sala de aula](tutorial-connect-virtual-machine-classroom-lab.md)
