---
title: Instalar & implantar o agente do Linux C
description: Saiba como instalar e implantar o agente de segurança baseado no defender para IoT C no Linux
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 8c03f6b882c8b1a64c9f256493c5d586b5fa0f89
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90933676"
---
# <a name="deploy-defender-for-iot-c-based-security-agent-for-linux"></a>Implantar o defender para o agente de segurança baseado em IoT C para Linux

Este guia explica como instalar e implantar o agente de segurança baseado no defender para IoT C no Linux.

Neste guia, você aprenderá a:

> [!div class="checklist"]
> * Instalar
> * Verificar implantação
> * Desinstalar o agente
> * Solucionar problemas

## <a name="prerequisites"></a>Pré-requisitos

Para outras plataformas e tipos de agente, consulte [escolher o agente de segurança certo](how-to-deploy-agent.md).

1. Para implantar o agente de segurança, são necessários direitos de administrador local no computador em que você deseja instalar o (sudo).

1. [Criar um módulo de segurança](quickstart-create-security-twin.md) para o dispositivo.

## <a name="installation"></a>Instalação

Para instalar e implantar o agente de segurança, use o seguinte fluxo de trabalho:

1. Baixe a versão mais recente em seu computador do [GitHub](https://aka.ms/iot-security-github-c).

1. Extraia o conteúdo do pacote e navegue até a pasta _/src/Installation_

1. Adicione permissões em execução ao **script InstallSecurityAgent** executando o seguinte comando:

   ```
   chmod +x InstallSecurityAgent.sh
   ```

1. Em seguida, execute:

   ```
   ./InstallSecurityAgent.sh -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -i
   ```

   Veja [Como configurar a autenticação](concept-security-agent-authentication-methods.md) para mais informações sobre parâmetros de autenticação.

Esse script executa a seguinte função:

1. Instala pré-requisitos.

1. Adiciona um usuário de serviço (com logon interativo desabilitado).

1. Instala o agente como um **Daemon** – pressupõe que o dispositivo usa **systemd** para gerenciamento de serviços.

1. Configura o agente com os parâmetros de autenticação fornecidos.

Para obter ajuda adicional, execute o script com o parâmetro –ajuda:

```./InstallSecurityAgent.sh --help```

### <a name="uninstall-the-agent"></a>Desinstalar o agente

Para desinstalar o agente, execute o script com o parâmetro –-Uninstall:

```./InstallSecurityAgent.sh -–uninstall```

## <a name="troubleshooting"></a>Solução de problemas

Verifique o status da implantação executando:

```systemctl status ASCIoTAgent.service```

## <a name="next-steps"></a>Próximas etapas

- Leia a [visão geral](overview.md) do serviço defender para IOT
- Saiba mais sobre a [arquitetura](architecture.md) do defender para IOT
- Habilite o [serviço](quickstart-onboard-iot-hub.md)
- Leia as [perguntas frequentes](resources-frequently-asked-questions.md)
- Entenda os [Alertas de Segurança](concept-security-alerts.md)
