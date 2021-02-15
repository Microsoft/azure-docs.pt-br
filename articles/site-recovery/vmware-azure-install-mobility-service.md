---
title: Preparar computadores de origem para instalar o serviço de mobilidade por meio da instalação por push para recuperação de desastre de VMs VMware e servidores físicos no Azure | Microsoft Docs
description: Saiba como preparar seu servidor para instalar o agente de mobilidade por meio da instalação por push para recuperação de desastre de VMs VMware e servidores físicos no Azure usando o serviço Azure Site Recovery.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: 78fddb5b4512883f8e78d6ed53f6e3dbbeba0e4f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90524990"
---
# <a name="prepare-source-machine-for-push-installation-of-mobility-agent"></a>Preparar o computador de origem para a instalação por push do agente de mobilidade

Quando você configura a recuperação de desastre para VMs VMware e servidores físicos usando [Azure Site Recovery](site-recovery-overview.md), instale o [Serviço Mobilidade de Recuperação do Site](vmware-physical-mobility-service-overview.md) em cada VM VMware local e servidor físico.  O serviço Mobility captura gravações de dados na máquina e as encaminha para o servidor do processo de Recuperação do Site.

## <a name="install-on-windows-machine"></a>Instalar na máquina Windows

Em cada máquina Windows que você deseja proteger, faça o seguinte:

1. Certifique-se de que haja conectividade de rede entre a máquina e o servidor de processo. Se você não tiver configurado um servidor de processos separado, por padrão, ele estará em execução no servidor de configuração.
1. Crie uma conta que o servidor de processo possa usar para acessar o computador. A conta deve ter direitos de administrador, seja local ou do domínio. Use essa conta somente para a instalação por push e atualizações de agentes.
2. Se você não usar uma conta de domínio, desative o controle de acesso de usuário remoto no computador local da seguinte maneira:
    - Na chave do Registro HKEY_LOCAL_MACHINE \ SOFTWARE \ Microsoft \ Windows \ CurrentVersion \ Policies \ System, adicione um novo DWORD: **LocalAccountTokenFilterPolicy**. Defina o valor como **1**.
    -  Para fazer isso no prompt de comando, execute o seguinte comando:
    
       ```
       REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1 /f
       ```

3. No Firewall do Windows na máquina que você deseja proteger, selecione **Permitir um aplicativo ou recurso pelo Firewall**. Habilite **Compartilhamento de Arquivo e Impressora** e **Instrumentação de Gerenciamento do Windows (WMI)**. Em computadores pertencentes a um domínio, pode-se configurar o firewall com um objeto de Política de Grupo (GPO).

   ![Configurações de firewall](./media/vmware-azure-install-mobility-service/mobility1.png)

4. Adicione a conta criada em CSPSConfigtool. Para fazer isso, entre servidor de configuração.
5. Abra **cspsconfigtool.exe**. Ele está disponível como um atalho na área de trabalho e na pasta%ProgramData%\ASR\home\svsystems\bin.
6. Na guia **Gerenciar Contas**, selecione **Adicionar Conta**.
7. Adicione a conta que você criou.
8. Insira as credenciais usadas quando você habilitar a replicação de um computador.

## <a name="install-on-linux-machine"></a>Instalar no computador Linux

Em cada máquina Linux que você deseja proteger, faça o seguinte:

1. Assegure-se de que haja conectividade de rede entre a máquina Linux e o servidor de processo.
2. Crie uma conta que o servidor de processo possa usar para acessar o computador. A conta deve ser um usuário **raiz** no servidor Linux de origem. Use essa conta somente para a instalação por push e atualizações.
3. Verifique se o arquivo /etc/hosts no servidor Linux de origem contém entradas que mapeiam o nome do host local para os endereços IP associados a todos os adaptadores de rede.
4. Instale os pacotes openssh, openssh-server e openssl mais recentes no computador que você deseja replicar.
5. Verifique se o Secure Shell (SSH) está habilitado e em execução na porta 22.
4. Habilitar a autenticação de senha e subsistema SFTP no arquivo sshd_config. Para fazer isso, entre como **raiz**.
5. No arquivo **/etc/ssh/sshd_config** , localize a linha que começa com **PasswordAuthentication**.
6. Remova a marca de comentário da linha e altere o valor para **yes**.
7. Localize a linha que começa com **subsistema**e remova a marca de comentário da linha.

      ![Linux](./media/vmware-azure-install-mobility-service/mobility2.png)

8. Reinicie o serviço **sshd**.
9. Adicione a conta criada em CSPSConfigtool. Para fazer isso, entre servidor de configuração.
10. Abra **cspsconfigtool.exe**. Ele está disponível como atalho na área de trabalho e na pasta %ProgramData%\home\svsystems\bin.
11. Na guia **Gerenciar Contas**, selecione **Adicionar Conta**.
12. Adicione a conta que você criou.
13. Insira as credenciais usadas quando você habilitar a replicação de um computador.
1. Etapa adicional para atualização ou proteção de computadores SUSE Linux Enterprise Server 11 SP3 ou RHEL 5 ou CentOS 5 ou Debian 7. [Verifique se a versão mais recente está disponível no servidor de configuração](vmware-physical-mobility-service-overview.md#download-latest-mobility-agent-installer-for-suse-11-sp3-rhel-5-debian-7-server).

## <a name="anti-virus-on-replicated-machines"></a>Antivírus em computadores replicados

Se as máquinas que você deseja replicar tiverem o software antivírus ativo em execução, exclua a pasta de instalação do serviço Mobility das operações de antivírus (*C:\ProgramData\ASR\agent*). Isso garante que a replicação funciona conforme o esperado.

## <a name="next-steps"></a>Próximas etapas

Depois que o Mobility Service for instalado, no portal do Azure, selecione **+ Replicate** para começar a proteger essas VMs. Saiba mais sobre como habilitar a replicação para [VMs VMware](vmware-azure-enable-replication.md) e [servidores físicos](physical-azure-disaster-recovery.md#enable-replication).


