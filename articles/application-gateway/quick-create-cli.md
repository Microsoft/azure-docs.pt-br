---
title: Início Rápido – Direcionar o tráfego da Web com o Gateway de Aplicativo do Azure – CLI do Azure | Microsoft Docs
description: Saiba como usar a CLI do Azure para criar um Gateway de Aplicativo do Azure que direciona o tráfego da Web para máquinas virtuais em um pool de back-end.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 07/19/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: dd68f4a565c28f1dbac7e94442a8f8231af01328
ms.sourcegitcommit: da0a8676b3c5283fddcd94cdd9044c3b99815046
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68314909"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-cli"></a>Início Rápido: Direcionar o tráfego da Web com o Gateway de Aplicativo do Azure – CLI do Azure

Este início rápido mostra como usar a CLI do Azure para criar um gateway de aplicativo.  Após criar o gateway de aplicativo, você vai testá-lo para verificar se está funcionando corretamente. Com Gateway de Aplicativo do Azure, você direciona o tráfego de aplicativo Web para recursos específicos, atribuindo ouvintes a portas, criando regras e adicionando recursos a um pool de back-end. Este artigo usa uma configuração simples com um IP de front-end público, um ouvinte básico para hospedar um único site no gateway de aplicativo, duas máquinas virtuais usadas para o pool de back-end e uma regra de roteamento de solicitações básica.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Pré-requisitos

### <a name="azure-cli"></a>CLI do Azure

Se você optar por instalar e usar a CLI localmente, execute a CLI do Azure versão 2.0.4 ou posterior. Para localizar a versão, execute **az --version**. Para saber mais sobre como instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

### <a name="resource-group"></a>Resource group

No Azure, você pode alocar recursos relacionados a um grupo de recursos. Crie um grupo de recursos usando [az group create](/cli/azure/group#az-group-create). 

O exemplo a seguir cria um grupo de recursos denominado *myResourceGroupAG* no local *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

### <a name="required-network-resources"></a>Recursos de rede necessários 

Para que o Azure se comunique entre os recursos que você cria, ele precisa de uma rede virtual.  A sub-rede de gateway de aplicativo pode conter apenas gateways de aplicativo. Nenhum outro recurso é permitido.  Você pode criar uma nova sub-rede do Gateway de aplicativo ou usar uma existente. Crie duas sub-redes neste exemplo: uma para o gateway de aplicativo e outra para os servidores de back-end. É possível configurar o IP de front-end do Gateway de Aplicativo como Público ou Privado, de acordo com o caso de uso. Neste exemplo, você escolherá um IP público de front-end.

Para criar a rede virtual e a sub-rede, use [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create). Execute [az network public-ip create](/cli/azure/network/public-ip) para criar o endereço IP público.

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroupAG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24
az network vnet subnet create \
  --name myBackendSubnet \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet   \
  --address-prefix 10.0.2.0/24
az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --allocation-method Static \
  --sku Standard
```

### <a name="backend-servers"></a>Servidores de back-end

Um back-end pode ter NICs, conjuntos de dimensionamento de máquinas virtuais, IPs públicos, IPs internos, FQDN (nomes de domínio totalmente qualificados) e back-ends multilocatário como Serviço de Aplicativo do Azure. Neste exemplo, você cria duas máquinas virtuais para serem usadas como servidores de back-end para o Gateway de Aplicativo. Você também pode instalar o IIS nas máquinas virtuais para testar o gateway de aplicativo.

#### <a name="create-two-virtual-machines"></a>Criar duas máquinas virtuais

Instale o [servidor Web NGINX](https://docs.nginx.com/nginx/) nas máquinas virtuais para verificar se o gateway de aplicativo foi criado com êxito. Você pode usar um arquivo de configuração cloud-init para instalar o NGINX e executar um aplicativo Node.js "Olá, Mundo" em uma máquina virtual Linux. Para obter mais informações sobre a cloud-init, veja [Suporte a cloud-init para máquinas virtuais no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init).

No Azure Cloud Shell, copie e cole a seguinte configuração em um arquivo chamado *cloud-init.txt*. Insira *editor cloud-init.txt* para criar o arquivo.

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```

Crie as interfaces de rede com [az network create](/cli/azure/network/nic#az-network-nic-create). Para criar as máquinas virtuais, use [az vm create](/cli/azure/vm#az-vm-create).

```azurecli-interactive
for i in `seq 1 2`; do
  az network nic create \
    --resource-group myResourceGroupAG \
    --name myNic$i \
    --vnet-name myVNet \
    --subnet myBackendSubnet
  az vm create \
    --resource-group myResourceGroupAG \
    --name myVM$i \
    --nics myNic$i \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
done
```

## <a name="create-the-application-gateway"></a>Criar o gateway de aplicativo

Crie um gateway de aplicativo usando [az network application-gateway create](/cli/azure/network/application-gateway). Quando você cria um gateway de aplicativo usando a CLI do Azure, pode especificar informações de configuração, como configurações de HTTP, SKU e capacidade. Em seguida, o Azure adiciona os endereços IP privados das interfaces de rede como servidores no pool de back-end do gateway de aplicativo.

```azurecli-interactive
address1=$(az network nic show --name myNic1 --resource-group myResourceGroupAG | grep "\"privateIpAddress\":" | grep -oE '[^ ]+$' | tr -d '",')
address2=$(az network nic show --name myNic2 --resource-group myResourceGroupAG | grep "\"privateIpAddress\":" | grep -oE '[^ ]+$' | tr -d '",')
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --capacity 2 \
  --sku Standard_v2 \
  --http-settings-cookie-based-affinity Enabled \
  --public-ip-address myAGPublicIPAddress \
  --vnet-name myVNet \
  --subnet myAGSubnet \
  --servers "$address1" "$address2"
```

Pode levar até 30 minutos para que o Azure crie o gateway de aplicativo. Depois que ele é criado, você pode exibir as configurações a seguir na seção **Configurações** da página **Gateway de aplicativo**:

- **appGatewayBackendPool**: Localizado na página **Pools de back-end**. Especifica o pool de back-end necessário.
- **appGatewayBackendHttpSettings**: Localizado na página **Configurações de HTTP**. Especifica que o gateway de aplicativo usa a porta 80 e o protocolo HTTP para comunicação.
- **appGatewayHttpListener**: Localizado na página **Ouvintes**. Especifica o ouvinte padrão associado a **appGatewayBackendPool**.
- **appGatewayFrontendIP**: Localizado na página **Configurações de IP de front-end**. Ele atribui *myAGPublicIPAddress* a **appGatewayHttpListener**.
- **rule1**: Localizado na página **Regras**. Especifica a regra de roteamento padrão que está associada ao **appGatewayHttpListener**.

## <a name="test-the-application-gateway"></a>Testar o gateway de aplicativo

Embora o Azure não exija um servidor Web NGINX para criar o gateway de aplicativo, você o instalou neste início rápido para verificar se o Azure criou o gateway de aplicativo com êxito. Para obter o endereço IP público do novo gateway de aplicativo, use [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show). 

```azurepowershell-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

Copie e cole o endereço IP público na barra de endereços do seu navegador.
    
![Teste o gateway de aplicativo](./media/quick-create-cli/application-gateway-nginxtest.png)

Quando atualizar o navegador, você deverá ver o nome da segunda VM. Isso indica que o gateway de aplicativo foi criado com êxito e que pode conectar-se ao back-end.

## <a name="clean-up-resources"></a>Limpar recursos

Quando não precisar mais dos recursos que você criou com o gateway de aplicativo, use o comando [az group delete](/cli/azure/group#az-group-delete) para remover o grupo de recursos. Ao remover o grupo de recursos, você também remove o gateway de aplicativo e todos os recursos relacionados.

```azurecli-interactive 
az group delete --name myResourceGroupAG
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Gerenciar o tráfego da web com um gateway de aplicativo usando a CLI do Azure](./tutorial-manage-web-traffic-cli.md)

