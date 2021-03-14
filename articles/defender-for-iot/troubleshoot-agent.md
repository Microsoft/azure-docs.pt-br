---
title: Solucionar problemas de inicialização do agente de segurança (Linux)
description: Solucionar problemas de trabalho com agentes de segurança do Azure defender para IoT para Linux.
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
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: 7be6cf1df15d7afd7cb9447be68ff70ff7b14d03
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2021
ms.locfileid: "102449213"
---
# <a name="security-agent-troubleshoot-guide-linux"></a>Guia de solução de problemas do agente de segurança (Linux)

Este artigo explica como resolver problemas em potencial no processo de inicialização do agente de segurança.

O agente do Azure defender para IoT é iniciado automaticamente após a instalação. O processo de inicialização do agente inclui a leitura da configuração local, a conexão ao Hub IoT do Azure e a recuperação da configuração de conexões remotas. A falha em qualquer uma dessas etapas pode fazer com que o agente de segurança falhe.

Neste guia de solução de problemas, você aprenderá a:

- Validar se o agente de segurança está em execução
- Obter erros do agente de segurança
- Entender e corrigir erros do agente de segurança

## <a name="validate-if-the-security-agent-is-running"></a>Validar se o agente de segurança está em execução

1. Para validar o agente de segurança está em execução, aguarde alguns minutos depois de instalar o agente e execute o comando a seguir.
     <br>

    **Agente C**

    ```bash
    grep "ASC for IoT Agent initialized" /var/log/syslog
    ```

    **Agente C#**

    ```bash
    grep "Agent is initialized!" /var/log/syslog
    ```

1. Se o comando retornar uma linha vazia, o agente de segurança não poderá ser iniciado com êxito.

## <a name="force-stop-the-security-agent"></a>Forçar interrupção do agente de segurança

Nos casos em que o agente de segurança não pode iniciar, pare o agente com o comando a seguir e continue com a tabela de erros abaixo:

```bash
systemctl stop ASCIoTAgent.service
```

## <a name="get-security-agent-errors"></a>Obter erros do agente de segurança

1. Recupere erro (s) do agente de segurança executando o seguinte comando:

    ```bash
    grep ASCIoTAgent /var/log/syslog
    ```

1. O comando obter erro do agente de segurança recupera todos os logs criados pelo agente do defender para IoT. Use a tabela a seguir para entender os erros e executar as etapas corretas para a correção.

> [!Note]
> Os logs de erros são mostrados em ordem cronológica. Certifique-se de anotar o carimbo de data/hora de cada erro para ajudar na sua correção.

## <a name="restart-the-agent"></a>Reiniciar o agente

1. Depois de localizar e corrigir um erro do agente de segurança, tente reiniciar o agente executando o comando a seguir.

    ```bash
    systemctl restart ASCIoTAgent.service
    ```

1. Repita o processo anterior para recuperar parar e recuperar os erros se o agente continuar a falhar o processo de inicialização.

## <a name="understand-security-agent-errors"></a>Entender os erros do agente de segurança

A maioria dos erros do agente de segurança são exibidos no seguinte formato:

```
Defender for IoT agent encountered an error! Error in: {Error Code}, reason: {Error sub code}, extra details: {error specific details}
```

| Código de erro | Subcódigo de erro | Detalhes do erro | Corrigir C | Corrigir C # |
|:-----------|:---------------|:--------|:------------|:------------|
| Configuração local | Configuração ausente | Uma configuração está ausente no arquivo de configuração local. A mensagem de erro deve indicar qual chave está faltando. | Adicione a chave ausente ao LocalConfiguration.js/var/no arquivo, consulte [cs-localconfig-Reference](azure-iot-security-local-configuration-c.md) para obter detalhes.| Adicione a chave ausente ao arquivo de General.config, consulte [c#-localconfig-Reference](azure-iot-security-local-configuration-csharp.md) para obter detalhes. |
| Configuração local | Não é possível analisar a configuração | Um valor de configuração não pode ser analisado. A mensagem de erro deve indicar qual chave não pode ser analisada. Um valor de configuração não pode ser analisado porque o valor não está no tipo esperado ou o valor está fora do intervalo. | Corrija o valor da chave em/var/LocalConfiguration.jsno arquivo para que ele corresponda ao esquema LocalConfiguration, consulte o [c#-localconfig-Reference](azure-iot-security-local-configuration-csharp.md) para obter detalhes. |  Corrija o valor da chave no arquivo General.config para que ele corresponda ao esquema, consulte [cs-localconfig-Reference](azure-iot-security-local-configuration-c.md) para obter detalhes.|
| Configuração local | Formato de arquivo | Falha ao analisar o arquivo de configuração. | O arquivo de configuração está corrompido, baixe o agente e reinstale-o. | |
| Configuração remota | Tempo limite | O agente não pôde buscar o módulo azureiotsecurity no intervalo de tempo limite. | Verifique se a configuração de autenticação está correta e tente novamente. | O agente não pôde buscar o módulo azureiotsecurity em cima no período de tempo limite. | Verifique se a configuração de autenticação está correta e tente novamente. |
| Autenticação | Arquivo inexistente | O arquivo no caminho fornecido não existe. | Verifique se o arquivo existe no caminho fornecido ou vá para a **LocalConfiguration.jsno** arquivo e altere a configuração de **FilePath** . | Verifique se o arquivo existe no caminho fornecido ou vá para o arquivo de **Authentication.config** e altere a configuração de **FilePath** .|
| Autenticação | Permissão de arquivo | O agente não tem permissões suficientes para abrir o arquivo. | Conceda ao usuário **asciotagent** permissões de leitura no arquivo no caminho fornecido. | Verifique se o arquivo está acessível. |
| Autenticação | Formato de arquivo | O arquivo fornecido não está no formato correto. | Verifique se o arquivo está no formato correto. Os tipos de arquivo com suporte são. pfx e. PEM. | Verifique se o arquivo é um arquivo de certificado válido. |
| Autenticação | Não Autorizado | O agente não pôde se autenticar no Hub IoT com as credenciais fornecidas. | Valide a configuração de autenticação no arquivo LocalConfiguration, percorra a configuração de autenticação e verifique se todos os detalhes estão corretos, valide se o segredo no arquivo corresponde à identidade autenticada. | Valide a configuração de autenticação no Authentication.config, percorra a configuração de autenticação e verifique se todos os detalhes estão corretos e, em seguida, valide se o segredo no arquivo corresponde à identidade autenticada.
| Autenticação | Não encontrado | O dispositivo/módulo foi encontrado. | Validar configuração de autenticação-Verifique se o nome do host está correto, se o dispositivo existe no Hub IoT e se tem um módulo azureiotsecurity. |  Validar configuração de autenticação-Verifique se o nome do host está correto, se o dispositivo existe no Hub IoT e se tem um módulo azureiotsecurity. |
| Autenticação | Configuração ausente | Uma configuração está ausente no arquivo de *Authentication.config* . A mensagem de erro deve indicar qual chave está faltando. | Adicione a chave ausente ao *LocalConfiguration.jsno* arquivo.| Adicione a chave ausente ao arquivo de *Authentication.config* , consulte [c#-localconfig-Reference](azure-iot-security-local-configuration-csharp.md) para obter detalhes. |
| Autenticação | Não é possível analisar a configuração | Um valor de configuração não pode ser analisado. A mensagem de erro deve indicar qual chave não pode ser analisada. Um valor de configuração não pode ser analisado porque o valor não é do tipo esperado ou o valor está fora do intervalo. |Corrija o valor da chave no **LocalConfiguration.jsno** arquivo. |Corrija o valor da chave no arquivo **Authentication.config** para corresponder ao esquema, consulte [cs-localconfig-Reference](azure-iot-security-local-configuration-c.md) para obter detalhes.|
|

## <a name="next-steps"></a>Próximas etapas

- Leia a [visão geral](overview.md) do serviço defender para IOT
- Saiba mais sobre a [arquitetura](architecture.md) do defender para IOT
- Habilitar o [serviço](quickstart-onboard-iot-hub.md) defender para IOT
- Leia as [perguntas frequentes](resources-frequently-asked-questions.md) do serviço defender para IOT
- Aprenda a acessar [dados brutos de segurança](how-to-security-data-access.md)
- Entender as [recomendações](concept-recommendations.md)
- Entender os [alertas](concept-security-alerts.md) de segurança
