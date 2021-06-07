---
title: 'Azure AD Connect: atualização automática | Microsoft Docs'
description: Este tópico descreve o recurso interno de atualização automática na sincronização do Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 6b395e8f-fa3c-4e55-be54-392dd303c472
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/09/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c8dcc8766b21551f3cd62289805fe735ef0f333
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91317609"
---
# <a name="azure-ad-connect-automatic-upgrade"></a>Azure AD Connect: Atualização automática
Esse recurso foi introduzido com a build [1.1.105.0 (lançada em fevereiro de 2016)](reference-connect-version-history.md).  Esse recurso foi atualizado na [build 1.1.561](reference-connect-version-history.md) e agora dá suporte a cenários adicionais que anteriormente não tinham suporte.

## <a name="overview"></a>Visão geral
Verificar se a instalação do Azure AD Connect está sempre atualizada nunca foi tão fácil após o lançamento do recurso de **atualização automática** . Esse recurso é habilitado por padrão para instalações rápidas e atualizações de DirSync. Quando uma nova versão for lançada, a instalação será atualizada automaticamente.
A atualização automática é habilitada por padrão para o seguinte:

* Instalação das configurações expressas e atualizações de DirSync.
* Usar o SQL Express LocalDB, que é o que as configurações Expressas sempre usam. O DirSync com o SQL Express também usa o LocalDB.
* A conta do AD é a conta MSOL_ padrão criada pelas configurações Expressas e DirSync.
* Ter menos de 100 mil objetos no metaverso.

O estado atual da atualização automática pode ser exibido com o cmdlet `Get-ADSyncAutoUpgrade`do PowerShell. Ele contém os seguintes estados:

| Estado | Comentário |
| --- | --- |
| habilitado |A atualização automática está habilitada. |
| Suspenso |Somente definido pelo sistema. O sistema não está **atualmente** qualificado para receber atualizações automáticas. |
| Desabilitado |A atualização automática está desabilitada. |

Você pode alterar entre **Habilitado** e **Desabilitado** com o `Set-ADSyncAutoUpgrade`. Somente o sistema deve definir o estado como **Suspenso**.  Antes da versão 1.1.750.0, o cmdlet Set-ADSyncAutoUpgrade bloqueava a Atualização automática se o estado de atualização automática estivesse definido como Suspenso. Essa funcionalidade foi alterada para não bloquear a Atualização automática.

A atualização automática está usando o Azure AD Connect Health para a infraestrutura de atualização. Para que a atualização automática funcione, não deixe de abrir as URLs no seu servidor proxy do **Azure AD Connect Health** conforme documentado em [Intervalos de endereços IP e URLs do Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).


Se a interface do usuário **Synchronization Service Manager** estiver em execução no servidor, a atualização será suspensa até que a interface do usuário seja fechada.

## <a name="troubleshooting"></a>Solução de problemas
Se a sua instalação do Connect não for atualizada conforme o esperado, siga estas etapas para descobrir o que poderia estar errado.

Primeiro, você não deve esperar que a atualização automática seja tentada no primeiro dia em que uma nova versão for lançada. Há uma aleatoriedade intencional antes que ocorra uma tentativa de atualização. Sendo assim, não se assuste se a instalação não for atualizada imediatamente.

Se você achar que algo não está certo, primeiro execute `Get-ADSyncAutoUpgrade` para garantir que a atualização automática esteja habilitada.

Se o estado for suspenso, você poderá usar o `Get-ADSyncAutoUpgrade -Detail` para exibir o motivo.  O motivo da suspensão pode conter qualquer valor de cadeia de caracteres, mas geralmente conterá o valor da cadeia de caracteres de UpgradeResult, ou seja, `UpgradeNotSupportedNonLocalDbInstall` ou `UpgradeAbortedAdSyncExeInUse` .  Um valor composto também pode ser retornado, como `UpgradeFailedRollbackSuccess-GetPasswordHashSyncStateFailed` .

Também é possível obter um resultado que não seja um UpgradeResult, ou seja, ' AADHealthEndpointNotDefined ' ou ' DirSyncInPlaceUpgradeNonLocalDb '.

Em seguida, verifique se que você abriu as URLs necessárias em seu proxy ou no firewall. A atualização automática está usando o Azure AD Connect Health conforme descrito na [Visão geral](#overview). Se você usar um proxy, certifique-se de que a Integridade foi configurada para usar um [servidor proxy](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy). Teste também a [conectividade de integridade](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) com o Azure AD.

Com a conectividade com o Azure AD verificada, é hora de examinar os logs de eventos. Inicie o visualizador de eventos e examine o log de eventos do **Aplicativo** . Adicione um filtro EventLog para a origem **Azure ad Connect atualização** e o intervalo de ID de evento **300-399**.  
![Captura de tela que mostra a janela "filtrar log atual" com "origens do evento" e a caixa de IDs de evento "incluir/excluir" realçada.](./media/how-to-connect-install-automatic-upgrade/eventlogfilter.png)  

Agora, você pode ver os logs de eventos associados ao status da atualização automática.  
![Filtro de log de eventos para atualização automática](./media/how-to-connect-install-automatic-upgrade/eventlogresult.png)  

O código de resultado tem um prefixo com uma visão geral do estado.

| Prefixo do código de resultado | Descrição |
| --- | --- |
| Sucesso |A instalação foi atualizada com êxito. |
| UpgradeAborted |Uma condição temporária interrompeu a atualização. Ela será tentada novamente e a expectativa é de que tenha êxito posteriormente. |
| UpgradeNotSupported |O sistema tem uma configuração que está impedindo que o sistema seja atualizado automaticamente. A atualização será tentada novamente para ver se o estado está mudando, mas a expectativa é de que o sistema precise ser atualizado manualmente. |

Esta é uma lista das mensagens mais comuns que você encontrará. Ela não lista todas as mensagens, mas a mensagem de resultado deve ser clara quanto a qual é o problema.

| Mensagem de resultado | Descrição |
| --- | --- |
| **UpgradeAborted** | |
| UpgradeAbortedCouldNotSetUpgradeMarker |Não foi possível escrever no registro. |
| UpgradeAbortedInsufficientDatabasePermissions |O grupo de administradores interno não tem permissões para o banco de dados. Atualize manualmente para a versão mais recente do Azure AD Connect para resolver esse problema. |
| UpgradeAbortedInsufficientDiskSpace |Não há espaço em disco suficiente para dar suporte a uma atualização. |
| UpgradeAbortedSecurityGroupsNotPresent |Não foi possível encontrar e resolver todos os grupos de segurança usados pelo mecanismo de sincronização. |
| UpgradeAbortedServiceCanNotBeStarted |O serviço NT **Microsoft Azure AD Sync** falhou ao iniciar. |
| UpgradeAbortedServiceCanNotBeStarted |O serviço NT **Microsoft Azure AD Sync** falhou ao parar. |
| UpgradeAbortedServiceIsNotRunning |O serviço NT **Microsoft Azure AD Sync** não está executando. |
| UpgradeAbortedSyncCycleDisabled |A opção SyncCycle no [agendador](how-to-connect-sync-feature-scheduler.md) foi desabilitada. |
| UpgradeAbortedSyncExeInUse |A [interface do usuário do Synchronization Service Manager](how-to-connect-sync-service-manager-ui.md) está aberta no servidor. |
| UpgradeAbortedSyncOrConfigurationInProgress |O assistente de instalação está em execução ou uma sincronização foi agendada fora do agendador. |
| **UpgradeNotSupported** | |
| UpgradeNotSupportedCustomizedSyncRules |Você adicionou suas próprias regras personalizadas à configuração. |
| UpgradeNotSupportedInvalidPersistedState |A instalação não é uma configuração Express ou uma atualização de DirSync. |
| UpgradeNotSupportedNonLocalDbInstall |Você não está usando um banco de dados SQL Server Express LocalDB. |
|UpgradeNotSupportedLocalDbSizeExceeded|O tamanho do BD local é maior ou igual a 8 GB|
|UpgradeNotSupportedAADHealthUploadDisabled|Os carregamentos de dados de integridade foram desabilitados no portal|

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre [Como integrar suas identidades locais ao Active Directory do Azure](whatis-hybrid-identity.md).
