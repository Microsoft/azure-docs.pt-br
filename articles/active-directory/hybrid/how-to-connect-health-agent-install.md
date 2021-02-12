---
title: Instalar os agentes do Connect Health no Azure Active Directory
description: Este artigo Azure AD Connect Health descreve a instalação do agente para Serviços de Federação do Active Directory (AD FS) (AD FS) e para sincronização.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: 1cc8ae90-607d-4925-9c30-6770a4bd1b4e
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/20/2020
ms.topic: how-to
ms.author: billmath
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 57362aa84886d7b7d764617ce5a43ca2393bed52
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98018234"
---
# <a name="azure-ad-connect-health-agent-installation"></a>Instalação do agente do Azure AD Connect Health

Neste artigo, você aprenderá a instalar e configurar os agentes de integridade de conexão do Azure Active Directory (Azure AD). Para baixar os agentes, consulte [estas instruções](how-to-connect-install-roadmap.md#download-and-install-azure-ad-connect-health-agent).

## <a name="requirements"></a>Requisitos

A tabela a seguir lista os requisitos para usar o Azure AD Connect Health.

| Requisito | Descrição |
| --- | --- |
| O Azure AD Premium está instalado. |Azure AD Connect Health é um recurso do Azure AD Premium. Para obter mais informações, consulte [inscrever-se para Azure ad Premium](../fundamentals/active-directory-get-started-premium.md). <br /><br />Para iniciar uma avaliação gratuita de 30 dias, consulte [iniciar uma avaliação](https://azure.microsoft.com/trial/get-started-active-directory/). |
| Você é um administrador global no Azure AD. |Por padrão, somente os administradores globais podem instalar e configurar os agentes de integridade, acessar o portal e realizar qualquer operação dentro de Azure AD Connect Health. Para saber mais, consulte [Administrar seu diretório do Azure AD](../fundamentals/active-directory-whatis.md). <br /><br /> Usando o controle de acesso baseado em função do Azure (RBAC do Azure), você pode permitir que outros usuários em sua organização acessem Azure AD Connect Health. Para obter mais informações, consulte [RBAC do Azure para Azure ad Connect Health](how-to-connect-health-operations.md#manage-access-with-azure-rbac). <br /><br />**Importante**: Use uma conta corporativa ou de estudante para instalar os agentes. Você não pode usar um conta Microsoft. Para obter mais informações, consulte [inscrever-se no Azure como uma organização](../fundamentals/sign-up-organization.md). |
| O agente de Azure AD Connect Health é instalado em cada servidor de destino. | Os agentes de integridade devem ser instalados e configurados nos servidores de destino para que possam receber dados e fornecer recursos de monitoramento e análise. <br /><br />Por exemplo, para obter dados de sua infraestrutura de Serviços de Federação do Active Directory (AD FS) (AD FS), você deve instalar o agente no servidor de AD FS e o servidor de proxy de aplicativo Web. Da mesma forma, para obter dados da sua infraestrutura local Azure AD Domain Services (Azure AD DS), você deve instalar o agente nos controladores de domínio.  |
| Os pontos de extremidade de serviço do Azure têm conectividade de saída. | Durante a instalação e o runtime, o agente requer conectividade com os pontos de extremidade de serviço do Azure AD Connect Health. Se os firewalls bloquearem a conectividade de saída, adicione os [pontos de extremidade de conectividade de saída](how-to-connect-health-agent-install.md#outbound-connectivity-to-the-azure-service-endpoints) à lista de permissões. |
|A conectividade de saída é baseada em endereços IP. | Para obter informações sobre filtragem de firewall com base em endereços IP, consulte [intervalos de IP do Azure](https://www.microsoft.com/download/details.aspx?id=41653).|
| A inspeção TLS para o tráfego de saída é filtrada ou desabilitada. | A etapa de registro do agente ou as operações de carregamento de dados poderão falhar se houver inspeção ou rescisão de TLS para o tráfego de saída na camada de rede. Para obter mais informações, consulte [Configurar a inspeção de TLS](/previous-versions/tn-archive/ee796230(v=technet.10)). |
| As portas de firewall no servidor estão executando o agente. |O agente requer que as seguintes portas de firewall sejam abertas para que ele possa se comunicar com os pontos de extremidade de serviço Azure AD Connect Health: <br /><li>Porta TCP 443</li><li>Porta TCP 5671</li> <br />A versão mais recente do agente não requer a porta 5671. Atualize para a versão mais recente para que apenas a porta 443 seja necessária. Para obter mais informações, consulte [portas e protocolos necessários à identidade híbrida](./reference-connect-ports.md). |
| Se a segurança aprimorada do Internet Explorer estiver habilitada, permitir sites especificados.  |Se a segurança aprimorada do Internet Explorer estiver habilitada, permita os seguintes sites no servidor em que você instala o agente:<br /><li>https:\//login.microsoftonline.com</li><li>https:\//secure.aadcdn.microsoftonline-p.com</li><li>https:\//login.windows.net</li><li>https: \/ /aadcdn.msftauth.net</li><li>O servidor de Federação para sua organização que é confiável pelo Azure AD (por exemplo, https: \/ /STS.contoso.com)</li> <br />Para obter mais informações, consulte [como configurar o Internet Explorer](https://support.microsoft.com/help/815141/internet-explorer-enhanced-security-configuration-changes-the-browsing). Se você tiver um proxy em sua rede, consulte a observação que aparece no final desta tabela.|
| O PowerShell versão 4,0 ou mais recente está instalado. | O Windows Server 2012 inclui o PowerShell versão 3,0. Esta versão *não* é suficiente para o agente.</br></br> O Windows Server 2012 R2 e versões posteriores incluem uma versão suficientemente recente do PowerShell.|
|O FIPS (padrão FIPS) está desabilitado.|Os agentes de Azure AD Connect Health não dão suporte a FIPS.|

> [!IMPORTANT]
> O Windows Server Core não dá suporte à instalação do agente de Azure AD Connect Health.


> [!NOTE]
> Se você tiver um ambiente altamente bloqueado e restrito, será necessário adicionar mais URLs do que aquelas que as listas de tabelas para segurança aprimorada do Internet Explorer. Além disso, adicione URLs listadas na tabela na próxima seção.  


### <a name="outbound-connectivity-to-the-azure-service-endpoints"></a>Conectividade de saída para os pontos de extremidade de serviço do Azure

Durante a instalação e o tempo de execução, o agente precisa de conectividade para Azure AD Connect Health pontos de extremidade de serviço. Se os firewalls bloquearem a conectividade de saída, verifique se as URLs na tabela a seguir não estão bloqueadas por padrão. 

Não desabilite o monitoramento de segurança ou a inspeção dessas URLs. Em vez disso, permita que eles permitam outro tráfego de Internet. 

Essas URLs permitem a comunicação com pontos de extremidade de serviço Azure AD Connect Health. Mais adiante neste artigo, você aprenderá a [verificar a conectividade de saída](#test-connectivity-to-azure-ad-connect-health-service) usando o `Test-AzureADConnectHealthConnectivity` .

| Ambiente de domínio | Pontos de extremidade de serviço do Azure exigidos |
| --- | --- |
| Público geral | <li>&#42;.blob.core.windows.net </li><li>&#42;.aadconnecthealth.azure.com </li><li>&#42;. servicebus.windows.net-Port: 5671 (esse ponto de extremidade não é necessário na versão mais recente do agente.)</li><li>&#42;.adhybridhealth.azure.com/</li><li>https:\//management.azure.com </li><li>https:\//policykeyservice.dc.ad.msft.net/</li><li>https:\//login.windows.net</li><li>https:\//login.microsoftonline.com</li><li>https:\//secure.aadcdn.microsoftonline-p.com </li><li>https: \/ /www.Office.com (esse ponto de extremidade é usado somente para fins de descoberta durante o registro.)</li> <li>https://aadcdn.msftauth.net</li><li>https://aadcdn.msauth.net</li> |
| Azure Alemanha | <li>&#42;.blob.core.cloudapi.de </li><li>&#42;.servicebus.cloudapi.de </li> <li>&#42;.aadconnecthealth.microsoftazure.de </li><li>https:\//management.microsoftazure.de </li><li>https:\//policykeyservice.aadcdi.microsoftazure.de </li><li>https:\//login.microsoftonline.de </li><li>https:\//secure.aadcdn.microsoftonline-p.de </li><li>https: \/ /www.Office.de (esse ponto de extremidade é usado somente para fins de descoberta durante o registro.)</li> <li>https://aadcdn.msftauth.net</li><li>https://aadcdn.msauth.net</li> |
| Azure Government | <li>&#42;.blob.core.usgovcloudapi.net </li> <li>&#42;.servicebus.usgovcloudapi.net </li> <li>&#42;.aadconnecthealth.microsoftazure.us </li> <li>https:\//management.usgovcloudapi.net </li><li>https:\//policykeyservice.aadcdi.azure.us </li><li>https:\//login.microsoftonline.us </li><li>https:\//secure.aadcdn.microsoftonline-p.com </li><li>https: \/ /www.Office.com (esse ponto de extremidade é usado somente para fins de descoberta durante o registro.)</li> <li>https://aadcdn.msftauth.net</li><li>https://aadcdn.msauth.net</li> |


## <a name="install-the-agent"></a>Instalar o agente

Para baixar e instalar o agente de Azure AD Connect Health: 

* Certifique-se de atender [aos requisitos](how-to-connect-health-agent-install.md#requirements) para Azure ad Connect Health.
* Introdução ao uso de Azure AD Connect Health para AD FS:
    * [Baixe o agente de Azure ad Connect Health para AD FS](https://go.microsoft.com/fwlink/?LinkID=518973).
    * Consulte as [instruções de instalação](#install-the-agent-for-ad-fs).
* Introdução ao uso de Azure AD Connect Health para sincronização:
    * [Baixe e instale a versão mais recente do Azure AD Connect](https://go.microsoft.com/fwlink/?linkid=615771). O agente de integridade para sincronização é instalado como parte da instalação do Azure AD Connect (versão 1.0.9125.0 ou posterior).
* Introdução ao uso de Azure AD Connect Health para o Azure AD DS:
    * [Baixe o agente de Azure ad Connect Health para AD DS do Azure](https://go.microsoft.com/fwlink/?LinkID=820540).
    * Consulte as [instruções de instalação](#install-the-agent-for-azure-ad-ds).

## <a name="install-the-agent-for-ad-fs"></a>Instalar o agente para AD FS

> [!NOTE]
> Seu servidor de AD FS deve ser diferente do seu servidor de sincronização. Não instale o agente de AD FS no servidor de sincronização.
>

Antes de instalar o agente, verifique se seu nome de host do AD FS Server é exclusivo e não está presente no serviço AD FS.
Para iniciar a instalação do agente, clique duas vezes no arquivo *. exe* que você baixou. Na primeira janela, selecione **instalar**.

![Captura de tela mostrando a janela de instalação do Azure AD Connect Health AD FS Agent.](./media/how-to-connect-health-agent-install/install1.png)

Após a conclusão da instalação, selecione **Configurar agora**.

![Captura de tela mostrando a mensagem de confirmação para a instalação do Azure AD Connect Health AD FS Agent.](./media/how-to-connect-health-agent-install/install2.png)

Uma janela do PowerShell é aberta para iniciar o processo de registro do agente. Quando for solicitado, entre usando uma conta do Azure AD que tenha permissões para registrar o agente. Por padrão, a conta de administrador global tem permissões.

![Captura de tela mostrando a janela de entrada para Azure AD Connect Health AD FS.](./media/how-to-connect-health-agent-install/install3.png)

Depois de entrar, o PowerShell continua. Ao concluir, você pode fechar o PowerShell. A configuração foi concluída.

Neste ponto, os serviços de agente devem ser iniciados automaticamente para permitir que o agente carregue com segurança os dados necessários para o serviço de nuvem.

Se você ainda não atendeu a todos os pré-requisitos, os avisos aparecem na janela do PowerShell. Certifique-se de concluir os [requisitos](how-to-connect-health-agent-install.md#requirements) antes de instalar o agente. A captura de tela a seguir mostra um exemplo desses avisos.

![Captura de tela mostrando o Azure AD Connect Health AD FS o script de configuração.](./media/how-to-connect-health-agent-install/install4.png)

Para verificar se o agente foi instalado, procure os seguintes serviços no servidor. Se você tiver concluído a configuração, eles já deverão estar em execução. Caso contrário, eles são interrompidos até que a configuração seja concluída.

* Serviço de diagnóstico do AD FS do Azure AD Connect Health
* Serviço do Insights do AD FS do Azure AD Connect Health
* Serviço de Monitoramento do AD FS do Azure AD Connect Health

![Captura de tela mostrando Azure AD Connect Health serviços de AD FS.](./media/how-to-connect-health-agent-install/install5.png)


### <a name="enable-auditing-for-ad-fs"></a>Habilitar a auditoria para AD FS

> [!NOTE]
> Esta seção aplica-se somente a servidores AD FS. Você não precisa seguir estas etapas nos servidores de proxy de aplicativo Web.
>

O recurso de análise de uso precisa coletar e analisar dados. Portanto, o agente de Azure AD Connect Health precisa das informações no AD FS logs de auditoria. Esses logs não são habilitados por padrão. Use os procedimentos a seguir para habilitar AD FS auditoria e para localizar os AD FS logs de auditoria em seus servidores de AD FS.

#### <a name="to-enable-auditing-for-ad-fs-on-windows-server-2012-r2"></a>Para habilitar a auditoria do AD FS no Windows Server 2012 R2

1. Na tela iniciar, abra **Gerenciador do servidor** e, em seguida, abra **política de segurança local**. Ou, na barra de tarefas, abra **Gerenciador do servidor** e, em seguida, selecione **ferramentas/política de segurança local**.
2. Vá para a pasta *segurança \ Diretivas de atribuição de direitos* . Em seguida, clique duas vezes em **gerar auditorias de segurança**.
3. Na guia **Configuração de segurança local**, verifique se a conta de serviço AD FS está listada. Se não estiver listado, selecione **Adicionar usuário ou grupo** e adicione-o à lista. Depois, selecione **OK**.
4. Para habilitar a auditoria, abra uma janela de prompt de comando com privilégios elevados. Em seguida, execute o seguinte comando: 
    
    `auditpol.exe /set /subcategory:{0CCE9222-69AE-11D9-BED3-505054503030} /failure:enable /success:enable`
1. Feche a **Política de Segurança Local**.
    >[!Important]
    >As etapas a seguir são necessárias apenas para servidores de AD FS primários. 
1. Abra o snap-in **Gerenciamento do AD FS**. (Em **Gerenciador do servidor**, selecione **ferramentas**  >  **Gerenciamento de AD FS**.)
1. No painel **ações** , selecione **editar propriedades de serviço de Federação**.
1. Na caixa de diálogo **Propriedades do serviço de Federação** , selecione a guia **eventos** .
1. Marque as caixas de seleção **auditorias com êxito e auditorias de falha** e, em seguida, selecione **OK**.
1. Para habilitar o log detalhado por meio do PowerShell, use o seguinte comando: 

    `Set-AdfsProperties -LOGLevel Verbose`

#### <a name="to-enable-auditing-for-ad-fs-on-windows-server-2016"></a>Para habilitar a auditoria do AD FS no Windows Server 2016

1. Na tela iniciar, abra **Gerenciador do servidor** e, em seguida, abra **política de segurança local**. Ou, na barra de tarefas, abra **Gerenciador do servidor** e, em seguida, selecione **ferramentas/política de segurança local**.
2. Vá para a pasta *segurança \ Diretivas de atribuição de direitos* e clique duas vezes em **gerar auditorias de segurança**.
3. Na guia **Configuração de segurança local**, verifique se a conta de serviço AD FS está listada. Se não estiver listado, selecione **Adicionar usuário ou grupo** e adicione a conta de serviço AD FS à lista. Depois, selecione **OK**.
4. Para habilitar a auditoria, abra uma janela de prompt de comando com privilégios elevados. Em seguida, execute o seguinte comando: 

    `auditpol.exe /set /subcategory:{0CCE9222-69AE-11D9-BED3-505054503030} /failure:enable /success:enable`
1. Feche a **Política de Segurança Local**.
    >[!Important]
    >As etapas a seguir são necessárias apenas para servidores de AD FS primários.
1. Abra o snap-in **Gerenciamento do AD FS**. (Em **Gerenciador do servidor**, selecione **ferramentas**  >  **Gerenciamento de AD FS**.)
1. No painel **ações** , selecione **editar propriedades de serviço de Federação**.
1. Na caixa de diálogo **Propriedades do serviço de Federação** , selecione a guia **eventos** .
1. Marque as caixas de seleção **auditorias com êxito e auditorias de falha** e, em seguida, selecione **OK**. Auditorias com êxito e auditorias de falha devem ser habilitadas por padrão.
1. Abra uma janela do PowerShell e execute o seguinte comando: 

    `Set-AdfsProperties -AuditLevel Verbose`

O nível de auditoria "básico" é habilitado por padrão. Para obter mais informações, consulte [AD FS o aprimoramento da auditoria no Windows Server 2016](/windows-server/identity/ad-fs/technical-reference/auditing-enhancements-to-ad-fs-in-windows-server).


#### <a name="to-locate-the-ad-fs-audit-logs"></a>Para localizar os logs de auditoria do AD FS

1. Abra o **Visualizador de Eventos**.
2. Vá para **logs do Windows** e selecione **segurança**.
3. À direita, selecione **Filtrar logs atuais**.
4. Para **origens de eventos**, selecione **AD FS auditoria**.

    Para obter mais informações sobre logs de auditoria, consulte [perguntas sobre operações](reference-connect-health-faq.md#operations-questions).

    ![Captura de tela mostrando a janela Filtrar log atual. No campo "origens do evento", "AD FS auditoria" é selecionado.](./media/how-to-connect-health-agent-install/adfsaudit.png)

> [!WARNING]
> Uma política de grupo pode desabilitar a auditoria do AD FS. Se AD FS auditoria estiver desabilitada, a análise de uso sobre atividades de logon não estará disponível. Verifique se você não tem nenhuma política de grupo que desabilite AD FS auditoria.
>


## <a name="install-the-agent-for-sync"></a>Instalar o agente para sincronização

O agente de Azure AD Connect Health para sincronização é instalado automaticamente na versão mais recente do Azure AD Connect. Para usar Azure AD Connect para sincronização, [Baixe a versão mais recente do Azure ad Connect](https://www.microsoft.com/download/details.aspx?id=47594) e instale-a.

Para verificar se o agente foi instalado, procure os serviços a seguir no servidor. Se você concluiu a configuração, os serviços já devem estar em execução. Caso contrário, os serviços serão interrompidos até que a configuração seja concluída.

* Serviço de Informações do Azure AD Connect Health para Sincronização
* Serviço de Monitoramento do Azure AD Connect Health para Sincronização

![Captura de tela mostrando o Azure AD Connect Health em execução para serviços de sincronização no servidor.](./media/how-to-connect-health-agent-install/services.png)

> [!NOTE]
> Lembre-se de que você deve ter Azure AD Premium para usar Azure AD Connect Health. Se você não tiver Azure AD Premium, não será possível concluir a configuração no portal do Azure. Para obter mais informações, consulte os [requisitos](how-to-connect-health-agent-install.md#requirements).
>
>

## <a name="manually-register-azure-ad-connect-health-for-sync"></a>Registrar Azure AD Connect Health manualmente para sincronização

Se a Azure AD Connect Health para o registro do agente de sincronização falhar após a instalação bem-sucedida do Azure AD Connect, você poderá usar um comando do PowerShell para registrar manualmente o agente.

> [!IMPORTANT]
> Use esse comando do PowerShell somente se o registro do agente falhar após a instalação do Azure AD Connect.
>
>

Registre manualmente o agente de Azure AD Connect Health para sincronização usando o comando do PowerShell a seguir. Os serviços do Azure AD Connect Health não serão iniciados até que o agente seja registrado com êxito.

`Register-AzureADConnectHealthSyncAgent -AttributeFiltering $false -StagingMode $false`

O comando usa os seguintes parâmetros:

* **AttributeFiltering**: `$true` (padrão) se Azure ad Connect não estiver sincronizando o conjunto de atributos padrão e tiver sido personalizado para usar um conjunto de atributos filtrados. Caso contrário, use `$false`.
* **Preparomode**: `$false` (padrão) se o servidor de Azure ad Connect *não* estiver no modo de preparo. Se o servidor estiver configurado para estar no modo de preparo, use `$true` .

Quando for solicitada a autenticação, use a mesma conta de administrador global (como admin@domain.onmicrosoft.com ) que você usou para configurar Azure ad Connect.

## <a name="install-the-agent-for-azure-ad-ds"></a>Instalar o agente para o Azure AD DS

Para iniciar a instalação do agente, clique duas vezes no arquivo *. exe* que você baixou. Na primeira janela, selecione **instalar**.

![Captura de tela mostrando o agente de Azure AD Connect Health para AD DS janela de instalação.](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install1.png)

Quando a instalação for concluída, selecione **Configurar agora**.

![Captura de tela mostrando a janela que conclui a instalação do agente de Azure AD Connect Health para o AD DS do Azure.](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install2.png)

Uma janela de prompt de comando é aberta. O PowerShell é executado `Register-AzureADConnectHealthADDSAgent` . Quando for solicitado, entre no Azure.

![Captura de tela mostrando a janela de entrada para o agente de Azure AD Connect Health para AD DS do Azure.](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install3.png)

Depois de entrar, o PowerShell continua. Ao concluir, você pode fechar o PowerShell. A configuração foi concluída.

Neste ponto, os serviços devem ser iniciados automaticamente, permitindo que o agente monitore e colete dados. Se você ainda não atendeu a todos os pré-requisitos descritos nas seções anteriores, os avisos aparecem na janela do PowerShell. Certifique-se de concluir os [requisitos](how-to-connect-health-agent-install.md#requirements) antes de instalar o agente. A captura de tela a seguir mostra um exemplo desses avisos.

![Captura de tela mostrando um aviso para o agente de Azure AD Connect Health para a configuração de AD DS do Azure.](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install4.png)

Para verificar se o agente está instalado, procure os seguintes serviços no controlador de domínio:

* Serviço do Insights do AD DS do Azure AD Connect Health
* Serviço de Monitoramento do AD DS do Azure AD Connect Health

Se você tiver concluído a configuração, esses serviços já deverão estar em execução. Caso contrário, eles serão interrompidos até que a configuração seja concluída.

![Captura de tela mostrando os serviços em execução no controlador de domínio.](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install5.png)

### <a name="quickly-install-the-agent-on-multiple-servers"></a>Instalar rapidamente o agente em vários servidores

1. Crie uma conta de usuário no Azure AD. Proteja-o usando uma senha.
2. Atribua a função de **proprietário** para essa conta local do Azure AD no Azure ad Connect Health usando o Portal. Siga [estas etapas](how-to-connect-health-operations.md#manage-access-with-azure-rbac). Atribua a função a todas as instâncias de serviço. 
3. Baixe o arquivo MSI *. exe* no controlador de domínio local para a instalação.
4. Execute o seguinte script. Substitua os parâmetros pela sua nova conta de usuário e sua senha. 

    ```powershell
    AdHealthAddsAgentSetup.exe /quiet
    Start-Sleep 30
    $userName = "NEWUSER@DOMAIN"
    $secpasswd = ConvertTo-SecureString "PASSWORD" -AsPlainText -Force
    $myCreds = New-Object System.Management.Automation.PSCredential ($userName, $secpasswd)
    import-module "C:\Program Files\Azure Ad Connect Health Adds Agent\PowerShell\AdHealthAdds"
     
    Register-AzureADConnectHealthADDSAgent -Credential $myCreds
    
    ```

Ao concluir, você pode remover o acesso à conta local fazendo uma ou mais das seguintes tarefas: 
* Remova a atribuição de função da conta local para Azure AD Connect Health.
* Gire a senha da conta local. 
* Desabilite a conta local do Azure AD.
* Exclua a conta local do Azure AD.  

## <a name="register-the-agent-by-using-powershell"></a>Registrar o agente usando o PowerShell

Depois de instalar o arquivo de *setup.exe* de agente apropriado, você pode registrar o agente usando os seguintes comandos do PowerShell, dependendo da função. Abra uma janela do PowerShell e execute o comando apropriado:

```powershell
    Register-AzureADConnectHealthADFSAgent
    Register-AzureADConnectHealthADDSAgent
    Register-AzureADConnectHealthSyncAgent

```

> [!NOTE]
> Para se registrar em nuvens soberanas, use as seguintes linhas de comando:
>
> ```powershell
> Register-AzureADConnectHealthADFSAgent -UserPrincipalName upn-of-the-user
> Register-AzureADConnectHealthADDSAgent -UserPrincipalName upn-of-the-user
> Register-AzureADConnectHealthSyncAgent -UserPrincipalName upn-of-the-user
> ```
>


Esses comandos são aceitos `Credential` como um parâmetro para concluir o registro de forma não interativa ou para concluir o registro em um computador que executa o Server Core. Tenha em mente que:
* Você pode capturar `Credential` em uma variável do PowerShell que é passada como um parâmetro.
* Você pode fornecer qualquer identidade do Azure AD que tenha permissões para registrar os agentes e que *não tenha a* autenticação multifator habilitada.
* Por padrão, os administradores globais têm permissões para registrar os agentes. Você também pode permitir que identidades com menos privilégios executem esta etapa. Para obter mais informações, consulte [RBAC do Azure](how-to-connect-health-operations.md#manage-access-with-azure-rbac).

```powershell
    $cred = Get-Credential
    Register-AzureADConnectHealthADFSAgent -Credential $cred

```

## <a name="configure-azure-ad-connect-health-agents-to-use-http-proxy"></a>Configurar agentes de Azure AD Connect Health para usar o proxy HTTP

Você pode configurar agentes de Azure AD Connect Health para trabalhar com um proxy HTTP.

> [!NOTE]
> * `Netsh WinHttp set ProxyServerAddress` não é suportado. O agente usa System.Net em vez de serviços HTTP do Windows para fazer solicitações da Web.
> * O endereço de proxy HTTP configurado é usado para passar mensagens HTTPS criptografadas.
> * Não há suporte para proxies autenticados (usando HTTPBasic).
>
>

### <a name="change-the-agent-proxy-configuration"></a>Alterar a configuração de proxy do agente

Para configurar o agente de Azure AD Connect Health para usar um proxy HTTP, você pode:
* Importar configurações de proxy existentes.
* Especifique os endereços de proxy manualmente.
* Limpe a configuração de proxy existente.

> [!NOTE]
> Para atualizar as configurações de proxy, você deve reiniciar todos os serviços do Azure AD Connect Health Agent. Execute o comando a seguir:
>
> `Restart-Service AzureADConnectHealth*`

#### <a name="import-existing-proxy-settings"></a>Importar configurações de proxy existentes

Você pode importar as configurações de proxy HTTP do Internet Explorer para que os agentes de Azure AD Connect Health possam usar as configurações. Em cada um dos servidores que executam o agente de integridade, execute o seguinte comando do PowerShell:

```powershell
Set-AzureAdConnectHealthProxySettings -ImportFromInternetSettings
```

Você pode importar as configurações de proxy do WinHTTP para que os agentes de Azure AD Connect Health possam usá-las. Em cada um dos servidores que executam o agente de integridade, execute o seguinte comando do PowerShell:

```powershell
Set-AzureAdConnectHealthProxySettings -ImportFromWinHttp
```

#### <a name="specify-proxy-addresses-manually"></a>Especificar endereços de proxy manualmente

Você pode especificar manualmente um servidor proxy. Em cada um dos servidores que executam o agente de integridade, execute o seguinte comando do PowerShell:

```powershell
Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress address:port
```

Aqui está um exemplo: 

`Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress myproxyserver: 443`

Neste exemplo:
* A `address` configuração pode ser um nome de servidor de resolução de DNS ou um endereço IPv4.
* Você pode omitir `port` . Se você fizer isso, 443 será a porta padrão.

#### <a name="clear-the-existing-proxy-configuration"></a>Limpar a configuração de proxy existente

Você pode limpar a configuração de proxy existente executando o comando a seguir:

```powershell
Set-AzureAdConnectHealthProxySettings -NoProxy
```

### <a name="read-current-proxy-settings"></a>Ler configurações de proxy atuais

Você pode ler as configurações de proxy atuais executando o seguinte comando:

```powershell
Get-AzureAdConnectHealthProxySettings
```

## <a name="test-connectivity-to-azure-ad-connect-health-service"></a>Testar a conectividade com o serviço Azure AD Connect Health

Ocasionalmente, o agente de Azure AD Connect Health pode perder a conectividade com o serviço de Azure AD Connect Health. As causas dessa perda de conectividade podem incluir problemas de rede, problemas de permissão e vários outros problemas.

Se o agente não puder enviar dados para o serviço de Azure AD Connect Health por mais de duas horas, o seguinte alerta aparecerá no Portal: "Serviço de Integridade dados não estão atualizados". 

Você pode descobrir se o agente afetado Azure AD Connect Health pode carregar dados no serviço de Azure AD Connect Health executando o seguinte comando do PowerShell:

```powershell
Test-AzureADConnectHealthConnectivity -Role ADFS
```

O parâmetro de função usa os seguintes valores:

* ADFS
* Sincronização
* ADICIONA

> [!NOTE]
> Para usar a ferramenta de conectividade, você deve primeiro registrar o agente. Se você não puder concluir o registro do agente, certifique-se de ter atendido todos os [requisitos](how-to-connect-health-agent-install.md#requirements) para Azure ad Connect Health. A conectividade é testada por padrão durante o registro do agente.
>
>

## <a name="next-steps"></a>Próximas etapas

Confira os seguintes artigos relacionados:

* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Operações de Azure AD Connect Health](how-to-connect-health-operations.md)
* [Usando o Azure AD Connect Health com o AD FS](how-to-connect-health-adfs.md)
* [Usando o Azure AD Connect Health for Sync](how-to-connect-health-sync.md)
* [Usando Azure AD Connect Health com o AD DS do Azure](how-to-connect-health-adds.md)
* [Perguntas frequentes do Azure AD Connect Health](reference-connect-health-faq.md)
* [Histórico de versões do Azure AD Connect Health](reference-connect-health-version-history.md)
