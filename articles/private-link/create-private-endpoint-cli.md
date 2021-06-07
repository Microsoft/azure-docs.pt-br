---
title: Início Rápido – Criar um Ponto de Extremidade Privado no Azure usando a CLI do Azure
description: Use este início rápido para aprender a criar um Ponto de Extremidade Privado usando a CLI do Azure.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 11/07/2020
ms.author: allensu
ms.openlocfilehash: 5088b4e50899a2643488103ba29a7e36a7f256ea
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778347"
---
# <a name="quickstart-create-a-private-endpoint-using-azure-cli"></a>Início Rápido: criar um Ponto de Extremidade Privado usando a CLI do Azure

Introdução ao Link Privado do Azure usando um Ponto de Extremidade Privado para se conectar com segurança a um aplicativo Web do Azure.

Neste guia de início rápido, você criará um ponto de extremidade privado para um aplicativo Web do Azure e implantará uma máquina virtual para testar a conexão privada.  

Pontos de extremidade privados podem ser criados para diferentes tipos de serviços do Azure, como o Azure SQL e o Armazenamento do Azure.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um Aplicativo Web do Azure com um **PremiumV2-tier** ou um plano de serviço de aplicativo mais alto implantado em sua assinatura do Azure.  
    * Para obter mais informações e um exemplo, confira [Início Rápido: Criar um aplicativo Web ASP.NET Core no Azure](../app-service/quickstart-dotnetcore.md). 
    * Para obter um tutorial detalhado sobre como criar um aplicativo Web e um ponto de extremidade, confira [Tutorial: Conectar-se a um aplicativo Web usando um Ponto de Extremidade Privado do Azure](tutorial-private-endpoint-webapp-portal.md).
* Entre no portal do Azure e verifique se a sua assinatura está ativa executando `az login`.
* Verifique sua versão da CLI do Azure em uma janela Comando ou de terminal executando `az --version`. Para obter a última versão, confira as [notas sobre a versão mais recente](/cli/azure/release-notes-azure-cli?tabs=azure-cli).
  * Caso não tenha a última versão, atualize a instalação seguindo o [guia de instalação para seu sistema operacional ou sua plataforma](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

Crie um grupo de recursos com [az group create](/cli/azure/group#az_group_create):

* chamado **CreatePrivateEndpointQS-rg**. 
* Na localização **eastus**.

```azurecli-interactive
az group create \
    --name CreatePrivateEndpointQS-rg \
    --location eastus
```

## <a name="create-a-virtual-network-and-bastion-host"></a>Criar uma rede virtual e um bastion host

Nesta seção, você criará uma rede virtual, uma sub-rede e um bastion host. 

O bastion host será usado para se conectar com segurança à máquina virtual para testar o ponto de extremidade privado.

Criar uma rede virtual com o comando [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create)

* Chamada **myVNet**.
* Prefixo de endereço igual a **10.0.0.0/16**.
* Sub-rede chamada **myBackendSubnet**.
* Prefixo de sub-rede igual a **10.0.0.0/24**.
* No grupo de recursos **CreatePrivateEndpointQS-rg**.
* Localização de **eastus**.

```azurecli-interactive
az network vnet create \
    --resource-group CreatePrivateEndpointQS-rg\
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
    --resource-group CreatePrivateEndpointQS-rg \
    --vnet-name myVNet \
    --disable-private-endpoint-network-policies true
```

Use [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) para criar um endereço IP público para o bastion host:

* Crie um endereço IP público com redundância de zona padrão chamado **myBastionIP**.
* Em **CreatePrivateEndpointQS-rg**.

```azurecli-interactive
az network public-ip create \
    --resource-group CreatePrivateEndpointQS-rg \
    --name myBastionIP \
    --sku Standard
```

Use o comando [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) para criar uma sub-rede do bastion:

* Chamada **AzureBastionSubnet**.
* Prefixo de endereço igual a **10.0.1.0/24**.
* Na rede virtual **myVNet**.
* No grupo de recursos **CreatePrivateEndpointQS-rg**.

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreatePrivateEndpointQS-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.0.1.0/24
```

Use [az network bastion create](/cli/azure/network/bastion#az_network_bastion_create) para criar um bastion host:

* chamado **myBastionHost**.
* Em **CreatePrivateEndpointQS-rg**.
* Associado ao IP público **myBastionIP**.
* Associado à rede virtual **myVNet**.
* Na localização **eastus**.

```azurecli-interactive
az network bastion create \
    --resource-group CreatePrivateEndpointQS-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

Levará alguns minutos para que o host do Azure Bastion seja implantado.

## <a name="create-test-virtual-machine"></a>Criar máquina virtual de teste

Nesta seção, você criará uma máquina virtual que será usada para testar o ponto de extremidade privado.

Crie uma VM com o comando  [az vm create](/cli/azure/vm#az_vm_create). Quando solicitado, forneça uma senha para ser usada como as credenciais da VM:

* chamada **myVM**.
* Em **CreatePrivateEndpointQS-rg**.
* Na rede **myVNet**.
* Na sub-rede **myBackendSubnet**.
* Imagem do servidor **Win2019Datacenter**.

```azurecli-interactive
az vm create \
    --resource-group CreatePrivateEndpointQS-rg \
    --name myVM \
    --image Win2019Datacenter \
    --public-ip-address "" \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --admin-username azureuser
```

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="create-private-endpoint"></a>Criar um ponto de extremidade privado

Nesta seção, você criará o ponto de extremidade privado.

Use o comando [az webapp list](/cli/azure/webapp#az_webapp_list) para colocar a ID do recurso do aplicativo Web criado anteriormente em uma variável do Shell.

Use o comando [az network private-endpoint create](/cli/azure/network/private-endpoint#az_network_private_endpoint_create) para criar o ponto de extremidade e a conexão:

* chamado **myPrivateEndpoint**.
* No grupo de recursos **CreatePrivateEndpointQS-rg**.
* Na rede virtual **myVNet**.
* Na sub-rede **myBackendSubnet**.
* Conexão chamada **myConnection**.
* Seu webapp **\<webapp-resource-group-name>** .

```azurecli-interactive
id=$(az webapp list \
    --resource-group <webapp-resource-group-name> \
    --query '[].[id]' \
    --output tsv)

az network private-endpoint create \
    --name myPrivateEndpoint \
    --resource-group CreatePrivateEndpointQS-rg \
    --vnet-name myVNet --subnet myBackendSubnet \
    --private-connection-resource-id $id \
    --group-id sites \
    --connection-name myConnection  
```

## <a name="configure-the-private-dns-zone"></a>Configurar a zona DNS privada

Nesta seção, você vai criar e configurar a zona DNS privada usando o comando [az network private-dns zone create](/cli/azure/network/private-dns/zone#ext_privatedns_az_network_private_dns_zone_create).  

Você usará o comando [az network private-dns link vnet create](/cli/azure/network/private-dns/link/vnet#ext_privatedns_az_network_private_dns_link_vnet_create) para criar o link da rede virtual para a zona DNS.

Você criará um grupo de zonas DNS com o comando [az network private-endpoint dns-zone-group create](/cli/azure/network/private-endpoint/dns-zone-group#az_network_private_endpoint_dns_zone_group_create).

* Zona chamada **privatelink.azurewebsites.net**
* Na rede virtual **myVNet**.
* No grupo de recursos **CreatePrivateEndpointQS-rg**.
* Link DNS chamado **myDNSLink**.
* Associado a **myPrivateEndpoint**.
* Grupo de zonas chamado **MyZoneGroup**.

```azurecli-interactive
az network private-dns zone create \
    --resource-group CreatePrivateEndpointQS-rg \
    --name "privatelink.azurewebsites.net"

az network private-dns link vnet create \
    --resource-group CreatePrivateEndpointQS-rg \
    --zone-name "privatelink.azurewebsites.net" \
    --name MyDNSLink \
    --virtual-network myVNet \
    --registration-enabled false

az network private-endpoint dns-zone-group create \
   --resource-group CreatePrivateEndpointQS-rg \
   --endpoint-name myPrivateEndpoint \
   --name MyZoneGroup \
   --private-dns-zone "privatelink.azurewebsites.net" \
   --zone-name webapp
```

## <a name="test-connectivity-to-private-endpoint"></a>Testar a conectividade com o ponto de extremidade privado

Nesta seção, você usará a máquina virtual criada na etapa anterior para se conectar ao SQL Server pelo ponto de extremidade privado.

1. Entre no [Portal do Azure](https://portal.azure.com) 
 
2. Selecione **Grupos de recursos** no painel de navegação à esquerda.

3. Selecione **CreatePrivateEndpointQS-rg**.

4. Selecione **myVM**.

5. Na página de visão geral de **myVM**, escolha **Conectar** e **Bastion**.

6. Selecione o botão azul **Usar Bastion**.

7. Insira o nome de usuário e a senha que você inseriu durante a criação da máquina virtual.

8. Abra o Windows PowerShell no servidor depois de se conectar.

9. Digite `nslookup <your-webapp-name>.azurewebsites.net`. Substitua **\<your-webapp-name>** pelo nome do aplicativo Web criado nas etapas anteriores.  Você receberá uma mensagem semelhante à mostrada abaixo:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mywebapp8675.privatelink.azurewebsites.net
    Address:  10.0.0.5
    Aliases:  mywebapp8675.azurewebsites.net
    ```

    O endereço IP privado **10.0.0.5** é retornado para o nome do aplicativo Web.  Esse endereço está na sub-rede da rede virtual criada anteriormente.

10. Na conexão do bastion com **myVM**, abra o Internet Explorer.

11. Insira a URL do aplicativo Web, **https://\<your-webapp-name>.azurewebsites.net**.

12. Você receberá a página padrão do aplicativo Web se o aplicativo não tiver sido implantado:

    :::image type="content" source="./media/create-private-endpoint-portal/web-app-default-page.png" alt-text="Página padrão do aplicativo Web." border="true":::

13. Feche a conexão com **myVM**.

## <a name="clean-up-resources"></a>Limpar os recursos 
Ao terminar de usar o ponto de extremidade privado e a VM, use o comando [az group delete](/cli/azure/group#az_group_delete) para remover o grupo de recursos e todos os recursos que ele tem:

```azurecli-interactive
az group delete \
    --name CreatePrivateEndpointQS-rg
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou:

* Uma rede virtual e um bastion host.
* Uma máquina virtual.
* Um ponto de extremidade privado para um Aplicativo Web do Azure.

Você usou a máquina virtual para testar a conectividade seguramente com o aplicativo Web no ponto de extremidade privado.

Para obter mais informações sobre os serviços que dão suporte a um ponto de extremidade privado, confira:
> [!div class="nextstepaction"]
> [Disponibilidade de Link Privado](private-link-overview.md#availability)
