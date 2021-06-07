---
title: Conecte seu Alsid para Active Directory ao Azure sentinela | Microsoft Docs
description: Saiba como usar o Alsid para conector de Active Directory para efetuar pull de logs de Alsid no Azure Sentinel. Exiba dados do Alsid em pastas de trabalho, crie alertas e melhore a investigação.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2021
ms.author: yelevin
ms.openlocfilehash: 654ecb65068e4321b85594d96e8ca7a7f73cde7e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99566668"
---
# <a name="connect-your-alsid-for-active-directory-ad-to-azure-sentinel"></a>Conectar seu Alsid para o Active Directory (AD) ao Azure Sentinel

> [!IMPORTANT]
> O Alsid para conector de Active Directory está atualmente em **Visualização**. Consulte os [termos de uso suplementares para Microsoft Azure visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) de termos legais adicionais que se aplicam aos recursos do Azure que estão em versão beta, visualização ou, de outra forma, ainda não foram lançadas em disponibilidade geral.

Este artigo explica como conectar sua solução do Alsid for AD ao Azure Sentinel. O Alsid for Active Directory data Connector permite que você conecte facilmente seus Alsid para logs do AD com o Azure Sentinel, para que você possa exibir os dados em pastas de trabalho, consultá-los para criar alertas personalizados e incorporá-los para melhorar a investigação. A integração entre o Alsid para o AD e o Azure Sentinel usa um servidor syslog com o agente de Log Analytics instalado. Ele também usa um Log Parser personalizado baseado em uma função Kusto.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho no qual você está executando o Azure Sentinel.

## <a name="prerequisites"></a>Pré-requisitos

- Você deve ter permissão de gravação no espaço de trabalho do Azure Sentinel.

- Sua solução Alsid para AD deve ser configurada para exportar logs via syslog.

## <a name="send-alsid-for-ad-logs-to-azure-sentinel-via-the-syslog-agent"></a>Enviar Alsid para logs do AD para o Azure Sentinel por meio do agente de syslog

Configure o Alsid para o AD para encaminhar mensagens de syslog para seu espaço de trabalho do Azure Sentinel por meio do agente de syslog.

1. No menu de navegação do Azure Sentinel, selecione **conectores de dados**.

1. Na Galeria de **conectores de dados** , selecione o conector **Alsid para Active Directory (versão prévia)** e, em seguida, **abra a página conector**.

1. Siga as instruções na página **Alsid para conector de Active Directory** :

    1. Configurar um servidor syslog

        1. Se você ainda não tiver um, crie um servidor syslog Linux para o Alsid para AD enviar logs para o. O Azure Sentinel dá suporte aos daemons **rsyslog** e **syslog-ng** . 

        1. Configure seu servidor syslog para saída Alsid para logs do AD em um arquivo separado.

    1. Configurar o Alsid para o AD para enviar logs para o servidor syslog

        1. Em seu **Alsid para o portal do AD** , vá para *sistema*, *configuração* e, em seguida, *syslog*. A partir daí, você pode criar um novo alerta de syslog em direção ao servidor syslog. Para o servidor remoto, use o endereço IP do computador Linux no qual você instalou o agente Linux.

        1. Verifique se os logs estão corretamente coletados no servidor em um arquivo separado (para fazer isso, você pode usar o botão **testar a configuração** na configuração de alerta do *syslog* no Alsid para AD).

    1. Instalar e carregar o agente de Log Analytics para Linux

        - Escolha uma VM Linux do Azure ou um computador Linux não Azure (físico ou virtual). Siga os links e as instruções na tela. Consulte [configurar seu computador ou dispositivo Linux](connect-syslog.md#configure-your-linux-machine-or-appliance) para obter mais detalhes.

    1. Configurar os logs a serem coletados pelo agente de Log Analytics

        - Selecione as instalações e severidades na configuração configurações avançadas do espaço de trabalho.

            1. Clique no link **abrir as configurações avançadas do espaço de trabalho configuração >** na página do conector.

            1. Na tela **Configurações avançadas** , selecione **dados** e **logs personalizados**.

            1. Marque a caixa de seleção **aplicar configuração abaixo a meus computadores Linux** e clique em **Adicionar**.

            1. Clique em **escolher arquivo** para carregar um exemplo de Alsid para o arquivo syslog do AD do computador Linux que executa o servidor syslog e clique em **Avançar**.

            1. Verifique se **definir delimitador de registro** está definido como **nova linha** e clique em **Avançar**.

            1. Selecione **Linux** e insira o caminho do arquivo para o arquivo syslog, clique em **+** e em **Avançar**.

            1. No campo nome, digite *AlsidForADLog* antes do sufixo _CL e clique em **concluído**.
    
Pode levar até 20 minutos antes que os logs comecem a aparecer na Log Analytics.

## <a name="find-your-data"></a>Encontre seus dados

Depois que uma conexão bem-sucedida é estabelecida, os dados aparecem nos **logs**, em **logs personalizados** na tabela *AlsidForADLog_CL* .

Esse conector de dados depende de um analisador baseado em uma função Kusto para funcionar conforme o esperado. Use as etapas a seguir para configurar a função **afad_parser** Kusto a ser usada em consultas e pastas de trabalho do.

1. No menu de navegação do Azure Sentinel, selecione **logs**.

1. Copie a consulta a seguir e cole-a na janela de consulta.
    ```kusto
    let CodenameTable=datatable(Codename: string, Explanation: string) [
    "test-checker-codename", "This is a test checker",
    "", "Not an alert",
    "C-ADM-ACC-USAGE", "Recent use of the default administrator account",
    "C-UNCONST-DELEG", "Dangerous delegation",
    "C-PASSWORD-DONT-EXPIRE", "Accounts with never expiring passwords",
    "C-USERS-CAN-JOIN-COMPUTERS", "Users allowed to join computers to the domain",
    "C-CLEARTEXT-PASSWORD", "Potential clear-text password",
    "C-PROTECTED-USERS-GROUP-UNUSED", "Protected Users group not used",
    "C-PASSWORD-POLICY", "Weak password policies are applied on users",
    "C-GPO-HARDENING", "Domain without computer-hardening GPOs",
    "C-LAPS-UNSECURE-CONFIG", "Local administrative account management",
    "C-AAD-CONNECT", "Verify permissions related to AAD Connect accounts",
    "C-AAD-SSO-PASSWORD", "Verify AAD SSO account password last change",
    "C-GPO-SD-CONSISTENCY", "Verify sensitive GPO objects and files permissions",
    "C-DSHEURISTICS", "Domain using a dangerous backward-compatibility configuration",
    "C-DOMAIN-FUNCTIONAL-LEVEL", "Domains have an outdated functional level",
    "C-DISABLED-ACCOUNTS-PRIV-GROUPS", "Disabled accounts in privileged groups",
    "C-DCSHADOW", "Rogue domain controllers",
    "C-DC-ACCESS-CONSISTENCY", "Domain controllers managed by illegitimate users",
    "C-DANGEROUS-TRUST-RELATIONSHIP", "Dangerous trust relationship",
    "C-DANGEROUS-SENSITIVE-PRIVILEGES", "Dangerous sensitive privileges",
    "C-DANG-PRIMGROUPID", "User Primary Group ID",
    "C-BAD-PASSWORD-COUNT", "Brute-force attack detection",
    "C-ADMINCOUNT-ACCOUNT-PROPS", "AdminCount attribute set on standard users",
    "C-ACCOUNTS-DANG-SID-HISTORY", "Accounts having a dangerous SID History attribute",
    "C-ABNORMAL-ENTRIES-IN-SCHEMA", "Dangerous rights in AD's schema",
    "C-GPOLICY-DISABLED-UNLINKED", "Unlinked, disabled or orphan GPO",
    "C-KERBEROS-CONFIG-ACCOUNT", "Kerberos configuration on user account",
    "C-KRBTGT-PASSWORD", "KDC password last change",
    "C-LAPS-UNSECURE-CONFIG", "Local administrative account management",
    "C-NATIVE-ADM-GROUP-MEMBERS", "Native administrative group members",
    "C-NETLOGON-SECURITY", "Unsecured configuration of Netlogon protocol",
    "C-OBSOLETE-SYSTEMS", "Computers running an obsolete OS",
    "C-PASSWORD-NOT-REQUIRED", "Account that might have an empty password",
    "C-PKI-WEAK-CRYPTO", "Use of weak cryptography algorithms into Active Directory PKI",
    "C-PRE-WIN2000-ACCESS-MEMBERS", "Accounts using a pre-Windows 2000 compatible access control",
    "C-PRIV-ACCOUNTS-SPN", "Privileged accounts running Kerberos services",
    "C-REVER-PWD-GPO", "Reversible passwords in GPO",
    "C-ROOTOBJECTS-SD-CONSISTENCY", "Root objects permissions allowing DCSync-like attacks",
    "C-SDPROP-CONSISTENCY", "Ensure SDProp consistency",
    "C-SENSITIVE-CERTIFICATES-ON-USER", "Ensure SDProp consistency",
    "C-SLEEPING-ACCOUNTS", "Sleeping accounts",
    "C-USER-PASSWORD", "User account using old password",
    "C-USERS-REVER-PWDS", "Reversible passwords"
    ];
    let Common = AlsidForADLog_CL
    | parse RawData with
                         Time:datetime  " "
                         Host:string  " "
                         Product:string "["
                         PID:int "]: \""
                         MessageType:int "\" \""
                         AlertID:int "\" \""
                         Forest:string "\" \""
                         Domain:string "\" "
                         DistinctPart:string;
    let Deviances = Common
    | where MessageType == 0 | parse DistinctPart with "\""
                         Codename:string "\" \""
                         Severity:string "\" \""
                         ADObject:string "\" \""
                         DevianceID:string "\" \""
                         ProfileID:string "\" \""
                         ReasonCodename:string "\" \""
                         EventID:string "\""
                         Attributes:string "\r\n";
    let Changes = Common
    | where MessageType == 1
    | parse kind=regex DistinctPart with "\""
                         ADObject:string "\" \""
                         EventID:string "\" \""
                         EventType:string "\" "
                         Attributes:string "\r?\n";
    union Changes, Deviances
    | project-away DistinctPart, Product, _ResourceId, _SubscriptionId
    | lookup kind=leftouter CodenameTable on Codename;
    ```

1. Clique na lista suspensa **salvar** e clique em **salvar**. No painel **salvar** ,

    1. Em **nome**, insira **afad_parser**.

    1. Em **salvar como**, escolha **função**.

    1. Em **alias de função**, insira **afad_parser**.

    1. Em **categoria**, insira **funções**.

    1. Clique em **Save** (Salvar).

    Os aplicativos de funções normalmente levam entre 10 e 15 minutos para serem ativados.

Agora você está pronto para consultar os dados do Alsid para o AD, inserindo `afad_parser` na linha superior da janela de consulta.

Consulte a guia **próximas etapas** na página conector para obter mais exemplos de consulta.

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a conectar o Alsid for AD ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade de seus dados e ameaças em potencial](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Use pastas de trabalho](tutorial-monitor-your-data.md) para monitorar seus dados.
