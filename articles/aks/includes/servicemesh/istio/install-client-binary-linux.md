---
author: paulbouwer
ms.topic: include
ms.date: 10/02/2020
ms.author: pabouwer
ms.openlocfilehash: 164844a9da09563f8fbefe7ec60aff7eb05ace2d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91666684"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>Baixar e instalar o binário do cliente İSTİO istioctl

Em um shell baseado em bash no [subsistema Linux ou Windows para Linux][install-wsl], use `curl` para baixar a versão İSTİO e, em seguida, extrair com `tar` da seguinte maneira:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.7.3

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istioctl-$ISTIO_VERSION-linux-amd64.tar.gz" | tar xz
```

O `istioctl` binário do cliente é executado no computador cliente e permite que você instale e gerencie İSTİO em seu cluster do AKS. Use os seguintes comandos para instalar o binário de cliente do Istio `istioctl` em um shell baseado em bash no Linux ou [Subsistema do Windows para Linux][install-wsl]. Esses comandos copiam o `istioctl` binário de cliente para o local do programa do usuário padrão no `PATH`.

```bash
sudo mv ./istioctl /usr/local/bin/istioctl
sudo chmod +x /usr/local/bin/istioctl
```

Se você quiser concluir a linha de comando do binário de cliente do Istio `istioctl`, configure-o conforme a seguir:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```

<!-- LINKS - external -->
[install-wsl]: /windows/wsl/install-win10
