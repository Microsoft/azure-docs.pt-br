---
title: "Sincronização do Azure AD Connect: agendador | Microsoft Docs"
description: "Este tópico descreve o recurso Agendador interno na sincronização do Azure AD Connect."
services: active-directory
documentationcenter: 
author: AndKjell
manager: femila
editor: 
ms.assetid: 6b1a598f-89c0-4244-9b20-f4aaad5233cf
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: 63f69756b3933fecdec75cc677e1098447e5b94e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-connect-sync-scheduler"></a>Sincronização do Azure AD Connect: agendador
Este tópico descreve o agendador interno na sincronização do Azure AD Connect (também conhecido como mecanismo de sincronização).

Esse recurso foi introduzido com a compilação 1.1.105.0 (lançada em fevereiro de 2016).

## <a name="overview"></a>Visão geral
A sincronização do Azure AD Connect sincroniza mudanças ocorridas em seu diretório local usando um agendador. Há dois processos de agendador, um para sincronização de senha e outro para sincronização de atributo/objeto e tarefas de manutenção. Este tópico aborda a última opção.

Em versões anteriores, o agendador de objetos e atributos era separado do mecanismo de sincronização. Ele usava o agendador de tarefas do Windows ou um serviço do Windows separado para disparar o processo de sincronização. O agendador faz parte dos recursos internos nas versões 1.1 do mecanismo de sincronização e permite alguma personalização. A frequência de sincronização do novo padrão é de 30 minutos.

O agendador é responsável por duas tarefas:

* **Ciclo de sincronização**. O processo de importação, sincronização e exportação das alterações.
* **Tarefas de manutenção**. Renove as chaves e certificados para a redefinição de Senha e o DRS (Serviço de Registro de Dispositivo). Limpe as entradas antigas no log de operações.

O agendador em si está sempre em execução, mas ele pode ser configurado para executar apenas uma ou nenhuma dessas tarefas. Por exemplo, se você precisar ter seu próprio processo de ciclo de sincronização, poderá desabilitar essa tarefa no agendador, mas ainda executar a tarefa de manutenção.

## <a name="scheduler-configuration"></a>Configuração do agendador
Para ver as configurações atuais, acesse o PowerShell e execute `Get-ADSyncScheduler`. Ele mostra algo parecido com esta imagem:

![GetSyncScheduler](./media/active-directory-aadconnectsync-feature-scheduler/getsynccyclesettings2016.png)

Se você vir **O comando de sincronização ou o cmdlet não está disponível** quando executar esse cmdlet, o módulo do PowerShell não estará carregado. Esse problema poderá ocorrer se você executar o Azure AD Connect em um controlador de domínio ou em um servidor com níveis mais altos de restrição do PowerShell do que as configurações padrão. Se você vir esse erro, execute `Import-Module ADSync` para disponibilizar o cmdlet.

* **AllowedSyncCycleInterval**. O intervalo de tempo mais curto entre os ciclos de sincronização permitido pelo Azure AD. Você não pode sincronizar com maior frequência do que a permitida por essa configuração e ainda ter suporte.
* **CurrentlyEffectiveSyncCycleInterval**. O agendamento atualmente em vigor. Ele terá o mesmo valor de CustomizedSyncInterval (se definido) se não for mais frequente do que AllowedSyncInterval. Se você usar uma versão anterior à 1.1.281 e alterar o CustomizedSyncCycleInterval, essa alteração entrará em vigor após o próximo ciclo de sincronização. Desde o build 1.1.281, a alteração entra em vigor imediatamente.
* **CustomizedSyncCycleInterval**. Se você quiser que o agendador seja executado em intervalos diferentes do padrão de 30 minutos, terá que definir nessa configuração. Na figura acima, o agendador foi definido para ser executado a cada hora. Se você definir essa configuração como um valor menor do que AllowedSyncInterval, a última opção será usada.
* **NextSyncCyclePolicyType**. Delta ou Inicial. Define se a próxima execução deve apenas processar alterações delta ou se a próxima execução deve fazer importação e sincronização completas. O última opção também reprocessaria regras novas ou alteradas.
* **NextSyncCycleStartTimeInUTC**. Da próxima vez, o agendador iniciará o próximo ciclo de sincronização.
* **PurgeRunHistoryInterval**. Os logs de operação de tempo devem ser mantidos. Esses logs podem ser analisados no gerenciador do serviço de sincronização. O padrão é manter os logs por sete dias.
* **SyncCycleEnabled**. Indica se o agendador está executando os processos de exportação, importação e sincronização como parte de sua operação.
* **MaintenanceEnabled**. Mostra se o processo de manutenção está habilitado. Ele atualiza os certificados/chaves e limpa o log de operações.
* **StagingModeEnabled**. Mostra se o [modo de preparo](active-directory-aadconnectsync-operations.md#staging-mode) está habilitado. Quando essa configuração está habilitada, ela impede que as exportações sejam executadas, mas ainda executa a importação e a sincronização.
* **SchedulerSuspended**. Definido pelo Connect durante uma atualização para bloquear temporariamente a execução do agendador.

Você pode alterar algumas dessas configurações com `Set-ADSyncScheduler`. Os parâmetros a seguir podem ser modificados:

* CustomizedSyncCycleInterval
* NextSyncCyclePolicyType
* PurgeRunHistoryInterval
* SyncCycleEnabled
* MaintenanceEnabled

Em builds anteriores do Azure AD Connect, **isStagingModeEnabled** era exposto em Set-ADSyncScheduler. **Não há suporte** para a definição dessa propriedade. A propriedade **SchedulerSuspended** só deve ser modificada pelo Connect. **Não há suporte** para a definição dela diretamente com o PowerShell.

A configuração do agendador é armazenada no Azure AD. Se você tiver um servidor de preparo, uma alteração no servidor primário também afetará o servidor de preparo (exceto IsStagingModeEnabled).

### <a name="customizedsynccycleinterval"></a>CustomizedSyncCycleInterval
Sintaxe: `Set-ADSyncScheduler -CustomizedSyncCycleInterval d.HH:mm:ss`  
d - dias, HH - horas, mm - minutos, ss - segundos

Exemplo: `Set-ADSyncScheduler -CustomizedSyncCycleInterval 03:00:00`  
Altera o Agendador para executar a cada três horas.

Exemplo: `Set-ADSyncScheduler -CustomizedSyncCycleInterval 1.0:0:0`  
Altera o Agendador para executar diariamente.

### <a name="disable-the-scheduler"></a>Desabilitar o agendador  
Se você precisar fazer alterações de configuração, talvez seja melhor desabilitar o agendador. Por exemplo, quando você [configura a filtragem](active-directory-aadconnectsync-configure-filtering.md) ou [faz alterações nas regras de sincronização](active-directory-aadconnectsync-change-the-configuration.md).

Execute `Set-ADSyncScheduler -SyncCycleEnabled $false` para desabilitar o agendador.

![Desabilitar o agendador](./media/active-directory-aadconnectsync-change-the-configuration/schedulerdisable.png)

Quando você fizer as alterações, não se esqueça de habilitar o agendador novamente com `Set-ADSyncScheduler -SyncCycleEnabled $true`.

## <a name="start-the-scheduler"></a>Iniciar o agendador
Por padrão, o agendador é executado a cada 30 minutos. Em alguns casos, é bom executar um ciclo de sincronização entre os ciclos agendados ou terá que executar um tipo diferente.

**Ciclo de sincronização delta**  
Um ciclo de sincronização delta inclui as seguintes etapas:

* Importação delta em todos os conectores
* Sincronização delta em todos os conectores
* Exportação em todos os conectores

É possível que você tenha uma alteração urgente que deva ser sincronizada imediatamente e precise executar um ciclo manualmente. Se precisar executar um ciclo manualmente, execute `Start-ADSyncSyncCycle -PolicyType Delta`no PowerShell.

**Ciclo de sincronização completo**  
Caso tenha feito uma das alterações de configuração a seguir, será necessário executar um ciclo de sincronização completo (também conhecido como Inicial):

* Adicionou mais objetos ou atributos para serem importados de um diretório de origem
* Realizou alterações nas regras de sincronização
* Alterou a [filtragem](active-directory-aadconnectsync-configure-filtering.md) para incluir um número diferente de objetos

Se você tiver realizado uma dessas alterações, precisará executar um ciclo de sincronização completa para que o mecanismo de sincronização possa reconsolidar os espaços conectores. Um ciclo de sincronização completa inclui as seguintes etapas:

* Importação completa de todos os conectores
* Sincronização completa de todos os conectores
* Exportação em todos os conectores

Para iniciar um ciclo de sincronização completo, execute `Start-ADSyncSyncCycle -PolicyType Initial` em um prompt do PowerShell. Esse comando inicia um ciclo de sincronização completa.

## <a name="stop-the-scheduler"></a>Parar o agendador
Se o agendador estiver executando um ciclo de sincronização, talvez seja necessário interrompê-lo. Por exemplo, se você iniciar o assistente de instalação e receber este erro:

![SyncCycleRunningError](./media/active-directory-aadconnectsync-feature-scheduler/synccyclerunningerror.png)

Se um ciclo de sincronização estiver em execução, você não poderá alterar a configuração. Você pode aguardar até que o agendador conclua o processo ou pode interrompê-lo para realizar suas alterações logo em seguida. Parar o ciclo atual não é prejudicial e as alterações serão processadas na próxima execução.

1. Comece informando o agendador para interromper o ciclo atual com o cmdlet `Stop-ADSyncSyncCycle`do PowerShell.
2. Se você usar uma versão anterior à 1.1.281, parando em seguida, o agendador não interromperá a tarefa atual do conector atual. Para forçar a interrupção do Conector, execute as seguintes ações: ![StopAConnector](./media/active-directory-aadconnectsync-feature-scheduler/stopaconnector.png)
   * Inicie o **Serviço de Sincronização** no menu Iniciar. Vá para **Conectores**, realce o Conector com o estado **Executando** e selecione **Parar** em Ações.

O agendador ainda está ativo e será iniciado novamente na próxima oportunidade.

## <a name="custom-scheduler"></a>Agendador personalizado
Os cmdlets documentados nesta seção só estão disponíveis no build [1.1.130.0](active-directory-aadconnect-version-history.md#111300) e posteriores.

Se o agendador interno não atender às suas necessidades, você poderá fazer o agendamento dos Conectores usando o PowerShell.

### <a name="invoke-adsyncrunprofile"></a>Invoke-ADSyncRunProfile
Você pode iniciar um perfil para um Conector desta forma:

```
Invoke-ADSyncRunProfile -ConnectorName "name of connector" -RunProfileName "name of profile"
```

Os nomes a serem usados como [Nomes de conector](active-directory-aadconnectsync-service-manager-ui-connectors.md) e [Nomes de Perfil de Execução](active-directory-aadconnectsync-service-manager-ui-connectors.md#configure-run-profiles) podem ser encontrados na [Interface do Usuário do Synchronization Service Manager](active-directory-aadconnectsync-service-manager-ui.md).

![Invocar perfil de execução](./media/active-directory-aadconnectsync-feature-scheduler/invokerunprofile.png)  

O cmdlet `Invoke-ADSyncRunProfile` é síncrono, ou seja, ele não retornará o controle até que o Conector tenha concluído a operação, seja com êxito ou com erro.

Quando você agendar seus Conectores, a recomendação é agendá-los na seguinte ordem:

1. (Completo/Delta) Importar de diretórios locais, como o Active Directory
2. (Completo/Delta) Importar do Azure AD
3. (Completo/Delta) Sincronizar de diretórios locais, como o Active Directory
4. (Completo/Delta) Sincronização do Azure AD
5. Exportar para o Azure AD
6. Exportar para diretórios locais, como o Active Directory

Essa ordem é como o agendador interno executa os conectores.

### <a name="get-adsyncconnectorrunstatus"></a>Get-ADSyncConnectorRunStatus
Você também pode monitorar o mecanismo de sincronização para ver se ele está ocupado ou ocioso. Esse cmdlet retornará um resultado vazio se o mecanismo de sincronização estiver ocioso e não estiver executando um Conector. Se um Conector estiver em execução, ele retornará o nome do Conector.

```
Get-ADSyncConnectorRunStatus
```

![Status de execução do conector](./media/active-directory-aadconnectsync-feature-scheduler/getconnectorrunstatus.png)  
Na imagem acima, a primeira linha é de um estado em que o mecanismo de sincronização está ocioso. A segunda linha é de quando o Conector do Azure AD está em execução.

## <a name="scheduler-and-installation-wizard"></a>Agendador e o assistente de instalação
Se você iniciar o assistente de instalação, o agendador será temporariamente suspenso. Esse comportamento ocorre porque ele pressupõe que você fará alterações na configuração e as definições não poderão ser aplicadas se o mecanismo de sincronização estiver ativamente em execução. Por esse motivo, não deixe o assistente de instalação aberto, já que ele impede que o mecanismo de sincronização execute ações de sincronização.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre a configuração de [sincronização do Azure AD Connect](active-directory-aadconnectsync-whatis.md) .

Saiba mais sobre [Como integrar suas identidades locais ao Active Directory do Azure](active-directory-aadconnect.md).
