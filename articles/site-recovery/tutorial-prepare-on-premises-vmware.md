---
title: "Preparar os servidores VMware locais para recuperação de desastre das VMs VMware para o Azure | Microsoft Docs"
description: "Saiba como preparar os servidores VMware locais para recuperação de desastre para o Azure usando o serviço Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 90a4582c-6436-4a54-a8f8-1fee806b8af7
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2017
ms.author: raynew
ms.openlocfilehash: 2e59a78f2c348b581155484d77e272a050da1f1d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="prepare-on-premises-vmware-servers-for-disaster-recovery-to-azure"></a>Preparar servidores VMware locais para recuperação de desastre para o Azure

Este tutorial mostra como preparar sua infraestrutura do VMware local, quando você deseja replicar VMs VMware para o Azure. Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Preparar uma conta no vCenter Server ou no host ESXi do vSphere para automatizar a descoberta de VM
> * Preparar uma conta para a instalação automática do serviço de Mobilidade nas VMs VMware
> * Examinar os requisitos do servidor VMware
> * Examinar os requisitos de VM VMware

Nesta série de tutoriais, mostramos como fazer backup de uma única VM usando o Azure Site Recovery. Se você planeja proteger várias VMs VMware, baixe a [Ferramenta Planejador de Implantações](https://aka.ms/asr-deployment-planner) para replicação do VMware. Com essa ferramenta você coleta informações sobre compatibilidade de VM, discos por VM e rotatividade de dados por disco. A ferramenta também inclui requisitos de largura de banda da rede, bem como a infraestrutura do Azure necessária para replicação e failover de teste bem-sucedidos. [Saiba mais](site-recovery-deployment-planner.md) sobre a execução dessa ferramenta.

Este é o segundo tutorial da série. Verifique se você [configurou os componentes do Azure](tutorial-prepare-azure.md) conforme foi descrito no tutorial anterior.

## <a name="prepare-an-account-for-automatic-discovery"></a>Preparar uma conta para a descoberta automática

O Site Recovery precisa de acesso aos servidores VMware para:

- Descobrir as VMs automaticamente. Pelo menos uma conta somente leitura é necessária.
- Orquestrar a replicação, o failover e o failback. Você precisa de uma conta que possa executar operações como criar e remover discos e ativar VMs.

Crie a conta da seguinte maneira:

1. Para usar uma conta dedicada, crie uma função no nível do vCenter. Dê um nome à função como **Azure_Site_Recovery**.
2. Atribua à função as permissões resumidas na tabela a seguir.
3. Crie um usuário no vCenter Server. ou um host do vSphere. Atribua a função ao usuário.

### <a name="vmware-account-permissions"></a>Permissões de conta do VMware

**Tarefa** | **Função/Permissões** | **Detalhes**
--- | --- | ---
**Descoberta VM** | Pelo menos um usuário somente leitura<br/><br/> Objeto de data center –> Propagar para o objeto filho, função = somente leitura | Usuário atribuído no nível de datacenter e tem acesso a todos os objetos no datacenter.<br/><br/> Para restringir o acesso, atribua a função **Nenhum acesso** com o objeto **Propagar para filho** aos objetos filho (hosts vSphere, armazenamentos de dados, VMs e redes).
**Replicação, failover, failback totais** |  Crie uma função (Azure_Site_Recovery) com as permissões necessárias e, em seguida, atribua a função a um usuário ou grupo do VMware<br/><br/> Objeto de Data Center –> Propagar para o Objeto Filho, função = Azure_Site_Recovery<br/><br/> Armazenamento de dados -> alocar espaço, procurar armazenamento de dados, operações de arquivo de baixo nível, remover arquivo, atualizar arquivos de máquina virtual<br/><br/> Rede -> Atribuição de rede<br/><br/> Recurso -> Atribuir VM ao pool de recursos, migrar VM desligada, migrar VM ligada<br/><br/> Tarefas -> Criar tarefa, atualizar tarefa<br/><br/> Máquina virtual -> Configuração<br/><br/> Máquina virtual -> Interagir -> responder à pergunta, conexão de dispositivo, configurar mídia de CD, configurar mídia de disquete, desligar, ligar, instalação de ferramentas VMware<br/><br/> Máquina virtual -> Inventário -> Criar, registrar, cancelar registro<br/><br/> Máquina virtual -> Provisionamento -> Permitir download de máquina virtual, permitir upload de arquivos de máquina virtual<br/><br/> Máquina virtual -> Instantâneos -> Remover instantâneos | Usuário atribuído no nível de datacenter e tem acesso a todos os objetos no datacenter.<br/><br/> Para restringir o acesso, atribua a função **Nenhum acesso** com o objeto **Propagar para filho** aos objetos filho (hosts vSphere, armazenamentos de dados, VMs e redes).

## <a name="prepare-an-account-for-mobility-service-installation"></a>Preparar uma conta para a instalação do serviço de Mobilidade

O serviço de Mobilidade deve ser instalado na VM que você deseja replicar. O Site Recovery instala esse serviço automaticamente quando você habilita a replicação para a VM. Para instalação automática, você precisa preparar uma conta que o Site Recovery usará para acessar a VM. Você especificará esta conta quando configurar a recuperação de desastre no console do Azure.

1. Prepare um domínio ou uma conta local com permissões para instalar na VM.
2. Para instalar em VMs do Windows, se você não estiver usando uma conta de domínio, desabilite o controle de Acesso de Usuários Remoto no computador local.
   - No Registro, em **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**, adicione a entrada DWORD **LocalAccountTokenFilterPolicy**, com um valor igual 1.
3. Para instalar em VMs do Linux, prepare uma conta raiz no servidor Linux de origem.


## <a name="check-vmware-server-requirements"></a>Verificar os requisitos do servidor VMware

Verifique se os servidores VMware atendem aos seguintes requisitos.

**Componente** | **Requisito**
--- | ---
**vCenter Server** | vCenter 6.5, 6.0 ou 5.5
**Host do vSphere** | vSphere 6.5, 6.0, 5.5

## <a name="check-vmware-vm-requirements"></a>Verificar os requisitos de VM VMware

Verifique se a VM está em conformidade com os requisitos do Azure resumidos na tabela a seguir.

**Requisitos de VM** | **Detalhes**
--- | ---
**Tamanho de disco do sistema operacional** | Até 2048 GB.
**Contagem de discos do sistema operacional** | 1
**Contagem de discos de dados** | 64 ou menos
**Tamanho do VHD do disco de dados** | Até 4.095 GB
**Adaptadores de rede** | Há suporte para vários adaptadores
**VHD compartilhado** | Sem suporte
**Disco FC** | Sem suporte
**Formato de disco rígido** | VHD ou VHDX.<br/><br/> Apesar de atualmente o VHDX não ter suporte no Azure, a Recuperação de Site converterá automaticamente o VHDX em VHD ao realizar o failover para o Azure. Quando você executa failback para o local, as VMs continuam a usar o formato VHDX.
**Bitlocker** | Sem suporte. Desabilite antes de habilitar a replicação para uma VM.
**Nome da VM** | Entre 1 e 63 caracteres.<br/><br/> Restrito a letras, números e hifens. O nome da VM deve começar e terminar com uma letra ou um número.
**Tipo de VM** | Geração 1 – Linux ou Windows<br/><br/>Geração 2 – somente Windows

A VM também deve estar executando um sistema operacional com suporte. Consulte a [matriz de suporte do Site Recovery](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions) para obter uma lista completa de versões com suporte.

## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Preparar para conectar VMs do Azure após o failover

Durante um cenário de failover você poderá se conectar da sua rede local às suas VMs replicadas no Azure.

Para se conectar a VMs do Windows usando o RDP após o failover, faça o seguinte:

1. Para acessar a Internet, habilite o RDP na VM local antes do failover. Verifique se as regras de TCP e de UDP foram adicionadas no perfil **Público** e se o RDP é permitido no **Firewall do Windows** > **Aplicativos permitidos** para todos os perfis.
2. Para acessar por meio da VPN site a site, habilite o RDP no computador local. O RDP deve ser permitido no **Firewall do Windows** -> **Aplicativos e recursos permitidos** para redes de **Domínio e Privadas**.
   Verifique se a política de SAN do sistema operacional está definida como **OnlineAll**. [Saiba mais](https://support.microsoft.com/kb/3031135). Não deve haver nenhuma atualização pendente do Windows na VM quando você dispara um failover. Se houver, não será possível fazer logon na máquina virtual até que a atualização seja concluída.
3. Na VM do Microsoft Azure após o failover, verifique o **Diagnóstico de inicialização** para exibir uma captura de tela da VM. Se você não puder se conectar, verifique se a VM está em execução e examine estas [dicas de solução de problemas](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

Para conectar-se a VMs do Linux usando SSH após o failover, faça o seguinte:

1. No computador local antes do failover, verifique se o serviço Secure Shell está definido para iniciar automaticamente na inicialização do sistema. Verifique se as regras de firewall permitem uma conexão SSH.

2. Na VM do Azure após o failover, permita as conexões de entrada com a porta SSH para as regras do grupo de segurança de rede na VM com failover e para a sub-rede do Azure à qual ela está conectada.
   [Adicione um endereço IP público](site-recovery-monitoring-and-troubleshooting.md#adding-a-public-ip-on-a-resource-manager-virtual-machine) para a VM. Você pode verificar o **Diagnóstico de inicialização** para exibir uma captura de tela da VM.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Configurar a recuperação de desastre de VMs VMware para o Azure](tutorial-vmware-to-azure.md)
