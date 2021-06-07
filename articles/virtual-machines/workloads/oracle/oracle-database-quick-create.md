---
title: Criar um banco de dados Oracle em uma VM do Azure | Microsoft Docs
description: Coloque rapidamente em funcionamento um banco de dados Oracle Database 12c no ambiente do Azure.
author: dbakevlar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: quickstart
ms.date: 10/05/2020
ms.author: kegorman
ms.openlocfilehash: 8964248bb23b2b615c7e73e26d730fbd79b4e9e7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102184450"
---
# <a name="create-an-oracle-database-in-an-azure-vm"></a>Criar um Banco de Dados Oracle em uma VM do Azure

Esse guia detalha como usar a CLI do Azure para implantar uma máquina virtual do Azure usando a [imagem da galeria do marketplace da Oracle](https://azuremarketplace.microsoft.com/marketplace/apps/Oracle.OracleDatabase12102EnterpriseEdition?tab=Overview) a fim de criar um banco de dados Oracle 19c. Depois que o servidor for implantado, conecte-se por meio de SSH para configurar o banco de dados Oracle. 

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Se você optar por instalar e usar a CLI localmente, este guia de início rápido exigirá a execução da CLI do Azure versão 2.0.4 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. 

O exemplo a seguir cria um grupo de recursos denominado *rg-oracle* na localização *eastus*.

```azurecli-interactive
az group create --name rg-oracle --location eastus
```

## <a name="create-virtual-machine"></a>Criar máquina virtual

Para criar uma VM (máquina virtual), use o comando [az vm create](/cli/azure/vm). 

O exemplo a seguir cria uma VM chamada `vmoracle19c`. Ele também criará chaves SSH, se elas ainda não existirem em um local de chave padrão. Para usar um conjunto específico de chaves, use a opção `--ssh-key-value`.  

```azurecli-interactive 
az vm create ^
    --resource-group rg-oracle ^
    --name vmoracle19c ^
    --image Oracle:oracle-database-19-3:oracle-database-19-0904:latest ^
    --size Standard_DS2_v2 ^
    --admin-username azureuser ^
    --generate-ssh-keys ^
    --public-ip-address-allocation static ^
    --public-ip-address-dns-name vmoracle19c

```

Depois de criar a VM, a CLI do Azure exibe informações semelhantes ao exemplo a seguir. Observe o valor de `publicIpAddress`. Você pode usar esse endereço para acessar a VM.

```output
{
  "fqdns": "",
  "id": "/subscriptions/{snip}/resourceGroups/rg-oracle/providers/Microsoft.Compute/virtualMachines/vmoracle19c",
  "location": "eastus",
  "macAddress": "00-0D-3A-36-2F-56",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.64.104.241",
  "resourceGroup": "rg-oracle"
}
```
## <a name="create-and-attach-a-new-disk-for-oracle-datafiles-and-fra"></a>Criar um disco e anexá-lo a arquivos de dados da Oracle e ao FRA

```bash
az vm disk attach --name oradata01 --new --resource-group rg-oracle --size-gb 128 --sku StandardSSD_LRS --vm-name vmoracle19c
```

## <a name="open-ports-for-connectivity"></a>Abrir as portas para conectividade
Nesta tarefa, você precisa configurar alguns pontos de extremidade externos para o ouvinte de banco de dados e o EM Express a serem usados configurando o Grupo de Segurança de Rede do Azure que protege a VM. 

1. Para abrir o ponto de extremidade que você usa para acessar o banco de dados Oracle remotamente, crie uma regra de Grupo de Segurança de Rede da seguinte maneira:
   ```bash
   az network nsg rule create ^
       --resource-group rg-oracle ^
       --nsg-name vmoracle19cNSG ^
       --name allow-oracle ^
       --protocol tcp ^
       --priority 1001 ^
       --destination-port-range 1521
   ```
2. Para abrir o ponto de extremidade que você usa para acessar o Oracle EM Express remotamente, crie uma regra de Grupo de Segurança de Rede com az network nsg rule create da seguinte maneira:
   ```bash
   az network nsg rule create ^
       --resource-group rg-oracle ^
       --nsg-name vmoracle19cNSG ^
       --name allow-oracle-EM ^
       --protocol tcp ^
       --priority 1002 ^
       --destination-port-range 5502
   ```
3. Se for necessário, obtenha o endereço IP público de sua VM com az network public-ip show da seguinte maneira:

   ```bash
   az network public-ip show ^
       --resource-group rg-oracle ^
       --name vmoracle19cPublicIP ^
       --query [ipAddress] ^
       --output tsv
   ```

## <a name="prepare-the-vm-environment"></a>Preparar o ambiente da VM

1. Conectar-se à VM

   Para criar uma sessão SSH com a VM, use o comando a seguir. Substitua o endereço IP pelo valor `publicIpAddress` para a sua VM.

   ```bash
   ssh azureuser@<publicIpAddress>
   ```

2. Mudar para o usuário raiz

   ```bash
   sudo su -
   ```

3. Verificar o último dispositivo de disco criado que formataremos para usar a retenção de arquivos de dados da Oracle

   ```bash
   ls -alt /dev/sd*|head -1
   ```

   A saída será semelhante a:
   ```output
   brw-rw----. 1 root disk 8, 16 Dec  8 22:57 /dev/sdc
   ```

4. Formatar o dispositivo. 
   Como o usuário raiz, executar o parted no dispositivo 
   
   Primeiro, crie um rótulo de disco:
   ```bash
   parted /dev/sdc mklabel gpt
   ```
   Em seguida, crie uma partição primária abrangendo todo o disco:
   ```bash
   parted -a optimal /dev/sdc mkpart primary 0GB 64GB   
   ```
   Por fim, verifique os detalhes do dispositivo imprimindo os metadados dele:
   ```bash
   parted /dev/sdc print
   ```
   A saída deverá ser semelhante ao seguinte:
   ```bash
   # parted /dev/sdc print
   Model: Msft Virtual Disk (scsi)
   Disk /dev/sdc: 68.7GB
   Sector size (logical/physical): 512B/4096B
   Partition Table: gpt
   Disk Flags:
   Number  Start   End     Size    File system  Name     Flags
    1      1049kB  64.0GB  64.0GB  ext4         primary
   ```

5. Criar um sistema de arquivos na partição do dispositivo

   ```bash
   mkfs -t ext4 /dev/sdc1
   ```

6. Criar um ponto de montagem
   ```bash
   mkdir /u02
   ```

7. Monte o disco

   ```bash
   mount /dev/sdc1 /u02
   ```

8. Alterar permissões no ponto de montagem

   ```bash
   chmod 777 /u02
   ```

9. Adicionar a montagem ao arquivo /etc/fstab. 

   ```bash
   echo "/dev/sdc1               /u02                    ext4    defaults        0 0" >> /etc/fstab
   ```
   
10. Atualize o arquivo ***/etc/hosts*** com o IP e o nome do host.

    Altere o ***IP público e VMname*** para refletir seus valores reais:
  
    ```bash
    echo "<Public IP> <VMname>.eastus.cloudapp.azure.com <VMname>" >> /etc/hosts
    ```
11. Atualizar o arquivo do nome do host
    
    Use o comando a seguir para adicionar o nome de domínio da VM ao arquivo **/etc/hostname**. Isso pressupõe que você criou o grupo de recursos e a VM na região **eastus**:
    
    ```bash
    sed -i 's/$/\.eastus\.cloudapp\.azure\.com &/' /etc/hostname
    ```

12. Abrir portas de firewall
    
    Como o SELinux é habilitado por padrão na imagem do Marketplace, precisamos abrir o firewall para tráfego para a porta de escuta 1521 do banco de dados e a porta 5502 do Enterprise Manager Express. Execute os seguintes comandos como usuário raiz:

    ```bash
    firewall-cmd --zone=public --add-port=1521/tcp --permanent
    firewall-cmd --zone=public --add-port=5502/tcp --permanent
    firewall-cmd --reload
    ```
   

## <a name="create-the-database"></a>Criar o banco de dados

O software Oracle já está instalado na imagem do Marketplace. Crie um banco de dados de exemplo conforme descrito a seguir. 

1.  Mude para o usuário **oracle**:

    ```bash
    sudo su - oracle
    ```
2. Iniciar o ouvinte de banco de dados

   ```bash
   lsnrctl start
   ```
   A saída deverá ser semelhante a esta:
  
   ```output
   LSNRCTL for Linux: Version 19.0.0.0.0 - Production on 20-OCT-2020 01:58:18

   Copyright (c) 1991, 2019, Oracle.  All rights reserved.

   Starting /u01/app/oracle/product/19.0.0/dbhome_1/bin/tnslsnr: please wait...

   TNSLSNR for Linux: Version 19.0.0.0.0 - Production
   Log messages written to /u01/app/oracle/diag/tnslsnr/vmoracle19c/listener/alert/log.xml
   Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=vmoracle19c.eastus.cloudapp.azure.com)(PORT=1521)))

   Connecting to (ADDRESS=(PROTOCOL=tcp)(HOST=)(PORT=1521))
   STATUS of the LISTENER
   ------------------------
   Alias                     LISTENER
   Version                   TNSLSNR for Linux: Version 19.0.0.0.0 - Production
   Start Date                20-OCT-2020 01:58:18
   Uptime                    0 days 0 hr. 0 min. 0 sec
   Trace Level               off
   Security                  ON: Local OS Authentication
   SNMP                      OFF
   Listener Log File         /u01/app/oracle/diag/tnslsnr/vmoracle19c/listener/alert/log.xml
   Listening Endpoints Summary...
     (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=vmoracle19c.eastus.cloudapp.azure.com)(PORT=1521)))
   The listener supports no services
   The command completed successfully
   ```
3. Criar um diretório de dados para os arquivos de dados do Oracle:

   ```bash
   mkdir /u02/oradata
   ```
    

3.  Execute o Assistente de Criação de Banco de Dados:

    ```bash
    dbca -silent \
       -createDatabase \
       -templateName General_Purpose.dbc \
       -gdbname test \
       -sid test \
       -responseFile NO_VALUE \
       -characterSet AL32UTF8 \
       -sysPassword OraPasswd1 \
       -systemPassword OraPasswd1 \
       -createAsContainerDatabase false \
       -databaseType MULTIPURPOSE \
       -automaticMemoryManagement false \
       -storageType FS \
       -datafileDestination "/u02/oradata/" \
       -ignorePreReqs
    ```

    A criação do banco de dados demora alguns minutos.

    Você verá uma saída com aparência semelhante à seguinte:

    ```output
        Prepare for db operation
       10% complete
       Copying database files
       40% complete
       Creating and starting Oracle instance
       42% complete
       46% complete
       50% complete
       54% complete
       60% complete
       Completing Database Creation
       66% complete
       69% complete
       70% complete
       Executing Post Configuration Actions
       100% complete
       Database creation complete. For details check the logfiles at: /u01/app/oracle/cfgtoollogs/dbca/test.
       Database Information:
       Global Database Name:test
       System Identifier(SID):test
       Look at the log file "/u01/app/oracle/cfgtoollogs/dbca/test/test.log" for further details.
    ```

4. Definir variáveis do Oracle

    Antes de se conectar, você precisa definir a variável de ambiente *ORACLE_SID*:

    ```bash
        export ORACLE_SID=test
    ```

    Você também deve adicionar a variável ORACLE_SID ao arquivo `.bashrc` dos usuários `oracle` para entradas futuras usando o seguinte comando:

    ```bash
    echo "export ORACLE_SID=test" >> ~oracle/.bashrc
    ```

## <a name="oracle-em-express-connectivity"></a>Conectividade com o Oracle EM Express

Para ter uma ferramenta de gerenciamento de GUI que você pode usar para explorar o banco de dados, configure o Oracle EM Express. Para se conectar ao Oracle EM Express, a porta deve ser configurada primeiramente no Oracle. 

1. Conecte-se ao seu banco de dados usando sqlplus:

    ```bash
    sqlplus sys as sysdba
    ```

2. Após a conexão, defina a porta 5502 como EM Express

    ```bash
    exec DBMS_XDB_CONFIG.SETHTTPSPORT(5502);
    ```

3.  Conecte-se ao EM Express pelo navegador. Verifique se o seu navegador é compatível com EM Express (é necessário ter Flash instalado): 

    ```https
    https://<VM ip address or hostname>:5502/em
    ```

    Você pode fazer logon usando a conta **SYS** e marcar a caixa de seleção **como sysdba**. Use a senha **OraPasswd1** que você definiu durante a instalação. 

    ![Captura de tela da página de logon Oracle OEM Express](./media/oracle-quick-start/oracle_oem_express_login.png)

## <a name="automate-database-startup-and-shutdown"></a>Automatizar a inicialização e o desligamento do banco de dados

Por padrão, o banco de dados Oracle não inicia automaticamente quando você reinicia a VM. Para configurar o banco de dados Oracle para iniciar automaticamente, primeiro entre como raiz. Em seguida, crie e atualize alguns arquivos do sistema.

1. Conectar-se como raiz

    ```bash
    sudo su -
    ```

2.  Execute o seguinte comando para alterar o sinalizador de inicialização automática de `N` para `Y` no arquivo `/etc/oratab`:

    ```bash
    sed -i 's/:N/:Y/' /etc/oratab
    ```

3.  Crie um arquivo chamado `/etc/init.d/dbora` e cole o seguinte conteúdo:

    ```bash
    #!/bin/sh
    # chkconfig: 345 99 10
    # Description: Oracle auto start-stop script.
    #
    # Set ORA_HOME to be equivalent to $ORACLE_HOME.
    ORA_HOME=/u01/app/oracle/product/19.0.0/dbhome_1
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

## <a name="clean-up-resources"></a>Limpar os recursos

Depois de terminar de explorar seu primeiro banco de dados Oracle no Azure e a VM não for mais necessária, você poderá usar o comando [az group delete](/cli/azure/group) para remover o grupo de recursos, a VM e todos os recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Saiba como proteger seu banco de dados no Azure com as [Estratégias de Backup da Oracle](./oracle-database-backup-strategies.md)

Saiba mais sobre outras [Soluções Oracle no Azure](./oracle-overview.md). 

Experimente o tutorial [Instalar e configurar o Oracle Automated Storage Management](configure-oracle-asm.md).
