---
title: Acessar o painel de controle do Avere vFXT – Azure
description: Como se conectar ao cluster do vFXT e ao painel de controle do Avere baseado em navegador para configurar o Avere vFXT
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: v-erkell
ms.openlocfilehash: 830be92d37f304598cca05c3ac80973158c38a59
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67439979"
---
# <a name="access-the-vfxt-cluster"></a>Acessar o cluster do vFXT

Para alterar as configurações e monitorar o cluster do Avere vFXT, use o painel de controle do Avere. O painel de controle do Avere é uma interface gráfica baseada em navegador para o cluster.

Como o cluster do vFXT fica dentro de uma rede virtual privada, você precisa criar um túnel SSH ou usar outro método para alcançar o endereço IP de gerenciamento do cluster. Há duas etapas básicas: 

1. Criar uma conexão entre sua estação de trabalho e a rede virtual privada 
1. Carregar o painel de controle do cluster em um navegador da Web 

> [!NOTE] 
> Este artigo pressupõe que você definiu um endereço IP público no controlador do cluster ou em outra VM na rede virtual de seu cluster. Este artigo descreve como usar essa VM como um host para acessar o cluster. Se você estiver usando uma VPN ou o ExpressRoute para acessar a rede virtual, vá para [Conectar-se ao painel de controle do Avere](#connect-to-the-avere-control-panel-in-a-browser).

Antes de se conectar, certifique-se de que o par de chaves pública/privada SSH que você usou ao criar o controlador do cluster esteja instalado no computador local. Leia a documentação das chaves SSH para [Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows) ou para [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys) se você precisar de ajuda. (Se você tiver usado uma senha, em vez de uma chave pública, será solicitado a inseri-la ao se conectar.) 

## <a name="create-an-ssh-tunnel"></a>Criar um túnel SSH 

Você pode criar um túnel SSH na linha de comando de um baseado em Linux ou sistema de cliente do Windows 10. 

Use um comando com essa forma de túnel de SSH: 

ssh -L *local_port*:*cluster_mgmt_ip*:443 *controller_username*\@*controller_public_IP*

Este comando estabelece a conexão com o endereço IP de gerenciamento do cluster por meio do endereço IP do controlador do cluster.

Exemplo:

```sh
ssh -L 8443:10.0.0.5:443 azureuser@203.0.113.51
```

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
