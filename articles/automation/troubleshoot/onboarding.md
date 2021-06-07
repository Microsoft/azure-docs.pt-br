---
title: Solucionar problemas de implantação do recurso de Automação do Azure
description: Este artigo informa como solucionar e resolver problemas que surgem ao implantar os recursos de Automação do Azure.
services: automation
ms.date: 02/11/2021
ms.topic: troubleshooting
ms.openlocfilehash: 43bc367be9ad9bb32f33f94df774acb3e808182a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100651112"
---
# <a name="troubleshoot-feature-deployment-issues"></a>Solucionar problemas de implantação de recurso

Você pode receber mensagens de erro ao implantar o recurso de Gerenciamento de Atualizações de Automação do Azure ou o recurso de Controle de Alterações e Inventário em suas VMs. Este artigo descreve os erros que podem ocorrer e como resolvê-los.

## <a name="known-issues"></a>Problemas conhecidos

### <a name="scenario-renaming-a-registered-node-requires-unregister-or-register-again"></a><a name="node-rename"></a>Cenário: Renomear um nó registrado requer cancelar o registro ou registrar novamente

#### <a name="issue"></a>Problema

Um nó é registrado na Automação do Azure e, em seguida, o nome do computador do sistema operacional é alterado. Os relatórios do nó continuam a aparecer com o nome original.

#### <a name="cause"></a>Causa

Renomear nós registrados não atualiza o nome do nó na Automação do Azure.

#### <a name="resolution"></a>Resolução

Cancele o registro do nó State Configuration da Automação do Azure e registre-o novamente. Os relatórios publicados no serviço antes dessa hora não estarão mais disponíveis.

### <a name="scenario-re-signing-certificates-via-https-proxy-isnt-supported"></a><a name="resigning-cert"></a>Cenário: Não há suporte para a assinatura de certificados novamente por proxy HTTPS

#### <a name="issue"></a>Problema

Quando você se conecta por meio de um proxy que termina o tráfego HTTPS e, em seguida, criptografa novamente o tráfego usando um novo certificado, o serviço não permite a conexão.

#### <a name="cause"></a>Causa

A Automação do Azure não dá suporte a nova assinatura de certificados usada para criptografar o tráfego.

#### <a name="resolution"></a>Resolução

Atualmente, não há nenhuma solução alternativa para esse problema.

## <a name="general-errors"></a>Erros gerais

### <a name="scenario-feature-deployment-fails-with-the-message-the-solution-cannot-be-enabled"></a><a name="missing-write-permissions"></a>Cenário: A implantação do recurso falhará com a mensagem “A solução não pode ser habilitada”

#### <a name="issue"></a>Problema

Você recebe uma das seguintes mensagens ao tentar habilitar um recurso em uma VM:

```error
The solution cannot be enabled due to missing permissions for the virtual machine or deployments
```

```error
The solution cannot be enabled on this VM because the permission to read the workspace is missing
```

#### <a name="cause"></a>Causa

Esse erro é causado por permissões incorretas ou ausentes na VM ou espaço de trabalho ou para o usuário.

#### <a name="resolution"></a>Resolução

Verifique se você tem as [permissões de implantação de recurso](../automation-role-based-access-control.md#feature-setup-permissions) corretas e tente implantar o recurso novamente. Se você receber a mensagem de erro `The solution cannot be enabled on this VM because the permission to read the workspace is missing` , consulte as [informações de solução de problemas](update-management.md#failed-to-enable-error)a seguir.

### <a name="scenario-feature-deployment-fails-with-the-message-failed-to-configure-automation-account-for-diagnostic-logging"></a><a name="diagnostic-logging"></a>Cenário: Falha na implantação de recurso com a mensagem "falha ao configurar a conta de automação para log de diagnóstico"

#### <a name="issue"></a>Problema

Você recebe a seguinte mensagem quando tenta habilitar um recurso em uma VM:

```error
Failed to configure automation account for diagnostic logging
```

#### <a name="cause"></a>Causa

Este erro pode ser causado se o tipo de preço não corresponder ao modelo de cobrança da assinatura. Para saber mais, confira [Monitorar o uso e os custos estimados no Azure Monitor](../../azure-monitor//usage-estimated-costs.md).

#### <a name="resolution"></a>Resolução

Crie manualmente seu espaço de trabalho do Log Analytics e repita o processo de implantação do recurso para selecionar o espaço de trabalho criado.

### <a name="scenario-computergroupqueryformaterror"></a><a name="computer-group-query-format-error"></a>Cenário: ComputerGroupQueryFormatError

#### <a name="issue"></a>Problema

Esse código de erro significa que a consulta de pesquisa salva referente ao grupo de computadores e usada para o recurso de destino não foi formatada corretamente. 

#### <a name="cause"></a>Causa

Você pode ter alterado a consulta ou o sistema pode tê-la alterado.

#### <a name="resolution"></a>Resolução

Você pode excluir a consulta para o recurso e, em seguida, habilitar o recurso novamente, o que recria a consulta. A consulta pode ser encontrada em seu espaço de trabalho, em **Pesquisas salvas**. O nome da consulta é **MicrosoftDefaultComputerGroup**, e a categoria da consulta é o nome do recurso associado. Se vários recursos estiverem habilitados, a consulta **MicrosoftDefaultComputerGroup** mostra várias vezes em **Pesquisas salvas**.

### <a name="scenario-policyviolation"></a><a name="policy-violation"></a>Cenário: PolicyViolation

#### <a name="issue"></a>Problema

Esse código de erro indica que a implantação falhou devido à violação de uma ou mais políticas.

#### <a name="cause"></a>Causa 

Uma política está impedindo que a operação seja concluída.

#### <a name="resolution"></a>Resolução

Para implantar o recurso com êxito, você precisa considerar alterar a política indicada. Como há muitos tipos diferentes de políticas que podem ser definidas, as alterações necessárias dependem da política violada. Por exemplo, se uma política for definida em um grupo de recursos que nega permissão para alterar o conteúdo de alguns recursos contidos, você poderá escolher uma destas correções:

* Remover completamente a política.
* Tente habilitar o recurso para um grupo de recursos diferente.
* Redirecione a política para um recurso específico, por exemplo, uma conta de automação.
* Revise o conjunto de recursos ao qual a política foi configurada para negar.

Verifique as notificações no canto superior direito do portal do Azure ou navegue para o grupo de recursos que contém sua conta de automação e selecione **Implantações** em **Configurações** para exibir a implantação com falha. Para saber mais sobre o Azure Policy, confira [Visão geral do Azure Policy](../../governance/policy/overview.md?toc=%2fazure%2fautomation%2ftoc.json).

### <a name="scenario-errors-trying-to-unlink-a-workspace"></a><a name="unlink"></a>Cenário: Erros ao tentar desvincular um espaço de trabalho

#### <a name="issue"></a>Problema

Você recebe a seguinte mensagem de erro quando tenta desvincular um espaço de trabalho:

```error
The link cannot be updated or deleted because it is linked to Update Management and/or ChangeTracking Solutions.
```

#### <a name="cause"></a>Causa

Esse erro ocorre quando você ainda tem recursos ativos em seu espaço de trabalho do Log Analytics que dependem de sua conta de Automação e espaço de trabalho do Log Analytics que está sendo vinculado.

### <a name="resolution"></a>Resolução

Remova os recursos dos seguintes recursos do seu espaço de trabalho se você os estiver usando:

* Gerenciamento de atualizações
* Controle de Alterações e Inventário
* Iniciar/Parar VMs durante os horários inativos

Depois de remover os recursos, você poderá desvincular seu espaço de trabalho. É importante limpar todos os artefatos existentes desses recursos do seu espaço de trabalho e sua conta de automação:

* Para Gerenciamento de Atualizações, remova **Atualizar implantações (agendas)** da sua conta de automação.
* Para Iniciar/Parar VMs fora do horário comercial, remova os bloqueios nos componentes de recurso em sua conta de automação em **Configurações** > **Bloqueios**. Para saber mais, confira [Remover o recurso](../automation-solution-vm-management-remove.md).

## <a name="log-analytics-for-windows-extension-failures"></a><a name="mma-extension-failures"></a>Log Analytics para falhas de extensão do Windows

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

Uma instalação do agente do Log Analytics para extensão do Windows pode falhar por vários motivos. A seção a seguir descreve os problemas de implantação de recursos que podem causar falhas durante a implantação da extensão do agente de Log Analytics para Windows.

>[!NOTE]
>O agente de Log Analytics para Windows é o nome usado atualmente na Automação do Azure para o Microsoft Monitoring Agent (MMA).

### <a name="scenario-an-exception-occurred-during-a-webclient-request"></a><a name="webclient-exception"></a>Cenário: Ocorreu uma exceção durante uma solicitação do WebClient

A extensão do Log Analytics para Windows na VM não consegue se comunicar com recursos externos e a implantação falha.

#### <a name="issue"></a>Problema

A seguir, exemplos de mensagens de erro retornadas:

```error
Please verify the VM has a running VM agent, and can establish outbound connections to Azure storage.
```

```error
'Manifest download error from https://<endpoint>/<endpointId>/Microsoft.EnterpriseCloud.Monitoring_MicrosoftMonitoringAgent_australiaeast_manifest.xml. Error: UnknownError. An exception occurred during a WebClient request.
```

#### <a name="cause"></a>Causa

Algumas causas possíveis desse erro são:

* Um proxy configurado na VM permite apenas portas específicas.
* Uma configuração de firewall bloqueou o acesso às portas e endereços necessários.

#### <a name="resolution"></a>Resolução

Certifique-se de ter as portas e os endereços adequados abertos para comunicação. Para obter uma lista de portas e endereços, confira [planejando sua rede](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="scenario-install-failed-because-of-transient-environment-issues"></a><a name="transient-environment-issue"></a>Cenário: A instalação falhou devido a problemas transitórios de ambiente

A instalação da extensão do Log Analytics para Windows falhou durante a implantação porque outra instalação ou ação está bloqueando a instalação.

#### <a name="issue"></a>Problema

A seguir, exemplos de mensagens de erro que podem ser retornadas:

```error
The Microsoft Monitoring Agent failed to install on this machine. Please try to uninstall and reinstall the extension. If the issue persists, please contact support.
```

```error
'Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 1618'
```

```error
'Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.2) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.2\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 1601'
```

#### <a name="cause"></a>Causa

Algumas causas possíveis desse erro são:

* Outra instalação está em progresso.
* O sistema foi acionado para reiniciar durante a implantação do modelo.

#### <a name="resolution"></a>Resolução

Esse erro é transitório por natureza. Repita a implantação para instalar a extensão.

### <a name="scenario-installation-timeout"></a><a name="installation-timeout"></a>Cenário: Tempo limite de instalação

A instalação da extensão do Log Analytics para Windows não foi concluída devido a um tempo limite.

#### <a name="issue"></a>Problema

Segue-se um exemplo de uma mensagem de erro que pode ser devolvida:

```error
Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 15614
```

#### <a name="cause"></a>Causa

Esse tipo de erro ocorre porque a VM está sob uma carga pesada durante a instalação.

### <a name="resolution"></a>Resolução

Tente instalar a extensão do agente de Log Analytics para Windows quando a VM estiver sob uma carga inferior.

## <a name="next-steps"></a>Próximas etapas

Se você não encontrar seu problema aqui ou não conseguir resolvê-lo, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure nos [Fóruns do Azure](https://azure.microsoft.com/support/forums/).
* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport), a conta oficial do Microsoft Azure para melhorar a experiência do cliente. O Suporte do Azure conecta a Comunidade do Azure a respostas, suporte e especialistas.
* Registrar um incidente de suporte do Azure. Acesse o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **Obter suporte**.