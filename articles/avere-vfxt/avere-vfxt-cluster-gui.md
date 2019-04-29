---
title: Acessar o painel de controle do Avere vFXT – Azure
description: Como se conectar ao cluster do vFXT e ao painel de controle do Avere baseado em navegador para configurar o Avere vFXT
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: f989f4d103efecf2b6e206287dd8b7b300a1796d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60794316"
---
# <a name="access-the-vfxt-cluster"></a>Acessar o cluster do vFXT

Para alterar as configurações e monitorar o cluster do Avere vFXT, use o painel de controle do Avere. O painel de controle do Avere é uma interface gráfica baseada em navegador para o cluster.

Como o cluster do vFXT fica dentro de uma rede virtual privada, você precisa criar um túnel SSH ou usar outro método para alcançar o endereço IP de gerenciamento do cluster. Há duas etapas básicas: 

1. Criar uma conexão entre sua estação de trabalho e a rede virtual privada 
1. Carregar o painel de controle do cluster em um navegador da Web 

> [!NOTE] 
> Este artigo pressupõe que você definiu um endereço IP público no controlador do cluster ou em outra VM na rede virtual de seu cluster. Este artigo descreve como usar essa VM como um host para acessar o cluster. Se você estiver usando uma VPN ou o ExpressRoute para acessar a rede virtual, vá para [Conectar-se ao painel de controle do Avere](#connect-to-the-avere-control-panel-in-a-browser).

Antes de se conectar, certifique-se de que o par de chaves pública/privada SSH que você usou ao criar o controlador do cluster esteja instalado no computador local. Leia a documentação das chaves SSH para [Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows) ou para [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys) se você precisar de ajuda. (Se você tiver usado uma senha, em vez de uma chave pública, será solicitado a inseri-la ao se conectar.) 

## <a name="ssh-tunnel-with-a-linux-host"></a>Túnel Secure Shell com um host do Linux

Se usar um cliente baseado em Linux, use um comando de túnel Secure Shell com esse formulário: 

ssh -L *local_port*:*cluster_mgmt_ip*:443 *controller_username*\@*controller_public_IP*

Este comando estabelece a conexão com o endereço IP de gerenciamento do cluster por meio do endereço IP do controlador do cluster.

Exemplo:

```sh
ssh -L 8443:10.0.0.5:443 azureuser@203.0.113.51
```

A autenticação será automática se você tiver usado sua chave pública SSH para criar o cluster e a chave correspondente estiver instalada no sistema cliente. Se você tiver usado uma senha, o sistema solicitará que você a insira.

## <a name="ssh-tunnel-with-a-windows-host"></a>Túnel SSH com um host do Windows

Este exemplo usa o utilitário terminal baseado no Windows comum, PuTTY.

Preencha o campo **nome do host** no PuTTY com o nome de usuário do controlador do cluster e seu endereço IP: *your_username*\@*controller_public_IP*.

Exemplo: ``azureuser@203.0.113.51``

No painel **Configuração**:

1. Expanda **Conexão** > **SSH** à esquerda. 
1. Clique em **Túneis**. 
1. Insira uma porta de origem, como 8443. 
1. Para o destino, insira o endereço IP de gerenciamento do cluster do vFXT e a porta 443. 
   Exemplo: ``203.0.113.51:443``
1. Clique em **Adicionar**.
1. Clique em **Abrir**.

![Captura de tela do aplicativo Putty mostrando onde clicar para adicionar um túnel](media/avere-vfxt-ptty-numbered.png)

A autenticação será automática se você tiver usado sua chave pública SSH para criar o cluster e a chave correspondente estiver instalada no sistema cliente. Se você tiver usado uma senha, o sistema solicitará que você a insira.

## <a name="connect-to-the-avere-control-panel-in-a-browser"></a>Conectar-se ao painel de controle do Avere em um navegador

Esta etapa usa um navegador da Web para se conectar ao utilitário de configuração em execução no cluster do vFXT.

* Para uma conexão de túnel SSH, abra seu navegador da Web e navegue até `https://127.0.0.1:8443`. 

  Você conectou ao cluster do endereço IP ao criar o túnel, portanto, você só precisa usar o endereço IP do host local no navegador. Se você tiver usado uma porta local diferente de 8443, use seu número de porta.

* Se você estiver usando uma VPN ou ExpressRoute para acessar o cluster, navegue até o endereço IP de gerenciamento de cluster em seu navegador. Exemplo: ``https://203.0.113.51``

Dependendo de seu navegador, talvez seja necessário clicar em **Avançado** e verificar se é seguro prosseguir para a página.

Insira o nome de usuário `admin` e a senha administrativa fornecidos no momento da criação do cluster.

![Captura de tela da página de entrada do Avere preenchida com o nome de usuário "admin" e uma senha](media/avere-vfxt-gui-login.png)

Clique em **Logon** ou pressione enter no teclado.

## <a name="next-steps"></a>Próximas etapas

Agora que você pode acessar o cluster, habilite o [suporte](avere-vfxt-enable-support.md).
