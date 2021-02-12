---
title: Solução do Azure VMware por CloudSimple-fazer backup de máquinas virtuais de carga de trabalho na nuvem privada usando o Veeam
description: Descreve como você pode fazer backup de suas máquinas virtuais que estão sendo executadas em uma nuvem privada do CloudSimple com base no Azure usando o Veeam B&R 9,5
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 7be606b3e23a594e67acf3f169d88353403d8577
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/05/2021
ms.locfileid: "97899330"
---
# <a name="back-up-workload-vms-on-cloudsimple-private-cloud-using-veeam-br"></a>Fazer backup de VMs de carga de trabalho na nuvem privada do CloudSimple usando o Veeam B&R

Este guia descreve como você pode fazer backup de suas máquinas virtuais que estão sendo executadas em uma nuvem privada do CloudSimple com base no Azure usando o Veeam B&R 9,5.

## <a name="about-the-veeam-back-up-and-recovery-solution"></a>Sobre a solução de backup e recuperação do Veeam

A solução Veeam inclui os componentes a seguir.

**Servidor de backup**

O servidor de backup é um Windows Server (VM) que serve como centro de controle para Veeam e executa essas funções: 

* Coordena tarefas de backup, replicação, verificação de recuperação e restauração
* Controla o agendamento de trabalho e a alocação de recursos
* Permite que você configure e gerencie componentes de infraestrutura de backup e especifique configurações globais para a infraestrutura de backup

**Servidores proxy**

Os servidores proxy são instalados entre o servidor de backup e outros componentes da infraestrutura de backup. Eles gerenciam as seguintes funções:

* Recuperação de dados da VM do armazenamento de produção
* Compactação
* Eliminação de duplicação
* Criptografia
* Transmissão de dados para o repositório de backup

**Repositório de backup**

O repositório de backup é o local de armazenamento em que o Veeam mantém arquivos de backup, cópias de VM e metadados para VMs replicadas.  O repositório pode ser um servidor Windows ou Linux com discos locais (ou NFS/SMB montado) ou um dispositivo de eliminação de duplicação de armazenamento de hardware.

### <a name="veeam-deployment-scenarios"></a>Cenários de implantação do Veeam
Você pode aproveitar o Azure para fornecer um repositório de backup e um destino de armazenamento para backup e arquivamento de longo prazo. Todo o tráfego de rede de backup entre as VMs na nuvem privada e o repositório de backup no Azure passa por um link de alta largura de banda e baixa latência. O tráfego de replicação entre regiões passa pela rede interna do Azure backplane, o que reduz os custos de largura de banda para os usuários.

**Implantação básica**

Para ambientes com menos de 30 TB para backup, o CloudSimple recomenda a seguinte configuração:

* Servidor de backup do Veeam e servidor proxy instalados na mesma VM na nuvem privada.
* Um repositório de backup primário baseado em Linux no Azure configurado como um destino para trabalhos de backup.
* `azcopy` usado para copiar os dados do repositório de backup primário para um contêiner de blob do Azure que é replicado para outra região.

![Diagrama que mostra cenários básicos de implantação de Veeam.](media/veeam-basicdeployment.png)

**Implantação avançada**

Para ambientes com mais de 30 TB para fazer backup, o CloudSimple recomenda a seguinte configuração:

* Um servidor proxy por nó no cluster vSAN, conforme recomendado pelo Veeam.
* Repositório de backup primário baseado no Windows na nuvem privada para armazenar em cache cinco dias de dados para restaurações rápidas.
* Repositório de backup do Linux no Azure como um destino para trabalhos de cópia de backup para retenção de duração mais longa. Esse repositório deve ser configurado como um repositório de backup de expansão.
* `azcopy` usado para copiar os dados do repositório de backup primário para um contêiner de blob do Azure que é replicado para outra região.

![Cenários de implantação básica](media/veeam-advanceddeployment.png)

Na figura anterior, observe que o proxy de backup é uma VM com acesso de adição quente a discos de VM de carga de trabalho no repositório de armazenamento vSAN. Veeam usa o modo de transporte proxy de backup de dispositivo virtual para vSAN.

## <a name="requirements-for-veeam-solution-on-cloudsimple"></a>Requisitos para a solução Veeam no CloudSimple

A solução Veeam exige que você faça o seguinte:

* Forneça suas próprias licenças do Veeam.
* Implante e gerencie o Veeam para fazer backup das cargas de trabalho em execução na nuvem privada do CloudSimple.

Essa solução fornece controle total sobre a ferramenta de backup Veeam e oferece a opção de usar a interface Veeam nativa ou o plug-in do vCenter do Veeam para gerenciar trabalhos de backup de VM.

Se você for um usuário Veeam existente, poderá ignorar a seção nos componentes da solução Veeam e prosseguir diretamente para os [cenários de implantação do Veeam](#veeam-deployment-scenarios).

## <a name="install-and-configure-veeam-backups-in-your-cloudsimple-private-cloud"></a>Instalar e configurar backups do Veeam em sua nuvem privada do CloudSimple

As seções a seguir descrevem como instalar e configurar uma solução de backup Veeam para sua nuvem privada do CloudSimple.

O processo de implantação consiste nestas etapas:

1. [interface do usuário do vCenter: configurar serviços de infraestrutura em sua nuvem privada](#vcenter-ui-set-up-infrastructure-services-in-your-private-cloud)
2. [Portal do CloudSimple: configurar a rede de nuvem privada para Veeam](#cloudsimple-private-cloud-set-up-private-cloud-networking-for-veeam)
3. [Portal do CloudSimple: escalonar privilégios](#cloudsimple-private-cloud-escalate-privileges-for-cloudowner)
4. [Portal do Azure: conectar sua rede virtual à nuvem privada](#azure-portal-connect-your-virtual-network-to-the-private-cloud)
5. [Portal do Azure: criar um repositório de backup no Azure](#azure-portal-connect-your-virtual-network-to-the-private-cloud)
6. [Portal do Azure: configurar o armazenamento de BLOBs do Azure para retenção de dados de longo prazo](#configure-azure-blob-storage-for-long-term-data-retention)
7. [interface do usuário do vCenter da nuvem privada: instalar o Veeam B&R](#vcenter-console-of-private-cloud-install-veeam-br)
8. [Console do Veeam: configurar o backup do Veeam & software de recuperação](#veeam-console-install-veeam-backup-and-recovery-software)
9. [Portal do CloudSimple: configurar os privilégios de acesso e desescalonamento de Veeam](#cloudsimple-portal-set-up-veeam-access-and-de-escalate-privileges)

### <a name="before-you-begin"></a>Antes de começar

Os itens a seguir são necessários antes de começar a implantação do Veeam:

* Uma assinatura do Azure de sua propriedade
* Um grupo de recursos do Azure criado previamente
* Uma rede virtual do Azure em sua assinatura
* Uma conta de armazenamento do Azure
* Uma [nuvem privada](create-private-cloud.md) criada usando o portal do CloudSimple.  

Os seguintes itens são necessários durante a fase de implementação:

* Modelos do VMware para Windows para instalar o Veeam (como o Windows Server 2012 R2-imagem de 64 bits)
* Uma VLAN disponível identificada para a rede de backup
* CIDR da sub-rede a ser atribuída à rede de backup
* ISO (mídia instalável) do Veeam 9,5 carregada no repositório de armazenamento do vSAN da nuvem privada

### <a name="vcenter-ui-set-up-infrastructure-services-in-your-private-cloud"></a>interface do usuário do vCenter: configurar serviços de infraestrutura em sua nuvem privada

Configure serviços de infraestrutura na nuvem privada para facilitar o gerenciamento de suas cargas de trabalho e ferramentas.

* Você pode adicionar um provedor de identidade externo conforme descrito em [configurar fontes de identidade do vCenter para usar Active Directory](set-vcenter-identity.md) se qualquer uma das seguintes opções for aplicável:

  * Você deseja identificar os usuários do seu Active Directory local (AD) em sua nuvem privada.
  * Você deseja configurar um AD em sua nuvem privada para todos os usuários.
  * Você deseja usar o Azure AD.
* Para fornecer pesquisa de endereço IP, gerenciamento de endereços IP e serviços de resolução de nomes para suas cargas de trabalho na nuvem privada, configure um servidor DHCP e DNS, conforme descrito em [configurar aplicativos DNS e DHCP e cargas de trabalho em sua nuvem privada do CloudSimple](dns-dhcp-setup.md).

### <a name="cloudsimple-private-cloud-set-up-private-cloud-networking-for-veeam"></a>Nuvem privada do CloudSimple: configurar a rede de nuvem privada para Veeam

Acesse o portal do CloudSimple para configurar a rede de nuvem privada para a solução Veeam.

Crie uma VLAN para a rede de backup e atribua a ela um CIDR de sub-rede. Para obter instruções, consulte [criar e gerenciar VLANs/sub-redes](create-vlan-subnet.md).

Crie regras de firewall entre a sub-rede de gerenciamento e a rede de backup para permitir o tráfego de rede nas portas usadas pelo Veeam. Consulte o tópico Veeam [usava portas](https://helpcenter.veeam.com/docs/backup/vsphere/used_ports.html?ver=95). Para obter instruções sobre a criação da regra de firewall, consulte [configurar regras e tabelas de firewall](firewall.md).

A tabela a seguir fornece uma lista de portas.

| ícone | Descrição | ícone | Descrição |
| ------------ | ------------- | ------------ | ------------- |
| Servidor de backup  | vCenter  | HTTPS/TCP  | 443 |
| Servidor de backup <br> *Necessário para implantar os componentes de replicação & backup do Veeam* | Proxy de backup  | TCP/UDP  | 135, 137 a 139 e 445 |
    | Servidor de backup   | DNS  | UDP  | 53  | 
    | Servidor de backup   | Servidor de notificação de atualização do Veeam  | TCP  | 80  | 
    | Servidor de backup   | Servidor de atualização de licença do Veeam  | TCP  | 443  | 
    | Proxy de backup   | vCenter |   |   | 
    | Proxy de backup  | Repositório de backup do Linux   | TCP  | 22  | 
    | Proxy de backup  | Repositório de backup do Windows  | TCP  | 49152-65535   | 
    | Repositório de backup  | Proxy de backup  | TCP  | 2500-5000  | 
    | Repositório de backup de origem<br> *Usado para trabalhos de cópia de backup*  | Repositório de destino de backup  | TCP  | 2500-5000  | 

Crie regras de firewall entre a sub-rede de carga de trabalho e a rede de backup, conforme descrito em [configurar regras e tabelas de firewall](firewall.md).  Para backup e restauração com reconhecimento de aplicativo, [as portas adicionais](https://helpcenter.veeam.com/docs/backup/vsphere/used_ports.html?ver=95) devem ser abertas nas VMs de carga de trabalho que hospedam aplicativos específicos.

Por padrão, o CloudSimple fornece um link do ExpressRoute 1 Gbps. Para tamanhos de ambiente maiores, um link de largura de banda maior pode ser necessário. Contate o suporte do Azure para obter mais informações sobre links de largura de banda maiores.

Para continuar a instalação, você precisa da chave de autorização e do URI do circuito par e do acesso à sua assinatura do Azure.  Essas informações estão disponíveis na página conexão de rede virtual no portal do CloudSimple. Para obter instruções, consulte [obter informações de emparelhamento para a rede virtual do Azure para a conexão CloudSimple](virtual-network-connection.md). Se você tiver problemas para obter as informações, [entre em contato com o suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

### <a name="cloudsimple-private-cloud-escalate-privileges-for-cloudowner"></a>Nuvem privada CloudSimple: escalonar privilégios para cloudowner

O usuário ' cloudowner ' padrão não tem privilégios suficientes no vCenter da nuvem privada para instalar o VEEAM, portanto, os privilégios de vCenter do usuário devem ser escalados. Para obter mais informações, consulte [escalonar privilégios](escalate-private-cloud-privileges.md).

### <a name="azure-portal-connect-your-virtual-network-to-the-private-cloud"></a>Portal do Azure: conectar sua rede virtual à nuvem privada

Conecte sua rede virtual à nuvem privada seguindo as instruções em conexão de [rede virtual do Azure usando o ExpressRoute](azure-expressroute-connection.md).

### <a name="azure-portal-create-a-backup-repository-vm"></a>Portal do Azure: criar uma VM de repositório de backup

1. Crie uma VM D2 v3 padrão com (2 vCPUs e 8 GB de memória).
2. Selecione a imagem baseada em CentOS 7,4.
3. Configure um NSG (grupo de segurança de rede) para a VM. Verifique se a VM não tem um endereço IP público e não está acessível pela Internet pública.
4. Crie uma conta de usuário com base em senha e nome de usuários para a nova VM. Para obter instruções, consulte [criar uma máquina virtual Linux no portal do Azure](../virtual-machines/linux/quick-create-portal.md).
5. Crie HDD 1x GiB Standard e anexe-o à VM do repositório.  Para obter instruções, consulte [como anexar um disco de dados gerenciado a uma VM do Windows no portal do Azure](../virtual-machines/windows/attach-managed-disk-portal.md).
6. [Crie um volume XFS no disco gerenciado](https://www.digitalocean.com/docs/volumes/how-to/). Faça logon na VM usando as credenciais mencionadas anteriormente. Execute o script a seguir para criar um volume lógico, adicione o disco a ele, crie uma [partição](https://www.digitalocean.com/docs/volumes/how-to/partition/) de sistema de arquivos XFS e [monte](https://www.digitalocean.com/docs/volumes/how-to/mount/) a partição no caminho/backup1.

    Exemplo de script:

    ```
    sudo pvcreate /dev/sdc
    sudo vgcreate backup1 /dev/sdc
    sudo lvcreate -n backup1 -l 100%FREE backup1
    sudo mkdir -p /backup1
    sudo chown veeamadmin /backup1
    sudo chmod 775 /backup1
    sudo mkfs.xfs -d su=64k -d sw=1 -f /dev/mapper/backup1-backup1
    sudo mount -t xfs /dev/mapper/backup1-backup1 /backup1
    ```

7. Expor o/backup1 como um ponto de montagem de NFS para o servidor de backup Veeam que está em execução na nuvem privada. Para obter instruções, consulte o artigo do oceano digital [como configurar uma montagem de NFS no CentOS 6](https://www.digitalocean.com/community/tutorials/how-to-set-up-an-nfs-mount-on-centos-6). Use esse nome de compartilhamento NFS ao configurar o repositório de backup no servidor de backup Veeam.

8. Configure regras de filtragem no NSG para a VM do repositório de backup para permitir explicitamente todo o tráfego de rede de e para a VM.

> [!NOTE]
> O Veeam Backup & Replication usa o protocolo SSH para se comunicar com repositórios de backup do Linux e requer o utilitário SCP em repositórios do Linux. Verifique se o daemon SSH está configurado corretamente e se o SCP está disponível no host Linux.

### <a name="configure-azure-blob-storage-for-long-term-data-retention"></a>Configurar o armazenamento de BLOBs do Azure para retenção de dados de longo prazo

1. Crie uma conta de armazenamento de uso geral (GPv2) do tipo padrão e um contêiner de BLOB, conforme descrito no Introdução de vídeo da Microsoft [com o armazenamento do Azure](https://azure.microsoft.com/resources/videos/get-started-with-azure-storage).
2. Crie um contêiner de armazenamento do Azure, conforme descrito na referência [criar contêiner](/rest/api/storageservices/create-container) .
2. Baixe o `azcopy` Utilitário de linha de comando para Linux da Microsoft. Você pode usar os seguintes comandos no shell bash no CentOS 7,5.

    ```
    wget -O azcopy.tar.gz https://aka.ms/downloadazcopylinux64
    tar -xf azcopy.tar.gz
    sudo ./install.sh
    sudo yum -y install libunwind.x86_64
    sudo yum -y install icu
    ```

3. Use o `azcopy` comando para copiar arquivos de backup de e para o contêiner de BLOB.  Consulte [transferir dados com AzCopy no Linux](../storage/common/storage-use-azcopy-v10.md) para obter comandos detalhados.

### <a name="vcenter-console-of-private-cloud-install-veeam-br"></a>console do vCenter da nuvem privada: instalar o Veeam B&R

Acesse o vCenter de sua nuvem privada para criar uma conta de serviço Veeam, instale o Veeam B&R 9,5 e configure o Veeam usando a conta de serviço.

1. Crie uma nova função chamada ' Veeam Backup role ' e atribua a ela permissões necessárias conforme recomendado pelo Veeam. Para obter detalhes, consulte o tópico Veeam [permissões necessárias](https://helpcenter.veeam.com/docs/backup/vsphere/required_permissions.html?ver=95).
2. Crie um novo grupo ' grupo de usuários Veeam ' no vCenter e atribua a ele a ' função de backup Veeam '.
3. Crie um novo usuário ' conta de serviço do Veeam ' e adicione-o ao ' grupo de usuários do Veeam '.

    ![Criando uma conta de serviço do Veeam](media/veeam-vcenter01.png)

4. Crie um grupo de portas distribuídas no vCenter usando a rede de backup VLAN. Para obter detalhes, veja o vídeo do VMware [criando um grupo de portas distribuídas no cliente Web vSphere](https://www.youtube.com/watch?v=wpCd5ZbPOpA).
5. Crie as VMs para os servidores de backup e proxy do Veeam no vCenter de acordo com os [requisitos de sistema do Veeam](https://helpcenter.veeam.com/docs/backup/vsphere/system_requirements.html?ver=95). Você pode usar o Windows 2012 R2 ou Linux. Para obter mais informações, consulte [requisitos para usar repositórios de backup do Linux](https://www.veeam.com/kb2216).
6. Monte o ISO Veeam instalável como um dispositivo de CDROM na VM do servidor de backup do Veeam.
7. Usando uma sessão RDP para o computador Windows 2012 R2 (o destino para a instalação do Veeam), [Instale o Veeam B&R 9.5 U3](https://helpcenter.veeam.com/docs/backup/vsphere/install_vbr.html?ver=95) em uma VM do Windows 2012 R2.
8. Localize o endereço IP interno da VM do servidor de backup Veeam e configure o endereço IP para ser estático no servidor DHCP. As etapas exatas necessárias para fazer isso dependem do servidor DHCP. Por exemplo, o artigo do NETGATE <a href="https://www.netgate.com/docs/pfsense/dhcp/dhcp-server.html" target="_blank">mapeamentos DHCP estáticos</a> explica como configurar um servidor DHCP usando um roteador pfSense.

### <a name="veeam-console-install-veeam-backup-and-recovery-software"></a>Console do Veeam: instalar o software de backup e recuperação do Veeam

Usando o console do Veeam, configure o software de backup e recuperação do Veeam. Para obter detalhes, consulte [Veeam Backup & Replication v9-Installation and Deployment](https://www.youtube.com/watch?v=b4BqC_WXARk).

1. Adicione VMware vSphere como um ambiente de servidor gerenciado. Quando solicitado, forneça as credenciais da conta de serviço do Veeam que você criou no início do [console do vCenter da nuvem privada: Instale o Veeam B&R](#vcenter-console-of-private-cloud-install-veeam-br).

    * Use as configurações padrão para controle de carga e configurações avançadas padrão.
    * Defina o local do servidor de montagem como o servidor de backup.
    * Altere o local de backup de configuração do servidor Veeam para o repositório remoto.

2. Adicione o servidor Linux no Azure como o repositório de backup.

    * Use as configurações padrão para o controle de carga e para as configurações avançadas. 
    * Defina o local do servidor de montagem como o servidor de backup.
    * Altere o local de backup de configuração do servidor Veeam para o repositório remoto.

3. Habilite a criptografia de backup de configuração usando **as configurações de backup de configuração> inicial**.

4. Adicione uma VM do Windows Server como um servidor proxy para o ambiente VMware. Usando ' regras de tráfego ' para um proxy, criptografe os dados de backup pela conexão.

5. Configurar trabalhos de backup.
    * Para configurar trabalhos de backup, siga as instruções em [criando um trabalho de backup](https://www.youtube.com/watch?v=YHxcUFEss4M).
    * Habilite a criptografia de arquivos de backup em **Configurações avançadas > armazenamento**.

6. Configurar trabalhos de cópia de backup.

    * Para configurar trabalhos de cópia de backup, siga as instruções no vídeo [criando um trabalho de cópia de backup](https://www.youtube.com/watch?v=LvEHV0_WDWI&t=2s).
    * Habilite a criptografia de arquivos de backup em **Configurações avançadas > armazenamento**.

### <a name="cloudsimple-portal-set-up-veeam-access-and-de-escalate-privileges"></a>Portal do CloudSimple: configurar os privilégios de acesso e desescalonamento de Veeam
Crie um endereço IP público para o servidor de backup e recuperação Veeam. Para obter instruções, consulte [alocar endereços IP públicos](public-ips.md).

Crie uma regra de firewall usando para permitir que o servidor de backup do Veeam crie uma conexão de saída com o site do Veeam para baixar atualizações/patches na porta TCP 80. Para obter instruções, consulte [configurar regras e tabelas de firewall](firewall.md).

Para desescalonar privilégios, consulte [desescalonamento de privilégios](escalate-private-cloud-privileges.md#de-escalate-privileges).

## <a name="references"></a>Referências

### <a name="cloudsimple-references"></a>Referências de CloudSimple

* [Criar uma nuvem privada](create-private-cloud.md)
* [Criar e gerenciar VLANs/sub-redes](create-vlan-subnet.md)
* [Fontes de identidade do vCenter](set-vcenter-identity.md)
* [Configuração de DNS e DHCP de carga de trabalho](dns-dhcp-setup.md)
* [Escalonar privilégios](escalate-privileges.md)
* [Configurar regras e tabelas de firewall](firewall.md)
* [Permissões de nuvem privada](learn-private-cloud-permissions.md)
* [Alocar endereços IP públicos](public-ips.md)

### <a name="veeam-references"></a>Referências de Veeam

* [Portas usadas](https://helpcenter.veeam.com/docs/backup/vsphere/used_ports.html?ver=95)
* [Permissões necessárias](https://helpcenter.veeam.com/docs/backup/vsphere/required_permissions.html?ver=95)
* [Requisitos do sistema](https://helpcenter.veeam.com/docs/backup/vsphere/system_requirements.html?ver=95)
* [Instalando o Veeam Backup & replicação](https://helpcenter.veeam.com/docs/backup/vsphere/install_vbr.html?ver=95)
* [Módulos e permissões necessários para suporte a FLR e repositório de vários sistemas operacionais para Linux](https://www.veeam.com/kb2216)
* [Veeam Backup & Replication v9 – instalação e implantação-vídeo](https://www.youtube.com/watch?v=b4BqC_WXARk)
* [Veeam v9 criando um trabalho de backup-vídeo](https://www.youtube.com/watch?v=YHxcUFEss4M)
* [Veeam v9 criando um trabalho de cópia de backup-vídeo](https://www.youtube.com/watch?v=LvEHV0_WDWI&t=2s)

### <a name="azure-references"></a>Referências do Azure

* [Configurar um gateway de rede virtual para ExpressRoute usando o portal do Azure](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)
* [Conectar uma VNet a um circuito - assinatura diferente](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription)
* [Criar uma máquina virtual do Linux no portal do Azure](../virtual-machines/linux/quick-create-portal.md)
* [Como anexar um disco de dados gerenciado a uma VM do Windows no portal do Azure](../virtual-machines/windows/attach-managed-disk-portal.md)
* [Introdução com o armazenamento do Azure-vídeo](https://azure.microsoft.com/resources/videos/get-started-with-azure-storage)
* [Create Container](/rest/api/storageservices/create-container)
* [Transferir dados com o AzCopy no Linux](../storage/common/storage-use-azcopy-v10.md)

### <a name="vmware-references"></a>Referências do VMware

* [Criando um grupo de portas distribuídas no cliente Web vSphere-vídeo](https://www.youtube.com/watch?v=wpCd5ZbPOpA)

### <a name="other-references"></a>Outras referências

* [Criar um volume XFS no disco gerenciado – RedHat](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/storage_administration_guide/ch-xfs)
* [Como configurar uma montagem de NFS no CentOS 7-HowToForge](https://www.howtoforge.com/nfs-server-and-client-on-centos-7)
* [Configurando o servidor DHCP-NETGATE](https://www.netgate.com/docs/pfsense/dhcp/dhcp-server.html)
