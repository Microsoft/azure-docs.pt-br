---
title: Implementar o Oracle Data Guard em uma máquina virtual Linux do Azure | Microsoft Docs
description: Execute rapidamente o Oracle Data Guard no ambiente do Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: romitgirdhar
manager: jeconnoc
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
ms.openlocfilehash: c98e59cd0e547381d6b173b3a4b91c3a3e27b3a8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60771712"
---
# <a name="implement-oracle-data-guard-on-an-azure-linux-virtual-machine"></a>Implementar o Oracle Data Guard em uma máquina virtual Linux do Azure 

A CLI do Azure é usada para criar e gerenciar recursos do Azure da linha de comando ou em scripts. Este artigo descreve como usar a CLI do Azure para implantar um banco de dados Oracle Database 12c da imagem do Azure Marketplace. Este artigo mostra a você passo a passo como instalar e configurar o Data Guard em uma VM (máquina virtual) do Azure.

Antes de começar, verifique se a CLI do Azure está instalada. Para obter mais informações, consulte o [Guia de instalação da CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="prepare-the-environment"></a>Preparar o ambiente
### <a name="assumptions"></a>Suposições

Para instalar o Oracle Data Guard, você precisa criar duas VMs do Azure no mesmo conjunto de disponibilidade:

- A VM primária (myVM1) tem uma instância do Oracle em execução.
- A VM em espera (myVM2) tem o software Oracle apenas instalado.

A imagem do Marketplace usada para criar as VMs é Oracle:Oracle-Database-Ee:12.1.0.2:latest.

### <a name="sign-in-to-azure"></a>Entrar no Azure 

Entre na sua assinatura do Azure usando o comando [az login](/cli/azure/reference-index) e siga as instruções na tela.

```azurecli
az login
```

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos usando o comando [az group create](/cli/azure/group). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. 

O exemplo a seguir cria um grupo de recursos denominado `myResourceGroup` no local `westus`:

```azurecli
az group create --name myResourceGroup --location westus
```

### <a name="create-an-availability-set"></a>Criar um conjunto de disponibilidade

A criação de um conjunto de disponibilidade é opcional, mas é recomendável. Para obter mais informações, confira [Diretrizes de conjuntos de disponibilidade do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines).

```azurecli
az vm availability-set create \
    --resource-group myResourceGroup \
    --name myAvailabilitySet \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

### <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Crie uma VM com o comando [az vm create](/cli/azure/vm). 

O exemplo a seguir cria duas VMs, chamadas `myVM1` e `myVM2`. Ele também criará chaves SSH, se elas ainda não existirem em um local de chave padrão. Para usar um conjunto específico de chaves, use a opção `--ssh-key-value`.

Criar myVM1 (primário):
```azurecli
az vm create \
     --resource-group myResourceGroup \
     --name myVM1 \
     --availability-set myAvailabilitySet \
     --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
     --size Standard_DS1_v2  \
     --admin-username azureuser \
     --generate-ssh-keys \
```

Depois de criar a VM, a CLI do Azure exibe informações semelhantes ao exemplo a seguir. Observe o valor de `publicIpAddress`. Você pode usar esse endereço para acessar a VM.

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

Criar myVM2 (em espera):
```azurecli
az vm create \
     --resource-group myResourceGroup \
     --name myVM2 \
     --availability-set myAvailabilitySet \
     --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
     --size Standard_DS1_v2  \
     --admin-username azureuser \
     --generate-ssh-keys \
```

Observe o valor de `publicIpAddress` depois de criar myVM2.

### <a name="open-the-tcp-port-for-connectivity"></a>Abrir a porta TCP para conectividade

Esta etapa configura pontos de extremidade externos, que permitem o acesso remoto ao banco de dados Oracle.

Abrir porta para myVM1:

```azurecli
az network nsg rule create --resource-group myResourceGroup\
    --nsg-name myVM1NSG --name allow-oracle\
    --protocol tcp --direction inbound --priority 999 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 1521 --access allow
```

O resultado deve ser semelhante à resposta a seguir:

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

Abra a porta para myVM2:

```azurecli
az network nsg rule create --resource-group myResourceGroup\
    --nsg-name myVM2NSG --name allow-oracle\
    --protocol tcp --direction inbound --priority 999 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 1521 --access allow
```

### <a name="connect-to-the-virtual-machine"></a>Conectar-se à máquina virtual

Use o seguinte comando para criar uma sessão SSH com a máquina virtual. Substitua o endereço IP pelo valor de `publicIpAddress` de sua máquina virtual.

```bash 
$ ssh azureuser@<publicIpAddress>
```

### <a name="create-the-database-on-myvm1-primary"></a>Crie banco de dados myVM1 (primário)

O software Oracle já está instalado na imagem do Marketplace, portanto, a próxima etapa é instalar o banco de dados. 

Mude para o superusuário Oracle:

```bash
$ sudo su - oracle
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
Look at the log file "/u01/app/oracle/cfgtoollogs/dbca/cdb1/cdb1.log" for further details.
```

Defina as variáveis ORACLE_SID e ORACLE_HOME:

```bash
$ ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME
$ ORACLE_SID=cdb1; export ORACLE_SID
```

Opcionalmente, você pode adicionar ORACLE_HOME e ORACLE_SID ao arquivo /home/oracle/.bashrc, de maneira que essas configurações sejam salvas para logons futuros:

```bash
# add oracle home
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
# add oracle sid
export ORACLE_SID=cdb1
```

## <a name="configure-data-guard"></a>Configurar o Data Guard

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
Habilite o registro em log forçado e verifique se há pelo menos um arquivo de log:

```bash
SQL> ALTER DATABASE FORCE LOGGING;
SQL> ALTER SYSTEM SWITCH LOGFILE;
```

Criar logs de restauração em espera:

```bash
SQL> ALTER DATABASE ADD STANDBY LOGFILE ('/u01/app/oracle/oradata/cdb1/standby_redo01.log') SIZE 50M;
SQL> ALTER DATABASE ADD STANDBY LOGFILE ('/u01/app/oracle/oradata/cdb1/standby_redo02.log') SIZE 50M;
SQL> ALTER DATABASE ADD STANDBY LOGFILE ('/u01/app/oracle/oradata/cdb1/standby_redo03.log') SIZE 50M;
SQL> ALTER DATABASE ADD STANDBY LOGFILE ('/u01/app/oracle/oradata/cdb1/standby_redo04.log') SIZE 50M;
```

Ligue o Flashback (que facilita muito a recuperação) e defina STANDBY\_FILE\_MANAGEMENT como automático. Saia do SQL*Plus depois disso.

```bash
SQL> ALTER DATABASE FLASHBACK ON;
SQL> ALTER SYSTEM SET STANDBY_FILE_MANAGEMENT=AUTO;
SQL> EXIT;
```

### <a name="set-up-service-on-myvm1-primary"></a>Configuração de serviço no myVM1 (primário)

Edite ou crie o arquivo tnsnames.ora, que está na pasta $ORACLE_HOME\network\admin.

Adicione as seguintes entradas:

```bash
cdb1 =
  (DESCRIPTION =
    (ADDRESS_LIST =
      (ADDRESS = (PROTOCOL = TCP)(HOST = myVM1)(PORT = 1521))
    )
    (CONNECT_DATA =
      (SID = cdb1)
    )
  )

cdb1_stby =
  (DESCRIPTION =
    (ADDRESS_LIST =
      (ADDRESS = (PROTOCOL = TCP)(HOST = myVM2)(PORT = 1521))
    )
    (CONNECT_DATA =
      (SID = cdb1)
    )
  )
```

Edite ou crie o arquivo listener.ora, que está na pasta $ORACLE_HOME\network\admin.

Adicione as seguintes entradas:

```bash
LISTENER =
  (DESCRIPTION_LIST =
    (DESCRIPTION =
      (ADDRESS = (PROTOCOL = TCP)(HOST = myVM1)(PORT = 1521))
      (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1521))
    )
  )

SID_LIST_LISTENER =
  (SID_LIST =
    (SID_DESC =
      (GLOBAL_DBNAME = cdb1_DGMGRL)
      (ORACLE_HOME = /u01/app/oracle/product/12.1.0/dbhome_1)
      (SID_NAME = cdb1)
    )
  )

ADR_BASE_LISTENER = /u01/app/oracle
```

Habilite o Data Guard Broker:
```bash
$ sqlplus / as sysdba
SQL> ALTER SYSTEM SET dg_broker_start=true;
SQL> EXIT;
```
Inicie o ouvinte:

```bash
$ lsnrctl stop
$ lsnrctl start
```

### <a name="set-up-service-on-myvm2-standby"></a>Configuração de serviço no myVM2 (em espera)

SSH para myVM2:

```bash 
$ ssh azureuser@<publicIpAddress>
```

Faça logon como Oracle:

```bash
$ sudo su - oracle
```

Edite ou crie o arquivo tnsnames.ora, que está na pasta $ORACLE_HOME\network\admin.

Adicione as seguintes entradas:

```bash
cdb1 =
  (DESCRIPTION =
    (ADDRESS_LIST =
      (ADDRESS = (PROTOCOL = TCP)(HOST = myVM1)(PORT = 1521))
    )
    (CONNECT_DATA =
      (SID = cdb1)
    )
  )

cdb1_stby =
  (DESCRIPTION =
    (ADDRESS_LIST =
      (ADDRESS = (PROTOCOL = TCP)(HOST = myVM2)(PORT = 1521))
    )
    (CONNECT_DATA =
      (SID = cdb1)
    )
  )
```

Edite ou crie o arquivo listener.ora, que está na pasta $ORACLE_HOME\network\admin.

Adicione as seguintes entradas:

```bash
LISTENER =
  (DESCRIPTION_LIST =
    (DESCRIPTION =
      (ADDRESS = (PROTOCOL = TCP)(HOST = myVM2)(PORT = 1521))
      (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1521))
    )
  )

SID_LIST_LISTENER =
  (SID_LIST =
    (SID_DESC =
      (GLOBAL_DBNAME = cdb1_DGMGRL)
      (ORACLE_HOME = /u01/app/oracle/product/12.1.0/dbhome_1)
      (SID_NAME = cdb1)
    )
  )

ADR_BASE_LISTENER = /u01/app/oracle
```

Inicie o ouvinte:

```bash
$ lsnrctl stop
$ lsnrctl start
```


### <a name="restore-the-database-to-myvm2-standby"></a>Restaure banco de dados para myVM2 (em espera)

Crie o arquivo de parâmetro /tmp/initcdb1_stby.ora com o seguinte conteúdo:
```bash
*.db_name='cdb1'
```

Crie as pastas:

```bash
mkdir -p /u01/app/oracle/oradata/cdb1/pdbseed
mkdir -p /u01/app/oracle/oradata/cdb1/pdb1
mkdir -p /u01/app/oracle/fast_recovery_area/cdb1
mkdir -p /u01/app/oracle/admin/cdb1/adump
```

Crie um arquivo de senha:

```bash
$ orapwd file=/u01/app/oracle/product/12.1.0/dbhome_1/dbs/orapwcdb1 password=OraPasswd1 entries=10
```
Inicie o banco de dados em myVM2:

```bash
$ export ORACLE_SID=cdb1
$ sqlplus / as sysdba

SQL> STARTUP NOMOUNT PFILE='/tmp/initcdb1_stby.ora';
SQL> EXIT;
```

Restaure o banco de dados usando a ferramenta RMAN:

```bash
$ rman TARGET sys/OraPasswd1@cdb1 AUXILIARY sys/OraPasswd1@cdb1_stby
```

Executar os seguintes comandos na RMAN:
```bash
DUPLICATE TARGET DATABASE
  FOR STANDBY
  FROM ACTIVE DATABASE
  DORECOVER
  SPFILE
    SET db_unique_name='CDB1_STBY' COMMENT 'Is standby'
  NOFILENAMECHECK;
```

Você verá mensagens semelhantes à seguinte quando o comando for concluído. Saia da RMAN.
```bash
media recovery complete, elapsed time: 00:00:00
Finished recover at 29-JUN-17
Finished Duplicate Db at 29-JUN-17

RMAN> EXIT;
```

Opcionalmente, você pode adicionar ORACLE_HOME e ORACLE_SID ao arquivo /home/oracle/.bashrc, de maneira que essas configurações sejam salvas para logons futuros:

```bash
# add oracle home
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
# add oracle sid
export ORACLE_SID=cdb1
```

Habilite o Data Guard Broker:
```bash
$ sqlplus / as sysdba
SQL> ALTER SYSTEM SET dg_broker_start=true;
SQL> EXIT;
```

### <a name="configure-data-guard-broker-on-myvm1-primary"></a>Configurar o Data Guard Broker em myVM1 (primário)

Inicie o Data Guard Manager e faça logon usando SYS e uma senha. (Não use a autenticação do SO.) Realize o que é descrito a seguir:

```bash
$ dgmgrl sys/OraPasswd1@cdb1
DGMGRL for Linux: Version 12.1.0.2.0 - 64bit Production

Copyright (c) 2000, 2013, Oracle. All rights reserved.

Welcome to DGMGRL, type "help" for information.
Connected as SYSDBA.
DGMGRL> CREATE CONFIGURATION my_dg_config AS PRIMARY DATABASE IS cdb1 CONNECT IDENTIFIER IS cdb1;
Configuration "my_dg_config" created with primary database "cdb1"
DGMGRL> ADD DATABASE cdb1_stby AS CONNECT IDENTIFIER IS cdb1_stby MAINTAINED AS PHYSICAL;
Database "cdb1_stby" added
DGMGRL> ENABLE CONFIGURATION;
Enabled.
```

Examine a configuração:
```bash
DGMGRL> SHOW CONFIGURATION;

Configuration - my_dg_config

  Protection Mode: MaxPerformance
  Members:
  cdb1      - Primary database
    cdb1_stby - Physical standby database

Fast-Start Failover: DISABLED

Configuration Status:
SUCCESS   (status updated 26 seconds ago)
```

Você concluiu a configuração do Oracle Data Guard. A próxima seção mostra como testar a conectividade e fazer transições.

### <a name="connect-the-database-from-the-client-machine"></a>Conectar o banco de dados do computador cliente

Atualizar ou criar o arquivo tnsnames.ora no computador cliente. Esse arquivo geralmente está em $ORACLE_HOME\network\admin.

Substitua os endereços IP pelos valores de `publicIpAddress` para myVM1 e myVM2:

```bash
cdb1=
  (DESCRIPTION=
    (ADDRESS=
      (PROTOCOL=TCP)
      (HOST=<myVM1 IP address>)
      (PORT=1521)
    )
    (CONNECT_DATA=
      (SERVER=dedicated)
      (SERVICE_NAME=cdb1)
    )
  )

cdb1_stby=
  (DESCRIPTION=
    (ADDRESS=
      (PROTOCOL=TCP)
      (HOST=<myVM2 IP address>)
      (PORT=1521)
    )
    (CONNECT_DATA=
      (SERVER=dedicated)
      (SERVICE_NAME=cdb1_stby)
    )
  )
```

Inicie o SQL*Plus:

```bash
$ sqlplus sys/OraPasswd1@cdb1
SQL*Plus: Release 12.2.0.1.0 Production on Wed May 10 14:18:31 2017

Copyright (c) 1982, 2016, Oracle.  All rights reserved.

Connected to:
Oracle Database 12c Enterprise Edition Release 12.1.0.2.0 - 64bit Production
With the Partitioning, OLAP, Advanced Analytics and Real Application Testing options

SQL>
```
## <a name="test-the-data-guard-configuration"></a>Testar as configurações do Data Guard

### <a name="switch-over-the-database-on-myvm1-primary"></a>Realizar a transição do banco de dados em myVM1 (primário)

Para mudar de primário para em espera (cdb1 para cdb1_stby):

```bash
$ dgmgrl sys/OraPasswd1@cdb1
DGMGRL for Linux: Version 12.1.0.2.0 - 64bit Production

Copyright (c) 2000, 2013, Oracle. All rights reserved.

Welcome to DGMGRL, type "help" for information.
Connected as SYSDBA.
DGMGRL> SWITCHOVER TO cdb1_stby;
Performing switchover NOW, please wait...
Operation requires a connection to instance "cdb1" on database "cdb1_stby"
Connecting to instance "cdb1"...
Connected as SYSDBA.
New primary database "cdb1_stby" is opening...
Operation requires start up of instance "cdb1" on database "cdb1"
Starting instance "cdb1"...
ORACLE instance started.
Database mounted.
Switchover succeeded, new primary is "cdb1_stby"
DGMGRL>
```

Agora você pode se conectar ao banco de dados em espera.

Inicie o SQL*Plus:

```bash

$ sqlplus sys/OraPasswd1@cdb1_stby
SQL*Plus: Release 12.2.0.1.0 Production on Wed May 10 14:18:31 2017

Copyright (c) 1982, 2016, Oracle.  All rights reserved.

Connected to:
Oracle Database 12c Enterprise Edition Release 12.1.0.2.0 - 64bit Production
With the Partitioning, OLAP, Advanced Analytics and Real Application Testing options

SQL>
```

### <a name="switch-over-the-database-on-myvm2-standby"></a>Realizar a transição do banco de dados em myVM2 (em espera)

Para realizar a transição, execute o seguinte em myVM2:
```bash
$ dgmgrl sys/OraPasswd1@cdb1_stby
DGMGRL for Linux: Version 12.1.0.2.0 - 64bit Production

Copyright (c) 2000, 2013, Oracle. All rights reserved.

Welcome to DGMGRL, type "help" for information.
Connected as SYSDBA.
DGMGRL> SWITCHOVER TO cdb1;
Performing switchover NOW, please wait...
Operation requires a connection to instance "cdb1" on database "cdb1"
Connecting to instance "cdb1"...
Connected as SYSDBA.
New primary database "cdb1" is opening...
Operation requires start up of instance "cdb1" on database "cdb1_stby"
Starting instance "cdb1"...
ORACLE instance started.
Database mounted.
Switchover succeeded, new primary is "cdb1"
```

Novamente, você agora deve ser capaz de se conectar ao banco de dados primário.

Inicie o SQL*Plus:

```bash

$ sqlplus sys/OraPasswd1@cdb1
SQL*Plus: Release 12.2.0.1.0 Production on Wed May 10 14:18:31 2017

Copyright (c) 1982, 2016, Oracle.  All rights reserved.

Connected to:
Oracle Database 12c Enterprise Edition Release 12.1.0.2.0 - 64bit Production
With the Partitioning, OLAP, Advanced Analytics and Real Application Testing options

SQL>
```

Você concluiu a instalação e configuração do Data Guard no Oracle Linux.


## <a name="delete-the-virtual-machine"></a>Excluir a máquina virtual

Quando a VM não for mais necessária, o comando abaixo poderá ser usado para remover o grupo de recursos, a VM e todos os recursos relacionados:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

[Tutorial: Criar máquinas virtuais altamente disponíveis](../../linux/create-cli-complete.md)

[Explorar exemplos da CLI do Azure de implantação de VM](../../linux/cli-samples.md)
