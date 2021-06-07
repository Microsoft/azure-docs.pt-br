---
title: 'Sincronização do Azure AD Connect: agendador | Microsoft Docs'
description: Este tópico descreve o recurso Agendador interno na sincronização do Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 6b1a598f-89c0-4244-9b20-f4aaad5233cf
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/01/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad7b0039602add7f4cd3cdd300bd829c4f148a79
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "90084729"
---
# <a name="azure-ad-connect-sync-scheduler"></a>Sincronização do Azure AD Connect: agendador
Este tópico descreve o Agendador interno no Azure AD Connect sincronização (mecanismo de sincronização).

Esse recurso foi introduzido com a compilação 1.1.105.0 (lançada em fevereiro de 2016).

## <a name="overview"></a>Visão geral
A sincronização do Azure AD Connect sincroniza mudanças ocorridas em seu diretório local usando um agendador. Há dois processos de agendador, um para sincronização de senha e outro para sincronização de atributo/objeto e tarefas de manutenção. Este tópico aborda a última opção.

Em versões anteriores, o agendador de objetos e atributos era separado do mecanismo de sincronização. Ele usava o agendador de tarefas do Windows ou um serviço do Windows separado para disparar o processo de sincronização. O agendador faz parte dos recursos internos nas versões 1.1 do mecanismo de sincronização e permite alguma personalização. A frequência de sincronização do novo padrão é de 30 minutos.

O agendador é responsável por duas tarefas:

* **Ciclo de sincronização**. O processo de importação, sincronização e exportação das alterações.
* **Tarefas de manutenção**. Renove as chaves e certificados para a redefinição de Senha e o DRS (Serviço de Registro de Dispositivo). Limpe as entradas antigas no log de operações.

O agendador em si está sempre em execução, mas ele pode ser configurado para executar apenas uma ou nenhuma dessas tarefas. Por exemplo, se você precisar ter seu próprio processo de ciclo de sincronização, poderá desabilitar essa tarefa no agendador, mas ainda executar a tarefa de manutenção.

>[!IMPORTANT]
>Por padrão a cada 30 minutos, um ciclo de sincronização é executado. Se você tiver modificado o ciclo de sincronização, precisará certificar-se de que um ciclo de sincronização seja executado pelo menos uma vez a cada 7 dias. 
>
>* Uma sincronização Delta precisa ocorrer dentro de 7 dias a partir da última sincronização Delta.
>* Uma sincronização Delta (após uma sincronização completa) precisa ocorrer dentro de 7 dias a partir da hora em que a última sincronização completa foi concluída.
>
>A falha em fazer isso pode causar problemas de sincronização que exigirão a execução de uma sincronização completa para resolver. Isso também se aplica a servidores no modo de preparo.

## <a name="scheduler-configuration"></a>Configuração do agendador
Para ver as configurações atuais, acesse o PowerShell e execute `Get-ADSyncScheduler`. Ele mostra algo parecido com esta imagem:

![GetSyncScheduler](./media/how-to-connect-sync-feature-scheduler/getsynccyclesettings2016.png)

Se você vir **O comando de sincronização ou o cmdlet não está disponível** quando executar esse cmdlet, o módulo do PowerShell não estará carregado. Esse problema poderá ocorrer se você executar o Azure AD Connect em um controlador de domínio ou em um servidor com níveis mais altos de restrição do PowerShell do que as configurações padrão. Se você vir esse erro, execute `Import-Module ADSync` para disponibilizar o cmdlet.

* **AllowedSyncCycleInterval**. O intervalo de tempo mais curto entre os ciclos de sincronização permitido pelo Azure AD. Você não pode sincronizar com maior frequência do que a permitida por essa configuração e ainda ter suporte.
* **CurrentlyEffectiveSyncCycleInterval**. O agendamento atualmente em vigor. Ele terá o mesmo valor de CustomizedSyncInterval (se definido) se não for mais frequente do que AllowedSyncInterval. Se você usar uma versão anterior à 1.1.281 e alterar o CustomizedSyncCycleInterval, essa alteração entrará em vigor após o próximo ciclo de sincronização. Desde o build 1.1.281, a alteração entra em vigor imediatamente.
* **CustomizedSyncCycleInterval**. Se você quiser que o agendador seja executado em intervalos diferentes do padrão de 30 minutos, terá que definir nessa configuração. Na figura acima, o agendador foi definido para ser executado a cada hora. Se você definir essa configuração como um valor menor do que AllowedSyncInterval, a última opção será usada.
* **NextSyncCyclePolicyType**. Delta ou Inicial. Define se a próxima execução deve processar apenas alterações delta ou se a próxima execução deve fazer uma importação e sincronização completas. O último também reprocessaria todas as regras novas ou alteradas.
* **NextSyncCycleStartTimeInUTC**. Da próxima vez, o agendador iniciará o próximo ciclo de sincronização.
* **PurgeRunHistoryInterval**. Os logs de operação de tempo devem ser mantidos. Esses logs podem ser analisados no gerenciador do serviço de sincronização. O padrão é manter os logs por sete dias.
* **SyncCycleEnabled**. Indica se o agendador está executando os processos de exportação, importação e sincronização como parte de sua operação.
* **MaintenanceEnabled**. Mostra se o processo de manutenção está habilitado. Ele atualiza os certificados/chaves e limpa o log de operações.
* **StagingModeEnabled**. Mostra se o [modo de preparo](how-to-connect-sync-staging-server.md) está habilitado. Quando essa configuração está habilitada, ela impede que as exportações sejam executadas, mas ainda executa a importação e a sincronização.
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
Se você precisar fazer alterações de configuração, talvez seja melhor desabilitar o agendador. Por exemplo, quando você [configura a filtragem](how-to-connect-sync-configure-filtering.md) ou [faz alterações nas regras de sincronização](how-to-connect-sync-change-the-configuration.md).

Execute `Set-ADSyncScheduler -SyncCycleEnabled $false` para desabilitar o agendador.

![Desabilitar o agendador](./media/how-to-connect-sync-feature-scheduler/schedulerdisable.png)

Quando você fizer as alterações, não se esqueça de habilitar o agendador novamente com `Set-ADSyncScheduler -SyncCycleEnabled $true`.

## <a name="start-the-scheduler"></a>Iniciar o agendador
Por padrão, o agendador é executado a cada 30 minutos. Em alguns casos, é bom executar um ciclo de sincronização entre os ciclos agendados ou terá que executar um tipo diferente.

### <a name="delta-sync-cycle"></a>Ciclo de sincronização delta
 Um ciclo de sincronização delta inclui as seguintes etapas:


- Importação delta em todos os conectores
- Sincronização delta em todos os conectores
- Exportação em todos os conectores

### <a name="full-sync-cycle"></a>Ciclo de sincronização completo
Um ciclo de sincronização completa inclui as seguintes etapas:

- Importação completa de todos os conectores
- Sincronização completa de todos os conectores
- Exportação em todos os conectores

É possível que você tenha uma alteração urgente que deva ser sincronizada imediatamente e precise executar um ciclo manualmente. 

Se você precisar executar um ciclo de sincronização manualmente, a partir do PowerShell, execute `Start-ADSyncSyncCycle -PolicyType Delta` .

Para iniciar um ciclo de sincronização completo, execute `Start-ADSyncSyncCycle -PolicyType Initial` em um prompt do PowerShell.   

Executar um ciclo de sincronização completo pode ser muito demorado, leia a próxima seção para ler como otimizar esse processo.

### <a name="sync-steps-required-for-different-configuration-changes"></a>Etapas de sincronização necessárias para diferentes alterações de configuração
Alterações de configuração diferentes exigem etapas de sincronização diferentes para garantir que as alterações sejam aplicadas corretamente a todos os objetos.

- Adição de mais objetos ou atributos a serem importados de um diretório de origem (adicionando/modificando as regras de sincronização)
    - Uma importação completa é necessária no conector para esse diretório de origem
- Realizou alterações nas regras de sincronização
    - Uma sincronização completa é necessária no conector para as regras de sincronização alteradas
- Alterou a [filtragem](how-to-connect-sync-configure-filtering.md) para incluir um número diferente de objetos
    - Uma importação completa é necessária no conector para cada conector do AD, a menos que você esteja usando a filtragem baseada em atributo com base em atributos que já estão sendo importados para o mecanismo de sincronização

### <a name="customizing-a-sync-cycle-run-the-right-mix-of-delta-and-full-sync-steps"></a>Personalizando um ciclo de sincronização, execute a combinação certa de Delta e etapas completas de sincronização
Para evitar a execução de um ciclo de sincronização completo, você pode marcar conectores específicos para executar uma etapa completa usando os cmdlets a seguir.

`Set-ADSyncSchedulerConnectorOverride -Connector <ConnectorGuid> -FullImportRequired $true`

`Set-ADSyncSchedulerConnectorOverride -Connector <ConnectorGuid> -FullSyncRequired $true`

`Get-ADSyncSchedulerConnectorOverride -Connector <ConnectorGuid>` 

Exemplo: se você fez alterações nas regras de sincronização do conector "floresta do AD A" que não exigem nenhum novo atributo a ser importado, execute os cmdlets a seguir para executar um ciclo de sincronização Delta que também fez uma etapa de sincronização completa para esse conector.

`Set-ADSyncSchedulerConnectorOverride -ConnectorName “AD Forest A” -FullSyncRequired $true`

`Start-ADSyncSyncCycle -PolicyType Delta`

Exemplo: se você fez alterações nas regras de sincronização para o conector "floresta do AD A" para que agora eles exijam que um novo atributo seja importado, você executaria os seguintes cmdlets para executar um ciclo de sincronização Delta que também fez uma importação completa, etapa de sincronização completa para esse conector.

`Set-ADSyncSchedulerConnectorOverride -ConnectorName “AD Forest A” -FullImportRequired $true`

`Set-ADSyncSchedulerConnectorOverride -ConnectorName “AD Forest A” -FullSyncRequired $true`

`Start-ADSyncSyncCycle -PolicyType Delta`


## <a name="stop-the-scheduler"></a>Parar o agendador
Se o agendador estiver executando um ciclo de sincronização, talvez seja necessário interrompê-lo. Por exemplo, se você iniciar o assistente de instalação e receber este erro:

![A captura de tela mostra não pode alterar a mensagem de erro de configuração.](./media/how-to-connect-sync-feature-scheduler/synccyclerunningerror.png)

Se um ciclo de sincronização estiver em execução, você não poderá alterar a configuração. Você pode aguardar até que o agendador conclua o processo ou pode interrompê-lo para realizar suas alterações logo em seguida. Parar o ciclo atual não é prejudicial e as alterações serão processadas na próxima execução.

1. Comece informando o agendador para interromper o ciclo atual com o cmdlet `Stop-ADSyncSyncCycle`do PowerShell.
2. Se você usar uma versão anterior à 1.1.281, parando em seguida, o agendador não interromperá a tarefa atual do conector atual. Para forçar a interrupção do Conector, execute as seguintes ações: 

   ![A captura de tela mostra Synchronization Service Manager com conectores selecionados e um conector em execução realçado com a ação parar selecionada.](./media/how-to-connect-sync-feature-scheduler/stopaconnector.png)

   * Inicie o **serviço de sincronização** no menu iniciar. Acesse **conectores**, realce o conector com o estado **em execução** e selecione **parar** nas ações.

O agendador ainda está ativo e será iniciado novamente na próxima oportunidade.

## <a name="custom-scheduler"></a>Agendador personalizado
Os cmdlets documentados nesta seção só estão disponíveis no build [1.1.130.0](reference-connect-version-history.md) e posteriores.

Se o agendador interno não atender às suas necessidades, você poderá fazer o agendamento dos Conectores usando o PowerShell.

### <a name="invoke-adsyncrunprofile"></a>Invoke-ADSyncRunProfile
Você pode iniciar um perfil para um Conector desta forma:

```
Invoke-ADSyncRunProfile -ConnectorName "name of connector" -RunProfileName "name of profile"
```

Os nomes a serem usados como [Nomes de conector](how-to-connect-sync-service-manager-ui-connectors.md) e [Nomes de Perfil de Execução](how-to-connect-sync-service-manager-ui-connectors.md#configure-run-profiles) podem ser encontrados na [Interface do Usuário do Synchronization Service Manager](how-to-connect-sync-service-manager-ui.md).

![Invocar perfil de execução](./media/how-to-connect-sync-feature-scheduler/invokerunprofile.png)  

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

![Status de execução do conector](./media/how-to-connect-sync-feature-scheduler/getconnectorrunstatus.png)  
 Na imagem acima, a primeira linha é de um estado em que o mecanismo de sincronização está ocioso. A segunda linha é de quando o Conector do Azure AD está em execução.

## <a name="scheduler-and-installation-wizard"></a>Agendador e o assistente de instalação
Se você iniciar o assistente de instalação, o agendador será temporariamente suspenso. Esse comportamento ocorre porque ele pressupõe que você fará alterações na configuração e as definições não poderão ser aplicadas se o mecanismo de sincronização estiver ativamente em execução. Por esse motivo, não deixe o assistente de instalação aberto, já que ele impede que o mecanismo de sincronização execute ações de sincronização.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre a configuração de [sincronização do Azure AD Connect](how-to-connect-sync-whatis.md) .

Saiba mais sobre [Como integrar suas identidades locais ao Active Directory do Azure](whatis-hybrid-identity.md).
