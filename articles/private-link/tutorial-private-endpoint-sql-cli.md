---
title: 'Tutorial: como se conectar a um SQL Server do Azure usando um Ponto de Extremidade Privado do Azure – CLI do Azure'
description: Use este tutorial para aprender como criar um SQL Server do Azure com um ponto de extremidade privado usando a CLI do Azure
services: private-link
author: asudbring
ms.service: private-link
ms.topic: tutorial
ms.date: 11/03/2020
ms.author: allensu
ms.custom: fasttrack-edit, devx-track-azurecli
ms.openlocfilehash: a8fafeaaf974893c9a1a71115912f2a7b019ddd9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771813"
---
# <a name="tutorial-connect-to-an-azure-sql-server-using-an-azure-private-endpoint---azure-cli"></a>Tutorial: como se conectar a um SQL Server do Azure usando um Ponto de Extremidade Privado do Azure – CLI do Azure

Um ponto de extremidade privado do Azure é o bloco de construção básico para o Link Privado no Azure. Ele permite que os recursos do Azure, como VMs (máquinas virtuais), se comuniquem em modo privado com os recursos do Link Privado.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma rede virtual e um bastion host.
> * Crie uma máquina virtual.
> * Criar um SQL Server do Azure e um ponto de extremidade privado.
> * Testar a conectividade com o ponto de extremidade privado do SQL Server.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Entre no portal do Azure e verifique se a sua assinatura está ativa executando `az login`.
* Verifique sua versão da CLI do Azure em uma janela Comando ou de terminal executando `az --version`. Para obter a última versão, confira as [notas sobre a versão mais recente](/cli/azure/release-notes-azure-cli?tabs=azure-cli).
  * Caso não tenha a última versão, atualize a instalação seguindo o [guia de instalação para seu sistema operacional ou sua plataforma](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

Crie um grupo de recursos com [az group create](/cli/azure/group#az_group_create):

* Chamado **CreateSQLEndpointTutorial-rg**. 
* Na localização **eastus**.

```azurecli-interactive
az group create \
    --name CreateSQLEndpointTutorial-rg \
    --location eastus
```

## <a name="create-a-virtual-network-and-bastion-host"></a>Criar uma rede virtual e um bastion host

Nesta seção, você criará uma rede virtual, uma sub-rede e um bastion host. 

O bastion host será usado para se conectar com segurança à máquina virtual para testar o ponto de extremidade privado.

Criar uma rede virtual com o comando [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create)

* Chamada **myVNet**.
* Prefixo de endereço igual a **10.0.0.0/16**.
* Sub-rede chamada **myBackendSubnet**.
* Prefixo de sub-rede **10.0.0.0/24**.
* No grupo de recursos **CreateSQLEndpointTutorial-rg**.
* Localização de **eastus**.

```azurecli-interactive
az network vnet create \
    --resource-group CreateSQLEndpointTutorial-rg\
    --location eastus \
    --name myVNet \
    --address-prefixes 10.0.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.0.0.0/24
```

Atualize a sub-rede para desabilitar as políticas de rede do ponto de extremidade privado para o ponto de extremidade privado com o comando [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update):

```azurecli-interactive
az network vnet subnet update \
    --name myBackendSubnet \
    --resource-group CreateSQLEndpointTutorial-rg \
    --vnet-name myVNet \
    --disable-private-endpoint-network-policies true
```

Use [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) para criar um endereço IP público para o bastion host:

* Crie um endereço IP público com redundância de zona padrão chamado **myBastionIP**.
* Em **CreateSQLEndpointTutorial-rg**.

```azurecli-interactive
az network public-ip create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --name myBastionIP \
    --sku Standard
```

Use [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) para criar uma sub-rede do bastion:

* Chamada **AzureBastionSubnet**.
* Prefixo de endereço igual a **10.0.1.0/24**.
* Na rede virtual **myVNet**.
* No grupo de recursos **CreateSQLEndpointTutorial-rg**.

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.0.1.0/24
```

Use [az network bastion create](/cli/azure/network/bastion#az_network_bastion_create) para criar um bastion host:

* chamado **myBastionHost**.
* Em **CreateSQLEndpointTutorial-rg**.
* Associado ao IP público **myBastionIP**.
* Associado à rede virtual **myVNet**.
* Na localização **eastus**.

```azurecli-interactive
az network bastion create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

Levará alguns minutos para que o host do Azure Bastion seja implantado.

## <a name="create-test-virtual-machine"></a>Criar máquina virtual de teste

Nesta seção, você criará uma máquina virtual que será usada para testar o ponto de extremidade privado.

Crie uma VM com o comando  [az vm create](/cli/azure/vm#az_vm_create). Quando solicitado, forneça uma senha para ser usada como as credenciais da VM:

* Chamada **myVM**.
* Em **CreateSQLEndpointTutorial-rg**.
* Na rede **myVNet**.
* Na sub-rede **myBackendSubnet**.
* Imagem do servidor **Win2019Datacenter**.

```azurecli-interactive
az vm create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --name myVM \
    --image Win2019Datacenter \
    --public-ip-address "" \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --admin-username azureuser
```

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="create-an-azure-sql-server"></a>Criar um SQL Server do Azure

Nesta seção, você criará um SQL Server e um banco de dados.

Use [az sql server create](/cli/azure/sql/server#az_sql_server_create) para criar um SQL Server:

* Substitua **\<sql-server-name>** pelo seu próprio nome do servidor.
* Substitua **\<your-password>** por sua senha.
* Em **CreateSQLEndpointTutorial-rg**.
* Na região **eastus**.

```azurecli-interactive
az sql server create \
    --name <sql-server-name> \
    --resource-group CreateSQLEndpointTutorial-rg \
    --location eastus \
    --admin-user sqladmin \
    --admin-password <your-password>
```

Use [az sql db create](/cli/azure/sql/db#az_sql_db_create) para criar um banco de dados:

* Chamado **myDataBase**.
* Em **CreateSQLEndpointTutorial-rg**.
* Substitua **\<sql-server-name>** pelo seu próprio nome do servidor.

```azurecli-interactive
az sql db create \
    --resource-group CreateSQLEndpointTutorial-rg  \
    --server <sql-server-name> \
    --name myDataBase \
    --sample-name AdventureWorksLT
```

## <a name="create-private-endpoint"></a>Criar um ponto de extremidade privado

Nesta seção, você criará o ponto de extremidade privado.

Use [az sql server list](/cli/azure/sql/server#az_sql_server_list) para colocar a ID do recurso do SQL Server em uma variável de shell.

Use [az network private-endpoint create](/cli/azure/network/private-endpoint#az_network_private_endpoint_create) para criar o ponto de extremidade e a conexão:

* Chamado **myPrivateEndpoint**.
* No grupo de recursos **CreateSQLEndpointTutorial-rg**.
* Na rede virtual **myVNet**.
* Na sub-rede **myBackendSubnet**.
* Conexão chamada **myConnection**.

```azurecli-interactive
id=$(az sql server list \
    --resource-group CreateSQLEndpointTutorial-rg \
    --query '[].[id]' \
    --output tsv)

az network private-endpoint create \
    --name myPrivateEndpoint \
    --resource-group CreateSQLEndpointTutorial-rg \
    --vnet-name myVNet --subnet myBackendSubnet \
    --private-connection-resource-id $id \
    --group-ids sqlServer \
    --connection-name myConnection  
```

## <a name="configure-the-private-dns-zone"></a>Configurar a zona DNS privada

Nesta seção, você vai criar e configurar a zona DNS privada usando o comando [az network private-dns zone create](/cli/azure/network/private-dns/zone#ext_privatedns_az_network_private_dns_zone_create).  

Você usará o comando [az network private-dns link vnet create](/cli/azure/network/private-dns/link/vnet#ext_privatedns_az_network_private_dns_link_vnet_create) para criar o link da rede virtual para a zona DNS.

Você criará um grupo de zona DNS com [az network private-endpoint dns-zone-group create](/cli/azure/network/private-endpoint/dns-zone-group#az_network_private_endpoint_dns_zone_group_create).

* Zona chamada **privatelink.database.windows.net**
* Na rede virtual **myVNet**.
* No grupo de recursos **CreateSQLEndpointTutorial-rg**.
* Link DNS chamado **myDNSLink**.
* Associado a **myPrivateEndpoint**.
* Grupo de zonas chamado **MyZoneGroup**.

```azurecli-interactive
az network private-dns zone create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --name "privatelink.database.windows.net"

az network private-dns link vnet create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --zone-name "privatelink.database.windows.net" \
    --name MyDNSLink \
    --virtual-network myVNet \
    --registration-enabled false

az network private-endpoint dns-zone-group create \
   --resource-group CreateSQLEndpointTutorial-rg \
   --endpoint-name myPrivateEndpoint \
   --name MyZoneGroup \
   --private-dns-zone "privatelink.database.windows.net" \
   --zone-name sql
```

## <a name="test-connectivity-to-private-endpoint"></a>Testar a conectividade com o ponto de extremidade privado

Nesta seção, você usará a máquina virtual criada na etapa anterior para se conectar ao SQL Server pelo ponto de extremidade privado.

1. Entre no [Portal do Azure](https://portal.azure.com) 
 
2. Selecione **Grupos de recursos** no painel de navegação à esquerda.

3. Selecione **CreateSQLEndpointTutorial-rg**.

4. Selecione **myVM**.

5. Na página de visão geral de **myVM**, escolha **Conectar** e **Bastion**.

6. Selecione o botão azul **Usar Bastion**.

7. Insira o nome de usuário e a senha que você inseriu durante a criação da máquina virtual.

8. Abra o Windows PowerShell no servidor depois de se conectar.

9. Digite `nslookup <sqlserver-name>.database.windows.net`. Substitua **\<sqlserver-name>** pelo nome do SQL Server criado nas etapas anteriores.  Você receberá uma mensagem semelhante à mostrada abaixo:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mysqlserver8675.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:  mysqlserver8675.database.windows.net
    ```

    O endereço IP privado **10.0.0.5** é retornado para o nome do SQL Server.  Esse endereço está na sub-rede da rede virtual criada anteriormente.


10. Instale o [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?preserve-view=true&view=sql-server-2017) em **myVM**.

11. Abra o **SQL Server Management Studio**.

12. Em **Conectar-se ao servidor**, insira ou selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | Tipo de servidor | Selecione **Mecanismo de Banco de Dados**.|
    | Nome do servidor | Insira **\<sql-server-name>.database.windows.net** |
    | Autenticação | Selecione **Autenticação do SQL Server**. |
    | Nome de usuário | Insira o nome de usuário que você inseriu durante a criação do servidor |
    | Senha | Insira a senha que você inseriu durante a criação do servidor |
    | Lembrar senha | Selecione **Sim** na barra superior. |

13. Selecione **Conectar**.

14. Procure bancos de dados no menu à esquerda.

15. (Opcional) Crie ou consulte informações em **mysqldatabase**.

16. Feche a conexão do bastion a **myVM**. 

## <a name="clean-up-resources"></a>Limpar os recursos 

Quando terminar de usar o ponto de extremidade privado, o SQL Server e a VM, exclua o grupo de recursos e todos os recursos que ele contém: 

```azurecli-interactive
az group delete \
    --name CreateSQLEndpointTutorial-rg
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou:

* Uma rede virtual e um bastion host.
* Uma máquina virtual.
* SQL Server do Azure com um ponto de extremidade privado.

Você usou a máquina virtual para testar a conectividade seguramente com o SQL Server no ponto de extremidade privado.

Como próxima etapa, talvez você também esteja interessado no cenário de arquitetura de um **aplicativo Web com conectividade privada com o banco de dados SQL do Azure**, que conecta um aplicativo Web fora da rede virtual ao ponto de extremidade privado de um banco de dados.
> [!div class="nextstepaction"]
> [Aplicativo Web com conectividade privada com o banco de dados SQL do Azure](/azure/architecture/example-scenario/private-web-app/private-web-app)