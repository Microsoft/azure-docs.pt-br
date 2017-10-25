---
title: "Usar a extensão CustomScript em uma VM do Linux | Microsoft Docs"
description: "Saiba como usar a extensão CustomScript para implantar aplicativos em Máquinas Virtuais do Linux no Azure criadas com o modelo de implantação clássico."
editor: tysonn
manager: timlt
documentationcenter: 
services: virtual-machines-linux
author: gbowerman
tags: azure-service-management
ms.assetid: e535241d-feca-4412-b07a-67c936ba88a0
ms.service: virtual-machines-linux
ms.workload: multiple
ms.tgt_pltfrm: linux
ms.devlang: na
ms.topic: article
ms.date: 06/01/2017
ms.author: guybo
ms.openlocfilehash: cb1fc9a44dc9e57d9cc9f1c546ad937d67e63c2f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-a-lamp-app-using-the-azure-customscript-extension-for-linux"></a>Implantar um aplicativo LAMP usando a extensão CustomScript do Azure para Linux
> [!IMPORTANT] 
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de Recursos e Clássico](../../../resource-manager-deployment-model.md). Este artigo aborda o uso do modelo de implantação Clássica. A Microsoft recomenda que a maioria das implantações novas use o modelo do Gerenciador de Recursos. Para obter informações sobre como implantar uma pilha LAMP usando o modelo do Resource Manager, veja [aqui](../tutorial-lamp-stack.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

A Extensão de Script Personalizado do Microsoft Azure para Linux é uma maneira de personalizar VMs (máquinas virtuais) executando código arbitrário escrito em qualquer linguagem de script com suporte da VM (por exemplo, Python e Bash). Isso fornece uma maneira muito flexível para automatizar a implantação de aplicativos em várias máquinas.

Você pode implantar a Extensão CustomScript usando o portal do Azure, Windows PowerShell ou a Interface de linha de comando do Azure (CLI do Azure).

Neste artigo, usaremos a CLI do Azure para implantar um aplicativo simples da LAMP em uma VM do Ubuntu criada com o modelo de implantação clássico.

## <a name="prerequisites"></a>Pré-requisitos
Para este exemplo, primeiro crie duas VMs do Azure executando o Ubuntu 14.04 ou posterior. As VMs são chamadas *script-vm* e *lamp-vm*. Use nomes exclusivos ao criar as VMs. Uma é usada para executar os comandos da CLI e a outra para implantar o aplicativo LAMP.

Você também precisa de uma conta de Armazenamento do Azure e uma chave para acessá-la (você pode obtê-la no portal do Azure).

Se precisar de ajuda para criar VMs do Linux no Azure, confira [Criar uma máquina virtual executando Linux](createportal.md).

Os comandos install reconhecem o Ubuntu, mas você pode adaptar a instalação para qualquer distribuição Linux com suporte.

A VM script-vm precisa ter a CLI do Azure instalada e uma conexão ativa com o Azure. Para obter ajuda sobre isso, confira [Instalar e configurar a Interface de linha de comando do Azure](../../../cli-install-nodejs.md).

## <a name="upload-a-script"></a>Carregar um script
Usaremos a Extensão de Script Personalizado para executar um script em uma VM remota para instalar a pilha LAMP e criar uma página PHP. Para acessar o script em qualquer lugar, vou carregá-lo como um blob do Azure.

### <a name="script-overview"></a>Visão geral do script
O próximo script de exemplo instala uma pilha LAMP no Ubuntu (incluindo a configuração de uma instalação silenciosa do MySQL), grava um arquivo PHP simples e inicia o Apache.

    #!/bin/bash
    # set up a silent install of MySQL
    dbpass="mySQLPassw0rd"

    export DEBIAN_FRONTEND=noninteractive
    echo mysql-server-5.6 mysql-server/root_password password $dbpass | debconf-set-selections
    echo mysql-server-5.6 mysql-server/root_password_again password $dbpass | debconf-set-selections

    # install the LAMP stack
    apt-get -y install apache2 mysql-server php5 php5-mysql  

    # write some PHP
    echo \<center\>\<h1\>My Demo App\</h1\>\<br/\>\</center\> > /var/www/html/phpinfo.php
    echo \<\?php phpinfo\(\)\; \?\> >> /var/www/html/phpinfo.php

    # restart Apache
    apachectl restart

### <a name="upload-script"></a>Carregar o script
Salve o script como um arquivo de texto, por exemplo *install_lamp.sh* e carregue-o no Armazenamento do Azure. Você pode fazer isso facilmente com o Azure CLI. O exemplo a seguir carrega o arquivo em um contêiner de armazenamento chamado "scripts". Se o contêiner não existir, você precisará criá-lo primeiro.

    azure storage blob upload -a <yourStorageAccountName> -k <yourStorageKey> --container scripts ./install_lamp.sh

Crie também um arquivo JSON que descreva como baixar o script do Armazenamento do Azure. Salve isso como *public_config.json* (substituindo "mystorage" com o nome da sua conta de armazenamento):

    {"fileUris":["https://mystorage.blob.core.windows.net/scripts/install_lamp.sh"], "commandToExecute":"sh install_lamp.sh" }


## <a name="deploy-the-extension"></a>Implantar a extensão
Agora, você pode usar o próximo comando para implantar a Extensão de Script Personalizado do Linux na VM remota usando a CLI do Azure.

    azure vm extension set -c "./public_config.json" lamp-vm CustomScript Microsoft.Azure.Extensions 2.0

O comando anterior baixa e executa o script *install_lamp.sh* na VM chamada *lamp-vm*.

Como o aplicativo inclui um servidor Web, lembre-se de abrir uma porta de escuta HTTP na VM remota com o próximo comando.

    azure vm endpoint create -n Apache -o tcp lamp-vm 80 80

## <a name="monitoring-and-troubleshooting"></a>Monitoramento e solução de problemas
Você pode verificar a execução do script personalizado analisando o arquivo de log na VM remota. SSH para *lamp-vm* e parte final do arquivo de log com o próximo comando.

    cd /var/log/azure/customscript
    tail -f handler.log

Depois de executar a Extensão de Script Personalizado, você pode navegar até a página PHP que criou para obter informações. A página PHP para o exemplo neste artigo é a *http://lamp-vm.cloudapp.net/phpinfo.php*.

## <a name="additional-resources"></a>Recursos adicionais
Você pode usar as mesmas etapas básicas para implantar aplicativos mais complexos. Neste exemplo, o script de instalação foi salvo como um blob público no armazenamento do Azure. Uma opção mais segura seria armazenar o script de instalação como um blob seguro com uma [Assinatura de Acesso Seguro](https://msdn.microsoft.com/library/azure/ee395415.aspx) (SAS).

Recursos adicionais da CLI do Azure, do Linux e da Extensão de Script Personalizado são listados a seguir.

[Automatizar tarefas de personalização de VM do Linux usando a extensão CustomScript](https://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/)

[Extensões Linux do Azure (GitHub)](https://github.com/Azure/azure-linux-extensions)