---
title: Usar Azure Lab Services para Hackathon
description: Este artigo descreve como usar Azure Lab Services para criar laboratórios que você pode usar para executar o hackathons.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: d4648def48557d70547ef7d41d6c3e516671849e
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2021
ms.locfileid: "102452723"
---
# <a name="use-azure-lab-services-for-your-next-hackathon"></a>Usar Azure Lab Services para seu próximo Hackathon
A Azure Lab Services foi projetada para ser leve e fácil de usar para que você possa criar rapidamente um novo laboratório de VMs (máquinas virtuais) para seu Hackathon.  Use a lista de verificação a seguir para garantir que o Hackathon seja o mais tranqüila possível. Esta lista de verificação deve ser concluída pelo seu departamento de ti ou corpo docente responsável por criar e gerenciar seu laboratório de Hackathon. 

Para usar os serviços de laboratório para seu Hackathon, certifique-se de que a conta de laboratório e seu laboratório sejam criados pelo menos alguns dias antes do início do seu Hackathon. Além disso, siga as orientações abaixo:

## <a name="guidance"></a>Orientação

- **Crie o laboratório em uma região ou local mais próximo dos participantes**. 

    Para reduzir a latência, crie seu laboratório em uma região mais próxima de seus participantes do Hackathon.  Se seus participantes estiverem localizados em todo o mundo, você precisará usar o melhor julgamento para criar um laboratório localizado centralmente.  Ou então, divida o Hackathon para usar vários laboratórios com base nos locais onde os participantes estão localizados.
- **Escolha um tamanho de computação mais adequado para as necessidades de uso**.

    Em geral, quanto maior o tamanho de computação, mais rápido será o desempenho da máquina virtual. No entanto, para limitar os custos, você precisará selecionar o tamanho de computação apropriado com base nas necessidades dos seus participantes. Consulte [informações de dimensionamento de VM no guia do administrador](administrator-guide.md#vm-sizing) para obter detalhes sobre os tamanhos de computação disponíveis.
- **Configure o RDP\SSH para conexão de área de trabalho remota para VMs do Linux**.

    Se seu Hackathon usar VMs do Linux, verifique se a área de trabalho remota está habilitada para que seus participantes possam usar o RDP (protocolo de área de trabalho remota) ou SSH (Secure Shell) para se conectar às suas VMs. Esta etapa só é necessária para VMs Linux e deve ser habilitada durante a criação do laboratório. Além disso, para o RDP, talvez seja necessário instalar e configurar o servidor RDP e os pacotes de GUI na VM do modelo antes da publicação.  Para obter mais informações, consulte o [Guia de instruções sobre como habilitar a área de trabalho remota para Linux](how-to-enable-remote-desktop-linux.md).

- **Instale e interrompa as atualizações do Windows**. 

    Se você estiver usando uma imagem do Windows, recomendamos que instale as atualizações mais recentes do Windows na [VM de modelo](how-to-create-manage-template.md) do laboratório antes de publicá-lo para criar VMs de laboratórios. É para fins de segurança e para impedir que os participantes sejam interrompidos durante o Hackathon para instalar atualizações, o que também pode fazer com que suas VMs sejam reiniciadas. Você também pode considerar desativar as atualizações do Windows para evitar qualquer interrupção futura. Consulte o [Guia de instruções sobre como instalar e configurar as atualizações do Windows](how-to-prepare-windows-template.md#install-and-configure-updates).
- **Decida como os alunos farão o backup de seu trabalho**. 

    Os alunos são cada uma das máquinas virtuais atribuídas durante o tempo de vida do Hackathon. Eles podem salvar seu trabalho diretamente no computador, mas é recomendável que os alunos cometam seu trabalho para que eles tenham acesso a ele depois que o Hackathon terminar. Por exemplo, eles devem ser salvos em um local externo, como OneDrive, GitHub e assim por diante. Para usar o OneDrive, você pode optar por configurá-lo automaticamente para alunos em suas máquinas virtuais de laboratório. Consulte o [Guia de instruções para instalar e configurar o onedrive](how-to-prepare-windows-template.md#install-and-configure-onedrive).
- **Defina a capacidade da VM de acordo com o número de participantes**. 

    Verifique se a capacidade da máquina virtual do laboratório está definida com base no número de participantes que você espera no seu Hackathon. Quando você publica a máquina virtual de modelo, pode levar várias horas para criar todos os computadores no laboratório. É por isso que recomendamos que você faça isso com antecedência para o início do Hackathon. Para obter mais informações, consulte o [Guia de instruções sobre como atualizar a capacidade do laboratório](how-to-set-virtual-machine-passwords.md#update-the-lab-capacity).

- **Decida se deseja restringir o acesso ao laboratório**. 

    Ao adicionar usuários ao laboratório, há uma opção de acesso restrito habilitada por padrão. Esse recurso exige que você adicione todos os emails dos seus participantes do Hackathon à lista antes que eles possam registrar e acessar o laboratório usando o link de registro. Se você tiver um Hackathon em que não saiba quem os participantes estarão antes do evento, você pode optar por desabilitar a opção de acesso restrito, que permite que qualquer pessoa Registre-se no laboratório usando o link de registro. Para obter mais informações, consulte o [Guia de instruções sobre como adicionar usuários](how-to-configure-student-usage.md).

- **Verifique as configurações de agendamento, cota e desligamento** automático. 

    Os serviços de laboratório fornecem vários controles de custo para limitar o uso de VMs. No entanto, se essas configurações estiverem configuradas incorretamente, elas poderão fazer com que as máquinas virtuais do laboratório sejam desligadas inesperadamente. Para garantir que essas configurações sejam definidas adequadamente para seu Hackathon, verifique as seguintes configurações:

    **Agenda**: uma [agenda](how-to-create-schedules.md) permite que você controle automaticamente quando os computadores dos laboratórios são iniciados e desligados. Por padrão, nenhuma agenda é configurada quando você cria um novo laboratório. No entanto, você deve garantir que a agenda do laboratório esteja definida de acordo com o que faz sentido para seu Hackathon.  Por exemplo, se seu Hackathon começar no sábado às 8:00 AM e terminar no domingo às 5:00 PM, você poderá criar uma agenda que inicia automaticamente a máquina às 7:30 de sábado (cerca de 30 minutos antes do início do Hackathon) e a desliga em 5:00 PM no domingo. Em vez disso, você também pode optar por não usar uma agenda.

    **Cota**: a [cota](how-to-configure-student-usage.md#set-quotas-for-users) controla o número de horas que os participantes terão acesso a uma máquina virtual fora das horas agendadas. Se a cota for atingida enquanto um participante a estiver usando, a máquina será desligada automaticamente e o participante não poderá reiniciá-la, a menos que a cota seja aumentada. Por padrão, quando você cria um laboratório, a cota é definida como 10 horas. Novamente, você deve ter certeza de definir a cota para que ela permita tempo suficiente para o Hackathon, o que é especialmente importante se você não tiver criado uma agenda.

    **Desligamento** automático: quando habilitado, a configuração de [desligamento](how-to-enable-shutdown-disconnect.md) automática faz com que as máquinas virtuais do Windows sejam desligadas automaticamente após um determinado período de tempo quando um aluno se desconectar da sessão RDP. Por padrão, essa configuração está desabilitada.

- **Defina as configurações de firewall para permitir conexões com VMs de laboratório**. 

    Verifique se as configurações de firewall da sua escola ou da organização permitem conectar-se a VMs de laboratório usando o RDP\SSH. Para obter mais informações, consulte o [Guia de instruções sobre como definir as configurações de firewall de sua rede](how-to-configure-firewall-settings.md).

- **Instale o RDP\SSH Client nos tablets, Macs, PCs e assim por diante dos participantes**.

    Os participantes do Hackathon devem ter um cliente RDP e/ou SSH instalado em seus tablets ou laptops que eles usarão para se conectar às VMs do laboratório. Você pode escolher entre clientes RDP ou SSH diferentes, como:

    - Aplicativo de **conexão de área de trabalho remota** da Microsoft para conexões RDP. O aplicativo Conexão de Área de Trabalho Remota tem suporte em diferentes tipos de plataformas, incluindo Chromebooks e [Mac](https://techcommunity.microsoft.com/t5/azure-lab-services/connecting-to-azure-lab-services-environments-on-your-macos/ba-p/1290162).
    - Saída [para usar o ssh para se](https://techcommunity.microsoft.com/t5/azure-lab-services/connecting-to-azure-lab-services-environments-on-your-macos/ba-p/1290162) conectar a uma VM do Linux.
- **Verifique as máquinas virtuais do laboratório**. 

    Depois de publicar as VMs do laboratório, verifique se elas estão configuradas corretamente. Você só precisa fazer essa verificação para uma das máquinas virtuais do laboratório do participante:

    1. Conecte-se usando o RDP e/ou SSH.
    2. Abra cada aplicativo e ferramenta adicionais que você instalou para personalizar a imagem de máquina virtual base.
    3. Percorra alguns cenários básicos que são representativos das atividades que os participantes farão para garantir que o desempenho da VM seja adequado com base no tamanho de computação selecionado.

## <a name="on-the-day-of-hackathon"></a>No dia de Hackathon
Esta seção descreve as etapas para concluir o dia de sua Hackathon.

1. **Inicie as VMs do laboratório**.

    Dependendo do seu sistema operacional, sua máquina de laboratório pode levar até 30 minutos para ser iniciada. Como resultado, é importante iniciar os computadores antes que o Hackathon seja iniciado para que seus participantes não precisem esperar. Se você estiver usando uma agenda, verifique se as VMs são iniciadas automaticamente pelo menos 30 minutos antes também.
2. **Convide os alunos para registrar e acessar sua máquina virtual de laboratório**. 

    Forneça aos seus participantes as seguintes informações para que os participantes possam acessar suas VMs de laboratório. 

    - O link de registro do laboratório. 
    - Credenciais que devem ser usadas para se conectar ao computador. Esta etapa se aplicará somente se seu laboratório usar uma imagem baseada no Windows e você tiver configurado todas as VMs para usar a mesma senha.
    - Instruções sobre como os participantes e/Oum o RDP para seus computadores.

        Para obter mais informações, consulte [o guia de instruções sobre como enviar convites para usuários](how-to-configure-student-usage.md#send-invitations-to-users) e [conectar-se a VMs do Linux](how-to-use-remote-desktop-linux-student.md). 

## <a name="next-steps"></a>Próximas etapas
Comece com a criação de uma conta de laboratório em laboratórios seguindo as instruções no artigo: [tutorial: configurar uma conta de laboratório com Azure Lab Services](tutorial-setup-lab-account.md).
