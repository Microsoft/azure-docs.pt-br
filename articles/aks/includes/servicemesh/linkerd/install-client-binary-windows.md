---
author: paulbouwer
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 1a023475de1ce2891916807632d9ee15e382326c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "81736077"
---
## <a name="download-and-install-the-linkerd-linkerd-client-binary"></a>Baixar e instalar o binário do cliente Linkerd Linkerd

Em um shell baseado no PowerShell no Windows, use `Invoke-WebRequest` para baixar a versão Linkerd da seguinte maneira:

```powershell
# Specify the Linkerd version that will be leveraged throughout these instructions
$LINKERD_VERSION="stable-2.6.0"

# Enforce TLS 1.2
[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-windows.exe" -OutFile "linkerd2-cli-$LINKERD_VERSION-windows.exe"
```

O `linkerd` binário do cliente é executado no computador cliente e permite que você interaja com a malha do serviço Linkerd. Use os comandos a seguir para instalar o `linkerd` binário do cliente Linkerd em um shell baseado no PowerShell no Windows. Esses comandos copiam o `linkerd` binário do cliente para uma pasta Linkerd e, em seguida, o disponibilizam imediatamente (no shell atual) e permanentemente (através de reinicializações do Shell) por meio de seu `PATH` . Você não precisa de privilégios elevados (administrador) para executar esses comandos e não precisa reiniciar o Shell.

```powershell
# Copy linkerd.exe to C:\Linkerd
New-Item -ItemType Directory -Force -Path "C:\Linkerd"
Copy-Item -Path ".\linkerd2-cli-$LINKERD_VERSION-windows.exe" -Destination "C:\Linkerd\linkerd.exe"

# Add C:\Linkerd to PATH. 
# Make the new PATH permanently available for the current User
$USER_PATH = [environment]::GetEnvironmentVariable("PATH", "User") + ";C:\Linkerd\"
[environment]::SetEnvironmentVariable("PATH", $USER_PATH, "User")
# Make the new PATH immediately available in the current shell
$env:PATH += ";C:\Linkerd\"
```