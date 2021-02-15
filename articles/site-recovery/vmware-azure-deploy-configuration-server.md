---
title: Implantar o servidor de configuração no Azure Site Recovery
description: Este artigo descreve como implantar um servidor de configuração para a recuperação após desastres do VMware com o Azure Site Recovery
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/15/2019
ms.author: ramamill
ms.openlocfilehash: a8ceb3df68ebe42f83c70ed62327bf59c0dfc225
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92359808"
---
# <a name="deploy-a-configuration-server"></a>Implante um servidor de configuração

Você implanta um servidor de configuração local quando você usa o [Azure Site Recovery](site-recovery-overview.md) para recuperação de desastre de VMs VMware e servidores físicos para o Azure. O servidor de configuração coordena a comunicação entre o ambiente de VMware local e o Azure. Ele também gerencia a replicação de dados. Este artigo orienta você pelas etapas necessárias para implantar o servidor de configuração quando você estiver replicando VMs do VMware no Azure. Se você precisar configurar um servidor de configuração para replicação de servidor físico, consulte [Configurar o servidor de configuração para recuperação de desastre de servidores físicos no Azure](physical-azure-set-up-source.md).

> [!TIP]
> Para saber mais sobre a função de um servidor de configuração como parte da arquitetura de Azure Site Recovery, confira [arquitetura de recuperação de desastre do VMware para o Azure](vmware-azure-architecture.md).

## <a name="deploy-a-configuration-server-through-an-ova-template"></a>Implantar um servidor de configuração por meio de um modelo OVA

O servidor de configuração deve ser configurado como uma VM VMware altamente disponível com determinados requisitos mínimos de hardware e de dimensionamento. Para uma implantação conveniente e fácil, o Site Recovery fornece um modelo de OVA (aplicativo de virtualização aberta) para download para configurar o servidor de configuração que está em conformidade com todos os requisitos obrigatórios listados aqui.

## <a name="prerequisites"></a>Pré-requisitos

Os requisitos mínimos de hardware para um servidor de configuração são resumidos nas seções a seguir.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="azure-active-directory-permission-requirements"></a>Requisitos de permissão do Azure Active Directory

Você deve ter um usuário com uma das seguintes permissões definidas no Azure Active Directory (Azure AD) para registrar o servidor de configuração com os serviços de Azure Site Recovery.

1. O usuário deve ter uma função de desenvolvedor de aplicativo para criar um aplicativo.
    - Para verificar, entre no portal do Azure.</br>
    - Vá para **Azure Active Directory**  >  **funções e administradores**.</br>
    - Verifique se a função de desenvolvedor de aplicativo está atribuída ao usuário. Caso contrário, use um usuário com essa permissão ou entre em contato com um [administrador para habilitar a permissão](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md#assign-roles).
    
2. Se a função de desenvolvedor de aplicativo não puder ser atribuída, verifique se o sinalizador **usuários podem registrar aplicativos** está definido como **true** para que o usuário crie uma identidade. Para habilitar essas permissões:
    - Entre no portal do Azure.
    - Vá para **Azure Active Directory**  >  **configurações do usuário**.
    - Em **registros de aplicativo**, **os usuários podem registrar aplicativos**, selecione **Sim**.

      ![AD_application_permission do Azure](media/vmware-azure-deploy-configuration-server/AAD_application_permission.png)

> [!NOTE]
> *Não há suporte para*serviços de Federação do Active Directory (AD FS). Use uma conta gerenciada por meio de [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md).

## <a name="download-the-template"></a>Baixe o modelo

1. No cofre, vá para **Preparar infraestrutura** > **Origem**.
2. Em **Preparar origem**, selecione **+Servidor de configuração**.
3. Em **Adicionar Servidor**, verifique se o **Servidor de configuração para VMware** aparece em **Tipo de servidor**.
4. Baixe o modelo de OVA para o servidor de configuração.

   > [!TIP]
   >Você também pode baixar a versão mais recente do modelo do servidor de configuração diretamente do [centro de download da Microsoft](https://aka.ms/asrconfigurationserver).

> [!NOTE]
> A licença fornecida com um modelo OVA é uma licença de avaliação válida por 180 dias. Após esse período, você deve adquirir uma licença.

## <a name="import-the-template-in-vmware"></a>Importar o modelo para a VMware

1. Entre no VMware vCenter Server ou no host vSphere ESXi usando o cliente VMware vSphere.
2. No menu **arquivo** , selecione **implantar modelo OVF** para iniciar o assistente para **implantar modelo de OVF** .

     ![Implantar modelo OVF](./media/vmware-azure-deploy-configuration-server/vcenter-wizard.png)

3. Em **Selecionar origem**, insira o local do OVF baixado.
4. Em **Revisar detalhes**, selecione **Avançar**.
5. Em **Selecionar nome e pasta** e **Selecionar configuração**, aceite as configurações padrão.
6. Em **Selecionar armazenamento**, para obter o melhor desempenho, selecione **Thick Provision Eager Zeroed** em **Selecionar formato de disco virtual**. O uso da opção de provisionamento dinâmico pode afetar o desempenho do servidor de configuração.
7. No restante das páginas do assistente, aceite as configurações padrão.
8. Em **Pronto para concluir**:

    * Para configurar a VM com as configurações padrão, selecione **ligar após a implantação**  >  **concluir**.
    * Para adicionar uma interface de rede adicional, desmarque **Ligar após a implantação** e, em seguida, selecione **Concluir**. Por padrão, o modelo de servidor de configuração é implantado com uma única NIC. É possível incluir NICs adicionais após a implantação.

> [!IMPORTANT]
> Não altere as configurações de recursos, como memória, núcleos e restrição de CPU, ou modifique ou exclua os serviços ou arquivos instalados no servidor de configuração após a implantação. Esses tipos de alterações afetam o registro do servidor de configuração com os serviços do Azure e o desempenho do servidor de configuração.

## <a name="add-an-additional-adapter"></a>Adicionar mais um adaptador

> [!NOTE]
> Duas NICs são necessárias se você planeja reter os endereços IP dos computadores de origem no failover e desejar fazer failback para o local mais tarde. Uma NIC está conectada a computadores de origem e a outra NIC é usada para a conectividade do Azure.

Se você deseja adicionar mais uma NIC ao servidor de configuração, adicione-o antes de registrar o servidor no cofre. Adicionar outros adaptadores não é suportado após o registro.

1. No inventário vSphere Cliente, clique com o botão direito na VM e selecione **Editar configurações**.
2. Em **Hardware**, selecione **Adicionar** > **Adaptador Ethernet**. Em seguida, selecione **Avançar**.
3. Selecione um tipo de adaptador e uma rede.
4. Para se conectar à NIC virtual quando a VM estiver ativada, selecione **Conectar-se ao ligar**. Em seguida, selecione **Avançar**  >  **concluir**  >  **OK**.

## <a name="register-the-configuration-server-with-azure-site-recovery-services"></a>Registre o servidor de configuração com os serviços do Azure Site Recovery

1. No console do cliente VMware vSphere, ative a VM.
2. A VM será inicializada com uma experiência de instalação do Windows Server 2016. Aceite o contrato de licença e insira uma senha de administrador.
3. Após a conclusão da instalação, entre na VM como administrador.
4. Na primeira vez que você entrar, em alguns segundos a ferramenta de configuração do Azure Site Recovery será iniciada.
5. Insira um nome que seja usado para registrar o servidor de configuração com o Site Recovery. Em seguida, selecione **Avançar**.
6. A ferramenta verifica se a VM pode se conectar ao Azure. Depois que a conexão for estabelecida, selecione **Entrar** para fazer logon na sua assinatura do Azure.</br>
    a. As credenciais devem ter acesso ao cofre no qual você deseja registrar o servidor de configuração.</br>
    b. Verifique se a conta de usuário escolhida tem permissão para criar um aplicativo no Azure. Para habilitar as permissões necessárias, siga as diretrizes na seção [Azure Active Directory requisitos de permissão](#azure-active-directory-permission-requirements).
7. A ferramenta executa algumas tarefas de configuração e, em seguida, é reinicializada.
8. Entre novamente no computador. O assistente de gerenciamento do servidor de configuração é iniciado automaticamente em alguns segundos.

### <a name="configure-settings"></a>Definir configurações

1. No assistente de gerenciamento do servidor de configuração, selecione **Configurar conectividade**. Nas caixas suspensas, primeiro selecione a NIC que o servidor de processo interno usa para descoberta e instalação por push do serviço de mobilidade em computadores de origem. Em seguida, selecione a NIC que o servidor de configuração usa para conectividade com o Azure. Clique em **Salvar**. Não é possível alterar essa configuração depois de ela ter sido definida. Não altere o endereço IP de um servidor de configuração. Verifique se o IP atribuído ao servidor de configuração é um IP estático e não um IP DHCP.
2. Em **selecionar cofre dos serviços de recuperação**, entre no Microsoft Azure com as credenciais usadas na etapa 6 de [registrar o servidor de configuração com os serviços de Azure site Recovery](#register-the-configuration-server-with-azure-site-recovery-services).
3. Depois de entrar, selecione sua assinatura do Azure e o grupo de recursos e o cofre relevantes.

    > [!NOTE]
    > Após o registro, não há flexibilidade para alterar o cofre dos serviços de recuperação.
    > A alteração de um cofre dos serviços de recuperação requer a desassociação do servidor de configuração do cofre atual e a replicação de todas as máquinas virtuais protegidas no servidor de configuração é interrompida. Para saber mais, consulte [gerenciar o servidor de configuração para recuperação de desastre de VM VMware](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault).

4. Em **instalar software de**terceiros:

    |Cenário   |Etapas a serem executadas  |
    |---------|---------|
    |Posso baixar e instalar o MySQL manualmente?     |  Sim. Baixe o aplicativo MySQL, coloque-o na pasta **C:\Temp\ASRSetup**e, em seguida, instale manualmente. Depois de aceitar os termos e selecionar **baixar e instalar**, o portal diz *já instalado*. Você pode prosseguir para a próxima etapa.       |
    |Posso evitar o download do MySQL online?     |   Sim. Coloque o aplicativo instalador do MySQL na pasta **C:\Temp\ASRSetup**. Aceite os termos, selecione **baixar e instalar**e o portal usará o instalador adicionado para instalar o aplicativo. Após a conclusão da instalação, vá para a próxima etapa.    |
    |Quero baixar e instalar o MySQL por meio do Azure Site Recovery.    |  Aceite o contrato de licença e selecione **baixar e instalar**. Após a conclusão da instalação, vá para a próxima etapa.       |

5. Em **validar configuração do dispositivo**, os pré-requisitos são verificados antes de continuar.
6. Em **Configurar o servidor vCenter Server/vSphere ESXi**, insira o FQDN ou o endereço IP do servidor vCenter, ou host vSphere, onde as VMs que você deseja replicar estão localizadas. Insira a porta na qual o servidor está escutando. Insira um nome amigável a ser usado para o servidor VMware no cofre.
7. Insira as credenciais a serem usadas pelo servidor de configuração para se conectar ao servidor VMware. O Site Recovery usa essas credenciais para descobrir automaticamente as VMs do VMware que estão disponíveis para replicação. Selecione **Adicionar** > **Continuar**. As credenciais digitadas aqui são salvas localmente.
8. Em **configurar credenciais da máquina virtual**, insira o nome de usuário e a senha das máquinas virtuais para instalar automaticamente o serviço de mobilidade durante a replicação. Para computadores **Windows** , a conta precisa de privilégios de administrador local nos computadores que você deseja replicar. Para o **Linux**, forneça detalhes para a conta raiz.
9. Selecione **Finalizar configuração** para concluir o registro.
10. Após o término do registro, abra o portal do Azure e verifique se o servidor de configuração e o servidor VMware estão listados em **Cofre dos Serviços de Recuperação** > **Gerenciar** > **Infraestrutura do Site Recovery** > **Servidores de Configuração**.

## <a name="upgrade-the-configuration-server"></a>Atualizar o servidor de configuração

Para atualizar o servidor de configuração para a versão mais recente, consulte [gerenciar o servidor de configuração para recuperação de desastre de VM VMware](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server). Para obter instruções sobre como atualizar todos os componentes do Site Recovery, consulte [atualizações de serviço no site Recovery](service-updates-how-to.md).

## <a name="manage-the-configuration-server"></a>Gerenciar o servidor de configuração

Para evitar interrupções na replicação em andamento, verifique se o endereço IP do servidor de configuração não é alterado depois que o servidor de configuração é registrado em um cofre. Para saber mais sobre as tarefas comuns de gerenciamento do servidor de configuração, consulte [gerenciar o servidor de configuração para a recuperação de desastre de VM do VMware](vmware-azure-manage-configuration-server.md).

## <a name="troubleshoot-deployment-issues"></a>Solucionar problemas de implantação

Consulte nosso [artigo de solução de problemas](vmware-azure-troubleshoot-configuration-server.md) para resolver problemas de conectividade & a implantação.

## <a name="faqs"></a>Perguntas frequentes

* Por quanto tempo a licença fornecida em um servidor de configuração implantado por meio de OVF é válida? O que acontecerá se eu não reativar a licença?

    A licença fornecida com um modelo OVA é uma licença de avaliação válida por 180 dias. Antes da expiração, você precisa ativar a licença. Caso contrário, isso pode resultar em um desligamento frequente do servidor de configuração e causar um obstáculo às atividades de replicação. Para obter mais informações, consulte [gerenciar o servidor de configuração para recuperação de desastre de VM VMware](vmware-azure-manage-configuration-server.md#update-windows-license).

* Posso usar a VM onde o servidor de configuração está instalado para finalidades diferentes?

    Não. Use a VM para a única finalidade do servidor de configuração. Certifique-se de seguir todas as especificações mencionadas em [pré-requisitos](#prerequisites) para o gerenciamento eficiente da recuperação de desastres.
* Posso trocar o cofre já registrado no servidor de configuração por um cofre recém-criado?

    Não. Depois que um cofre é registrado no servidor de configuração, ele não pode ser alterado.
* Posso usar o mesmo servidor de configuração para proteger máquinas físicas e virtuais?

    Sim. O mesmo servidor de configuração pode ser usado para replicar máquinas físicas e virtuais. No entanto, o computador físico pode realizar o failback somente para uma VM VMware.
* Qual é a finalidade de um servidor de configuração e onde ele é usado?

    Para saber mais sobre o servidor de configuração e suas funcionalidades, confira [arquitetura de replicação do VMware para o Azure](vmware-azure-architecture.md).
* Onde posso encontrar a versão mais recente do servidor de configuração?

    Para obter as etapas para atualizar o servidor de configuração por meio do portal, veja [Atualizar o servidor de configuração](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server). Para obter instruções sobre como atualizar todos os componentes do Site Recovery, consulte [atualizações de serviço no site Recovery](./service-updates-how-to.md).
* Em que local posso baixar a frase secreta para o servidor de configuração?

    Para baixar a senha, consulte [gerenciar o servidor de configuração para a recuperação de desastre de VM do VMware](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase).
* Posso alterar a frase secreta?

    Não. Não altere a frase secreta do servidor de configuração. Uma alteração na frase secreta interrompe a replicação de computadores protegidos e leva a um estado de integridade crítico.
* Em que local posso baixar as chaves de registro do cofre?

    Em **cofre dos serviços de recuperação**, selecione **gerenciar**  >  **site Recovery**  >  **servidores de configuração**de infraestrutura. Em **Servidores**, selecione **Baixar a chave de registro** para baixar o arquivo de credenciais do cofre.
* Posso clonar um servidor de configuração existente e usá-lo para a orquestração de replicação?

    Não. Não há suporte para o uso de um componente de servidor de configuração clonado. A clonagem de um servidor de processo de expansão também é um cenário sem suporte. A clonagem de Site Recovery componentes afeta as replicações contínuas.

* Posso alterar o IP de um servidor de configuração?

    Não. Não altere o endereço IP de um servidor de configuração. Verifique se todos os IPs atribuídos ao servidor de configuração são IPs estáticos e não IPs de DHCP.
* Posso configurar um servidor de configuração no Azure?

    Configure um servidor de configuração em um ambiente local com uma linha de visão direta com o v-Center e para minimizar as latências de transferência de dados. É possível fazer backups agendados do servidor de configuração para [fins de failback](vmware-azure-manage-configuration-server.md#failback-requirements).

* Posso alterar o driver de cache em um servidor de configuração ou em um servidor de processo de expansão?

    Não, o driver de cache não pode ser alterado após a conclusão da configuração.

Para obter mais perguntas frequentes sobre servidores de configuração, consulte [perguntas comuns do servidor de configuração](vmware-azure-common-questions.md#configuration-server).

## <a name="next-steps"></a>Próximas etapas

Configurar a recuperação de desastre de [VMs do VMware](vmware-azure-tutorial.md) para o Azure.