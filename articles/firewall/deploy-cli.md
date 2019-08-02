---
title: Implantar e configurar o Firewall do Azure usando a CLI do Azure
description: Neste artigo, você aprenderá como implantar e configurar o Firewall do Azure usando a CLI do Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.date: 7/10/2019
ms.author: victorh
ms.topic: article
ms.openlocfilehash: 24954eecde58c978fa3e14bb3a2d411d708687a3
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707155"
---
# <a name="deploy-and-configure-azure-firewall-using-azure-cli"></a>Implantar e configurar o Firewall do Azure usando a CLI do Azure

O controle do acesso à saída de rede é uma parte importante de um plano geral de segurança de rede. Por exemplo, você talvez queira limitar o acesso a sites. Ou você talvez queira limitar os endereços IP e portas de saída que podem ser acessados.

Uma maneira de controlar o acesso à saída de rede em uma sub-rede do Azure é com o Firewall do Azure. Com o Firewall do Azure, você pode configurar:

* Regras de aplicativo que definem FQDNs (nomes de domínio totalmente qualificados) que podem ser acessados em uma sub-rede. O FQDN também pode [incluem instâncias do SQL](sql-fqdn-filtering.md).
* Regras de rede que definem endereço de origem, protocolo, porta de destino e endereço de destino.

O tráfego de rede está sujeito às regras de firewall configuradas quando o tráfego de rede para o firewall foi roteado como a sub-rede de gateway padrão.

Neste artigo, você pode criar uma única rede virtual simplificada com três sub-redes para facilitar a implantação. Para implantações de produção, uma [modelo hub e spoke](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) é recomendado. O firewall está em sua própria VNet. Os servidores de carga de trabalho estão em VNets emparelhadas na mesma região que uma ou mais sub-redes.

* **AzureFirewallSubnet**: o firewall está nesta sub-rede.
* **Workload-SN**: o servidor de carga de trabalho está nessa sub-rede. O tráfego de rede dessa sub-rede passa pelo firewall.
* **Jump-SN** -o servidor “jump” está nessa sub-rede. O servidor jump tem um endereço IP público a que você pode se conectar usando a Área de Trabalho Remota. A partir daí, você pode se conectar (usando outra Área de Trabalho Remota) ao servidor de carga de trabalho.

![Infraestrutura de rede do tutorial](media/tutorial-firewall-rules-portal/Tutorial_network.png)

Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Configurar um ambiente de rede de teste
> * Implantar um firewall
> * Criar uma rota padrão
> * Configure uma regra de aplicativo para permitir acesso a www.google.com
> * Configurar uma regra de rede para permitir o acesso a servidores DNS externos
> * Testar o firewall

Se você preferir, você pode concluir este procedimento usando o [portal do Azure](tutorial-firewall-deploy-portal.md) ou [do Azure PowerShell](deploy-ps.md).

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Pré-requisitos

### <a name="azure-cli"></a>CLI do Azure

Se você optar por instalar e usar a CLI localmente, execute a CLI do Azure versão 2.0.4 ou posterior. Para localizar a versão, execute **az --version**. Para saber mais sobre como instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

Instale a extensão do Firewall do Azure:

```azurecli-interactive
az extension add -n azure-firewall
```


## <a name="set-up-the-network"></a>Configurar a rede

Primeiro, crie um grupo de recursos para conter os recursos necessários à implantação do firewall. Em seguida, crie uma VNET, sub-redes e servidores de teste.

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

O grupo de recursos contém todos os recursos para a implantação.

```azurecli-interactive
az group create --name Test-FW-RG --location eastus
```

### <a name="create-a-vnet"></a>Criar uma VNET

Essa rede virtual tem três sub-redes.

> [!NOTE]
> O tamanho mínimo da sub-rede AzureFirewallSubnet é /26.

```azurecli-interactive
az network vnet create \
  --name Test-FW-VN \
  --resource-group Test-FW-RG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name AzureFirewallSubnet \
  --subnet-prefix 10.0.1.0/24
az network vnet subnet create \
  --name Workload-SN \
  --resource-group Test-FW-RG \
  --vnet-name Test-FW-VN   \
  --address-prefix 10.0.2.0/24
az network vnet subnet create \
  --name Jump-SN \
  --resource-group Test-FW-RG \
  --vnet-name Test-FW-VN   \
  --address-prefix 10.0.3.0/24
```

### <a name="create-virtual-machines"></a>Criar máquinas virtuais

Agora crie as máquinas virtuais de jump e carga de trabalho e coloque-as nas sub-redes apropriadas.
Quando solicitado, digite uma senha para a máquina virtual.

Crie a máquina virtual de salto de Srv.

```azurecli-interactive
az vm create \
    --resource-group Test-FW-RG \
    --name Srv-Jump \
    --location eastus \
    --image win2016datacenter \
    --vnet-name Test-FW-VN \
    --subnet Jump-SN \
    --admin-username azureadmin
az vm open-port --port 3389 --resource-group Test-FW-RG --name Srv-Jump
```



Crie uma NIC para o trabalho de Srv com endereços IP do servidor DNS específicos e nenhum endereço IP público com o qual testar.

```azurecli-interactive
az network nic create \
    -g Test-FW-RG \
    -n Srv-Work-NIC \
   --vnet-name Test-FW-VN \
   --subnet Workload-SN \
   --public-ip-address "" \
   --dns-servers 209.244.0.3 209.244.0.4
```

Agora, crie a máquina virtual de carga de trabalho.
Quando solicitado, digite uma senha para a máquina virtual.

```azurecli-interactive
az vm create \
    --resource-group Test-FW-RG \
    --name Srv-Work \
    --location eastus \
    --image win2016datacenter \
    --nics Srv-Work-NIC \
    --admin-username azureadmin
```

## <a name="deploy-the-firewall"></a>Implantar o firewall

Agora, implante o firewall na rede virtual.

```azurecli-interactive
az network firewall create \
    --name Test-FW01 \
    --resource-group Test-FW-RG \
    --location eastus
az network public-ip create \
    --name fw-pip \
    --resource-group Test-FW-RG \
    --location eastus \
    --allocation-method static \
    --sku standard
az network firewall ip-config create \
    --firewall-name Test-FW01 \
    --name FW-config \
    --public-ip-address fw-pip \
    --resource-group Test-FW-RG \
    --vnet-name Test-FW-VN
az network firewall update \
    --name Test-FW01 \
    --resource-group Test-FW-RG 
az network public-ip show \
    --name fw-pip \
    --resource-group Test-FW-RG
fwprivaddr="$(az network firewall ip-config list -g Test-FW-RG -f Test-FW01 --query "[?name=='FW-config'].privateIpAddress" --output tsv)"
```

Anote o endereço IP privado. Você o usará mais tarde quando criar a rota padrão.

## <a name="create-a-default-route"></a>Criar uma rota padrão

Criar uma tabela, com a propagação de rotas BGP desabilitada

```azurecli-interactive
az network route-table create \
    --name Firewall-rt-table \
    --resource-group Test-FW-RG \
    --location eastus \
    --disable-bgp-route-propagation true
```

Crie a rota.

```azurecli-interactive
az network route-table route create \
  --resource-group Test-FW-RG \
  --name DG-Route \
  --route-table-name Firewall-rt-table \
  --address-prefix 0.0.0.0/0 \
  --next-hop-type VirtualAppliance \
  --next-hop-ip-address $fwprivaddr
```

Associar a tabela de rotas à sub-rede

```azurecli-interactive
az network vnet subnet update \
    -n Workload-SN \
    -g Test-FW-RG \
    --vnet-name Test-FW-VN \
    --address-prefixes 10.0.2.0/24 \
    --route-table Firewall-rt-table
```

## <a name="configure-an-application-rule"></a>Configurar uma regra de aplicativo

A regra de aplicativo permite o acesso de saída para www.google.com.

```azurecli-interactive
az network firewall application-rule create \
   --collection-name App-Coll01 \
   --firewall-name Test-FW01 \
   --name Allow-Google \
   --protocols Http=80 Https=443 \
   --resource-group Test-FW-RG \
   --target-fqdns www.google.com \
   --source-addresses 10.0.2.0/24 \
   --priority 200 \
   --action Allow
```

O Firewall do Azure inclui uma coleção de regras internas para FQDNs de infraestrutura que têm permissão por padrão. Esses FQDNs são específicos da plataforma e não podem ser usados para outras finalidades. Para saber mais, veja [FQDNs de infraestrutura](infrastructure-fqdns.md).

## <a name="configure-a-network-rule"></a>Configurar uma regra de rede

A regra de rede permite acesso de saída para dois endereços IP na porta 53 (DNS).

```azurecli-interactive
az network firewall network-rule create \
   --collection-name Net-Coll01 \
   --destination-addresses 209.244.0.3 209.244.0.4 \
   --destination-ports 53 \
   --firewall-name Test-FW01 \
   --name Allow-DNS \
   --protocols UDP \
   --resource-group Test-FW-RG \
   --priority 200 \
   --source-addresses 10.0.2.0/24 \
   --action Allow
```

## <a name="test-the-firewall"></a>Testar o firewall

Agora teste o firewall para confirmar se ele funciona conforme o esperado.

1. Observe o endereço IP privado para o **Srv trabalho** máquina virtual:

   ```azurecli-interactive
   az vm list-ip-addresses \
   -g Test-FW-RG \
   -n Srv-Work
   ```

1. Conecte uma área de trabalho remota à máquina virtual **Srv-Jump** e entre. A partir daí, abra uma conexão de área de trabalho remota para o **Srv trabalho** privada endereço IP e entrada.

3. Na **SRV trabalho**, abra uma janela do PowerShell e execute os seguintes comandos:

   ```
   nslookup www.google.com
   nslookup www.microsoft.com
   ```

   Os dois comandos devem retornar respostas, mostrando que suas consultas DNS são atravessar o firewall.

1. Execute os seguintes comandos:

   ```
   Invoke-WebRequest -Uri https://www.google.com
   Invoke-WebRequest -Uri https://www.google.com

   Invoke-WebRequest -Uri https://www.microsoft.com
   Invoke-WebRequest -Uri https://www.microsoft.com
   ```

   As solicitações de www.google.com devem ter êxito, e as solicitações de www.microsoft.com devem falhar. Isso demonstra que suas regras de firewall estão funcionando conforme o esperado.

Agora que você verificou se as regras de firewall estão funcionando:

* É possível resolver nomes DNS usando o servidor DNS externo configurado.
* Você pode navegar para o FQDN permitido, mas não para os outros.

## <a name="clean-up-resources"></a>Limpar recursos

Você pode manter seus recursos de firewall para o próximo tutorial, ou se não for mais necessário, exclua o **FW-teste-RG** grupo de recursos para excluir todos os recursos relacionados ao firewall:

```azurecli-interactive
az group delete \
  -n Test-FW-RG
```

## <a name="next-steps"></a>Próximas etapas

* [Tutorial: Monitorar os logs do Firewall do Azure](./tutorial-diagnostics.md)
