---
title: Baixar o SDK do Azure para PHP
description: Saiba como baixar e instalar o SDK do Azure para PHP.
documentationcenter: php
services: app-service\web
author: allclark
manager: douge
editor: ''
ms.assetid: bac355ac-4c25-42f4-8273-c5112eafa8d4
ms.service: app-service-web
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 06/01/2016
ms.author: yaya
ms.openlocfilehash: 9dd336ef973b603108aad62c90e4dab385d20317
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67873893"
---
# <a name="download-the-azure-sdk-for-php"></a>Baixar o SDK do Azure para PHP

## <a name="overview"></a>Visão geral

O SDK do Azure para o Python inclui componentes que permitem que você desenvolva, implante e gerencie aplicativos do PHP para o Azure. O SDK do Azure para o PHP inclui especificamente o seguinte:

* **As bibliotecas de cliente PHP para Azure**. Essas bibliotecas de classe fornecem uma interface para acessar recursos do Azure, como serviços de gerenciamento de dados e serviços de nuvem.
* **A Interface de Linha de Comando do Azure para Mac, Linux e Windows (Azure CLI)** . Esse é um conjunto de ferramentas de linha de comando para implantar e gerenciar serviços do Azure, como os Sites do Azure e Máquinas Virtuais do Azure. O CLI do Azure em qualquer plataforma, incluindo Mac, Linux e Windows.
* **PowerShell para Azure (somente Windows)** . Esse é um conjunto de cmdlets do PowerShell para implantar e gerenciar serviços do Microsoft Azure, como Serviços de Nuvem e Máquinas Virtuais.
* **Os Emuladores do Azure (somente Windows)** . Os emuladores de computação e armazenamento são emuladores locais de serviços de nuvem e serviços de gerenciamento de dados que permitem que você teste um aplicativo localmente. Os Emuladores do Azure são executados somente no Windows.

As seções a seguir descrevem como baixar e instalar os componentes descritos acima.

As instruções neste tópico pressupõem que você tenha o [php][install-php] instalado.

> [!NOTE]
> Você deve ter o PHP 5.5 ou superior para usar as bibliotecas de cliente PHP para Azure.
>
>

## <a name="php-client-libraries-for-azure"></a>As bibliotecas de cliente PHP para Azure.

As Bibliotecas de Cliente PHP para Azure fornecem uma interface para acessar recursos do Azure, como serviços de gerenciamento de dados e serviços de nuvem, de qualquer sistema operacional. Essas bibliotecas podem ser instaladas por meio do Compositor.

Para obter informações sobre como usar as bibliotecas de cliente php para o Azure, consulte [como usar o serviço][blob-service] , [How to Use the Table Service][table-service] BLOB e [como usar o]serviço fila[fila-serviço].

### <a name="install-via-composer"></a>Instalar por meio do Composer

1. [Instalar o Git][install-git]. No Windows, também será necessário adicionar o Git executável à variável de ambiente PATH.

2. Crie um arquivo chamado **composer.json** na raiz do seu projeto e adicione o seguinte código a ele:

        {
            "require": {
                "microsoft/windowsazure": "^0.4"
            }
        }

3. Baixe o **[Composer. Phar][composer-phar]** na raiz do projeto.

4. Abra um prompt de comando e execute esta função na raiz do projeto

        php composer.phar install

## <a name="azure-powershell-and-azure-emulators"></a>O PowerShell do Azure e o Emuladores do Azure.

O PowerShell do Azure é um conjunto de cmdlets do PowerShell para implantar e gerenciar serviços do Azure (como Serviços de Nuvem e Máquinas Virtuais). Os Emuladores do Azure são emuladores de serviços de nuvem e serviços de gerenciamento de dados que permitem que você teste um aplicativo localmente. Esses componentes recebem suporte apenas no Windows.

A maneira recomendada para instalar Azure PowerShell e os emuladores do Azure é usar o [Microsoft Web Platform Installer][download-wpi]. Observe que você também pode optar por instalar outros componentes de desenvolvimento, como PHP, SQL Server, os Drivers da Microsoft do SQL Server para PHP e WebMatrix.

Para obter informações sobre como usar Azure PowerShell, consulte [como usar Azure PowerShell][powershell-tools].

## <a name="azure-cli"></a>CLI do Azure

O CLI do Azure é um conjunto de ferramentas de linha de comando para implantar e gerenciar serviços do Azure, como os Sites do Azure e Máquinas Virtuais do Azure. Para obter informações sobre como instalar a CLI do Azure, consulte [Instalar a CLI do Azure](cli-install-nodejs.md).

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte o [Centro de Desenvolvimento PHP](https://azure.microsoft.com/develop/php/).

[install-php]: https://www.php.net/manual/en/install.php
[composer-github]: https://github.com/composer/composer
[composer-phar]: https://getcomposer.org/composer.phar
[nodejs-org]: https://nodejs.org/
[install-node-linux]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager
[download-wpi]: https://go.microsoft.com/fwlink/?LinkId=253447
[mac-installer]: https://go.microsoft.com/fwlink/?LinkId=252249
[blob-service]: https://go.microsoft.com/fwlink/?LinkId=252714
[table-service]: https://go.microsoft.com/fwlink/?LinkId=252715
[queue-service]: https://go.microsoft.com/fwlink/?LinkId=252716
[azure cli]: https://go.microsoft.com/fwlink/?LinkId=252717
[powershell-tools]: https://go.microsoft.com/fwlink/?LinkId=252718
[php-sdk-github]: https://go.microsoft.com/fwlink/?LinkId=252719
[install-git]: https://git-scm.com/book/en/Getting-Started-Installing-Git
