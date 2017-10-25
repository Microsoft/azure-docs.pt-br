---
title: Instalar a CLI do Azure 1.0 | Microsoft Docs
description: "Instalar a CLI do Azure 1.0 para Mac, Linux e Windows para começar a usar os serviços do Azure"
editor: 
manager: timlt
documentationcenter: 
author: squillace
services: virtual-machines-linux,virtual-network,storage,azure-resource-manager
tags: azure-resource-manager,azure-service-management
ms.assetid: bdb776c8-7a76-4f3a-887c-236b4fffee10
ms.service: multiple
ms.workload: multiple
ms.tgt_pltfrm: command-line-interface
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: rasquill
ms.openlocfilehash: 63b35ed25b809a16b61b685fd35aa67474b0a369
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="install-the-azure-cli-10"></a>Instalar a CLI do Azure 1.0
> [!div class="op_single_selector"]
> * [PowerShell](/powershell/azure/overview)
> * [CLI 1.0 do Azure](cli-install-nodejs.md)
> * [CLI 2.0 do Azure](/cli/azure/install-azure-cli)

> [!IMPORTANT]
> Este tópico descreve como instalar a CLI do Azure 1.0, que se baseia no nodeJs e dá suporte a todas as chamadas à API de implantação clássica, bem como uma variedade de atividades de implantação do Resource Manager. É necessário usar a [CLI do Azure 2.0](/cli/azure/overview) para implantações e gerenciamento novos ou prospectivos da CLI.

Instale rapidamente a CLI (Interface de Linha de Comando) do Azure 1.0 para usar um conjunto de comandos de software livre baseados em shell para criar e gerenciar recursos no Microsoft Azure. Você tem várias opções para instalar essas ferramentas de plataforma cruzada em seu computador:

* **pacote npm** – Execute o npm (o gerenciador de pacotes para JavaScript) para instalar o último pacote da CLI do Azure 1.0 na distribuição Linux ou no sistema operacional. Exige o node.js e o npm em seu computador.
* **Installer** – Baixe um instalador para facilitar a instalação no Mac ou Windows.
* **Contêiner do Docker** – Comece a usar a CLI mais recente em um contêiner do Docker pronto para ser executado. Exige um host do Docker em seu computador.

Para obter mais opções e um histórico, consulte o repositório do projeto no [GitHub](https://github.com/azure/azure-xplat-cli).

Depois que a CLI do Azure 1.0 for instalada, [conecte-a à sua assinatura do Azure](xplat-cli-connect.md) e execute os comandos **azure** na interface de linha de comando (Bash, Terminal, Prompt de comando e assim por diante) para trabalhar com os recursos do Azure.

## <a name="option-1-install-an-npm-package"></a>Opção 1: Instalar um pacote npm
Para instalar a CLI de um pacote npm, você precisa baixar e instalar os [Node.js e o npm mais recentes](https://nodejs.org/en/download/package-manager/). Em seguida, execute **npm install** para instalar o pacote azure-cli:

```bash
npm install -g azure-cli
```

Em distribuições Linux, talvez você precise usar **sudo** para executar o comando **npm** com êxito, como segue:

```bash
sudo npm install -g azure-cli
```

> [!NOTE]
> Se precisar instalar ou atualizar o Node.js e o npm em sua distribuição ou SO do Linux, é recomendável que você instale a versão mais recente do Node.js LTS (4. x). Se você usar uma versão mais antiga, poderá obter erros de instalação.

Se preferir, baixe o [arquivo tar][linux-installer] do Linux mais recente para o pacote npm localmente. Em seguida, instale o pacote npm baixado da seguinte maneira (em distribuições Linux, talvez seja necessário usar **sudo**):

```bash
npm install -g <path to downloaded tar file>
```

## <a name="option-2-use-an-installer"></a>Opção 2: Usar um instalador
Se você usar um computador com Windows ou Mac, os instaladores de CLI a seguir estarão disponíveis para download:

* [Instalador do Mac OS X][mac-installer]
* [Windows MSI][windows-installer]

> [!TIP]
> No Windows, você também pode baixar o [Web Platform Installer](https://go.microsoft.com/?linkid=9828653) para instalar a CLI. Esse instalador lhe dá a opção de instalar o SDK do Azure e ferramentas de linha de comando adicionais após a instalação da CLI.

## <a name="option-3-use-a-docker-container"></a>Opção 3: Usar um contêiner do Docker
Se você tiver configurado o computador como um host do [Docker](https://docs.docker.com/engine/understanding-docker/), poderá executar a última CLI do Azure 1.0 em um contêiner do Docker. Execute o seguinte comando (em distribuições Linux, talvez você precise usar **sudo**):

```bash
docker run -it microsoft/azure-cli
```

## <a name="run-azure-cli-10-commands"></a>Executar comandos da CLI do Azure 1.0
Depois que a CLI do Azure 1.0 for instalada, execute o comando **azure** na interface do usuário de linha de comando (Bash, Terminal, Prompt de comando e assim por diante). Por exemplo, para executar o comando de ajuda, digite o seguinte:

```azurecli
azure help
```

> [!NOTE]
> Em algumas distribuições Linux, você poderá receber um erro semelhante a `/usr/bin/env: ‘node’: No such file or directory`. Esse erro ocorre quando instalações recentes do Node.js são instaladas em /usr/bin/nodejs. Para corrigi-lo, crie um link simbólico para /usr/bin/node executando este comando:

```bash
sudo ln -s /usr/bin/nodejs /usr/bin/node
```

Para ver a versão da CLI do Azure 1.0 instalada, digite o seguinte:

```azurecli
azure --version
```

Agora você está pronto! Para acessar todos os comandos da CLI para trabalhar com seus próprios recursos, [conecte-se à sua assinatura do Azure por meio da CLI do Azure](xplat-cli-connect.md).

> [!NOTE]
> Ao usar a CLI do Azure pela primeira vez, você verá uma mensagem perguntando se deseja permitir que a Microsoft colete informações de uso. A participação é voluntária. Se optar por participar, você poderá parar a qualquer momento executando `azure telemetry --disable`. Para habilitar a participação a qualquer momento, execute `azure telemetry --enable`.

## <a name="update-the-cli"></a>Atualizar a CLI
Com frequência, a Microsoft lança versões atualizadas da CLI do Azure. Reinstale a CLI usando o instalador do seu sistema operacional ou execute o contêiner do Docker mais recente. Ou, se tiver o Node.js e o npm mais recentes instalados, atualize-os digitando o seguinte (em distribuições Linux, talvez você precise usar **sudo**).

```bash
npm update -g azure-cli
```

## <a name="enable-tab-completion"></a>Ativar o recurso auto-completar com TAB
Há suporte para o recurso auto-completar de comandos da CLI para Mac e Linux.

Para habilitá-lo no zsh, execute:

```bash
echo '. <(azure --completion)' >> .zshrc
```

Para habilitá-lo no bash, execute:

```bash
azure --completion >> ~/azure.completion.sh
echo 'source ~/azure.completion.sh' >> ~/.bash_profile
```


## <a name="next-steps"></a>Próximas etapas
* [Conecte-se da CLI à sua assinatura do Azure](xplat-cli-connect.md) para criar e gerenciar recursos do Azure.
* Para saber mais sobre a CLI do Azure, baixar o código-fonte, relatar problemas ou colaborar com o projeto, visite o [Repositório GitHub para a CLI do Azure](https://github.com/azure/azure-xplat-cli).
* Se tiver dúvidas quanto ao uso da CLI do Azure ou do Azure, visite os [Fóruns do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurescripting).


[mac-installer]: http://aka.ms/mac-azure-cli
[windows-installer]: http://aka.ms/webpi-azure-cli
[linux-installer]: http://aka.ms/linux-azure-cli
[cliasm]: /cli/azure/get-started-with-az-cli2
[cliarm]: ./virtual-machines/azure-cli-arm-commands.md
