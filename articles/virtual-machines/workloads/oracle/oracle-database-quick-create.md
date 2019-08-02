---
title: Criar um banco de dados Oracle em uma VM do Azure | Microsoft Docs
description: Coloque rapidamente em funcionamento um banco de dados Oracle Database 12c no ambiente do Azure.
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
ms.openlocfilehash: 26c6abc75e653b489a7385c423a9b2d00a4ed063
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705301"
---
# <a name="create-an-oracle-database-in-an-azure-vm"></a>Criar um Banco de Dados Oracle em uma VM do Azure

Esse guia detalha como usar a CLI do Azure para implantar uma máquina virtual do Azure a partir da [imagem da galeria do marketplace da Oracle](https://azuremarketplace.microsoft.com/marketplace/apps/Oracle.OracleDatabase12102EnterpriseEdition?tab=Overview) a fim de criar um banco de dados Oracle 12c. Depois que o servidor for implantado, conecte-se por meio de SSH para configurar o banco de dados Oracle. 

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este guia de início rápido exigirá a execução da CLI do Azure versão 2.0.4 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. 

O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* no local *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```
## <a name="create-virtual-machine"></a>Criar máquina virtual

Para criar uma VM (máquina virtual), use o comando [az vm create](/cli/azure/vm). 

O exemplo a seguir cria uma VM chamada `myVM`. Ele também criará chaves SSH, se elas ainda não existirem em um local de chave padrão. Para usar um conjunto específico de chaves, use a opção `--ssh-key-value`.  

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
    --size Standard_DS2_v2 \
    --admin-username azureuser \
    --generate-ssh-keys
```

Depois de criar a VM, a CLI do Azure exibe informações semelhantes ao exemplo a seguir. Observe o valor de `publicIpAddress`. Você pode usar esse endereço para acessar a VM.

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/{snip}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westus",
  "macAddress": "00-0D-3A-36-2F-56",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.64.104.241",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="connect-to-the-vm"></a>Conectar-se à VM

Para criar uma sessão SSH com a VM, use o comando a seguir. Substitua o endereço IP pelo valor `publicIpAddress` para a sua VM.

```bash 
ssh azureuser@<publicIpAddress>
```

## <a name="create-the-database"></a>Criar o banco de dados

O software Oracle já está instalado na imagem do Marketplace. Crie um banco de dados de exemplo da seguinte maneira. 

1.  Altere para o superusuário *oracle* e inicialize o ouvinte para o registro em log:

    ```bash
    $ sudo su - oracle
    $ lsnrctl start
    ```

    A saída deverá ser semelhante a esta:

    ```bash
    Copyright (c) 1991, 2014, Oracle.  All rights reserved.

    Starting /u01/app/oracle/product/12.1.0/dbhome_1/bin/tnslsnr: please wait...

    TNSLSNR for Linux: Version 12.1.0.2.0 - Production
    Log messages written to /u01/app/oracle/diag/tnslsnr/myVM/listener/alert/log.xml
    Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=myVM.twltkue3xvsujaz1bvlrhfuiwf.dx.internal.cloudapp.net)(PORT=1521)))

    Connecting to (ADDRESS=(PROTOCOL=tcp)(HOST=)(PORT=1521))
    STATUS of the LISTENER
    ------------------------
    Alias                     LISTENER
    Version                   TNSLSNR for Linux: Version 12.1.0.2.0 - Production
    Start Date                23-MAR-2017 15:32:08
    Uptime                    0 days 0 hr. 0 min. 0 sec
    Trace Level               off
    Security                  ON: Local OS Authentication
    SNMP                      OFF
    Listener Log File         /u01/app/oracle/diag/tnslsnr/myVM/listener/alert/log.xml
    Listening Endpoints Summary...
    (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=myVM.twltkue3xvsujaz1bvlrhfuiwf.dx.internal.cloudapp.net)(PORT=1521)))
    The listener supports no services
    The command completed successfully
    ```

2.  Crie o banco de dados:

    ```bash
    dbca -silent \
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

    A criação do banco de dados demora alguns minutos.

3. Definir variáveis do Oracle

Antes de se conectar, você precisa definir duas variáveis de ambiente: *ORACLE_HOME* e *ORACLE_SID*.

```bash
ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME
ORACLE_SID=cdb1; export ORACLE_SID
```
Você também pode adicionar as variáveis ORACLE_HOME e ORACLE_SID ao arquivo .bashrc. Isso economiza as variáveis de ambiente para entradas futuras. Confirme se as instruções a seguir foram adicionadas ao arquivo `~/.bashrc` usando o editor de sua escolha.

```bash
# Add ORACLE_HOME. 
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1 
# Add ORACLE_SID. 
export ORACLE_SID=cdb1 
```

## <a name="oracle-em-express-connectivity"></a>Conectividade com o Oracle EM Express

Para ter uma ferramenta de gerenciamento de GUI que você pode usar para explorar o banco de dados, configure o Oracle EM Express. Para se conectar ao Oracle EM Express, a porta deve ser configurada primeiramente no Oracle. 

1. Conecte-se ao seu banco de dados usando sqlplus:

    ```bash
    sqlplus / as sysdba
    ```

2. Após a conexão, defina a porta 5502 como EM Express

    ```bash
    exec DBMS_XDB_CONFIG.SETHTTPSPORT(5502);
    ```

3. Abra o contêiner PDB1, se ainda não estiver aberto, mas verifique o status primeiro:

    ```bash
    select con_id, name, open_mode from v$pdbs;
    ```

    A saída deverá ser semelhante a esta:

    ```bash
      CON_ID NAME                           OPEN_MODE 
      ----------- ------------------------- ---------- 
      2           PDB$SEED                  READ ONLY 
      3           PDB1                      MOUNT
    ```

4. Se o OPEN_MODE de `PDB1` não for READ WRITE, execute os comandos a seguir para abrir PDB1:

   ```bash
    alter session set container=pdb1;
    alter database open;
   ```

Você precisa digitar `quit` para encerrar a sessão de sqlplus e o digitar `exit` fazer logoff do usuário do Oracle.

## <a name="automate-database-startup-and-shutdown"></a>Automatizar a inicialização e o desligamento do banco de dados

Por padrão, o banco de dados Oracle não inicia automaticamente quando você reinicia a VM. Para configurar o banco de dados Oracle para iniciar automaticamente, primeiro entre como raiz. Em seguida, crie e atualize alguns arquivos do sistema.

1. Conectar-se como raiz
    ```bash
    sudo su -
    ```

2.  Usando o editor de sua escolha, edite o arquivo `/etc/oratab` e altere o padrão `N` para `Y`:

    ```bash
    cdb1:/u01/app/oracle/product/12.1.0/dbhome_1:Y
    ```

3.  Crie um arquivo chamado `/etc/init.d/dbora` e cole o conteúdo a seguir:

    ```
    #!/bin/sh
    # chkconfig: 345 99 10
    # Description: Oracle auto start-stop script.
    #
    # Set ORA_HOME to be equivalent to $ORACLE_HOME.
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle

    case "$1" in
    'start')
        # Start the Oracle databases:
        # The following command assumes that the Oracle sign-in
        # will not prompt the user for any values.
        # Remove "&" if you don't want startup as a background process.
        su - $ORA_OWNER -c "$ORA_HOME/bin/dbstart $ORA_HOME" &
        touch /var/lock/subsys/dbora
        ;;

    'stop')
        # Stop the Oracle databases:
        # The following command assumes that the Oracle sign-in
        # will not prompt the user for any values.
        su - $ORA_OWNER -c "$ORA_HOME/bin/dbshut $ORA_HOME" &
        rm -f /var/lock/subsys/dbora
        ;;
    esac
    ```

4.  Altere as permissões nos arquivos com *chmod* da seguinte maneira:

    ```bash
    chgrp dba /etc/init.d/dbora
    chmod 750 /etc/init.d/dbora
    ```

5.  Crie links simbólicos para inicialização e desligamento, da seguinte maneira:

    ```bash
    ln -s /etc/init.d/dbora /etc/rc.d/rc0.d/K01dbora
    ln -s /etc/init.d/dbora /etc/rc.d/rc3.d/S99dbora
    ln -s /etc/init.d/dbora /etc/rc.d/rc5.d/S99dbora
    ```

6.  Para testar as alterações, reinicie a VM:

    ```bash
    reboot
    ```

## <a name="open-ports-for-connectivity"></a>Abrir as portas para conectividade

A última tarefa é configurar alguns pontos de extremidade externos. Para configurar o Grupo de Segurança de Rede do Azure que protege a VM, primeiro saia da sessão SSH na VM (você de ter saído do SSH ao reiniciar na etapa anterior). 

1.  Para abrir o ponto de extremidade que você usa para acessar o banco de dados Oracle remotamente, crie uma regra de Grupo de Segurança de Rede com [az network nsg rule create](/cli/azure/network/nsg/rule) da seguinte maneira: 

    ```azurecli-interactive
    az network nsg rule create \
        --resource-group myResourceGroup\
        --nsg-name myVmNSG \
        --name allow-oracle \
        --protocol tcp \
        --priority 1001 \
        --destination-port-range 1521
    ```

2.  Para abrir o ponto de extremidade que você usa para acessar o Oracle EM Express remotamente, crie uma regra de Grupo de Segurança de Rede com [az network nsg rule create](/cli/azure/network/nsg/rule) da seguinte maneira:

    ```azurecli-interactive
    az network nsg rule create \
        --resource-group myResourceGroup \
        --nsg-name myVmNSG \
        --name allow-oracle-EM \
        --protocol tcp \
        --priority 1002 \
        --destination-port-range 5502
    ```

3. Se for necessário, obtenha o endereço IP público de sua VM com [az network public-ip show](/cli/azure/network/public-ip) da seguinte maneira:

    ```azurecli-interactive
    az network public-ip show \
        --resource-group myResourceGroup \
        --name myVMPublicIP \
        --query [ipAddress] \
        --output tsv
    ```

4.  Conecte-se ao EM Express pelo navegador. Verifique se o seu navegador é compatível com EM Express (é necessário ter Flash instalado): 

    ```
    https://<VM ip address or hostname>:5502/em
    ```

Você pode fazer logon usando a conta **SYS** e marcar a caixa de seleção **como sysdba**. Use a senha **OraPasswd1** que você definiu durante a instalação. 

![Captura de tela da página de logon Oracle OEM Express](./media/oracle-quick-start/oracle_oem_express_login.png)

## <a name="clean-up-resources"></a>Limpar recursos

Depois de terminar de explorar seu primeiro banco de dados Oracle no Azure e a VM não for mais necessária, você poderá usar o comando [az group delete](/cli/azure/group) para remover o grupo de recursos, a VM e todos os recursos relacionados.

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre outras [Soluções Oracle no Azure](oracle-considerations.md). 

Experimente o tutorial [Instalar e configurar o Oracle Automated Storage Management](configure-oracle-asm.md).
