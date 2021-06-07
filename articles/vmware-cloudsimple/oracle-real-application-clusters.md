---
title: Solução do Azure VMware por CloudSimple-Otimize sua nuvem privada do CloudSimple para Oracle RAC
description: Descreve como implantar um novo cluster e otimizar uma VM para a instalação e a configuração do RAC (Oracle Real Application Clusters)
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3959aae5f490af10c6747cfa67d9960e0c4a203f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97899262"
---
# <a name="optimize-your-cloudsimple-private-cloud-for-installing-oracle-rac"></a>Otimize sua nuvem privada do CloudSimple para instalar o Oracle RAC

Você pode implantar RAC (clusters de aplicativos reais) da Oracle em seu ambiente de nuvem privada do CloudSimple. Este guia descreve como implantar um novo cluster e otimizar uma VM para a solução Oracle RAC. Depois de concluir as etapas neste tópico, você pode instalar e configurar o Oracle RAC.

## <a name="storage-policy"></a>Política de armazenamento

A implementação bem-sucedida do Oracle RAC requer um número adequado de nós no cluster.  Na política de armazenamento vSAN, as falhas de tolerar (FTT) são aplicadas aos discos de dados usados para armazenar os discos de log, logs e refazer.  O número necessário de nós para tolerar falhas com eficiência é 2N + 1, em que N é o valor de FTT.

Exemplo: se o FTT desejado for 2, o número total de nós no cluster deverá ser 2 * 2 + 1 = 5.

## <a name="overview-of-deployment"></a>Visão geral da implantação

As seções a seguir descrevem como configurar seu ambiente de nuvem privada do CloudSimple para o Oracle RAC.

1. Práticas recomendadas para configuração de disco
2. Implantar o cluster vSphere de nuvem privada CloudSimple
3. Configurar a rede para o Oracle RAC
4. Configurar políticas de armazenamento vSAN
5. Criar VMs Oracle e criar discos de VM compartilhados
6. Configurar as regras de afinidade de VM para host

## <a name="best-practices-for-disk-configuration"></a>Práticas recomendadas para configuração de disco

As máquinas virtuais do Oracle RAC têm vários discos, que são usados para funções específicas.  Os discos compartilhados são montados em todas as máquinas virtuais, que são usadas pelo cluster Oracle RAC.  Os discos de instalação de software e sistema operacional são montados somente nas máquinas virtuais individuais.  

![Visão geral dos discos de máquina virtual Oracle RAC](media/oracle-vm-disks-overview.png)

O exemplo a seguir usa os discos definidos na tabela a seguir.

| Disco                                      | Finalidade                                       | Disco compartilhado |
|-------------------------------------------|-----------------------------------------------|-------------|
| Sistema operacional                                        | Disco do sistema operacional                         | Não          |
| GRADE                                      | Local de instalação do software de grade da Oracle     | Não          |
| DATABASE                                  | Local de instalação do software de banco de dados Oracle | Não          |
| ORAHOME                                   | Local base para binários do banco de dados Oracle    | Não          |
| DATA1, DATA2, DATA3, DATA4                | Disco em que os arquivos de banco de dados Oracle estão armazenados   | Sim         |
| REDO1, REDO2, REDO3, REDO4, REDO5, REDO6  | Refazer discos de log                                | Sim         |
| OCR1, OCR2, OCR3, OCR4, OCR5              | Discos de votação                                  | Sim         |
| FRA1, FRA2                                | Discos da área de recuperação rápida                      | Sim         |

![Configuração de disco da máquina virtual Oracle](media/oracle-vmdk.png)

### <a name="virtual-machine-configuration"></a>Configuração de máquina virtual

* Cada máquina virtual é configurada com quatro controladores SCSI.
* O tipo de controlador SCSI está definido como VMware paravirtual.
* Vários discos virtuais (. vmdk) são criados.
* Os discos são montados em controladores SCSI diferentes.
* O tipo de compartilhamento de vários gravadores está definido para discos de cluster compartilhados.
* a política de armazenamento vSAN é definida para garantir a alta disponibilidade de discos.

### <a name="operating-system-and-software-disk-configuration"></a>Configuração de disco de software e sistema operacional

Cada máquina virtual Oracle é configurada com vários discos para o sistema operacional do host, troca, instalação de software e outras funções de sistema operacional.  Esses discos não são compartilhados entre as máquinas virtuais.  

* Três discos para cada máquina virtual são configurados como discos virtuais e montados em máquinas virtuais do Oracle RAC.
    * Disco do SO
    * Disco para armazenar arquivos de instalação de grade do Oracle
    * Disco para armazenar arquivos de instalação do banco de dados Oracle
* Os discos podem ser configurados como **provisionados com provisionamento dinâmico**.
* Cada disco é montado no primeiro controlador SCSI (SCSI0).  
* O compartilhamento está definido como **sem compartilhamento**.
* A redundância é definida no armazenamento usando as políticas de vSAN.  

![Diagrama que mostra a configuração física do disco do sistema operacional Oracle RAC.](media/oracle-vm-os-disks.png)

### <a name="data-disk-configuration"></a>Configuração de disco de dados

Os discos de dados são usados principalmente para armazenar arquivos de banco de dado.  

* Quatro discos são configurados como discos virtuais e montados em todas as máquinas virtuais do Oracle RAC.
* Cada disco é montado em um controlador SCSI diferente.
* Cada disco virtual é configurado como **provisionamento espesso ansiosos por zero**.  
* O compartilhamento é definido como **multi-Writer**.  
* Os discos devem ser configurados como um grupo de discos de gerenciamento de armazenamento automático (ASM).  
* A redundância é definida no armazenamento usando as políticas de vSAN.  
* A redundância do ASM é definida como redundância **externa** .

![Configuração do grupo de discos de dados do Oracle RAC](media/oracle-vm-data-disks.png)

### <a name="redo-log-disk-configuration"></a>Refazer configuração do disco de log

Os arquivos de log redo são usados para armazenar uma cópia das alterações feitas no banco de dados.  Os arquivos de log são usados quando os dados precisam ser recuperados após qualquer falha.

* Os discos de log redo devem ser configurados como vários grupos de discos.  
* Seis discos são criados e montados em todas as máquinas virtuais do Oracle RAC.
* Os discos são montados em controladores SCSI diferentes
* Cada disco virtual é configurado como **provisionamento espesso ansiosos por zero**.
* O compartilhamento é definido como **multi-Writer**.  
* Os discos devem ser configurados como dois grupos de discos ASM.
* Cada grupo de discos ASM contém três discos, que estão em controladores SCSI diferentes.  
* A redundância do ASM é definida como redundância **normal** .
* Cinco arquivos de log de restauração são criados no grupo de logs de restauração do ASM

```
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
```

![Configuração de grupo de disco de log de restauração do Oracle RAC](media/oracle-vm-redo-log-disks.png)

### <a name="oracle-voting-disk-configuration"></a>Configuração de disco de votação Oracle

Os discos de votação fornecem a funcionalidade de disco de quorum como um canal de comunicação adicional para evitar qualquer situação de divisão-Brain.

* Cinco discos são criados e montados em todas as máquinas virtuais do Oracle RAC.
* Os discos são montados em um controlador SCSI
* Cada disco virtual é configurado como **provisionamento espesso ansiosos por zero**.
* O compartilhamento é definido como **multi-Writer**.  
* Os discos devem ser configurados como um grupo de discos ASM.  
* A redundância do ASM é definida como **alta** redundância.

![Configuração do grupo de discos de votação do Oracle RAC](media/oracle-vm-voting-disks.png)

### <a name="oracle-fast-recovery-area-disk-configuration-optional"></a>Configuração de disco da área de recuperação rápida da Oracle (opcional)

A FRA (área de recuperação rápida) é o sistema de arquivos gerenciado pelo grupo de discos do Oracle ASM.  FRA fornece um local de armazenamento compartilhado para backup e arquivos de recuperação. A Oracle cria logs arquivados e os logs do flashback na área de recuperação rápida. O RMAN (Oracle Recovery Manager) pode, opcionalmente, armazenar seus conjuntos de backup e cópias de imagem na área de recuperação rápida e o utiliza ao restaurar arquivos durante a recuperação de mídia.

* Dois discos são criados e montados em todas as máquinas virtuais do Oracle RAC.
* Os discos são montados em um controlador SCSI diferente
* Cada disco virtual é configurado como **provisionamento espesso ansiosos por zero**.
* O compartilhamento é definido como **multi-Writer**.  
* Os discos devem ser configurados como um grupo de discos ASM.  
* A redundância do ASM é definida como redundância **externa** .

![Diagrama que mostra a configuração do grupo de discos de votação do Oracle RAC.](media/oracle-vm-fra-disks.png)

## <a name="deploy-cloudsimple-private-cloud-vsphere-cluster"></a>Implantar o cluster vSphere de nuvem privada CloudSimple

Para implantar um cluster vSphere em sua nuvem privada, siga este processo:

1. No portal do CloudSimple, [crie uma nuvem privada](create-private-cloud.md). CloudSimple cria um usuário vCenter padrão chamado ' cloudowner ' na nuvem privada recém-criada. Para obter detalhes sobre o usuário de nuvem privada padrão e o modelo de permissão, consulte [saiba mais sobre o modelo de permissão de nuvem privada](learn-private-cloud-permissions.md).  Esta etapa cria o cluster de gerenciamento primário para sua nuvem privada.

2. No portal do CloudSimple, [expanda a nuvem privada](expand-private-cloud.md) com um novo cluster.  Este cluster será usado para implantar o Oracle RAC.  Selecione o número de nós com base na tolerância a falhas desejada (mínimo de três nós).

## <a name="set-up-networking-for-oracle-rac"></a>Configurar a rede para o Oracle RAC

1. Em sua nuvem privada, [crie duas VLANs](create-vlan-subnet.md), uma para a rede pública da Oracle e outra para a rede privada da Oracle e atribua cidrs de sub-rede apropriados.
2. Depois que as VLANs forem criadas, crie os [grupos de portas distribuídas no vCenter da nuvem privada](create-vlan-subnet.md#use-vlan-information-to-set-up-a-distributed-port-group-in-vsphere).
3. Configure uma [máquina virtual DHCP e servidor DNS](dns-dhcp-setup.md) no seu cluster de gerenciamento para o ambiente Oracle.
4. [Configure o encaminhamento de DNS no servidor DNS](on-premises-dns-setup.md#create-a-conditional-forwarder) instalado na nuvem privada.

## <a name="set-up-vsan-storage-policies"></a>Configurar políticas de armazenamento do vSAN

as políticas vSAN definem as falhas a serem toleradas e a distribuição de disco para os dados armazenados nos discos de VM.  A política de armazenamento criada deve ser aplicada nos discos de VM durante a criação da VM.

1. [Entre no cliente vSphere](./vcenter-access.md) da sua nuvem privada.
2. No menu superior, selecione **políticas e perfis**.
3. No menu à esquerda, selecione **políticas de armazenamento de VM** e, em seguida, selecione **criar uma política de armazenamento de VM**.
4. Insira um nome significativo para a política e clique em **Avançar**.
5. Na seção **estrutura de política** , selecione **habilitar regras para o armazenamento vSAN** e clique em **Avançar**.
6. Na seção   >  **disponibilidade** vSAN, selecione **nenhum** para tolerância a desastres do site. Para falhas a tolerar, selecione a opção de **espelhamento de RAID** para o FTT desejado.
    ![configurações de vSAN ](media/oracle-rac-storage-wizard-vsan.png) .
7. Na seção **avançado** , selecione o número de faixas de disco por objeto. Para reserva de espaço de objeto, selecione **espesso provisionado**. Selecione **desabilitar soma de verificação de objeto**. Clique em **AVANÇAR**.
8. Siga as instruções na tela para exibir a lista de repositórios de armazenamento vSAN compatíveis, examine as configurações e conclua a instalação.

## <a name="create-oracle-vms-and-create-shared-vm-disks-for-oracle"></a>Criar VMs Oracle e criar discos de VM compartilhados para Oracle

Para criar uma VM para Oracle, clone uma VM existente ou crie uma nova.  Esta seção descreve como criar uma nova VM e, em seguida, cloná-la para criar uma segunda depois de instalar o sistema operacional base.  Depois que as VMs forem criadas, você poderá criar um adicionar discos a elas.  O cluster Oracle usa discos compartilhados para armazenamento, dados, logs e logs de refazer.

### <a name="create-vms"></a>Criar VMs

1. No vCenter, clique no ícone **hosts e clusters** . Selecione o cluster que você criou para Oracle.
2. Clique com o botão direito do mouse no cluster e selecione **nova máquina virtual**.
3. Selecione **criar nova máquina virtual** e clique em **Avançar**.
4. Nomeie o computador, selecione o local da VM Oracle e clique em **Avançar**.
5. Selecione o recurso de cluster e clique em **Avançar**.
6. Selecione o repositório de armazenamento vSAN para o cluster e clique em **Avançar**.
7. Mantenha a seleção de compatibilidade do ESXi 6,5 padrão e clique em **Avançar**.
8. Selecione o sistema operacional convidado do ISO para a VM que você está criando e clique em **Avançar**.
9. Selecione o tamanho do disco rígido necessário para instalar o sistema operacional.
10. Para instalar o aplicativo em um dispositivo diferente, clique em **Adicionar novo dispositivo**.
11. Selecione opções de rede e atribua o grupo de portas distribuídas criado para a rede pública.
12. Para adicionar mais interfaces de rede, clique em **Adicionar novo dispositivo** e selecione o grupo de portas distribuídas criado para a rede privada.
13. Para nova unidade de DC/DVD, selecione o arquivo ISO do repositório de armazenamento que contém o ISO para a instalação do sistema operacional preferencial. Selecione o arquivo que você carregou anteriormente para a pasta ISOs e modelos e clique em **OK**.
14. Examine as configurações e clique em **OK** para criar a nova VM.
15. Ligue a VM. Instalar o sistema operacional e todas as atualizações necessárias

Após a instalação do sistema operacional, você pode clonar uma segunda VM. Clique com o botão direito do mouse na entrada VM e selecione a opção e clonagem.

### <a name="create-shared-disks-for-vms"></a>Criar discos compartilhados para VMs

O Oracle usa o disco compartilhado para armazenar os arquivos de log de dados, log e refazer.  Você pode criar um disco compartilhado no vCenter e montá-lo em ambas as VMs.  Para obter um melhor desempenho, coloque os discos de dados em diferentes etapas de controladores SCSI abaixo mostram como criar um disco compartilhado no vCenter e, em seguida, anexá-lo a uma máquina virtual. o cliente do vCenter flash é usado para modificar as propriedades da VM.

#### <a name="create-disks-on-the-first-vm"></a>Criar discos na primeira VM

1. No vCenter, clique com o botão direito do mouse em uma das VMs Oracle e selecione **Editar configurações**.
2. Na seção novo dispositivo, selecione **controlador SCSI** e clique em **Adicionar**.
3. Na seção novo dispositivo, selecione **novo disco rígido** e clique em **Adicionar**.
4. Expanda as propriedades do novo disco rígido.
5. Especifique o tamanho do disco rígido.
6. Especifique a política de armazenamento da VM para ser a política de armazenamento do vSAN que você definiu anteriormente.
7. Selecione o local como uma pasta no repositório de armazenamento vSAN. O local ajuda a navegar e anexar os discos a uma segunda VM.
8. Para provisionamento de disco, selecione **provisionamento espesso adiantado para zero**.
9. Para compartilhamento, especifique **multi-Writer**.
10. Para o nó do dispositivo virtual, selecione o novo controlador SCSI que foi criado na etapa 2.

    ![Captura de tela que realça os campos necessários para criar discos na primeira VM.](media/oracle-rac-new-hard-disk.png)

Repita as etapas 2 a 10 para todos os novos discos necessários para os dados, logs e arquivos de log de restauração do Oracle.

#### <a name="attach-disks-to-second-vm"></a>Anexar discos à segunda VM

1. No vCenter, clique com o botão direito do mouse em uma das VMs Oracle e selecione **Editar configurações**.
2. Na seção novo dispositivo, selecione **controlador SCSI** e clique em **Adicionar**.
3. Na seção novo dispositivo, selecione **disco rígido existente** e clique em **Adicionar**.
4. Navegue até o local onde o disco foi criado para a primeira VM e selecione o arquivo VMDK.
5. Especifique a política de armazenamento da VM para ser a política de armazenamento do vSAN que você definiu anteriormente.
6. Para provisionamento de disco, selecione **provisionamento espesso adiantado para zero**.
7. Para compartilhamento, especifique **multi-Writer**.
8. Para o nó do dispositivo virtual, selecione o novo controlador SCSI que foi criado na etapa 2.

    ![Criar discos na primeira VM](media/oracle-rac-existing-hard-disk.png)

Repita as etapas 2 a 7 para todos os novos discos necessários para os dados, logs e arquivos de log de restauração do Oracle.

## <a name="set-up-vm-host-affinity-rules"></a>Configurar regras de afinidade de host de VM

As regras de afinidade de VM para host garantem que a VM seja executada no host desejado.  Você pode definir regras no vCenter para garantir que a VM Oracle seja executada no host com recursos adequados e atenda a quaisquer requisitos de licenciamento específicos.

1. No portal do CloudSimple, [Escale os privilégios](escalate-private-cloud-privileges.md) do usuário cloudowner.
2. Faça logon no cliente vSphere da sua nuvem privada.
3. No cliente vSphere, selecione o cluster em que as VMs Oracle são implantadas e clique em **Configurar**.
4. Em configurar, selecione **grupos de host/VM**.
5. Clique em **+** .
6. Adicione um grupo de VMs. Selecione **grupo de VMs** como o tipo. Insira o nome do grupo. Selecione as VMs e clique em **OK** para criar o grupo.
6. Adicione um grupo de hosts. Selecione **grupo de hosts** como o tipo. Insira o nome do grupo. Selecione os hosts onde as VMs serão executadas e clique em **OK** para criar o grupo.
7. Para criar uma regra, clique em **regras de VM/host**.
8. Clique em **+** .
9. Insira um nome para a regra e marque **habilitar**.
10. Para o tipo de regra, selecione **máquinas virtuais para hospedar**.
11. Selecione o grupo de VMs que contém as VMs Oracle.
12. Selecione **deve executar em hosts neste grupo**.
13. Selecione o grupo de hosts que você criou.
14. Clique em **OK** para criar a regra.

## <a name="references"></a>Referências

* [Sobre as políticas do vSAN](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.virtualsan.doc/GUID-08911FD3-2462-4C1C-AE81-0D4DBC8F7990.html)
* [Atributo do VMware multi-Writer para VMDKs compartilhados](https://docs.vmware.com/en/VMware-Cloud-on-AWS/solutions/VMware-Cloud-on-AWS.df6735f8b729fee463802083d46fdc75/GUID-A7642A82B3D6C5F7806DB40A3F2766D9.html)
