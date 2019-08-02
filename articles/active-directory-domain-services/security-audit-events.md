---
title: Habilitar auditorias de segurança para serviços de domínio do AD do Azure | Microsoft Docs
description: Habilitar auditorias de segurança para serviços de domínio do AD do Azure
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 662362c3-1a5e-4e94-ae09-8e4254443697
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: iainfou
ms.openlocfilehash: 3105296b3c670d3d44789c93878fa1fc6076973b
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/04/2019
ms.locfileid: "67566489"
---
# <a name="enable-security-audits-for-azure-ad-domain-services-preview"></a>Habilitar auditorias de segurança para serviços de domínio do AD do Azure (visualização)
Auditoria de segurança de serviço de domínio do AD do Azure permite aos clientes usar o portal de serviços de domínio do AD do Azure para transmitir eventos de auditoria de segurança para recursos de destino. Os recursos que podem receber esses eventos incluem o armazenamento do Azure, os espaços de trabalho do Log Analytics do Azure ou Hub de eventos do Azure. Logo depois de habilitar eventos de auditoria de segurança, o serviço de domínio do Azure AD envia todos os eventos auditados para a categoria selecionada para o recurso de destino. Eventos de auditoria de segurança permitem que os clientes eventos auditado de arquivamento no armazenamento do Azure. Além disso, os clientes podem transmitir eventos em segurança informações e eventos software de gerenciamento (SIEM) (ou equivalente) usando hubs de eventos ou fazer sua própria análise e insights usando o Azure Log Analytics do portal do Azure. 

> [!IMPORTANT]
> Segurança e auditoria do Azure AD Domain Services está disponível apenas em instâncias com base no Azure Resource Manager para o Azure AD Domain Services.
>
>

## <a name="auditing-event-categories"></a>Categorias de evento de auditoria
Segurança e auditoria do Azure AD Domain Services se alinha com auditoria tradicional que vem para controladores de domínio do Active Directory Domain Services. Reutilizar os padrões de auditoria existentes garante que a mesma lógica pode ser usada ao analisar os eventos. Segurança e auditoria do Azure AD Domain Services inclui as seguintes categorias de evento.

| Nome da categoria de auditoria | DESCRIÇÃO |
|:---|:---|
| Logon de conta|Audita as tentativas de autenticação de dados da conta em um controlador de domínio ou em uma segurança contas Manager (SAM) local.</p>Eventos e as configurações de política de Logoff e logon rastrear tentativas de acesso a um determinado computador. As configurações e eventos nesta categoria enfocam a conta de banco de dados que é usada. Essa categoria inclui as seguintes subcategorias:<ul><li>[Validação de credenciais de auditoria](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-credential-validation)</li><li>[Serviço de autenticação do Kerberos de auditoria](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kerberos-authentication-service)</li><li>[Auditar operações do tíquete de serviço Kerberos](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kerberos-service-ticket-operations)</li><li>[Auditoria de outros eventos de Logon/Logoff](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li></ul>|
| Gerenciamento de Conta|As auditorias altera a grupos e contas de usuário e computador. Essa categoria inclui as seguintes subcategorias:<ul><li>[Gerenciamento de grupo de aplicativos de auditoria](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-application-group-management)</li><li>[Auditoria de gerenciamento de conta de computador](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-computer-account-management)</li><li>[Gerenciamento de grupo de distribuição de auditoria](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-distribution-group-management)</li><li>[Outro gerenciamento de conta de auditoria](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-account-management-events)</li><li>[Gerenciamento de grupo de segurança de auditoria](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-group-management)</li><li>[Auditoria de gerenciamento de conta de usuário](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-user-account-management)</li></ul>|
| Rastreamento de detalhes|Audita as atividades de aplicativos individuais e os usuários no computador em questão e entender como um computador está sendo usado. Essa categoria inclui as seguintes subcategorias:<ul><li>[Auditoria da atividade DPAPI](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-dpapi-activity)</li><li>[Auditar a atividade de PNP](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-pnp-activity)</li><li>[Criação do processo de auditoria](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-process-creation)</li><li>[Encerramento do processo de auditoria](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-process-termination)</li><li>[Auditoria de eventos de RPC](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-rpc-events)</li></ul>|
| Acesso de serviços de diretório|Auditorias de tentativas de acessar e modificar objetos no Active Directory Domain Services (AD DS). Esses eventos são registrados em controladores de domínio de auditoria. Essa categoria inclui as seguintes subcategorias:<ul><li>[Replicação do serviço de diretório detalhados de auditoria](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-detailed-directory-service-replication)</li><li>[Auditoria de acesso do serviço de diretório](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-access)</li><li>[Alterações de serviço de diretório de auditoria](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-changes)</li><li>[Auditoria de replicação do serviço de diretório](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-replication)</li></ul>|
| Logon de Logoff|Audita as tentativas de logon em um computador de forma interativa ou através de uma rede. Esses eventos são úteis para rastrear a atividade de usuário e a identificação de possíveis ataques em recursos de rede. Essa categoria inclui as seguintes subcategorias:<ul><li>[Bloqueio de conta de auditoria](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-account-lockout)</li><li>[Declarações de usuário/dispositivo de auditoria](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-user-device-claims)</li><li>[Auditoria modo IPsec estendido](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-extended-mode)</li><li>[Associação de grupo de auditoria](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-group-membership)</li><li>[Auditoria do modo IPsec principal](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-main-mode)</li><li>[Auditoria do modo IPsec rápido](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-quick-mode)</li><li>[Auditoria de Logoff](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-logoff)</li><li>[Auditoria de Logon](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-logon)</li><li>[Servidor de políticas de rede de auditoria](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-network-policy-server)</li><li>[Auditoria de outros eventos de Logon/Logoff](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li><li>[Auditoria de Logon especial](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-special-logon)</li></ul>|
|Acesso a objetos| Auditorias tentam acessar objetos específicos ou tipos de objetos em um computador ou rede. Essa categoria inclui as seguintes subcategorias:<ul><li>[Auditoria de aplicativo gerado](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-application-generated)</li><li>[Serviços de certificação de auditoria](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-certification-services)</li><li>[Auditoria de compartilhamento de arquivos detalhado](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-detailed-file-share)</li><li>[Compartilhamento de arquivos de auditoria](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-file-share)</li><li>[Sistema de arquivos de auditoria](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-file-system)</li><li>[Conexão de plataforma de filtragem de auditoria](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-connection)</li><li>[Remoção de pacote de plataforma de filtragem de auditoria](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-packet-drop)</li><li>[Auditoria de manipulação de identificador](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-handle-manipulation)</li><li>[Objeto de Kernel de auditoria](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kernel-object)</li><li>[Auditoria de outros eventos de acesso do objeto](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-object-access-events)</li><li>[Registro de auditoria](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-registry)</li><li>[Armazenamento removível de auditoria](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-removable-storage)</li><li>[Auditoria de SAM](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-sam)</li><li>[Auditoria de preparo da política de acesso Central](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-central-access-policy-staging)</li></ul>|
|Alteração da política|Auditorias altera as políticas de segurança importante em uma rede ou sistema local. Normalmente, as diretivas são estabelecidas pelos administradores para ajudar os recursos de rede segura. Monitorar alterações ou tentativas de alterar essas políticas pode ser um aspecto importante do gerenciamento de segurança para uma rede. Essa categoria inclui as seguintes subcategorias:<ul><li>[Auditoria de alteração de política de auditoria](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-audit-policy-change)</li><li>[Auditoria de alteração de política de autenticação](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-authentication-policy-change)</li><li>[Auditoria de alteração de diretiva de autorização](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-authorization-policy-change)</li><li>[Auditoria de alteração de políticas de plataforma de filtragem](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-policy-change)</li><li>[Auditoria da mudança na política de nível de regra MPSSVC](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-mpssvc-rule-level-policy-change)</li><li>[Outra alteração de política de auditoria](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-policy-change-events)</li></ul>|
|Uso de privilégios| Audita o uso de determinadas permissões em um ou mais sistemas. Essa categoria inclui as seguintes subcategorias:<ul><li>[Auditar o uso de privilégios não confidenciais](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-non-sensitive-privilege-use)</li><li>[Auditar o uso de privilégios confidenciais](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-sensitive-privilege-use)</li><li>[Auditoria de outros eventos de uso de privilégio](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-privilege-use-events)</li></ul>|
|Sistema| Auditorias alterações no nível de sistema para um computador que não são incluídos em outras categorias e que tem implicações de segurança potencial. Essa categoria inclui as seguintes subcategorias:<ul><li>[Auditoria do Driver IPsec](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-driver)</li><li>[Auditoria de outros eventos do sistema](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-system-events)</li><li>[Alteração de estado de segurança de auditoria](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-state-change)</li><li>[Extensão do sistema de segurança de auditoria](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-system-extension)</li><li>[Integridade do sistema de auditoria](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-system-integrity)</li></ul>|

## <a name="event-ids-per-category"></a>IDs de eventos por categoria
 Auditoria de segurança de serviços de domínio do AD do Azure registra as seguintes IDs de evento quando a ação específica aciona um evento auditável.

| Nome da categoria de evento | IDs de eventos |
|:---|:---|
|Segurança de Logon de conta|4767, 4774, 4775, 4776, 4777|
|Segurança do gerenciamento de conta|4720, 4722, 4723, 4724, 4725, 4726, 4727, 4728, 4729, 4730, 4731, 4732, 4733, 4734, 4735, 4737, 4738, 4740, 4741, 4742, 4743, 4754, 4755, 4756, 4757, 4758, 4764, 4765, 4766, 4780, 4781, 4782, 4793, 4798, 4799, 5376, 5377|
|Segurança de controle de detalhe|Nenhum|
|Segurança de acesso ao DS|5136, 5137, 5138, 5139, 5141|
|Segurança de logon Logoff|4624, 4625, 4634, 4647, 4648, 4672, 4675, 4964|
|Segurança de acesso do objeto|Nenhum|
|Segurança de alteração de política|4670, 4703, 4704, 4705, 4706, 4707, 4713, 4715, 4716, 4717, 4718, 4719, 4739, 4864, 4865, 4866, 4867, 4904, 4906, 4911, 4912|
|Usar a segurança de privilégio|4985|
|Segurança do sistema|4612, 4621|

## <a name="enable-security-audit-events"></a>Habilitar eventos de auditoria de segurança
As diretrizes a seguir ajudam você a inscrever-se para eventos de auditoria de segurança do Azure AD Domain Services.

> [!IMPORTANT]
> Auditorias de segurança do Azure AD Domain Services não são retroativas. Não é possível recuperar os eventos de passado ou repetir eventos do passado. O serviço só pode enviar eventos que ocorrem depois que ele está habilitado.
>

### <a name="choose-the-target-resource"></a>Escolha o recurso de destino
Você pode usar qualquer combinação de espaços de trabalho do Azure Log Analytics, Hubs de eventos ou armazenamento do Azure como um recurso de destino para seu auditorias de segurança. Considere a tabela a seguir para o melhor recurso para seu caso de uso.

> [!IMPORTANT]
> Você precisa criar o recurso de destino antes de habilitar a auditoria de segurança do Azure AD Domain Services.
>

| Recurso de destino | Cenário |
|:---|:---|
|Armazenamento do Azure|Considere o uso desse destino quando a sua necessidade primária é armazenar os eventos de auditoria de segurança para fins de arquivamento. Outros destinos podem ser usados para fins de arquivamento; No entanto, esses destinos fornecem recursos além da necessidade primário de arquivamento. Para criar uma conta de armazenamento do Azure, siga [criar uma conta de armazenamento.](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal#create-a-storage-account-1)|
|Hubs de eventos do Azure|Considere o uso desse destino quando a sua necessidade principal é compartilhar eventos de auditoria de segurança com software adicional, como software de análise de dados ou o software de gerenciamento (SIEM) de & evento de informações de segurança. Para criar um hub de eventos, execute [guia de início rápido: Crie um hub de eventos usando o portal do Azure.](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)|
|O espaço de trabalho do Azure Log Analytics|Considere o uso desse destino quando sua necessidade primária deve analisar e revisar as auditorias seguras do portal do Azure diretamente.  Para criar um espaço de trabalho do Log Analytics, execute [criar um espaço de trabalho do Log Analytics no portal do Azure.](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)|

## <a name="using-the-azure-portal-to-enable-security-audit-events"></a>Usando o portal do Azure para habilitar eventos de auditoria de segurança 
1. Entre no Portal do Azure em https://portal.azure.com.  No portal do Azure, clique em todos os serviços. Na lista de recursos, digite **domínio**. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Clique em **do Azure AD Domain Services**.
2. Clique na instância do Azure AD Domain Services na lista.
3. Clique em **configurações de diagnóstico (visualização)** da lista de ações à esquerda.</p>
![ação de configuração de diagnóstico](./media/security-audit-events/diagnostic-settings-action.png)
4. Digite o nome da configuração de diagnóstico (**aadds-auditing** como um exemplo).</p>
![página de configurações de diagnóstico](./media/security-audit-events/diagnostic-settings-page.png)
5. Marque a caixa de seleção apropriada ao lado de recursos de destino que você usará com eventos de auditoria de segurança.
    > [!NOTE]
    > Você não pode criar recursos de destino desta página.
    >
    
    **armazenamento do Azure:**</p>
    Selecione **arquivar em uma conta de armazenamento**. Clique em **Configurar**. Selecione o **assinatura** e o **conta de armazenamento** você deseja usar para arquivar eventos de auditoria de segurança. Clique em **OK**.</p>
    
    ![configurações de armazenamento de diagnóstico](./media/security-audit-events/diag-settings-storage.png)
    
    **Hubs de eventos do Azure:**</p>
    Selecione **Stream para um hub de eventos**. Clique em **Configurar**. No **página de hub de evento Select**, selecione o **assinatura** usado para criar o hub de eventos. Em seguida, selecione a **namespace do hub de eventos**, **nome do hub de eventos**, e **nome de política do hub de eventos**. Clique em **OK**.</p>
    ![configurações do hub de eventos de diagnóstico](./media/security-audit-events/diag-settings-eventhub.png)
    
    **Espaços de trabalho do Log Analytics do Azure:**</p>
    Selecione **Enviar para o Log Analytics**. Selecione o **assinatura** e **espaço de trabalho do Log Analytics** usado para armazenar eventos de auditoria de segurança.</p>
    ![configurações de espaço de trabalho de diagnóstico](./media/security-audit-events/diag-settings-log-analytics.png)

6. Selecione as categorias de log a que serem incluídos para o recurso de destino específico. Se estiver usando contas de armazenamento, você pode configurar políticas de retenção.

    > [!NOTE]
    > Você pode selecionar categorias de log diferente para cada recurso de destino dentro de uma única configuração. Isso permite que você escolha quais logs categorias que você deseja manter para o Log Analytics e a quais categorias de logs que deseja arquivar.
    >

7. Clique em **salvar** para confirmar as alterações. Os recursos de destino receberá eventos de auditoria de segurança do Azure AD Domain Services logo após você salva sua configuração.

## <a name="using-azure-powershell-to-enable-security-audit-events"></a>Usando o Azure PowerShell para habilitar eventos de auditoria de segurança
 
### <a name="prerequisites"></a>Pré-requisitos

Siga as instruções no artigo para [instalar o módulo do Azure PowerShell e conectar-se à sua assinatura do Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).

### <a name="enable-security-audits"></a>Habilitar a auditoria de segurança

1. Autenticar para o Azure Resource Manager para o locatário apropriada e assinatura usando o **AzAccount Connect** cmdlet do PowerShell do Azure.
2. Crie o recurso de destino para a segurança de eventos de auditoria.</p>
    **armazenamento do Azure:**</p>
    Siga [criar uma conta de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-powershell) para criar sua conta de armazenamento.</p>
    **Hubs de eventos do Azure:**</p>
    Siga [guia de início rápido: Criar um hub de eventos usando o Azure PowerShell](https://docs.microsoft.com/azure/event-hubs/event-hubs-quickstart-powershell) para criar seu hub de eventos. Você também precisará usar o [New-AzEventHubAuthorizationRule](https://docs.microsoft.com/powershell/module/az.eventhub/new-azeventhubauthorizationrule?view=azps-2.3.2) cmdlet do PowerShell do Azure para criar uma regra de autorização para conceder permissões de serviços de domínio do AD do Active Directory para o hub de eventos **namespace**. A regra de autorização deve incluir a **Manage**, **escutar**, e **enviar** direitos.
    > [!IMPORTANT]
    > Verifique se que você definir a regra de autorização sobre o namespace de hub de eventos e não o hub de eventos.
       
    </p>
    
    **Espaços de trabalho do Log Analytics do Azure:**</p>
    Siga [criar um espaço de trabalho do Log Analytics com o Azure PowerShell](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace-posh) para criar seu espaço de trabalho.
3. Obter a ID de recurso para sua instância do Azure AD Domain Services. Em um console do Windows PowerShell aberto, autenticado, digite o comando a seguir. Use o **$aadds. ResourceId** variável como um parâmetro para a ID de recurso do Azure AD Domain Services para futuras cmdlets.
    ```powershell
    $aadds = Get-AzResource -name aaddsDomainName
    ``` 
4. Use o **AzDiagnosticSetting conjunto** cmdlet para definir as configurações de diagnóstico do Azure para usar o recurso de destino para eventos de auditoria de segurança do Azure AD Domain Services. Nos exemplos abaixo, a variável $aadds. ResourceId representa a ID de recurso da sua instância do Azure AD Domain Services (consulte a etapa 3).</p>
    **armazenamento do Azure:**
    ```powershell
    Set-AzDiagnosticSetting `
    -ResourceId $aadds.ResourceId` 
    -StorageAccountId storageAccountId `
    -Enabled $true
    ```
    Substitua *storageAccountId* com seu armazenamento de ID da conta</p>
    
    **Hubs de eventos do Azure:**
    ```powershell
    Set-AzDiagnosticSetting -ResourceId $aadds.ResourceId ` 
    -EventHubName eventHubName `
    -EventHubAuthorizationRuleId eventHubRuleId `
    -Enabled $true
    ```
    Substitua *eventHubName* com o nome do hub de eventos. Substitua *eventHubRuleId* com sua ID de regra de autorização que você criou anteriormente.</p>
    
    **Espaços de trabalho do Log Analytics do Azure:**
    ```powershell
    Set-AzureRmDiagnosticSetting -ResourceId $aadds.ResourceId ` 
    -WorkspaceID workspaceId `
    -Enabled $true
    ```
    Substitua *workspaceId* com a ID do espaço de trabalho do Log Analytics criado anteriormente. 

## <a name="view-security-audit-events-using-azure-monitor"></a>Exibir eventos de auditoria de segurança usando o Azure Monitor
Espaços de trabalho do log analítico permitem exibir e analisar os eventos de auditoria de segurança usando o Azure Monitor e a linguagem de consulta do Kusto. A linguagem de consulta foi projetada para uso somente leitura que apresenta os recursos analíticos do power com uma sintaxe fácil de ler.
Aqui estão alguns recursos para ajudá-lo a começar com linguagens de consulta do Kusto.
* [Documentação do Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/)
* [Introdução ao Log Analytics no Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)
* [Introdução às consultas de log no Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)
* [Criar e compartilhar painéis de dados do Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-logs-dashboards)

## <a name="sample-queries"></a>Consultas de exemplo

### <a name="sample-query-1"></a>Exemplo de consulta 1
Todos os conta bloqueio eventos nos últimos sete dias.
```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
```

### <a name="sample-query-2"></a>Exemplo de consulta 2
Todos os conta bloqueio eventos (4740) entre 26 de junho de 2019 às 9h e 1 de julho de 2019 meia-noite, classificados em ordem crescente por data e hora.
```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= datetime(2019-06-26 09:00) and TimeGenerated <= datetime(2019-07-01) 
| where OperationName has "4740"
| sort by TimeGenerated asc
```

### <a name="sample-query-3"></a>Exemplo de consulta 3
Conta de log em eventos de sete dias atrás (daqui) para a conta de usuário nomeada.
```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-4"></a>Exemplo de consulta 4
Eventos de logon de conta sete dias atrás do agora para a conta denominada usuário que tentou entrar usando uma senha incorreta (0xC0000006a).
```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc000006a" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-5"></a>Exemplo de consulta 5
Eventos de logon de conta sete dias atrás do agora para a conta denominada usuário que tentou entrar enquanto a conta foi bloqueada (0xC0000234).
```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-6"></a>Exemplo de consulta 6
O número de eventos de logon de conta de sete dias atrás do agora para todas as tentativas de entrada que ocorreram para todos os bloqueada usuários.
```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| summarize count()
```

## <a name="related-content"></a>Conteúdo relacionado
* [Visão geral](https://docs.microsoft.com/azure/kusto/query/) do Kusto linguagem de consulta.
* [Tutorial do Kusto](https://docs.microsoft.com/azure/kusto/query/tutorial) para se familiarizar com os conceitos básicos de consulta.
* [Consultas de exemplo](https://docs.microsoft.com/azure/kusto/query/samples) que ajudam a aprender novas formas para ver seus dados.
* Kusto [práticas recomendadas](https://docs.microsoft.com/azure/kusto/query/best-practices) – otimizar suas consultas para o sucesso.














 
