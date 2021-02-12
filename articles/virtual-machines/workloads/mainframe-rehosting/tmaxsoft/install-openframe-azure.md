---
title: Instalar o TmaxSoft OpenFrame em máquinas virtuais do Azure
description: Saiba como configurar um ambiente OpenFrame no Azure adequado para desenvolvimento, demonstrações, testes ou cargas de trabalho de produção.
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.openlocfilehash: ad3dbd8cdc6fff7eca8c24bb6e5258022d52cb9b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96018487"
---
# <a name="install-tmaxsoft-openframe-on-azure"></a>Instalar o TmaxSoft OpenFrame no Azure

Saiba como configurar um ambiente OpenFrame no Azure adequado para desenvolvimento, demonstrações, testes ou cargas de trabalho de produção. Este tutorial orienta você em cada etapa.

O OpenFrame inclui vários componentes que criam o ambiente de emulação de mainframe no Azure. Por exemplo, OpenFrame serviços online substituir o middleware de mainframe, como o IBM Customer Information System (CICS) e o lote OpenFrame, por seu componente TJES, substitui o JES (subsistema de entrada de trabalho) do IBM Mainframe.

O OpenFrame funciona com qualquer banco de dados relacional, incluindo Oracle Database, Microsoft SQL Server, IBM DB2 e MySQL. Esta instalação do OpenFrame usa o banco de dados relacional do TmaxSoft Tibero. O OpenFrame e o Tibero são executados em um sistema operacional Linux. Este tutorial instala o CentOS 7,3, embora você possa usar outras distribuições do Linux com suporte. O servidor de aplicativos OpenFrame e o banco de dados Tibero são instalados em uma VM (máquina virtual).

O tutorial orienta você pela instalação dos componentes do OpenFrame Suite. Alguns devem ser instalados separadamente.

Principais componentes do OpenFrame:

- Pacotes de instalação necessários.
- Banco de dados Tibero.
- O ODBC (Open Database Connectivity) é usado por aplicativos no OpenFrame para se comunicar com o banco de dados Tibero.
- OpenFrame base, o middleware que gerencia todo o sistema.
- OpenFrame batch, a solução que substitui os sistemas de lote do mainframe.
- TACF, um módulo de serviço que controla o acesso do usuário aos sistemas e recursos.
- Classificar, uma ferramenta de classificação para transações em lotes.
- OFCOBOL, um compilador que interpreta os programas COBOL do mainframe.
- OFASM, um compilador que interpreta os programas de Assembler do mainframe.
- O tipo de servidor OpenFrame C (OSC), a solução que substitui o middleware do mainframe e o IBM CICS.
- JEUS (Java Enterprise User Solution), um servidor de aplicativos da Web que é certificado para o Java Enterprise Edition 6.
- OFGW, o componente de gateway OpenFrame que fornece um ouvinte 3270.
- OFManager, uma solução que fornece as funções de operação e gerenciamento do OpenFrame no ambiente da Web.

Outros componentes necessários do OpenFrame:

- OSI, a solução que substitui o middleware de mainframe e o IMS DC.
- TJES, a solução que fornece o ambiente JES do mainframe.
- OFTSAM, a solução que permite que (V) arquivos SAM sejam usados no sistema aberto.
- OFHiDB, a solução que substitui o IMS DB do mainframe.
- OFPLI, um compilador que interpreta os programas PL/I do mainframe.
- PROTRIEVE, uma solução que executa a CA-EasyTrieve da linguagem de mainframe.
- OFMiner, uma solução que analisa os ativos de mainframes e os migra para o Azure.

## <a name="architecture"></a>Arquitetura

A figura a seguir fornece uma visão geral dos componentes arquitetônicos do OpenFrame 7,0 instalados neste tutorial:

![Componentes do OpenFrame](media/openframe-02.png)

## <a name="azure-system-requirements"></a>Requisitos de sistema do Azure

A tabela a seguir lista os requisitos para a instalação no Azure.
<!-- markdownlint-disable MD033 -->

<table>
<thead>
    <tr><th>Requisito</th><th>Descrição</th></tr>
</thead>
<tbody>
<tr><td>Distribuições Linux com suporte no Azure
</td>
<td>
Linux x86 2,6 (32 bits, 64 bits)<br/>
Red Hat 7. x<br/>
CentOS 7. x<br/>
</td>
</tr>
<tr><td>Hardware
</td>
<td>Núcleos: 2 (mínimo)<br/>
Memória: 4 GB (mínimo)<br/>
Espaço de permuta: 1 GB (mínimo)<br/>
Disco rígido: 100 GB (mínimo)<br/>
</td>
</tr>
<tr><td>Software opcional para usuários do Windows
</td>
<td>Disparador: usado neste guia para configurar recursos de VM<br/>
WinSCP: um cliente SFTP e cliente de FTP populares que você pode usar<br/>
Eclipse para Windows: uma plataforma de desenvolvimento com suporte do TmaxSoft<br/>
(Não há suporte para a Microsoft Visual Studio no momento)
</td>
</tr>
</tbody>
</table>

<!-- markdownlint-enable MD033 -->

## <a name="prerequisites"></a>Pré-requisitos

Planeje gastar alguns dias para montar todo o software necessário e concluir todos os processos manuais.

Antes de começar, faça o seguinte:

- Obtenha a mídia de instalação do OpenFrame do TmaxSoft. Se você for um cliente TmaxSoft existente, entre em contato com seu representante do TmaxSoft para obter uma cópia licenciada. Caso contrário, solicite uma versão de avaliação de [TmaxSoft](https://www.tmaxsoft.com/contact/).

- Solicite a documentação do OpenFrame enviando um email para <support@tmaxsoft.com> .

- Obtenha uma assinatura do Azure se você ainda não tiver uma. Você também pode criar uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- Opcional. Configure um túnel VPN site a site ou um Jumpbox que restringe o acesso à VM do Azure aos usuários permitidos em sua organização. Essa etapa não é necessária, mas é uma prática recomendada.

## <a name="set-up-a-vm-on-azure-for-openframe-and-tibero"></a>Configurar uma VM no Azure para OpenFrame e Tibero

Você pode configurar o ambiente OpenFrame usando vários padrões de implantação, mas o procedimento a seguir mostra como implantar o servidor de aplicativos OpenFrame e o banco de dados Tibero em uma VM. Em ambientes maiores e para cargas de trabalho considerável, uma prática recomendada é implantar o banco de dados separadamente em sua própria VM para melhorar o desempenho.

**Para criar uma VM**

1. Vá para a portal do Azure em <https://portal.azure.com> e entre em sua conta.

2. Clique em **Máquinas Virtuais**.

    ![Lista de recursos no portal do Azure](media/vm-01.png)

3. Clique em **Adicionar**.

    ![Adicionar opção no portal do Azure](media/vm-02.png)

4. À direita dos **sistemas operacionais**, clique em **mais**.

     ![Mais opções no portal do Azure](media/vm-03.png)

5. Clique em **CentOS-based 7,3** para seguir este passo a passo exatamente, ou você pode escolher outra distribuição do Linux com suporte.

     ![Opções do sistema operacional no portal do Azure](media/vm-04.png)

6. Nas configurações **básicas** , digite **nome**, **nome de usuário**, **tipo de autenticação**, **assinatura** (pré-pago é o estilo de pagamento AWS) e grupo de **recursos** (use um existente ou crie um grupo TmaxSoft).

7. Ao concluir (incluindo o par de chaves pública/privada para o **tipo de autenticação**), clique em **Enviar**.

> [!NOTE]
> Se estiver usando uma chave pública SSH para o **tipo de autenticação**, consulte as etapas na próxima seção para gerar o par de chaves pública/privada e, em seguida, retome as etapas aqui.

### <a name="generate-a-publicprivate-key-pair"></a>Gerar um par de chaves pública/privada

Se você estiver usando um sistema operacional Windows, precisará de PuTTYgen para gerar um par de chaves pública/privada.

A chave pública pode ser compartilhada livremente, mas a chave privada deve ser mantida totalmente secreta e nunca deve ser compartilhada com outra entidade. Depois de gerar as chaves, você deve colar a **chave pública SSH** na configuração — em vigor, carregando-a para a VM do Linux. Ele é armazenado dentro \_ de chaves autorizadas dentro do \~ diretório/.ssh do diretório base da conta do usuário. Em seguida, a VM do Linux é capaz de reconhecer e validar a conexão assim que você fornecer a **chave privada SSH** associada no cliente SSH (em nosso caso, reproduzida).

Ao conceder a novos indivíduos acesso à VM: 

- Cada novo indivíduo gera suas próprias chaves públicas/privadas usando PuTTYgen.
- Os indivíduos armazenam suas próprias chaves privadas separadamente e enviam as informações de chave pública para o administrador da VM.
- O administrador cola o conteúdo da chave pública no \~ arquivo de chaves/.ssh/Authorized \_ .
- O novo indivíduo se conecta por meio de renovar.

**Para gerar um par de chaves pública/privada**

1.  Baixe o PuTTYgen de <https://www.putty.org/> e instale-o usando todas as configurações padrão.

2.  Para abrir o PuTTYgen, localize o diretório de instalação de saída na partição C: \\ arquivos de programas \\ .

    ![Interface de saída](media/puttygen-01.png)

3.  Clique em **Gerar**.

    ![Captura de tela que mostra a caixa de diálogo gerador de chave de geração e realça o botão gerar.](media/puttygen-02.png)

4.  Após a geração, salve a chave pública e a chave privada. Cole o conteúdo da chave pública na seção **chave pública SSH** do painel **criar \> noções básicas da máquina virtual** (mostrado nas etapas 6 e 7 na seção anterior).

    ![Caixa de diálogo gerador de chave de geração](media/puttygen-03.png)

### <a name="configure-vm-features"></a>Configurar recursos de VM

1. Em portal do Azure, na folha **escolher um tamanho** , escolha as configurações de hardware do computador Linux que você deseja. Os requisitos *mínimos* para a instalação de Tibero e OpenFrame são 2 CPUs e 4 GB de RAM, conforme mostrado nesta instalação de exemplo:

    ![Criar máquina virtual-noções básicas](media/create-vm-01.png)

2. Clique em **3 configurações** e use as configurações padrão para configurar recursos opcionais.
3. Examine os detalhes de pagamento.

    ![Criar máquina virtual-comprar](media/create-vm-02.png)

4. Envie suas seleções. O Azure começa a implantar a VM. Esse processo normalmente leva alguns minutos.

5. Quando a VM é implantada, seu painel é exibido, mostrando todas as configurações que foram selecionadas durante a configuração. Anote o **endereço IP público**.

    ![tmax no painel do Azure](media/create-vm-03.png)

6. Abra o PuTTY.

7. Para **nome do host**, digite seu nome de usuário e o endereço IP público que você copiou. Por exemplo, **nome de usuário \@ publicip**.

    ![Captura de tela que mostra a caixa de diálogo configuração de saída e realça o campo nome do host (ou endereço IP).](media/putty-01.png)

8. Na caixa **categoria** , clique em **conexão \> SSH \> autenticação**. Forneça o caminho para o arquivo de **chave privada** .

    ![Caixa de diálogo configuração de saída](media/putty-02.png)

9. Clique em **abrir** para iniciar a janela de saída. Se for bem-sucedido, você estará conectado à nova VM CentOS em execução no Azure.

10. Para fazer logon como usuário raiz, digite **sudo bash**.

    ![Logon de usuário raiz na janela de comando](media/putty-03.png)

## <a name="set-up-the-environment-and-packages"></a>Configurar o ambiente e os pacotes

Agora que a VM foi criada e você está conectado, você deve executar algumas etapas de instalação e instalar os pacotes de pré-instalação necessários.

1. Mapeie o nome **ofdemo** para o endereço IP local usando vi para editar o arquivo de hosts ( `vi /etc/hosts` ). Supondo que nosso IP seja 192.168.96.148 ofdemo, isso é antes da alteração:

    ```vi
    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4 
    ::1              localhost localhost.localdomain localhost6 localhost6.localdomain 
    <IP Address>    <your hostname>
    ```

     Isso ocorre após a alteração:

    ```vi
    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4 
    ::1              localhost localhost.localdomain localhost6 localhost6.localdomain 
    192.168.96.148   ofdemo
    ```

2. Criar grupos e usuários:

    ```vi
    [root@ofdemo ~]# adduser -d /home/oframe7 oframe7 
    [root@ofdemo ~]# passwd oframe7
    ```

3. Altere a senha para o usuário oframe7:

    ```vi
    New password: 
    Retype new password: 
    passwd: all authentication tokens updated successfully.
    ```

4. Atualize os parâmetros de kernel em/etc/sysctl.conf:

    ```vi
    [root@ofdemo ~]# vi /etc/sysctl.conf
    kernel.shmall = 7294967296 
    kernel.sem = 10000 32000 10000 10000
    ```

5. Atualize os parâmetros do kernel dinamicamente sem reinicialização:

    ```vi
    [root@ofdemo ~]# /sbin/sysctl -p
    ```

6. Obter os pacotes necessários: Verifique se o servidor está conectado à Internet, baixe os pacotes a seguir e instale-os:

     - dos2unix
     - glibc
     - glibc. i686 glibc. x86 \_ 64
     - libaio
     - ncurses

          > [!NOTE]
          > Depois de instalar o pacote ncurses, crie os seguintes links simbólicos:
         ```
         ln -s /usr/lib64/libncurses.so.5.9 /usr/lib/libtermcap.so
         ln -s /usr/lib64/libncurses.so.5.9 /usr/lib/libtermcap.so.2
         ```

     - gcc
     - gcc-c + +
     - libaio-desenvolvedor. x86 \_ 64
     - strace
     - ltrace
     - gdb

7. No caso da instalação do RPM do Java, faça o seguinte:

```
root@ofdemo ~]# rpm -ivh jdk-7u79-linux-x64.rpm
[root@ofdemo ~]# vi .bash_profile

# JAVA ENV
export JAVA_HOME=/usr/java/jdk1.7.0_79/
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=$CLASSPATH:$JAVA_HOME/jre/lib/ext:$JAVA_HOME/lib/tools.jar

[root@ofdemo ~]# source /etc/profile
[root@ofdemo ~]# java –version

java version "1.7.0_79"
Java(TM) SE Runtime Environment (build 1.7.0_79-b15)
Java HotSpot(TM) 64-Bit Server VM (build 24.79-b02, mixed mode)

[root@ofdemo ~]# echo $JAVA_HOME /usr/java/jdk1.7.0_79/
```

## <a name="install-the-tibero-database"></a>Instalar o banco de dados Tibero

O Tibero fornece as várias funções principais no ambiente OpenFrame no Azure:

- Tibero é usado como o armazenamento de dados interno OpenFrame para várias funções do sistema.
- Os arquivos do VSAM, incluindo KSDS, RRDS e ESDS, usam o banco de dados do Tibero internamente para armazenamento de arquivos.
- O repositório de dados TACF é armazenado em Tibero.
- As informações do catálogo OpenFrame são armazenadas em Tibero.
- O banco de dados Tibero pode ser usado como uma substituição para que o IBM DB2 armazene o aplicativo.

**Para instalar o Tibero**

1. Verifique se o arquivo do instalador binário Tibero está presente e examine o número de versão.
2. Copie o software Tibero para a conta de usuário do Tibero (oframe). Por exemplo:

    ```
    [oframe7@ofdemo ~]$ tar -xzvf tibero6-bin-6_rel_FS04-linux64-121793-opt-tested.tar.gz 
    [oframe7@ofdemo ~]$ mv license.xml /opt/tmaxdb/tibero6/license/
    ```

3. Abra \_ o perfil. bash em vi `vi .bash_profile` () e cole o seguinte:

    ```
    # Tibero6 ENV
    export TB_HOME=/opt/tmaxdb/tibero6 
    export TB_SID=TVSAM export TB_PROF_DIR=$TB_HOME/bin/prof 
    export LD_LIBRARY_PATH=$TB_HOME/lib:$TB_HOME/client/lib:$LD_LIBRARY_PATH 
    export PATH=$TB_HOME/bin:$TB_HOME/client/bin:$PATH
    ```

4. Para executar o perfil bash, no prompt de comando, digite:

    ```
    source .bash_profile
    ```

5. Gere o arquivo Tip (um arquivo de configuração para Tibero) e abra-o em vi. Por exemplo:

    ```
    [oframe7@ofdemo ~]$ sh $TB_HOME/config/gen_tip.sh
    [oframe7@ofdemo ~]$ vi $TB_HOME/config/$TB_SID.tip
    ```

6. Modifique \$ TB \_ Home/Client/config/tbdsn. TBR e coloque 127.0.0.1 em vez de oflocalhost, conforme mostrado:

    ```
    TVSAM=( 
    (INSTANCE=(HOST=127.0.0.1)
        (PT=8629)
    (DB_NAME=TVSAM)
          )
     )
    ```

7. Crie o banco de dados. O seguinte resultado é exibido:

    ```
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof.
    Listener port = 8629
    Tibero 6
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NOMOUNT mode).
     /--------------------- newmount sql ------------------------/
    create database character set MSWIN949 national character set UTF16;
    /-----------------------------------------------------------/
    Database created.
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof.
    Listener port = 8629
    Tibero 6
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NORMAL mode).
    /opt/tmaxdb/tibero6/bin/tbsvr
    ………………………..
    Creating agent table...
    Done.
    For details, check /opt/tmaxdb/tibero6/instance/TVSAM/log/system_init.log.
    ************************************************** 
    * Tibero Database TVSAM is created successfully on Fri Aug 12 19:10:43 UTC 2016.
    *     Tibero home directory ($TB_HOME) =
    *         /opt/tmaxdb/tibero6
    *     Tibero service ID ($TB_SID) = TVSAM
    *     Tibero binary path =
    *         /opt/tmaxdb/tibero6/bin:/opt/tmaxdb/tibero6/client/bin
    *     Initialization parameter file =
    *         /opt/tmaxdb/tibero6/config/TVSAM.tip
    * 
    * Make sure that you always set up environment variables $TB_HOME and
    * $TB_SID properly before you run Tibero.
     ******************************************************************************
    ```

8. Para reciclar o Tibero, primeiro desligue-o usando o `tbdown` comando. Por exemplo:

    ```
    [oframe7@ofdemo ~]$$ tbdown 
    Tibero instance terminated (NORMAL mode).
    ```

9. Agora, inicialize o Tibero usando `tbboot` . Por exemplo:

    ```
    [oframe7@ofdemo ~]$ tbboot
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof. Listener port = 8629

    Tibero 6  
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NORMAL mode).
    ```

10. Para criar um espaço de tabela, acesse o banco de dados usando o usuário SYS (sys/tmax) e crie o espaço de tabela necessário para o volume padrão e TACF:

    ```
    [oframe7@ofdemo ~]$ tbsql tibero/tmax
    tbSQL 6  
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Connected to Tibero.
    ```

11. Agora, digite os seguintes comandos SQL:

    ```
    SQL> create tablespace "DEFVOL" datafile 'DEFVOL.dbf' size 500M autoextend on; create tablespace "TACF00" datafile 'TACF00.dbf' size 500M autoextend on; create tablespace "OFM_REPOSITORY" datafile 'ofm_repository.dbf' size 300M autoextend on;
    SQL> Tablespace 'DEFVOL' created.
    SQL> Tablespace 'TACF00' created.
    SQL> Tablespace ' OFM_REPOSITORY ' created.
    SQL> SQL> Disconnected.
    ```

12. Inicialize o Tibero e verifique se os processos Tibero estão em execução:

    ```
    [oframe7@ofdemo ~]$ tbboot 
    ps -ef | egrep tbsvr
    ```

Saída:

![Saída de Tibero](media/tibero-01.png)

## <a name="install-odbc"></a>Instalar o ODBC

Os aplicativos no OpenFrame se comunicam com o banco de dados Tibero usando a API ODBC fornecida pelo projeto unixODBC de código-fonte aberto.

Para instalar o ODBC:

1. Verifique se o arquivo do instalador unixODBC-2.3.4. tar. gz está presente ou use o `wget unixODBC-2.3.4.tar.gz` comando. Por exemplo:

     ```
     [oframe7@ofdemo ~]$ wget ftp://ftp.unixodbc.org/pub/unixODBC/unixODBC-2.3.4.tar.gz
     ```

2. Descompacte o binário. Por exemplo:

     ```
     [oframe7@ofdemo ~]$ tar -zxvf unixODBC-2.3.4.tar.gz
     ```

3. Navegue até o diretório unixODBC-2.3.4 e gere o makefile usando a verificação de informações do computador. Por exemplo:

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ ./configure --prefix=/opt/tmaxapp/unixODBC/ --sysconfdir=/opt/tmaxapp/unixODBC/etc
     ```

     Por padrão, o unixODBC é instalado em/usr/local, portanto, `--prefix` passa um valor para alterar o local. Da mesma forma, os arquivos de configuração são instalados em/etc por padrão, portanto, `--sysconfdir` passa o valor do local desejado.

4. Executar makefile: `[oframe7@ofdemo unixODBC-2.3.4]$ make`

5. Copie o arquivo executável no diretório do programa após a compilação. Por exemplo:

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ make install
     ```

6. Use vi para editar o perfil bash ( `vi ~/.bash_profile` ) e adicione o seguinte:

     ```
     # UNIX ODBC ENV 
     export ODBC_HOME=$HOME/unixODBC 
     export PATH=$ODBC_HOME/bin:$PATH 
     export LD_LIBRARY_PATH=$ODBC_HOME/lib:$LD_LIBRARY_PATH 
     export ODBCINI=$HOME/unixODBC/etc/odbc.ini 
     export ODBCSYSINI=$HOME
     ```

7. Aplique o ODBC. Edite os seguintes arquivos adequadamente. Por exemplo:

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ source ~/.bash_profile

     [oframe7@ofdemo ~]$ cd

     [oframe7@ofdemo ~]$ odbcinst -j unixODBC 2.3.4
     DRIVERS............: /home/oframe7/odbcinst.ini
     SYSTEM DATA SOURCES: /home/oframe7/odbc.ini
     FILE DATA SOURCES..: /home/oframe7/ODBCDataSources
     USER DATA SOURCES..: /home/oframe7/unixODBC/etc/odbc.ini
     SQLULEN Size.......: 8
     SQLLEN Size........: 8
     SQLSETPOSIROW Size.: 8

     [oframe7@ofdemo ~]$ vi odbcinst.ini

     [Tibero]
     Description = Tibero ODBC driver for Tibero6
     Driver = /opt/tmaxdb/tibero6/client/lib/libtbodbc.so
     Setup = 
     FileUsage = 
     CPTimeout = 
     CPReuse = 
     Driver Logging = 7

     [ODBC]
     Trace = NO 
     TraceFile = /home/oframe7/odbc.log 
     ForceTrace = Yes 
     Pooling = No 
     DEBUG = 1

     [oframe7@ofdemo ~]$ vi odbc.ini

     [TVSAM]
     Description = Tibero ODBC driver for Tibero6 
     Driver = Tibero 
     DSN = TVSAM 
     SID = TVSAM 
     User = tibero 
     password = tmax
     ```

8. Crie um link simbólico e valide a conexão de banco de dados Tibero:

     ```
     [oframe7@ofdemo ~]$ ln $ODBC_HOME/lib/libodbc.so $ODBC_HOME/lib/libodbc.so.1 [oframe7@ofdemo ~]$ ln $ODBC_HOME/lib/libodbcinst.so 
     $ODBC_HOME/lib/libodbcinst.so.1

     [oframe7@ofdemo lib]$ isql TVSAM tibero tmax
     ```

É exibida a saída a seguir:

![Saída de ODBC mostrando conectada ao SQL](media/odbc-01.png)

## <a name="install-openframe-base"></a>Instalar OpenFrame base

O servidor de aplicativos base é instalado antes dos serviços individuais que o OpenFrame usa para gerenciar o sistema no Azure, incluindo os processos de servidor de manipulação de transações.

**Para instalar o OpenFrame base**

1. Verifique se a instalação do Tibero foi bem-sucedida e verifique se o arquivo de configuração do OpenFrame \_ Base7 \_ 0 \_ Linux \_ x86 \_ 64. bin e arquivo base. Properties estão presentes a seguir.

2. Atualize o perfil bash com as seguintes informações específicas do Tibero:

     ```bash
     alias ofhome='cd $OPENFRAME_HOME'
     alias ulog='cd $OPENFRAME_HOME/log/tmax/ulog'
     alias sysjcl='cd $OPENFRAME_HOME/volume_default/SYS1.JCLLIB'
     alias sysload='cd $OPENFRAME_HOME/volume_default/SYS1.LOADLIB'
     alias sysproc='cd $OPENFRAME_HOME/volume_default/SYS1.PROCLIB'
     alias oscsrc='cd $OPENFRAME_HOME/osc/oivp'
     alias osisrc='cd $OPENFRAME_HOME/osi/oivp'
     alias defvol='cd $OPENFRAME_HOME/volume_default'
     ```

3. Execute o perfil bash:`[oframe7@ofdemo ~]$ . .bash_profile`
4. Verifique se os processos Tibero estão em execução. Por exemplo:

     ```linux
     [oframe7@ofdemo ~]$ ps -ef|grep tbsvr
     ```

    ![Base](media/base-01.png)

     > [!IMPORTANT]
     > Certifique-se de iniciar o Tibero antes da instalação.

5. Gere a licença em [TechNet.TmaxSoft.com](https://technet.tmaxsoft.com/en/front/main/main.do) e coloque as licenças OpenFrame base, Batch, TACF, OSC na pasta apropriada:

     ```
     [oframe7@ofdemo ~]$ cp license.dat /opt/tmaxapp/OpenFrame/core/license/
     [oframe7@ofdemo ~]$ cp lictjes.dat lictacf.dat licosc.dat $OPENFRAME_HOME/license/
     ```

6. Baixe os arquivos binários base OpenFrame e base. Properties:

     ```
     [oframe7@ofdemo ~]$ vi base.properties
     OPENFRAME_HOME= <appropriate location for installation> ex. /opt/tmaxapp/OpenFrame TP_HOST_NAME=<your IP Hostname> ex. ofdemo 
     TP_HOST_IP=<your IP Address> ex. 192.168.96.148 
     TP_SHMKEY=63481 
     TP_TPORTNO=6623 
     TP_UNBLOCK_PORT=6291 
     TP_NODE_NAME=NODE1 
     TP_NODE_LIST=NODE1 
     MASCAT_NAME=SYS1.MASTER.ICFCAT 
     MASCAT_CREATE=YES 
     DEFAULT_VOLSER=DEFVOL 
     VOLADD_DEFINE=YES TSAM_USERNAME=tibero 
     TSAM_PASSWORD=tmax 
     TSAM_DATABASE=oframe 
     DATASET_SHMKEY=63211 
     DSLOCK_DATA=SYS1.DSLOCK.DATA 
     DSLOCK_LOG=SYS1.DSLOCK.LOG 
     DSLOCK_SEQ=dslock_seq.dat 
     DSLOCK_CREATE=YES 
     OPENFRAME_LICENSE_PATH=/opt/tmaxapp/license/OPENFRAME TMAX_LICENSE_PATH=/opt/tmaxapp/license/TMAX
     ```

7. Execute o instalador usando o arquivo base. Properties. Por exemplo:

    ```
    [oframe7@ofdemo ~]$ chmod a+x OpenFrame_Base7_0_Linux_x86_64.bin 
    [oframe7@ofdemo ~]$ ./OpenFrame_Base7_0_Linux_x86_64.bin -f base.properties
    ```

    Ao concluir, a mensagem instalação concluída é ditocada.

8. Verifique a estrutura do diretório base do OpenFrame usando o `ls -ltr` comando. Por exemplo:

     ```
     [oframe7@ofdemo OpenFrame]$ ls -ltr
     total 44

     drwxrwxr-x. 4 oframe7 oframe7 61 Nov 30 16:57 UninstallerData 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 bin 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 cpm drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 data 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 include 
     drwxrwxr-x. 2 oframe7 oframe7 8192 Nov 30 16:57 lib 
     drwxrwxr-x. 6 oframe7 oframe7 48 Nov 30 16:57 log 
     drwxrwxr-x. 2 oframe7 oframe7 6 Nov 30 16:57 profile 
     drwxrwxr-x. 7 oframe7 oframe7 62 Nov 30 16:57 sample 
     drwxrwxr-x. 2 oframe7 oframe7 6 Nov 30 16:57 schema 
     drwxrwxr-x. 2 oframe7 oframe7 6 Nov 30 16:57 temp 
     drwxrwxr-x. 3 oframe7 oframe7 16 Nov 30 16:57 shared 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:58 license 
     drwxrwxr-x. 23 oframe7 oframe7 4096 Nov 30 16:58 core 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:58 config 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:58 scripts 
     drwxrwxr-x. 2 oframe7 oframe7 25 Nov 30 16:58 volume_default
     ```

9. Iniciar base do OpenFrame:

     ```
     [oframe7@ofdemo ~]$ cp /usr/lib/libtermcap.so.2 $TMAXDIR/lib
     Startup Tmax Server
     [oframe7@ofdemo ~]$ tmboot
     ```

     ![saída do comando tmboot](media/base-02.png)

10. Verifique se o status do processo está pronto usando o comando tmadmin em si. RDY é exibido na coluna **status** para cada um dos processos:

     ![saída do comando tmadmin](media/base-03.png)

11. Desligar base do OpenFrame:

     ```
     [oframe7@ofdemo ~]$ tmdown 
     Do you really want to down whole Tmax? (y : n): y

     TMDOWN for node(NODE1) is starting: 
     TMDOWN: SERVER(ofrsasvr:36) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrdsedt:39) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(vtammgr:43) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrcmsvr:40) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrdmsvr:38) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrlhsvr:37) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofruisvr:41) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrsmlog:42) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: CLH downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: CLL downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: TLM downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: TMM downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: TMAX is down
     ```

## <a name="install-openframe-batch"></a>Instalar o lote OpenFrame

O OpenFrame batch consiste em vários componentes que simulam ambientes de lote de mainframe e são usados para executar trabalhos em lotes no Azure.

**Para instalar o lote**

1. Verifique se a instalação básica foi bem-sucedida e, em seguida, verifique se o arquivo de \_ \_ configuração de Batch. Properties do OpenFrame Batch7 0 \_ Fix2 \_ MVS \_ Linux \_ x86 \_ 64. bin está presente:

2. No prompt de comando, digite `vi batch.properties` para editar o arquivo batch. Properties usando vi.

3. Modifique os parâmetros da seguinte maneira:

     ```
     OPENFRAME_HOME = /opt/tmaxapp/OpenFrame
     DEFAULT_VOLSER=DEFVOL 
     TP_NODE_NAME=NODE1 
     TP_NODE_LIST=NODE1 
     RESOURCE_SHMKEY=66991 
     #JOBQ_DATASET_CREATE=YES 
     #OUTPUTQ_DATASET_CREATE=YES 
     DEFAULT_JCLLIB_CREATE=YES 
     DEFAULT_PROCLIB_CREATE=YES 
     DEFAULT_USERLIB_CREATE=YES 
     TJES_USERNAME=tibero 
     TJES_PASSWORD=tmax 
     TJES_DATABASE=oframe 
     BATCH_TABLE_CREATE=YES
     ```

4. Para executar o instalador do lote, no prompt de comando, digite:

     ```
     ./OpenFrame_Batch7_0_Fix2_MVS_Linux_x86_64.bin -f batch.properties
     ```

5. Quando a instalação for concluída, inicie os conjuntos de OpenFrame instalados digitando `tmboot` no prompt de comando.

    ![saída de tmboot](media/tmboot-01.png)

6. Digite `tmadmin` no prompt de comando para verificar o processo OpenFrame.

    ![Tela de administração do tmax](media/tmadmin-01.png)

7. Execute os seguintes comandos:

     ```
     $$2 NODE1 (tmadm): quit 
     ADM quit for node (NODE1)
     ```

8. Use o `tmdown` comando para iniciar e desligar o lote:

     ```
     [oframe7@ofdemo ~]$tmdown
     Do you really want to down whole Tmax? (y : n): y

     TMDOWN for node(NODE1) is starting: 
     TMDOWN: SERVER(ofrsasvr:36) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:44) downed: Wed Sep  7 16:01:46 2016
     TMDOWN: SERVER(vtammgr: 43) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrcmsvr:40) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:45) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:46) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrdmsvr:38) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:47) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrdsedt:39) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjschd:54) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjinit:55) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:48) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjspbk:57) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:49) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:50) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:51) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrlhsvr:37) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:52) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:53) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjhist:56) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofruisvr:41) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmtsmgr:59) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrpmsvr:58) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrsmlog:42) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: CLL downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: TLM downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: CLH downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: TMM downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: TMAX is down
     ```

## <a name="install-tacf"></a>Instalar o TACF

TACF Manager é um módulo de serviço OpenFrame que controla o acesso do usuário a sistemas e recursos por meio do RACF Security.

**Para instalar o TACF**

1. Verifique se o arquivo \_ do \_ instalador OpenFrame Tacf7 0 \_ Fix2 \_ Linux \_ x86 \_ 64. bin e o arquivo de configuração TACF. Properties estão presentes.
2. Verifique se a instalação do lote foi bem-sucedida e, em seguida, use vi para abrir o arquivo TACF. Properties ( `vi tacf.properties` ).
3. Modifique os parâmetros de TACF:

     ```
     OPENFRAME_HOME=/opt/tmaxapp/OpenFrame 
     USE_OS_AUTH=NO 
     TACF_USERNAME=tibero 
     TACF_PASSWORD=tmax 
     TACF_DATABASE=oframe 
     TACF_TABLESPACE=TACF00 
     TACF_TABLE_CREATE=YES
     ```

4. Depois de concluir o instalador do TACF, aplique as variáveis de ambiente TACF. No prompt de comando, digite:

     ```
     source \~/.bash\_profile
     ```

5. Execute o instalador do TACF. No prompt de comando, digite:

     ```
     ./OpenFrame_Tacf7_0_Fix2_Linux_x86_64.bin -f tacf.properties
     ```

     A saída é parecida com esta:

     ```
     Wed Dec 07 17:36:42 EDT 2016
     Free Memory: 18703 kB 
     Total Memory: 28800 kB

     4 Command Line Args: 
     0:  -f 1:  tacf.properties 
     2:  -m 
     3:  SILENT 
     java.class.path: 
     /tmp/install.dir.41422/InstallerData 
     /tmp/install.dir.41422/InstallerData/installer.zip 
     ZGUtil.CLASS_PATH: 
     /tmp/install.dir.41422/InstallerData 
     tmp/install.dir.41422/InstallerData/installer.zip 
     sun.boot.class.path: 
     /tmp/install.dir.41422/Linux/resource/jre/lib/resources.jar /tmp/install.dir.41422/Linux/resource/jre/lib/rt.jar /tmp/install.dir.41422/Linux/resource/jre/lib/sunrsasign.jar /tmp/install.dir.41422/Linux/resource/jre/lib/jsse.jar /tmp/install.dir.41422/Linux/resource/jre/lib/jce.jar /tmp/install.dir.41422/Linux/resource/jre/lib/charsets.jar /tmp/install.dir.41422/Linux/resource/jre/lib/jfr.jar /tmp/install.dir.41422/Linux/resource/jre/classes
     ```

6. No prompt de comando, digite `tmboot` para reiniciar o OpenFrame. A saída é parecida com esta:

     ```
     TMBOOT for node(NODE1) is starting: 
     Welcome to Tmax demo system: it will expire 2016/11/4 
     Today: 2016/9/7 
     TMBOOT: TMM is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: CLL is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: CLH is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: TLM(tlm) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrsasvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrlhsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrdmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrdsedt) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrcmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofruisvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrsmlog) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(vtammgr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjschd) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjinit) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjhist) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjspbk) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrpmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmtsmgr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(tmsvr) is starting: Wed Sep  7 17:48:53 2016
     ```

7. Verifique se o status do processo está pronto usando `tmadmin` no `si` comando. Por exemplo:

     ```
     [oframe7\@ofdemo \~]\$ tmadmin
     ```

     Na coluna **status** , RDY aparece:

    ![RDY na coluna status](media/tmboot-02.png)

8. Execute os seguintes comandos:

     ```
     $$2 NODE1 (tmadm): quit 
     DM quit for node (NODE1)

     [oframe7@ofdemo ~]$ tacfmgr 
     Input USERNAME  : ROOT 
     Input PASSWORD  : SYS1

     TACFMGR: TACF MANAGER START!!!
     QUIT TACFMGR: TACF MANAGER END!!!

     [oframe7@ofdemo ~]$ tmdow
     ```

9. Desligue o servidor usando o `tmdown` comando. A saída é parecida com esta:

     ```
     [oframe7@ofdemo ~]$ tmdown 
     Do you really want to down whole Tmax? (y : n): y

     TMDOWN for node(NODE1) is starting: 
     TMDOWN: SERVER(ofrlhsvr:37) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrdsedt:39) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjschd:54) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjmsvr:47) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjmsvr:48) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrdmsvr:38) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjmsvr:50) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjhist:56) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrsasvr:36) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrcmsvr:40) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjspbk:57) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(tmsvr:60) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrpmsvr:58) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmtsmgr:59) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: CLL downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: CLH downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: TLM downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: TMM downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: TMAX is down
     ```

## <a name="install-prosort"></a>Instalar o sortimento

O sortimento é um utilitário usado em transações em lote para classificar dados.

**Para instalar o prosort**

1. Verifique se a instalação do lote foi bem-sucedida e verifique se o arquivo do instalador **prosort-bin-prosort \_ 2sp3-Linux64-2123-opt. tar. gz** está presente.

2. Execute o instalador usando o arquivo de propriedades. No prompt de comando, digite:

     ```
     tar -zxvf prosort-bin-prosort\_2sp3-linux64-2123-opt.tar.gz
     ```

3. Mova o diretório de classificação para o local inicial. No prompt de comando, digite:

     ```
     mv prosort /opt/tmaxapp/prosort
     ```

4. Crie um subdiretório de licença e copie o arquivo de licença para lá. Por exemplo:

     ```
     cd /opt/tmaxapp/prosort 
     mkdir license 
     cp /opt/tmaxsw/oflicense/prosort/license.xml /opt/tmaxapp/prosort/license
     ```

5. Abra bash. Profile em vi ( `vi .bash_profile` ) e atualize-o da seguinte maneira:

     ```bash
     #       PROSORT

     PROSORT_HOME=/opt/tmaxapp/prosort 
     PROSORT_SID=gbg 
     PATH=$PATH:$PROSORT_HOME/bin LD_LIBRARY_PATH=$PROSORT_HOME/lib:$LD_LIBRARY_PATH LIBPATH$PROSORT_HOME/lib:$LIBPATH 
     export PROSORT_HOME PROSORT_SID 
     PATH LD_LIBRARY_PATH LIBPATH 
     PATH=$PATH:$OPENFRAME_HOME/shbin 
     export PATH
     ```

6. Para executar o perfil bash, no prompt de comando, digite: `. .bash_profile`

7. Crie o arquivo de configuração. Por exemplo:

     ```
     oframe@oframe7: cd /opt/tmaxapp/prosort/config 
     oframe@oframe7: ./gen_tip.sh 
     Using PROSORT_SID "gbg"
      /home/oframe7/prosort/config/gbg.tip generated
     ```

8. Crie o link simbólico. Por exemplo:

     ```
     oframe@oframe7: cd /opt/tmaxapp/OpenFrame/util/ 
     oframe@oframe7home/oframe7/OpenFrame/util :  ln -s DFSORT SORT
     ```

9. Verifique a instalação de proclassificação executando o `prosort -h` comando. Por exemplo:

     ```
     oframe@oframe7: prosort -h

     Usage: prosort [options] [sort script files]
     options ------
     -h             Display this information 
     -v             Display version information 
     -s             Display state information 
     -j             Display profile information 
     -x             Use SyncSort compatible mode
     ```

## <a name="install-ofcobol"></a>Instalar o OFCOBOL

OFCOBOL é o compilador OpenFrame que interpreta os programas COBOL do mainframe. 

**Para instalar o OFCOBOL**

1. Verifique se a instalação do lote/online foi bem-sucedida e, em seguida, verifique se o \_ arquivo do instalador OpenFrame COBOL3 \_ 0 \_ 40 \_ Linux \_ x86 \_ 64. bin está presente.

2. Para executar o instalador do OFCOBOL, no prompt de comando, digite:

     ```
      ./OpenFrame\_COBOL3\_0\_40\_Linux\_x86\_64.bin
     ```

3. Leia o contrato de licenciamento e pressione ENTER para continuar.

4. Aceite o contrato de licenciamento. Quando a instalação for concluída, o seguinte será exibido:

     ```
     Choose Install Folder 
     --------------------
     Where would you like to install?
     Default Install Folder: /home/oframe7/OFCOBOL

     ENTER AN ABSOLUTE PATH, OR PRESS <ENTER> TO ACCEPT THE DEFAULT : /opt/tmaxapp/OFCOBOL

     INSTALL FOLDER IS: /opt/tmaxapp/OFCOBOL 
     IS THIS CORRECT? (Y/N): Y[oframe7@ofdemo ~]$ vi .bash_profile

     ============================================================================ Installing... 
     ------------
     [==================|==================|==================|==================] 
     [------------------|------------------|------------------|------------------]

     =============================================================================== Installation Complete 
     --------------------
     Congratulations. OpenFrame_COBOL has been successfully installed
     PRESS <ENTER> TO EXIT THE INSTALLER
     ```

5. Abra o perfil bash em vi ( `vi .bash_profile` ) e verifique se é atualizado com variáveis OFCOBOL.
6. Execute o perfil bash. No prompt de comando, digite:

     ```
      source ~/.bash_profile
     ```

7. Copie a licença OFCOBOL para a pasta instalada. Por exemplo:
     ```
     mv licofcob.dat $OFCOB_HOME/license
     ```
8. Vá para o arquivo de configuração OpenFrame tjclrun. conf e abra-o em vi. Por exemplo:
     ```
     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/config 
     [oframe7@ofdemo ~]$ vi tjclrun.conf
     ```

   Aqui está a seção SYSLIB antes da alteração:
     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bin LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${COBDIR}/lib:/ usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib
     ```
   Aqui está a seção SYSLIB após a alteração:
     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bin LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${COBDIR}/lib:/ usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib :${ODBC_HOME}/lib 
     :${OFCOB_HOME}/lib
     ```
9. Examine o \_ arquivo OpenFrame COBOL \_ InstallLog. log em vi e verifique se não há erros. Por exemplo:
     ```
     [oframe7@ofdemo ~]$ vi $OFCOB_HOME/UninstallerData/log/OpenFrame_COBOL_InstallLog.log 
     …….. 
     Summary 
     ------
     Installation: Successful. 
     131 Successes 
     0 Warnings 
     0 NonFatalErrors 
     0 FatalError
     ```
10. Use o `ofcob --version` comando e examine o número de versão para verificar a instalação. Por exemplo:

     ```
     [oframe7@ofdemo ~]$ ofcob --version 
     OpenFrame COBOL Compiler 3.0.54 
     CommitTag:: 645f3f6bf7fbe1c366a6557c55b96c48454f4bf
     ```

11. Reinicialize o OpenFrame usando o `tmdown/tmboot` comando.

## <a name="install-ofasm"></a>Instalar o OFASM

OFASM é o compilador OpenFrame que interpreta os programas de Assembler do mainframe.

**Para instalar o OFASM**

1. Verifique se a instalação do lote/online foi bem-sucedida e, em seguida, verifique se o \_ arquivo do instalador OpenFrame ASM3 \_ 0 \_ Linux \_ x86 \_ 64. bin está presente.

2. Execute o instalador. Por exemplo:

     ```
     [oframe7@ofdemo ~]$ ./OpenFrame_ASM3_0_Linux_x86_64.bin
     ```

3. Leia o contrato de licenciamento e pressione ENTER para continuar.
4. Aceite o contrato de licenciamento.
5. Verifique se o perfil bash foi atualizado com variáveis OFASM. Por exemplo:

     ```
     [oframe7@ofdemo ~]$ source .bash_profile
     [oframe7@ofdemo ~]$ ofasm --version 
     # TmaxSoft OpenFrameAssembler v3 r328 
     (3ff35168d34f6e2046b96415bbe374160fcb3a34)

     [oframe7@ofdemo OFASM]$ vi .bash_profile

     # OFASM ENV 
     export OFASM_HOME=/opt/tmaxapp/OFASM 
     export OFASM_MACLIB=$OFASM_HOME/maclib/free_macro 
     export PATH="${PATH}:$OFASM_HOME/bin:" 
     export LD_LIBRARY_PATH="./:$OFASM_HOME/lib:$LD_LIBRARY_PATH"
     ```

6. Abra o arquivo de configuração OpenFrame tjclrun. conf em vi e edite-o da seguinte maneira:

     ```
     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/config 
     [oframe7@ofdemo ~]$ vi tjclrun.conf
     ```

     Aqui está a seção [SYSLIB] *antes* da alteração:

     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bi n:${OPENFRAME_HOME}/volume_default/SYS1.LOADLIB LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${CO BDIR}/lib:/usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib:${OFCOB_HOM E}/lib:${ODBC_HOME}/lib:${OFPLI_HOME}/lib
     ```

     Aqui está a seção [SYSLIB] *após* a alteração:

     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bi n:${OPENFRAME_HOME}/volume_default/SYS1.LOADLIB LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${CO BDIR}/lib:/usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib:${OFCOB_HOM E}/lib:${ODBC_HOME}/lib:${OFPLI_HOME}/lib:${OFASM_HOME}/lib
     ```

7. Abra o \_ arquivo OpenFrame ASM \_ InstallLog. log em vi e verifique se não há erros. Por exemplo:

     ```
     [oframe7@ofdemo ~]$ vi 
     $OFASM_HOME/UninstallerData/log/OpenFrame_ASM_InstallLog.log 
     …….. 
     Summary 
     ------

     Installation: Successful.

     55 Successes 
     0 Warnings 
     0 NonFatalErrors 
     0 FatalErrors
     ```

8. Reinicialize o OpenFrame emitindo um dos seguintes comandos:

     ```
     tmdown / tmboot
     ```

     — ou —

     ```
     oscdown / oscboot
     ```

## <a name="install-osc"></a>Instalar o OSC

O OSC é o ambiente OpenFrame semelhante ao IBM CICS, que dá suporte a transações OLTP de alta velocidade e outras funções de gerenciamento.

**Para instalar o OSC**

1. Verifique se a instalação básica foi bem-sucedida e, em seguida, verifique se o arquivo de configuração do OpenFrame \_ OSC7 \_ 0 \_ Fix2 \_ Linux \_ x86 \_ 64. bin e arquivos OSC. Properties estão presentes.
2. Edite os seguintes parâmetros no arquivo OSC. Properties:
     ```
     OPENFRAME_HOME=/opt/tmaxapp/OpenFrame OSC_SYS_OSC_NCS_PATH=/opt/tmaxapp/OpenFrame/temp/OSC_NCS OSC_APP_OSC_TC_PATH=/opt/tmaxapp/OpenFrame/temp/OSC_TC
     ```

3. Execute o instalador usando o arquivo de propriedades, conforme mostrado:

     ```
     [oframe7@ofdemo ~]$ chmod a+x OpenFrame_OSC7_0_Fix2_Linux_x86_64.bin [oframe7@ofdemo ~]$ ./OpenFrame_OSC7_0_Fix2_Linux_x86_64.bin -f osc.properties
     ```

     Quando terminar, a mensagem "instalação concluída" será exibida.

4. Verifique se o perfil bash foi atualizado com as variáveis OSC.
5. Examine o \_ arquivo OpenFrame OSC7 \_ 0 \_ Fix2 \_ InstallLog. log. Ele deverá ser semelhante a este:

     ```
     Summary 
     ------ 
     Installation: Successful.

     233 Successes
     0 Warnings
     0 NonFatalErrors
     0 FatalError
     ```

6. Use vi para abrir o arquivo de configuração ofsys. Seq. Por exemplo:

     ```
     vi $OPENFRAME_HOME/config/ofsys.seq
     ```

7. Nas \# seções base e \# lote, edite os parâmetros conforme mostrado.

     ```
     Before changes
     #BASE
     ofrsasvr
     ofrlhsvr
     ofrdmsvr
     ofrdsedt
     ofrcmsvr
     ofruisvr
     ofrsmlog
     vtammgr
     TPFMAGENT

     #BATCH 
     #BATCH#obmtsmgr
     #BATCH#ofrpmsvr
     #BATCH#obmjmsvr
     #BATCH#obmjschd
     #BATCH#obmjinit
     #BATCH#obmjhist
     #BATCH#obmjspbk
     #TACF #TACF#tmsvr

     After changes  #BATCH
     #BASE          obmtsmgr 
     ofrsasvr       ofrpmsvr
     ofrlhsvr       obmjmsvr
     ofrdmsvr       obmjschd
     ofrdsedt       obmjinit
     ofrcmsvr       obmjhist
     ofruisvr       obmjspbk
     ofrsmlog
     vtammgr        #TACF
     TPFMAGENT      tmsvr
    ```

8. Copie o arquivo de licença. Por exemplo:

     ```
     [oframe7@ofdemo ~]$ cp /home/oframe7/oflicense/ofonline/licosc.dat 

     $OPENFRAME_HOME/license

     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/license 
     oframe@oframe7/OpenFrame/license / ls -l 
     -rwxr-xr-x. 1 oframe mqm 80 Sep 12 01:37 licosc.dat 
     -rwxr-xr-x. 1 oframe mqm 80 Sep  8 09:40 lictacf.dat 
     -rwxrwxr-x. 1 oframe mqm 80 Sep  3 11:54 lictjes.da
     ```

9. Para iniciar e desligar o OSC, inicialize a memória compartilhada da região CICS digitando `osctdlinit OSCOIVP1` no prompt de comando.

10. Execute `oscboot` para inicializar o OSC. A saída é parecida com esta:

     ```
     OSCBOOT : pre-processing       [ OK ]

     TMBOOT for node(NODE1) is starting: 
     Welcome to Tmax demo system: it will expire 2016/11/4 
     Today: 2016/9/12 
          TMBOOT: TMM is starting: Mon Sep 12 01:40:25 2016 
          TMBOOT: CLL is starting: Mon Sep 12 01:40:25 2016 
          TMBOOT: CLH is starting: Mon Sep 12 01:40:25 2016 
          TMBOOT: TLM(tlm) is starting: Mon Sep 12 01:40:25 2016 
     ```

11. Para verificar se o status do processo está pronto, use o `tmadmin` comando em si. Todos os processos devem exibir RDY na coluna **status** .

    ![Processos exibindo RDY](media/tmadmin-02.png)

12. Desligue o OSC usando o `oscdown` comando.

## <a name="install-jeus"></a>Instalar o JEUS

JEUS (Java Enterprise User Solution) fornece a camada de apresentação do servidor de aplicativos da Web do OpenFrame.

Antes de instalar o JEUS, instale o pacote Apache Ant, que fornece as bibliotecas e as ferramentas de linha de comando necessárias para instalar o JEUS.

**Para instalar o Apache Ant**

1. Baixe o binário do Ant usando o `wget` comando. Por exemplo:

     ```
     wget http://apache.mirror.cdnetworks.com/ant/binaries/apacheant-1.9.7-bin.tar.gz
     ```

2. Use o `tar` Utilitário para extrair o arquivo binário e movê-lo para um local apropriado. Por exemplo:

     ```
     tar -xvzf apache-ant-1.9.7-bin.tar.gz
     ```

3. Para obter eficiência, crie um link simbólico:

     ```
     ln -s apache-ant-1.9.7 ant
     ```

4. Abra o perfil bash em vi ( `vi .bash_profile` ) e atualize-o com as seguintes variáveis:

     ```
     # Ant ENV
     export ANT_HOME=$HOME/ant 
     export PATH=$HOME/ant/bin:$PATH
     ```

5.  Aplique a variável de ambiente modificada. Por exemplo:

     ```
     [oframe7\@ofdemo \~]\$ source \~/.bash\_profile
     ```

**Para instalar o JEUS**

1. Expanda o instalador usando o `tar` utilitário. Por exemplo:

     ```
     [oframe7@ofdemo ~]$ tar -zxvf jeus704.tar.gz
     ```

2. Crie uma pasta **Jeus** ( `mkdir jeus7` ) e descompacte o binário.
3. Altere para o diretório de **instalação** (ou use o parâmetro Jeus para seu próprio ambiente). Por exemplo:

     ```
     [oframe7@ofdemo ~]$ cd jeus7/setup/
     ```

4. Execute `ant clean-all` antes de executar a compilação. A saída é parecida com esta:

     ```
     Buildfile: /home/oframe7jeus7/setup/build.xml

     clean-bin:
     delete-domain:
     [echo] Deleting a domain configuration: domain = jeus_domain
     delete-nodesxml:
     clean-config:
     clean-all:
     BUILD SUCCESSFUL
     Total time: 0 seconds
     ```

5.  Faça um backup do arquivo Domain-config-template. Properties. Por exemplo:

     ```
     [oframe7@ofdemo ~]$ cp domain-config-template.properties domain-configtemplate.properties.bkp
     ```

6. Abra o arquivo Domain-config-template. Properties em vi:

     ```
     [oframe7\@ofdemo setup]\$ vi domain-config-template.properties
     ```

7. Altere `jeus.password=jeusadmin nodename=Tmaxsoft` para `jeus.password=tmax1234 nodename=ofdemo`

8. Execute o `ant install` comando para criar o Jeus.
9.  Atualize o arquivo de \_ perfil. bash com as variáveis Jeus, conforme mostrado:

     ```
     # JEUS ENV 
     export JEUS_HOME=/opt/tmaxui/jeus7 PATH="/opt/tmaxui/jeus7/bin:/opt/tmaxui/jeus7/lib/system:/opt/tmaxui/jeus7/webserver/bin:$ {PATH}" 
     export PATH
     ```

10. Execute o perfil bash. Por exemplo:

     ```
     [oframe7@ofdemo setup]$ . .bash_profile
     ```

11. *Opcional*. Crie um alias para fácil desligamento e inicialização de componentes JEUS:

     ```     
     # JEUS alias

     alias dsboot='startDomainAdminServer -domain jeus_domain -u administrator -p jeusadmin'
     alias msboot='startManagedServer -domain jeus_domain -server server1 -u administrator -p jeusadmin' 
     alias msdown=‘jeusadmin -u administrator -p tmax1234 "stop-server server1“’ 
     alias dsdown=‘jeusadmin -domain jeus_domain -u administrator -p tmax1234 "local-shutdown“’
     ```

12. Para verificar a instalação, inicie o servidor de administrador de domínio, conforme mostrado:

     ```
     [oframe7@ofdemo ~]$ startDomainAdminServer -domain jeus_domain -u administrator -p jeusadmin
     ```

13. Verifique por logon na Web usando a sintaxe:

     ```
     http://<IP>:<port>/webadmin/login
     ```

     Por exemplo, <http://192.168.92.133:9736/webadmin/login.> a tela de logon é exibida:
    
     ![Tela de logon do WebAdmin do JEUS](media/jeus-01.png)

     > [!NOTE]
     > Se você tiver problemas com a segurança da porta, abra a porta 9736 ou desabilite o firewall ( `systemctl stop firewall` ).

14. Para alterar o nome do host para Server1, clique em **bloquear & editar** e, em seguida, clique em **Server1**. Na janela do servidor, altere o nome do host da seguinte maneira:

    1.  Altere **NodeName** para **ofdemo**.
    2.  Clique em **OK** no lado direito da janela.
    3.  Clique em **aplicar alterações** no lado inferior esquerdo da janela e, para descrição, insira o *nome do host alterar*.

    ![Tela do WebAdmin do JEUS](media/jeus-02.png)

15. Verifique se a configuração foi bem-sucedida na tela de confirmação.

    ![tela do jeus_domain Server](media/jeus-03.png)

16. Inicie o processo do servidor gerenciado "Server1" usando o seguinte comando:

     ```
     [oframe7@ofdemo ~]$ startManagedServer -domain jeus_domain -server server1 -u administrator -p jeusadmin
     ```

## <a name="install-ofgw"></a>Instalar o OFGW

OFGW é o gateway OpenFrame que dá suporte à comunicação entre o emulador de terminal 3270 e a base OSI e gerencia as sessões entre o emulador de terminal e o OSI.

**Para instalar o OFGW**

1. Verifique se o JEUS foi instalado com êxito e verifique se o arquivo do \_ instalador do OFGW7 0 \_ 1 \_ Generic. bin está presente.
2. Execute o instalador. Por exemplo:

     ```
     [oframe7@ofdemo ~]$ ./OFGW7_0_1_Generic.bin
     ````

3. Use os seguintes locais para os prompts correspondentes:
     -   Diretório base do JEUS
     -   Nome de domínio JEUS
     -   Nome do servidor JEUS
     -   Driver Tibero
     -   Tmax ID do nó ofdemo

4. Aceite o restante dos padrões e pressione ENTER para sair do instalador.

5. Verifique se a URL para OFGW está funcionando conforme o esperado:

     ```
     Type URL 
     http://192.168.92.133:8088/webterminal/ and press enter
      < IP >               :8088/webterminal/
     ```

     A tela a seguir é exibida:

    ![Webterminal do OpenFrame](media/ofgw-01.png)

## <a name="install-ofmanager"></a>Instalar o OFManager

O OFManager fornece funções de operação e gerenciamento para OpenFrame no ambiente da Web.

**Para instalar o OFManager**

1. Verifique se o \_ arquivo do instalador genérico. bin do OFManager7 está presente.
2. Execute o instalador. Por exemplo:

     ```
     OFManager7_Generic.bin
     ```

3.  Pressione Enter para continuar e aceite o contrato de licença.
4.  Escolha a pasta de instalação.
5.  Aceite os padrões.
6.  Escolha Tibero como o banco de dados.
7.  Pressione Enter para sair do instalador.
8.  Verifique se a URL para OFManager está funcionando conforme o esperado:

     ```
     Type URL http://192.168.92.133:8088/ofmanager and press enter <  IP >  : < PORT >  ofmanager Enter ID:   ROOT 
     Password: SYS1
     ```

A tela inicial é exibida:

![Tela de logon do tmax OpenFrame Manager](media/ofmanager-01.png)

Isso conclui a instalação dos componentes do OpenFrame.

## <a name="next-steps"></a>Próximas etapas

Se você estiver considerando uma migração de mainframe, nosso ecossistema de parceiros em expansão estará disponível para ajudá-lo. Para obter orientações detalhadas sobre como escolher uma solução de parceiro, consulte a [Aliança de Modernização de Plataforma](https://datamigration.microsoft.com/).

-   [Introdução ao Azure](../../../../index.yml)
-   [Documentação do Host Integration Server (HIS)](/host-integration-server/)
-   [Guia de comparação de precisão e deslocamento do data center virtual do Azure](/archive/blogs/azurecat/new-whitepaper-azure-virtual-datacenter-lift-and-shift-guide)
