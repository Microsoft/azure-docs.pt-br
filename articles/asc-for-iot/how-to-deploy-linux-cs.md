---
title: Guia para instalar e implantar o Linux C# agente da Central de segurança do Azure para a versão prévia do IoT | Microsoft Docs
description: Saiba como instalar a Central de segurança para o agente do IoT do Azure no Linux de 32 bits e 64 bits.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: b0982203-c3c8-4a0b-8717-5b5ac4038d8c
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2019
ms.author: mlottner
ms.openlocfilehash: 808ff912a997a4c09a22048ada7546daab895701
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67618258"
---
# <a name="deploy-azure-security-center-for-iot-c-based-security-agent-for-linux"></a>Implantar a Central de segurança do Azure para IoT C#-com base em agente de segurança para Linux

> [!IMPORTANT]
> A Central de Segurança do Azure para IoT está em versão prévia pública no momento.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este guia explica como instalar e implantar o Azure Security Center (ASC) para IoT C#-com base em agente de segurança no Linux.

Neste guia, você aprenderá a: 
> [!div class="checklist"]
> * Instalar
> * Verificar implantação
> * Desinstalar o agente
> * Solução de problemas 

## <a name="prerequisites"></a>Pré-requisitos

Para outras plataformas e versões do agente, consulte [escolha o agente de segurança correta](how-to-deploy-agent.md).

1. Para implantar o agente de segurança, são necessários direitos de administrador local no computador em que você deseja instalar. 

1. [Criar um módulo de segurança](quickstart-create-security-twin.md) para o dispositivo.

## <a name="installation"></a>Instalação 

Para implantar o agente de segurança, siga as instruções:

1. Baixe a versão mais recente do [GitHub](https://aka.ms/iot-security-github-cs) para seu computador.

1. Extraia o conteúdo do pacote e navegue até a pasta _/install_.

1. Adicione permissões de execução para o **script InstallSecurityAgent** executando `chmod +x InstallSecurityAgent.sh` 

1. Em seguida, execute: 

   ```
   ./InstallSecurityAgent.sh -i -aui <authentication identity>  -aum <authentication method> -f <file path> -hn <host name>  -di <device id> -cl <certificate location kind>
   ```
   
   Veja [Como configurar a autenticação](concept-security-agent-authentication-methods.md) para mais informações sobre parâmetros de autenticação.

Esse script faz o seguinte:

- Instala pré-requisitos.

- Adiciona um usuário de serviço (com logon interativo desabilitado).

- Instala o agente como um **Daemon** – pressupõe que o dispositivo usa **systemd** para gerenciamento de serviços.

- Configura **sudoers** para permitir que o agente execute certas tarefas como raiz.

- Configura o agente com os parâmetros de autenticação fornecidos.


Para obter ajuda adicional, execute o script com o parâmetro –ajuda: `./InstallSecurityAgent.sh --help`

### <a name="uninstall-the-agent"></a>Desinstalar o agente

Para desinstalar o agente, execute o script com o parâmetro –u: `./InstallSecurityAgent.sh -u`. 

> [!NOTE]
> A desinstalação não remove todos os pré-requisitos ausentes que foram instalados durante a instalação.

## <a name="troubleshooting"></a>Solução de problemas  

1. Verifique o status da implantação executando:

    `systemctl status ASCIoTAgent.service`

2. Habilitar o registro em log.  
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

- Leia a [Visão geral](overview.md) do serviço ASC para IoT
- Saiba mais sobre a [Arquitetura](architecture.md) da ASC para IoT
- Habilite o [serviço](quickstart-onboard-iot-hub.md)
- Leia as [Perguntas frequentes](resources-frequently-asked-questions.md)
- Entenda os [alertas](concept-security-alerts.md)