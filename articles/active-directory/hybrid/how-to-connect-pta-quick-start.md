---
title: Autenticação de passagem do Azure AD – início rápido | Microsoft Docs
description: Este artigo descreve como começar a usar a autenticação de passagem do Azure AD (Azure Active Directory).
services: active-directory
keywords: Autenticação de Passagem do Azure AD Connect, instalar o Active Directory, componentes necessários para o Azure AD, SSO, Logon único
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/13/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3200dc6ad7756f77dc0d74df83a33c7e89d4bedb
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105044559"
---
# <a name="azure-active-directory-pass-through-authentication-quickstart"></a>Azure Active Directory autenticação de passagem: início rápido

## <a name="deploy-azure-ad-pass-through-authentication"></a>Implantar a autenticação de passagem do Azure AD

A autenticação de passagem do Azure Active Directory (Azure AD) permite que os usuários entrem em aplicativos locais e baseados em nuvem usando as mesmas senhas. Ela permite a entrada de usuários validando suas senhas diretamente no Active Directory local.

>[!IMPORTANT]
>Se você estiver migrando do AD FS (ou outras tecnologias de federação) para Autenticação de Passagem, é altamente recomendável seguir nosso guia detalhado de implantação publicado [aqui](https://aka.ms/adfstoPTADPDownload).

>[!NOTE]
>Se você estiver implantando a autenticação de passagem com a nuvem do Azure governamental, veja [considerações de identidade híbrida para o Azure governamental](./reference-connect-government-cloud.md).

Siga estas instruções para implantar a Autenticação de Passagem no seu locatário:

## <a name="step-1-check-the-prerequisites"></a>Etapa 1: Verificar os pré-requisitos

Verifique se os pré-requisitos a seguir estão em vigor.

>[!IMPORTANT]
>Do ponto de vista da segurança, os administradores devem tratar o servidor que executa o agente PTA como se fosse um controlador de domínio.  Os servidores do agente PTA devem ser protegidos ao longo das mesmas linhas, conforme descrito em [protegendo controladores de domínio contra ataques](/windows-server/identity/ad-ds/plan/security-best-practices/securing-domain-controllers-against-attack)

### <a name="in-the-azure-active-directory-admin-center"></a>No centro de administração do Azure Active Directory

1. Crie uma conta de administrador global somente em nuvem no seu locatário do Azure AD. Dessa forma, você pode gerenciar a configuração do seu locatário caso seus serviços locais falhem ou fiquem indisponíveis. Saiba mais sobre [adicionar uma conta de administrador global somente em nuvem](../fundamentals/add-users-azure-active-directory.md). A conclusão dessa etapa é essencial para garantir que você não seja bloqueado de seu locatário.
2. Adicione um ou mais [nomes de domínio personalizados](../fundamentals/add-custom-domain.md) ao seu locatário do Azure AD. Os usuários podem entrar com um desses nomes de domínio.

### <a name="in-your-on-premises-environment"></a>Em seu ambiente local

1. Identifique um servidor que execute o Windows Server 2012 R2 ou posterior para executar o Azure AD Connect. Se já não foi habilitado, [habilite o TLS 1.2 no servidor](./how-to-connect-install-prerequisites.md#enable-tls-12-for-azure-ad-connect). Adicione o servidor à mesma floresta do Active Directory dos usuários cujas senhas você precisa validar. Deve-se observar que não há suporte para a instalação do Pass-Through agente de autenticação em versões do Windows Server Core. 
2. Instale a [última versão do Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) no servidor identificado na etapa anterior. Se o Azure AD Connect já está em execução, verifique se a versão é a 1.1.750.0 ou posterior.

    >[!NOTE]
    >As versões 1.1.557.0, 1.1.558.0, 1.1.561.0 e 1.1.614.0 do Azure AD Connect têm um problema relacionado à sincronização de hash de senha. Se você _não_ pretende usar a sincronização de hash de senha em conjunto com a Autenticação de passagem, leia as [Notas de versão do Azure AD Connect](./reference-connect-version-history.md).

3. Identifique um ou mais servidores adicionais (que execute o Windows Server 2012 R2 ou posterior, com o TLS 1.2 habilitado) no qual você possa executar Agentes de Autenticação autônomos. Esses servidores adicionais são necessários para garantir a alta disponibilidade de solicitações de entrada. Adicione os servidores à mesma floresta do Active Directory dos usuários cujas senhas você precisa validar.

    >[!IMPORTANT]
    >Em ambientes de produção, recomendamos ter um mínimo de três Agentes de Autenticação em execução no seu locatário. Há um limite do sistema de 40 Agentes de Autenticação por locatário. Como melhor prática, trate todos os servidores que estão executando Agentes de Autenticação como sistemas de Camada 0 (veja a [referência](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)).

4. Se houver um firewall entre os servidores e o Azure AD, configure os seguintes itens:
   - Certifique-se de que os Agentes de Autenticação podem fazer solicitações de *saída* ao Azure AD sobre as seguintes portas:

     | Número da porta | Como ele é usado |
     | --- | --- |
     | **80** | Baixa as listas de CRLs (certificados revogados) enquanto valida o certificado TLS/SSL |
     | **443** | Lida com toda a comunicação de saída com o serviço |
     | **8080** (opcional) | Agentes de Autenticação relatam seu status de cada dez minutos através da porta 8080, se a porta 443 não estiver disponível. Esse status é exibido no portal do Azure Active Directory. A porta 8080 _não_ é usada para entradas de usuário. |
     
     Se o firewall impõe as regras de acordo com os usuários originadores, abra essas portas para o tráfego proveniente dos serviços Windows que são executados como um serviço de rede.
   - Se o firewall ou o proxy permitir que você adicione entradas DNS a uma permissão, adicione conexões a **\* . msappproxy.net** e **\* . ServiceBus.Windows.net**. Caso contrário, permita o acesso aos [Intervalos de IP do datacenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653), os quais são atualizados semanalmente.
   - Se você tiver um proxy HTTP de saída, verifique se essa URL, autologon.microsoftazuread-sso.com, está na lista de permissões. Você deve especificar essa URL explicitamente, pois o curinga pode não ser aceito. 
   - Os seus Agentes de autenticação devem acessar **login.windows.net** e **login.microsoftonline.net** para o registro inicial. Abra seu firewall para essas URLs também.
    - Para a validação de certificado, desbloqueie as seguintes URLs: **crl3.DigiCert.com:80**, **crl4.DigiCert.com:80**, **ocsp.digicert.com:80**, **www \. d-trust.net:80**, **root-C3-Ca2-2009.OCSP.d-Trust.net:80**, **CRL.Microsoft.com:80**, **oneocsp.Microsoft.com:80** e **OCSP.msocsp.com:80**. Uma vez que essas URLs são usadas para a validação de certificado com outros produtos da Microsoft, você talvez já tenha essas URLs desbloqueadas.

### <a name="azure-government-cloud-prerequisite"></a>Pré-requisito de nuvem do Azure governamental
Antes de habilitar a autenticação de passagem por meio do Azure AD Connect com a etapa 2, baixe a versão mais recente do agente PTA da portal do Azure.  Você precisa garantir que o agente seja de versões **1.5.1742.0.** ou posterior.  Para verificar seu agente, consulte [Atualizar agentes de autenticação](how-to-connect-pta-upgrade-preview-authentication-agents.md)

Depois de baixar a versão mais recente do agente, prossiga com as instruções abaixo para configurar a autenticação Pass-Through por meio de Azure AD Connect.

## <a name="step-2-enable-the-feature"></a>Etapa 2: habilitar o recurso

Habilite a autenticação de passagem por meio de [Azure ad Connect](whatis-hybrid-identity.md).

>[!IMPORTANT]
>É possível habilitar a Autenticação de passagem no servidor primário ou de preparo do Azure AD Connect. É altamente recomendável habilitá-la do servidor primário. Se você estiver configurando um servidor de preparo do Azure AD Connect no futuro, você **devem** continuar para escolher a autenticação de passagem como a opção de entrada; escolhendo outra opção será **desabilitar** Autenticação de passagem no locatário e substituir a configuração no servidor primário.

Se estiver instalando o Azure AD Connect pela primeira vez, escolha o [caminho de instalação personalizado](how-to-connect-install-custom.md). Na página de **entrada do usuário** , escolha autenticação de **passagem** como o método de **logon**. Após a conclusão bem-sucedida, um Agente de autenticação de passagem estará instalado no mesmo servidor do Azure AD Connect. Além disso, o recurso de autenticação de passagem estará habilitado em seu locatário.

![Azure AD Connect: entrada do usuário](./media/how-to-connect-pta-quick-start/sso3.png)

Se você já tiver instalado o Azure AD Connect usando a [instalação expressa](how-to-connect-install-express.md) ou o caminho de [instalação personalizada](how-to-connect-install-custom.md) , selecione a tarefa **alterar entrada do usuário** em Azure ad Connect e, em seguida, selecione **Avançar**. Em seguida, selecione **autenticação de passagem** como o método de entrada. Após a conclusão bem-sucedida, um Agente de autenticação de passagem estará instalado no mesmo servidor que o Azure AD Connect, e o recurso estará habilitado em seu locatário.

![Azure AD Connect: alterar entrada do usuário](./media/how-to-connect-pta-quick-start/changeusersignin.png)

>[!IMPORTANT]
>A Autenticação de Passagem é um recurso no nível do locatário. Ligá-lo afeta a entrada para usuários em _todos_ os domínios gerenciados em seu locatário. Se estiver alternando dos Serviços de Federação do Active Directory (AD FS) para Autenticação de passagem, você deve esperar pelo menos 12 horas antes de desligar a infraestrutura do AD FS. Esse tempo de espera é para garantir que os usuários continuem entrando no Exchange ActiveSync durante a transição. Para obter mais ajuda sobre a migração do AD FS para Autenticação de Passagem, veja o nosso plano detalhado de implantação publicado [aqui](https://aka.ms/adfstoptadpdownload).

## <a name="step-3-test-the-feature"></a>Etapa 3: testar o recurso

Siga estas instruções para verificar se você habilitou a Autenticação de passagem corretamente:

1. Entre no centro de [Administração do Azure Active Directory](https://aad.portal.azure.com) com as credenciais de administrador global para seu locatário.
2. Selecione **Azure Active Directory** no painel esquerdo.
3. Selecione **Azure AD Connect**.
4. Verifique se o recurso **Autenticação de passagem** aparece como **Habilitado**.
5. Selecione **autenticação de passagem**. Esse painel da **Autenticação de passagem** lista os servidores em que os Agentes de autenticação estão instalados.

![Centro de administração do Azure Active Directory: painel do Azure AD Connect](./media/how-to-connect-pta-quick-start/pta7.png)

![Centro de administração do Azure Active Directory: painel da Autenticação de passagem](./media/how-to-connect-pta-quick-start/pta8.png)

Nesse momento, os usuários de todos os domínios gerenciados no seu locatário podem entrar usando a Autenticação de passagem. No entanto, os usuários de domínios federados continuam a entrar usando o AD FS ou outro provedor de federação que já esteja configurado. Se você converter um domínio de federado para gerenciado, todos os usuários nesse domínio passarão automaticamente a entrar usando a Autenticação de passagem. O recurso de Autenticação de passagem os não afeta os usuários somente de nuvem.

## <a name="step-4-ensure-high-availability"></a>Etapa 4: Verificar a alta disponibilidade

Se você planeja implantar autenticação de passagem em um ambiente de produção, instale um Agente de Autenticação autônomo. Instale esses Agentes de Autenticação no(s) servidor(es) _diferente(s)_ do que está executando o Azure AD Connect. Esta configuração fornece alta disponibilidade para solicitações de entrada de usuário.

>[!IMPORTANT]
>Em ambientes de produção, recomendamos ter um mínimo de três Agentes de Autenticação em execução no seu locatário. Há um limite do sistema de 40 Agentes de Autenticação por locatário. Como melhor prática, trate todos os servidores que estão executando Agentes de Autenticação como sistemas de Camada 0 (veja a [referência](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)).

A instalação de vários agentes de autenticação de passagem garante a alta disponibilidade, mas não o balanceamento de carga determinístico entre os agentes de autenticação. Para determinar quantos agentes de autenticação você precisa para seu locatário, considere o pico e a carga média de solicitações de entrada que você espera ver em seu locatário. Como um parâmetro de comparação, um único Agente de autenticação pode manipular de 300 a 400 autenticações por segundo em um servidor padrão com CPU de 4 núcleos e 16 GB de RAM.

Para estimar o tráfego de rede, use as seguintes diretrizes de tamanho:
- Cada solicitação tem um tamanho de carga de (0,5 K + 1K * num_of_agents) bytes, ou seja, dados do Azure AD para o agente de autenticação. Aqui, "num_of_agents" indica o número de Agentes de autenticação registrado no seu locatário.
- Cada resposta tem um tamanho de payload de 1K bytes, ou seja, dados do agente de autenticação para o Azure AD.

Para a maioria dos clientes, três agentes de autenticação no total são suficientes para alta disponibilidade e capacidade. Você deve instalar os Agentes de Autenticação perto de seus controladores de domínio para melhorar a latência de entrada.

Para começar, siga estas instruções para baixar o software do agente de autenticação:

1. Para fazer o download da versão mais recente do Agente de Autenticação (versões 1.5.193.0 ou posteriores), entre no [centro de administração do Azure Active Directory](https://aad.portal.azure.com) com as credenciais de administrador global do seu locatário.
2. Selecione **Azure Active Directory** no painel esquerdo.
3. Selecione **Azure AD Connect**, **Autenticação de passagem** e depois **Baixar Agente**.
4. Selecione o botão **Aceitar termos e baixar**.

![Centro de administração do Azure Active Directory: botão de Baixar Agente de Autenticação](./media/how-to-connect-pta-quick-start/pta9.png)

![Centro de administração do Azure Active Directory: painel Baixar Agente](./media/how-to-connect-pta-quick-start/pta10.png)

>[!NOTE]
>Você também pode [baixar o software Agente de Autenticação](https://aka.ms/getauthagent) diretamente. Leia e aceite os [Termos de Serviço do Agente de Autenticação](https://aka.ms/authagenteula) _antes_ de instalá-lo.

Existem duas formas de implantar um Agente de Autenticação autônomo:

Primeiro, você pode fazer isso de forma interativa simplesmente executando o executável do Agente de Autenticação baixado e fornecendo credenciais de administrador global do locatário quando solicitado.

Segundo, você pode criar e executar um script de implantação autônomo. Isso é útil quando você deseja implantar vários Agentes de Autenticação ao mesmo tempo, ou instalar Agentes de Autenticação em servidores Windows que não possuem a interface do usuário ativada ou que você não pode acessar com a Área de Trabalho Remota. Aqui estão as instruções sobre como usar essa abordagem:

1. Execute o seguinte comando para instalar um Agente de Autenticação: `AADConnectAuthAgentSetup.exe REGISTERCONNECTOR="false" /q`.
2. Você pode registrar o Agente de Autenticação com nosso serviço usando o Windows PowerShell. Crie um objeto de credenciais do PowerShell `$cred` que contém um nome de usuário de administrador global e uma senha para seu locatário. Execute o seguinte comando, substituindo *\<username\>* e *\<password\>* :

  ```powershell
  $User = "<username>"
  $PlainPassword = '<password>'
  $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
  $cred = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $User, $SecurePassword
  ```
3. Vá para **C:\Arquivos de Programas\Microsoft Azure AD Connect Authentication Agent** e execute o seguinte script usando o objeto `$cred` que você criou:

  ```powershell
  RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft Azure AD Connect Authentication Agent\Modules\" -moduleName "PassthroughAuthPSModule" -Authenticationmode Credentials -Usercredentials $cred -Feature PassthroughAuthentication
  ```

>[!IMPORTANT]
>Se um agente de autenticação estiver instalado em uma máquina virtual, você não poderá clonar a máquina virtual para configurar outro agente de autenticação. Não há **suporte para** este método.

## <a name="step-5-configure-smart-lockout-capability"></a>Etapa 5: configurar o recurso de bloqueio inteligente

O bloqueio inteligente ajuda a bloquear atores ruins que estão tentando adivinhar as senhas de seus usuários ou usar métodos de força bruta para entrar. Ao definir as configurações de bloqueio inteligente no Azure AD e/ou configurações de bloqueio apropriadas no Active Directory local, os ataques podem ser filtrados antes de alcançarem Active Directory. Leia [Este artigo](../authentication/howto-password-smart-lockout.md) para saber mais sobre como definir as configurações de bloqueio inteligente em seu locatário para proteger suas contas de usuário.

## <a name="next-steps"></a>Próximas etapas
- [Migrar do AD FS para Autenticação de Passagem](https://aka.ms/adfstoptadp) – um guia detalhado para migrar do AD FS (ou outras tecnologias de federação) para Autenticação de Passagem.
- [Bloqueio Inteligente](../authentication/howto-password-smart-lockout.md): saiba como configurar a capacidade de Bloqueio Inteligente no seu locatário para proteger as contas de usuário.
- [Limitações atuais](how-to-connect-pta-current-limitations.md): saiba quais cenários têm suporte na Autenticação de passagem e quais não têm.
- [Análise técnica aprofundada](how-to-connect-pta-how-it-works.md): entenda como funciona o recurso de Autenticação de passagem.
- [Perguntas frequentes](how-to-connect-pta-faq.md): encontre respostas para perguntas frequentes.
- [Solução de problemas](tshoot-connect-pass-through-authentication.md): saiba como resolver problemas comuns com o recurso de Autenticação de Passagem.
- [Aprofundamento em segurança](how-to-connect-pta-security-deep-dive.md): obtenha informações técnicas sobre o recurso de Autenticação de passagem.
- [SSO contínuo do Azure AD](how-to-connect-sso.md): saiba mais sobre esse recurso complementar.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): use o Fórum do Azure Active Directory para arquivar novas solicitações.
