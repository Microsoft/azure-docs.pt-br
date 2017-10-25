---
title: Baixar o SDK do Azure para PHP
description: Saiba como baixar e instalar o SDK do Azure para PHP.
documentationcenter: php
services: app-service\web
author: allclark
manager: douge
editor: 
ms.assetid: bac355ac-4c25-42f4-8273-c5112eafa8d4
ms.service: app-service-web
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 06/01/2016
ms.author: allclark;yaqiyang
ms.openlocfilehash: fd3d28b133ef8e646f5c2f1c1127f654daa61b95
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="download-the-azure-sdk-for-php"></a>Baixar o SDK do Azure para PHP
## <a name="overview"></a>Visão geral
O SDK do Azure para o Python inclui componentes que permitem que você desenvolva, implante e gerencie aplicativos do PHP para o Azure. O SDK do Azure para o PHP inclui especificamente o seguinte:

* **As bibliotecas de cliente PHP para Azure**. Essas bibliotecas de classe fornecem uma interface para acessar recursos do Azure, como serviços de gerenciamento de dados e serviços de nuvem.  
* **A Interface de Linha de Comando do Azure para Mac, Linux e Windows (Azure CLI)**. Esse é um conjunto de ferramentas de linha de comando para implantar e gerenciar serviços do Azure, como os Sites do Azure e Máquinas Virtuais do Azure. O CLI do Azure em qualquer plataforma, incluindo Mac, Linux e Windows.
* **PowerShell para Azure (somente Windows)**. Esse é um conjunto de cmdlets do PowerShell para implantar e gerenciar serviços do Microsoft Azure, como Serviços de Nuvem e Máquinas Virtuais.
* **Os Emuladores do Azure (somente Windows)**. Os emuladores de computação e armazenamento são emuladores locais de serviços de nuvem e serviços de gerenciamento de dados que permitem que você teste um aplicativo localmente. Os Emuladores do Azure são executados somente no Windows.

As seções a seguir descrevem como baixar e instalar os componentes descritos acima.

As instruções descritas neste tópico pressupõem que você tenha o [PHP][install-php] instalado.

> [!NOTE]
> Você deve ter o PHP 5.5 ou superior para usar as bibliotecas de cliente PHP para Azure.
> 
> 

## <a name="php-client-libraries-for-azure"></a>As bibliotecas de cliente PHP para Azure.
As Bibliotecas de Cliente PHP para Azure fornecem uma interface para acessar recursos do Azure, como serviços de gerenciamento de dados e serviços de nuvem, de qualquer sistema operacional. Essas bibliotecas podem ser instaladas por meio do Compositor.

Para obter informações sobre como usar as Bibliotecas de Cliente do PHP para Azure, consulte [Como usar o serviço Blob][blob-service], [Como usar o serviço Tabela][table-service] e [Como usar o serviço Fila][queue-service].

### <a name="install-via-composer"></a>Instalar por meio do Composer
1. [Instale o Git][install-git].

    > [AZURE.NOTE] No Windows, também será necessário adicionar o Git executável à variável de ambiente PATH.

1. Crie um arquivo chamado **composer.json** na raiz do seu projeto e adicione o seguinte código a ele:
   
        {
            "require": {
                "microsoft/windowsazure": "^0.4"
            }
        }
2. Baixe **[composer.phar][composer-phar]** na raiz do projeto.
3. Abra um prompt de comando e execute esta função na raiz do projeto
   
        php composer.phar install

## <a name="azure-powershell-and-azure-emulators"></a>O PowerShell do Azure e o Emuladores do Azure.
O PowerShell do Azure é um conjunto de cmdlets do PowerShell para implantar e gerenciar serviços do Azure (como Serviços de Nuvem e Máquinas Virtuais). Os Emuladores do Azure são emuladores de serviços de nuvem e serviços de gerenciamento de dados que permitem que você teste um aplicativo localmente. Esses componentes recebem suporte apenas no Windows.

A maneira recomendada de instalar o Azure PowerShell e os Emuladores do Azure é usar o [Microsoft Web Platform Installer][download-wpi]. Observe que você também pode optar por instalar outros componentes de desenvolvimento, como PHP, SQL Server, os Drivers da Microsoft do SQL Server para PHP e WebMatrix.

Para obter informações sobre como usar o Azure PowerShell, consulte [Como usar o Azure PowerShell][powershell-tools].

## <a name="azure-cli"></a>CLI do Azure
O CLI do Azure é um conjunto de ferramentas de linha de comando para implantar e gerenciar serviços do Azure, como os Sites do Azure e Máquinas Virtuais do Azure. Para obter informações sobre como instalar a CLI do Azure, consulte [Instalar a CLI do Azure](cli-install-nodejs.md).

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações, consulte o [Centro de Desenvolvimento PHP](/develop/php/).

[install-php]: http://www.php.net/manual/en/install.php
[composer-github]: https://github.com/composer/composer
[composer-phar]: http://getcomposer.org/composer.phar
[nodejs-org]: http://nodejs.org/
[install-node-linux]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager
[download-wpi]: http://go.microsoft.com/fwlink/?LinkId=253447
[mac-installer]: http://go.microsoft.com/fwlink/?LinkId=252249
[blob-service]: http://go.microsoft.com/fwlink/?LinkId=252714
[table-service]: http://go.microsoft.com/fwlink/?LinkId=252715
[queue-service]: http://go.microsoft.com/fwlink/?LinkId=252716
[azure cli]: http://go.microsoft.com/fwlink/?LinkId=252717
[powershell-tools]: http://go.microsoft.com/fwlink/?LinkId=252718
[php-sdk-github]: http://go.microsoft.com/fwlink/?LinkId=252719
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
