---
title: "Instalação do Agente do Azure AD Connect Health | Microsoft Docs"
description: "Esta é a página do Azure AD Connect Health que descreve a instalação do agente para o AD FS e Sincronização."
services: active-directory
documentationcenter: 
author: karavar
manager: samueld
editor: curtand
ms.assetid: 1cc8ae90-607d-4925-9c30-6770a4bd1b4e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: bfdcc4aadab18091b2f57e8bc751b37d1bac4d26
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-connect-health-agent-installation"></a>Instalação do Agente do Azure AD Connect Health
Este documento explica como instalar e configurar os Agentes do Azure AD Connect Health. Você pode baixar os agentes [daqui](active-directory-aadconnect-health.md#download-and-install-azure-ad-connect-health-agent).

## <a name="requirements"></a>Requisitos
A tabela a seguir é uma lista de requisitos para o uso do Azure AD Connect Health.

| Requisito | Descrição |
| --- | --- |
| AD Premium do Azure |O Azure AD Connect Health é um recurso do Azure AD Premium e requer o Azure AD Premium. </br></br>Para saber mais, consulte [Introdução ao AD Premium do Azure](../active-directory-get-started-premium.md) </br>Para iniciar uma avaliação gratuita de 30 dias, confira [Iniciar uma avaliação.](https://azure.microsoft.com/trial/get-started-active-directory/) |
| Você deve ser um administrador global do Azure AD para começar a usar o Azure AD Connect Health |Por padrão, somente os administradores globais podem instalar e configurar os agentes de integridade para começar, acessar o portal e realizar operações no Azure AD Connect Health. Para saber mais, consulte [Administrar seu diretório do Azure AD](../active-directory-administer.md). <br><br> Usando o Controle de Acesso com Base em Funções, você pode permitir acesso ao Azure AD Connect Health para outros usuários em sua organização. Para saber mais, confira [Controle de Acesso com Base em Função para o Azure AD Connect Health.](active-directory-aadconnect-health-operations.md#manage-access-with-role-based-access-control) </br></br>**Importante:** a conta usada ao instalar os agentes deve ser uma conta corporativa ou de estudante. Não pode ser uma conta da Microsoft. Para saber mais, confira [Inscrever-se no Azure como uma organização](../sign-up-organization.md) |
| O Agente do Azure AD Connect Health é instalado em cada servidor de destino | O Azure AD Connect Health requer que os agentes de integridade sejam instalados e configurados nos servidores de destino para receber os dados e fornecer os recursos de Monitoramento e Análise </br></br>Por exemplo, para obter dados da sua infraestrutura do AD FS, o agente deverá ser instalado nos servidores do AD FS e do Proxy de Aplicativo Web. Da mesma forma, para obter dados da infraestrutura do AD DS local, o agente deve ser instalado nos controladores de domínio. </br></br> |
| Conectividade de saída para os pontos de extremidade de serviço do Azure | Durante a instalação e o tempo de execução, o agente requer conectividade com os pontos de extremidade de serviço do Azure AD Connect Health. Se a conectividade de saída estiver bloqueada por meio de Firewalls, verifique se os pontos de extremidade a seguir foram adicionados à lista de permissões: </br></br><li>&#42;.blob.core.windows.net </li><li>&#42;.servicebus.windows.net - Port: 5671 </li><li>&#42;.adhybridhealth.azure.com/</li><li>https://management.azure.com </li><li>https://policykeyservice.dc.ad.msft.net/</li><li>https://login.windows.net</li><li>https://login.microsoftonline.com</li><li>https://secure.aadcdn.microsoftonline-p.com</li> |
|Conectividade de saída com base em endereços IP | Para a filtragem baseada em endereço IP em firewalls, veja [Intervalos IP do Azure](https://www.microsoft.com/en-us/download/details.aspx?id=41653).|
| A inspeção de SSL para tráfego de saída está filtrada ou desabilitada | A etapa de registro do agente ou as operações de carregamento de dados podem falhar se houver inspeção de SSL o terminação para tráfego de saída na camada de rede. |
| Portas de firewall no servidor que executa o agente. |O agente requer que as seguintes portas de firewall estejam abertas para que o agente se comunique com os pontos de extremidade de serviço do Azure AD Health.</br></br><li>Porta TCP 443</li><li>Porta TCP 5671</li> |
| Permita os sites a seguir se a segurança reforçada do IE estiver habilitada |Se a Segurança Aprimorada do IE estiver habilitada, os sites a seguir precisarão receber permissão no servidor no qual o agente será instalado.</br></br><li>https://login.microsoftonline.com</li><li>https://secure.aadcdn.microsoftonline-p.com</li><li>https://login.windows.net</li><li>O servidor de federação da sua organização confiável pelo Azure Active Directory. Por exemplo: https://sts.contoso.com</li> |
| Certifique-se de que o PowerShell v4.0 ou mais recente esteja instalado | <li>O Windows Server 2008 R2 é fornecido com o PowerShell v 2.0, que não é suficiente para o agente.  Atualize o PowerShell como explicado abaixo em [Instalação do agente em servidores do Windows Server 2008 R2](#agent-installation-on-windows-server-2008-r2-servers).</li><li>O Windows Server 2012 é fornecido com o PowerShell v 3.0, que não é suficiente para o agente.  [Atualize](http://www.microsoft.com/en-us/download/details.aspx?id=40855) o Windows Menagement Framework.</li><li>O Windows Server 2012 R2 e posterior é fornecido com uma versão suficientemente recente do PowerShell.</li>|
|Desabilitar FIPS|Não há suporte para FIPS nos agentes do Azure AD Connect Health.|

## <a name="download-and-install-the-azure-ad-connect-health-agent"></a>Baixar e instalar o Agente do Azure AD Connect Health
* Verifique se você [atende aos requisitos](active-directory-aadconnect-health-agent-install.md#requirements) para o Azure AD Connect Health.
* Introdução ao uso do Azure AD Connect Health do AD FS
    * [Baixe o Agente do Azure AD Connect Health para AD FS.](http://go.microsoft.com/fwlink/?LinkID=518973)
    * [Veja as instruções de instalação](#installing-the-azure-ad-connect-health-agent-for-ad-fs).
* Introdução ao uso do Azure AD Connect Health para sincronização
    * [Baixe e instale a versão mais recente do Azure AD Connect](http://go.microsoft.com/fwlink/?linkid=615771). O Agente de Integridade para sincronização será instalado como parte da instalação do Azure AD Connect (versão 1.0.9125.0 ou superior).
* Introdução ao uso do Azure AD Connect Health para o AD DS
    * [Baixe o Agente do Azure AD Connect Health para AD DS](http://go.microsoft.com/fwlink/?LinkID=820540).
    * [Veja as instruções de instalação](#installing-the-azure-ad-connect-health-agent-for-ad-ds).

## <a name="installing-the-azure-ad-connect-health-agent-for-ad-fs"></a>Instalando o Agente do Azure AD Connect Health para AD FS
Para iniciar a instalação do agente, dê um clique duplo no arquivo .exe que você baixou. Na primeira tela, clique em instalar.

![Verifique o Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install1.png)

Depois que a instalação for concluída, clique em Configurar agora.

![Verifique o Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install2.png)

Isso abrirá uma janela do PowerShell para iniciar o processo de registro do agente. Quando solicitado, entre com uma conta do Azure AD que tenha acesso para executar o registro do agente. Por padrão, a conta de Administrador Global tem acesso.

![Verifique o Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install3.png)

Depois de entrar, o PowerShell continuará. Depois de concluir, feche o PowerShell e a configuração estará concluída.

Neste ponto, os serviços do agente devem ser iniciados automaticamente permitindo que o carregamento do agente os dados necessários para o serviço de nuvem de maneira segura.

Se você não tiver atendido a todos os pré-requisitos descritos nas seções anteriores, serão exibidos avisos na janela do PowerShell. Não se esqueça de concluir os [requisitos](active-directory-aadconnect-health-agent-install.md#requirements) antes de instalar o agente. A captura de tela a seguir é um exemplo desses erros.

![Verifique o Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install4.png)

Para verificar se o agente foi instalado, procure os serviços a seguir no servidor. Se você tiver concluído a configuração, eles já deverão estar em execução. Caso contrário, eles ficarão parados até a conclusão da configuração.

* Serviço de diagnóstico do AD FS do Azure AD Connect Health
* Serviço do Insights do AD FS do Azure AD Connect Health
* Serviço de Monitoramento do AD FS do Azure AD Connect Health

![Verifique o Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install5.png)

### <a name="agent-installation-on-windows-server-2008-r2-servers"></a>Instalação do agente em servidores do Windows Server 2008 R2
Etapas para servidores Windows Server 2008 R2:

1. Certifique-se de que o servidor esteja em execução no Service Pack 1 ou superior.
2. Desative a ESC do IE para instalação do agente:
3. Instale o Windows PowerShell 4.0 em cada um dos servidores antes de instalar o agente de integridade do AD. Para instalar o Windows PowerShell 4.0:
   * Instale o [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=40779) usando o link a seguir para baixar o instalador offline.
   * Instale o PowerShell ISE (de recursos do Windows)
   * Instale o [Windows Management Framework 4.0.](https://www.microsoft.com/download/details.aspx?id=40855)
   * Instale o Internet Explorer versão 10 ou superior no servidor. (Necessário para que o serviço de integridade faça sua autenticação usando suas credenciais de administrador do Azure).
4. Para saber mais sobre como instalar o Windows PowerShell 4.0 no Windows Server 2008 R2, consulte o artigo no wiki [aqui](http://social.technet.microsoft.com/wiki/contents/articles/20623.step-by-step-upgrading-the-powershell-version-4-on-2008-r2.aspx).

### <a name="enable-auditing-for-ad-fs"></a>Habilitar a auditoria do AD FS
> [!NOTE]
> Esta seção se aplica apenas aos servidores do AD FS. Você não precisa seguir estas etapas nos servidores de Proxy de aplicativo Web.
>

Para que o recurso de Análise de Uso colete e analise dados, o agente do Azure AD Connect Health precisa das informações nos Logs de auditoria do AD FS. Esses logs não estão habilitados por padrão. Use os procedimentos a seguir para habilitar a auditoria do AD FS e localizar os logs de auditoria do AD FS em seus servidores do AD FS.

#### <a name="to-enable-auditing-for-ad-fs-on-windows-server-2008-r2"></a>Para habilitar a auditoria do AD FS no Windows Server 2008 R2
1. Clique em **Iniciar**, aponte para **Programas**, aponte para **Ferramentas Administrativas** e clique em **Política de Segurança Local**.
2. Navegue até a pasta **Configurações de Segurança\Políticas Locais\Atribuição de Direitos de Usuário** e clique duas vezes em **Gerar auditorias de segurança**.
3. Na guia **Configuração de Segurança Local** , verifique se a conta de serviço do AD FS 2.0 está listada. Se ela não estiver presente, clique em **Adicionar Usuário ou Grupo** e adicione-a à lista e clique em **OK**.
4. Para habilitar a auditoria, abra um prompt de comando com privilégios elevados e execute o seguinte comando: <code>auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable</code>
5. Feche **Política de Segurança Local** e então abra o snap-in **Gerenciamento do AD FS**. Para abrir o snap-in Gerenciamento do AD FS, clique em **Iniciar**, aponte para **Programas**, aponte para **Ferramentas Administrativas** e clique em **Gerenciamento do AD FS 2.0**.
6. No painel **Ações**, clique em **Editar Propriedades do Serviço de Federação**.
7. Na caixa de diálogo **Propriedades do Serviço de Federação**, clique na guia **Eventos**.
8. Marque as caixas de seleção **Auditorias com êxito** e **Auditorias com falha**.
9. Clique em **OK**.

#### <a name="to-enable-auditing-for-ad-fs-on-windows-server-2012-r2"></a>Para habilitar a auditoria do AD FS no Windows Server 2012 R2
1. Abra **Política de Segurança Local** abrindo o **Gerenciador do Servidor** na tela Iniciar ou o Gerenciador do Servidor na barra de tarefas na área de trabalho e, em seguida, clique em **Ferramentas/Política de Segurança Local**.
2. Navegue até a pasta **Configurações de Segurança\Políticas Locais\Atribuição de Direitos de Usuário** e clique duas vezes em **Gerar auditorias de segurança**.
3. Na guia **Configuração de Segurança Local** , verifique se a conta de serviço do AD FS está listada. Se ela não estiver presente, clique em **Adicionar Usuário ou Grupo** e adicione-a à lista e clique em **OK**.
4. Para habilitar a auditoria, abra um prompt de comando com privilégios elevados e execute o seguinte comando: ```auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable```.
5. Feche **Política de Segurança Local** e abra o snap-in de **Gerenciamento do AD FS** (no Gerenciador do Servidor, clique em Ferramentas e selecione Gerenciamento do AD FS).
6. No painel Ações, clique em **Editar Propriedades do Serviço de Federação**.
7. Na caixa de diálogo Propriedades do Serviço de Federação, clique na guia **Eventos** .
8. Marque as caixas de seleção **Auditorias com êxito e Auditorias com falha** e clique em **OK**.

#### <a name="to-enable-auditing-for-ad-fs-on-windows-server-2016"></a>Para habilitar a auditoria do AD FS no Windows Server 2016
1. Abra **Política de Segurança Local** abrindo o **Gerenciador do Servidor** na tela Iniciar ou o Gerenciador do Servidor na barra de tarefas na área de trabalho e, em seguida, clique em **Ferramentas/Política de Segurança Local**.
2. Navegue até a pasta **Configurações de Segurança\Políticas Locais\Atribuição de Direitos de Usuário** e clique duas vezes em **Gerar auditorias de segurança**.
3. Na guia **Configuração de Segurança Local** , verifique se a conta de serviço do AD FS está listada. Se ela não estiver presente, clique em **Adicionar Usuário ou Grupo** e adicione a conta de serviço do AD FS à lista e clique em **OK**.
4. Para habilitar a auditoria, abra um prompt de comando com privilégios elevados e execute o seguinte comando: <code>auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable.</code>
5. Feche **Política de Segurança Local** e abra o snap-in de **Gerenciamento do AD FS** (no Gerenciador do Servidor, clique em Ferramentas e selecione Gerenciamento do AD FS).
6. No painel Ações, clique em **Editar Propriedades do Serviço de Federação**.
7. Na caixa de diálogo Propriedades do Serviço de Federação, clique na guia **Eventos** .
8. Marque as caixas de seleção **Auditorias com êxito e Auditorias com falha** e clique em **OK**. Isso deve ser habilitado por padrão.
9. Abra uma janela do PowerShell e execute o seguinte comando: ```Set-AdfsProperties -AuditLevel Verbose```.

Observe que o nível de auditoria "básico" é habilitado por padrão. Leia mais sobre o [aprimoramento de auditoria do AD FS no Windows Server 2016](https://technet.microsoft.com/en-us/windows-server-docs/identity/ad-fs/operations/auditing-enhancements-to-ad-fs-in-windows-server-2016)


#### <a name="to-locate-the-ad-fs-audit-logs"></a>Para localizar os logs de auditoria do AD FS
1. Abra o **Visualizador de Eventos**.
2. Vá para Logs do Windows e selecione **Segurança**.
3. À direita, clique em **Filtrar Logs Atuais**.
4. Em Origem do Evento, selecione **Auditoria do AD FS**.

![Logs de auditoria do AD FS](./media/active-directory-aadconnect-health-requirements/adfsaudit.png)

> [!WARNING]
> Uma política de grupo pode desabilitar a auditoria do AD FS. Se a auditoria do AD FS for desabilitada, a análise de uso sobre as atividades de logon não estará disponível. Verifique se você não tem uma política de grupo que desabilite a auditoria do AD FS.>
>


## <a name="installing-the-azure-ad-connect-health-agent-for-sync"></a>Instalando o agente do Azure AD Connect Health para sincronização
O agente do Azure AD Connect Health para sincronização é instalado automaticamente na compilação mais recente do Azure AD Connect. Para usar o Azure AD Connect para sincronização, você precisará baixar a versão mais recente do Azure AD Connect e instalá-lo. Você pode baixar a versão mais recente [aqui](http://www.microsoft.com/download/details.aspx?id=47594).

Para verificar se o agente foi instalado, procure os serviços a seguir no servidor. Se você tiver concluído a configuração, eles já deverão estar em execução. Caso contrário, eles ficarão parados até a conclusão da configuração.

* Serviço de Informações do Azure AD Connect Health para Sincronização
* Serviço de Monitoramento do Azure AD Connect Health para Sincronização

![Verificar sincronização do Azure AD Connect Health](./media/active-directory-aadconnect-health-sync/services.png)

> [!NOTE]
> Lembre-se de que usar o Azure AD Connect Health requer o Azure AD Premium. Se você não tiver o Azure AD Premium, você não poderá concluir a configuração no portal do Azure. Para saber mais, confira a [página de requisitos](active-directory-aadconnect-health-agent-install.md#requirements).
>
>

## <a name="manual-azure-ad-connect-health-for-sync-registration"></a>Registro manual do Azure AD Connect Health para Sincronização
Se o registro do agente do Azure AD Connect Health para Sincronização falhar após a instalação bem-sucedida do Azure AD Connect, você poderá usar o comando a seguir do PowerShell para registrar manualmente o agente.

> [!IMPORTANT]
> O uso desse comando do PowerShell só será necessário se o registro do agente falhar após a instalação do Azure AD Connect.
>
>

O comando a seguir do PowerShell é necessário somente quando o registro do agente de integridade falha mesmo após a instalação e a configuração bem-sucedidas do Azure AD Connect. Os serviços do Azure AD Connect Health não serão iniciados até que o agente seja registrado com êxito.

Você pode registrar manualmente o agente do Azure AD Connect Health para sincronização usando o seguinte comando do PowerShell:

`Register-AzureADConnectHealthSyncAgent -AttributeFiltering $false -StagingMode $false`

O comando usa os seguintes parâmetros:

* AttributeFiltering: $true (padrão) - se o Azure AD Connect não estiver sincronizando o conjunto de atributos padrão e tiver personalizado para usar um conjunto de atributos filtrados. Caso contrário, o valor será $false.
* StagingMode: $false (padrão) - se o servidor do Azure AD Connect NÃO estiver no modo de preparo; o valor será $true se o servidor estiver configurado para o modo de preparo.

Quando for solicitado a fazer a autenticação, você deverá usar a mesma conta do administrador global (como admin@domain.onmicrosoft.com) usada para configurar o Azure AD Connect.

## <a name="installing-the-azure-ad-connect-health-agent-for-ad-ds"></a>Instalando o Agente do Azure AD Connect Health para AD DS
Para iniciar a instalação do agente, dê um clique duplo no arquivo .exe que você baixou. Na primeira tela, clique em instalar.

![Verifique o Azure AD Connect Health](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install1.png)

Depois que a instalação for concluída, clique em Configurar agora.

![Verifique o Azure AD Connect Health](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install2.png)

Um prompt de comando será iniciado, seguido pelo PowerShell, que executará Register-AzureADConnectHealthADDSAgent. Quando solicitado a entrar no Azure, continue e entre.

![Verifique o Azure AD Connect Health](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install3.png)

Depois de entrar, o PowerShell continuará. Depois de concluir, feche o PowerShell e a configuração estará concluída.

Neste ponto, os serviços devem ser iniciados automaticamente, permitindo que o agente monitore e colete dados. Se você não tiver atendido a todos os pré-requisitos descritos nas seções anteriores, serão exibidos avisos na janela do PowerShell. Não se esqueça de concluir os [requisitos](active-directory-aadconnect-health-agent-install.md#requirements) antes de instalar o agente. A captura de tela a seguir é um exemplo desses erros.

![Verificar o Azure AD Connect Health para AD DS](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install4.png)

Para verificar se o agente foi instalado, procure os serviços a seguir no controlador de domínio.

* Serviço do Insights do AD DS do Azure AD Connect Health
* Serviço de Monitoramento do AD DS do Azure AD Connect Health

Se você tiver concluído a configuração, esses serviços já deverão estar em execução. Caso contrário, eles ficarão parados até a conclusão da configuração.

![Verifique o Azure AD Connect Health](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install5.png)


### <a name="agent-registration-using-powershell"></a>Registro de agente usando o PowerShell
Depois de instalar o agente apropriado com setup.exe, você poderá executar a etapa de registro de agente usando os comandos do PowerShell a seguir, dependendo da função. Abra uma janela do PowerShell e execute o comando apropriado:

```
    Register-AzureADConnectHealthADFSAgent
    Register-AzureADConnectHealthADDSAgent
    Register-AzureADConnectHealthSyncAgent

```

Esses comandos aceitam "Credencial" como um parâmetro para concluir o registro de forma não interativa ou em um computador Server-Core.
* A credencial pode ser capturada em uma variável do PowerShell que é passada como um parâmetro.
* Você pode fornecer qualquer identidade do Azure AD que tenha acesso para registrar os agentes e que NÃO tenha o MFA habilitado.
* Por padrão, administradores globais têm acesso para executar o registro do agente. Você também pode permitir que outras identidades menos privilegiadas executem esta etapa. Leia mais sobre [Controle de acesso com base em função](active-directory-aadconnect-health-operations.md#manage-access-with-role-based-access-control).

```
    $cred = Get-Credential
    Register-AzureADConnectHealthADFSAgent -Credential $cred

```

## <a name="configure-azure-ad-connect-health-agents-to-use-http-proxy"></a>Configurar agentes do Azure AD Connect Health para usar HTTP Proxy
Você pode configurar agentes do Azure AD Connect Health para trabalhar com um HTTP Proxy.

> [!NOTE]
> * Não há suporte para "Netsh WinHttp set ProxyServerAddress", pois o agente usa System.Net para fazer solicitações da web em vez do Microsoft Windows HTTP Services.
> * O endereço do Http Proxy configurado é usado para mensagens de passagem criptografadas de Https.
> * Não há suporte para proxies autenticados (usando HTTPBasic).
>
>

### <a name="change-health-agent-proxy-configuration"></a>Alterar a configuração de proxy do agente de integridade
Você tem as seguintes opções para configurar o agente do Azure AD Connect Health para usar um HTTP Proxy.

> [!NOTE]
> Todos os serviços do agente do Azure AD Connect Health devem ser reiniciados para que as configurações de proxy sejam atualizadas. Execute o comando a seguir:<br>
> Restart-Service AdHealth*
>
>

#### <a name="import-existing-proxy-settings"></a>Importar configurações de proxy existentes
##### <a name="import-from-internet-explorer"></a>Importar do Internet Explorer
As configurações de proxy HTTP do Internet Explorer podem ser importadas para serem usadas por agentes do Azure AD Connect Health. Em cada um dos servidores que executam o agente de integridade, execute o seguinte comando do PowerShell:

    Set-AzureAdConnectHealthProxySettings -ImportFromInternetSettings

##### <a name="import-from-winhttp"></a>Importar do WinHTTP
As configurações de proxy WinHTTP podem ser importadas para serem usadas por agentes do Azure AD Connect Health. Em cada um dos servidores que executam o agente de integridade, execute o seguinte comando do PowerShell:

    Set-AzureAdConnectHealthProxySettings -ImportFromWinHttp

#### <a name="specify-proxy-addresses-manually"></a>Especificar endereços de Proxy manualmente
Você pode executar manualmente um servidor proxy em cada um dos servidores que estejam executando o Agente de Integridade ao executar o seguinte comando do PowerShell:

    Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress address:port

Exemplo: *Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress myproxyserver:443*

* "address" pode ser um nome do servidor DNS que pode ser resolvido ou um endereço IPv4
* "port" pode ser omitida. Se omitida, 443 é escolhida como a porta padrão.

#### <a name="clear-existing-proxy-configuration"></a>Limpar a configuração de proxy existente
Você pode limpar a configuração de proxy existente executando o comando a seguir:

    Set-AzureAdConnectHealthProxySettings -NoProxy


### <a name="read-current-proxy-settings"></a>Ler configurações de proxy atuais
Você pode ler as configurações de proxy atuais ao executar este comando:

    Get-AzureAdConnectHealthProxySettings


## <a name="test-connectivity-to-azure-ad-connect-health-service"></a>Testar a conectividade com o serviço Azure AD Connect Health
É possível haver problemas que façam com que o agente do Azure AD Connect Health perca a conectividade com o serviço Azure AD Connect Health. Isso inclui problemas de rede, problemas de permissão, entre vários outros.

Se o agente não puder enviar dados para o serviço do Azure AD Connect Health por mais de duas horas, o seguinte alerta será indicado no portal: "Os dados do Serviço de Integridade não estão atualizados." Você pode confirmar se o agente do Azure AD Connect Health afetado consegue carregar dados no serviço Azure AD Connect Health ao executar o seguinte comando do PowerShell:

    Test-AzureADConnectHealthConnectivity -Role ADFS

O parâmetro de função usa os seguintes valores:

* ADFS
* Sincronizar
* ADICIONA

Você pode usar o sinalizador -ShowResults no comando para exibir logs detalhados. Use o seguinte exemplo:

    Test-AzureADConnectHealthConnectivity -Role Sync -ShowResult

> [!NOTE]
> Para usar a ferramenta de conectividade, você deve primeiro concluir o registro do agente. Se conseguir concluir o registro do agente, verifique se você atende a todos os [requisitos](active-directory-aadconnect-health-agent-install.md#requirements) do Azure AD Connect Health. Esse teste de conectividade é executado por padrão durante o registro do agente.
>
>

## <a name="related-links"></a>Links relacionados
* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Operações de Azure AD Connect Health](active-directory-aadconnect-health-operations.md)
* [Usando o Azure AD Connect Health com o AD FS](active-directory-aadconnect-health-adfs.md)
* [Usando o Azure AD Connect Health para sincronização](active-directory-aadconnect-health-sync.md)
* [Usar o Azure AD Connect Health com o AD DS](active-directory-aadconnect-health-adds.md)
* [Perguntas frequentes do Azure AD Connect Health](active-directory-aadconnect-health-faq.md)
* [Histórico de versão do Azure AD Connect Health](active-directory-aadconnect-health-version-history.md)
