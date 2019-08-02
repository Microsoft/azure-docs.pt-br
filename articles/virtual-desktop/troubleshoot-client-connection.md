---
title: Conexões de cliente de área de trabalho remota na área de trabalho de Virtual do Windows - Azure
description: Como resolver problemas quando você configurar as conexões de cliente em um ambiente de locatário de área de trabalho Virtual do Windows.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 04/08/2019
ms.author: v-chjenk
ms.openlocfilehash: b7a6daa791e44227fd146c9c328a939560ebb3b1
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67605287"
---
# <a name="remote-desktop-client-connections"></a>Conexões do cliente da Área de Trabalho Remota

Use este artigo para resolver problemas com conexões de cliente de área de trabalho Virtual do Windows.

## <a name="provide-feedback"></a>Fornecer comentários

Atualmente, não estamos usando casos de suporte durante a versão prévia da Área de Trabalho Virtual do Windows. Visite a [Comunidade Tecnológica da Área de Trabalho Virtual do Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) para comentar sobre o serviço da Área de Trabalho Virtual do Windows com a equipe do produto e membros ativos da comunidade.

## <a name="you-cant-open-a-web-client"></a>Não é possível abrir um cliente web

Confirme se não há conectividade com a internet, abrindo a outro site da web; Por exemplo, [www.Bing.com](https://www.bing.com).

Use **nslookup** para confirmar o DNS possa resolver o FQDN:

```cmd
nslookup rdweb.wvd.microsoft.com
```

Tente se conectar com outro cliente, como o cliente de área de trabalho remota para Windows 7 ou Windows 10 e veja se você pode abrir o cliente web.

### <a name="error-opening-another-site-fails"></a>Erro: Abrir outra falha do site

**Causa:** Problemas de rede e/ou interrupções.

**Correção:** Contate o suporte de rede.

### <a name="error-nslookup-cannot-resolve-the-name"></a>Erro: Nslookup não é possível resolver o nome

**Causa:** Problemas de rede e/ou interrupções.

**Correção:** Entre em contato com o suporte de rede

### <a name="error-you-cant-connect-but-other-clients-can-connect"></a>Erro: Você não pode se conectar, mas outros clientes podem se conectar

**Causa:** O navegador não está funcionando como esperado e parado de trabalho.

**Correção:** Siga estas instruções para solucionar problemas de navegador.

1. Reinicie o navegador.
2. Cookies do navegador não criptografado. Ver [como excluir arquivos de cookies no Internet Explorer](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer).
3. Limpe o cache do navegador. Ver [limpar o cache do navegador para o navegador](https://binged.it/2RKyfdU).
4. Abrir navegador em modo privado.

## <a name="web-client-stops-responding-or-disconnects"></a>Cliente Web para de responder ou desconecta

Tente se conectar usando outro navegador ou cliente.

### <a name="error-other-browsers-and-clients-also-malfunction-or-fail-to-open"></a>Erro: Outros navegadores e os clientes também não funcione corretamente ou não abrirem

**Causa:** Problemas de rede e/ou a operação do sistema ou interrupções

**Correção:** Contate o suporte de equipes.

## <a name="web-client-keeps-prompting-for-credentials"></a>Cliente Web mantém solicitar credenciais

Se o cliente Web continua solicitando as credenciais, siga estas instruções.

1. Confirme a que URL do cliente da web está correta.
2. Confirme se as credenciais são para o ambiente de área de trabalho Virtual do Windows vinculado à URL.
3. Cookies do navegador não criptografado. Ver [como excluir arquivos de cookies no Internet Explorer](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer).
4. Limpe o cache do navegador. Ver [limpar o cache do navegador para o navegador](https://binged.it/2RKyfdU).
5. Abrir navegador em modo privado.

## <a name="remote-desktop-client-for-windows-7-or-windows-10-stops-responding-or-cannot-be-opened"></a>Cliente de área de trabalho remota para Windows 7 ou Windows 10 para de responder ou não pode ser aberto

Use os seguintes cmdlets do PowerShell para limpar os registros de cliente fora de banda (OOB).

```PowerShell
Remove-ItemProperty 'HKCU:\Software\Microsoft\Terminal Server Client\Default' - Name FeedURLs

#Remove RdClientRadc registry key
Remove-Item 'HKCU:\Software\Microsoft\RdClientRadc' -Recurse

#Remove all files under %appdata%\RdClientRadc
Remove-Item C:\Users\pavithir\AppData\Roaming\RdClientRadc\* -Recurse
```

Navegue até **%AppData%\RdClientRadc** e excluir todo o conteúdo.

Desinstale e reinstale o cliente de área de trabalho remota para Windows 7 e Windows 10.

## <a name="troubleshooting-end-user-connectivity"></a>Solucionando problemas de conectividade do usuário final

Às vezes, os usuários podem acessar seus recursos locais e o feed, mas ainda tiver problemas de desempenho que impedem que eles acessem recursos remotos, a disponibilidade ou configuração. Nesses casos, o usuário obtém mensagens semelhantes a estas:

![Mensagem de erro de Conexão de área de trabalho remoto.](media/eb76b666808bddb611448dfb621152ce.png)

![Não é possível conectar-se para a mensagem de erro de gateway.](media/a8fbb9910d4672147335550affe58481.png)

Siga estas instruções gerais de solução de problemas para códigos de erro de conexão do cliente.

1. Confirme o nome de usuário e a hora quando o problema ocorreu.
2. Abra **PowerShell** e estabelecer a conexão para o locatário de área de trabalho Virtual do Windows onde o problema foi relatado.
3. Confirme se a conexão para o locatário correto com **RdsTenant Get.**
4. Usando o **Get-RdsHostPool** e **Get-RdsSessionHost** cmdlets, confirme que a solução de problemas está sendo feita no pool de host correto.
5. Execute o comando a seguir para obter uma lista de todas as atividades com falha de conexão de tipo para a janela de tempo especificado:

    ```PowerShell
     Get-RdsDiagnosticActivities -TenantName <TenantName> -username <UPN> -StartTime
     "11/21/2018 1:07:03 PM" -EndTime "11/21/2018 1:27:03 PM" -Outcome Failure -ActivityType Connection
    ```

6. Usando o **ActivityId** na saída do cmdlet anterior, execute o comando a seguir:

    ```PowerShell
    (Get-RdsDiagnosticActivities -TenantName $tenant -ActivityId <ActivityId> -Detailed).Errors
    ```

7. O comando produz uma saída semelhante à saída mostrada abaixo. Use **ErrorCodeSymbolic** e **ErrorMessage** para diagnosticar a causa raiz.

    ```PowerShell
    ErrorSource       : <Source>
    ErrorOperation    : <Operation>
    ErrorCode         : <Error code>
    ErrorCodeSymbolic : <Error code string>
    ErrorMessage      : <Error code message>
    ErrorInternal     : <Internal for the OS>
    ReportedBy        : <Reported by component>
    Time              : <Timestampt>
    ```

### <a name="error-oaddusertogroupfailed--failed-to-add-user--username-to-group--remote-desktop-users-reason-win32errornosuchmember"></a>Erro: O_ADD_USER_TO_GROUP_FAILED / Falha ao adicionar usuário = ≤username≥ ao grupo = Remote Desktop Users. Motivo: Win32.ERROR_NO_SUCH_MEMBER

**Causa:** VM não foi unida ao domínio em que é o objeto de usuário.

**Correção:** Adicione a VM para o domínio correto. Ver [ingressar uma máquina virtual Windows Server em um domínio gerenciado](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-join-windows-vm-portal).

### <a name="error-nslookup-cannot-resolve-the-name"></a>Erro: Nslookup não é possível resolver o nome

**Causa:** Problemas de rede ou interrupções.

**Correção:** Entre em contato com o suporte de rede

### <a name="error-connectionfailedclientprotocolerror"></a>Erro: ConnectionFailedClientProtocolError

**Causa:** As VMs que o usuário está tentando se conectar ao não são ingressados no domínio.

**Correção:** Junte-se todas as VMs que fazem parte de um pool de host para o controlador de domínio.

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>Usuário se conecta, mas nada é exibido (Nenhum feed)

Um usuário pode começar a clientes de área de trabalho remota e é capaz de autenticar, no entanto, o usuário não ver nenhum ícone na descoberta da web feed.

Confirme que o usuário relatar um problema foi atribuído aos grupos de aplicativos usando esta linha de comando:

```PowerShell
Get-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname>
```

Confirme que o usuário está se conectando com as credenciais corretas.

Se o cliente da web está sendo usado, confirme que não há nenhum problema de credenciais armazenadas em cache.

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral sobre solução de área de trabalho Virtual do Windows e as faixas de escalonamento de bloqueios, consulte [solução de problemas de visão geral, comentários e suporte](troubleshoot-set-up-overview.md).
- Para solucionar problemas durante a criação de um pool de locatário e o host em um ambiente de área de trabalho Virtual do Windows, consulte [criação de pool de locatário e host](troubleshoot-set-up-issues.md).
- Para solucionar problemas ao configurar uma máquina virtual (VM) na área de trabalho Virtual do Windows, consulte [configuração de máquina virtual do host de sessão](troubleshoot-vm-configuration.md).
- Para solucionar problemas ao usar o PowerShell com a área de trabalho Virtual do Windows, consulte [Windows PowerShell de área de trabalho Virtual](troubleshoot-powershell.md).
- Para saber mais sobre o serviço de visualização, consulte [ambiente de visualização de área de trabalho do Windows](https://docs.microsoft.com/azure/virtual-desktop/environment-setup?).
- Para percorrer um tutorial de solução de problemas, confira [Tutorial: Solucionar problemas de implantações de modelo do Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot).
- Para saber sobre as ações de auditoria, consulte [Auditar operações com o Gerenciador de Recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Para saber sobre as ações para determinar os erros durante a implantação, consulte [Exibir operações de implantação](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).
