---
title: 'Início Rápido: Direcionar o tráfego da Web usando a CLI'
titleSuffix: Azure Application Gateway
description: Neste guia de início rápido, você aprenderá a usar a CLI do Azure para criar um Gateway de Aplicativo do Azure que direciona o tráfego da Web para máquinas virtuais em um pool de back-end.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 01/19/2021
ms.author: victorh
ms.custom: mvc, devx-track-js, devx-track-azurecli
ms.openlocfilehash: 1a691d8ab212dd436b0dc9f7aafbc19a406b12b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98601777"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-cli"></a>Início Rápido: Direcionar o tráfego da Web com o Gateway de Aplicativo do Azure – CLI do Azure

Neste início rápido, você usará a CLI do Azure para criar um gateway de aplicativo. Em seguida, você o testará para verificar se ele funciona corretamente. 

O gateway de aplicativo direciona o tráfego da Web do aplicativo para recursos específicos em um pool de back-end. Você atribuirá ouvintes a portas, criará regras e adicionará recursos a um pool de back-end. Para simplificar, este artigo usa uma configuração simples com um endereço IP de front-end público, um ouvinte básico para hospedar um site no gateway de aplicativo, uma regra básica de roteamento de solicitação e duas máquinas virtuais no pool de back-end.

Conclua também este início rápido usando o [Azure PowerShell](quick-create-powershell.md) ou o [portal do Azure](quick-create-portal.md).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Este artigo exige a versão 2.0.4 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="create-resource-group"></a>Criar grupo de recursos

No Azure, você pode alocar recursos relacionados a um grupo de recursos. Crie um grupo de recursos usando `az group create`. 

O exemplo a seguir cria um grupo de recursos denominado *myResourceGroupAG* no local *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Criar recursos da rede 

Para que o Azure se comunique entre os recursos que você cria, ele precisa de uma rede virtual.  A sub-rede de gateway de aplicativo pode conter apenas gateways de aplicativo. Nenhum outro recurso é permitido.  Você pode criar uma nova sub-rede do Gateway de aplicativo ou usar uma existente. Neste exemplo, você criará duas sub-redes: uma para o gateway de aplicativo e outra para os servidores de back-end. É possível configurar o IP de front-end do Gateway de Aplicativo como Público ou Privado, de acordo com o caso de uso. Neste exemplo, você escolherá um endereço IP público de front-end.

Para criar a rede virtual e a sub-rede, use `az network vnet create`. Execute `az network public-ip create` para criar o endereço IP público.

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

## <a name="create-the-backend-servers"></a>Criar servidores de back-end

Um back-end pode ter NICs, conjuntos de dimensionamento de máquinas virtuais, endereços IP públicos, endereços IP internos, FQDN (nomes de domínio totalmente qualificados) e back-ends multilocatário como Serviço de Aplicativo do Azure. Neste exemplo, você cria duas máquinas virtuais para serem usadas como servidores de back-end para o Gateway de Aplicativo. Você também pode instalar o IIS nas máquinas virtuais para testar o gateway de aplicativo.

#### <a name="create-two-virtual-machines"></a>Criar duas máquinas virtuais

Instale o servidor Web NGINX nas máquinas virtuais para verificar se o gateway de aplicativo foi criado com êxito. Você pode usar um arquivo de configuração cloud-init para instalar o NGINX e executar um aplicativo Node.js "Olá, Mundo" em uma máquina virtual Linux. Para obter mais informações sobre a cloud-init, veja [Suporte a cloud-init para máquinas virtuais no Azure](../virtual-machines/linux/using-cloud-init.md).

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

Crie os adaptadores de rede com `az network nic create`. Para criar as máquinas virtuais, use `az vm create`.

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

Crie um gateway de aplicativo usando `az network application-gateway create`. Quando você cria um gateway de aplicativo usando a CLI do Azure, pode especificar informações de configuração, como configurações de HTTP, SKU e capacidade. Em seguida, o Azure adiciona os endereços IP privados das interfaces de rede como servidores no pool de back-end do gateway de aplicativo.

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

Embora o Azure não exija um servidor Web NGINX para criar o gateway de aplicativo, você o instalou neste início rápido para verificar se o Azure criou o gateway de aplicativo com êxito. Para obter o endereço IP público do novo gateway de aplicativo, use `az network public-ip show`. 

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

Copie e cole o endereço IP público na barra de endereços do seu navegador.
    
![Teste o gateway de aplicativo](./media/quick-create-cli/application-gateway-nginxtest.png)

Quando atualizar o navegador, você deverá ver o nome da segunda VM. Isso indica que o gateway de aplicativo foi criado com êxito e que pode conectar-se ao back-end.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não precisar mais dos recursos que você criou com o gateway de aplicativo, use o comando `az group delete` para excluir o grupo de recursos. Ao excluir o grupo de recursos, exclua também o gateway de aplicativo e todos os recursos relacionados a ele.

```azurecli-interactive 
az group delete --name myResourceGroupAG
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Gerenciar o tráfego da web com um gateway de aplicativo usando a CLI do Azure](./tutorial-manage-web-traffic-cli.md)

