---
title: 'Azure AD Connect: solução de problemas de autenticação de passagem | Microsoft Docs'
description: Este artigo descreve como solucionar problemas de Autenticação de Passagem do Azure AD (Azure Active Directory).
services: active-directory
keywords: Solução de problemas de Autenticação de Passagem do Azure AD Connect, instalar o Active Directory, componentes necessários do Azure AD, SSO, Logon Único
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 01/25/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9a014bd5c8f1edbfb00019b8541cef552271d65b
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/25/2021
ms.locfileid: "98762853"
---
# <a name="troubleshoot-azure-active-directory-pass-through-authentication"></a>Solucionar problemas de Autenticação de Passagem do Azure Active Directory

Este artigo ajuda você a localizar informações de solução de problemas comuns relacionados à autenticação de passagem do Azure AD.

>[!IMPORTANT]
>Se você está enfrentando problemas de conexão de usuário com a autenticação de passagem, não desabilite o recurso nem desinstale os agentes de autenticação de passagem sem ter uma conta Administrador Global somente de nuvem à qual realizar fallback. Saiba mais sobre como [Adicionar uma conta de administrador global somente em nuvem](../fundamentals/add-users-azure-active-directory.md). A realização dessa etapa é fundamental e garante que você não ficará bloqueado do seu locatário.

## <a name="general-issues"></a>Problemas gerais

### <a name="check-status-of-the-feature-and-authentication-agents"></a>Verificar o status do recurso e dos Agentes de autenticação

Verifique se o recurso de Autenticação de passagem ainda está **Habilitado** em seu locatário e se o status dos Agentes de autenticação aparece como **Ativo** e não **Inativo**. Você pode verificar o status indo até a folha **Azure AD Connect** no [centro de administração do Azure Active Directory](https://aad.portal.azure.com/).

![Centro de administração do Azure Active Directory - folha Azure AD Connect](./media/tshoot-connect-pass-through-authentication/pta7.png)

![Centro de administração do Azure Active Directory - folha Autenticação de Passagem](./media/tshoot-connect-pass-through-authentication/pta11.png)

### <a name="user-facing-sign-in-error-messages"></a>Mensagens de erro de entrada voltadas ao usuário

Se o usuário não consegue entrar usando a autenticação de passagem, ele poderá ver um dos seguintes erros voltados ao usuário na tela de entrada do Azure AD: 

|Erro|Descrição|Resolução
| --- | --- | ---
|AADSTS80001|Não é possível conectar ao Active Directory|Verifique se os servidores do agente são membros da mesma floresta do AD que os usuários cujas senhas precisam ser validadas e se são capazes de se conectar ao Active Directory.  
|AADSTS8002|Um tempo limite ao estabelecer conexão com o Active Directory|Verifique se o Active Directory está disponível e respondendo às solicitações dos agentes.
|AADSTS80004|O nome de usuário passado para o agente não era válido|Verifique se o usuário está tentando fazer logon com o nome de usuário correto.
|AADSTS80005|Validação encontrou WebException imprevisível|Um erro transitório. Tente novamente a solicitação. Caso a falha persista, contate o Suporte da Microsoft.
|AADSTS80007|Erro na comunicação com o Active Directory|Confira os logs do agente para obter mais informações e verifique se o Active Directory está funcionando conforme o esperado.

### <a name="users-get-invalid-usernamepassword-error"></a>Os usuários obtêm erro de nome de usuário/senha inválido 

Isso pode acontecer quando o UserPrincipalName local de um usuário (UPN) é diferente do UPN da nuvem do usuário.

Para confirmar que esse é o problema, primeiro teste se o agente de autenticação de passagem está funcionando corretamente:


1. Crie uma conta de teste.  
2. Importe o módulo do PowerShell no computador do agente:

 ```powershell
 Import-Module "C:\Program Files\Microsoft Azure AD Connect Authentication Agent\Modules\PassthroughAuthPSModule\PassthroughAuthPSModule.psd1"
 ```
3. Execute o comando Invoke PowerShell: 

 ```powershell
 Invoke-PassthroughAuthOnPremLogonTroubleshooter 
 ``` 
4. Quando for solicitado que você insira as credenciais, insira o mesmo nome de usuário e senha que são usados para entrar no ( https://login.microsoftonline.com) .

Se você receber o mesmo erro de nome de usuário/senha, isso significa que o agente de autenticação de passagem está funcionando corretamente e o problema pode ser que o UPN local não é roteável. Para saber mais, consulte [Configurando a ID de logon alternativa](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id).

> [!IMPORTANT]
> Se o servidor de Azure AD Connect não estiver ingressado no domínio, um requisito mencionado em [Azure ad Connect: pré-requisitos](./how-to-connect-install-prerequisites.md#installation-prerequisites), ocorrerá o problema inválido de nome de usuário/senha.

### <a name="sign-in-failure-reasons-on-the-azure-active-directory-admin-center-needs-premium-license"></a>Motivos de falha de conexão no centro de administração do Azure Active Directory (é necessário licença Premium)

Se o locatário tiver uma licença do Azure AD Premium associada a ele, você também poderá analisar o [relatório de atividade de entrada](../reports-monitoring/concept-sign-ins.md) no [Centro de administração do Azure Active Directory](https://aad.portal.azure.com/).

![Centro de administração do Azure Active Directory - relatório Entradas](./media/tshoot-connect-pass-through-authentication/pta4.png)

Navegue até **Azure Active Directory**  ->  **entradas** no [centro de administração do Azure Active Directory](https://aad.portal.azure.com/) e clique em uma atividade de entrada do usuário específico. Procure o campo **CÓDIGO DE ERRO DE LOGON**. Faça o mapeamento do valor desse campo até um motivo da falha e uma resolução usando a tabela a seguir:

|Código de erro de logon|Motivo da falha no logon|Resolução
| --- | --- | ---
| 50144 | A senha do Active Directory do usuário expirou. | Redefina a senha do usuário em seu Active Directory local.
| 80001 | Nenhum Agente de Autenticação disponível. | Instale e registre um Agente de Autenticação.
| 80002 | A solicitação de validação de senha do Agente de Autenticação atingiu o tempo limite. | Verifique se o seu Active Directory está acessível por meio do Agente de Autenticação.
| 80003 | Resposta inválida recebida pelo Agente de Autenticação. | Se o problema puder ser reproduzido de forma consistente entre vários usuários, verifique a configuração do Active Directory.
| 80004 | Nome UPN incorreto usado na solicitação de entrada. | Peça ao usuário para entrar com o nome de usuário correto.
| 80005 | Agente de Autenticação: ocorreu um erro. | Erro transitório. Tente novamente depois.
| 80007 | O Agente de Autenticação não pode se conectar ao Active Directory. | Verifique se o seu Active Directory está acessível por meio do Agente de Autenticação.
| 80010 | O Agente de Autenticação não pode descriptografar a senha. | Se o problema puder ser reproduzido consistentemente, instale e registre um novo Agente de Autenticação. E desinstale o atual. 
| 80011 | O Agente de Autenticação não pode recuperar a chave de descriptografia. | Se o problema puder ser reproduzido consistentemente, instale e registre um novo Agente de Autenticação. E desinstale o atual.
| 80014 | A solicitação de validação respondeu após o tempo máximo decorrido excedido. | O agente de autenticação atingiu o tempo limite. Abra um tíquete de suporte com o código de erro, ID de correlação e carimbo de data/hora para obter mais detalhes sobre este erro

>[!IMPORTANT]
>Os agentes de autenticação de passagem autenticam os usuários do Azure AD Validando seus nomes de usuário e senhas em relação à Active Directory chamando a [API do LogonUser do Win32](/windows/win32/api/winbase/nf-winbase-logonusera). Como resultado, se você tiver definido a configuração "logon para" no Active Directory para limitar o acesso de logon da estação de trabalho, você terá que adicionar servidores que hospedam agentes de autenticação de passagem à lista de "fazer logon em" servidores também. A falha ao fazer isso impedirá que os usuários entrem no Azure AD.

## <a name="authentication-agent-installation-issues"></a>Problemas de instalação do Agente de Autenticação

### <a name="an-unexpected-error-occurred"></a>Erro inesperado

[Colete os logs do agente](#collecting-pass-through-authentication-agent-logs) do servidor e entre em contato com o Suporte da Microsoft com o seu problema.

## <a name="authentication-agent-registration-issues"></a>Problemas de registro do Agente de Autenticação

### <a name="registration-of-the-authentication-agent-failed-due-to-blocked-ports"></a>O registro do Agente de Autenticação falhou devido a portas bloqueadas

Verifique se o servidor no qual o Agente de Autenticação foi instalado pode se comunicar com as URLs e portas do nosso serviço listadas [aqui](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites).

### <a name="registration-of-the-authentication-agent-failed-due-to-token-or-account-authorization-errors"></a>Falha no registro do Agente de Autenticação devido a erros de autorização de token ou de conta

Use uma conta Administrador Global somente de nuvem para todas as operações de registro e instalação de Agente de Autenticação do Azure AD Connect ou autônomo. Há um problema conhecido com contas de Administrador Global habilitadas para MFA; desative a MFA temporariamente como uma solução alternativa (somente para concluir as operações).

### <a name="an-unexpected-error-occurred"></a>Erro inesperado

[Colete os logs do agente](#collecting-pass-through-authentication-agent-logs) do servidor e entre em contato com o Suporte da Microsoft com o seu problema.

## <a name="authentication-agent-uninstallation-issues"></a>Problemas de desinstalação do Agente de Autenticação

### <a name="warning-message-when-uninstalling-azure-ad-connect"></a>Mensagem de aviso ao desinstalar o Azure AD Connect

Se você tiver a Autenticação de Passagem habilitada em seu locatário e tentar desinstalar o Azure AD Connect, será mostrada a seguinte mensagem de aviso: "Os usuários não poderão entrar no Azure AD, a menos que você tenha outros agentes de Autenticação de Passagem instalados em outros servidores."

Verifique se a configuração está [altamente disponível](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability) antes de desinstalar o Azure AD Connect para evitar falha de entrada de usuário.

## <a name="issues-with-enabling-the-feature"></a>Problemas com a habilitação do recurso

### <a name="enabling-the-feature-failed-because-there-were-no-authentication-agents-available"></a>A habilitação do recurso falhou porque não haviam Agentes de Autenticação disponíveis

Você precisa ter pelo menos um Agente de Autenticação ativo para habilitar a Autenticação de Passagem em seu locatário. Você pode instalar um Agente de Autenticação através da instalação do Azure AD Connect ou de um Agente de Autenticação autônomo.

### <a name="enabling-the-feature-failed-due-to-blocked-ports"></a>A habilitação do recurso falhou devido a portas bloqueadas

Certifique-se de que o servidor no qual o Azure AD Connect está instalado possa se comunicar com as URLs e portas de nosso serviço listadas [aqui](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites).

### <a name="enabling-the-feature-failed-due-to-token-or-account-authorization-errors"></a>A habilitação do recurso falhou devido a erros de autorização de token ou de conta

Use uma conta de Administrador Global somente de nuvem ao habilitar o recurso. Há um problema conhecido com contas de Administrador Global habilitadas para MFA (autenticação multifator); desative a MFA temporariamente como uma solução alternativa (somente para concluir as operações).

## <a name="collecting-pass-through-authentication-agent-logs"></a>Coletar logs do Agente de Autenticação de Passagem

Dependendo do tipo de problema que você tem, é preciso examinar locais diferentes em busca de logs do Agente de Autenticação de Passagem.

### <a name="azure-ad-connect-logs"></a>Logs do Azure AD Connect

Para erros relacionados à instalação, consulte os logs do Azure AD Connect em **%ProgramData%\AADConnect\trace-\*.log**.

### <a name="authentication-agent-event-logs"></a>Logs de eventos do Agente de Autenticação

Para erros relacionados ao Agente de autenticação, abra o aplicativo Visualizador de Eventos no servidor e verifique em **Application and Service Logs\Microsoft\AzureAdConnect\AuthenticationAgent\Admin**.

Para análises detalhadas, habilite o log de "Sessão" (botão direito do mouse dentro do aplicativo de Visualizador de eventos para encontrar essa opção). Não execute o Agente de Autenticação com esse log habilitado durante operações normais; use somente para solução de problemas. O conteúdo do log é visível somente depois que o log está desabilitado novamente.



### <a name="detailed-trace-logs"></a>Logs de rastreamento detalhados

Para solucionar problemas de falhas de conexão do usuário, procure os logs de rastreamento em **%ProgramData%\Microsoft\Azure AD Connect Authentication Agent\Trace\\**. Esses logs incluem os motivos pelos quais um usuário específico falha ao entrar usando o recurso de Autenticação de Passagem. Esses erros também são mapeados para os motivos de falha de entrada mostrados na tabela de motivos de falha de entrada anterior. A seguir está um exemplo de entrada de log:

```
    AzureADConnectAuthenticationAgentService.exe Error: 0 : Passthrough Authentication request failed. RequestId: 'df63f4a4-68b9-44ae-8d81-6ad2d844d84e'. Reason: '1328'.
        ThreadId=5
        DateTime=xxxx-xx-xxTxx:xx:xx.xxxxxxZ
```

Você pode obter detalhes descritivos do erro ("1328" no exemplo anterior) abrindo o prompt de comando e executando o comando a seguir. (Observação: substitua "1328" pelo número de erro real que você vê em seus logs):

`Net helpmsg 1328`

![Autenticação de Passagem](./media/tshoot-connect-pass-through-authentication/pta3.png)

### <a name="domain-controller-logs"></a>Logs do controlador de domínio

Se o log de auditoria estiver habilitado, será possível encontrar informações adicionais no logs de segurança dos controladores de domínio. Uma maneira simples de consultar solicitações de entrada enviadas pelos Agentes de Autenticação de Passagem é a seguinte:

```
    <QueryList>
    <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ProcessName'] and (Data='C:\Program Files\Microsoft Azure AD Connect Authentication Agent\AzureADConnectAuthenticationAgentService.exe')]]</Select>
    </Query>
    </QueryList>
```

## <a name="performance-monitor-counters"></a>Contadores do Monitor de Desempenho

Outra maneira de monitorar agentes de autenticação é o controle de contadores do Monitor de Desempenho específicos em cada servidor em que o agente de autenticação está instalado. Use os seguintes contadores Globais (**Nº de autenticações PTA**, **Nº de autenticações PTA com falha** e **Nº de autenticações PTA com êxito**) e contadores de Erro (**Nº de erros de autenticação PTA**):

![Contadores do Monitor de Desempenho de Autenticação de Passagem](./media/tshoot-connect-pass-through-authentication/pta12.png)

>[!IMPORTANT]
>A Autenticação de Passagem fornece alta disponibilidade usando vários Agentes de Autenticação e _não_ o balanceamento de carga. Dependendo da configuração, _nem_ todos os seus Agentes de Autenticação receberão um número de solicitações aproximadamente _igual_. É possível que um Agente de Autenticação específico não receba nenhum tráfego.
