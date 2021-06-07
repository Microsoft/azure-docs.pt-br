---
title: Habilitar auditorias de segurança para Azure AD Domain Services | Microsoft Docs
description: Saiba como habilitar auditorias de segurança para centralizar o log de eventos para análise e alertas no Azure AD Domain Services
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 662362c3-1a5e-4e94-ae09-8e4254443697
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: caf46850b3d8d6946225575b8a9a732a90847482
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100574136"
---
# <a name="enable-security-audits-for-azure-active-directory-domain-services"></a>Habilitar auditorias de segurança para Azure Active Directory Domain Services

As auditorias de segurança do Azure Active Directory Domain Services (AD DS do Azure) permitem que o Azure transmita eventos de segurança para os recursos de destino. Esses recursos incluem armazenamento do Azure, espaços de trabalho do Log Analytics do Azure ou Hub de eventos do Azure. Depois de habilitar os eventos de auditoria de segurança, o Azure AD DS envia todos os eventos auditados para a categoria selecionada para o recurso de destino.

Você pode arquivar eventos no armazenamento do Azure e transmitir eventos no software SIEM (gerenciamento de eventos e informações de segurança) (ou equivalente) usando os hubs de eventos do Azure ou fazer sua própria análise e usando os espaços de trabalho do Azure Log Analytics do portal do Azure.

> [!IMPORTANT]
> As auditorias de segurança do Azure AD DS só estão disponíveis para domínios gerenciados baseados em Azure Resource Manager. Para obter informações sobre como migrar, consulte [migrar AD DS do Azure do modelo de rede virtual clássica para o Gerenciador de recursos][migrate-azure-adds].

## <a name="security-audit-destinations"></a>Destinos de auditoria de segurança

Você pode usar o armazenamento do Azure, os hubs de eventos do Azure ou os espaços de trabalho do Azure Log Analytics como um recurso de destino para auditorias de segurança de AD DS do Azure. Esses destinos podem ser combinados. Por exemplo, você pode usar o armazenamento do Azure para arquivar eventos de auditoria de segurança, mas um espaço de trabalho do Azure Log Analytics para analisar e relatar as informações em curto prazo.

A tabela a seguir descreve os cenários para cada tipo de recurso de destino.

> [!IMPORTANT]
> Você precisa criar o recurso de destino antes de habilitar as auditorias de segurança do Azure AD DS. Você pode criar esses recursos usando o portal do Azure, Azure PowerShell ou o CLI do Azure.

| Recurso de destino | Cenário |
|:---|:---|
|Armazenamento do Azure| Esse destino deve ser usado quando a principal necessidade é armazenar eventos de auditoria de segurança para fins de arquivamento. Outros destinos podem ser usados para fins de arquivamento, no entanto, esses destinos fornecem recursos além da principal necessidade de arquivamento. <br /><br />Antes de habilitar os eventos de auditoria de segurança do Azure AD DS, primeiro [crie uma conta de armazenamento do Azure](../storage/common/storage-account-create.md).|
|Hubs de eventos do Azure| Esse destino deve ser usado quando a principal necessidade é compartilhar eventos de auditoria de segurança com software adicional, como software de análise de dados ou informações de segurança & software de gerenciamento de eventos (SIEM).<br /><br />Antes de habilitar os eventos de auditoria de segurança do Azure AD DS, [crie um hub de eventos usando portal do Azure](../event-hubs/event-hubs-create.md)|
|Espaço de trabalho Log Analytics do Azure| Esse destino deve ser usado quando a principal necessidade é analisar e revisar as auditorias seguras do portal do Azure diretamente.<br /><br />Antes de habilitar os eventos de auditoria de segurança do Azure AD DS, [crie um espaço de trabalho log Analytics no portal do Azure.](../azure-monitor/logs/quick-create-workspace.md)|

## <a name="enable-security-audit-events-using-the-azure-portal"></a>Habilitar eventos de auditoria de segurança usando o portal do Azure

Para habilitar os eventos de auditoria de segurança do Azure AD DS usando o portal do Azure, conclua as etapas a seguir.

> [!IMPORTANT]
> As auditorias de segurança do Azure AD DS não são retroativas. Você não pode recuperar ou repetir eventos do passado. Os AD DS do Azure só podem enviar eventos que ocorrem após a habilitação das auditorias de segurança.

1. Entre no Portal do Azure em https://portal.azure.com.
1. Na parte superior da portal do Azure, procure e selecione **Azure AD Domain Services**. Escolha o domínio gerenciado, como *aaddscontoso.com*.
1. Na janela AD DS do Azure, selecione **configurações de diagnóstico** no lado esquerdo.
1. Nenhum diagnóstico é configurado por padrão. Para começar, selecione **Adicionar configuração de diagnóstico**.

    ![Adicionar uma configuração de diagnóstico para Azure AD Domain Services](./media/security-audit-events/add-diagnostic-settings.png)

1. Insira um nome para a configuração de diagnóstico, como *aadds*.

    Marque a caixa destino de auditoria de segurança que você deseja. Você pode escolher entre uma conta de armazenamento do Azure, um hub de eventos do Azure ou um espaço de trabalho Log Analytics. Esses recursos de destino já devem existir em sua assinatura do Azure. Você não pode criar os recursos de destino neste assistente.

    ![Habilitar o destino necessário e o tipo de eventos de auditoria a serem capturados](./media/security-audit-events/diagnostic-settings-page.png)

    * **Armazenamento do Azure**
        * Selecione **arquivar em uma conta de armazenamento** e, em seguida, escolha **Configurar**.
        * Selecione a **assinatura** e a **conta de armazenamento** que você deseja usar para arquivar eventos de auditoria de segurança.
        * Quando estiver pronto, escolha **OK**.
    * **Hubs de eventos do Azure**
        * Selecione **fluxo para um hub de eventos** e escolha **Configurar**.
        * Selecione a **assinatura** e o **namespace do hub de eventos**. Se necessário, escolha também um **nome de Hub de eventos** e, em seguida, **nome da política do hub de eventos**.
        * Quando estiver pronto, escolha **OK**.
    * **Espaços de trabalho de análise de logs do Azure**
        * Selecione **Enviar para log Analytics**, em seguida, escolha a **assinatura** e **log Analytics espaço de trabalho** que você deseja usar para armazenar eventos de auditoria de segurança.

1. Selecione as categorias de log que você deseja incluir para o recurso de destino específico. Se você enviar os eventos de auditoria para uma conta de armazenamento do Azure, também poderá configurar uma política de retenção que define o número de dias para reter os dados. Uma configuração padrão de *0* retém todos os dados e não gira eventos após um período de tempo.

    Você pode selecionar diferentes categorias de log para cada recurso de destino em uma única configuração. Essa capacidade permite que você escolha quais categorias de logs você deseja manter Log Analytics e quais categorias de log você deseja arquivar, por exemplo.

1. Quando terminar, selecione **salvar** para confirmar suas alterações. Os recursos de destino começam a receber eventos de auditoria de segurança do Azure AD DS logo após a configuração ser salva.

## <a name="enable-security-audit-events-using-azure-powershell"></a>Habilitar eventos de auditoria de segurança usando Azure PowerShell

Para habilitar os eventos de auditoria de segurança do Azure AD DS usando o Azure PowerShell, conclua as etapas a seguir. Se necessário, primeiro [Instale o módulo Azure PowerShell e conecte-se à sua assinatura do Azure](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> As auditorias de segurança do Azure AD DS não são retroativas. Você não pode recuperar ou repetir eventos do passado. Os AD DS do Azure só podem enviar eventos que ocorrem após a habilitação das auditorias de segurança.

1. Autentique em sua assinatura do Azure usando o cmdlet [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) . Quando solicitado, insira suas credenciais de conta.

    ```azurepowershell
    Connect-AzAccount
    ```

1. Crie o recurso de destino para os eventos de auditoria de segurança.

    * **Armazenamento**  -  do Azure [Criar uma conta de armazenamento usando Azure PowerShell](../storage/common/storage-account-create.md?tabs=azure-powershell)
    * Hubs de eventos **do Azure**  -  [Crie um hub de eventos usando Azure PowerShell](../event-hubs/event-hubs-quickstart-powershell.md). Talvez você também precise usar o cmdlet [New-AzEventHubAuthorizationRule](/powershell/module/az.eventhub/new-azeventhubauthorizationrule) para criar uma regra de autorização que conceda permissões de AD DS do Azure para o *namespace* do hub de eventos. A regra de autorização deve incluir os direitos **gerenciar**, **escutar** e **Enviar** .

        > [!IMPORTANT]
        > Verifique se você definiu a regra de autorização no namespace do hub de eventos e não no próprio Hub de eventos.

    * **Espaços de trabalho**  -  de análise de logs do Azure [Crie um espaço de trabalho log Analytics com Azure PowerShell](../azure-monitor/logs/powershell-workspace-configuration.md).

1. Obtenha a ID de recurso para seu domínio gerenciado AD DS do Azure usando o cmdlet [Get-AzResource](/powershell/module/Az.Resources/Get-AzResource) . Crie uma variável chamada *$aadds. ResourceId* para manter o valor:

    ```azurepowershell
    $aadds = Get-AzResource -name aaddsDomainName
    ```

1. Defina as configurações de diagnóstico do Azure usando o cmdlet [set-AzDiagnosticSetting](/powershell/module/Az.Monitor/Set-AzDiagnosticSetting) para usar o recurso de destino para Azure AD Domain Services eventos de auditoria de segurança. Nos exemplos a seguir, a variável *$aadds. ResourceId* é usado na etapa anterior.

    * **Armazenamento do Azure** -substitua *storageAccountId* pelo nome da sua conta de armazenamento:

        ```powershell
        Set-AzDiagnosticSetting `
            -ResourceId $aadds.ResourceId `
            -StorageAccountId storageAccountId `
            -Enabled $true
        ```

    * **Hubs de eventos do Azure** – substitua *eventHubName* pelo nome do hub de eventos e *eventHubRuleId* pela sua ID de regra de autorização:

        ```powershell
        Set-AzDiagnosticSetting -ResourceId $aadds.ResourceId `
            -EventHubName eventHubName `
            -EventHubAuthorizationRuleId eventHubRuleId `
            -Enabled $true
        ```

    * **Espaços de trabalho analíticos de logs do Azure** – substitua *workspaceid* pela ID do espaço de trabalho log Analytics:

        ```powershell
        Set-AzureRmDiagnosticSetting -ResourceId $aadds.ResourceId `
            -WorkspaceID workspaceId `
            -Enabled $true
        ```

## <a name="query-and-view-security-audit-events-using-azure-monitor"></a>Consultar e exibir eventos de auditoria de segurança usando Azure Monitor

Os espaços de trabalho de análise de log permitem exibir e analisar os eventos de auditoria de segurança usando Azure Monitor e a linguagem de consulta Kusto. Essa linguagem de consulta é projetada para uso somente leitura que apresenta recursos de análise de energia com uma sintaxe de fácil leitura. Para obter mais informações sobre como começar a usar as linguagens de consulta do Kusto, consulte os seguintes artigos:

* [Documentação do Azure Monitor](../azure-monitor/index.yml)
* [Introdução ao Log Analytics no Azure Monitor](../azure-monitor/logs/log-analytics-tutorial.md)
* [Introdução às consultas de log no Azure Monitor](../azure-monitor/logs/get-started-queries.md)
* [Criar e compartilhar painéis de dados do Log Analytics](../azure-monitor/visualize/tutorial-logs-dashboards.md)

As consultas de exemplo a seguir podem ser usadas para iniciar a análise de eventos de auditoria de segurança do Azure AD DS.

### <a name="sample-query-1"></a>Exemplo de consulta 1

Exibir todos os eventos de bloqueio de conta dos últimos sete dias:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
```

### <a name="sample-query-2"></a>Exemplo de consulta 2

Exibir todos os eventos de bloqueio de conta (*4740*) entre 3 de junho de 2020 às 9h 10 de junho de 2020, meia-noite, classificada em ordem crescente pela data e hora:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= datetime(2020-06-03 09:00) and TimeGenerated <= datetime(2020-06-10)
| where OperationName has "4740"
| sort by TimeGenerated asc
```

### <a name="sample-query-3"></a>Exemplo de consulta 3

Exibir eventos de entrada de conta sete dias atrás (de agora) para a conta denominada usuário:

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-4"></a>Exemplo de consulta 4

Exibir eventos de entrada de conta sete dias atrás de agora para a conta chamada usuário que tentou entrar usando uma senha inadequada (*0xC0000006a*):

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc000006a" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-5"></a>Exemplo de consulta 5

Exibir eventos de entrada de conta sete dias atrás de agora para a conta chamada usuário que tentou entrar enquanto a conta estava bloqueada (*0xC0000234*):

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-6"></a>Exemplo de consulta 6

Exibir o número de eventos de entrada de conta sete dias atrás de agora para todas as tentativas de entrada que ocorreram para todos os usuários bloqueados:

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| summarize count()
```

## <a name="audit-event-categories"></a>Categorias de eventos de auditoria

As auditorias de segurança do Azure AD DS se alinham à auditoria tradicional para controladores de domínio AD DS tradicionais. Em ambientes híbridos, você pode reutilizar os padrões de auditoria existentes para que a mesma lógica possa ser usada ao analisar os eventos. Dependendo do cenário que você precisa para solucionar problemas ou analisar, as diferentes categorias de evento de auditoria precisam ser direcionadas.

As seguintes categorias de evento de auditoria estão disponíveis:

| Nome da categoria de auditoria | Descrição |
|:---|:---|
| Logon da conta|As tentativas de auditoria de autenticar dados de conta em um controlador de domínio ou em um SAM (Gerenciador de contas de segurança) local.</p>As configurações e os eventos da política de logon e logoff do controlam as tentativas de acessar um computador específico. As configurações e os eventos nesta categoria se concentram no banco de dados da conta usado. Essa categoria inclui as seguintes subcategorias:<ul><li>[Auditoria da validação de credenciais](/windows/security/threat-protection/auditing/audit-credential-validation)</li><li>[Auditoria do serviço de autenticação Kerberos](/windows/security/threat-protection/auditing/audit-kerberos-authentication-service)</li><li>[Auditoria das operações do tíquete de serviço Kerberos](/windows/security/threat-protection/auditing/audit-kerberos-service-ticket-operations)</li><li>[Auditoria de outros eventos de logon/logoff](/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li></ul>|
| Gerenciamento de Contas|Audita alterações em contas e grupos de usuários e computadores. Essa categoria inclui as seguintes subcategorias:<ul><li>[Auditoria do gerenciamento de grupo de aplicativos](/windows/security/threat-protection/auditing/audit-application-group-management)</li><li>[Auditoria do gerenciamento da conta de computador](/windows/security/threat-protection/auditing/audit-computer-account-management)</li><li>[Auditoria do gerenciamento do grupo de distribuição](/windows/security/threat-protection/auditing/audit-distribution-group-management)</li><li>[Auditar o gerenciamento de contas](/windows/security/threat-protection/auditing/audit-other-account-management-events)</li><li>[Auditoria de gerenciamento do grupo de distribuição](/windows/security/threat-protection/auditing/audit-security-group-management)</li><li>[Auditoria de gerenciamento de conta de usuário](/windows/security/threat-protection/auditing/audit-user-account-management)</li></ul>|
| Acompanhamento de detalhes|Audita atividades de aplicativos e usuários individuais nesse computador e para entender como um computador está sendo usado. Essa categoria inclui as seguintes subcategorias:<ul><li>[Auditoria da atividade DPAPI](/windows/security/threat-protection/auditing/audit-dpapi-activity)</li><li>[Auditar atividade de PNP](/windows/security/threat-protection/auditing/audit-pnp-activity)</li><li>[Auditoria do processo de criação](/windows/security/threat-protection/auditing/audit-process-creation)</li><li>[Auditoria do encerramento do processo](/windows/security/threat-protection/auditing/audit-process-termination)</li><li>[Auditoria de eventos de RPC](/windows/security/threat-protection/auditing/audit-rpc-events)</li></ul>|
| Acesso aos serviços de diretório|As tentativas de auditoria para acessar e modificar objetos no Active Directory Domain Services (AD DS). Esses eventos de auditoria são registrados somente em controladores de domínio. Essa categoria inclui as seguintes subcategorias:<ul><li>[Auditoria de replicação detalhada do serviço de diretório](/windows/security/threat-protection/auditing/audit-detailed-directory-service-replication)</li><li>[Auditoria de acesso do serviço de diretório](/windows/security/threat-protection/auditing/audit-directory-service-access)</li><li>[Auditoria de mudanças do serviço de diretório](/windows/security/threat-protection/auditing/audit-directory-service-changes)</li><li>[Auditoria de replicação do serviço de diretório](/windows/security/threat-protection/auditing/audit-directory-service-replication)</li></ul>|
| Logon-Logoff|As tentativas de auditoria para fazer logon em um computador interativamente ou em uma rede. Esses eventos são úteis para acompanhar a atividade do usuário e identificar possíveis ataques em recursos de rede. Essa categoria inclui as seguintes subcategorias:<ul><li>[Auditoria de bloqueio de conta](/windows/security/threat-protection/auditing/audit-account-lockout)</li><li>[Auditoria das declarações de dispositivo/usuário](/windows/security/threat-protection/auditing/audit-user-device-claims)</li><li>[Auditoria do modo IPsec estendido](/windows/security/threat-protection/auditing/audit-ipsec-extended-mode)</li><li>[Auditoria da associação a um grupo](/windows/security/threat-protection/auditing/audit-group-membership)</li><li>[Auditoria do modo IPsec principal](/windows/security/threat-protection/auditing/audit-ipsec-main-mode)</li><li>[Auditoria do modo IPsec rápido](/windows/security/threat-protection/auditing/audit-ipsec-quick-mode)</li><li>[Auditoria de logoff](/windows/security/threat-protection/auditing/audit-logoff)</li><li>[Auditoria de logon](/windows/security/threat-protection/auditing/audit-logon)</li><li>[Auditoria do Servidor de Políticas de Rede](/windows/security/threat-protection/auditing/audit-network-policy-server)</li><li>[Auditoria de outros eventos de logon/logoff](/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li><li>[Auditoria de logon especial](/windows/security/threat-protection/auditing/audit-special-logon)</li></ul>|
|Acesso a objetos| As tentativas de auditoria para acessar objetos específicos ou tipos de objetos em uma rede ou computador. Essa categoria inclui as seguintes subcategorias:<ul><li>[Auditoria de aplicativo gerado](/windows/security/threat-protection/auditing/audit-application-generated)</li><li>[Auditoria de serviços de certificação](/windows/security/threat-protection/auditing/audit-certification-services)</li><li>[Auditoria de compartilhamento de arquivos detalhado](/windows/security/threat-protection/auditing/audit-detailed-file-share)</li><li>[Auditoria de compartilhamento de arquivos](/windows/security/threat-protection/auditing/audit-file-share)</li><li>[Sistema de arquivos de auditoria](/windows/security/threat-protection/auditing/audit-file-system)</li><li>[Auditoria de conexão de plataforma de filtragem](/windows/security/threat-protection/auditing/audit-filtering-platform-connection)</li><li>[Auditoria de descarte de pacote de plataforma de filtragem](/windows/security/threat-protection/auditing/audit-filtering-platform-packet-drop)</li><li>[Manipulação de identificadores de auditoria](/windows/security/threat-protection/auditing/audit-handle-manipulation)</li><li>[Objeto de kernel de auditoria](/windows/security/threat-protection/auditing/audit-kernel-object)</li><li>[Auditoria de outros eventos de acesso ao objeto](/windows/security/threat-protection/auditing/audit-other-object-access-events)</li><li>[Registro de auditoria](/windows/security/threat-protection/auditing/audit-registry)</li><li>[Auditoria de armazenamento removível](/windows/security/threat-protection/auditing/audit-removable-storage)</li><li>[Auditoria de SAM](/windows/security/threat-protection/auditing/audit-sam)</li><li>[Auditoria do preparo da política de acesso central](/windows/security/threat-protection/auditing/audit-central-access-policy-staging)</li></ul>|
|Alteração de política|Audita alterações em políticas de segurança importantes em um sistema local ou rede. As políticas normalmente são estabelecidas por administradores para ajudar a proteger os recursos de rede. O monitoramento de alterações ou tentativas de alterar essas políticas pode ser um aspecto importante do gerenciamento de segurança para uma rede. Essa categoria inclui as seguintes subcategorias:<ul><li>[Auditoria da mudança na política de auditoria](/windows/security/threat-protection/auditing/audit-audit-policy-change)</li><li>[Auditoria da mudança na política de autenticação](/windows/security/threat-protection/auditing/audit-authentication-policy-change)</li><li>[Auditoria da mudança na política de autorização](/windows/security/threat-protection/auditing/audit-authorization-policy-change)</li><li>[Auditoria da mudança na política de plataforma de filtragem](/windows/security/threat-protection/auditing/audit-filtering-platform-policy-change)</li><li>[Auditoria da mudança na política de nível de regra MPSSVC](/windows/security/threat-protection/auditing/audit-mpssvc-rule-level-policy-change)</li><li>[Auditar outra alteração de política](/windows/security/threat-protection/auditing/audit-other-policy-change-events)</li></ul>|
|Uso de privilégios| Audita o uso de determinadas permissões em um ou mais sistemas. Essa categoria inclui as seguintes subcategorias:<ul><li>[Auditoria do uso de privilégios não confidenciais](/windows/security/threat-protection/auditing/audit-non-sensitive-privilege-use)</li><li>[Auditoria do uso de privilégios confidenciais](/windows/security/threat-protection/auditing/audit-sensitive-privilege-use)</li><li>[Auditoria de outros eventos de uso de privilégios](/windows/security/threat-protection/auditing/audit-other-privilege-use-events)</li></ul>|
|Sistema| Audita alterações no nível do sistema em um computador não incluído em outras categorias e que têm possíveis implicações de segurança. Essa categoria inclui as seguintes subcategorias:<ul><li>[Auditoria do driver IPsec](/windows/security/threat-protection/auditing/audit-ipsec-driver)</li><li>[Auditoria de outros eventos do sistema](/windows/security/threat-protection/auditing/audit-other-system-events)</li><li>[Auditoria da mudança no estado de segurança](/windows/security/threat-protection/auditing/audit-security-state-change)</li><li>[Auditoria da extensão do sistema de segurança](/windows/security/threat-protection/auditing/audit-security-system-extension)</li><li>[Auditoria da integridade do sistema](/windows/security/threat-protection/auditing/audit-system-integrity)</li></ul>|

## <a name="event-ids-per-category"></a>IDs de evento por categoria

 As auditorias de segurança AD DS do Azure registram as seguintes IDs de evento quando a ação específica dispara um evento auditável:

| Nome da categoria de evento | IDs de eventos |
|:---|:---|
|Segurança de logon da conta|4767, 4774, 4775, 4776, 4777|
|Segurança de gerenciamento de conta|4720, 4722, 4723, 4724, 4725, 4726, 4727, 4728, 4729, 4730, 4731, 4732, 4733, 4734, 4735, 4737, 4738, 4740, 4741, 4742, 4743, 4754, 4755, 4756, 4757, 4758, 4764, 4765, 4766, 4780, 4781, 4782, 4793, 4798, 4799, 5376, 5377|
|Segurança de acompanhamento de detalhes|Nenhum|
|Segurança de acesso DS|5136, 5137, 5138, 5139, 5141|
|Segurança de Logon-Logoff|4624, 4625, 4634, 4647, 4648, 4672, 4675, 4964|
|Segurança de acesso a objeto|Nenhum|
|Segurança de alteração de política|4670, 4703, 4704, 4705, 4706, 4707, 4713, 4715, 4716, 4717, 4718, 4719, 4739, 4864, 4865, 4866, 4867, 4904, 4906, 4911, 4912|
|Segurança de uso de privilégios|4985|
|Segurança do sistema|4612, 4621|

## <a name="next-steps"></a>Próximas etapas

Para obter informações específicas sobre o Kusto, consulte os seguintes artigos:

* [Visão geral](/azure/kusto/query/) da linguagem de consulta do Kusto.
* [Kusto tutorial](/azure/kusto/query/tutorial) para familiarizar você com noções básicas de consulta.
* [Exemplos de consultas](/azure/kusto/query/samples) que ajudam você a aprender novas maneiras de ver seus dados.
* Kusto [práticas recomendadas](/azure/kusto/query/best-practices) para otimizar suas consultas para obter êxito.

<!-- LINKS - Internal -->
[migrate-azure-adds]: migrate-from-classic-vnet.md