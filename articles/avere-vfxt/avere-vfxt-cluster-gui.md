---
title: Acessar o painel de controle do Avere vFXT – Azure
description: Como se conectar ao cluster do vFXT e ao painel de controle do Avere baseado em navegador para configurar o Avere vFXT
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 12/14/2019
ms.author: rohogue
ms.openlocfilehash: 79e7c5db2a2c445ae740a21744a0bdfe0736c01a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92342426"
---
# <a name="access-the-vfxt-cluster"></a>Acessar o cluster do vFXT

Para ajustar as configurações de cluster e monitorar o cluster, use o painel de controle avere. O painel de controle do Avere é uma interface gráfica baseada em navegador para o cluster.

Como o cluster vFXT fica dentro de uma rede virtual privada, você deve criar um túnel SSH ou usar outro método para alcançar o endereço IP de gerenciamento do cluster.

Há duas etapas básicas:

1. Criar uma conexão entre a estação de trabalho e a rede virtual privada
1. Carregar o painel de controle do cluster em um navegador da Web

> [!NOTE]
> Este artigo pressupõe que você definiu um endereço IP público no controlador do cluster ou em outra VM na rede virtual de seu cluster. Este artigo descreve como usar essa VM como um host para acessar o cluster. Se você estiver usando uma VPN ou ExpressRoute para acesso à rede virtual, pule para [conectar-se ao painel de controle do avere](#connect-to-the-avere-control-panel-in-a-browser).

Antes de se conectar, certifique-se de que o par de chaves pública/privada SSH que você usou ao criar o controlador do cluster esteja instalado no computador local. Leia a documentação das chaves SSH para [Windows](../virtual-machines/linux/ssh-from-windows.md) ou para [Linux](../virtual-machines/linux/mac-create-ssh-keys.md) se você precisar de ajuda. Se você usou uma senha em vez de uma chave pública, será solicitado a inseri-la ao se conectar.

## <a name="create-an-ssh-tunnel"></a>Criar um túnel SSH

Você pode criar um túnel SSH na linha de comando de um sistema cliente baseado em Linux ou Windows 10.

Use um comando de túnel SSH com este formulário:

ssh -L *local_port*:*cluster_mgmt_ip*:443 *controller_username*\@*controller_public_IP*

Este comando estabelece a conexão com o endereço IP de gerenciamento do cluster por meio do endereço IP do controlador do cluster.

Exemplo:

```sh
ssh -L 8443:10.0.0.5:443 azureuser@203.0.113.51
```

A autenticação será automática se você tiver usado sua chave pública SSH para criar o cluster e a chave correspondente estiver instalada no sistema cliente. Se você tiver usado uma senha, o sistema solicitará que você a insira.

## <a name="connect-to-the-avere-control-panel-in-a-browser"></a>Conectar-se ao painel de controle do Avere em um navegador

Esta etapa usa um navegador da Web para se conectar ao utilitário de configuração no cluster vFXT.

* Para uma conexão de túnel SSH, abra seu navegador da Web e navegue até `https://127.0.0.1:8443`.

  Você conectou ao cluster do endereço IP ao criar o túnel, portanto, você só precisa usar o endereço IP do host local no navegador. Se você tiver usado uma porta local diferente de 8443, use seu número de porta.

* Se você estiver usando uma VPN ou ExpressRoute para acessar o cluster, navegue até o endereço IP de gerenciamento de cluster em seu navegador. Exemplo: ``https://203.0.113.51``

Dependendo de seu navegador, talvez seja necessário clicar em **Avançado** e verificar se é seguro prosseguir para a página.

Insira o nome de usuário `admin` e a senha administrativa fornecidos no momento da criação do cluster.

![Captura de tela da página de entrada do Avere preenchida com o nome de usuário "admin" e uma senha](media/avere-vfxt-gui-login.png)

Clique em **Logon** ou pressione enter no teclado.

## <a name="next-steps"></a>Próximas etapas

Depois de fazer logon no painel de controle do cluster, habilite o [suporte](avere-vfxt-enable-support.md).