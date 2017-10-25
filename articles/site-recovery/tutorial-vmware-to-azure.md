---
title: "Configurar a recuperação de desastre de VMs VMware locais para o Azure com o Azure Site Recovery | Microsoft Docs"
description: "Saiba como configurar a recuperação de desastre de VMs VMware locais para o Azure com o serviço Azure Site Recovery."
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2017
ms.author: raynew
ms.openlocfilehash: ee445c8af2fc6620385d9c462d4c6551da3d7367
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-vmware-vms"></a>Configurar a recuperação de desastre de VMs VMware locais para o Azure

Este tutorial mostra como configurar a recuperação de desastre para o Azure de VMs VMware locais em execução no Windows. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um cofre de Serviços de Recuperação para o Site Recovery
> * Configurar os ambientes de replicação de origem e destino
> * Criar uma política de replicação
> * Habilitar a replicação para uma VM

Este é o terceiro tutorial de uma série. Este tutorial presume que você já tenha concluído as tarefas nos tutoriais anteriores:

1. [Preparar o Azure](tutorial-prepare-azure.md)
2. [Preparar o VMware local](tutorial-prepare-on-premises-vmware.md)

Antes de começar, é aconselhável [examinar a arquitetura](concepts-vmware-to-azure-architecture.md) do cenário de recuperação de desastre.

## <a name="configure-vmware-account-permissions"></a>Configurar permissões de conta do VMware

1. Criar uma função no nível do vCenter. Fornecer à função o nome **Azure_Site_Recovery**.
2. Atribuir as seguintes permissões para a função **Azure_Site_Recovery**.

   **Tarefa** | **Função/Permissões** | **Detalhes**
   --- | --- | ---
   **Descoberta VM** | Objeto de data center –> Propagar para o objeto filho, função = somente leitura | Pelo menos um usuário somente leitura.<br/><br/> Usuário atribuído no nível de datacenter e tem acesso a todos os objetos no datacenter.<br/><br/> Para restringir o acesso, atribua a função **Nenhum acesso** com o objeto **Propagar para filho** aos objetos filho (hosts vSphere, armazenamentos de dados, VMs e redes).
   **Replicação, failover, failback totais** |  Objeto de Data Center –> Propagar para o Objeto Filho, função = Azure_Site_Recovery<br/><br/> Armazenamento de dados -> alocar espaço, procurar armazenamento de dados, operações de arquivo de baixo nível, remover arquivo, atualizar arquivos de máquina virtual<br/><br/> Rede -> Atribuição de rede<br/><br/> Recurso -> Atribuir VM ao pool de recursos, migrar VM desligada, migrar VM ligada<br/><br/> Tarefas -> Criar tarefa, atualizar tarefa<br/><br/> Máquina virtual -> Configuração<br/><br/> Máquina virtual -> Interagir -> responder à pergunta, conexão de dispositivo, configurar mídia de CD, configurar mídia de disquete, desligar, ligar, instalação de ferramentas VMware<br/><br/> Máquina virtual -> Inventário -> Criar, registrar, cancelar registro<br/><br/> Máquina virtual -> Provisionamento -> Permitir download de máquina virtual, permitir upload de arquivos de máquina virtual<br/><br/> Máquina virtual -> Instantâneos -> Remover instantâneos | Usuário atribuído no nível de datacenter e tem acesso a todos os objetos no datacenter.<br/><br/> Para restringir o acesso, atribua a função **Nenhum acesso** com o objeto **Propagar para filho** aos objetos filho (hosts vSphere, armazenamentos de dados, VMs e redes).

3. Crie um usuário no vCenter Server ou no host do vSphere. Atribua a função ao usuário.

## <a name="specify-what-you-want-to-replicate"></a>Especifique o que você deseja replicar

O serviço de Mobilidade deve ser instalado em cada VM que você deseja replicar. O Site Recovery instala esse serviço automaticamente quando você habilita a replicação para a VM. Para instalação automática, você precisa preparar uma conta que o Site Recovery usará para acessar a VM.

Você pode usar uma conta local ou de domínio. Para VMs do Linux, a conta deve ser a raiz no servidor Linux de origem. Para VMs do Windows, se você não estiver usando uma conta de domínio, desabilite o Controle de Acesso de Usuários Remotos no computador local:

  - No Registro, em **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**, adicione a entrada DWORD **LocalAccountTokenFilterPolicy** e defina o valor como 1.

## <a name="set-up-the-source-environment"></a>Configurar o ambiente de origem

A configuração do ambiente de origem consiste em baixar a Instalação Unificada do Site Recovery, definir o servidor de configuração e registrá-lo no cofre, e descobrir VMs.

O servidor de configuração é uma única VM VMware local para hospedar todos os componentes do Site Recovery. Essa VM executa o servidor de configuração, o servidor de processo e o servidor de destino mestre.

- O servidor de configuração coordena a comunicação entre o ambiente local e o Azure e gerencia a replicação de dados.
- O servidor de processo atua como um gateway de replicação. Recebe dados de replicação, otimiza-os com caching, compactação e criptografia e os envia para o Armazenamento do Azure. O servidor de processo também instala o serviço de Mobilidade nas VMs que você deseja replicar e executa a descoberta automática de VMs em servidores VMware locais.
- O servidor de destino mestre lida com os dados de replicação durante o failback do Azure.

A VM do servidor de configuração deve ser uma VM VMware altamente disponível que atenda aos seguintes requisitos:

| **Requisito** | **Detalhes** |
|-----------------|-------------|
| Número de núcleos de CPU| 8 |
| RAM | 12 GB |
| Número de discos | 3 – disco de OS, disco de cache do servidor de processo, unidade de retenção (para failback) |
| Espaço livre em disco (cache do servidor de processo) | 600 GB |
| Espaço livre em disco (disco de retenção) | 600 GB |
| Versão do sistema operacional | Windows Server 2012 R2 |
| Localidade do sistema operacional | Inglês (en-us) |
| Versão do VMware vSphere PowerCLI | [PowerCLI 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1 "PowerCLI 6.0") |
| Funções do Windows Server | Não habilite estas funções: Active Directory Domain Services, Serviços de Informações da Internet, Hyper-V |
| Tipo de NIC | VMXNET3 |
| Tipo de endereço IP | estático |
| Portas | 443 (orquestração do canal de controle)<br/>9443 (transporte de dados)|

Na VM do servidor de configuração, verifique se o relógio do sistema está sincronizado com um Servidor de Horário.
O horário deve ser sincronizado dentro de 15 minutos. Se a diferença de tempo for maior que 15 minutos, a instalação falhará.

Verifique se a VM do servidor de configuração consegue acessar estas URLs:

- *.accesscontrol.windows.net. Usado para controle de acesso e gerenciamento de identidades.
- *.backup.windowsazure.com. Usado para transferência de dados de replicação e coordenação.
- *.blob.core.windows.net. Usado para acessar a conta de armazenamento que armazena os dados replicados.
- *.hypervrecoverymanager.windowsazure.com. Usado para operações de gerenciamento de replicação e coordenação.
- time.nist.gov and time.windows.com. Usados para verificar a sincronização de horário entre a hora do sistema e a hora global.

URLs para a nuvem do Azure Governamental:

- *.ugv.hypervrecoverymanager.windowsazure.us
- *.ugv.backup.windowsazure.us
- *.ugi.hypervrecoverymanager.windowsazure.us
- *.ugi.backup.windowsazure.us

As regras de firewall baseado em endereço IP devem permitir a comunicação com [Intervalos de IP de Datacenter do Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) e com as portas 443 (HTTPS) e 9443 (replicação de dados). Lembre-se de permitir os intervalos de endereços IP para a região do Azure da sua assinatura e para o Oeste dos EUA (usado para Controle de Acesso e Gerenciamento de Identidade).

### <a name="download-the-site-recovery-unified-setup"></a>Baixar a Instalação Unificada do Site Recovery

1. Abra o [Portal do Azure](https://portal.azure.com) e clique em **Todos os recursos**.
2. Clique no cofre de Recuperação de Serviço chamado **ContosoVMVault**.
3. Clique em **Site Recovery** > **Preparar a infraestrutura** > **Meta de proteção**.
4. Selecione **Local** para onde as máquinas estão localizadas, **Para o Azure** para onde você deseja replicar as máquinas e **Sim, com o VMware vSphere Hypervisor**. Em seguida, clique em **OK**.
5. No painel Preparar origem, clique em **+ Servidor de Configuração**.
6. Em **Adicionar Servidor**, verifique se **Servidor de Configuração** aparece em **Tipo de servidor**.
7. Baixe o arquivo de instalação Configuração Unificada da Recuperação de Site.
8. Baixe a chave do registro do cofre. Você precisará dela quando executar a Configuração Unificada. A chave é válida por cinco dias após ser gerada.

   ![Configurar origem](./media/tutorial-vmware-to-azure/source-settings.png)

### <a name="set-up-the-configuration-server"></a>Configurar o servidor de configuração

1. Execute o arquivo de instalação de Configuração Unificada.
2. Em **Antes de começar**, selecione **Instalar o servidor de configuração e o servidor em processo** e, sem seguida, clique em **Avançar**.

3. Em **Licença de Software de Terceiros**, clique em **Aceito** para baixar e instalar o MySQL e, em seguida, clique em **Avançar**.

4. Em **Registro**, selecione a chave de registro que você baixou do cofre.

5. Em **Configurações da Internet**, especifique como o Provedor em execução no servidor de configuração se conecta ao Azure Site Recovery pela Internet.

   - Se você desejar se conectar ao proxy que está configurado atualmente no computador, selecione **Conectar-se ao Azure Site Recovery usando um servidor proxy**.
   - Se quiser que o Provedor se conecte diretamente, selecione **Conectar diretamente o Azure Site Recovery sem um servidor proxy**.
   - Se o proxy existente exigir autenticação ou se você quiser usar um proxy personalizado para a conexão do provedor, selecione **Conectar-se com as configurações de proxy personalizadas** e especifique o endereço, a porta e as credenciais.

   ![Firewall](./media/tutorial-vmware-to-azure/combined-wiz4.png)

6. Em **Verificação de Pré-requisitos**, a configuração executa uma verificação para garantir que a instalação pode ser executada. Se aparecer um aviso sobre a **Verificação de sincronização de tempo global**, verifique se a hora no relógio do sistema (configurações de **Data e Hora**) é a mesma que a do fuso horário.

   ![Pré-requisitos](./media/tutorial-vmware-to-azure/combined-wiz5.png)

7. Em **Configuração do MySQL**, crie credenciais para fazer logon na instância do servidor MySQL instalada.

8. Em **Detalhes do Ambiente**, selecione **Sim** para proteger as VMs VMware. A instalação verifica se o PowerCLI 6.0 está instalado.

9. Em **Localização de Instalação**, selecione a localização em que você deseja instalar os binários e armazenar o cache. A unidade selecionado deve ter ao menos 5 GB de espaço em disco disponível, mas é recomendável uma unidade de cache com ao menos 600 GB de espaço livre.

10. Em **Seleção da Rede**, especifique o ouvinte (adaptador de rede e porta SSL) no qual o servidor de configuração envia e recebe os dados de replicação. A porta 9443 é a porta padrão usada para enviar e receber o tráfego de replicação, mas você pode modificar esse número de porta para atender aos requisitos do seu ambiente. Também podemos abrir a porta 443, usada para orquestrar operações de replicação. Não use a porta 443 para enviar ou receber tráfego de replicação.

11. Em **Resumo**, examine as informações e clique em **Instalar**. A instalação instala o servidor de configuração e registra nele o serviço Azure Site Recovery.

    ![Resumo](./media/tutorial-vmware-to-azure/combined-wiz10.png)

    Após a conclusão da instalação, uma frase secreta é gerada. Você precisará dela quando habilitar a replicação, portanto copie-a e guarde-a em um local seguro. O servidor é exibido no painel **Configurações** > **Servidores** no cofre.

### <a name="configure-automatic-discovery"></a>Configurar a descoberta automática

Para descobrir as VMs, o servidor de configuração precisa se conectar aos servidores VMware locais. Para os fins deste tutorial, adicione o vCenter Server ou os hosts do vSphere, usando uma conta que tenha privilégios de administrador no servidor.

1. No seu servidor de configuração, inicie **CSPSConfigtool.exe**. Está disponível como um atalho na área de trabalho e localizada na pasta *local da instalação*\home\svsystems\bin.

2. Clique em **Gerenciar Contas** > **Adicionar Conta**.

   ![Adicionar Conta](./media/tutorial-vmware-to-azure/credentials1.png)

3. Em **Detalhes da Conta**, adicione a conta que será usada para a descoberta automática.

   ![Detalhes](./media/tutorial-vmware-to-azure/credentials2.png)

Para adicionar um servidor:

1. Abra o [Portal do Azure](https://portal.azure.com) e clique em **Todos os recursos**.
2. Clique no cofre de Recuperação de Serviço chamado **ContosoVMVault**.
3. Clique em **Site Recovery** > **Preparar Infraestrutura** > **Origem**
4. Selecione **+ vCenter** para conectar-se a um vCenter Server ou a um host ESXi do vSphere.
5. Em **Adicionar vCenter**, especifique um nome amigável para o servidor. Em seguida, especifique o endereço IP ou o FQDN.
6. Deixe a porta definida para 443, a menos que os servidores VMware escutem solicitações em uma porta diferente.
7. Selecione a conta usada para a conexão com o servidor. Clique em **OK**.

A Site Recovery conecta-se a servidores VMware usando as configurações especificadas e descobre VMs.

> [!NOTE]
> Pode levar 15 minutos ou mais para que o nome da conta apareça no portal. Para atualizar imediatamente, clique em **Servidores de Configuração** > ***nome do servidor*** > **Atualizar Servidor**.

## <a name="set-up-the-target-environment"></a>Configurar o ambiente de origem

Selecione e verifique os recursos de destino.

1. Clique em **Preparar infraestrutura** > **Destino** e selecione a assinatura do Azure que você deseja usar.
2. Especifique se o seu modelo de implantação de destino é baseada no Gerenciador de Recursos ou clássico.
3. A Recuperação de Site verifica se você tem uma ou mais contas de armazenamento e redes do Azure compatíveis.

   ![Destino](./media/tutorial-vmware-to-azure/storage-network.png)

## <a name="create-a-replication-policy"></a>Criar uma política de replicação

1. Abra o [Portal do Azure](https://portal.azure.com) e clique em **Todos os recursos**.
2. Clique no cofre de Recuperação de Serviço chamado **ContosoVMVault**.
3. Para criar uma política de replicação, clique em **Infraestrutura do Site Recovery** > **Políticas de Replicação** > **+ Política de Replicação**.
4. Em **Criar política de replicação**, especifique um nome de política **VMwareRepPolicy**.
5. Em **Limite de RPO**, use o padrão de 60 minutos. Esse valor define a frequência em que são criados os pontos de recuperação. Um alerta será gerado se a replicação contínua exceder esse limite.
6. Em **Retenção de ponto de recuperação**, use o padrão de 24 horas para a duração do período de retenção de cada ponto de recuperação. Para este tutorial, podemos selecionar 72 horas. VMs replicadas podem ser recuperadas para qualquer ponto em uma janela.
7. Em **Frequência do instantâneo consistente com aplicativo**, use o padrão de 60 minutos para a frequência em que os instantâneos consistentes com o aplicativo são criados. Clique em **OK** para criar a política.

   ![Política](./media/tutorial-vmware-to-azure/replication-policy.png)

A política é associada automaticamente ao servidor de configuração. Por padrão, uma política de correspondência é criada automaticamente para failback. Por exemplo, se a política de replicação for **rep-policy**, a política de failback será **rep-policy-failback**. Essa política não é usada até você iniciar um failback do Azure.

## <a name="enable-replication"></a>Habilitar a replicação

O Site Recovery instala o serviço de Mobilidade quando a replicação é habilitada para uma VM. Pode levar 15 minutos ou mais para que as alterações entrem em vigor e apareçam no portal.

Habilite a replicação da seguinte maneira:

1. Clique em **Replicar aplicativo** > **Origem**.
2. Em **Origem**, selecione o servidor de configuração.
3. Em **Tipo de máquina**, selecione **Máquinas Virtuais**.
4. Em **Hipervisor do vCenter/vSphere**, selecione o servidor vCenter que gerencia o host vSphere ou selecione o host.
5. Selecione o servidor de processo (servidor de configuração). Em seguida, clique **OK**.
6. Em **Destino**, selecione a assinatura e o grupo de recursos em que você deseja criar as VMs com failover. Escolha o modelo de implantação que você deseja usar no Azure (clássico ou gerenciamento de recursos) para as VMs do failover.
7. Selecione a conta de armazenamento do Azure que você deseja usar para replicar os dados.
8. Selecione a rede e a sub-rede do Azure às quais as VMs do Azure se conectarão quando forem criadas após o failover.
9. Selecione **Configurar agora para computadores selecionados** para aplicar a configuração de rede a todos os computadores selecionados para proteção. Selecione **Configurar mais tarde** para selecionar a rede do Azure por computador.
10. Em **Máquinas Virtuais** > **Selecionar máquinas virtuais**, clique e selecione cada máquina que você deseja replicar. Você só pode selecionar computadores para os quais a replicação pode ser habilitada. Em seguida, clique em **OK**.
11. Em **Propriedades** > **Configurar propriedades**, selecione a conta que será usada pelo servidor de processo para instalar automaticamente o serviço de Mobilidade no computador.
12. Em **Configurações de replicação** > **Definir configurações de replicação**, verifique se a política de replicação correta está selecionada.
13. Clique em **Habilitar a Replicação**.

Você pode acompanhar o progresso do trabalho **Habilitar Proteção** em **Configurações** > **Trabalhos** > **Trabalhos de Recuperação de Site**. Após o trabalho de **Finalizar Proteção** ser executado, o computador estará pronto para failover.

Para monitorar as VMs adicionadas, você pode verificar o horário da última descoberta de VMs em **Servidores de Configuração**
> **Último Contato Em**. Para adicionar VMs sem esperar pela descoberta agendada, realce o servidor de configuração (não clique nele) e clique em **Atualizar**.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Realizar uma simulação de recuperação de desastre](site-recovery-test-failover-to-azure.md)
