---
title: Instalar & implantar o agente C# do Linux
description: Saiba como instalar e implantar o agente de segurança baseado no defender para IoT em C# no Linux
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.custom: devx-track-csharp
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: 48737831440a1402b6974955b4da61a4216b011f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90933279"
---
# <a name="deploy-defender-for-iot-c-based-security-agent-for-linux"></a>Implantar o defender para IoT C# com base no agente de segurança para Linux

Este guia explica como instalar e implantar o agente de segurança baseado em C# do defender para IoT no Linux.

Neste guia, você aprenderá a:

> [!div class="checklist"]
> * Instalar
> * Verificar implantação
> * Desinstalar o agente
> * Solucionar problemas

## <a name="prerequisites"></a>Pré-requisitos

Para outras plataformas e tipos de agente, consulte [escolher o agente de segurança certo](how-to-deploy-agent.md).

1. Para implantar o agente de segurança, são necessários direitos de administrador local no computador em que você deseja instalar.

1. [Criar um módulo de segurança](quickstart-create-security-twin.md) para o dispositivo.

## <a name="installation"></a>Instalação

Para implantar o agente de segurança, use as seguintes etapas:

1. Baixe a versão mais recente em seu computador do [GitHub](https://aka.ms/iot-security-github-cs).

1. Extraia o conteúdo do pacote e navegue até a pasta _/install_ .

1. Adicione permissões de execução para o **script InstallSecurityAgent** executando `chmod +x InstallSecurityAgent.sh`

1. Em seguida, execute o seguinte comando com **privilégios de raiz**:

   ```
   ./InstallSecurityAgent.sh -i -aui <authentication identity>  -aum <authentication method> -f <file path> -hn <host name>  -di <device id> -cl <certificate location kind>
   ```

   para obter mais informações sobre parâmetros de autenticação, consulte [como configurar a autenticação](concept-security-agent-authentication-methods.md).

O script executa as ações a seguir:

- Instala pré-requisitos.

- Adiciona um usuário de serviço (com logon interativo desabilitado).

- Instala o agente como um **daemon** -presume que o dispositivo usa o modelo de implantação clássico do **sistema** .

- Configura os **sudoers** para permitir que o agente execute determinadas tarefas como raiz.

- Configura o agente com os parâmetros de autenticação fornecidos.

Para obter ajuda adicional, execute o script com o parâmetro –ajuda: `./InstallSecurityAgent.sh --help`

### <a name="uninstall-the-agent"></a>Desinstalar o agente

Para desinstalar o agente, execute o script com o parâmetro –u: `./InstallSecurityAgent.sh -u`.

> [!NOTE]
> A desinstalação não remove todos os pré-requisitos ausentes que foram instalados durante a instalação.

## <a name="troubleshooting"></a>Solução de problemas

1. Verifique o status da implantação executando:

    `systemctl status ASCIoTAgent.service`

1. Habilitar o registro em log.
   Se o agente não conseguir iniciar, ative o log para obter mais informações.

   Ative o registro em log por:

   1. Abra o arquivo de configuração para edição em qualquer editor de Linux:

        `vi /var/ASCIoTAgent/General.config`

   1. Edite os valores a seguir:

      ```
      <add key="logLevel" value="Debug"/>
      <add key="fileLogLevel" value="Debug"/>
      <add key="diagnosticVerbosityLevel" value="Some" />
      <add key="logFilePath" value="IotAgentLog.log"/>
      ```

       O valor **logFilePath** é configurável.

       > [!NOTE]
       > É recomendável deixar o registro em log **desativado** após a conclusão da solução de problemas. Deixar o log **ativado** aumenta o tamanho do arquivo de log e o uso de dados.

   1. Reinicie o agente executando:

       `systemctl restart ASCIoTAgent.service`

   1. Veja o arquivo de log para obter mais informações sobre a falha.

       O local do arquivo de log é: `/var/ASCIoTAgent/IotAgentLog.log`

       Altere o caminho do local do arquivo de acordo com o nome que você escolheu para o **logFilePath** na etapa 2.

## <a name="next-steps"></a>Próximas etapas

- Leia a [visão geral](overview.md) do serviço defender para IOT
- Saiba mais sobre a [arquitetura](architecture.md) do defender para IOT
- Habilite o [serviço](quickstart-onboard-iot-hub.md)
- Leia as [perguntas frequentes](resources-frequently-asked-questions.md)
- Entenda os [alertas](concept-security-alerts.md)
