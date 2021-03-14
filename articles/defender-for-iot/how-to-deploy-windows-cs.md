---
title: Instalar o agente C# no dispositivo Windows
description: Saiba mais sobre como instalar o defender para agente de IoT em dispositivos Windows de 32 bits ou 64 bits.
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
ms.openlocfilehash: a4223dbc913b7b46cf73c0ed99f607fff67b7e67
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2021
ms.locfileid: "102448057"
---
# <a name="deploy-a-defender-for-iot-c-based-security-agent-for-windows"></a>Implantar um agente de segurança baseado no defender para IoT em C# para Windows

Este guia explica como instalar o agente de segurança baseado no defender para IoT em C# no Windows.

Neste guia, você aprenderá a:

- Instalar
- Verificar a implantação
- Desinstalar o agente
- Solucionar problemas

## <a name="prerequisites"></a>Pré-requisitos

Para outras plataformas e tipos de agente, consulte [escolher o agente de segurança certo](how-to-deploy-agent.md).

1. Direitos de administrador local no computador em que você deseja instalar.

1. [Criar um módulo de segurança](quickstart-create-security-twin.md) para o dispositivo.

## <a name="installation"></a>Instalação

Para instalar o agente de segurança, use o seguinte fluxo de trabalho:

1. Instale o agente do defender para IoT Windows C# no dispositivo. Baixe a versão mais recente em seu computador no [repositório do GitHub](https://github.com/Azure/Azure-IoT-Security-Agent-CS)do defender para IOT.

1. Extraia o conteúdo do pacote e navegue até a pasta /Install.

1. Abra o Windows PowerShell como administrador.
1. Adicione permissões em execução ao script InstallSecurityAgent executando:

    ```
    Unblock-File .\InstallSecurityAgent.ps1
    ```

    em seguida, execute:

    ```
    .\InstallSecurityAgent.ps1 -Install -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -cl <certificate location kind>
    ```

    Por exemplo:

    ```
    .\InstallSecurityAgent.ps1 -Install -aui Device -aum SymmetricKey -f c:\Temp\Key.txt -hn MyIotHub.azure-devices.net -di Mydevice1 -cl store
    ```

    Para obter mais informações sobre parâmetros de autenticação, consulte [como configurar a autenticação](concept-security-agent-authentication-methods.md).

Esse script executa as seguintes ações:

* Instala pré-requisitos.
* Adiciona um usuário de serviço (com logon interativo desabilitado).
* Instala o agente como um **Serviço do Sistema**.
* Configura o agente com os parâmetros de autenticação fornecidos.

Para obter ajuda extra, use o comando Get-Help no PowerShell.

Exemplo de Get-Help:    ```Get-Help .\InstallSecurityAgent.ps1```

### <a name="verify-deployment-status"></a>Verificar o status da implantação

Verifique o status da implantação do agente executando:

```sc.exe query "ASC IoT Agent"```

### <a name="uninstall-the-agent"></a>Desinstalar o agente

Para desinstalar o agente:

1. Execute o seguinte script do PowerShell com o parâmetro **-mode** definido como **Uninstall**.

    ```
    .\InstallSecurityAgent.ps1 -Uninstall
    ```

## <a name="troubleshooting"></a>Solução de problemas

Se o agente não conseguir iniciar, ative o log (por padrão, o log fica *desativado*) para obter mais informações.

Para ativar o log:

1. Abra o arquivo de configuração (General.config) para edição usando um editor de arquivo padrão.

1. Edite os valores a seguir:

   ```xml
   <add key="logLevel" value="Debug" />
   <add key="fileLogLevel" value="Debug"/>
   <add key="diagnosticVerbosityLevel" value="Some" />
   <add key="logFilePath" value="IoTAgentLog.log" />
   ```

    > [!NOTE]
    > É recomendável deixar o registro em log **desativado** após a conclusão da solução de problemas. Deixar o log **ativado** aumenta o tamanho do arquivo de log e o uso de dados.

1. Reinicie o agente executando o seguinte PowerShell ou a linha de comando:

    **PowerShell**

     ```
     Restart-Service "ASC IoT Agent"
     ```

   ou

    **CMD**

     ```
     sc.exe stop "ASC IoT Agent"
     sc.exe start "ASC IoT Agent"
     ```

1. Examine o arquivo de log para obter mais informações sobre a falha. O arquivo de log estaria presente no diretório de trabalho onde executamos o script. 

   Localização do arquivo de log: `.\IoTAgentLog.log`

## <a name="next-steps"></a>Próximas etapas

* Leia a [visão geral](overview.md) do serviço defender para IOT
* Saiba mais sobre a [arquitetura](architecture.md) do defender para IOT
* Habilite o [serviço](quickstart-onboard-iot-hub.md)
* Leia as [perguntas frequentes](resources-frequently-asked-questions.md)
* Entenda os [alertas](concept-security-alerts.md)
