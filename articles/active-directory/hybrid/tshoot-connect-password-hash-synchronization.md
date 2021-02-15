---
title: Solução de problemas de sincronização de hash de senha com a sincronização do Azure AD Connect | Microsoft Docs
description: Este artigo fornece informações de como solucionar problemas de sincronização de hash de senha.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 03/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8c6ec162ceb51c3bf19be42219b054d8371ff221
ms.sourcegitcommit: 08458f722d77b273fbb6b24a0a7476a5ac8b22e0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/15/2021
ms.locfileid: "98247345"
---
# <a name="troubleshoot-password-hash-synchronization-with-azure-ad-connect-sync"></a>Solução de problemas de sincronização de hash de senha com a sincronização do Azure AD Connect

Este tópico fornece etapas para solucionar problemas com a sincronização de hash de senha. Se as senhas não estiverem sincronizando conforme o esperado, isso poderá ocorrer para um subconjunto de usuários ou para todos os usuários.

Para a implantação do Azure AD (Azure Active Directory) Connect com a versão 1.1.614.0 ou posterior, use a tarefa de solução de problemas no assistente para solucionar problemas de sincronização de hash de senha:

* Se ocorrer um problema em que nenhuma senha seja sincronizada, consulte a seção [Nenhuma senha é sincronizada: solucionar problemas usando a tarefa de solução de problemas](#no-passwords-are-synchronized-troubleshoot-by-using-the-troubleshooting-task).

* Se ocorrer um problema com objetos individuais, consulte a seção [Um objeto não está sincronizando senhas: solucionar problemas usando a tarefa de solução de problemas](#one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-troubleshooting-task).

Para a implantação com a versão 1.1.524.0 ou posterior, há um cmdlet de diagnóstico que você pode usar para solucionar problemas de sincronização de hash de senha:

* Se ocorrer um problema em que nenhuma senha seja sincronizada, consulte a seção [Nenhuma senha é sincronizada: solucionar problemas usando o cmdlet de diagnóstico](#no-passwords-are-synchronized-troubleshoot-by-using-the-diagnostic-cmdlet).

* Se ocorrer um problema com objetos individuais, consulte a seção [Um objeto não está sincronizando senhas: solucionar problemas usando o cmdlet de diagnóstico](#one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-diagnostic-cmdlet).

Para versões mais antigas de implantação do Azure AD Connect:

* Se ocorrer um problema em que nenhuma senha seja sincronizada, consulte a seção [Nenhuma senha é sincronizada: etapas manuais de solução de problemas](#no-passwords-are-synchronized-manual-troubleshooting-steps).

* Se ocorrer um problema com objetos individuais, consulte a seção [Um objeto não está sincronizando senhas: etapas manuais de solução de problemas](#one-object-is-not-synchronizing-passwords-manual-troubleshooting-steps).



## <a name="no-passwords-are-synchronized-troubleshoot-by-using-the-troubleshooting-task"></a>Nenhuma senha é sincronizada: solucionar problemas usando a tarefa de solução de problemas

Você pode usar a tarefa de solução de problemas para descobrir por que nenhuma senha é sincronizada.

> [!NOTE]
> A tarefa de solução de problemas está disponível apenas para o Azure AD Connect versão 1.1.614.0 ou posterior.

### <a name="run-the-troubleshooting-task"></a>Executar a tarefa de solução de problemas

Para solucionar problemas em que nenhuma senha é sincronizada:

1. Abra uma nova sessão do Windows PowerShell no servidor do Azure AD Connect com a opção **Executar como administrador**.

2. Execute `Set-ExecutionPolicy RemoteSigned` ou `Set-ExecutionPolicy Unrestricted`.

3. Iniciar o assistente do Azure AD Connect.

4. Navegue até a página **tarefas adicionais** , selecione **solucionar problemas** e clique em **Avançar**.

5. Na página de solução de problemas, clique em **Iniciar** para iniciar o menu de solução de problemas no PowerShell.

6. No menu principal, selecione **solucionar problemas de sincronização de hash de senha**.

7. No submenu, selecione **a sincronização de hash de senha não funciona**.

### <a name="understand-the-results-of-the-troubleshooting-task"></a>Entender os resultados da tarefa de solução de problemas

A tarefa de solução de problemas executa as seguintes verificações:

* Valida se o recurso de sincronização de hash de senha está habilitado para seu locatário do Azure AD.

* Valida se o servidor do Azure AD Connect não está em modo de preparo.

* Para cada conector local existente do Active Directory (que corresponde a uma floresta existente do Active Directory):

   * Valida se o recurso de sincronização de hash de senha está habilitado.
   
   * Pesquisa eventos de pulsação de sincronização de hash de senha nos logs de eventos de aplicativo do Windows.

   * Para cada domínio do Active Directory sob o Active Directory Connector local:

      * Valida se o domínio está acessível no servidor do Azure AD Connect.

      * Valida se as contas do AD DS (Active Directory Domain Services) usadas pelo Active Directory Connector local tem o nome de usuário, a senha e as permissões corretos, necessários para a sincronização de hash de senha.

O diagrama a seguir ilustra os resultados do cmdlet para uma topologia do Active Directory local de domínio único:

![Saída de diagnóstico da sincronização de hash de senha](./media/tshoot-connect-password-hash-synchronization/phsglobalgeneral.png)

O restante desta seção descreve resultados específicos que são retornados pela tarefa e os problemas correspondentes.

#### <a name="password-hash-synchronization-feature-isnt-enabled"></a>O recurso de sincronização de hash de senha não está habilitado

Se você não tiver habilitado a sincronização de hash de senha usando o assistente do Azure AD Connect, o seguinte erro será retornado:

![A sincronização de hash de senha não está habilitada](./media/tshoot-connect-password-hash-synchronization/phsglobaldisabled.png)

#### <a name="azure-ad-connect-server-is-in-staging-mode"></a>O servidor do Azure AD Connect está em modo de preparo

Se o servidor do Azure AD Connect estiver em modo de preparo, a sincronização de hash de senha será temporariamente desabilitada e o seguinte erro será retornado:

![O servidor do Azure AD Connect está em modo de preparo](./media/tshoot-connect-password-hash-synchronization/phsglobalstaging.png)

#### <a name="no-password-hash-synchronization-heartbeat-events"></a>Nenhum evento de pulsação de sincronização de hash de senha

Cada Active Directory Connector local tem seu próprio canal de sincronização de hash de senha. Quando o canal de sincronização de hash de senha é estabelecido e não existem alterações de senha a serem sincronizadas, um evento de pulsação (EventId 654) é gerado uma vez a cada 30 minutos no log de eventos de aplicativos do Windows. Para cada Active Directory Connector local, o cmdlet pesquisa eventos de pulsação correspondentes nas últimas três horas. Se nenhum evento de pulsação for encontrado, o seguinte erro será retornado:

![Nenhum evento de pulsação de sincronização de hash de senha](./media/tshoot-connect-password-hash-synchronization/phsglobalnoheartbeat.png)

#### <a name="ad-ds-account-does-not-have-correct-permissions"></a>A conta do AD DS não tem as permissões corretas

Se a conta do AD DS usada pelo Active Directory Connector local para sincronizar os hashes de senha não tiver as permissões apropriadas, o seguinte erro será retornado:

![Captura de tela que mostra o erro retornado quando a conta de AD DS tem um nome de usuário ou senha incorretos.](./media/tshoot-connect-password-hash-synchronization/phsglobalaccountincorrectpermission.png)

#### <a name="incorrect-ad-ds-account-username-or-password"></a>Nome de usuário ou senha incorreta da conta do AD DS

Se a conta do AD DS usada pelo Active Directory Connector local para sincronizar os hashes de senha tiver um nome de usuário ou uma senha incorreta, o seguinte erro será retornado:

![Credenciais incorretas](./media/tshoot-connect-password-hash-synchronization/phsglobalaccountincorrectcredential.png)



## <a name="one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-troubleshooting-task"></a>Um objeto não está sincronizando senhas: solucionar problemas usando a tarefa de solução de problemas

Você pode usar a tarefa de solução de problemas para determinar por que um objeto não está sincronizando senhas.

> [!NOTE]
> A tarefa de solução de problemas está disponível apenas para o Azure AD Connect versão 1.1.614.0 ou posterior.

### <a name="run-the-diagnostics-cmdlet"></a>Executar o cmdlet de diagnóstico

Para solucionar problemas de um objeto de usuário específico:

1. Abra uma nova sessão do Windows PowerShell no servidor do Azure AD Connect com a opção **Executar como administrador**.

2. Execute `Set-ExecutionPolicy RemoteSigned` ou `Set-ExecutionPolicy Unrestricted`.

3. Iniciar o assistente do Azure AD Connect.

4. Navegue até a página **tarefas adicionais** , selecione **solucionar problemas** e clique em **Avançar**.

5. Na página de solução de problemas, clique em **Iniciar** para iniciar o menu de solução de problemas no PowerShell.

6. No menu principal, selecione **solucionar problemas de sincronização de hash de senha**.

7. No submenu, selecione **A senha não está sincronizada para uma conta de usuário específica**.

### <a name="understand-the-results-of-the-troubleshooting-task"></a>Entender os resultados da tarefa de solução de problemas

A tarefa de solução de problemas executa as seguintes verificações:

* Examina o estado do objeto do Active Directory no espaço do Active Directory Connector, no metaverso e no espaço do conector do Azure AD.

* Valida se há regras de sincronização com a sincronização de hash de senha habilitada e aplicada a objeto do Active Directory.

* Tenta recuperar e exibir os resultados da última tentativa de sincronizar a senha do objeto.

O diagrama a seguir ilustra os resultados do cmdlet, ao solucionar problemas de sincronização de hash de senha para um único objeto:

![Saída de diagnóstico da sincronização de hash de senha – objeto único](./media/tshoot-connect-password-hash-synchronization/phssingleobjectgeneral.png)

O restante desta seção descreve os resultados específicos retornados pelo cmdlet e os problemas correspondentes.

#### <a name="the-active-directory-object-isnt-exported-to-azure-ad"></a>O objeto do Active Directory não é exportado para o Azure AD

Falha na sincronização de hash de senha para essa conta do Active Directory local porque não há nenhum objeto correspondente no locatário do Azure AD. O seguinte erro é retornado.

![Objeto do Azure AD está ausente](./media/tshoot-connect-password-hash-synchronization/phssingleobjectnotexported.png)

#### <a name="user-has-a-temporary-password"></a>O usuário tem uma senha temporária

Atualmente, o Azure AD Connect não dá suporte à sincronização de senhas temporárias com o Azure AD. Uma senha será considerada temporária se o opção **Alterar a senha no próximo logon** estiver definida no usuário do Active Directory local. O seguinte erro é retornado.

![A senha temporária não é exportada](./media/tshoot-connect-password-hash-synchronization/phssingleobjecttemporarypassword.png)

#### <a name="results-of-last-attempt-to-synchronize-password-arent-available"></a>Os resultados da última tentativa de sincronizar a senha não estão disponíveis

Por padrão, o Azure AD Connect armazena os resultados das tentativas de sincronização de hash de senha por sete dias. Se não houver nenhum resultado disponível para o objeto selecionado do Active Directory, será retornado o seguinte aviso:

![Saída de diagnóstico de um único objeto – nenhum histórico de sincronização de senha](./media/tshoot-connect-password-hash-synchronization/phssingleobjectnohistory.png)



## <a name="no-passwords-are-synchronized-troubleshoot-by-using-the-diagnostic-cmdlet"></a>Nenhuma senha é sincronizada: solucionar problemas usando o cmdlet de diagnóstico

Você pode usar o cmdlet `Invoke-ADSyncDiagnostics` para descobrir por que nenhuma senha é sincronizada.

> [!NOTE]
> O cmdlet `Invoke-ADSyncDiagnostics` está disponível apenas para o Azure AD Connect versão 1.1.524.0 ou posterior.

### <a name="run-the-diagnostics-cmdlet"></a>Executar o cmdlet de diagnóstico

Para solucionar problemas em que nenhuma senha é sincronizada:

1. Abra uma nova sessão do Windows PowerShell no servidor do Azure AD Connect com a opção **Executar como administrador**.

2. Execute `Set-ExecutionPolicy RemoteSigned` ou `Set-ExecutionPolicy Unrestricted`.

3. Execute `Import-Module ADSyncDiagnostics`.

4. Execute `Invoke-ADSyncDiagnostics -PasswordSync`.



## <a name="one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-diagnostic-cmdlet"></a>Um objeto não está sincronizando senhas: solucionar problemas usando o cmdlet de diagnóstico

Você pode usar o cmdlet `Invoke-ADSyncDiagnostics` para determinar por que um objeto não está sincronizando senhas.

> [!NOTE]
> O cmdlet `Invoke-ADSyncDiagnostics` está disponível apenas para o Azure AD Connect versão 1.1.524.0 ou posterior.

### <a name="run-the-diagnostics-cmdlet"></a>Executar o cmdlet de diagnóstico

Para solucionar problemas em que nenhuma senha esteja sincronizada para um usuário:

1. Abra uma nova sessão do Windows PowerShell no servidor do Azure AD Connect com a opção **Executar como administrador**.

2. Execute `Set-ExecutionPolicy RemoteSigned` ou `Set-ExecutionPolicy Unrestricted`.

3. Execute `Import-Module ADSyncDiagnostics`.

4. Execute o cmdlet a seguir:

   ```
   Invoke-ADSyncDiagnostics -PasswordSync -ADConnectorName <Name-of-AD-Connector> -DistinguishedName <DistinguishedName-of-AD-object>
   ```

   Por exemplo:

   ```powershell
   Invoke-ADSyncDiagnostics -PasswordSync -ADConnectorName "contoso.com" -DistinguishedName "CN=TestUserCN=Users,DC=contoso,DC=com"
   ```



## <a name="no-passwords-are-synchronized-manual-troubleshooting-steps"></a>Nenhuma senha é sincronizada: etapas manuais de solução de problemas

Siga estas etapas para determinar por que nenhuma senha é sincronizada:

1. O servidor do Connect está no [modo de preparo](how-to-connect-sync-staging-server.md)? Um servidor no modo de preparo não sincroniza nenhuma senha.

2. Execute o script na seção [Obter o status das configurações de sincronização de senha](#get-the-status-of-password-sync-settings). Ele fornece uma visão geral da configuração da sincronização de senha.  

    ![Saída de script do PowerShell das configurações de sincronização de senha](./media/tshoot-connect-password-hash-synchronization/psverifyconfig.png)  

3. Se o recurso não estiver habilitado no Azure AD ou se o status do canal de sincronização não estiver habilitado, execute o assistente de instalação do Connect. Selecione **Personalizar opções de sincronização** e desmarque a sincronização de senha. Essa alteração desabilita temporariamente o recurso. Em seguida, execute o assistente novamente e habilite novamente a sincronização de senha. Execute o script novamente para verificar se a configuração está correta.

4. Procure se há erros no log de eventos. Procure os seguintes eventos, que poderão indicar um problema:
    * Fonte: “Sincronização de diretório” ID: 0, 611, 652, 655 Se um desses eventos aparecer, há um problema de conectividade. A mensagem do log de eventos contém informações da floresta em que há um problema. Para obter mais informações, consulte [Problema de conectividade](#connectivity problem).

5. Se não aparecer nenhuma pulsação ou se nada mais funcionou, execute [Disparar uma sincronização completa de todas as senhas](#trigger-a-full-sync-of-all-passwords). Execute o script apenas uma vez.

6. Confira a seção Solucionar problemas de um objeto que não está sincronizando senhas.

### <a name="connectivity-problems"></a>Problemas de conectividade

Você tem conectividade com o Azure AD?

A conta tem as permissões necessárias para ler os hashes de senha em todos os domínios? Se você instalou o Connect usando as configurações Expresso, as permissões já estarão corretas. 

Se você usou a instalação personalizada, defina as permissões manualmente, fazendo o seguinte:
    
1. Para localizar a conta usada pelo Active Directory Connector, inicie o **Synchronization Service Manager**. 
 
2. Acesse **Conectores** e, em seguida, pesquise a floresta do Active Directory local cujo problema você está solucionando. 
 
3. Selecione o conector e, em seguida, clique em **Propriedades**. 
 
4. Vá para **Conectar-se à floresta do Active Directory**.  
    
    ![Conta usada pelo Active Directory Connector](./media/tshoot-connect-password-hash-synchronization/connectoraccount.png)  
    Anote o nome de usuário e o domínio em que a conta está localizada.
    
5. Inicie os **Usuários e Computadores do Active Directory** e, em seguida, verifique se a conta que você encontrou anteriormente tem as seguintes permissões definidas na raiz de todos os domínios na floresta:
    * Replicar alterações de diretório
    * Replicar todas as alterações de diretório

6. Os controladores de domínio estão acessíveis pelo Azure AD Connect? Se o servidor do Connect não puder se conectar a todos os controladores de domínio, configure **Usar somente o controlador de domínio preferencial**.  
    
    ![Controlador de domínio usado pelo Active Directory Connector](./media/tshoot-connect-password-hash-synchronization/preferreddc.png)  

7. Volte para **Synchronization Service Manager** e **Configurar Partição de Diretório**. 
 
8. Selecione o domínio em **Selecionar partições de diretório**, selecione a caixa de seleção **Usar somente controladores de domínio preferenciais** e, em seguida, clique em **Configurar**. 

9. Na lista, insira os controladores de domínio que o Connect deve usar para a sincronização de senha. A mesma lista também é usada para importação e exportação. Siga estas etapas para todos os domínios.

> [!NOTE]
> Para aplicar essas alterações, reinicie o serviço de **sincronização de Microsoft Azure ad** (AdSync).

10. Se o script mostrar que não há nenhuma pulsação, execute o script [Disparar uma sincronização completa de todas as senhas](#trigger-a-full-sync-of-all-passwords).

## <a name="one-object-is-not-synchronizing-passwords-manual-troubleshooting-steps"></a>Um objeto não está sincronizando senhas: etapas manuais de solução de problemas

Você pode solucionar problemas de sincronização de hash de senha problemas facilmente analisando o status de um objeto.

1. Em **Usuários e computadores do Active Directory**, pesquise o usuário e, em seguida, verifique se a caixa de seleção **O usuário deve alterar a senha no próximo logon** está desmarcada.  

    ![Senhas produtivas do Active Directory](./media/tshoot-connect-password-hash-synchronization/adprodpassword.png)  

    Se a caixa de seleção estiver marcada, solicite que o usuário entre e altere a senha. As senhas temporárias não são sincronizadas com o Azure AD.

2. Se a senha estiver correta no Active Directory, siga o usuário no mecanismo de sincronização. Seguindo o usuário do Active Directory local ao Azure AD, você pode ver se há algum erro descritivo no objeto.

    a. Inicie o [Synchronization Service Manager](how-to-connect-sync-service-manager-ui.md).

    b. Clique em **Conectores**.

    c. Selecione o **Active Directory Connector** no qual o usuário está localizado.

    d. Selecione **Pesquisar Espaço do Conector**.

    e. No **Escopo** selecione **DN ou Âncora** e, em seguida, insira o DN completo do usuário cujo problema você está solucionando.

    ![Pesquisar por usuário no espaço do conector com DN](./media/tshoot-connect-password-hash-synchronization/searchcs.png)  

    f. Localize o usuário que você está procurando e, em seguida, clique em **Propriedades** para ver todos os atributos. Se o usuário não estiver no resultado da pesquisa, verifique as [regras de filtragem](how-to-connect-sync-configure-filtering.md) e lembre-se de executar [Aplicar e verificar alterações](how-to-connect-sync-configure-filtering.md#apply-and-verify-changes) para que o usuário seja exibido no Connect.

    g. Para ver os detalhes de sincronização de senha do objeto da semana passada, clique em **Log**.  

    ![Detalhes do log do objeto](./media/tshoot-connect-password-hash-synchronization/csobjectlog.png)  

    Se o log do objeto está vazio, o Azure AD Connect não conseguiu ler o hash de senha do Active Directory. Continue a solução de problemas com Erros de conectividade. Se aparecer qualquer outro valor que não seja **êxito**, consulte a tabela no [Log de sincronização de senha](#password-sync-log).

    h. Selecione a guia **linhagem** e verifique se pelo menos uma regra de sincronização na coluna **PasswordSync** é **True**. Na configuração padrão, o nome da regra de sincronização é **Entrada do AD – AccountEnabled do Usuário**.  

    ![Informações de linhagem de um usuário](./media/tshoot-connect-password-hash-synchronization/cspasswordsync.png)  

    i. Clique em **Propriedades de objeto do metaverso** para exibir uma lista de atributos do usuário.  

    ![Captura de tela que mostra a lista de atributos de usuário para as propriedades do objeto de metaverso.](./media/tshoot-connect-password-hash-synchronization/mvpasswordsync.png)  

    Verifique se não há nenhum atributo **cloudFiltered** presente. Verifique se os atributos de domínio (domainFQDN e domainNetBios) têm os valores esperados.

    j. Clique na guia **conectores** . Verifique se você vê conectores no local Active Directory e no Azure AD.

    ![Informações de metaverso](./media/tshoot-connect-password-hash-synchronization/mvconnectors.png)  

    k. Selecione a linha que representa o Azure AD, clique em **Propriedades** e, em seguida, clique na guia **linhagem** . O objeto de espaço do conector deve ter uma regra de saída na coluna **PasswordSync** definida como **true**. Na configuração padrão, o nome da regra de sincronização é **Saída para AAD – Ingresso do Usuário**.  

    ![Caixa de diálogo Propriedades do objeto de espaço do conector](./media/tshoot-connect-password-hash-synchronization/cspasswordsync2.png)  

### <a name="password-sync-log"></a>Log de sincronização de senha

A coluna de status pode ter os seguintes valores:

| Status | Descrição |
| --- | --- |
| Êxito |A senha foi sincronizada com êxito. |
| FilteredByTarget |A senha está definida para **O usuário deve alterar a senha no próximo logon**. A senha não foi sincronizada. |
| NoTargetConnection |Nenhum objeto no metaverso ou no espaço conector do AD do Azure. |
| SourceConnectorNotPresent |Nenhum objeto encontrado no espaço conector do Active Directory local. |
| TargetNotExportedToDirectory |O objeto no espaço conector do AD do Azure ainda não foi exportado. |
| MigratedCheckDetailsForMoreInfo |A entrada de log foi criada antes da versão 1.0.9125.0 e é mostrada em seu estado herdado. |
| Erro |O serviço retornou um erro desconhecido. |
| Unknown |Ocorreu um erro ao tentar processar um lote de hashes de senha.  |
| MissingAttribute |Atributos específicos (por exemplo, o hash de Kerberos) exigidos pelos Azure AD Domain Services não estão disponíveis. |
| RetryRequestedByTarget |Atributos específicos (por exemplo, o hash de Kerberos) exigidos pelos Azure AD Domain Services não estavam disponíveis anteriormente. É feita uma tentativa de sincronizar novamente o hash de senha do usuário. |

## <a name="scripts-to-help-troubleshooting"></a>Scripts para ajudar na solução de problemas

### <a name="get-the-status-of-password-sync-settings"></a>Obter o status das configurações da sincronização de senha

```powershell
Import-Module ADSync
$connectors = Get-ADSyncConnector
$aadConnectors = $connectors | Where-Object {$_.SubType -eq "Windows Azure Active Directory (Microsoft)"}
$adConnectors = $connectors | Where-Object {$_.ConnectorTypeName -eq "AD"}
if ($aadConnectors -ne $null -and $adConnectors -ne $null)
{
    if ($aadConnectors.Count -eq 1)
    {
        $features = Get-ADSyncAADCompanyFeature
        Write-Host
        Write-Host "Password sync feature enabled in your Azure AD directory: "  $features.PasswordHashSync
        foreach ($adConnector in $adConnectors)
        {
            Write-Host
            Write-Host "Password sync channel status BEGIN ------------------------------------------------------- "
            Write-Host
            Get-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector.Name
            Write-Host
            $pingEvents =
                Get-EventLog -LogName "Application" -Source "Directory Synchronization" -InstanceId 654  -After (Get-Date).AddHours(-3) |
                    Where-Object { $_.Message.ToUpperInvariant().Contains($adConnector.Identifier.ToString("D").ToUpperInvariant()) } |
                    Sort-Object { $_.Time } -Descending
            if ($pingEvents -ne $null)
            {
                Write-Host "Latest heart beat event (within last 3 hours). Time " $pingEvents[0].TimeWritten
            }
            else
            {
                Write-Warning "No ping event found within last 3 hours."
            }
            Write-Host
            Write-Host "Password sync channel status END ------------------------------------------------------- "
            Write-Host
        }
    }
    else
    {
        Write-Warning "More than one Azure AD Connectors found. Please update the script to use the appropriate Connector."
    }
}
Write-Host
if ($aadConnectors -eq $null)
{
    Write-Warning "No Azure AD Connector was found."
}
if ($adConnectors -eq $null)
{
    Write-Warning "No AD DS Connector was found."
}
Write-Host
```

#### <a name="trigger-a-full-sync-of-all-passwords"></a>Disparar uma sincronização completa de todas as senhas

> [!NOTE]
> Execute este script apenas uma vez. Se for necessário executá-lo mais de uma vez, haverá algum outro problema. Para solucionar o problema, contate o suporte da Microsoft.

Você pode disparar uma sincronização completa de todas as senhas usando o script a seguir:

```powershell
$adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"
$aadConnector = "<CASE SENSITIVE AAD CONNECTOR NAME>"
Import-Module adsync
$c = Get-ADSyncConnector -Name $adConnector
$p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
$p.Value = 1
$c.GlobalParameters.Remove($p.Name)
$c.GlobalParameters.Add($p)
$c = Add-ADSyncConnector -Connector $c
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $false
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $true
```

## <a name="next-steps"></a>Próximas etapas

* [Implementação de sincronização de hash de senha com a sincronização do Azure AD Connect](how-to-connect-password-hash-synchronization.md)
* [Sincronização do Azure AD Connect: personalizando as opções de sincronização](how-to-connect-sync-whatis.md)
* [Integração de suas identidades locais com o Active Directory do Azure](whatis-hybrid-identity.md)
