---
title: 'Azure AD Connect: atualizar de uma versão anterior | Microsoft Docs'
description: Explica os diferentes métodos para atualizar para a versão mais recente do Azure Active Directory Connect, incluindo a atualização in-loco e a migração swing.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 31f084d8-2b89-478c-9079-76cf92e6618f
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: Identity
ms.date: 04/08/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 36b7fce2e2ccb6f331e42e8052ef4fb75d35e831
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98729983"
---
# <a name="azure-ad-connect-upgrade-from-a-previous-version-to-the-latest"></a>Azure AD Connect: atualização de uma versão anterior para a mais recente
Este tópico descreve os diferentes métodos que você pode usar para atualizar sua instalação do Azure Active Directory (Azure AD) Connect para a versão mais recente.  Também é possível usar as etapas descritas na seção [migração Swing](#swing-migration) ao fazer uma alteração significativa na configuração.

>[!NOTE]
> É importante que você mantenha seus servidores atualizados com as versões mais recentes do Azure AD Connect. Estamos constantemente fazendo atualizações para o AADConnect, e essas atualizações incluem correções para problemas de segurança e bugs, bem como melhorias na capacidade de manutenção, desempenho e escalabilidade. Para ver qual é a versão mais recente e saber quais alterações foram feitas entre versões, consulte o histórico de versões de [lançamento](./reference-connect-version-history.md)

>[!NOTE]
> Atualmente, há suporte para atualização de qualquer versão do Azure AD Connect para a versão atual. Não há suporte para atualizações in-loco de DirSync ou ADSync e uma migração Swing é necessária.  Se você quiser atualizar do DirSync, consulte [Atualizar da ferramenta de sincronização do AD do Azure (DirSync)](how-to-dirsync-upgrade-get-started.md) ou da seção de [migração Swing](#swing-migration) .  </br>Na prática, os clientes em versões extremamente antigas podem encontrar problemas não relacionados diretamente ao Azure AD Connect. Os servidores que estão em produção há vários anos, normalmente tinham vários patches aplicados e nem todos eles podem ser contados.  Em geral, os clientes que não fizeram a atualização em 12-18 meses devem considerar uma atualização do swing, pois essa é a opção mais conservadora e menos arriscada.

Se você quiser atualizar do DirSync, confira [Atualizar da ferramenta de sincronização do Azure AD (DirSync)](how-to-dirsync-upgrade-get-started.md).

Há algumas estratégias diferentes que podem ser usadas para atualizar o Azure AD Connect.

| Método | Descrição |
| --- | --- |
| [Atualização automática](how-to-connect-install-automatic-upgrade.md) |Para clientes com uma instalação expressa, esse é o método mais simples. |
| [Atualização in-loco](#in-place-upgrade) |Se você tiver apenas um servidor, é possível atualizar a instalação in-loco no mesmo servidor. |
| [Migração swing](#swing-migration) |Com dois servidores, é possível preparar um dos servidores com a nova versão ou configuração e alterar o servidor ativo quando você estiver pronto. |

Para informações sobre permissões, consulte as [permissões necessárias para uma atualização](reference-connect-accounts-permissions.md#upgrade).

> [!NOTE]
> Depois de habilitar o novo Azure AD Connect Server para iniciar a sincronização de alterações no Azure AD, você não deve reverter para usando o DirSync ou o Azure AD Sync. O downgrade de Azure AD Connect para clientes herdados, incluindo o DirSync e o Azure AD Sync, não tem suporte e pode levar a problemas como perda de dados no Azure AD.

## <a name="in-place-upgrade"></a>Atualização in-loco
Uma atualização in-loco funciona para mudar do Azure AD Sync ou do Azure AD Connect. Ele não funcionará para a migração do DirSync ou para uma solução com o FIM (Forefront Identity Manager) + Azure AD Connector.

Esse será o método preferencial quando você tiver um único servidor e menos de cerca de 100.000 objetos. Se houver várias alterações nas regras de sincronização prontas para uso, uma importação completa e uma sincronização completa ocorrerão após a atualização. Esse método assegura que a nova configuração seja aplicada a todos os objetos existentes no sistema. Essa execução pode levar algumas horas, dependendo do número de objetos no escopo do mecanismo de sincronização. O agendador da sincronização delta normal (que, por padrão, sincroniza a cada 30 minutos), será suspensa, mas a sincronização de senha continuará. Você pode considerar fazer a atualização in-loco durante um final de semana. Se não houver nenhuma alteração na configuração pronta para uso com a nova versão do Azure AD Connect, uma importação/sincronização delta normal será iniciada em vez disso.  
![Atualização in-loco](./media/how-to-upgrade-previous-version/inplaceupgrade.png)

Se você tiver feito alterações nas regras de sincronização prontas, essas regras serão definidas para a configuração padrão na atualização. Para certificar-se de que sua configuração seja mantida entre as atualizações, verifique se as alterações foram feitas como descrito em [Práticas recomendadas para alterar a configuração padrão](how-to-connect-sync-best-practices-changing-default-configuration.md).

Durante a atualização in-loco, poderá haver alterações introduzidas que exijam que atividades de sincronização específicas (incluindo as etapas de importação completa e sincronização completa) sejam executadas após a conclusão da atualização. Para adiar tais atividades, consulte a seção [Como adiar a sincronização completa após a atualização](#how-to-defer-full-synchronization-after-upgrade).

Se estiver usando o Azure AD Connect com um conector não padrão (por exemplo, Conector do LDAP Genérico e Conector do SQL Genérico), atualize a configuração do conector correspondente no [Synchronization Service Manager](./how-to-connect-sync-service-manager-ui-connectors.md) após a atualização in-loco. Para obter detalhes sobre como atualizar a configuração do conector, consulte a seção do artigo [Histórico de lançamento de versão do conector – Solução de problemas](/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-version-history#troubleshooting). Se você não atualizar a configuração, as etapas de execução de importação e exportação não funcionarão corretamente para o conector. Você receberá o seguinte erro no log de eventos do aplicativo com a mensagem *“A versão do assembly na configuração do Conector do AAD (“X.X.XXX.X”) é anterior à versão real (“X.X.XXX.X”) de "C:\Program Files\Microsoft Azure AD Sync\Extensions\Microsoft.IAM.Connector.GenericLdap.dll"”.*

## <a name="swing-migration"></a>Migração swing
Se você tiver uma implantação complexa ou muitos objetos, talvez seja impossível fazer uma atualização in-loco do sistema dinâmico. Para alguns clientes, o processo poderá levar vários dias e, durante esse tempo, nenhuma alteração delta será processada. Você também pode usar esse método quando planejar fazer alterações significativas em sua configuração e quiser experimentá-las antes de enviá-las por push para a nuvem.

O método recomendado para estes cenários é usar uma migração swing. Você precisa de (pelo menos) dois servidores, um servidor ativo e um de preparo. O servidor ativo (exibido com linhas azuis sólidas na imagem abaixo) será responsável pela carga de produção ativa. O servidor de preparo (mostrado com linhas tracejadas roxas) está preparado com a nova versão ou configuração. Quando estiver totalmente pronto, esse servidor ficará ativo. O servidor ativo anterior, agora com a versão ou configuração antiga instalada, se tornará o servidor de preparo e será atualizado.

Os dois servidores podem usar versões diferentes. Por exemplo, o servidor ativo que você planeja desativar pode usar o Azure AD Sync e o novo servidor de preparo pode usar o Azure AD Connect. Se você usar a migração swing para desenvolver uma nova configuração, será uma boa ideia ter as mesmas versões nos dois servidores.  
![Servidor de preparo](./media/how-to-upgrade-previous-version/stagingserver1.png)

> [!NOTE]
> Alguns clientes preferem ter três ou quatro servidores para este cenário. Quando o servidor de preparo é atualizado, você não tem um servidor de backup para a [recuperação de desastres](how-to-connect-sync-staging-server.md#disaster-recovery). Com três ou quatro servidores, pode ser preparar um conjunto de servidores principais/em espera com a nova versão, garantindo que sempre haverá um servidor de preparo pronto para assumir o controle.

Estas etapas também funcionam para mudar do Azure AD Sync ou de uma solução com o FIM + Azure AD Connector. Estas etapas não funcionam para o DirSync, mas o mesmo método de migração swing (também chamado de implantação paralela) com as etapas para o DirSync pode ser encontrado em [Atualizar a sincronização do Azure Active Directory (DirSync)](how-to-dirsync-upgrade-get-started.md).

### <a name="use-a-swing-migration-to-upgrade"></a>Usar uma migração swing para atualizar
1. Se você usar o Azure AD Connect nos dois servidores e planejar fazer apenas uma alteração de configuração, certifique-se de que o servidor ativo e o servidor de preparo estejam usando a mesma versão. Isso facilita a comparação de diferenças mais tarde. Se você estiver atualizando do Azure AD Sync, esses servidores terão versões diferentes. Se você estiver atualizando de uma versão anterior do Azure AD Connect, é uma boa ideia começar com os dois servidores usando a mesma versão, mas isso não é obrigatório.
2. Se você tiver feito alguma configuração personalizada e o servidor de preparo não a tiver, siga as etapas em [Mover a configuração personalizada do servidor ativo para o de preparo](#move-a-custom-configuration-from-the-active-server-to-the-staging-server).
3. Se estiver atualizando de uma versão anterior do Azure AD Connect, atualize o servidor de preparo para a versão mais recente. Se estiver movendo do Azure AD Sync, instale o Azure AD Connect em seu servidor de preparo.
4. Permita que o mecanismo de sincronização execute a importação completa e a sincronização completa em seu servidor de preparo.
5. Verifique se a nova configuração não causou alterações inesperadas usando as etapas descritas em “Verificar” em [Verificar a configuração de um servidor](how-to-connect-sync-staging-server.md#verify-the-configuration-of-a-server). Se algo não acontecer conforme o esperado, siga as etapas para corrigir, executar a importação e a sincronização e verificar os dados até que eles fiquem corretos.
6. Altere o servidor de preparo para que ele passe a ser o servidor ativo. Esta é a etapa final "mudar servidor ativo" em [verificar a configuração de um servidor](how-to-connect-sync-staging-server.md#verify-the-configuration-of-a-server).
7. Se estiver atualizando do Azure AD Connect, atualize o servidor que está no modo de preparo para a versão mais recente. Siga as mesmas etapas anteriores para atualizar os dados e a configuração. Se estiver atualizando do Azure AD Sync, agora você poderá desativar e encerrar o servidor antigo.

### <a name="move-a-custom-configuration-from-the-active-server-to-the-staging-server"></a>Mover uma configuração personalizada do servidor ativo para o servidor de preparo
Se você tiver feito alterações de configuração no servidor ativo, precisará garantir que as mesmas alterações sejam aplicadas ao servidor de preparo. Para ajudar com essa mudança, você pode usar o [Documentador de configuração do Azure AD Connect](https://github.com/Microsoft/AADConnectConfigDocumenter).

Você pode mover as regras de sincronização personalizadas que você criou usando o PowerShell. Você deve aplicar outras alterações da mesma maneira em ambos os sistemas, mas não será possível migrar as alterações. O [documentador de configuração](https://github.com/Microsoft/AADConnectConfigDocumenter) pode ajudá-lo a comparar os dois sistemas para certificar-se de que eles são idênticos. A ferramenta também pode ajudar a automatizar as etapas desta seção.

Você precisa configurar os seguintes itens da mesma maneira em ambos os servidores:

* A conexão às mesmas florestas
* Qualquer filtragem de domínio e de unidade organizacional
* Os mesmos recursos opcionais, como a sincronização de senha e o write-back de senha

**Mover regras de sincronização personalizadas**  
Para mover regras de sincronização personalizadas, faça o seguinte:

1. Abra o **Editor de Regras de Sincronização** em seu servidor ativo.
2. Selecione uma regra personalizada. Clique em **Exportar**. Isso abre uma janela do Bloco de Notas. Salve o arquivo temporário com uma extensão PS1. Isso o transforma em um script do PowerShell. Copie o arquivo PS1 para o servidor de preparo.  
   ![Exportação da regra de sincronização](./media/how-to-upgrade-previous-version/exportrule.png)
3. O GUID do Conector é diferente no servidor de preparo e deve ser alterado. Para obter o GUID, inicie o **Editor de Regras de Sincronização**, escolha uma das regras prontas que representam o mesmo sistema conectado e clique em **Exportar**. Substitua o GUID em seu arquivo PS1 com o GUID do servidor de preparo.
4. Em um prompt do PowerShell, execute o arquivo PS1. Isso cria a regra de sincronização personalizada no servidor de preparo.
5. Repita essa etapa para todas as regras personalizadas.

## <a name="how-to-defer-full-synchronization-after-upgrade"></a>Como adiar a sincronização completa após a atualização
Durante a atualização in-loco, poderá haver alterações introduzidas que exijam que atividades de sincronização específicas (incluindo as etapas de importação completa e sincronização completa) sejam executadas. Por exemplo, as alterações de esquema do conector exigem a etapa de **importação completa** e as alterações de regra de sincronização integradas exigem que a etapa **sincronização completa** seja executada nos conectores afetados. Durante a atualização, o Azure AD Connect determina quais atividades de sincronização são necessárias e registra-as como *substituições*. No ciclo de sincronização seguinte, o agendador de sincronização seleciona essas substituições e executa-as. Quando uma substituição é executada com êxito, ela é removida.

Pode haver situações em que você não deseja que essas substituições ocorram imediatamente após a atualização. Por exemplo, você tem vários objetos sincronizados e deseja que essas etapas de sincronização ocorram depois do horário comercial. Para remover essas substituições:

1. Durante a atualização, **desmarque** a opção **iniciar o processo de sincronização quando a configuração for concluída**. Isso desabilita o agendador de sincronização e impede que o ciclo de sincronização ocorra automaticamente antes que as substituições sejam removidas.

   ![Captura de tela que realça a opção iniciar o processo de sincronização quando a configuração for concluída, que você precisa limpar.](./media/how-to-upgrade-previous-version/disablefullsync01.png)

2. Após a conclusão da atualização, execute o seguinte cmdlet para descobrir quais substituições foram adicionadas: `Get-ADSyncSchedulerConnectorOverride | fl`

   >[!NOTE]
   > As substituições são específicas do conector. No exemplo a seguir, as etapas de importação completa e de sincronização completa foram adicionadas ao AD Connector local e no Azure AD Connector.

   ![DisableFullSyncAfterUpgrade](./media/how-to-upgrade-previous-version/disablefullsync02.png)

3. Anote as substituições existentes que foram adicionadas.
   
4. Para remover as substituições da importação completa e da sincronização completa em um conector qualquer, execute o seguinte cmdlet: `Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier <Guid-of-ConnectorIdentifier> -FullImportRequired $false -FullSyncRequired $false`

   Para remover as substituições de todos os conectores, execute o seguinte script do PowerShell:

   ```
   foreach ($connectorOverride in Get-ADSyncSchedulerConnectorOverride)
   {
       Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier $connectorOverride.ConnectorIdentifier.Guid -FullSyncRequired $false -FullImportRequired $false
   }
   ```

5. Para retomar o agendador, execute o seguinte cmdlet: `Set-ADSyncScheduler -SyncCycleEnabled $true`

   >[!IMPORTANT]
   > Lembre-se de executar as etapas de sincronização necessárias assim que possível. Você pode executar essas etapas manualmente usando o Synchronization Service Manager ou adicionar as substituições novamente usando o cmdlet Set-ADSyncSchedulerConnectorOverride.

Para adicionar as substituições para a importação completa e para a sincronização completa em um conector qualquer, execute o seguinte cmdlet: `Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier <Guid> -FullImportRequired $true -FullSyncRequired $true`

## <a name="troubleshooting"></a>Solução de problemas
A seção a seguir contém solução de problemas e informações que você pode usar se encontrar um problema ao atualizar o Azure AD Connect.

### <a name="azure-active-directory-connector-missing-error-during-azure-ad-connect-upgrade"></a>Erro inexistente do conector do Azure Active Directory durante a atualização do Azure AD Connect

Quando você atualiza o Azure AD Connect de uma versão anterior, pode ocorrer o seguinte erro no início da atualização 

![Erro](./media/how-to-upgrade-previous-version/error1.png)

Esse erro ocorre porque o conector do Azure Active Directory com identificador, b891884f-051e-4a83-95af-2544101c9083, não existe na configuração atual do Azure AD Connect. Para verificar se este é o caso, abra uma janela do PowerShell, execute o Cmdlet `Get-ADSyncConnector -Identifier b891884f-051e-4a83-95af-2544101c9083`

```
PS C:\> Get-ADSyncConnector -Identifier b891884f-051e-4a83-95af-2544101c9083
Get-ADSyncConnector : Operation failed because the specified MA could not be found.
At line:1 char:1
+ Get-ADSyncConnector -Identifier b891884f-051e-4a83-95af-2544101c9083
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : ReadError: (Microsoft.Ident...ConnectorCmdlet:GetADSyncConnectorCmdlet) [Get-ADSyncConne
   ctor], ConnectorNotFoundException
    + FullyQualifiedErrorId : Operation failed because the specified MA could not be found.,Microsoft.IdentityManageme
   nt.PowerShell.Cmdlet.GetADSyncConnectorCmdlet

```

O cmdlet do PowerShell relata o erro **que o MA especificado não pôde ser encontrado**.

Isso ocorre porque a configuração atual do Azure AD Connect não é suportada para atualização. 

Se você deseja instalar uma versão mais recente do Azure AD Connect: feche o assistente do Azure AD Connect, desinstale o Azure AD Connect existente e execute uma instalação limpa do Azure AD Connect mais recente.



## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre como [integrar suas identidades locais com o Azure Active Directory](whatis-hybrid-identity.md).