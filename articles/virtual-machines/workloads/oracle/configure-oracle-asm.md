---
title: Configurar o Oracle ASM em uma máquina virtual Linux do Azure | Microsoft Docs
description: Coloque o Oracle ASM em funcionamento rapidamente no ambiente do Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: romitgirdhar
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2018
ms.author: rogirdh
ms.openlocfilehash: a2f6eab495680b3f32246488af5b7bbe5263d93a
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707695"
---
# <a name="set-up-oracle-asm-on-an-azure-linux-virtual-machine"></a>Configurar o Oracle ASM em uma máquina virtual Linux do Azure  

Máquinas virtuais do Azure fornecem um ambiente de computação totalmente configurável e flexível. Este tutorial aborda a implantação básica de máquina virtual do Azure combinada com a instalação e a configuração do Oracle ASM (Automated Storage Management).  Você aprenderá como:

> [!div class="checklist"]
> * Criar e conectar-se a uma VM do Oracle Database
> * Instalar e configurar o Oracle Automated Storage Management
> * Instalar e configurar a infraestrutura Oracle Grid
> * Inicializar uma instalação do Oracle ASM
> * Criar um Oracle DB gerenciado por ASM


[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este tutorial exigirá que você execute a CLI do Azure versão 2.0.4 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure]( /cli/azure/install-azure-cli). 

## <a name="prepare-the-environment"></a>Preparar o ambiente

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Para criar um grupo de recursos, use o comando [az group create](/cli/azure/group). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. Neste exemplo, um grupo de recursos chamado de *myResourceGroup* é criado na região *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

### <a name="create-a-vm"></a>Criar uma máquina virtual

Para criar uma máquina virtual com base na imagem do Oracle Database e configurá-la para usar o Oracle ASM, use o comando [az vm create](/cli/azure/vm). 

O exemplo a seguir cria uma VM denominada myVM que tem um tamanho Standard_DS2_v2 com quatro discos de dados anexados, cada um com 50 GB. Se ainda não existirem em um local de chave padrão, o exemplo também criará chaves SSH.  Para usar um conjunto específico de chaves, use a opção `--ssh-key-value`.  

   ```azurecli-interactive
   az vm create --resource-group myResourceGroup \
    --name myVM \
    --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
    --size Standard_DS2_v2 \
    --generate-ssh-keys \
    --data-disk-sizes-gb 50 50 50 50
   ```

Depois de criar a VM, a CLI do Azure exibe informações semelhantes ao exemplo a seguir. Observe o valor de `publicIpAddress`. Você pode usar esse endereço para acessar a VM.

   ```azurecli
   {
     "fqdns": "",
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
     "location": "eastus",
     "macAddress": "00-0D-3A-36-2F-56",
     "powerState": "VM running",
     "privateIpAddress": "10.0.0.4",
     "publicIpAddress": "13.64.104.241",
     "resourceGroup": "myResourceGroup"
   }
   ```

### <a name="connect-to-the-vm"></a>Conectar-se à VM

Para criar uma sessão SSH com a VM e definir configurações adicionais, use o seguinte comando. Substitua o endereço IP pelo valor `publicIpAddress` para a sua VM.

```bash 
ssh <publicIpAddress>
```

## <a name="install-oracle-asm"></a>Instalar o Oracle ASM

Para instalar o Oracle ASM, conclua as etapas a seguir. 

Para saber mais sobre como instalar o ASM do Oracle, confira [Downloads do Oracle ASMLib para Oracle Linux 6](https://www.oracle.com/technetwork/server-storage/linux/asmlib/ol6-1709075.html).  

1. Você precisa fazer logon como raiz para continuar com a instalação do ASM:

   ```bash
   sudo su -
   ```
   
2. Execute esses comandos adicionais para instalar os componentes do Oracle ASM:

   ```bash
    yum list | grep oracleasm 
    yum -y install kmod-oracleasm.x86_64 
    yum -y install oracleasm-support.x86_64 
    wget https://download.oracle.com/otn_software/asmlib/oracleasmlib-2.0.12-1.el6.x86_64.rpm 
    yum -y install oracleasmlib-2.0.12-1.el6.x86_64.rpm 
    rm -f oracleasmlib-2.0.12-1.el6.x86_64.rpm
   ```

3. Verifique se o Oracle ASM está instalado:

   ```bash
   rpm -qa |grep oracleasm
   ```

    A saída desse comando deve listar os seguintes componentes:

    ```bash
   oracleasm-support-2.1.10-4.el6.x86_64
   kmod-oracleasm-2.0.8-15.el6_9.x86_64
   oracleasmlib-2.0.12-1.el6.x86_64
    ```

4. O ASM exige funções e usuários específicos para funcionar corretamente. Os seguintes comandos criam os grupos e as contas de usuário de pré-requisito: 

   ```bash
    groupadd -g 54345 asmadmin 
    groupadd -g 54346 asmdba 
    groupadd -g 54347 asmoper 
    useradd -u 3000 -g oinstall -G dba,asmadmin,asmdba,asmoper grid 
    usermod -g oinstall -G dba,asmdba,asmadmin oracle
   ```

5. Verifique se os usuários e grupos foram criados corretamente:

   ```bash
   id grid
   ```

    A saída desse comando deve listar os seguintes usuários e grupos:

    ```bash
    uid=3000(grid) gid=54321(oinstall) groups=54321(oinstall),54322(dba),54345(asmadmin),54346(asmdba),54347(asmoper)
    ```
 
6. Crie uma pasta para a *grade* de usuários e altere o proprietário:

   ```bash
   mkdir /u01/app/grid 
   chown grid:oinstall /u01/app/grid
   ```

## <a name="set-up-oracle-asm"></a>Configurar o Oracle ASM

Para este tutorial, o usuário padrão é *grade* e o grupo padrão é *asmadmin*. Verifique se o usuário *oracle* faz parte do grupo asmadmin. Para configurar a instalação do Oracle ASM, conclua as seguintes etapas:

1. A configuração do driver de biblioteca do Oracle ASM envolve a definição do usuário padrão (grade) e do grupo padrão (asmadmin), bem como a configuração da unidade para iniciar na inicialização (escolher y) e verificar se há discos na inicialização (escolher y). Você precisa para responder aos prompts do seguinte comando:

   ```bash
   /usr/sbin/oracleasm configure -i
   ```

   A saída desse comando deve ser semelhante ao seguinte, parando com os prompts a serem respondidos.

    ```bash
   Configuring the Oracle ASM library driver.

   This will configure the on-boot properties of the Oracle ASM library
   driver. The following questions will determine whether the driver is
   loaded on boot and what permissions it will have. The current values
   will be shown in brackets ('[]'). Hitting <ENTER> without typing an
   answer will keep that current value. Ctrl-C will abort.

   Default user to own the driver interface []: grid
   Default group to own the driver interface []: asmadmin
   Start Oracle ASM library driver on boot (y/n) [n]: y
   Scan for Oracle ASM disks on boot (y/n) [y]: y
   Writing Oracle ASM library driver configuration: done
   ```

2. Veja a configuração de disco:
   ```bash
   cat /proc/partitions
   ```

   A saída desse comando deve ser semelhante à seguinte listagem de discos disponíveis

   ```bash
   8       16   14680064 sdb
   8       17   14678976 sdb1
   8        0   52428800 sda
   8        1     512000 sda1
   8        2   51915776 sda2
   8       48   52428800 sdd
   8       64   52428800 sde
   8       80   52428800 sdf
   8       32   52428800 sdc
   11       0       1152 sr0
   ```

3. Formate o disco */dev/sdc* executando o seguinte comando e respondendo aos prompts com:
   - *n* para a nova partição
   - *p* para a partição primária
   - *1* para selecionar a primeira partição
   - pressione `enter` para o primeiro cilindro padrão
   - pressione `enter` para o último cilindro padrão
   - pressione *w* para gravar as alterações na tabela de partição  

   ```bash
   fdisk /dev/sdc
   ```
   
   Usando as respostas fornecidas acima, a saída para o comando fdisk deve ficar semelhante ao seguinte:

   ```bash
   Device contains not a valid DOS partition table, or Sun, SGI or OSF disklabel
   Building a new DOS disklabel with disk identifier 0xf865c6ca.
   Changes will remain in memory only, until you decide to write them.
   After that, of course, the previous content won't be recoverable.

   Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

   The device presents a logical sector size that is smaller than
   the physical sector size. Aligning to a physical sector (or optimal
   I/O) size boundary is recommended, or performance may be impacted.

   WARNING: DOS-compatible mode is deprecated. It's strongly recommended to
           switch off the mode (command 'c') and change display units to
           sectors (command 'u').

   Command (m for help): n
   Command action
     e   extended
     p   primary partition (1-4)
   p
   Partition number (1-4): 1
   First cylinder (1-6527, default 1):
   Using default value 1
   Last cylinder, +cylinders or +size{K,M,G} (1-6527, default 6527):
   Using default value 6527

   Command (m for help): w
   The partition table has been altered!

   Calling ioctl() to re-read partition table.
   Syncing disks.
   ```

4. Repita o comando fdisk anterior para `/dev/sdd`, `/dev/sde` e `/dev/sdf`.

5. Verifique a configuração de disco:

   ```bash
   cat /proc/partitions
   ```

   A saída do comando deve ser semelhante ao seguinte:

   ```bash
   major minor  #blocks  name

     8       16   14680064 sdb
     8       17   14678976 sdb1
     8       32   52428800 sdc
     8       33   52428096 sdc1
     8       48   52428800 sdd
     8       49   52428096 sdd1
     8       64   52428800 sde
     8       65   52428096 sde1
     8       80   52428800 sdf
     8       81   52428096 sdf1
     8        0   52428800 sda
     8        1     512000 sda1
     8        2   51915776 sda2
     11       0    1048575 sr0
   ```

6. Verifique o status do serviço Oracle ASM e inicie o serviço Oracle ASM:

   ```bash
   service oracleasm status 
   service oracleasm start
   ```

   A saída do comando deve ser semelhante ao seguinte:
   
   ```bash
   Checking if ASM is loaded: no
   Checking if /dev/oracleasm is mounted: no
   Initializing the Oracle ASMLib driver:                     [  OK  ]
   Scanning the system for Oracle ASMLib disks:               [  OK  ]
   ```

7. Crie discos do Oracle ASM:

   ```bash
   service oracleasm createdisk ASMSP /dev/sdc1 
   service oracleasm createdisk DATA /dev/sdd1 
   service oracleasm createdisk DATA1 /dev/sde1 
   service oracleasm createdisk FRA /dev/sdf1
   ```    

   A saída do comando deve ser semelhante ao seguinte:

   ```bash
   Marking disk "ASMSP" as an ASM disk:                       [  OK  ]
   Marking disk "DATA" as an ASM disk:                        [  OK  ]
   Marking disk "DATA1" as an ASM disk:                       [  OK  ]
   Marking disk "FRA" as an ASM disk:                         [  OK  ]
   ```

8. Liste os discos do Oracle ASM:

   ```bash
   service oracleasm listdisks
   ```   

   A saída do comando deve listar os seguintes discos do Oracle ASM:

   ```bash
    ASMSP
    DATA
    DATA1
    FRA
   ```

9. Altere as senhas para os usuários de raiz, da oracle e de grade. **Anote essas novas senhas** pois elas serão usadas posteriormente, durante a instalação.

   ```bash
   passwd oracle 
   passwd grid 
   passwd root
   ```

10. Altere a permissão de pasta:

    ```bash
    chmod -R 775 /opt 
    chown grid:oinstall /opt 
    chown oracle:oinstall /dev/sdc1 
    chown oracle:oinstall /dev/sdd1 
    chown oracle:oinstall /dev/sde1 
    chown oracle:oinstall /dev/sdf1 
    chmod 600 /dev/sdc1 
    chmod 600 /dev/sdd1 
    chmod 600 /dev/sde1 
    chmod 600 /dev/sdf1
    ```

## <a name="download-and-prepare-oracle-grid-infrastructure"></a>Baixe e prepare a infraestrutura em grade do Oracle

Para baixar e preparar o software de infraestrutura em grade do Oracle, conclua as seguintes etapas:

1. Baixe a infraestrutura em grade do Oracle da [página de download do Oracle ASM](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-linux-download-2240591.html). 

   No download intitulado **Banco de Dados Oracle 12c versão 1 Infraestrutura em Grade (12.1.0.2.0) para Linux x86-64**, baixe os dois arquivos .zip.

2. Depois de baixar os arquivos .zip para o computador cliente, você poderá usar o protocolo SCP para copiar os arquivos na VM:

   ```bash
   scp *.zip <publicIpAddress>:.
   ```

3. SSH de volta para a Oracle VM no Azure a fim de mover os arquivos .zip para a pasta /opt. Em seguida, altere o proprietário dos arquivos:

   ```bash
   ssh <publicIPAddress>
   sudo mv ./*.zip /opt
   cd /opt
   sudo chown grid:oinstall linuxamd64_12102_grid_1of2.zip
   sudo chown grid:oinstall linuxamd64_12102_grid_2of2.zip
   ```

4. Descompacte os arquivos. (Instale a ferramenta de descompactação do Linux se ela ainda não estiver instalada.)
   
   ```bash
   sudo yum install unzip
   sudo unzip linuxamd64_12102_grid_1of2.zip
   sudo unzip linuxamd64_12102_grid_2of2.zip
   ```

5. Alterar permissão:
   
   ```bash
   sudo chown -R grid:oinstall /opt/grid
   ```

6. Atualize o espaço de troca configurado. Os componentes do Oracle Grid precisam de, pelo menos, 6,8 GB de espaço de troca para instalar o Grid. O tamanho do arquivo de permuta padrão para as imagens da Oracle Linux no Azure é de apenas 2.048 MB. Você precisa aumentar o `ResourceDisk.SwapSizeMB` no arquivo `/etc/waagent.conf` e reiniciar o serviço WALinuxAgent para que as configurações atualizadas entrem em vigor. Como ele é um arquivo somente leitura, você precisa alterar as permissões de arquivo para habilitar o acesso de gravação.

   ```bash
   sudo chmod 777 /etc/waagent.conf  
   vi /etc/waagent.conf
   ```
   
   Pesquise o `ResourceDisk.SwapSizeMB` e altere o valor para **8.192**. Será necessário pressionar `insert` para entrar no modo de inserção, digitar o valor de **8.192** e, em seguida, pressionar `esc` para retornar ao modo de comando. Para gravar as alterações e encerre o arquivo, digite `:wq` e pressione `enter`.
   
   > [!NOTE]
   > É altamente recomendável que você sempre use o `WALinuxAgent` para configurar o espaço de troca, de forma que ele seja sempre criado no disco efêmero local (disco temporário) para um desempenho melhor. Para obter mais informações sobre isso, consulte [Como adicionar um arquivo de permuta em máquinas virtuais Linux do Azure](https://support.microsoft.com/en-us/help/4010058/how-to-add-a-swap-file-in-linux-azure-virtual-machines).

## <a name="prepare-your-local-client-and-vm-to-run-x11"></a>Preparar seu cliente local e a VM para executar x11
A configuração do Oracle ASM exige uma interface gráfica para concluir a instalação e configuração. Estamos usando o protocolo x11 para facilitar essa instalação. Se você estiver usando um sistema de cliente (Mac ou Linux) que já tenha os recursos X11 habilitados e configurados, você poderá ignorar esta configuração e instalação exclusivas para computadores Windows. 

1. [Baixe o PuTTY](https://www.putty.org/) e o [Xming](https://xming.en.softonic.com/) em seu computador Windows. Você precisará concluir a instalação desses dois aplicativos com os valores padrão antes de continuar.

2. Depois de instalar o PuTTY, abra um prompt de comando, altere para a pasta PuTTY (por exemplo, C:\Program Files\PuTTY) e execute `puttygen.exe` para gerar uma chave.

3. No Gerador de Chave PuTTY:
   
   1. Gere uma chave selecionando o botão `Generate`.
   2. Copie o conteúdo da chave (Ctrl+C).
   3. Selecione o botão `Save private key`.
   4. Ignore o aviso sobre proteger a chave com uma frase secreta e, em seguida, selecione `OK`.

   ![Captura de tela do Gerador de Chaves PuTTY](./media/oracle-asm/puttykeygen.png)

4. Em sua VM, execute estes comandos:

   ```bash
   sudo su - grid
   mkdir .ssh 
   cd .ssh
   ```

5. Crie um arquivo chamado `authorized_keys`. Cole o conteúdo da chave nesse arquivo e salve-o.

   > [!NOTE]
   > A chave deve conter a cadeia de caracteres `ssh-rsa`. Além disso, o conteúdo da chave deve ser uma única linha de texto.
   >  

6. No sistema cliente, inicie o PuTTY. No painel **Categoria**, vá para **Conexão** > **SSH** > **Autenticação**. Na caixa **Arquivo de chave privada para autenticação**, navegue até a chave que você gerou anteriormente.

   ![Captura de tela das opções de autenticação SSH](./media/oracle-asm/setprivatekey.png)

7. No painel **Categoria**, vá para **Conexão** > **SSH** > **X11**. Selecione a caixa de seleção **Habilitar encaminhamento X11**.

   ![Captura de tela das opções de encaminhamento X11 do SSH](./media/oracle-asm/enablex11.png)

8. No painel **Categoria**, vá para **Sessão**. Insira sua VM Oracle ASM `<publicIPaddress>` na caixa de diálogo nome do host, preencha um novo nome `Saved Session` e, em seguida, clique em `Save`.  Uma vez salvo, clique em `open` para se conectar à sua máquina virtual Oracle ASM.  Na primeira vez que se conectar você será avisado de que o sistema remoto não está em cache no registro. Clique em `yes` para adicioná-lo e continuar.

   ![Captura de tela das opções de sessão do PuTTY](./media/oracle-asm/puttysession.png)

## <a name="install-oracle-grid-infrastructure"></a>Instalar a infraestrutura em grade do Oracle

Para instalar a infraestrutura em grade do Oracle, conclua as seguintes etapas:

1. Entre como **grade**. (Você deve ser capaz de entrar sem inserir uma senha.) 

   > [!NOTE]
   > Se estiver executando o Windows, verifique se você iniciou o Xming antes de começar a instalação.

   ```bash
   cd /opt/grid
   ./runInstaller
   ```

   O instalador da Infraestrutura em grade do Oracle 12c versão 1 é aberto. (Pode levar alguns minutos para iniciar o instalador.)

2. Na página **Selecionar Opção de Instalação** , selecione **Instalar e Configurar a Infraestrutura em grade do Oracle para um Servidor Autônomo**.

   ![Captura de tela da página Selecionar opção de instalação do instalador](./media/oracle-asm/install01.png)

3. Na página **Selecionar Idiomas do Produto**, verifique se **Inglês** ou o idioma desejado está selecionado.  Clique em `next`.

4. Na página **Criar grupo de discos ASM**:
   - Insira um nome para o grupo de discos.
   - Em **Redundância**, selecione **Externa**.
   - Em **Tamanho da Unidade de Alocação**, selecione **4**.
   - Em **Adicionar Discos**, selecione **ORCLASMSP**.
   - Clique em `next`.

5. Na página **Especificar Senha ASM**, selecione a opção **Usar as mesmas senhas para essas contas** e digite uma senha.

   ![Captura de tela da página Especificar Senha de ASM do instalador](./media/oracle-asm/install04.png)

6. Na página **Especificar opções de gerenciamento**, você tem a opção de configurar o controle de nuvem EM. Vamos ignorar essa opção – clique em `next` para continuar. 

7. Na página **Grupos Privilegiados do Sistema Operacional**, use as configurações padrão. Clique em `next` para continuar.

8. Na página **Especificar Local de Instalação**, use as configurações padrão. Clique em `next` para continuar.

9. Na página **Criar inventário**, altere o diretório de inventário para `/u01/app/grid/oraInventory`. Clique em `next` para continuar.

   ![Captura de tela da página Criar Inventário do Instalador](./media/oracle-asm/install08.png)

10. Na página **Configuração de execução de script de raiz**, marque a caixa de seleção **Executar scripts de configuração automaticamente**. Em seguida, selecione a opção **Usar credenciais de usuário "raiz"** e digite a senha do usuário raiz.

    ![Captura de tela da página Configuração de execução de script de raiz do instalador](./media/oracle-asm/install09.png)

11. Na página **Realizar verificações de pré-requisito**, a instalação atual falhará com erros. Este é um comportamento esperado. Selecione `Fix & Check Again`.

12. Na caixa de diálogo **Script de Correção**, clique em `OK`.

13. Na página **Resumo**, examine suas configurações selecionadas e clique em `Install`.

    ![Captura de tela da página Resumo do instalador](./media/oracle-asm/install12.png)

14. Uma caixa de diálogo de aviso será exibida informando a você que os scripts de configuração precisam ser executados como um usuário com privilégios. Clique em `Yes` para continuar.

15. Na página **Concluir**, clique em `Close` para terminar a instalação.

## <a name="set-up-your-oracle-asm-installation"></a>Configurar a sua instalação do Oracle ASM

Para configurar a instalação do Oracle ASM, conclua as seguintes etapas:

1. Verifique se você ainda está conectado como **grade**, da sua sessão X11. Talvez seja necessário pressionar `enter` para reviver terminal. Em seguida, inicie o Assistente de configuração do Oracle Automated Storage Management:

   ```bash
   cd /u01/app/grid/product/12.1.0/grid/bin
   ./asmca
   ```

   O assistente de configuração do Oracle ASM é aberto.

2. Na caixa de diálogo **Configurar ASM: grupos de disco**, clique no botão `Create` e, em seguida, clique em `Show Advanced Options`.

3. Na caixa de diálogo **Criar Grupo de Discos**:

   - Insira o nome do grupo de disco **DATA**.
   - Em **Selecionar discos membros**, selecione **ORCL_DATA** e **ORCL_DATA1**.
   - Em **Tamanho da Unidade de Alocação**, selecione **4**.
   - Clique em `ok` para criar o grupo de discos.
   - Clique em `ok` para fechar a janela de confirmação.

   ![Captura de tela da caixa de diálogo Criar Grupo de Discos](./media/oracle-asm/asm02.png)

4. Na caixa de diálogo **Configurar ASM: grupos de disco**, clique no botão `Create` e, em seguida, clique em `Show Advanced Options`.

5. Na caixa de diálogo **Criar Grupo de Discos**:

   - Insira o nome do grupo de disco **FRA**.
   - Em **Redundância**, selecione **Externa (nenhuma)** .
   - Em **Selecionar discos membros**, selecione **ORCL_FRA**.
   - Em **Tamanho da Unidade de Alocação**, selecione **4**.
   - Clique em `ok` para criar o grupo de discos.
   - Clique em `ok` para fechar a janela de confirmação.

   ![Captura de tela da caixa de diálogo Criar Grupo de Discos](./media/oracle-asm/asm04.png)

6. Selecione **Sair** para fechar o assistente de configuração do ASM.

   ![Captura de tela da caixa de diálogo Configurar ASM: grupos de discos com o botão Sair](./media/oracle-asm/asm05.png)

## <a name="create-the-database"></a>Criar o banco de dados

O software do Oracle Database já está instalado na imagem do Azure Marketplace. Para criar um banco de dados, siga as seguintes etapas:

1. Mude usuários para o superusuário oracle e inicialize o ouvinte para registro em log:

   ```bash
   su - oracle
   cd /u01/app/oracle/product/12.1.0/dbhome_1/bin
   ./dbca
   ```
   O assistente de configuração do banco de dados é aberto.

2. Na página **Operação do Banco de dados**, clique em `Create Database`.

3. Na página **Modo de Criação**:

   - Insira um nome para o banco de dados.
   - Para **Tipo de armazenamento**, verifique se **ASM (Automatic Storage Management )** está selecionado.
   - Para **Local dos Arquivos de Banco de Dado**, use o local padrão sugerido pelo ASM.
   - Para **Área de recuperação rápida**, use o local padrão sugerido pelo ASM.
   - digite uma **Senha administrativa** e **confirme a senha**.
   - certifique-se de que `create as container database` esteja selecionado.
   - digite um valor `pluggable database name`.

4. Na página **Resumo**, examine suas configurações selecionadas e, em seguida, clique em `Finish` para criar o banco de dados.

   ![Captura de tela da página Resumo](./media/oracle-asm/createdb03.png)

5. O Banco de dados foi criado. Na página **Concluir**, você tem a opção de desbloquear contas adicionais para usar esse banco de dados e alterar as senhas. Se você quiser fazer isso, selecione **Gerenciamento de Senhas** – caso contrário, clique em `close`.

## <a name="delete-the-vm"></a>Excluir a VM

Você configurou com êxito o Oracle Automated Storage Management na imagem do Oracle DB do Azure Marketplace.  Quando essa VM não for mais necessária, o comando abaixo poderá ser usado para remover o grupo de recursos, a VM e todos os recursos relacionados:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

[Tutorial: Configurar o Oracle DataGuard](configure-oracle-dataguard.md)

[Tutorial: Configurar o Oracle GoldenGate](Configure-oracle-golden-gate.md)

Examine [Projetar um Oracle DB](oracle-design.md)
