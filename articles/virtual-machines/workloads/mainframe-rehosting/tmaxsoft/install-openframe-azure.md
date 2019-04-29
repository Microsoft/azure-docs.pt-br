---
title: Instalar TmaxSoft OpenFrame em máquinas virtuais do Azure
description: Hospede novamente seu IBM z/OS mainframe cargas de trabalho usando o ambiente de TmaxSoft OpenFrame em máquinas virtuais do Azure (VMs).
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.openlocfilehash: 78a8b5e7a1c5512f81315519210bc7759dd15342
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60800448"
---
# <a name="install-tmaxsoft-openframe-on-azure"></a>Instalar o TmaxSoft OpenFrame no Azure

Saiba como configurar um ambiente OpenFrame no Azure adequado para desenvolvimento, demonstrações, teste ou cargas de trabalho de produção. Este tutorial orienta você por meio de cada etapa.

OpenFrame inclui vários componentes que criar o ambiente de emulação de mainframe no Azure. Por exemplo, serviços online da OpenFrame substituem o middleware de mainframe, como IBM Customer informações de controle de sistema (CICS) e lote OpenFrame, com seu componente TJES, substitui o subsistema de entrada de trabalho de (JES da mainframe IBM).

OpenFrame funciona com qualquer banco de dados relacional, incluindo o Microsoft SQL Server, banco de dados Oracle, IBM Db2 e MySQL. Esta instalação do OpenFrame usa o banco de dados relacional TmaxSoft Tibero. OpenFrame e Tibero executados em um sistema operacional Linux. Este tutorial instala CentOS 7.3, embora você possa usar outras distribuições do Linux com suporte. O servidor de aplicativos OpenFrame e o banco de dados Tibero são instalados em uma máquina virtual (VM).

O tutorial o orienta a instalação dos componentes OpenFrame suite. Alguns devem ser instalados separadamente.

Componentes de OpenFrame principal:

- Pacotes de instalação necessários.
- Banco de dados Tibero.
- Conectividade de banco de dados aberto (ODBC) é usado por aplicativos no OpenFrame para se comunicar com o banco de dados Tibero.
- OpenFrame Base, o middleware que gerencia todo o sistema.
- OpenFrame lote, a solução que substitui os sistemas de mainframe em lotes.
- TACF, um módulo de serviço que controla o acesso de usuário aos sistemas e recursos.
- ProSort, uma ferramenta de classificação para as transações em lotes.
- OFCOBOL, um compilador que interpreta os programas de COBOL do mainframe.
- OFASM, um compilador que interpreta os programas do assembler do mainframe.
- OpenFrame servidor tipo C OSC (), a solução que substitui o middleware e o IBM CICS de mainframe.
- Java Enterprise usuário solução (JEUS), um servidor de aplicativos web que é certificado para Java Enterprise Edition 6.
- OFGW, o componente de gateway OpenFrame que fornece um ouvinte 3270.
- OFManager, uma solução que fornece funções de gerenciamento e operação do OpenFrame no ambiente de web.

Componentes de OpenFrame outros necessários:

- OSI, a solução que substitui o middleware de mainframe e IMS DC.
- TJES, a solução que fornece o ambiente de JES do mainframe.
- OFTSAM, a solução que permite que os arquivos do SAM (V) a ser usado no sistema aberto.
- OFHiDB, a solução que substitui o mainframe's IMS DB.
- OFPLI, um compilador que interpreta o mainframe da PL / eu programas.
- PROTRIEVE, uma solução que executa o idioma de mainframe Easytrieve de autoridade de certificação.
- OFMiner, uma solução que analisa os ativos de mainframes e os migra para o Azure.

## <a name="architecture"></a>Arquitetura

A figura a seguir fornece uma visão geral dos componentes arquitetônicos 7.0 OpenFrame instalado neste tutorial:

![Componentes de OpenFrame](media/openframe-02.png)

## <a name="azure-system-requirements"></a>Requisitos de sistema do Azure

A tabela a seguir lista os requisitos para a instalação no Azure.
<!-- markdownlint-disable MD033 -->

<table>
<thead>
    <tr><th>Requisito</th><th>DESCRIÇÃO</th></tr>
</thead>
<tbody>
<tr><td>Distribuições de Linux com suporte no Azure
</td>
<td>
Linux x86 2.6 (32 bits, 64 bits)<br/>
Red Hat 7.x<br/>
CentOS 7.x<br/>
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
<tr><td>Software opcional para os usuários do Windows
</td>
<td>PuTTY: Usado neste guia para configurar recursos VM<br/>
WinSCP: Um cliente SFTP popular e um cliente FTP, que você pode usar<br/>
Eclipse para Windows: Uma plataforma de desenvolvimento com suporte pelo TmaxSoft<br/>
(Microsoft Visual Studio não é suportado no momento)
</td>
</tr>
</tbody>
</table>

<!-- markdownlint-enable MD033 -->

## <a name="prerequisites"></a>Pré-requisitos

Planeje dedicar alguns dias para reunir todos os softwares necessários e concluir todos os processos manuais.

Antes de começar, faça o seguinte:

- Obter a mídia de instalação OpenFrame do TmaxSoft. Se você for um cliente existente do TmaxSoft, entre em contato com seu representante de TmaxSoft de uma cópia licenciada. Caso contrário, solicite uma versão de avaliação [TmaxSoft](http://www.tmaxsoft.com/contact/).

- Solicitar a documentação OpenFrame enviando um email para <support@tmaxsoft.com>.

- Obtenha uma assinatura do Azure se você ainda não tiver um. Você também pode criar uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- Opcional. Configure um túnel VPN site a site ou um jumpbox que restringe o acesso à VM do Azure para os usuários permitidos em sua organização. Essa etapa não é necessária, mas ele é uma prática recomendada.

## <a name="set-up-a-vm-on-azure-for-openframe-and-tibero"></a>Configurar uma VM no Azure para OpenFrame e Tibero

Você pode configurar o ambiente de OpenFrame usando vários padrões de implantação, mas o procedimento a seguir mostra como implantar o OpenFrame application server e o banco de dados Tibero em uma VM. Em ambientes maiores e para cargas de trabalho considerável, uma prática recomendada é implantar o banco de dados separadamente em sua própria VM para melhorar o desempenho.

**Para criar uma VM**

1. Vá para o portal do Azure em <http://portal.azure.com> e entrar em sua conta.

2. Clique em **Máquinas Virtuais**.

    ![Lista de recursos no portal do Azure](media/vm-01.png)

3. Clique em **Adicionar**.

    ![Adicione a opção no portal do Azure](media/vm-02.png)

4. À direita da vírgula **sistemas operacionais**, clique em **mais**.

     ![Mais uma opção no portal do Azure](media/vm-03.png)

5. Clique em **baseado em CentOS 7.3** a seguir neste passo a passo exatamente, ou você pode escolher outro suporte para a distribuição do Linux.

     ![Opções do sistema operacional no portal do Azure](media/vm-04.png)

6. No **Noções básicas** as configurações, insira **nome**, **nome de usuário**, **tipo de autenticação**, **assinatura** (Pré-pago é o estilo do AWS de pagamento), e **grupo de recursos** (use um existente ou crie um grupo de TmaxSoft).

7. Ao concluir, (incluindo o par de chaves pública/privada **tipo de autenticação**), clique em **enviar**.

> [!NOTE]
> Se usando uma chave pública de SSH para **tipo de autenticação**, consulte as etapas na próxima seção para gerar o par de chaves pública/privada e retomar as etapas descritas aqui.

### <a name="generate-a-publicprivate-key-pair"></a>Gerar um par de chaves pública/privada

Se você estiver usando um sistema de operacional Windows, você precisa PuTTYgen para gerar um par de chaves pública/privada.

A chave pública pode ser compartilhada livremente, mas a chave privada deve ser mantida em segredo inteiramente e nunca deve ser compartilhada com outra parte. Depois de gerar as chaves, você deve colar a **chave pública SSH** na configuração — na verdade, carregá-lo à VM do Linux. Ele é armazenado autorizado dentro\_teclas dentro a \~/.ssh diretório da pasta base da conta do usuário. A VM do Linux, em seguida, é capaz de reconhecer e validar a conexão depois que você fornecer associado **chave privada SSH** no cliente de SSH (no nosso caso, PuTTY).

Ao fornecer novos indivíduos com acesso a VM: 

- Cada novo indivíduo gera suas próprias chaves pública/privada usando PuTTYgen.
- As pessoas armazenam suas próprias chaves particulares separadamente e enviar as informações de chave públicas para o administrador da VM.
- O administrador cola o conteúdo da chave pública para o \~/.ssh/authorized\_arquivo de chaves.
- O novo indivíduo conecta-se via PuTTY.

**Para gerar um par de chaves pública/privada**

1.  Baixe o PuTTYgen do <https://www.putty.org/> e instalá-lo usando todas as configurações padrão.

2.  Para abrir o PuTTYgen, localize o diretório de instalação PuTTY em c:\\arquivos de programas\\PuTTY.

    ![Interface do puTTY](media/puttygen-01.png)

3.  Clique em **Gerar**.

    ![Caixa de diálogo do puTTY Key Generator](media/puttygen-02.png)

4.  Após a geração, salve a chave pública e a chave privada. Cole o conteúdo da chave pública na **chave pública SSH** seção o **criar a máquina virtual \> Noções básicas** painel (mostrado nas etapas 6 e 7 na seção anterior).

    ![Caixa de diálogo do puTTY Key Generator](media/puttygen-03.png)

### <a name="configure-vm-features"></a>Configurar recursos VM

1. No portal do Azure, nos **escolher um tamanho** folha, escolha as configurações de hardware de máquina Linux desejadas. O *mínimo* requisitos para instalar Tibero e OpenFrame são 2 CPUs e 4 GB de RAM, conforme mostrado nesta instalação de exemplo:

    ![Criar a máquina virtual – Noções básicas](media/create-vm-01.png)

2. Clique em **3 configurações** e use as configurações padrão para configurar recursos opcionais.
3. Examine os detalhes de pagamento.

    ![Criar a máquina virtual – compra](media/create-vm-02.png)

4. Envie suas seleções. Azure começa a implantar a VM. Esse processo normalmente leva alguns minutos.

5. Quando a VM é implantada, o painel é exibido, mostrando todas as configurações que foram selecionadas durante a configuração. Anote o **endereço IP público**.

    ![tmax no painel do Azure](media/create-vm-03.png)

6. Abra o PuTTY.

7. Para **nome do Host**, digite seu nome de usuário e o endereço IP público é copiado. Por exemplo, **nome de usuário\@publicip**.

    ![Caixa de diálogo de configuração puTTY](media/putty-01.png)

8. No **categoria** , clique em **Conexão \> SSH \> Auth**. Forneça o caminho para seu **chave privada** arquivo.

    ![Caixa de diálogo de configuração puTTY](media/putty-02.png)

9. Clique em **abrir** para iniciar a janela PuTTY. Se for bem-sucedido, você está conectado à sua nova VM CentOS em execução no Azure.

10. Para fazer logon como usuário raiz, digite **sudo bash**.

    ![Logon do usuário raiz na janela de comando](media/putty-03.png)

## <a name="set-up-the-environment-and-packages"></a>Configurar o ambiente e pacotes

Agora que a VM é criada e você estiver conectado, você deve executar algumas etapas de instalação e instale os pacotes necessários de pré-instalação.

1. O nome do mapa **ofdemo** para o endereço IP local usando o vi para editar o arquivo de hosts (`vi /etc/hosts`). Supondo que o IP é 192.168.96.148 ofdemo, isso é antes da alteração:

    ```vi
    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4 
    ::1              localhost localhost.localdomain localhost6 localhost6.localdomain 
    <IP Address>    <your hostname>
    ```

     Isso é após a alteração:

    ```vi
    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4 
    ::1              localhost localhost.localdomain localhost6 localhost6.localdomain 
    192.168.96.148   ofdemo
    ```

2. Crie usuários e grupos:

    ```vi
    [root@ofdemo ~]# adduser -d /home/oframe7 oframe7 
    [root@ofdemo ~]# passwd oframe7
    ```

3. Altere a senha de usuário oframe7:

    ```vi
    New password: 
    Retype new password: 
    passwd: all authentication tokens updated successfully.
    ```

4. Atualize os parâmetros de kernel no /etc/sysctl.conf:

    ```vi
    [root@ofdemo ~]# vi /etc/sysctl.conf
    kernel.shmall = 7294967296 
    kernel.sem = 10000 32000 10000 10000
    ```

5. Atualize os parâmetros de kernel dinamicamente sem a reinicialização:

    ```vi
    [root@ofdemo ~]# /sbin/sysctl -p
    ```

6. Obtenha os pacotes necessários: Verifique se que o servidor está conectado à Internet, baixe os seguintes pacotes e, em seguida, instalá-los:

     - dos2unix
     - glibc
     - glibc.i686 glibc.x86\_64
     - libaio
     - ncurses

          > [!NOTE]
          > Depois de instalar o pacote ncurses, crie os seguintes links simbólicos:
         ```
         ln -s /usr/lib64/libncurses.so.5.9 /usr/lib/libtermcap.so
         ln -s /usr/lib64/libncurses.so.5.9 /usr/lib/libtermcap.so.2
         ```

     - gcc
     - gcc-c++
     - libaio-devel.x86\_64
     - strace
     - ltrace
     - gdb

7. No caso de instalação do RPM do Java, faça o seguinte:

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

Tibero fornece as várias funções importantes no ambiente OpenFrame no Azure:

- Tibero é usado como o repositório de dados interno OpenFrame para várias funções do sistema.
- Arquivos VSAM, incluindo KSDS, RRDS e ESDS, usam o banco de dados Tibero internamente para armazenamento de dados.
- O repositório de dados TACF é armazenado em Tibero.
- As informações de catálogo OpenFrame são armazenadas em Tibero.
- O banco de dados Tibero pode ser usado como uma substituição para o IBM Db2 para armazenar dados de aplicativo.

**Para instalar o Tibero**

1. Verifique se o arquivo de instalador binário Tibero está presente e examine o número de versão.
2. Copie o software Tibero para a conta de usuário Tibero (oframe). Por exemplo: 

    ```
    [oframe7@ofdemo ~]$ tar -xzvf tibero6-bin-6_rel_FS04-linux64-121793-opt-tested.tar.gz 
    [oframe7@ofdemo ~]$ mv license.xml /opt/tmaxdb/tibero6/license/
    ```

3. Abra .bash\_perfil no vi (`vi .bash_profile`) e cole o seguinte:

    ```
    # Tibero6 ENV
    export TB_HOME=/opt/tmaxdb/tibero6 
    export TB_SID=TVSAM export TB_PROF_DIR=$TB_HOME/bin/prof 
    export LD_LIBRARY_PATH=$TB_HOME/lib:$TB_HOME/client/lib:$LD_LIBRARY_PATH 
    export PATH=$TB_HOME/bin:$TB_HOME/client/bin:$PATH
    ```

4. Para executar o perfil de bash, no prompt de comando digite:

    ```
    source .bash_profile
    ```

5. Gerar o arquivo de dica (um arquivo de configuração para Tibero), em seguida, abra-o no vi. Por exemplo: 

    ```
    [oframe7@ofdemo ~]$ sh $TB_HOME/config/gen_tip.sh
    [oframe7@ofdemo ~]$ vi $TB_HOME/config/$TB_SID.tip
    ```

6. Modifique \$TB\_HOME/client/config/tbdsn.tbr e coloque 127.0.0.1 em vez disso, oflocalhost conforme mostrado:

    ```
    TVSAM=( 
    (INSTANCE=(HOST=127.0.0.1)
        (PT=8629)
    (DB_NAME=TVSAM)
          )
     )
    ```

7. Crie o banco de dados. A saída a seguir será exibida:

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

8. Para reciclar Tibero, primeiro desligá-lo usando o `tbdown` comando. Por exemplo: 

    ```
    [oframe7@ofdemo ~]$$ tbdown 
    Tibero instance terminated (NORMAL mode).
    ```

9. Agora inicialize usando Tibero `tbboot`. Por exemplo: 

    ```
    [oframe7@ofdemo ~]$ tbboot
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof. Listener port = 8629

    Tibero 6  
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NORMAL mode).
    ```

10. Para criar um espaço de tabela, acessar o banco de dados usando SYS usuário (sys tmax), em seguida, crie o espaço de tabela necessário para o volume padrão e TACF:

    ```
    [oframe7@ofdemo ~]$ tbsql tibero/tmax
    tbSQL 6  
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Connected to Tibero.
    ```

11. Agora, digite os comandos SQL a seguir:

    ```
    SQL> create tablespace "DEFVOL" datafile 'DEFVOL.dbf' size 500M autoextend on; create tablespace "TACF00" datafile 'TACF00.dbf' size 500M autoextend on; create tablespace "OFM_REPOSITORY" datafile 'ofm_repository.dbf' size 300M autoextend on;
    SQL> Tablespace 'DEFVOL' created.
    SQL> Tablespace 'TACF00' created.
    SQL> Tablespace ' OFM_REPOSITORY ' created.
    SQL> SQL> Disconnected.
    ```

12. Inicializar Tibero e verificar se os processos Tibero estão em execução:

    ```
    [oframe7@ofdemo ~]$ tbboot 
    ps -ef | egrep tbsvr
    ```

Saída:

![Saída de Tibero](media/tibero-01.png)

## <a name="install-odbc"></a>Instale o ODBC

Aplicativos em OpenFrame se comunicar com o banco de dados Tibero usando a API de ODBC fornecida pelo projeto de código-fonte aberto unixODBC.

Para instalar o ODBC:

1. Verifique se o arquivo do instalador unixODBC 2.3.4.tar.gz está presente ou use o `wget unixODBC-2.3.4.tar.gz` comando. Por exemplo: 

     ```
     [oframe7@ofdemo ~]$ wget ftp://ftp.unixodbc.org/pub/unixODBC/unixODBC-2.3.4.tar.gz
     ```

2. Descompacte o binário. Por exemplo: 

     ```
     [oframe7@ofdemo ~]$ tar -zxvf unixODBC-2.3.4.tar.gz
     ```

3. Navegue até o diretório unixODBC-2.3.4 e gerar o Makefile usando as informações de verificação de máquina. Por exemplo: 

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ ./configure --prefix=/opt/tmaxapp/unixODBC/ --sysconfdir=/opt/tmaxapp/unixODBC/etc
     ```

     Por padrão, unixODBC é instalado em /usr /local, portanto, `--prefix` transmite um valor para alterar o local. Da mesma forma, arquivos de configuração são instalados em /etc. por padrão, portanto, `--sysconfdir` passa o valor do local desejado.

4. Execute o Makefile: `[oframe7@ofdemo unixODBC-2.3.4]$ make`

5. Copie o arquivo executável no diretório de programas após a compilação. Por exemplo: 

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ make install
     ```

6. Use o vi para editar o perfil de bash (`vi ~/.bash_profile`) e adicione o seguinte:

     ```
     # UNIX ODBC ENV 
     export ODBC_HOME=$HOME/unixODBC 
     export PATH=$ODBC_HOME/bin:$PATH 
     export LD_LIBRARY_PATH=$ODBC_HOME/lib:$LD_LIBRARY_PATH 
     export ODBCINI=$HOME/unixODBC/etc/odbc.ini 
     export ODBCSYSINI=$HOME
     ```

7. Aplique o ODBC. Edite os arquivos a seguir adequadamente. Por exemplo: 

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

8. Criar um link simbólico e validar a conexão de banco de dados Tibero:

     ```
     [oframe7@ofdemo ~]$ ln $ODBC_HOME/lib/libodbc.so $ODBC_HOME/lib/libodbc.so.1 [oframe7@ofdemo ~]$ ln $ODBC_HOME/lib/libodbcinst.so 
     $ODBC_HOME/lib/libodbcinst.so.1

     [oframe7@ofdemo lib]$ isql TVSAM tibero tmax
     ```

A seguinte saída é exibida:

![Saída ODBC mostrando conectado ao SQL](media/odbc-01.png)

## <a name="install-openframe-base"></a>Instalar a Base de OpenFrame

O servidor de aplicativos básico é instalado antes dos serviços individuais que OpenFrame usa para gerenciar o sistema no Azure, incluindo a manipulação de processos do servidor da transação.

**Para instalar a Base de OpenFrame**

1. Verifique se a instalação Tibero foi bem-sucedida e, em seguida, verifique o seguinte OpenFrame\_Base7\_0\_Linux\_x86\_arquivo do instalador de 64 bin e o arquivo de configuração base.properties estão presentes.

2. Atualize o perfil de bash com as seguintes informações Tibero específicas:

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
4. Certifique-se de que os processos Tibero estão em execução. Por exemplo: 

     ```linux
     [oframe7@ofdemo ~]$ ps -ef|grep tbsvr
     ```

    ![Base](media/base-01.png)

     > [!IMPORTANT]
     > Certifique-se de que iniciar Tibero antes da instalação.

5. Gerar a licença em [technet.tmaxsoft.com](https://technet.tmaxsoft.com/en/front/main/main.do) e colocar a base de dados de OpenFrame, lote, TACF, licenças de OSC na pasta apropriada:

     ```
     [oframe7@ofdemo ~]$ cp license.dat /opt/tmaxapp/OpenFrame/core/license/
     [oframe7@ofdemo ~]$ cp lictjes.dat lictacf.dat licosc.dat $OPENFRAME_HOME/license/
     ```

6. Baixe os arquivos binários e base.properties de OpenFrame Base:

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

7. Execute o instalador usando o arquivo base.properties. Por exemplo: 

    ```
    [oframe7@ofdemo ~]$ chmod a+x OpenFrame_Base7_0_Linux_x86_64.bin 
    [oframe7@ofdemo ~]$ ./OpenFrame_Base7_0_Linux_x86_64.bin -f base.properties
    ```

    Ao concluir, a mensagem de conclusão da instalação é exibido.

8. Verifique se a estrutura de diretório OpenFrame Base usando o `ls -ltr` comando. Por exemplo: 

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

9. Inicie OpenFrame Base:

     ```
     [oframe7@ofdemo ~]$ cp /usr/lib/libtermcap.so.2 $TMAXDIR/lib
     Startup Tmax Server
     [oframe7@ofdemo ~]$ tmboot
     ```

     ![saída do comando tmboot](media/base-02.png)

10. Verifique se que o status do processo está pronto para usar o comando tmadmin em si. RDY é exibido na **status** coluna para cada um dos processos:

     ![saída do comando tmadmin](media/base-03.png)

11. Desligue OpenFrame Base:

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

Lote OpenFrame consiste em vários componentes que simule os ambientes de lote de mainframe e é usado para executar trabalhos em lote no Azure.

**Para instalar o lote**

1. Certifique-se a instalação de base foi bem-sucedida e, em seguida, verifique o OpenFrame\_Batch7\_0\_Fix2\_MVS\_Linux\_x86\_arquivo do instalador de 64 bin e arquivo de configuração batch.Properties estão presentes:

2. No prompt de comando, digite `vi batch.properties` para editar o arquivo batch.properties usando o vi.

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

4. Para executar o instalador do lote, no prompt de comando digite:

     ```
     ./OpenFrame_Batch7_0_Fix2_MVS_Linux_x86_64.bin -f batch.properties
     ```

5. Quando a instalação for concluída, inicie os conjuntos de OpenFrame instalados digitando `tmboot` no prompt de comando.

    ![saída de tmboot](media/tmboot-01.png)

6. Tipo `tmadmin` no prompt de comando para verificar o processo de OpenFrame.

    ![Tela de administração de Tmax](media/tmadmin-01.png)

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

## <a name="install-tacf"></a>Instalar TACF

TACF Manager é um módulo de serviço de OpenFrame que controla o acesso de usuário a recursos por meio da segurança RACF e sistemas.

**Para instalar o TACF**

1. Verifique o OpenFrame\_Tacf7\_0\_Fix2\_Linux\_x86\_arquivo do instalador de 64 bin e o arquivo de configuração tacf.properties estão presentes.
2. Verifique se a instalação do lote foi bem-sucedida, use o vi para abrir o arquivo tacf.properties (`vi tacf.properties`).
3. Modifique os parâmetros TACF:

     ```
     OPENFRAME_HOME=/opt/tmaxapp/OpenFrame 
     USE_OS_AUTH=NO 
     TACF_USERNAME=tibero 
     TACF_PASSWORD=tmax 
     TACF_DATABASE=oframe 
     TACF_TABLESPACE=TACF00 
     TACF_TABLE_CREATE=YES
     ```

4. Depois de concluir o instalador TACF, aplique as variáveis de ambiente TACF. No prompt de comando, digite:

     ```
     source \~/.bash\_profile
     ```

5. Execute o instalador TACF. No prompt de comando, digite:

     ```
     ./OpenFrame_Tacf7_0_Fix2_Linux_x86_64.bin -f tacf.properties
     ```

     A saída é semelhante a:

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

6. No prompt de comando, digite `tmboot` reiniciar OpenFrame. A saída é semelhante a:

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

7. Verifique se o status do processo está pronto para uso `tmadmin` no `si` comando. Por exemplo: 

     ```
     [oframe7\@ofdemo \~]\$ tmadmin
     ```

     No **status** RDY de coluna, será exibida:

    ![RDY na coluna de status](media/tmboot-02.png)

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

9. Desligue o servidor usando o `tmdown` comando. A saída é semelhante a:

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

## <a name="install-prosort"></a>Instalação ProSort

ProSort é um utilitário usado em transações em lotes para classificar os dados.

**Para instalar o ProSort**

1. Verifique se a instalação do lote foi bem-sucedida e, em seguida, verifique se que o **prosort bin prosort\_2sp3 linux64-2123 opt.tar.gz** arquivo do instalador está presente.

2. Execute o instalador usando o arquivo de propriedades. No prompt de comando, digite:

     ```
     tar -zxvf prosort-bin-prosort\_2sp3-linux64-2123-opt.tar.gz
     ```

3. Mova o diretório prosort para o local de residência. No prompt de comando, digite:

     ```
     mv prosort /opt/tmaxapp/prosort
     ```

4. Crie um subdiretório de licença e copie o arquivo de licença. Por exemplo: 

     ```
     cd /opt/tmaxapp/prosort 
     mkdir license 
     cp /opt/tmaxsw/oflicense/prosort/license.xml /opt/tmaxapp/prosort/license
     ```

5. Abra bash.profile no vi (`vi .bash_profile`) e atualizá-lo da seguinte maneira:

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

6. Para executar o perfil de bash, no prompt de comando, digite: `. .bash_profile`

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

9. Verificar a instalação ProSort executando o `prosort -h` comando. Por exemplo: 

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

## <a name="install-ofcobol"></a>Instalar OFCOBOL

OFCOBOL é o compilador OpenFrame que interpreta os programas de COBOL do mainframe. 

**Para instalar o OFCOBOL**

1. Certifique-se de que a instalação de lote/Online foi bem-sucedida e, em seguida, verifique o OpenFrame\_COBOL3\_0\_40\_Linux\_x86\_arquivo do instalador de 64 bin está presente.

2. Para executar o instalador OFCOBOL, no prompt de comando, digite:

     ```
      ./OpenFrame\_COBOL3\_0\_40\_Linux\_x86\_64.bin
     ```

3. Leia o contrato de licença e pressione Enter para continuar.

4. Aceite o contrato de licenciamento. Quando a instalação for concluída, aparece o seguinte:

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

5. Abra o perfil de bash em vi (`vi .bash_profile`) e verifique se que é atualizado com as variáveis OFCOBOL.
6. Execute o perfil de bash. No prompt de comando, digite:

     ```
      source ~/.bash_profile
     ```

7. Copie a licença OFCOBOL para a pasta de instalação. Por exemplo: 
     ```
     mv licofcob.dat $OFCOB_HOME/license
     ```
8. Vá para o arquivo de configuração de tjclrun.conf OpenFrame e abri-lo no vi. Por exemplo: 
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
9. Examine o OpenFrame\_COBOL\_InstallLog.log no vi e verificar se há erros. Por exemplo: 
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
10. Use o `ofcob --version` de comando e examine o número de versão para verificar a instalação. Por exemplo: 

     ```
     [oframe7@ofdemo ~]$ ofcob --version 
     OpenFrame COBOL Compiler 3.0.54 
     CommitTag:: 645f3f6bf7fbe1c366a6557c55b96c48454f4bf
     ```

11. Reinicializar OpenFrame usando o `tmdown/tmboot` comando.

## <a name="install-ofasm"></a>Instalar OFASM

OFASM é o compilador OpenFrame que interpreta os programas do assembler do mainframe.

**Para instalar o OFASM**

1. Certifique-se de que a instalação de lote/Online foi bem-sucedida e, em seguida, verifique o OpenFrame\_ASM3\_0\_Linux\_x86\_arquivo do instalador de 64 bin está presente.

2. Execute o instalador. Por exemplo: 

     ```
     [oframe7@ofdemo ~]$ ./OpenFrame_ASM3_0_Linux_x86_64.bin
     ```

3. Leia o contrato de licença e pressione Enter para continuar.
4. Aceite o contrato de licenciamento.
5. Verifique se que o perfil bash é atualizado com as variáveis OFASM. Por exemplo: 

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

6. Abra o arquivo de configuração de tjclrun.conf OpenFrame no vi e editá-lo da seguinte maneira:

     ```
     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/config 
     [oframe7@ofdemo ~]$ vi tjclrun.conf
     ```

     Aqui está a seção [SYSLIB] *antes de* a alteração:

     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bi n:${OPENFRAME_HOME}/volume_default/SYS1.LOADLIB LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${CO BDIR}/lib:/usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib:${OFCOB_HOM E}/lib:${ODBC_HOME}/lib:${OFPLI_HOME}/lib
     ```

     Aqui está a seção [SYSLIB] *depois de* a alteração:

     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bi n:${OPENFRAME_HOME}/volume_default/SYS1.LOADLIB LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${CO BDIR}/lib:/usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib:${OFCOB_HOM E}/lib:${ODBC_HOME}/lib:${OFPLI_HOME}/lib:${OFASM_HOME}/lib
     ```

7. Abra o OpenFrame\_ASM\_InstallLog.log no vi e verificar se há erros. Por exemplo: 

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

8. Reinicialize OpenFrame emitindo um dos comandos a seguir:

     ```
     tmdown / tmboot
     ```

     – ou –

     ```
     oscdown / oscboot
     ```

## <a name="install-osc"></a>Instalar OSC

OSC é o ambiente de OpenFrame semelhante ao IBM CICS que dá suporte a transações de OLTP de alta velocidade e outras funções de gerenciamento.

**Para instalar OSC**

1. Certifique-se a instalação de base foi bem-sucedida e, em seguida, verifique o OpenFrame\_OSC7\_0\_Fix2\_Linux\_x86\_arquivo do instalador de 64 bin e o arquivo de configuração osc.properties são presente.
2. Edite os seguintes parâmetros no arquivo osc.properties:
     ```
     OPENFRAME_HOME=/opt/tmaxapp/OpenFrame OSC_SYS_OSC_NCS_PATH=/opt/tmaxapp/OpenFrame/temp/OSC_NCS OSC_APP_OSC_TC_PATH=/opt/tmaxapp/OpenFrame/temp/OSC_TC
     ```

3. Execute o instalador usando o arquivo de propriedades, conforme mostrado:

     ```
     [oframe7@ofdemo ~]$ chmod a+x OpenFrame_OSC7_0_Fix2_Linux_x86_64.bin [oframe7@ofdemo ~]$ ./OpenFrame_OSC7_0_Fix2_Linux_x86_64.bin -f osc.properties
     ```

     Quando terminar, a mensagem "Instalação concluída" é exibida.

4. Verifique se o perfil bash é atualizado com variáveis OSC.
5. Examine o OpenFrame\_OSC7\_0\_Fix2\_InstallLog.log arquivo. O resultado deve ser semelhante a esse:

     ```
     Summary 
     ------ 
     Installation: Successful.

     233 Successes
     0 Warnings
     0 NonFatalErrors
     0 FatalError
     ```

6. Use vi para abrir o arquivo de configuração ofsys.seq. Por exemplo: 

     ```
     vi $OPENFRAME_HOME/config/ofsys.seq
     ```

7. No \#BASE e \#seções do LOTE, edite os parâmetros, conforme mostrado.

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

9. Para iniciar e desligar OSC, inicializar a memória de região compartilhada CICS digitando `osctdlinit OSCOIVP1` no prompt de comando.

10. Executar `oscboot` a inicialização do OSC. A saída é semelhante a:

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

11. Para verificar o status do processo está pronto, use o `tmadmin` comando em si. Todos os processos devem exibir RDY na **status** coluna.

    ![Exibindo RDY de processos](media/tmadmin-02.png)

12. Desligue OSC usando o `oscdown` comando.

## <a name="install-jeus"></a>Instalar JEUS

JEUS (solução de usuário do Java Enterprise) fornece a camada de apresentação do servidor de aplicativos web OpenFrame.

Antes de instalar JEUS, instale o pacote de Apache Ant, que fornece as bibliotecas e ferramentas de linha de comando necessárias para instalar o JEUS.

**Para instalar o Apache Ant**

1. Download Ant binário usando o `wget` comando. Por exemplo: 

     ```
     wget http://apache.mirror.cdnetworks.com/ant/binaries/apacheant-1.9.7-bin.tar.gz
     ```

2. Use o `tar` utilitário para extrair o arquivo binário e movê-lo para um local apropriado. Por exemplo: 

     ```
     tar -xvzf apache-ant-1.9.7-bin.tar.gz
     ```

3. Para obter eficiência, crie um link simbólico:

     ```
     ln -s apache-ant-1.9.7 ant
     ```

4. Abra o perfil de bash em vi (`vi .bash_profile`) e atualizá-lo com as seguintes variáveis:

     ```
     # Ant ENV
     export ANT_HOME=$HOME/ant 
     export PATH=$HOME/ant/bin:$PATH
     ```

5.  Aplicam-se a variável de ambiente modificada. Por exemplo: 

     ```
     [oframe7\@ofdemo \~]\$ source \~/.bash\_profile
     ```

**Para instalar o JEUS**

1. Expanda o instalador usando o `tar` utilitário. Por exemplo: 

     ```
     [oframe7@ofdemo ~]$ tar -zxvf jeus704.tar.gz
     ```

2. Criar uma **jeus** pasta (`mkdir jeus7`) e descompacte o binário.
3. Altere para o **instalação** diretório (ou use o parâmetro JEUS para seu próprio ambiente). Por exemplo: 

     ```
     [oframe7@ofdemo ~]$ cd jeus7/setup/
     ```

4. Executar `ant clean-all` antes de executar a compilação. A saída é semelhante a:

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

5.  Faça um backup do arquivo config-domínio-template.properties. Por exemplo: 

     ```
     [oframe7@ofdemo ~]$ cp domain-config-template.properties domain-configtemplate.properties.bkp
     ```

6. Abra o arquivo de domínio-config-template.properties no vi:

     ```
     [oframe7\@ofdemo setup]\$ vi domain-config-template.properties
     ```

7. Altere `jeus.password=jeusadmin nodename=Tmaxsoft` para `jeus.password=tmax1234 nodename=ofdemo`

8. Execute o `ant install` para compilar JEUS.
9.  Atualizar o .bash\_arquivo de perfil com as variáveis JEUS, conforme mostrado:

     ```
     # JEUS ENV 
     export JEUS_HOME=/opt/tmaxui/jeus7 PATH="/opt/tmaxui/jeus7/bin:/opt/tmaxui/jeus7/lib/system:/opt/tmaxui/jeus7/webserver/bin:$ {PATH}" 
     export PATH
     ```

10. Execute o perfil de bash. Por exemplo: 

     ```
     [oframe7@ofdemo setup]$ . .bash_profile
     ```

11. *Opcional*. Crie um alias para fácil desligamento e inicialização dos componentes JEUS:

     ```     
     # JEUS alias

     alias dsboot='startDomainAdminServer -domain jeus_domain -u administrator -p jeusadmin'
     alias msboot='startManagedServer -domain jeus_domain -server server1 -u administrator -p jeusadmin' 
     alias msdown=‘jeusadmin -u administrator -p tmax1234 "stop-server server1“’ 
     alias dsdown=‘jeusadmin -domain jeus_domain -u administrator -p tmax1234 "local-shutdown“’
     ```

12. Para verificar a instalação, inicie o servidor de administração de domínio, conforme mostrado:

     ```
     [oframe7@ofdemo ~]$ startDomainAdminServer -domain jeus_domain -u administrator -p jeusadmin
     ```

13. Verificar logon da web usando a sintaxe:

     ```
     http://<IP>:<port>/webadmin/login
     ```

     Por exemplo, <http://192.168.92.133:9736/webadmin/login.> aparece a tela de logon:
    
     ![Tela de logon JEUS WebAdmin](media/jeus-01.png)

     > [!NOTE]
     > Se você tiver problemas com a segurança de porta, abra a porta 9736 ou desativar o firewall (`systemctl stop firewall`).

14. Para alterar o nome do host para o servidor1, clique em **bloquear & Editar**, em seguida, clique em **server1**. Na janela do servidor, altere o nome do host da seguinte maneira:

    1.  Alteração **Nodename** à **ofdemo**.
    2.  Clique em **Okey** no lado direito da janela.
    3.  Clique em **aplicar alterações** na parte inferior esquerda da janela e para a descrição, insira *alteração de nome de host*.

    ![Tela de JEUS WebAdmin](media/jeus-02.png)

15. Verifique se que a configuração foi bem-sucedida na tela de confirmação.

    ![jeus_domain Server screen](media/jeus-03.png)

16. Inicie o processo de servidor gerenciado "server1" usando o seguinte comando:

     ```
     [oframe7@ofdemo ~]$ startManagedServer -domain jeus_domain -server server1 -u administrator -p jeusadmin
     ```

## <a name="install-ofgw"></a>Instalar OFGW

OFGW é o gateway de OpenFrame que dá suporte à comunicação entre o emulador de terminal 3270 e a base OSI e gerencia as sessões entre o emulador de terminal e OSI.

**Para instalar o OFGW**

1. Certifique-se de que JEUS foi instalado com êxito e, em seguida, verifique o OFGW7\_0\_1\_Generic.bin arquivo do instalador está presente.
2. Execute o instalador. Por exemplo: 

     ```
     [oframe7@ofdemo ~]$ ./OFGW7_0_1_Generic.bin
     ````

3. Use os seguintes locais para os avisos correspondentes:
     -   Diretório base JEUS
     -   Nome de domínio JEUS
     -   Nome do servidor JEUS
     -   Driver Tibero
     -   ID do nó Tmax ofdemo

4. Aceite o restante dos padrões e, em seguida, pressione Enter para sair do instalador.

5. Verifique se a URL para OFGW está funcionando conforme o esperado:

     ```
     Type URL 
     http://192.168.92.133:8088/webterminal/ and press enter
      < IP >               :8088/webterminal/
     ```

     Aparece a tela a seguir:

    ![OpenFrame WebTerminal](media/ofgw-01.png)

## <a name="install-ofmanager"></a>Instalar OFManager

OFManager fornece funções de gerenciamento e operação OpenFrame no ambiente de web.

**Para instalar o OFManager**

1. Verifique o OFManager7\_Generic.bin arquivo do instalador está presente.
2. Execute o instalador. Por exemplo: 

     ```
     OFManager7_Generic.bin
     ```

3.  Pressione Enter para continuar e, em seguida, aceite o contrato de licença.
4.  Escolha a pasta de instalação.
5.  Aceite os padrões.
6.  Escolha Tibero como o banco de dados.
7.  Pressione Enter para sair do instalador.
8.  Verifique se a URL para OFManager está funcionando conforme o esperado:

     ```
     Type URL http://192.168.92.133:8088/ofmanager and press enter <  IP >  : < PORT >  ofmanager Enter ID:   ROOT 
     Password: SYS1
     ```

A tela Iniciar aparece:

![Tela de logon Tmax OpenFrame Manager](media/ofmanager-01.png)

Isso conclui a instalação dos componentes OpenFrame.

## <a name="next-steps"></a>Próximas etapas

Se você estiver considerando uma migração de mainframe, nosso ecossistema de parceiros a expansão está disponível para ajudá-lo. Para obter orientações detalhadas sobre como escolher uma solução de parceiro, consulte a [Aliança de Modernização de Plataforma](https://www.platformmodernization.org/pages/mainframe.aspx).

-   [Introdução ao Azure](https://docs.microsoft.com/azure/)
-   [Documentação do Host Integration Server (HIS)](https://docs.microsoft.com/host-integration-server/)
-   [Guia de Lift-and-Shift do Data Center Virtual do Azure](https://blogs.msdn.microsoft.com/azurecat/2018/03/12/new-whitepaper-azure-virtual-datacenter-lift-and-shift-guide/)
