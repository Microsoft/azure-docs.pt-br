---
title: 'Tutorial: Criar e testar um Gateway da NAT – portal do Azure'
titlesuffix: Azure Virtual Network NAT
description: Este tutorial mostra como criar um Gateway da NAT usando o portal do Azure e testar o serviço NAT
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumundD
Customer intent: I want to test a NAT Gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/24/2020
ms.author: allensu
ms.openlocfilehash: d798725892a9586c17cd7023863fe5cf7df05cb6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "84417830"
---
# <a name="tutorial-create-a-nat-gateway-using-the-azure-portal-and-test-the-nat-service"></a>Tutorial: Criar um Gateway da NAT usando o portal do Azure e testar o serviço NAT

Neste tutorial, você criará um gateway da NAT para fornecer conectividade de saída para máquinas virtuais no Azure. Para testar o gateway da NAT, implante uma máquina virtual de origem e de destino. Você testará o gateway da NAT fazendo conexões de saída com um endereço IP público da máquina virtual de origem para a máquina virtual de destino.  Este tutorial implanta a origem e o destino em duas redes virtuais diferentes no mesmo grupo de recursos apenas para simplificar.

Se você preferir, poderá realizar essas etapas usando a [CLI do Azure](tutorial-create-validate-nat-gateway-cli.md) ou o [Azure PowerShell](tutorial-create-validate-nat-gateway-powershell.md) em vez do portal.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no [portal do Azure](https://portal.azure.com).

## <a name="prepare-the-source-for-outbound-traffic"></a>Preparar a origem para o tráfego de saída

Orientaremos você pela configuração de um ambiente de teste completo e a execução dos testes em si nas próximas etapas. Começaremos com a origem, que usará o recurso de gateway da NAT que criaremos nas etapas posteriores.

## <a name="virtual-network-and-parameters"></a>Rede virtual e parâmetros

Antes de implantar uma VM e usar o gateway da NAT, precisamos criar o grupo de recursos e a rede virtual.

Nesta seção, você precisará substituir os seguintes parâmetros nas etapas pelas informações abaixo:

| Parâmetro                   | Valor                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupNAT |
| **\<virtual-network-name>** | myVNetsource          |
| **\<region-name>**          | Leste dos EUA 2      |
| **\<IPv4-address-space>**   | 192.168.0.0/16          |
| **\<subnet-name>**          | mySubnetsource        |
| **\<subnet-address-range>** | 192.168.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="create-source-virtual-machine"></a>Criar a máquina virtual de origem

Agora criaremos uma VM para usar o serviço NAT. Essa VM tem um IP público para ser usado como um IP público em nível de instância para que você possa acessar a VM. O serviço NAT reconhece a direção do fluxo e substituirá o destino da Internet padrão em sua sub-rede. O endereço IP público da VM não será usado para conexões de saída.

Para testar o gateway da NAT, atribuiremos um recurso de endereço IP público como um IP público em nível de instância para acessar essa VM externamente. Esse endereço é usado somente para acessá-la para o teste.  Demonstraremos como o serviço NAT tem precedência sobre outras opções de saída.

Você também pode criar essa VM sem um IP público e criar outra VM para usá-la como um jumpbox sem um IP público como um exercício.

1. No lado superior esquerdo do portal, selecione **Criar um recurso** > **Computação** > **Ubuntu Server 18.04 LTS** ou procure **Ubuntu Server 18.04 LTS** na pesquisa do Marketplace.

2. Em **Criar uma máquina virtual**, insira ou selecione os seguintes valores na guia **Informações Básicas**:
   - **Assinatura** > **Grupo de Recursos**: Selecione **myResourceGroupNAT**.
   - **Detalhes da Instância** > **Nome da máquina virtual**: insira **myVMsource**.
   - **Detalhes da Instância** > **Região** > selecione **Leste dos EUA 2**.
   - **Conta de administrador** > **Autenticação, insira**: Selecione **Senha**.
   - **Conta de administrador** > Insira as informações de **Nome de usuário**, **Senha** e **Confirmar senha**.
   - **Regras de porta de entrada** > **Portas de entrada públicas**: Selecione **Permitir portas selecionadas**.
   - **Regras de porta de entrada** > **Selecionar portas de entrada**: Selecione **SSH (22)**
   - Selecione a guia **Rede** ou selecione **Avançar: Discos**, em seguida, **Avançar: Rede**.

3. Na guia **Rede** verifique se os itens abaixo estão selecionados:
   - **Rede virtual**: **myVnetsource**
   - **Sub-rede**: **mySubnetsource**
   - **IP Público** > selecione **Criar**.  Na janela **Criar endereço IP público**, insira **myPublicIPsourceVM** no campo **Nome**. Selecione **Standard** para o **SKU**. Mantenha o restante com os padrões e clique em **OK**.
   - **Grupo de segurança de rede da NIC**: Selecione **Basic**.
   - **Porta de entrada públicas**: Selecione **Permitir portas selecionadas**.
   - **Selecionar as portas de entrada**: Confirme se **SSH** está selecionado.

4. Na guia **Gerenciamento**, em **Monitoramento**, defina **Diagnóstico de inicialização** como **Desativado**.

5. Selecione **Examinar + criar**.

6. Examine as configurações e clique em **Criar**.

## <a name="create-the-nat-gateway"></a>Criar o Gateway da NAT

Você pode usar um ou mais recursos de endereço IP público, prefixos IP público ou ambos com o gateway da NAT. Adicionaremos um recurso de IP público, um prefixo IP público e um recurso de gateway da NAT.

Esta seção fornece detalhes sobre como criar e configurar os seguintes componentes do serviço NAT usando o recurso de gateway da NAT:
  - Um pool de IPs públicos e o prefixo IP público a serem usados para fluxos de saída convertidos pelo recurso de Gateway da NAT.
  - Altere o tempo limite de ociosidade do padrão de 4 minutos para 10 minutos.

### <a name="create-a-public-ip-address"></a>Criar um endereço IP público

1. No lado superior esquerdo do portal, selecione **Criar um recurso** > **Rede** > **Endereço IP público** ou procure **Endereço IP público** na pesquisa do Marketplace. 

2. Em **Criar endereço IP público**, insira ou selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | Versão IP | Selecione **IPv4**.
    | SKU | Selecione **Padrão**.
    | Nome | Insira **myPublicIPsource**. |
    | Subscription | Selecione sua assinatura.|
    | Resource group | Selecione **myResourceGroupNAT**. |
    | Location | Selecione **Leste dos EUA 2**.|

3. Deixe o restante dos padrões e selecione **Criar**.

### <a name="create-a-public-ip-prefix"></a>Criar um prefixo IP público

1. No lado superior esquerdo do portal, selecione **Criar um recurso** > **Rede** > **Prefixo IP público** ou procure **Prefixo IP público** na pesquisa do Marketplace.

2. Em **Criar um prefixo IP público**, insira ou selecione os seguintes valores na guia **Informações Básicas**:
   - **Assinatura** > **Grupo de Recursos**: Selecione **myResourceGroupNAT**>
   - **Detalhes da instância** > **Nome**: insira **myPublicIPprefixsource**.
   - **Detalhes da instância** > **Região**: Selecione **Leste dos EUA 2**.
   - **Detalhes da instância** > **Tamanho do prefixo**: Selecione **/31 (2 endereços)**

3. Mantenha o restante dos padrões e selecione **Examinar + criar**.

4. Examine as configurações e selecione **Criar**.


### <a name="create-a-nat-gateway-resource"></a>Criar um recurso de gateway da NAT

1. No lado superior esquerdo do portal, selecione **Criar um recurso** > **Rede** > **gateway da NAT** ou procure **gateway da NAT** na pesquisa do Marketplace.

2. Em **Criar gateway da NAT (conversão de endereços de rede)** , insira ou selecione os seguintes valores na guia **Informações Básicas**:
   - **Assinatura** > **Grupo de Recursos**: Selecione **myResourceGroupNAT**.
   - **Detalhes da instância** > **Nome do gateway da NAT**: insira **myNATgateway**.
   - **Detalhes da instância** > **Região**: Selecione **Leste dos EUA 2**.
   - **Detalhes da instância** > **Tempo limite de ociosidade (minutos)** : insira **10**.
   - Selecione a guia **IP Público** ou selecione **Avançar: IP Público**.

3. Na guia **IP Público**, insira ou selecione os seguintes valores:
   - **Endereços IP públicos**: Selecione **myPublicIPsource**.
   - **Prefixos IP públicos**: Selecione **myPublicIPprefixsource**.
   - Selecione a guia **Sub-rede** ou selecione **Avançar: Sub-rede**.

4. Na guia **Sub-rede**, insira ou selecione os seguintes valores:
   - **Rede virtual**: Selecione **myResourceGroupNAT** > **myVnetsource**.
   - **Nome da sub-rede**: Selecione a caixa ao lado de **mySubnetsource**.

5. Selecione **Examinar + criar**.

6. Examine as configurações e selecione **Criar**.

Todo o tráfego de saída para os destinos da Internet agora usa o serviço NAT.  Não é necessário configurar uma UDR.


## <a name="prepare-destination-for-outbound-traffic"></a>Preparar destino para o tráfego de saída

Agora criaremos um destino para o tráfego de saída convertido pelo serviço NAT para permitir que você o teste.


## <a name="virtual-network-and-parameters-for-destination"></a>Rede virtual e parâmetros para o destino

Antes de implantar uma VM para o destino, precisamos criar uma rede virtual em que a máquina virtual de destino possa residir. Estas são as mesmas etapas para a VM de origem com algumas pequenas alterações para expor o ponto de extremidade de destino.

Nesta seção, você precisará substituir os seguintes parâmetros nas etapas pelas informações abaixo:

| Parâmetro                   | Valor                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupNAT |
| **\<virtual-network-name>** | myVNetdestination          |
| **\<region-name>**          | Leste dos EUA 2      |
| **\<IPv4-address-space>**   | 10.1.0.0/16          |
| **\<subnet-name>**          | mySubnetdestination        |
| **\<subnet-address-range>** | 10.1.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="create-destination-virtual-machine"></a>Escolher a máquina virtual de destino

1. No lado superior esquerdo do portal, selecione **Criar um recurso** > **Computação** > **Ubuntu Server 18.04 LTS** ou procure **Ubuntu Server 18.04 LTS** na pesquisa do Marketplace.

2. Em **Criar uma máquina virtual**, insira ou selecione os seguintes valores na guia **Informações Básicas**:
   - **Assinatura** > **Grupo de Recursos**: Selecione **myResourceGroupNAT**.
   - **Detalhes da instância** > **Nome da máquina virtual**: insira **myVMdestination**.
   - **Detalhes da Instância** > **Região** > selecione **Leste dos EUA 2**.
   - **Conta de administrador** > **Autenticação, insira**: Selecione **Senha**.
   - **Conta de administrador** > Insira as informações de **Nome de usuário**, **Senha** e **Confirmar senha**.
   - **Regras de porta de entrada** > **Portas de entrada públicas**: Selecione **Permitir portas selecionadas**.
   - **Regras de porta de entrada** > **Selecionar portas de entrada**: Selecione **SSH (22)** e **HTTP (80)** .
   - Selecione a guia **Rede** ou selecione **Avançar: Discos**, em seguida, **Avançar: Rede**.

3. Na guia **Rede** verifique se os itens abaixo estão selecionados:
   - **Rede virtual**: **myVnetdestination**
   - **Sub-rede**: **mySubnetdestination**
   - **IP Público** > selecione **Criar**.  Na janela **Criar endereço IP público**, insira **myPublicIPdestinationVM** no campo **Nome**. Selecione **Standard** para **SKU**. Mantenha o restante com os padrões e clique em **OK**.
   - **Grupo de segurança de rede da NIC**: Selecione **Basic**.
   - **Porta de entrada públicas**: Selecione **Permitir portas selecionadas**.
   - **Selecionar as portas de entrada**: Confirme se **SSH** e **HTTP** estão selecionados.

4. Na guia **Gerenciamento**, em **Monitoramento**, defina **Diagnóstico de inicialização** como **Desativado**.

5. Selecione **Examinar + criar**.

6. Examine as configurações e selecione **Criar**.

## <a name="prepare-a-web-server-and-test-payload-on-destination-vm"></a>Preparar um servidor Web e testar o conteúdo na VM de destino

Primeiro precisamos descobrir o endereço IP da VM de destino. 

1. No lado esquerdo do portal, selecione **Grupos de recursos**.
2. Selecione **myResourceGroupNAT**.
3. Selecione **myVMdestination**.
4. Em **Visão geral**, copie o valor do **Endereço IP público** e cole-o no bloco de notas para usá-lo e acessar a VM.

>[!IMPORTANT]
>Copie o endereço IP público e cole-o em um bloco de notas para usá-lo nas etapas seguintes. Indique isso como a máquina virtual de destino.

### <a name="sign-in-to-destination-vm"></a>Entrar na VM de destino

Abra um [Azure Cloud Shell](https://shell.azure.com) no navegador. Use o endereço IP recuperado na etapa anterior para se conectar por SSH à máquina virtual.

```azurecli-interactive
ssh <username>@<ip-address-destination>
```

Copie e cole os comandos a seguir depois de fazer logon.  

```bash
sudo apt-get -y update && \
sudo apt-get -y upgrade && \
sudo apt-get -y dist-upgrade && \
sudo apt-get -y autoremove && \
sudo apt-get -y autoclean && \
sudo apt-get -y install nginx && \
sudo ln -sf /dev/null /var/log/nginx/access.log && \
sudo touch /var/www/html/index.html && \
sudo rm /var/www/html/index.nginx-debian.html && \
sudo dd if=/dev/zero of=/var/www/html/100k bs=1024 count=100
```

Esses comandos atualizarão sua máquina virtual, instalarão o nginx e criarão um arquivo de 100 KB. Esse arquivo será recuperado da VM de origem usando o serviço NAT.

Feche a sessão SSH com a VM de destino.

## <a name="prepare-test-on-source-vm"></a>Preparar o teste na VM de origem

Primeiro precisamos descobrir o endereço IP da VM de origem.

1. No lado esquerdo do portal, selecione **Grupos de recursos**.
2. Selecione **myResourceGroupNAT**.
3. Selecione **myVMsource**.
4. Em **Visão geral**, copie o valor do **Endereço IP público** e cole-o no bloco de notas para usá-lo e acessar a VM.

>[!IMPORTANT]
>Copie o endereço IP público e cole-o em um bloco de notas para usá-lo nas etapas seguintes. Indique isso como a máquina virtual de origem.

### <a name="log-into-source-vm"></a>Fazer logon na VM de origem

Abra uma nova guia para o [Azure Cloud Shell](https://shell.azure.com) no navegador.  Use o endereço IP recuperado na etapa anterior para se conectar por SSH à máquina virtual. 

```azurecli-interactive
ssh <username>@<ip-address-source>
```

Copie e cole os comandos a seguir para se preparar para testar o serviço NAT.

```bash
sudo apt-get -y update && \
sudo apt-get -y upgrade && \
sudo apt-get -y dist-upgrade && \
sudo apt-get -y autoremove && \
sudo apt-get -y autoclean && \
sudo apt-get install -y nload golang && \
echo 'export GOPATH=${HOME}/go' >> .bashrc && \
echo 'export PATH=${PATH}:${GOPATH}/bin' >> .bashrc && \
. ~/.bashrc &&
go get -u github.com/rakyll/hey

```

Esse comando atualizará sua máquina virtual, instalará o go, instalará o [hey](https://github.com/rakyll/hey) por meio do GitHub e atualizará o ambiente de shell.

Agora você está pronto para testar o serviço NAT.

## <a name="validate-nat-service"></a>Validar o serviço NAT

Enquanto estiver conectado à VM de origem, você poderá usar o **curl** e o **hey** para gerar solicitações para o endereço IP de destino.

Use curl para recuperar o arquivo de 100 KB.  Substitua **\<ip-address-destination>** no exemplo abaixo pelo endereço IP de destino copiado anteriormente.  O parâmetro **--output** indica que o arquivo recuperado será descartado.

```bash
curl http://<ip-address-destination>/100k --output /dev/null
```

Você também pode gerar uma série de solicitações usando o **hey**. Novamente, substitua **\<ip-address-destination>** pelo endereço IP de destino que você copiou anteriormente.

```bash
hey -n 100 -c 10 -t 30 --disable-keepalive http://<ip-address-destination>/100k
```

Esse comando vai gerar 100 solicitações, 10 simultaneamente, com um tempo limite de 30 segundos e sem reutilizar a conexão TCP.  Cada solicitação recuperará 100 KB.  No fim da execução, o **hey** relatará algumas estatísticas sobre o desempenho do serviço NAT.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não forem mais necessários, exclua o grupo de recursos, o gateway da NAT e todos os recursos relacionados. Selecione o grupo de recursos **myResourceGroupNAT** que contém o gateway da NAT e, em seguida, selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você criou um gateway da NAT, criou uma VM de origem e destino e, em seguida, testou o gateway da NAT.

Examine as métricas no Azure Monitor para verificar a operação do serviço NAT. Diagnostique problemas como o esgotamento de recursos das portas SNAT disponíveis.  O esgotamento de recursos das portas SNAT é resolvido com facilidade adicionando mais recursos de endereço IP público, recursos de prefixo IP público ou ambos.

- Saiba mais sobre a [NAT de Rede Virtual](./nat-overview.md)
- Saiba mais sobre o [recurso de gateway da NAT](./nat-gateway-resource.md).
- Início Rápido para implantar o [recurso de gateway da NAT usando a CLI do Azure](./quickstart-create-nat-gateway-cli.md).
- Início Rápido para implantar o [recurso de gateway da NAT usando o Azure PowerShell](./quickstart-create-nat-gateway-powershell.md).
- Início Rápido para implantar o [recurso de gateway da NAT usando o portal do Azure](./quickstart-create-nat-gateway-portal.md).

> [!div class="nextstepaction"]

