---
title: Como instalar o servidor de destino mestre do Linux para o failover do Azure no local | Microsoft Docs
description: "Antes de proteger novamente uma máquina virtual Linux, você precisa de um servidor de destino mestre do Linux. Saiba como instalar um."
services: site-recovery
documentationcenter: 
author: ruturaj
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 08/11/2017
ms.author: ruturajd
ms.openlocfilehash: 5341e3e56e0c366079958dd9a885f6ee3e8436cb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="install-a-linux-master-target-server"></a>Instalar o servidor de destino mestre do Linux
Após o failover de suas máquinas virtuais, você poderá executar failback das máquinas virtuais para o site local. Para realizar failback, você precisa proteger novamente a máquina virtual do Azure para o site local. Para este processo, é necessário um servidor de destino mestre para receber o tráfego. 

Se sua máquina virtual protegida for uma máquina virtual do Windows, será necessário um destino mestre do Windows. Para uma máquina virtual Linux, é necessário um destino mestre do Linux. Leia as etapas a seguir para saber como criar e instalar um destino mestre do Linux.

> [!IMPORTANT]
> A partir da versão 9.10.0 do servidor de destino mestre, o servidor de destino mestre mais recente somente pode ser instalado em um servidor Ubuntu 16.04. Novas instalações não são permitidas em servidores CentOS6.6. No entanto, você pode continuar a atualizar os servidores de destino mestre antigos usando a versão 9.10.0.

## <a name="overview"></a>Visão geral
Este artigo fornece instruções sobre como instalar um destino mestre do Linux.

Publique comentários ou perguntas no final deste artigo ou no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="prerequisites"></a>Pré-requisitos

* Para escolher o host no qual deseja implantar o destino mestre, determine se o failback vai ser para uma máquina virtual local existente ou para uma nova máquina virtual. 
    * Para uma máquina virtual existente, o host do destino mestre deve ter acesso a armazenamentos de dados da máquina virtual.
    * Se a máquina virtual no local não existir, a máquina virtual de failback será criada no mesmo host como o destino mestre. Você pode escolher qualquer host ESXi para instalar o destino mestre.
* O destino mestre deve estar em uma rede que pode se comunicar com o servidor de processo e com o servidor de configuração.
* A versão de destino mestre deve ser igual ou anterior às versões de servidor de processo e o servidor de configuração. Por exemplo, se a versão do servidor de configuração for a 9.4, a versão de destino mestre poderá ser 9.4 ou 9.3 mas não 9.5.
* O destino mestre só pode ser uma máquina virtual VMware e não um servidor físico.

## <a name="create-the-master-target-according-to-the-sizing-guidelines"></a>Criar o destino mestre de acordo com as diretrizes de dimensionamento

Crie o destino mestre de acordo com as seguintes diretrizes de dimensionamento:
- **RAM**: 6 GB ou mais
- **Tamanho do disco do sistema operacional**: 100 GB ou mais (para instalar o CentOS6.6)
- **Tamanho de disco adicional para a unidade de retenção**: 1 TB
- **Núcleos de CPU**: 4 núcleos ou mais

Há suporte para os kernels do Ubuntu a seguir.


|Série de Kernel  |Suporte até  |
|---------|---------|
|4.4      |4.4.0-81-generic         |
|4.8      |4.8.0-56-generic         |
|4.10     |4.10.0-24-generic        |


## <a name="deploy-the-master-target-server"></a>Implantar o servidor de destino mestre

### <a name="install-ubuntu-16042-minimal"></a>Instalar o Ubuntu 16.04.2 Minimal

Use as seguinte as etapas para instalar o sistema de operacional de 64 bits do Ubuntu 16.04.2.

**Etapa 1:** acesse o [link de download](https://www.ubuntu.com/download/server/thank-you?version=16.04.2&architecture=amd64) e escolha o espelho mais próximo para baixar um ISO do Ubuntu 16.04.2 Minimal de 64 bits.

Mantenha um ISO do Ubuntu 16.04.2 Minimal de 64 bits na unidade de DVD e inicie o sistema.

**Etapa 2:** selecione **Inglês** como o seu Idioma de preferência e selecione **Enter**.

![Selecionar um idioma](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image1.png)

**Etapa 3:** selecione **Instalar Servidor Ubuntu** e pressione **Enter**.

![Selecionar Instalar Ubuntu Server](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image2.png)

**Etapa 4:** selecione **Inglês** como o idioma de preferência e selecione **Enter**.

![Selecionar Inglês como seu idioma preferencial](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image3.png)

**Etapa 5:** selecione a opção apropriada para o **Fuso Horário** na lista de opções e selecione **Enter**.

![Selecionar o fuso horário correto](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image4.png)

**Etapa 6:** selecione **Não** (a opção padrão) e selecione **Enter**.


![Configurar o teclado](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image5.png)

**Etapa 7:** selecione **Inglês (EUA)** como um país de origem para o teclado e selecione **Enter**.

![Selecionar EUA como país de origem](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image6.png)

**Etapa 8:** selecione **inglês (EUA)** como o layout do teclado e selecione **Enter**.

![Selecionar Inglês (Estados Unidos) como o layout do teclado](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image7.png)

**Etapa 9:** insira o nome do host para o servidor na caixa **Nome do host** e selecione **Continuar**.

![Inserir o nome do host para o servidor](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image8.png)

**Etapa 10:** para criar uma conta de usuário, digite o nome de usuário e selecione **Continuar**.

![Criar uma conta do usuário](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image9.png)

**Etapa 11:** digite a senha para a nova conta de usuário e selecione **Continuar**.

![Digitar a senha](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image10.png)

**Etapa 12:** confirme a senha para o novo usuário e selecione **Continuar**.

![Confirmar as senhas](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image11.png)

**Etapa 13:** selecione **Não** (a opção padrão) e selecione **Enter**.

![Configurar usuários e senhas](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image12.png)

**Etapa 14:** se o fuso horário exibido está correto, selecione **Sim** (a opção padrão) e selecione **Enter**.

Para reconfigurar seu fuso horário, selecione **Não**.

![Configurar o relógio](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image13.png)

**Etapa 15:** entre as opções de método de particionamento, selecione **Guiado - usar o disco inteiro**e selecione **Enter**.

![Selecionar a opção de método de particionamento](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image14.png)

**Etapa 16:** selecione o disco adequado nas opções para **Selecionar disco para partição** e selecione **Enter**.


![Selecionar o disco](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image15.png)

**Etapa 17:** selecione **Sim** para gravar as alterações para o disco e selecione **Enter**.

![Gravar as alterações em disco](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image16.png)

**Etapa 18:** selecione a opção padrão, selecione **Continuar**e selecione **Enter**.

![Selecionar a opção padrão](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image17.png)

**Etapa 19:** selecione a opção apropriada para o gerenciamento de atualizações em seu sistema e selecione **Enter**.

![Selecionar a forma de gerenciar atualizações](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image18.png)

> [!WARNING]
> Como o servidor de destino mestre do Azure Site Recovery requer uma versão muito específica do Ubuntu, você precisa fazer com que as atualizações de kernel estejam desabilitadas para a máquina virtual. Se elas estiverem habilitadas, todas as atualizações regulares farão com que o servidor de destino mestre não funcione corretamente. Selecione a opção **Sem atualizações automáticas**.


**Etapa 20:** selecione as opções padrão. Se você quiser uma conexão do openSSH para SSH, então selecione a opção **OpenSSH server** e depois selecione **Continuar**.

![Selecionar o software](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image19.png)

**Etapa 21:** selecione **Sim**e selecione **Enter**.

![Instalar o carregador de inicialização GRUB](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image20.png)

**Etapa 22:** selecione o dispositivo apropriado para a instalação do carregador de inicialização (preferencialmente **/dev/sda**) e selecione **Enter**.

![Selecionar um dispositivo para a instalação do carregador de inicialização](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image21.png)

**Etapa 23:** selecione **Continuar**e selecione **Enter** para concluir a instalação.

![Concluir a instalação](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image22.png)

Após a instalação, entre na VM com as novas credenciais de usuário. (Consulte a **Etapa 10** para saber mais.)

Execute as etapas descritas na captura de tela a seguir para definir a senha do usuário raiz. Faça logon como usuário raiz.

![Definir a senha do usuário raiz](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image23.png)


### <a name="prepare-the-machine-for-configuration-as-a-master-target-server"></a>Preparar o computador para ser configurado como um servidor de destino mestre
Em seguida, prepare o computador para ser configurado como um servidor de destino mestre.

Para obter a ID de cada disco SCSI em uma máquina virtual Linux, habilite o parâmetro **disk.EnableUUID = TRUE**.

Para habilitar esse parâmetro, use as seguintes etapas:

1. Desligue sua máquina virtual.

2. Clique com o botão direito do mouse na entrada da máquina virtual no painel esquerdo e selecione **Editar Configurações**.

3. Selecione a guia **Opções**.

4. No painel esquerdo, selecione **Avançado** > **Geral**e selecione o botão **Parâmetros de Configuração** no canto inferior direito da tela.

    ![Guia Opções](./media/site-recovery-how-to-install-linux-master-target/media/image20.png)

    A opção **Parâmetros de Configuração** não fica disponível enquanto a máquina está em execução. Para ativar essa guia, desligue a máquina virtual.

5. Verifique se já existe uma linha com **disk.EnableUUID**.

    - Se o valor existe e está definido como **False**, altere o valor para **True**. (Os valores não diferenciam maiúsculas de minúsculas.)

    - Se o valor existir e se está definido como **True**, selecione **Cancelar**.

    - Se o valor não existir, selecione **Adicionar Linha**.

    - Na coluna Nome, adicione **disk.EnableUUID**e defina o valor como **TRUE**.

    ![Verificação se disk.EnableUUID já existe](./media/site-recovery-how-to-install-linux-master-target/media/image21.png)

#### <a name="disable-kernel-upgrades"></a>Desabilitar atualizações de kernel

O servidor de destino mestre do Azure Site Recovery requer uma versão muito específica do Ubuntu, verifique se as atualizações de kernel estão desabilitadas para a máquina virtual.

Se as atualizações de kernel estiverem habilitadas, todas as atualizações regulares farão com que o servidor de destino mestre não funcione corretamente.

#### <a name="download-and-install-additional-packages"></a>Baixar e instalar pacotes adicionais

> [!NOTE]
> Verifique se você tem conectividade com a Internet para baixar e instalar pacotes adicionais. Se você não tiver conexão com a Internet, precisará encontrar esses pacotes RPM e instalá-los manualmente.

```
apt-get install -y multipath-tools lsscsi python-pyasn1 lvm2 kpartx
```

### <a name="get-the-installer-for-setup"></a>Obter o instalador para instalação

Se o destino mestre tiver conectividade com a Internet, você poderá usar as etapas a seguir para baixar o instalador. Caso contrário, você pode copiar o instalador do servidor de processo e instalá-lo.

#### <a name="download-the-master-target-installation-packages"></a>Baixar os pacotes de instalação do destino mestre

[Baixe os bits de instalação de destino mestre Linux mais recentes](https://aka.ms/latestlinuxmobsvc).

Para baixá-los usando o Linux, digite:

```
wget https://aka.ms/latestlinuxmobsvc -O latestlinuxmobsvc.tar.gz
```

Baixe e descompacte o instalador em seu diretório base. Se você o descompactar em **/usr/Local**, a instalação falhará.


#### <a name="access-the-installer-from-the-process-server"></a>Acessar o instalador do servidor de processos

1. No servidor de processo, vá para **C:\Arquivos de Programas (x86)\Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository**.

2. Copie o arquivo do instalador obrigatório do servidor de processo e salvá-lo como **latestlinuxmobsvc.tar.gz** no seu diretório base.


### <a name="apply-custom-configuration-changes"></a>Aplicar alterações de configuração personalizadas

Para aplicar alterações de configuração personalizada, use as seguintes etapas:


1. Execute o comando a seguir para descompactar o binário.
    ```
    tar -zxvf latestlinuxmobsvc.tar.gz
    ```
    ![Captura de tela da execução do comando](./media/site-recovery-how-to-install-linux-master-target/image16.png)

2. Execute o comando a seguir para conceder permissão.
    ```
    chmod 755 ./ApplyCustomChanges.sh
    ```

3. Execute o comando a seguir para executar o script.
    ```
    ./ApplyCustomChanges.sh
    ```
> [!NOTE]
> Execute o script apenas uma vez no servidor. Desligue o servidor. Em seguida, reinicialize o servidor depois de adicionar um disco, conforme descrito na próxima seção.

### <a name="add-a-retention-disk-to-the-linux-master-target-virtual-machine"></a>Adicionar um disco de retenção para a máquina virtual de destino mestre do Linux

Use as etapas a seguir para criar um disco de retenção:

1. Anexe um novo disco de 1 TB à máquina virtual de destino mestre Linux e inicialize a máquina.

2. Use o comando **multipath -ll** para conhecer a ID de vários caminhos do disco de retenção.

    ```
    multipath -ll
    ```
    ![A ID de vários caminhos do disco de retenção](./media/site-recovery-how-to-install-linux-master-target/media/image22.png)

3. Formate a unidade e crie um sistema de arquivos na nova unidade.

    ```
    mkfs.ext4 /dev/mapper/<Retention disk's multipath id>
    ```
    ![Criação de um sistema de arquivos na unidade](./media/site-recovery-how-to-install-linux-master-target/media/image23.png)

4. Depois de criar o sistema de arquivos, monte o disco de retenção.
    ```
    mkdir /mnt/retention
    mount /dev/mapper/<Retention disk's multipath id> /mnt/retention
    ```
    ![Montagem do disco de retenção](./media/site-recovery-how-to-install-linux-master-target/media/image24.png)

5. Crie a entrada **fstab** para montar a unidade de retenção sempre que o sistema iniciar.
    ```
    vi /etc/fstab
    ```
    Selecione **Inserir** para começar a editar o arquivo. Crie uma nova linha e insira o texto a seguir. Edite a ID de vários caminhos de disco com base na ID de vários caminhos realçada no comando anterior.

    **/dev/mapper/<Retention disks multipath id> /mnt/retention ext4 rw 0 0**

    Selecione **Esc** e digite **:wq** (gravar e sair) para fechar a janela do editor.

### <a name="install-the-master-target"></a>Instalar o destino mestre

> [!IMPORTANT]
> A versão do servidor de destino mestre deve ser igual ou anterior às versões de servidor de processo e o servidor de configuração. Se essa condição não for atendida, o proteger novamente terá êxito, mas a replicação falhará.


> [!NOTE]
> Antes de instalar o servidor de destino mestre, verifique se o arquivo **/etc/hosts** na máquina virtual contém entradas que mapeiam o nome do host local para os endereços IP associados a todos os adaptadores de rede.

1. Copie a frase secreta de **C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase** no servidor de configuração. Em seguida, salve-o como **passphrase.txt** no mesmo diretório local executando o seguinte comando:

    ```
    echo <passphrase> >passphrase.txt
    ```
    Exemplo: 
    
    ```
    echo itUx70I47uxDuUVY >passphrase.txt
    ```

2. Anote o endereço IP do servidor de configuração. Você precisará disso na próxima etapa.

3. Execute o comando a seguir para instalar o servidor de destino mestre e registrar o servidor no servidor de configuração.

    ```
    ./install -q -d /usr/local/ASR -r MT -v VmWare
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <ConfigurationServer IP Address> -P passphrase.txt
    ```

    Exemplo: 
    
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i 104.40.75.37 -P passphrase.txt
    ```

    Aguarde até que o script seja concluído. Se o destino mestre for registrado com êxito, ele será listado na página Infraestrutura do **Site Recovery** do portal.


#### <a name="install-the-master-target-by-using-interactive-installation"></a>Instalar o destino mestre usando a instalação interativa

1. Execute o comando a seguir para instalar o destino mestre. Para a função de agente, escolha **Destino Mestre**.

    ```
    ./install
    ```

2. Escolha o local padrão para instalação e selecione **Enter** para continuar.

    ![Escolher um local padrão para a instalação de destino mestre](./media/site-recovery-how-to-install-linux-master-target/image17.png)

Após a conclusão da instalação, registre o servidor de configuração por meio da linha de comando.

1. Observe o endereço IP do servidor de configuração. Você precisará disso na próxima etapa.

2. Execute o comando a seguir para instalar o servidor de destino mestre e registrar o servidor no servidor de configuração.

    ```
    ./install -q -d /usr/local/ASR -r MT -v VmWare
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <ConfigurationServer IP Address> -P passphrase.txt
    ```
    Exemplo: 

    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i 104.40.75.37 -P passphrase.txt
    ```

   Aguarde até que o script seja concluído. Se o destino mestre for registrado com êxito, ele será listado na página Infraestrutura do **Site Recovery** do portal.


### <a name="upgrade-the-master-target"></a>Atualizar o destino mestre

Execute o instalador. Ele detecta automaticamente que o agente está instalado no destino mestre. Para atualizar, selecione **Y**.  Quando a instalação for concluída, verifique a versão de destino mestre instalada usando o comando a seguir.

    ```
    cat /usr/local/.vx_version
    ```

Você pode ver que o campo **Versão** fornece o número de versão de destino mestre.

### <a name="install-vmware-tools-on-the-master-target-server"></a>Instalar ferramentas do VMware no servidor de destino mestre

Você precisa instalar ferramentas do VMware no destino mestre para que ele possa descobrir os armazenamentos de dados. Se as ferramentas não forem instaladas, a tela Proteja Novamente não estará listada nos armazenamentos de dados. Após a instalação das ferramentas do VMware, será necessário reiniciar.

## <a name="next-steps"></a>Próximas etapas
Depois que a instalação e o registro do destino mestre forem concluídos, você poderá ver o destino mestre na seção **Destino Mestre** na **Infraestrutura do Site Recovery**, na visão geral do servidor de configuração.

Você pode prosseguir com a [nova proteção](site-recovery-how-to-reprotect.md), seguida pelo failback.

## <a name="common-issues"></a>Problemas comuns

* Não ative Storage vMotion em quaisquer componentes de gerenciamento como um destino mestre. Se o destino mestre se mover após um proteger novamente com êxito, os VMDKs (discos de máquina virtual) não poderão ser desanexados. Nesse caso, o failback falhará.

* O destino mestre não deve ter todos os instantâneos na máquina virtual. Se houver instantâneos, o failback falhará.

* Devido a algumas configurações de NIC personalizadas em alguns clientes, a interface de rede é desabilitada durante a inicialização, e não é possível inicializar o destino mestre. Verifique se as propriedades a seguir foram definidas corretamente. Verifique essas propriedades em /etc/sysconfig/network-scripts/ifcfg-eth* do arquivo da placa Ethernet.
    * BOOTPROTO=dhcp
    * ONBOOT=yes
