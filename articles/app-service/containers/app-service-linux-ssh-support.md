---
title: Suporte de SSH para o Serviço de Aplicativo no Linux – Azure | Microsoft Docs
description: Saiba como usar SSH com o Serviço de Aplicativo do Azure no Linux.
keywords: serviço de aplicativo do azure, aplicativo web, linux, oss
services: app-service
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: 66f9988f-8ffa-414a-9137-3a9b15a5573c
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/25/2019
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: b54d5003f67a1bd79e1e52eef87df858bc68ade1
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67551906"
---
# <a name="ssh-support-for-azure-app-service-on-linux"></a>Suporte de SSH para o Serviço de Aplicativo do Azure no Linux

[Secure Shell (SSH)](https://wikipedia.org/wiki/Secure_Shell) normalmente é usado para executar comandos administrativos remotamente a partir de um terminal de linha de comando. O Serviço de Aplicativo no Linux fornece suporte de SSH no contêiner de aplicativo com cada uma das imagens internas do Docker usadas para a Pilha em Tempo de Execução de novos aplicativos Web. 

![Pilhas de tempo de execução](./media/app-service-linux-ssh-support/app-service-linux-runtime-stack.png)

Para imagens personalizadas de Docker, ao configurar o servidor SSH em sua imagem personalizada.

Você também pode se conectar ao contêiner diretamente do seu computador de desenvolvimento local usando o SSH e SFTP.

## <a name="open-ssh-session-in-browser"></a>Abra a sessão SSH aberta no navegador

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-no-h.md)]

## <a name="use-ssh-support-with-custom-docker-images"></a>Use o suporte de SSH com imagens personalizadas do Docker

Ver [Configurar SSH em um contêiner personalizado](configure-custom-container.md#enable-ssh).

## <a name="open-ssh-session-from-remote-shell"></a>Abrir sessão SSH de shell remoto

> [!NOTE]
> Esse recurso está atualmente em versão prévia.
>

Usando o túnel TCP, você pode criar uma conexão de rede entre o computador de desenvolvimento e o Aplicativo Web para contêineres em uma conexão autenticada do WebSocket. Isso permite que você abra uma sessão SSH com o contêiner em execução no serviço de Serviço de Aplicativo do Azure do cliente de sua escolha.

Para começar, você precisa instalar a [CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest). Para ver como funciona sem instalar a CLI do Azure, abra [Azure Cloud Shell](../../cloud-shell/overview.md). 

Abra uma conexão remota para seu aplicativo usando o comando [criar az webapp remoto-conexão](/cli/azure/ext/webapp/webapp/remote-connection?view=azure-cli-latest#ext-webapp-az-webapp-remote-connection-create). Especificar  _\<id da assinatura >_ ,  _\<nome do grupo >_ e \_ \<app-name > _ para seu aplicativo.

```azurecli-interactive
az webapp create-remote-connection --subscription <subscription-id> --resource-group <resource-group-name> -n <app-name> &
```

> [!TIP]
> `&` no final do comando é apenas para conveniência, se você estiver usando o Azure Cloud Shell. Executa o processo em segundo plano para que você possa executar o próximo comando no mesmo shell.

A saída do comando fornece as informações que você precisa para abrir uma sessão SSH.

```
Port 21382 is open
SSH is available { username: root, password: Docker! }
Start your favorite client and connect to port 21382
```

Abra uma sessão SSH com o contêiner com o cliente de sua escolha, usando a porta local. O exemplo a seguir usa o comando padrão [ssh](https://ss64.com/bash/ssh.html):

```azurecli-interactive
ssh root@127.0.0.1 -p <port>
```

Ao ser solicitado, digite `yes` para continuar a se conectar. Você receberá uma solicitação de senha. Use `Docker!`, que foi mostrado anteriormente.

```
Warning: Permanently added '[127.0.0.1]:21382' (ECDSA) to the list of known hosts.
root@127.0.0.1's password:
```

Ao autenticar, você deverá ver a tela de boas-vindas da sessão.

```
  _____
  /  _  \ __________ _________   ____
 /  /_\  \___   /  |  \_  __ \_/ __ \
/    |    \/    /|  |  /|  | \/\  ___/
\____|__  /_____ \____/ |__|    \___  >
        \/      \/                  \/
A P P   S E R V I C E   O N   L I N U X

0e690efa93e2:~#
```

Agora você está conectado a seu conector.  

Tente executar o comando novamente [top](https://ss64.com/bash/top.html). Você deve ser capaz de ver o processo do aplicativo na lista de processos. No exemplo abaixo, é aquele com `PID 263`.

```
Mem: 1578756K used, 127032K free, 8744K shrd, 201592K buff, 341348K cached
CPU:   3% usr   3% sys   0% nic  92% idle   0% io   0% irq   0% sirq
Load average: 0.07 0.04 0.08 4/765 45738
  PID  PPID USER     STAT   VSZ %VSZ CPU %CPU COMMAND
    1     0 root     S     1528   0%   0   0% /sbin/init
  235     1 root     S     632m  38%   0   0% PM2 v2.10.3: God Daemon (/root/.pm2)
  263   235 root     S     630m  38%   0   0% node /home/site/wwwroot/app.js
  482   291 root     S     7368   0%   0   0% sshd: root@pts/0
45513   291 root     S     7356   0%   0   0% sshd: root@pts/1
  291     1 root     S     7324   0%   0   0% /usr/sbin/sshd
  490   482 root     S     1540   0%   0   0% -ash
45539 45513 root     S     1540   0%   0   0% -ash
45678 45539 root     R     1536   0%   0   0% top
45733     1 root     Z        0   0%   0   0% [init]
45734     1 root     Z        0   0%   0   0% [init]
45735     1 root     Z        0   0%   0   0% [init]
45736     1 root     Z        0   0%   0   0% [init]
45737     1 root     Z        0   0%   0   0% [init]
45738     1 root     Z        0   0%   0   0% [init]
```

## <a name="next-steps"></a>Próximas etapas

Você pode postar perguntas e problemas no [fórum do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

Para obter mais informações sobre o aplicativo Web para contêineres, consulte:

* [Introduzindo a depuração remota de aplicativos Node.js no Serviço de Aplicativo do Azure de aplicativo do Azure do código VS](https://medium.com/@auchenberg/introducing-remote-debugging-of-node-js-apps-on-azure-app-service-from-vs-code-in-public-preview-9b8d83a6e1f0)
* [Como usar uma imagem personalizada do Docker para o Aplicativo Web para Contêineres](quickstart-docker-go.md)
* [Usando o .NET Core no Serviço de Aplicativo do Azure no Linux](quickstart-dotnetcore.md)
* [Usando o Ruby no Serviço de Aplicativo do Azure no Linux](quickstart-ruby.md)
* [Perguntas frequentes sobre o Aplicativo Web para Contêineres do Serviço de Aplicativo do Azure](app-service-linux-faq.md)
