---
title: "Implantar LAMP em uma máquina virtual do Linux no Azure | Microsoft Docs"
description: "Tutorial – instalar a pilha LAMP em uma VM do Linux no Azure"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 6c12603a-e391-4d3e-acce-442dd7ebb2fe
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 08/03/2017
ms.author: danlep
ms.openlocfilehash: c00e6a190633348411f47490808739d570cafd69
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="install-a-lamp-web-server-on-an-azure-vm"></a>Instalar um servidor Web LAMP em uma VM do Azure
Este artigo explica como implantar um servidor Web Apache, MySQL e PHP (a pilha LAMP) em uma VM do Ubuntu no Azure. Se você prefere o servidor Web NGINX, consulte o tutorial da [Pilha LEMP](tutorial-lemp-stack.md). Para ver o servidor LAMP em ação, opcionalmente, você pode instalar e configurar um site de WordPress. Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Criar uma VM Ubuntu (o "L" na pilha de LAMP)
> * Abra a porta 80 para tráfego da Web
> * Instalar Apache, MySQL e PHP
> * Verificar a instalação e a configuração
> * Instalar o WordPress no servidor LAMP


Para saber mais sobre a pilha LAMP, incluindo recomendações para um ambiente de produção, consulte a [Documentação do Ubuntu](https://help.ubuntu.com/community/ApacheMySQLPHP).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este tutorial exigirá que você execute a CLI do Azure versão 2.0.4 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure]( /cli/azure/install-azure-cli). 

[!INCLUDE [virtual-machines-linux-tutorial-stack-intro.md](../../../includes/virtual-machines-linux-tutorial-stack-intro.md)]

## <a name="install-apache-mysql-and-php"></a>Instalar Apache, MySQL e PHP

Execute o seguinte comando para atualizar as fontes de pacote do Ubuntu e instalar o Apache, o MySQL e o PHP. Observe o acento circunflexo (^) no final do comando.


```bash
sudo apt update && sudo apt install lamp-server^
```



Você será solicitado a instalar os pacotes e outras dependências. Quando solicitado, defina uma senha raiz para o MySQL e, em seguida, pressione [Enter] para continuar. Avance pelas solicitações restantes. Esse processo instala as extensões PHP mínimas obrigatórias e necessárias para usar o PHP com o MySQL. 

![Página de senha raiz do MySQL][1]

## <a name="verify-installation-and-configuration"></a>Verificar a instalação e a configuração


### <a name="apache"></a>Apache

Verifique a versão do Apache com o seguinte comando:
```bash
apache2 -v
```

Com o Apache instalado e a porta 80 aberta para a sua VM, o servidor Web agora pode ser acessado por meio da Internet. Para exibir a página padrão do Apache2 Ubuntu, abra um navegador da Web e digite o endereço IP público da VM. Insira o endereço IP público que você usou para o SSH para a VM:

![Página padrão do Apache][3]


### <a name="mysql"></a>MySQL

Verifique a versão do MySQL com o seguinte comando (observe o parâmetro `V` em letra maiúscula):

```bash
mysql -V
```

É recomendável executar o seguinte script para ajudar a proteger a instalação do MySQL:

```bash
mysql_secure_installation
```

Digite a senha raiz do MySQL e defina as configurações de segurança do seu ambiente.

Se você deseja criar um banco de dados MySQL, adicionar usuários ou alterar definições de configuração, faça logon no MySQL:

```bash
mysql -u root -p
```

Quando terminar, saia do prompt do MySQL digitando `\q`.

### <a name="php"></a>PHP

Verifique a versão do PHP com o seguinte comando:

```bash
php -v
```

Se você deseja realizar mais testes, crie uma página de informações rápida de PHP para exibição em um navegador. O comando a seguir cria a página de informações de PHP:

```bash
sudo sh -c 'echo "<?php phpinfo(); ?>" > /var/www/html/info.php'
```

Agora você pode verificar a página de informações de PHP que você criou. Abra um navegador e acesse `http://yourPublicIPAddress/info.php`. Substitua o endereço IP público de sua VM. A página deve ser semelhante a esta imagem.

![Página de informações de PHP][2]

[!INCLUDE [virtual-machines-linux-tutorial-wordpress.md](../../../includes/virtual-machines-linux-tutorial-wordpress.md)]


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você implantou um servidor LAMP no Azure. Você aprendeu como:

> [!div class="checklist"]
> * Criar uma VM do Ubuntu
> * Abra a porta 80 para tráfego da Web
> * Instalar Apache, MySQL e PHP
> * Verificar a instalação e a configuração
> * Instalar o WordPress no servidor LAMP

Vá para o próximo tutorial para saber como proteger servidores Web com certificados SSL.

> [!div class="nextstepaction"]
> [Proteger servidor Web com SSL](tutorial-secure-web-server.md)

[1]: ./media/tutorial-lamp-stack/configmysqlpassword-small.png
[2]: ./media/tutorial-lamp-stack/phpsuccesspage.png
[3]: ./media/tutorial-lamp-stack/apachesuccesspage.png