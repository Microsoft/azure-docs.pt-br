---
author: paulbouwer
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 876e05d7b18ac193edbc9cf842ea2c1bf0555d54
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "77593694"
---
## <a name="download-and-install-the-linkerd-linkerd-client-binary"></a>Baixar e instalar o binário do cliente Linkerd Linkerd

Em um shell baseado em bash no MacOS, use `curl` para baixar a versão Linkerd da seguinte maneira:

```bash
# Specify the Linkerd version that will be leveraged throughout these instructions
LINKERD_VERSION=stable-2.6.0

curl -sLO "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-darwin"
```

O `linkerd` binário do cliente é executado no computador cliente e permite que você interaja com a malha do serviço Linkerd. Use os comandos a seguir para instalar o `linkerd` binário do cliente Linkerd em um shell baseado em bash no MacOS. Esses comandos copiam o `linkerd` binário de cliente para o local do programa do usuário padrão no `PATH`.

```bash
sudo cp ./linkerd2-cli-$LINKERD_VERSION-darwin /usr/local/bin/linkerd
sudo chmod +x /usr/local/bin/linkerd
```

Se você quiser a conclusão da linha de comando para o `linkerd` binário do cliente Linkerd, configure-o da seguinte maneira:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && linkerd completion bash > ~/completions/linkerd.bash
source ~/completions/linkerd.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/linkerd.bash" >> ~/.bashrc
```
