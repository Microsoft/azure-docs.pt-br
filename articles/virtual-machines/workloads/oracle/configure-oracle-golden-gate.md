---
title: Implementar Oracle Golden Gate em uma VM Linux do Azure | Microsoft Docs
description: Coloque rapidamente em funcionamento um Oracle Golden Gate no seu ambiente do Azure.
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
ms.openlocfilehash: 39d1b8b860fd19261bd39c345d464dd37b48d871
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707544"
---
# <a name="implement-oracle-golden-gate-on-an-azure-linux-vm"></a>Implementar Oracle Golden Gate em uma VM Linux do Azure 

A CLI do Azure é usada para criar e gerenciar recursos do Azure da linha de comando ou em scripts. Esse guia detalha como usar a CLI do Azure para implantar um banco de dados Oracle 12c por meio da imagem na galeria do Marketplace do Azure. 

Este documento mostra passo a passo sobre como criar, instalar e configurar o Oracle Golden Gate em uma VM do Azure.

Antes de começar, certifique-se de que a CLI do Azure foi instalada. Para obter mais informações, consulte o [Guia de instalação da CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="prepare-the-environment"></a>Preparar o ambiente

Para executar a instalação do Oracle Golden Gate, você precisa criar duas VMs do Azure no mesmo conjunto de disponibilidade. A imagem do Marketplace usada para criar as VMs é **Oracle:Oracle-Database-Ee:12.1.0.2:latest**.

Você também precisa estar familiarizado com Unix editor vi e ter um entendimento básico de x11 (X Windows).

Este é um resumo da configuração do ambiente:
> 
> |  | **Site primário** | **Replicar site** |
> | --- | --- | --- |
> | **Versão do Oracle** |Oracle 12c Release 2 – (12.1.0.2) |Oracle 12c Release 2 – (12.1.0.2)|
> | **Nome da máquina** |myVM1 |myVM2 |
> | **Sistema operacional** |Oracle Linux 6.x |Oracle Linux 6.x |
> | **SID do Oracle** |CDB1 |CDB1 |
> | **Esquema de replicação** |TEST|TEST |
> | **Golden Gate proprietário/replicar** |C ##GGADMIN |REPUSER |
> | **Processo de Golden Gate** |EXTORA |REPORA|


### <a name="sign-in-to-azure"></a>Entrar no Azure 

Entre na sua assinatura do Azure como comando [az login](/cli/azure/reference-index). Em seguida, execute as instruções na tela.

```azurecli
az login
```

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e a partir do qual podem ser gerenciados. 

O exemplo a seguir cria um grupo de recursos denominado `myResourceGroup` no local `westus`.

```azurecli
az group create --name myResourceGroup --location westus
```

### <a name="create-an-availability-set"></a>Criar um conjunto de disponibilidade

Esta etapa é opcional, mas recomendada. Para obter mais informações, confira [Guia de conjuntos de disponibilidade do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines).

```azurecli
az vm availability-set create \
    --resource-group myResourceGroup \
    --name myAvailabilitySet \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

### <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Crie uma VM com o comando [az vm create](/cli/azure/vm). 

O exemplo a seguir cria duas VMs, chamadas `myVM1` e `myVM2`. Crie chaves SSH, se elas ainda não existirem em um local de chave padrão. Para usar um conjunto específico de chaves, use a opção `--ssh-key-value`.

#### <a name="create-myvm1-primary"></a>Criar myVM1 (primário):
```azurecli
az vm create \
     --resource-group myResourceGroup \
     --name myVM1 \
     --availability-set myAvailabilitySet \
     --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
     --size Standard_DS1_v2  \
     --generate-ssh-keys \
```

Quando a VM tiver sido criada, a CLI do Azure mostra informações semelhantes ao exemplo a seguir. (Anote `publicIpAddress`. Esse endereço é usado para acessar a VM.)

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westus",
  "macAddress": "00-0D-3A-36-2F-56",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.64.104.241",
  "resourceGroup": "myResourceGroup"
}
```

#### <a name="create-myvm2-replicate"></a>Criar myVM2 (replicação):
```azurecli
az vm create \
     --resource-group myResourceGroup \
     --name myVM2 \
     --availability-set myAvailabilitySet \
     --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
     --size Standard_DS1_v2  \
     --generate-ssh-keys \
```

Anote o `publicIpAddress` também após ele ter sido criado.

### <a name="open-the-tcp-port-for-connectivity"></a>Abrir a porta TCP para conectividade

A próxima etapa é configurar pontos de extremidade externos, que permitem acessar remotamente o banco de dados Oracle. Para configurar os pontos de extremidade externos, execute os comandos a seguir.

#### <a name="open-the-port-for-myvm1"></a>Abrir porta para myVM1:

```azurecli
az network nsg rule create --resource-group myResourceGroup\
    --nsg-name myVm1NSG --name allow-oracle\
    --protocol tcp --direction inbound --priority 999 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 1521 --access allow
```

O resultado deve ser semelhante à seguinte resposta:

```bash
{
  "access": "Allow",
  "description": null,
  "destinationAddressPrefix": "*",
  "destinationPortRange": "1521",
  "direction": "Inbound",
  "etag": "W/\"bd77dcae-e5fd-4bd6-a632-26045b646414\"",
  "id": "/subscriptions/<subscription-id>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myVmNSG/securityRules/allow-oracle",
  "name": "allow-oracle",
  "priority": 999,
  "protocol": "Tcp",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sourceAddressPrefix": "*",
  "sourcePortRange": "*"
}
```

#### <a name="open-the-port-for-myvm2"></a>Abra a porta para myVM2:

```azurecli
az network nsg rule create --resource-group myResourceGroup\
    --nsg-name myVm2NSG --name allow-oracle\
    --protocol tcp --direction inbound --priority 999 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 1521 --access allow
```

### <a name="connect-to-the-virtual-machine"></a>Conectar-se à máquina virtual

Use o seguinte comando para criar uma sessão SSH com a máquina virtual. Substitua o endereço IP pelo `publicIpAddress` de sua máquina virtual.

```bash 
ssh <publicIpAddress>
```

### <a name="create-the-database-on-myvm1-primary"></a>Crie banco de dados myVM1 (primário)

O software Oracle já está instalado na imagem do Marketplace, portanto, a próxima etapa é instalar o banco de dados. 

Execute o software como o superusuário 'oracle':

```bash
sudo su - oracle
```

Crie o banco de dados:

```bash
$ dbca -silent \
   -createDatabase \
   -templateName General_Purpose.dbc \
   -gdbname cdb1 \
   -sid cdb1 \
   -responseFile NO_VALUE \
   -characterSet AL32UTF8 \
   -sysPassword OraPasswd1 \
   -systemPassword OraPasswd1 \
   -createAsContainerDatabase true \
   -numberOfPDBs 1 \
   -pdbName pdb1 \
   -pdbAdminPassword OraPasswd1 \
   -databaseType MULTIPURPOSE \
   -automaticMemoryManagement false \
   -storageType FS \
   -ignorePreReqs
```
A saída deve ser semelhante à seguinte resposta:

```bash
Copying database files
1% complete
2% complete
8% complete
13% complete
19% complete
27% complete
Creating and starting Oracle instance
29% complete
32% complete
33% complete
34% complete
38% complete
42% complete
43% complete
45% complete
Completing Database Creation
48% complete
51% complete
53% complete
62% complete
70% complete
72% complete
Creating Pluggable Databases
78% complete
100% complete
Look at the log file "/u01/app/oracle/cfgtoollogs/dbca/cdb1/cdb1.log" for more details.
```

Defina as variáveis ORACLE_SID e ORACLE_HOME.

```bash
$ ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME
$ ORACLE_SID=cdb1; export ORACLE_SID
$ LD_LIBRARY_PATH=ORACLE_HOME/lib; export LD_LIBRARY_PATH
```

Opcionalmente, você pode adicionar ORACLE_HOME e ORACLE_SID ao arquivo .bashrc, de maneira que essas configurações sejam salvas para logons futuros:

```bash
# add oracle home
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
# add oracle sid
export ORACLE_SID=cdb1
# add Oracle library path
export LD_LIBRARY_PATH=$ORACLE_HOME/lib
```

### <a name="start-oracle-listener"></a>Iniciar o Oracle listener
```bash
$ lsnrctl start
```

### <a name="create-the-database-on-myvm2-replicate"></a>Crie banco de dados myVM2 (replicação)

```bash
sudo su - oracle
```
Crie o banco de dados:

```bash
$ dbca -silent \
   -createDatabase \
   -templateName General_Purpose.dbc \
   -gdbname cdb1 \
   -sid cdb1 \
   -responseFile NO_VALUE \
   -characterSet AL32UTF8 \
   -sysPassword OraPasswd1 \
   -systemPassword OraPasswd1 \
   -createAsContainerDatabase true \
   -numberOfPDBs 1 \
   -pdbName pdb1 \
   -pdbAdminPassword OraPasswd1 \
   -databaseType MULTIPURPOSE \
   -automaticMemoryManagement false \
   -storageType FS \
   -ignorePreReqs
```
Defina as variáveis ORACLE_SID e ORACLE_HOME.

```bash
$ ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME
$ ORACLE_SID=cdb1; export ORACLE_SID
$ LD_LIBRARY_PATH=ORACLE_HOME/lib; export LD_LIBRARY_PATH
```

Opcionalmente, você pode adicionar ORACLE_HOME e ORACLE_SID ao arquivo .bashrc, de maneira que essas configurações sejam salvas para logons futuros.

```bash
# add oracle home
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
# add oracle sid
export ORACLE_SID=cdb1
# add Oracle library path
export LD_LIBRARY_PATH=$ORACLE_HOME/lib
```

### <a name="start-oracle-listener"></a>Iniciar o Oracle listener
```bash
$ sudo su - oracle
$ lsnrctl start
```

## <a name="configure-golden-gate"></a>Configurar o Golden Gate 
Para configurar Golden Gate, execute as etapas nesta seção.

### <a name="enable-archive-log-mode-on-myvm1-primary"></a>Habilitar o modo de log de arquivo morto em myVM1 (primário)

```bash
$ sqlplus / as sysdba
SQL> SELECT log_mode FROM v$database;

LOG_MODE
------------
NOARCHIVELOG

SQL> SHUTDOWN IMMEDIATE;
SQL> STARTUP MOUNT;
SQL> ALTER DATABASE ARCHIVELOG;
SQL> ALTER DATABASE OPEN;
```
Habilite o registro em log forçado e verifique se há pelo menos um arquivo de log.

```bash
SQL> ALTER DATABASE FORCE LOGGING;
SQL> ALTER SYSTEM SWITCH LOGFILE;
SQL> ALTER SYSTEM set enable_goldengate_replication=true;
SQL> ALTER PLUGGABLE DATABASE PDB1 OPEN;
SQL> ALTER SESSION SET CONTAINER=PDB1;
SQL> ALTER DATABASE ADD SUPPLEMENTAL LOG DATA;
SQL> EXIT;
```

### <a name="download-golden-gate-software"></a>Baixar o software de Golden Gate
Para baixar e preparar o software de Oracle Golden Gate, conclua as seguintes etapas:

1. Baixe o arquivo **fbo_ggs_Linux_x64_shiphome.zip** da [página de download do Oracle Golden Gate](https://www.oracle.com/technetwork/middleware/goldengate/downloads/index.html). Sob o título do download **12.x.x.x do Oracle GoldenGate para Oracle Linux x86-64**, deve haver um conjunto de arquivos. zip para download.

2. Depois de baixar os arquivos .zip para o computador cliente, você poderá usar o protocolo SCP para copiar os arquivos na sua VM:

   ```bash
   $ scp fbo_ggs_Linux_x64_shiphome.zip <publicIpAddress>:<folder>
   ```

3. Mova os arquivos .zip para a pasta **/opt**. Em seguida, altere o proprietário dos arquivos da seguinte maneira:

   ```bash
   $ sudo su -
   # mv <folder>/*.zip /opt
   ```

4. Descompacte os arquivos (instale o utilitário unzip do Linux se ele ainda não estiver instalado):

   ```bash
   # yum install unzip
   # cd /opt
   # unzip fbo_ggs_Linux_x64_shiphome.zip
   ```

5. Alterar permissão:

   ```bash
   # chown -R oracle:oinstall /opt/fbo_ggs_Linux_x64_shiphome
   ```

### <a name="prepare-the-client-and-vm-to-run-x11-for-windows-clients-only"></a>Prepare o cliente e a VM para executar X11 (somente para clientes Windows)
Esta é uma etapa opcional. Essa é uma etapa opcional, você pode ignorá-la se estiver usando um cliente do Linux ou já tiver a instalação do x11.

1. Baixe o PuTTY e o Xming em seu computador Windows:

   * [Baixar PuTTY](https://www.putty.org/)
   * [Baixar Xming](https://xming.en.softonic.com/)

2. Depois de instalar o PuTTY, na pasta PuTTY (por exemplo, C:\Arquivos de Programa\PuTTY), execute puttygen.exe (Gerador de Chave PuTTY).

3. No Gerador de Chave PuTTY:

   - Para gerar uma chave, selecione o botão **Gerar**.
   - Copie o conteúdo da chave (**Ctrl+C**).
   - Selecione o botão **Salvar chave privada**.
   - Ignore o aviso que aparece e selecione **OK**.

   ![Captura de tela da página do gerador de chave PuTTY](./media/oracle-golden-gate/puttykeygen.png)

4. Em sua VM, execute estes comandos:

   ```bash
   # sudo su - oracle
   $ mkdir .ssh (if not already created)
   $ cd .ssh
   ```

5. Crie um arquivo chamado **authorized_keys**. Cole o conteúdo da chave nesse arquivo e salve-o.

   > [!NOTE]
   > A chave deve conter a cadeia de caracteres `ssh-rsa`. Além disso, o conteúdo da chave deve ser uma única linha de texto.
   >  

6. Inicie o PuTTY. No painel **Categoria**, selecione **Conexão** > **SSH** > **Autenticação**. Na caixa **Arquivo de chave privada para autenticação**, navegue até a chave que você gerou anteriormente.

   ![Captura de tela da página Instalar chave privada](./media/oracle-golden-gate/setprivatekey.png)

7. No painel **Categoria**, selecione **Conexão** > **SSH** > **X11**. Em seguida, selecione a caixa **Habilitar encaminhamento X11**.

   ![Captura de tela da página Habilitar X11](./media/oracle-golden-gate/enablex11.png)

8. No painel **Categoria**, vá para **Sessão**. Insira as informações do host e selecione **Abrir**.

   ![Captura de tela da página de sessão](./media/oracle-golden-gate/puttysession.png)

### <a name="install-golden-gate-software"></a>Instalar o software de Golden Gate

Para instalar o Oracle Golden Gate, conclua as etapas a seguir:

1. Entre como oracle. (Você deve ser capaz de entrar sem inserir uma senha.) Verifique se Xming está em execução antes de iniciar a instalação.
 
   ```bash
   $ cd /opt/fbo_ggs_Linux_x64_shiphome/Disk1
   $ ./runInstaller
   ```
2. Selecione 'Oracle GoldenGate para o banco de dados Oracle 12c'. Em seguida, selecione **Avançar** para continuar.

   ![Captura de tela da página Selecionar Instalação do instalador](./media/oracle-golden-gate/golden_gate_install_01.png)

3. Altere o local do software. Em seguida, selecione a caixa **Iniciar Gerenciador** e insira o local do banco de dados. Selecione **Avançar** para continuar.

   ![Captura de tela da página Selecionar Instalação](./media/oracle-golden-gate/golden_gate_install_02.png)

4. Altere o diretório de inventário e, em seguida, selecione **Avançar** para continuar.

   ![Captura de tela da página Selecionar Instalação](./media/oracle-golden-gate/golden_gate_install_03.png)

5. Na tela de **Resumo**, selecione **Instalar** para continuar.

   ![Captura de tela da página Selecionar Instalação do instalador](./media/oracle-golden-gate/golden_gate_install_04.png)

6. Pode ser solicitado que você execute um script como 'root'. Nesse caso, abra uma sessão separada, ssh para a VM, sudo para a raiz e, em seguida, execute o script. Selecione **OK** para continuar.

   ![Captura de tela da página Selecionar Instalação](./media/oracle-golden-gate/golden_gate_install_05.png)

7. Quando a instalação for concluída, selecione **Fechar** para concluir o processo.

   ![Captura de tela da página Selecionar Instalação](./media/oracle-golden-gate/golden_gate_install_06.png)

### <a name="set-up-service-on-myvm1-primary"></a>Configuração de serviço no myVM1 (primário)

1. Criar ou atualizar o arquivo tnsnames.ora:

   ```bash
   $ cd $ORACLE_HOME/network/admin
   $ vi tnsnames.ora

   cdb1=
    (DESCRIPTION=
      (ADDRESS=
        (PROTOCOL=TCP)
        (HOST=localhost)
        (PORT=1521)
      )
      (CONNECT_DATA=
        (SERVER=dedicated)
        (SERVICE_NAME=cdb1)
      )
    )

   pdb1=
    (DESCRIPTION=
      (ADDRESS=
        (PROTOCOL=TCP)
        (HOST=localhost)
        (PORT=1521)
      )
      (CONNECT_DATA=
        (SERVER=dedicated)
        (SERVICE_NAME=pdb1)
      )
    )
   ```

2. Crie as contas de usuário e de proprietário de Golden Gate.

   > [!NOTE]
   > A conta do proprietário deve ter o prefixo de C##.
   >

    ```bash
    $ sqlplus / as sysdba
    SQL> CREATE USER C##GGADMIN identified by ggadmin;
    SQL> EXEC dbms_goldengate_auth.grant_admin_privilege('C##GGADMIN',container=>'ALL');
    SQL> GRANT DBA to C##GGADMIN container=all;
    SQL> connect C##GGADMIN/ggadmin
    SQL> ALTER SESSION SET CONTAINER=PDB1;
    SQL> EXIT;
    ```

3. Crie a conta de usuário de teste Golden Gate:

   ```bash
   $ cd /u01/app/oracle/product/12.1.0/oggcore_1
   $ sqlplus system/OraPasswd1@pdb1
   SQL> CREATE USER test identified by test DEFAULT TABLESPACE USERS TEMPORARY TABLESPACE TEMP;
   SQL> GRANT connect, resource, dba TO test;
   SQL> ALTER USER test QUOTA 100M on USERS;
   SQL> connect test/test@pdb1
   SQL> @demo_ora_create
   SQL> @demo_ora_insert
   SQL> EXIT;
   ```

4. Configure o arquivo de parâmetro de extração.

   Inicie a interface de linha de comando de Golden Gate (ggsci):

   ```bash
   $ sudo su - oracle
   $ cd /u01/app/oracle/product/12.1.0/oggcore_1
   $ ./ggsci
   GGSCI> DBLOGIN USERID test@pdb1, PASSWORD test
   Successfully logged into database  pdb1
   GGSCI>  ADD SCHEMATRANDATA pdb1.test
   2017-05-23 15:44:25  INFO    OGG-01788  SCHEMATRANDATA has been added on schema test.
   2017-05-23 15:44:25  INFO    OGG-01976  SCHEMATRANDATA for scheduling columns has been added on schema test.

   GGSCI> EDIT PARAMS EXTORA
   ```
5. Adicione o seguinte para o arquivo de parâmetro de EXTRAÇÃO (usando comandos vi). Pressione a tecla Esc, ': wq!' para salvar o arquivo. 

   ```bash
   EXTRACT EXTORA
   USERID C##GGADMIN, PASSWORD ggadmin
   RMTHOST 10.0.0.5, MGRPORT 7809
   RMTTRAIL ./dirdat/rt  
   DDL INCLUDE MAPPED
   DDLOPTIONS REPORT 
   LOGALLSUPCOLS
   UPDATERECORDFORMAT COMPACT
   TABLE pdb1.test.TCUSTMER;
   TABLE pdb1.test.TCUSTORD;
   ```
6. Registrar extrair – extrair integrado:

   ```bash
   $ cd /u01/app/oracle/product/12.1.0/oggcore_1
   $ ./ggsci

   GGSCI> dblogin userid C##GGADMIN, password ggadmin
   Successfully logged into database CDB$ROOT.

   GGSCI> REGISTER EXTRACT EXTORA DATABASE CONTAINER(pdb1)

   2017-05-23 15:58:34  INFO    OGG-02003  Extract EXTORA successfully registered with database at SCN 1821260.

   GGSCI> exit
   ```
7. Configurar pontos de verificação de extração e iniciar a extração em tempo real:

   ```bash
   $ ./ggsci
   GGSCI>  ADD EXTRACT EXTORA, INTEGRATED TRANLOG, BEGIN NOW
   EXTRACT (Integrated) added.

   GGSCI>  ADD RMTTRAIL ./dirdat/rt, EXTRACT EXTORA, MEGABYTES 10
   RMTTRAIL added.

   GGSCI>  START EXTRACT EXTORA

   Sending START request to MANAGER ...
   EXTRACT EXTORA starting

   GGSCI > info all

   Program     Status      Group       Lag at Chkpt  Time Since Chkpt

   MANAGER     RUNNING
   EXTRACT     RUNNING     EXTORA      00:00:11      00:00:04
   ```
   Nesta etapa, você deve encontrar o SCN inicial, que será usado mais tarde, em uma seção diferente:

   ```bash
   $ sqlplus / as sysdba
   SQL> alter session set container = pdb1;
   SQL> SELECT current_scn from v$database;
   CURRENT_SCN
   -----------
      1857887
   SQL> EXIT;
   ```

   ```bash
   $ ./ggsci
   GGSCI> EDIT PARAMS INITEXT
   ```

   ```bash
   EXTRACT INITEXT
   USERID C##GGADMIN, PASSWORD ggadmin
   RMTHOST 10.0.0.5, MGRPORT 7809
   RMTTASK REPLICAT, GROUP INITREP
   TABLE pdb1.test.*, SQLPREDICATE 'AS OF SCN 1857887'; 
   ```

   ```bash
   GGSCI> ADD EXTRACT INITEXT, SOURCEISTABLE
   ```

### <a name="set-up-service-on-myvm2-replicate"></a>Configuração de serviço no myVM2 (replicação)


1. Criar ou atualizar o arquivo tnsnames.ora:

   ```bash
   $ cd $ORACLE_HOME/network/admin
   $ vi tnsnames.ora

   cdb1=
    (DESCRIPTION=
      (ADDRESS=
        (PROTOCOL=TCP)
        (HOST=localhost)
        (PORT=1521)
      )
      (CONNECT_DATA=
        (SERVER=dedicated)
        (SERVICE_NAME=cdb1)
      )
    )

   pdb1=
    (DESCRIPTION=
      (ADDRESS=
        (PROTOCOL=TCP)
        (HOST=localhost)
        (PORT=1521)
      )
      (CONNECT_DATA=
        (SERVER=dedicated)
        (SERVICE_NAME=pdb1)
      )
    )
   ```

2. Crie uma conta de replicação:

   ```bash
   $ sqlplus / as sysdba
   SQL> alter session set container = pdb1;
   SQL> create user repuser identified by rep_pass container=current;
   SQL> grant dba to repuser;
   SQL> exec dbms_goldengate_auth.grant_admin_privilege('REPUSER',container=>'PDB1');
   SQL> connect repuser/rep_pass@pdb1 
   SQL> EXIT;
   ```

3. Crie uma conta de usuário de teste Golden Gate:

   ```bash
   $ cd /u01/app/oracle/product/12.1.0/oggcore_1
   $ sqlplus system/OraPasswd1@pdb1
   SQL> CREATE USER test identified by test DEFAULT TABLESPACE USERS TEMPORARY TABLESPACE TEMP;
   SQL> GRANT connect, resource, dba TO test;
   SQL> ALTER USER test QUOTA 100M on USERS;
   SQL> connect test/test@pdb1
   SQL> @demo_ora_create
   SQL> EXIT;
   ```

4. Arquivo de parâmetro de réplicas para replicar as alterações: 

   ```bash
   $ cd /u01/app/oracle/product/12.1.0/oggcore_1
   $ ./ggsci
   GGSCI> EDIT PARAMS REPORA  
   ```
   Conteúdo do arquivo de parâmetro REPORA:

   ```bash
   REPLICAT REPORA
   ASSUMETARGETDEFS
   DISCARDFILE ./dirrpt/repora.dsc, PURGE, MEGABYTES 100
   DDL INCLUDE MAPPED
   DDLOPTIONS REPORT
   DBOPTIONS INTEGRATEDPARAMS(parallelism 6)
   USERID repuser@pdb1, PASSWORD rep_pass
   MAP pdb1.test.*, TARGET pdb1.test.*;
   ```

5. Configure um ponto de verificação de replicação:

   ```bash
   GGSCI> ADD REPLICAT REPORA, INTEGRATED, EXTTRAIL ./dirdat/rt
   GGSCI> EDIT PARAMS INITREP

   ```

   ```bash
   REPLICAT INITREP
   ASSUMETARGETDEFS
   DISCARDFILE ./dirrpt/tcustmer.dsc, APPEND
   USERID repuser@pdb1, PASSWORD rep_pass
   MAP pdb1.test.*, TARGET pdb1.test.*;   
   ```

   ```bash
   GGSCI> ADD REPLICAT INITREP, SPECIALRUN
   ```

### <a name="set-up-the-replication-myvm1-and-myvm2"></a>Configurar a replicação (myVM1 e myVM2)

#### <a name="1-set-up-the-replication-on-myvm2-replicate"></a>1. Configurar a replicação em myVM2 (replicação)

  ```bash
  $ cd /u01/app/oracle/product/12.1.0/oggcore_1
  $ ./ggsci
  GGSCI> EDIT PARAMS MGR
  ```
Atualize o arquivo com o seguinte:

  ```bash
  PORT 7809
  ACCESSRULE, PROG *, IPADDR *, ALLOW
  ```
Em seguida, reinicie o serviço do Gerenciador:

  ```bash
  GGSCI> STOP MGR
  GGSCI> START MGR
  GGSCI> EXIT
  ```

#### <a name="2-set-up-the-replication-on-myvm1-primary"></a>2. Configurar a replicação em myVM1 (primário)

Inicie o carregamento inicial e verifique se há erros:

```bash
$ cd /u01/app/oracle/product/12.1.0/oggcore_1
$ ./ggsci
GGSCI> START EXTRACT INITEXT
GGSCI> VIEW REPORT INITEXT
```
#### <a name="3-set-up-the-replication-on-myvm2-replicate"></a>3. Configurar a replicação em myVM2 (replicação)

Alteração de número SCN com o número obtido antes:

  ```bash
  $ cd /u01/app/oracle/product/12.1.0/oggcore_1
  $ ./ggsci
  START REPLICAT REPORA, AFTERCSN 1857887
  ```
A replicação foi iniciada e você pode testá-la, inserindo novos registros em tabelas de TESTE.


### <a name="view-job-status-and-troubleshooting"></a>Exibir o status do trabalho e solução de problemas

#### <a name="view-reports"></a>Exibir relatórios
Para exibir relatórios em myVM1, execute os seguintes comandos:

  ```bash
  GGSCI> VIEW REPORT EXTORA 
  ```
 
Para exibir relatórios em myVM2, execute os seguintes comandos:

  ```bash
  GGSCI> VIEW REPORT REPORA
  ```

#### <a name="view-status-and-history"></a>Exibir o status e histórico
Para exibir o status e histórico em myVM1, execute os seguintes comandos:

  ```bash
  GGSCI> dblogin userid c##ggadmin, password ggadmin 
  GGSCI> INFO EXTRACT EXTORA, DETAIL
  ```

Para exibir o status e histórico em myVM2, execute os seguintes comandos:

  ```bash
  GGSCI> dblogin userid repuser@pdb1 password rep_pass 
  GGSCI> INFO REP REPORA, DETAIL
  ```
Isso conclui a instalação e configuração do Golden Gate no Oracle linux.


## <a name="delete-the-virtual-machine"></a>Excluir a máquina virtual

Quando não é mais necessário, o comando a seguir pode ser usado para remover o grupo de recursos, a VM e todos os recursos relacionados.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

[Tutorial Criar máquinas virtuais altamente disponíveis](../../linux/create-cli-complete.md)

[Explorar as amostras de CLI de implantação de VM](../../linux/cli-samples.md)
