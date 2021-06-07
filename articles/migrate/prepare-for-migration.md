---
title: Preparar computadores para migração com as Migrações para Azure
description: Saiba como preparar computadores locais para migração com as Migrações para Azure.
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: how-to
ms.date: 06/08/2020
ms.openlocfilehash: d8f9d4e0b002348f286f45c6b45c96531c5d6530
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105558220"
---
# <a name="prepare-on-premises-machines-for-migration-to-azure"></a>Preparar computadores locais para migração para Azure

Este artigo descreve como preparar computadores locais antes de começar a migrá-los para o Azure usando a ferramenta [Migrações para Azure: Migração de Servidor](migrate-services-overview.md#azure-migrate-server-migration-tool).

Neste artigo você:
> [!div class="checklist"]
> * Examinará limitações de migração.
> * Selecionará um método para migrar VMs VMware
> * Verifique os requisitos do hipervisor e do sistema operacional para computadores que você deseja migrar.
> * Examinará o acesso a URL e à porta dos computadores que deseja migrar.
> * Examinará alterações que você pode precisar fazer antes de começar a migração.
> * Verificar os requisitos de VMs do Azure para computadores migrados
> * Vai preparar os computadores, de modo que você possa se conectar às VMs do Azure após a migração.



## <a name="verify-migration-limitations"></a>Verificará limitações de migração

A tabela resume os limites de descoberta, avaliação e migração para as Migrações para Azure. Recomendamos que você avalie os computadores antes da migração, mas isso não é necessário.

**Cenário** | **Projeto** | **Descoberta/Avaliação** | **Migração**
--- | --- | --- | ---
**VMs VMware** | Descubra e avalie até 35.000 VMs do VMware em um único projeto das Migrações para Azure. | Descubra até 10 mil VMs VMware com um único [dispositivo de Migrações para Azure](common-questions-appliance.md) para VMware. | **Migração sem agente**: você pode replicar simultaneamente no máximo 500 VMs de cada vCenter Server. **Migração baseada em agente**: você pode [escalar horizontalmente](./agent-based-migration-architecture.md#performance-and-scaling) o [dispositivo de replicação](migrate-replication-appliance.md) para replicar grandes números de VMs.<br/><br/> No portal, você pode selecionar até 10 computadores ao mesmo tempo para replicação. Para replicar mais computadores, adicione lotes de 10.
**VMs Hyper-V** | Descubra e avalie até 35.000 VMs do VMware em um único projeto das Migrações para Azure. | Descobrir até 5.000 VMs Hyper-V com um único dispositivo de Migrações para Azure | Um dispositivo não é usado para a migração do Hyper-V. Em vez disso, o Provedor de Replicação do Hyper-V é executado em cada host Hyper-V.<br/><br/> A capacidade de replicação é influenciada por fatores de desempenho, como rotatividade de VM e largura de banda de upload para dados de replicação.<br/><br/> No portal, você pode selecionar até 10 computadores ao mesmo tempo para replicação. Para replicar mais computadores, adicione lotes de 10.
**Computadores físicos** | Descubra e avalie até 35.000 computadores do VMware em um único projeto das Migrações para Azure. | Descubra até 250 servidores físicos com um único dispositivo de Migrações para Azure para servidores físicos. | Você pode [escalar horizontalmente](./agent-based-migration-architecture.md#performance-and-scaling) o [dispositivo de replicação](migrate-replication-appliance.md) para replicar grandes números de servidores.<br/><br/> No portal, você pode selecionar até 10 computadores ao mesmo tempo para replicação. Para replicar mais computadores, adicione lotes de 10.

## <a name="select-a-vmware-migration-method"></a>Selecione um método de migração do VMware

Se você estiver migrando VMs VMware para o Azure, [compare](server-migrate-overview.md#compare-migration-methods) os métodos de migração baseados em agente e sem agente para decidir qual funciona para você.

## <a name="verify-hypervisor-requirements"></a>Verificar os requisitos do hipervisor

- Verifique os requisitos da migração [sem agente do VMware](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless) ou [baseada em agente do VMware](migrate-support-matrix-vmware-migration.md#vmware-requirements-agent-based).
- Verificar os requisitos do [host Hyper-V](migrate-support-matrix-hyper-v-migration.md#hyper-v-host-requirements).


## <a name="verify-operating-system-requirements"></a>Verificar requisitos do sistema operacional

Verificar os sistemas operacionais compatíveis para migração:

- Se você estiver migrando VMs VMware ou VMs Hyper-V, verifique os requisitos de VM VMware para a migração [sem agente](migrate-support-matrix-vmware-migration.md#vm-requirements-agentless) e [baseada em agente](migrate-support-matrix-vmware-migration.md#vm-requirements-agent-based) e os requisitos para [VMs Hyper-V](migrate-support-matrix-hyper-v-migration.md#hyper-v-vms).
- Verifique se os [sistemas operacionais Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) são compatíveis com o Azure.
- Verifique se as [distribuições Linux](../virtual-machines/linux/endorsed-distros.md) são compatíveis com o Azure.

## <a name="review-url-and-port-access"></a>Examinar o acesso a URL e à porta

Examine quais URLs e portas são acessadas durante a migração.

**Cenário** | **Detalhes** |  **URLs** | **Portas**
--- | --- | --- | ---
**Migração sem agente do VMware** | Usa o [dispositivo de Migrações para Azure](migrate-appliance-architecture.md) para migração. Nada é instalado em VMs VMware. | Examine [URLs](migrate-appliance.md#url-access) de nuvem pública e do governo necessárias para descoberta, avaliação e migração com o dispositivo. | [Examine](migrate-support-matrix-vmware-migration.md#port-requirements-agentless) os requisitos de porta para migração sem agente.
**Migração baseada em agente do VMware** | Usa o [dispositivo de replicação](migrate-replication-appliance.md) para migração. O agente do Serviço de Mobilidade é instalado em VMs. | Examine as URLs de [nuvem pública](migrate-replication-appliance.md#url-access) e do [Azure Government](migrate-replication-appliance.md#azure-government-url-access) que o dispositivo de replicação precisa acessar. | [Examine](migrate-replication-appliance.md#port-access) as portas usadas durante a migração baseada em agente.
**Migração do Hyper-V** | Usa um provedor instalado em hosts Hyper-V para migração. Nada é instalado em VMs Hyper-V. | Examine as URLs de [nuvem pública](migrate-support-matrix-hyper-v-migration.md#url-access-public-cloud) e do [Azure Government](migrate-support-matrix-hyper-v-migration.md#url-access-azure-government) que o Provedor de Replicação em execução nos hosts precisa acessar. | O Provedor de Replicação no host Hyper-V usa conexões de saída na porta HTTPS 443 para enviar dados de replicação de VM.
**Computadores físicos** | Usa o [dispositivo de replicação](migrate-replication-appliance.md) para migração. O agente do Serviço de Mobilidade é instalado nos computadores físicos. | Examine as URLs de [nuvem pública](migrate-replication-appliance.md#url-access) e do [Azure Government](migrate-replication-appliance.md#azure-government-url-access) que o dispositivo de replicação precisa acessar. | [Examine](migrate-replication-appliance.md#port-access) as portas usadas durante a migração física.

## <a name="verify-required-changes-before-migrating"></a>Verificar as alterações necessárias antes da migração

Há algumas alterações necessárias nas VMs antes de migrá-las para o Azure.

- Para alguns sistemas operacionais, as Migrações para Azure fazem alterações automaticamente durante o processo de replicação/migração.
- Para outros sistemas operacionais, você precisa definir as configurações manualmente.
- É importante definir as configurações manualmente antes de começar a migração. Se você migrar a VM antes de fazer a alteração, ela não poderá ser inicializada no Azure.

Examine as tabelas para identificar as alterações que você precisa fazer.

### <a name="windows-machines"></a>Máquinas do Windows

As alterações necessárias estão resumidas na tabela.

**Ação** | **VMware (migração sem agente)** | **VMware (migração baseada em agente)/computadores físicos** | **Windows no Hyper-V** 
--- | --- | --- | ---
**Configurar a política de SAN como Todos Online**<br/><br/> Isso verifica se os volumes do Windows na VM do Azure usam as mesmas atribuições de letra da unidade que a VM local. | Defina automaticamente para computadores que executam o Windows Server 2008 R2 ou posterior.<br/><br/> Configure manualmente para sistemas operacionais anteriores. | Defina automaticamente na maioria dos casos. | Configure manualmente.
**Instalar a Integração Convidada do Hyper-V** | [Instale manualmente](prepare-windows-server-2003-migration.md#install-on-vmware-vms) em computadores que executam o Windows Server 2003. | [Instale manualmente](prepare-windows-server-2003-migration.md#install-on-vmware-vms) em computadores que executam o Windows Server 2003. | [Instale manualmente](prepare-windows-server-2003-migration.md#install-on-hyper-v-vms) em computadores que executam o Windows Server 2003.
**Habilite o Console Serial do Azure**.<br/><br/>[Habilite o console](/troubleshoot/azure/virtual-machines/serial-console-windows) em VMs do Azure para ajudar na solução de problemas. Não é necessário reinicializar a VM. A VM do Azure será inicializada com a imagem de disco. A inicialização da imagem de disco é equivalente a uma reinicialização para a nova VM. | Habilitar manualmente | Habilitar manualmente | Habilitar manualmente
**Conectar-se após a migração**<br/><br/> Para se conectar após a migração, há várias etapas a serem seguidas antes da migração. | [Instalar](#prepare-to-connect-to-azure-windows-vms) manualmente. | [Instalar](#prepare-to-connect-to-azure-windows-vms) manualmente. | [Instalar](#prepare-to-connect-to-azure-windows-vms) manualmente.


#### <a name="configure-san-policy"></a>Configurar a política SAN

Por padrão, é atribuída a unidade D às VMs do Azure para uso como armazenamento temporário.

- Essa atribuição de unidades faz todas as outras atribuições de unidade de armazenamento anexadas serem incrementadas em uma letra.
- Por exemplo, se a instalação local usar um disco de dados atribuído à unidade D para instalações de aplicativos, a atribuição dessa unidade será incrementada para a unidade E após você migrar a VM para o Azure. 
- Para evitar essa atribuição automática e para garantir que o Azure atribua a próxima letra da unidade livre ao volume temporário, defina a política de SAN (rede de área de armazenamento) como **OnlineAll**:

Defina essa configuração manualmente da seguinte maneira:

1. No computador local (não no servidor host), abra um prompt de comandos com privilégios elevados.
2. Insira **diskpart**.
3. Insira **SAN**. Se a letra da unidade do sistema operacional convidado não for mantida, **Todos Offline** ou **Compartilhados Offline** será retornado.
4. No prompt **DISKPART**, insira **SAN Policy=OnlineAll**. Essa configuração garante que os discos sejam colocados online e que você possa fazer leituras e gravações nos dois discos.
5. Durante essa migração de teste, você poderá verificar se as letras da unidade foram preservadas.


### <a name="linux-machines"></a>Máquinas do Linux

As Migrações para Azure concluem essas ações automaticamente para estas versões

- Red Hat Enterprise Linux 6.x, 7.0, 7.4, 7.5, 7.6, 7.7 e 7.8 (o agente de VM do Linux do Azure também será instalado de modo automático durante a migração)
- CentOS 6.x, 7.4, 7.5, 7.6 e 7.7 (o agente de VM do Linux do Azure também será instalado de modo automático durante a migração)
- SUSE Linux Enterprise Server 12 SP1+
- SUSE Linux Enterprise Server 15 SP1
- Ubuntu 14.04 LTS, 16.04LTS, 18.04LTS, 19.04 e 19.10 (o agente de VM do Linux do Azure também será instalado de modo automático durante a migração)
- Ubuntu 16.04LTS e 18.04LTS
- Debian 9, 8, 7
- Oracle Linux 7.7, 7.7-CI

Para outras versões, prepare os computadores conforme resumido na tabela.  


**Ação** | **Detalhes** | **Versão do Linux**
--- | --- | ---
**Instalar o Integration Services do Linux para Hyper-V** | Recompile a imagem de inicialização do Linux, de modo que ela contenha os drivers necessários do Hyper-V. A recompilação da imagem de inicialização verifica se a VM será inicializada no Azure. | A maioria das novas versões das distribuições do Linux tem isso incluído por padrão.<br/><br/> Se não estiver incluído, instale manualmente para todas as versões, exceto aquelas informadas acima.
**Habilitar o log do Console Serial do Azure** | A habilitação do log do console ajuda a solucionar problemas. Não é necessário reinicializar a VM. A VM do Azure será inicializada com a imagem de disco. A inicialização da imagem de disco é equivalente a uma reinicialização para a nova VM.<br/><br/> Siga [estas instruções](/troubleshoot/azure/virtual-machines/serial-console-linux) para habilitar.
**Atualizar arquivo do mapa do dispositivo** | Atualize o arquivo do mapa do dispositivo com as associações nome do dispositivo/volume, a fim de usar identificadores de dispositivo persistentes. | Instale manualmente para todas as versões, exceto aquelas informadas acima. (Aplicável somente em cenário do VMware baseado em agente)
**Atualizar entradas fstab** |  Atualize entradas para usar identificadores de volume persistentes.    | Atualize manualmente para todas as versões, exceto aquelas informadas acima.
**Remover regra udev** | Remova as regras udev que reservam os nomes de interface em endereços MAC etc. | Remova manualmente para todas as versões, exceto aquelas informadas acima.
**Atualizar adaptadores de rede** | Atualizar os adaptadores de rede para receber endereço IP baseado em DHCP.nst | Atualize manualmente para todas as versões, exceto aquelas informadas acima.
**Habilitar ssh** | Verifique se o ssh está habilitado e se o serviço sshd foi definido para ser iniciado automaticamente na reinicialização.<br/><br/> Verifique se as solicitações de conexão ssh de entrada não estão bloqueadas pelo firewall do sistema operacional ou por regras programáveis.| Habilite manualmente para todas as versões, exceto aquelas informadas acima.

A tabela a seguir resume as etapas executadas automaticamente para os sistemas operacionais listados acima.


| Ação                                      | Migração de VMware baseada em\-agente | Migração de VMware sem agente | Hyper\-V   |
|---------------------------------------------|-------------------------------|----------------------------|------------|
| Instalar o Hyper\-V Linux Integration Services | Sim                           | Sim                        | Não é necessário |
| Habilitar o registro em log do console serial do Azure         | Sim                           | Sim                        | Não         |
| Atualizar arquivo do mapa do dispositivo                      | Sim                           | Não                         | Não         |
| Atualizar entradas fstab                        | Sim                           | Sim                        | Não         |
| Remover regra udev                            | Sim                           | Sim                        | Não         |
| Atualizar adaptadores de rede                   | Sim                           | Sim                        | Não         |
| Habilitar ssh                                  | Não                            | Não                         | Não         |

Saiba mais sobre as etapas para [executar uma VM do Linux no Azure](../virtual-machines/linux/create-upload-generic.md) e obtenha instruções para algumas distribuições populares do Linux.

Examine a lista de [pacotes necessários](../virtual-machines/extensions/agent-linux.md#requirements) para instalar o agente de VM do Linux. As Migrações para Azure instalarão o agente de VM do Linux de modo automático para versões que incluem: RHEL6, RHEL7, CentOS7 (a versão 6 deverá ter suporte semelhante ao RHEL), Ubuntu 14.04, Ubuntu 16.04 e Ubuntu 18.04 ao usar um método sem agente de migração do VMware.

## <a name="check-azure-vm-requirements"></a>Verificar os requisitos de VM do Azure

Os computadores locais replicados no Azure precisam atender aos requisitos da VM do Azure de sistema operacional e arquitetura, discos, configurações de rede e nomenclatura da VM.

Antes de migrar, examine os requisitos de VMs do Azure para migração do [VMware](migrate-support-matrix-vmware-migration.md#azure-vm-requirements), do [Hyper-V](migrate-support-matrix-hyper-v-migration.md#azure-vm-requirements) e de [servidor físico](migrate-support-matrix-physical-migration.md#azure-vm-requirements).



## <a name="prepare-to-connect-after-migration"></a>Prepare-se para se conectar após a migração

As VMs do Azure são criadas durante a migração para o Azure. Após a migração, você precisará conseguir se conectar às novas VMs do Azure. Várias etapas são necessárias para uma conexão bem-sucedida.

### <a name="prepare-to-connect-to-azure-windows-vms"></a>Preparar a conexão com as VMs do Azure para Windows

Em computadores Windows locais:

1. Defina as configurações do Windows. As configurações incluem a remoção de rotas persistentes estáticas ou do proxy WinHTTP.
2. Verifique se todos os [serviços necessários](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services) estão em execução.
3. Habilite a RDP (Área de Trabalho Remota) para permitir conexões remotas com o computador local. Saiba como [usar o PowerShell para habilitar o RDP](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings).
4. Para acessar uma VM do Azure pela Internet após a migração, no Firewall do Windows, no computador local, dê permissão ao TCP e UDP no Perfil público e defina RDP como um aplicativo com permissão para todos os perfis.
5. Se desejar acessar uma VM do Azure por um VPN site a site após a migração, no Firewall do Windows no computador local, dê permissão ao RDP para os perfis Domínio e Privado. Saiba como [permitir o tráfego RDP](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules). 
6. Verifique se não há nenhuma atualização do Windows pendente na VM local quando você fizer a migração. Se houver, a instalação das atualizações poderá ser iniciada na VM do Azure após a migração e você não poderá entrar na VM até a conclusão das atualizações.


### <a name="prepare-to-connect-with-linux-azure-vms"></a>Preparar-se para se conectar às VMs do Azure para Linux

Em computadores Linux locais:

1. Verifique se o serviço Secure Shell está definido para iniciar automaticamente na inicialização do sistema.
2. Verifique se as regras de firewall permitem uma conexão SSH.

### <a name="configure-azure-vms-after-migration"></a>Configurar VMs do Azure após a migração

Após a migração, conclua estas etapas nas VMs do Azure criadas:

1. Para se conectar à VM pela Internet, atribua um endereço IP público à VM. É necessário usar um endereço IP público diferente para a VM do Azure daquele que usou para o computador local. [Saiba mais](../virtual-network/virtual-network-public-ip-address.md).
2. Verifique se as regras do NSG (grupo de segurança de rede) na VM permitem conexões de entrada à porta RDP ou SSH.
3. Confira [Diagnóstico de inicialização](/troubleshoot/azure/virtual-machines/boot-diagnostics#enable-boot-diagnostics-on-existing-virtual-machine) para visualizar a VM.


## <a name="next-steps"></a>Próximas etapas

Decida qual método você deseja usar para [migrar as VMs do VMware](server-migrate-overview.md) para o Azure ou comece a migrar [VMs do Hyper-V](tutorial-migrate-hyper-v.md) ou [servidores físicos ou VMs virtualizadas ou de nuvem](tutorial-migrate-physical-virtual-machines.md).

## <a name="see-whats-supported"></a>Veja o que é compatível

Para VMs do VMware, a Migração de Servidor dá suporte à [migração sem agente ou baseada em agente](server-migrate-overview.md).

- **VMs do VMware**: verifique [os requisitos de migração e o suporte](migrate-support-matrix-vmware-migration.md) para as VMs do VMware.
- **VMs do Hyper-V**: Verifique os [requisitos de migração e suporte](migrate-support-matrix-hyper-v-migration.md) para VMs Hyper-V.
- **Computadores físicos**: verifique [os requisitos de migração e o suporte](migrate-support-matrix-physical-migration.md) para computadores físicos locais e outros servidores virtualizados.