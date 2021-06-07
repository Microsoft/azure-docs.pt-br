---
title: 'Azure AD Connect: migrar da Federação para o PHS para o Azure AD | Microsoft Docs'
description: Este artigo contém informações sobre como migrar seu ambiente de identidade híbrida da federação para a sincronização de hash de senha.
services: active-directory
author: billmath
manager: daveba
ms.reviewer: martincoetzer
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/29/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: dca888bf9e3dc75e80764949a11d95efe3514635
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96861809"
---
# <a name="migrate-from-federation-to-password-hash-synchronization-for-azure-active-directory"></a>Migrar da federação para a sincronização de hash de senha do Azure Active Directory

Este artigo descreve como mover seus domínios de organização de AD FS (Serviços de Federação do Active Directory) para sincronização de hash de senha.

> [!NOTE]
> Alterar seu método de autenticação requer planejamento, teste e potencialmente tempo de inatividade. A [distribuição em etapas](how-to-connect-staged-rollout.md) fornece uma maneira alternativa de testar e migrar gradualmente da Federação para a autenticação na nuvem usando a sincronização de hash de senha.
>
> Se você planeja usar a distribuição em etapas, lembre-se de desativar os recursos de distribuição em etapas quando terminar de recortar.  Para obter mais informações, consulte [migrar para a autenticação de nuvem usando a distribuição em etapas](how-to-connect-staged-rollout.md)


## <a name="prerequisites-for-migrating-to-password-hash-synchronization"></a>Pré-requisitos para a migração para a sincronização de hash de senha

Os seguintes pré-requisitos são necessários para migrar de usar o AD FS para usar a sincronização de hash de senha.


### <a name="update-azure-ad-connect"></a>Atualizar o Azure AD Connect

No mínimo, para executar com êxito as etapas de migração para a sincronização de hash de senha, você precisará ter o [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) 1.1.819.0. Esta versão contém alterações significativas na maneira como a entrada de conversão é executada, além de reduzir de potencialmente horas para minutos o tempo geral para migração da federação para a autenticação de nuvem.


> [!IMPORTANT]
> Você pode ler em documentação, ferramentas e blogs desatualizados que é necessária conversão do usuário ao converter domínios de identidade federada para identidade gerenciada. A *conversão de usuários* não é mais necessária. A Microsoft está trabalhando para atualizar a documentação e as ferramentas para refletir essa alteração.

Para atualizar Azure AD Connect, conclua as etapas em [Azure ad Connect: Atualize para a versão mais recente](./how-to-upgrade-previous-version.md).

### <a name="password-hash-synchronization-required-permissions"></a>Permissões necessárias para sincronização de hash de senha

Você pode configurar o Azure AD Connect usando configurações expressas ou uma instalação personalizada. Se você usou a opção de instalação personalizada, as [permissões necessárias](./reference-connect-accounts-permissions.md) para a sincronização de hash de senha podem não estar em vigor.

A conta de serviço do AD DS (Active Directory Domain Services) do Azure AD Connect requer as seguintes permissões para sincronizar hashes de senha:

* Replicar alterações de diretório
* Replicar todas as alterações de diretório

Agora é um bom momento para verificar se essas permissões estão em vigor para todos os domínios na floresta.

### <a name="plan-the-migration-method"></a>Planeje o método de migração

Você pode escolher entre dois métodos para migrar de gerenciamento de identidades federadas para sincronização de hash de senha e SSO (logon único) contínuo. O método usado depende de como sua instância do AD FS foi originalmente configurada.

* **Azure AD Connect**. Se você originalmente tiver configurado o AD FS usando o Azure AD Connect, *deverá* alterar para sincronização de hash de senha usando o assistente do Azure AD Connect.

   O Azure AD Connect executa automaticamente o cmdlet **Set-MsolDomainAuthentication** quando você altera o método de entrada do usuário. O Azure AD Connect cancela automaticamente a federação todos os domínios federados verificados no locatário do Azure AD.

   > [!NOTE]
   > No momento, se você tiver usado originalmente o Azure AD Connect para configurar o AD FS, você não poderá evitar cancelar a federação de todos os domínios em seu locatário ao alterar a entrada do usuário para a sincronização de hash de senha. ‎
* **Azure AD Connect com PowerShell**. Você pode usar esse método somente se originalmente não tiver configurado o AD FS usando o Azure AD Connect. Para essa opção, você ainda deve alterar o método de entrada do usuário usando o Assistente do Azure AD Connect. A principal diferença dessa opção é que o assistente não executa automaticamente o cmdlet **Set-MsolDomainAuthentication**. Com essa opção, você tem controle total sobre quais domínios serão convertidos e em qual ordem.

Para entender qual método você deve usar, conclua as etapas nas seções a seguir.

#### <a name="verify-current-user-sign-in-settings"></a>Verificar as configurações atuais de entrada do usuário

Para verificar suas atuais configurações de entrada do usuário:

1. Entre no [portal do Azure AD](https://aad.portal.azure.com/) usando uma conta de Administrador Global.
2. Na seção **Entrada do usuário**, verifique as seguintes configurações:
   * **Federação** é definida como **Habilitada**.
   * **Logon único contínuo** está definido como **Desabilitado**.
   * **Autenticação de passagem** está definida como **Desabilitada**.

   ![Captura de tela das configurações na seção de entrada do Usuário do Azure AD Connect](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image1.png)

#### <a name="verify-the-azure-ad-connect-configuration"></a>Verificar a configuração do Azure AD Connect

1. No seu servidor do Azure AD Connect, abra o Azure AD Connect. Selecione **Configurar**.
2. Na página **Tarefas adicionais**, selecione **Exibir a configuração atual** e, em seguida, selecione **Avançar**.<br />

   ![Captura de tela da opção Exibir configuração atual selecionada na página de Tarefas adicionais](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image2.png)<br />
3. Na página **Examinar sua Solução**, anote o status de **Sincronização de hash de senha**.<br /> 

   * Se **Sincronização de hash de senha** estiver definida como **Desabilitada**, conclua as etapas neste artigo para habilitá-la.
   * Se a **sincronização de hash de senha** estiver definida como **habilitada**, você poderá ignorar a seção **etapa 1: habilitar a sincronização de hash de senha** neste artigo.
4. Na página **Examinar sua solução**, role para baixo até os **AD FS (Serviços de Federação do Active Directory)**.<br />

   * Se a configuração do AD FS aparecer nesta seção, você poderá presumir com segurança que o AD FS foi originalmente configurado por meio do Azure AD Connect. Você pode converter seus domínios de identidade federada em identidade gerenciada usando a opção **Alterar entrada do usuário** do Azure AD Connect. O processo é detalhado na **opção de seção a: alternar da Federação para a sincronização de hash de senha usando Azure ad Connect**.
   * Se o AD FS não está listado nas configurações atuais, você deve converter manualmente seus domínios de identidade federada para identidade gerenciada usando o PowerShell. Para obter mais informações sobre esse processo, consulte a seção **opção B: alternar de Federação para sincronização de hash de senha usando Azure ad Connect e PowerShell**.

### <a name="document-current-federation-settings"></a>Configurações de federação atuais do documento

Para localizar as configurações de Federação atuais, execute o cmdlet **Get-MsolDomainFederationSettings**:

``` PowerShell
Get-MsolDomainFederationSettings -DomainName YourDomain.extention | fl *
```

Exemplo:

``` PowerShell
Get-MsolDomainFederationSettings -DomainName Contoso.com | fl *
```

Verifique quaisquer configurações que possam ter sido personalizadas para sua documentação de implantação e design de federação. Especificamente, procure as personalizações em **PreferredAuthenticationProtocol**, **SupportsMfa** e **PromptLoginBehavior**.

Para obter mais informações, confira estes tópicos:

* [Prompt do AD FS = suporte ao parâmetro de logon](/windows-server/identity/ad-fs/operations/ad-fs-prompt-login)
* [Set-MsolDomainAuthentication](/powershell/module/msonline/set-msoldomainauthentication)

> [!NOTE]
> Se **SupportsMfa** está definido como **Verdadeiro**, você está usando uma solução de autenticação multifator localmente para injetar um desafio de segundo fator no fluxo de autenticação de usuário. Essa configuração não funciona mais para cenários de autenticação do Azure AD depois de converter esse domínio de autenticação federada para gerenciada. Depois de desabilitar a Federação, você separa a relação para a sua Federação local e inclui adaptadores MFA locais. 
>
> Em vez disso, use o serviço baseado em nuvem da autenticação multifator do Azure AD para executar a mesma função. Avalie seus requisitos de autenticação multifator com cuidado antes de continuar. Antes de converter seus domínios, certifique-se de que você entendeu como usar a autenticação multifator do Azure AD, as implicações de licenciamento e o processo de registro do usuário.

#### <a name="back-up-federation-settings"></a>Configurações de federação de backup

Embora nenhuma alteração tenha sido feita para outras partes confiáveis em seu farm do AD FS durante os processos descritos neste artigo, é recomendável que você tenha um backup válido do farm do AD FS do qual você possa restaurar. Você pode criar um backup válido atual usando a [Ferramenta de Restauração Rápida do AD FS](/windows-server/identity/ad-fs/operations/ad-fs-rapid-restore-tool) da Microsoft gratuita. Você pode usar a ferramenta para fazer backup do AD FS e para restaurar um farm existente ou criar um novo farm.

Se você optar por não usar a ferramenta de restauração rápida AD FS, no mínimo, deverá exportar a relação de confiança de terceira parte confiável da plataforma de identidade Microsoft 365 e quaisquer regras de declaração personalizada associadas que você adicionou. Você pode exportar o objeto de confiança de terceira parte confiável e as regras de declaração associadas usando o exemplo do PowerShell a seguir:

``` PowerShell
(Get-AdfsRelyingPartyTrust -Name "Microsoft Office 365 Identity Platform") | Export-CliXML "C:\temp\O365-RelyingPartyTrust.xml"
```

## <a name="deployment-considerations-and-using-ad-fs"></a>Considerações sobre implantação e uso do AD FS

Esta seção descreve considerações sobre implantação e detalhes de como usar o AD FS.

### <a name="current-ad-fs-use"></a>Uso do AD FS atual

Antes de converter a identidade federada para a identidade gerenciada, examine com mais detalhes como você usa atualmente AD FS para o Azure AD, Microsoft 365 e outros aplicativos (confianças de terceira parte confiável). Especificamente, considere os cenários descritos na tabela a seguir:

| If | Então |
|-|-|
| Você planeja continuar usando AD FS com outros aplicativos (além do Azure AD e Microsoft 365). | Depois de converter seus domínios, você usará tanto o AD FS quanto o Azure AD. Considere a experiência do usuário. Em alguns cenários, os usuários podem ser solicitados a autenticar duas vezes: uma vez ao Azure AD (em que um usuário obtém acesso SSO a outros aplicativos, como Microsoft 365), e novamente para todos os aplicativos que ainda estão associados a AD FS como uma relação de confiança de terceira parte confiável. |
| Sua instância do AD FS é muito personalizada e depende das configurações de personalização específicas no arquivo onload.js (por exemplo, se você tiver alterado a experiência de conexão para que os usuários usem apenas um formato **SamAccountName** para o nome de usuário, em vez de um nome UPN, ou sua organização tiver aplicado fortemente a identidade de marca à experiência de conexão). O arquivo onload.js não pode ser duplicado no Azure AD. | Antes de continuar, verifique se que o Azure AD pode atender aos seus atuais requisitos de personalização. Para obter mais informações e diretrizes, veja as seções sobre identidade visual do AD FS e personalização do AD FS.|
| Você usa o AD FS para bloquear versões anteriores de clientes de autenticação.| Considere substituir os controles de AD FS que bloqueiam versões anteriores de clientes de autenticação usando uma combinação de [controles de acesso condicional](../conditional-access/concept-conditional-access-conditions.md) e [regras de acesso para cliente do Exchange Online](/exchange/clients-and-mobile-in-exchange-online/client-access-rules/client-access-rules). |
| Você exige que os usuários realizem a autenticação multifator em relação a uma solução de servidor de autenticação multifator local quando os usuários se autenticam para o AD FS.| Em um domínio de identidade gerenciada, você não pode injetar um desafio de autenticação multifator por meio da solução de autenticação multifator local no fluxo de autenticação. No entanto, você pode usar o serviço de autenticação multifator do Azure AD para autenticação multifator depois que o domínio é convertido.<br /><br /> Se os usuários não usam atualmente a autenticação multifator do Azure AD, é necessária uma etapa de registro de usuário de OneTime. Você deve preparar e comunicar o registro planejado a seus usuários. |
| Atualmente, você usa políticas de controle de acesso (regras de AuthZ) em AD FS para controlar o acesso ao Microsoft 365.| Considere substituir as políticas com as [políticas de acesso condicional](../conditional-access/overview.md) do Azure ad equivalentes e as [regras de acesso para cliente do Exchange Online](/exchange/clients-and-mobile-in-exchange-online/client-access-rules/client-access-rules).|

### <a name="common-ad-fs-customizations"></a>Personalizações de comuns do AD FS

Esta seção descreve as personalizações comuns do AD FS.

#### <a name="insidecorporatenetwork-claim"></a>Declaração de InsideCorporateNetwork

O AD FS emite a declaração **InsideCorporateNetwork** se o usuário que está realizando a autenticação está dentro da rede corporativa. Essa declaração então pode ser passada para o Azure AD. A declaração é usada para ignorar a autenticação multifator com base no local da rede do usuário. Para saber como determinar se essa funcionalidade no momento está habilitada no AD FS, veja [IPs confiáveis para usuários federados](../authentication/howto-mfa-adfs.md).

A declaração **InsideCorporateNetwork** não está mais disponível depois da conversão de seus domínios em sincronização de hash de senha. Você agora usa [localizações nomeadas no Azure AD](../reports-monitoring/quickstart-configure-named-locations.md) para substituir essa funcionalidade.

Depois de configurar os locais nomeados, você deve atualizar todas as políticas de acesso condicional que foram configuradas para incluir ou excluir a rede **todos os locais confiáveis** ou valores de **IPs confiáveis MFA** para refletir os novos locais nomeados.

Para obter mais informações sobre a condição de **local** no acesso condicional, consulte [Active Directory locais de acesso condicional](../conditional-access/location-condition.md).

#### <a name="hybrid-azure-ad-joined-devices"></a>Dispositivos ingressados no Azure AD híbrido

Ao unir um dispositivo ao Azure AD, você pode criar regras de acesso condicional que impõem que os dispositivos atendam aos seus padrões de acesso para segurança e conformidade. Além disso, os usuários podem entrar em um dispositivo usando uma conta corporativa ou escolar, em vez de uma conta pessoal. Quando você usa dispositivos ingressados no Azure AD híbrido, pode ingressar seus dispositivos ingressados em domínio do Active Directory no Azure AD. Seu ambiente federado pode ter sido configurado para usar esse recurso.

Para garantir que a junção híbrida continue funcionando para todos os dispositivos que ingressaram no domínio depois que os domínios são convertidos para a sincronização de hash de senha, para clientes do Windows 10, você deve usar Azure AD Connect opções de dispositivo para sincronizar Active Directory contas de computador com o Azure AD. 

Para contas de computador do Windows 8 e Windows 7, a associação híbrida usa SSO contínuo para registrar o computador no Azure AD. Você não precisa sincronizar contas de computador do Windows 8 e do Windows 7 como faz para dispositivos Windows 10. No entanto, você deve implantar um arquivo atualizado workplacejoin.exe (por meio de um arquivo. msi) para os clientes do Windows 8 e do Windows 7 para que eles possam ser registrados usando SSO contínuo. [Baixe o arquivo .msi](https://www.microsoft.com/download/details.aspx?id=53554).

Para obter mais informações, veja [Configurar dispositivos ingressados no Azure AD híbrido](../devices/hybrid-azuread-join-plan.md).

#### <a name="branding"></a>Identidade visual

Se sua organização tiver [personalizadas suas páginas de entrada no AD FS](/windows-server/identity/ad-fs/operations/ad-fs-user-sign-in-customization) para exibir informações mais pertinentes para a organização, considere fazer [personalizações de página de entrada do Azure AD](../fundamentals/customize-branding.md) similares.

Embora personalizações similares estejam disponíveis, algumas alterações visuais em páginas de entrada devem ser esperadas após a conversão. Você talvez queira fornecer informações sobre as alterações esperadas em suas comunicações com os usuários.

> [!NOTE]
> A identidade visual da organização estará disponível somente se você comprar a licença Premium ou básica para Azure Active Directory ou se tiver uma licença de Microsoft 365.

## <a name="plan-deployment-and-support"></a>Planejar a implantação e o suporte

Conclua as tarefas descritas nesta seção para ajudá-lo a planejar a implantação e o suporte.

### <a name="plan-the-maintenance-window"></a>Planejar a janela de manutenção

Embora o processo de conversão de domínio seja relativamente rápido, o Azure AD pode continuar a enviar algumas solicitações de autenticação para servidores do AD FS por até quatro horas após a conversão de domínio ter sido concluída. Durante essa janela de quatro horas, e dependendo de vários caches do lado do serviço, o Azure AD pode não aceitar essas autenticações. Os usuários podem receber um erro. O usuário pode se autenticar no AD FS, mas o Azure AD não aceita mais o token emitido pelo usuário porque essa relação de confiança de federação agora foi removida.

Somente os usuários que acessam os serviços por meio de um navegador da Web durante essa janela pós-conversão antes que o cache do lado do serviço seja removido são afetados. Não é esperado que os clientes herdados (Exchange ActiveSync, Outlook 2010/2013) sejam afetados, pois o Exchange Online mantém um cache de suas credenciais por um período definido. O cache é usado para silenciosamente autenticar o usuário outra vez. O usuário não precisa retornar ao AD FS. Credenciais armazenadas no dispositivo para esses clientes são usadas para silenciosamente autenticarem a si próprios outra vez depois que esse cache é limpo. Não é esperado que os usuários recebam qualquer prompt de senha como resultado do processo de conversão de domínio. 

Clientes de autenticação modernos (aplicativos do Office 2016 e Office 2013, iOS e Android) usam um token de atualização válido para obter novos tokens de acesso para acesso contínuo aos recursos, em vez de retornar para o AD FS. Esses clientes são imunes a quaisquer prompts de senha resultantes do processo de conversão de domínio. Os clientes continuarão a funcionar sem configuração adicional.

> [!IMPORTANT]
> Não desligue seu ambiente de AD FS ou remova o Microsoft 365 terceira parte confiável até verificar se todos os usuários podem se autenticar com êxito usando a autenticação de nuvem.

### <a name="plan-for-rollback"></a>Planejar a reversão

Se você encontrar um grande problema que não consiga resolver rapidamente, poderá decidir reverter a solução para federação. É importante planejar o que fazer se a implantação não for distribuída conforme o pretendido. Se a conversão do domínio ou dos usuários falhar durante a implantação ou se for preciso reverter a federação, você deverá compreender como atenuar qualquer eventual interrupção e reduzir o efeito sobre seus usuários.

#### <a name="to-roll-back"></a>Para reverter

Para planejar a reversão, verifique a documentação de implantação e o design da federação para seus detalhes de implantação específicos. O processo deve incluir estas tarefas:

* Converter domínios gerenciados em domínios federados usando o cmdlet **Convert-MSOLDomainToFederated**.
* Se necessário, configurar regras de declarações adicionais.

### <a name="plan-communications"></a>Planejar a comunicação

Uma parte importante do planejamento de implantação e suporte é garantir que os usuários sejam proativamente informados sobre alterações futuras. Os usuários devem saber com antecedência o que eles podem encontrar e o que é exigido deles. 

Depois que a sincronização de hash de senha e o SSO contínuo são implantados, a experiência de entrada do usuário para acessar Microsoft 365 e outros recursos que são autenticados por meio de alterações do Azure AD. Os usuários que estão fora da rede veem apenas a página de entrada no Azure AD. Esses usuários não são redirecionados para a página baseada em formulários apresentada por servidores de proxy de aplicativo Web voltados para o lado externo.

Inclua os seguintes elementos em sua estratégia de comunicação:

* Notificar os usuários sobre funcionalidades futuras e lançadas usando:
   * Email e outros canais de comunicação interna.
   * Elementos visuais, como cartazes.
   * Comunicações executivas, ao vivo ou de outro tipo.
* Determinar quem vai personalizar a comunicação e quem vai enviar as comunicações e quando.

## <a name="implement-your-solution"></a>Implementar sua solução

Você planejou sua solução. Agora, agora você pode implementá-la. A implementação envolve os seguintes componentes:

* Habilitar a sincronização de hash de senha.
* Preparar-se para SSO contínuo.
* Alterar o método de entrada para sincronização de hash de senha e habilitar SSO contínuo.

### <a name="step-1-enable-password-hash-synchronization"></a>Etapa 1: habilitar a sincronização de hash de senha

A primeira etapa para implementar esta solução é habilitar a sincronização de hash de senha usando o assistente do Azure AD Connect. A sincronização de hash de senha é um recurso opcional que você pode habilitar em ambientes que usam federação. Não há efeito sobre o fluxo de autenticação. Nesse caso, o Azure AD Connect iniciará a sincronização de hashes de senha sem afetar usuários que entraram usando federação.

Por esse motivo, recomendamos que você conclua esta etapa como uma tarefa de preparação bem antes de alterar seu método de entrada do domínio. Em seguida, você terá bastante tempo para verificar se a sincronização de hash de senha funciona corretamente.

Para habilitar a sincronização de hash de senha:

1. No servidor do Azure AD Connect, abra o assistente do Azure AD Connect e, em seguida, selecione **Configurar**.
2. Selecione **Personalizar opções de sincronização** e, em seguida, selecione **Avançar**.
3. Na página **Conectar ao Azure AD**, insira o nome de usuário e a senha de uma conta de Administrador Global.
4. Na página **Conectar seus diretórios**, selecione **Avançar**.
5. Na página **Filtragem de domínio e UO**, selecione **Avançar**.
6. Na página **Recursos opcionais**, selecione **Sincronização de senha** e **Avançar**.
 
   ![Captura de tela da opção Sincronização de Senha selecionada na página de Recursos opcionais](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image6.png)<br />
7. Selecione **Avançar** nas páginas restantes. Na última página, selecione **Configurar**.
8. O Azure AD Connect começa a sincronizar hashes de senha na próxima sincronização.

Após a sincronização de hash de senha ter sido habilitada, os hashes de senha para todos os usuários no escopo de sincronização do Azure AD Connect passarão por nova operação de hash e serão gravados no Azure AD. Dependendo do número de usuários, essa operação pode levar minutos ou várias horas.

Para fins de planejamento, você deve estimar que cerca de 20 mil usuários são processados em 1 hora.

Para verificar se a sincronização de hash de senha funciona corretamente, execute a tarefa **Solução de Problemas** no assistente do Azure AD Connect:

1. Abra uma nova sessão do Windows PowerShell no servidor do Azure AD Connect usando a opção Executar como Administrador.
2. Execute `Set-ExecutionPolicy RemoteSigned` ou `Set-ExecutionPolicy Unrestricted`.
3. Iniciar o assistente do Azure AD Connect.
4. Vá para página **Tarefas Adicionais**, selecione **Solucionar problemas** e selecione **Avançar**.
5. Na página **Solução de Problemas**, selecione **Iniciar** para iniciar o menu de solução de problemas no PowerShell.
6. No menu principal, selecione **Solucionar problemas de sincronização de hash de senha**.
7. No submenu, selecione **A Sincronização de hash de senha não funciona**.

Para solucionar problemas, veja [Solucionar problemas de sincronização de hash de senha com a sincronização do Azure AD Connect](./tshoot-connect-password-hash-synchronization.md).

### <a name="step-2-prepare-for-seamless-sso"></a>Etapa 2: preparar para o SSO contínuo

Para que todos os seus dispositivos usem o SSO contínuo, você deve adicionar uma URL do Azure AD às configurações de zona da intranet dos usuários usando a política de grupo no Active Directory.

Por padrão, os navegadores da Web calculam automaticamente a zona correta, seja Internet ou intranet, de uma URL. Por exemplo, **http: \/ \/ contoso/** Maps para a zona da intranet e **http: \/ \/ intranet.contoso.com** mapeia para a zona da Internet (porque a URL contém um ponto). Os navegadores enviam tíquetes Kerberos para um ponto de extremidade da nuvem, como a URL do Azure AD, a menos que você adicione explicitamente a URL à zona da intranet do navegador.

Conclua as [etapas para distribuir](./how-to-connect-sso-quick-start.md) as alterações necessárias para seus dispositivos.

> [!IMPORTANT]
> Fazer essa alteração não modificará a maneira como os usuários entram no Azure AD. No entanto, é importante que você aplique essa configuração a todos os seus dispositivos antes de continuar. Usuários que entram em dispositivos que não receberam essa configuração simplesmente devem digitar um nome de usuário e senha para entrar no Azure AD.

### <a name="step-3-change-the-sign-in-method-to-password-hash-synchronization-and-enable-seamless-sso"></a>Etapa 3: alterar o método de entrada para a sincronização de hash de senha e habilitar o SSO contínuo

Você tem duas opções para alterar o método de entrada para sincronização de hash de senha e habilitar SSO contínuo.

#### <a name="option-a-switch-from-federation-to-password-hash-synchronization-by-using-azure-ad-connect"></a>Opção A: alternar da Federação para a sincronização de hash de senha usando Azure AD Connect

Use esse método se você tiver configurado inicialmente seu ambiente do AD FS usando o Azure AD Connect. Você não poderá usar esse método se *não* tiver configurado originalmente seu ambiente do AD FS usando o Azure AD Connect.

Primeiro, altere o método de entrada:

1. No servidor do Azure AD Connect, abra o Assistente do Azure AD Connect.
2. Selecione **alterar entrada do usuário** e, em seguida, selecione **Avançar**. 

   ![Captura de tela da opção Alterar opção de entrada do usuário na página Tarefas adicionais](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image7.png)<br />
3. Na página **Conectar ao Azure AD**, insira o nome de usuário e a senha de uma conta de Administrador Global.
4. Na página **Entrada do usuário**, selecione o **botão de Sincronização de hash de senha**. Marque a caixa de seleção **Não converter contas de usuário**. Essa opção foi preterida. Selecione **Habilitar o logon único** e selecione **Avançar**.

   ![Captura de tela da página Habilitar logon único](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image8.png)<br />

   > [!NOTE]
   > Do Azure AD Connect versão 1.1.880.0 em diante, a caixa de seleção **Logon único contínuo está selecionada por padrão**.

   > [!IMPORTANT]
   > Você pode ignorar com segurança os avisos indicando que a conversão de usuário e a sincronização de hash de senha completa são etapas necessárias para converter da federação para a autenticação de nuvem. Observe que essas etapas não são mais necessárias. Se ainda vir esses avisos, verifique que você está executando a versão mais recente do Azure AD Connect e usando a versão mais recente deste guia. Para obter mais informações, veja a seção [Atualizar o Azure AD Connect](#update-azure-ad-connect).

5. Na página **Habilitar logon único**, insira as credenciais de uma conta de Administrador de Domínio e, em seguida, selecione **Avançar**.

   ![Captura de tela da página habilitar logon único, na qual você pode inserir as credenciais da conta do administrador de domínio.](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image9.png)<br />

   > [!NOTE]
   > Credenciais da conta de administrador de domínio são necessárias para habilitar SSO contínuo. O processo conclui as seguintes ações, que exigem essas permissões elevadas. As credenciais da conta do Administrador de Domínio não são armazenadas no Azure AD Connect nem no Azure AD. As credenciais da conta de Administrador de Domínio são usadas somente para ativar o recurso. As credenciais são descartadas quando o processo é concluído com êxito.
   >
   > 1. Uma conta de computador denominada AZUREADSSOACC (que representa o Azure AD) é criada em sua instância do Active Directory local.
   > 2. A chave de descriptografia Kerberos da conta do computador é compartilhada com segurança com o Azure AD.
   > 3. Os dois SPNs (nomes de entidade de serviço) Kerberos são criados para representar duas URLs que são usadas durante a entrada no Azure AD.

6. Na página **Pronto para configurar**, verifique se a caixa de seleção **Iniciar o processo de sincronização quando a configuração for concluída** está marcada. Em seguida, selecione **Configurar**.

      ![Captura de tela da página Pronto para configurar](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image10.png)<br />

   > [!IMPORTANT]
   > Neste ponto, todos os seus domínios federados serão alterados para autenticação gerenciada. Sincronização de hash de senha é o novo método de autenticação.

7. No portal do AD do Azure, selecione **Azure Active Directory**  >  **Azure ad Connect**.
8. Verifique estas configurações:
   * **Federação** está definida como **Desabilitada**.
   * **Logon único contínuo** está definido como **Habilitado**.
   * **Sincronização de Senha** está definida como **Habilitada**.<br /> 

   ![Captura de tela que mostra as configurações na seção de entrada do usuário do portal do Azure AD.](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image11.png)<br />

Vá para [Testes e próximas etapas](#testing-and-next-steps).

   > [!IMPORTANT]
   > Ignore a seção **opção B: alternar da Federação para a sincronização de hash de senha usando Azure ad Connect e o PowerShell**. As etapas nesta seção não se aplicarão se você tiver escolhido a opção A para alterar o método de entrada para sincronização de hash de senha e habilitar SSO contínuo.

#### <a name="option-b-switch-from-federation-to-password-hash-synchronization-using-azure-ad-connect-and-powershell"></a>Opção B: alternar da Federação para a sincronização de hash de senha usando o Azure AD Connect e o PowerShell

Use esta opção se você não tiver configurado inicialmente seus domínios federados usando o Azure AD Connect. Durante esse processo, você habilita SSO contínuo e alterna seus domínios de federados para gerenciados.

1. No servidor do Azure AD Connect, abra o Assistente do Azure AD Connect.
2. Selecione **alterar entrada do usuário** e, em seguida, selecione **Avançar**.
3. Na página **Conectar ao Azure AD**, insira o nome de usuário e a senha de uma conta de Administrador Global.
4. Na página de **entrada do usuário** , selecione o botão **sincronização de hash de senha** . Selecione **Habilitar o logon único** e selecione **Avançar**.

   Antes de habilitar a sincronização de hash de senha: ![ captura de tela que mostra a opção não configurar na página de entrada do usuário](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image12.png)<br />

   Depois de habilitar a sincronização de hash de senha: ![ captura de tela que mostra as novas opções na página de entrada do usuário](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image13.png)<br />
   
   > [!NOTE]
   > Do Azure AD Connect versão 1.1.880.0 em diante, a caixa de seleção **Logon único contínuo está selecionada por padrão**.

5. Na página **Habilitar logon único**, insira as credenciais para uma conta de Administrador de Domínio e, em seguida, selecione **Avançar**.

   > [!NOTE]
   > Credenciais da conta de administrador de domínio são necessárias para habilitar SSO contínuo. O processo conclui as seguintes ações, que exigem essas permissões elevadas. As credenciais da conta do Administrador de Domínio não são armazenadas no Azure AD Connect nem no Azure AD. As credenciais da conta de Administrador de Domínio são usadas somente para ativar o recurso. As credenciais são descartadas quando o processo é concluído com êxito.
   >
   > 1. Uma conta de computador denominada AZUREADSSOACC (que representa o Azure AD) é criada em sua instância do Active Directory local.
   > 2. A chave de descriptografia Kerberos da conta do computador é compartilhada com segurança com o Azure AD.
   > 3. Os dois SPNs (nomes de entidade de serviço) Kerberos são criados para representar duas URLs que são usadas durante a entrada no Azure AD.

6. Na página **Pronto para configurar**, verifique se a caixa de seleção **Iniciar o processo de sincronização quando a configuração for concluída** está marcada. Em seguida, selecione **Configurar**.

   ![Captura de tela que mostra o botão Configurar na página Pronto para configurar](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image15.png)<br />
   Quando você seleciona o botão **Configurar**, SSO contínuo é configurado como indicado na etapa anterior. A configuração de sincronização de hash de senha não é modificada porque ela foi habilitada anteriormente.

   > [!IMPORTANT]
   > Nenhuma alteração é feita na forma como os usuários entram neste momento.

7. No portal do Azure AD, verifique estas configurações:
   * **Federação** é definida como **Habilitada**.
   * **Logon único contínuo** está definido como **Habilitado**.
   * **Sincronização de Senha** está definida como **Habilitada**.

   ![Captura de tela que mostra as configurações na seção de Entrada do usuário](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image16.png)

#### <a name="convert-domains-from-federated-to-managed"></a>Converter domínios de federado em gerenciado

Neste ponto, a federação está ainda habilitada e operacional para seus domínios. Para continuar com a implantação, cada domínio precisa ser convertido de federado para gerenciado para forçar a autenticação de usuário por meio da sincronização de hash de senha.

> [!IMPORTANT]
> Você não precisa converter todos os domínios ao mesmo tempo. Você pode optar por começar com um domínio de teste em seu locatário de produção ou iniciar com seu domínio que tem o menor número de usuários.

Conclua a conversão usando o módulo PowerShell do Azure AD:

1. No PowerShell, entre no Azure AD usando uma conta de Administrador Global.
2. Para converter o primeiro domínio, execute o comando a seguir:

   ``` PowerShell
   Set-MsolDomainAuthentication -Authentication Managed -DomainName <domain name>
   ```

3. No portal do AD do Azure, selecione **Azure Active Directory**  >  **Azure ad Connect**.
4. Verifique se o domínio foi convertido em gerenciado, executando o seguinte comando:

   ``` PowerShell
   Get-MsolDomain -DomainName <domain name>
   ```

## <a name="testing-and-next-steps"></a>Testes e próximas etapas

Conclua as seguintes tarefas para verificar a sincronização de hash de senha e concluir o processo de conversão.

### <a name="test-authentication-by-using-password-hash-synchronization"></a>Testar a autenticação usando a sincronização de hash de senha 

Quando seu locatário usava identidade federada, os usuários eram redirecionados da página de entrada do Azure AD para seu ambiente do AD FS. Agora que o locatário está configurado para usar sincronização de hash de senha, em vez da autenticação federada, os usuários não são redirecionados para o AD FS. Em vez disso, os usuários entram diretamente na página de entrada do Azure AD.

Para testar a sincronização de hash de senha:

1. Abra o Internet Explorer no modo InPrivate para que o SSO contínuo não o conecte automaticamente.
2. Vá para a página de entrada do Office 365 ( [https://portal.office.com](https://portal.office.com/) ).
3. Insira um nome UPN do usuário e, em seguida, selecione **Avançar**. Insira o nome UPN de um usuário híbrido que foi sincronizado da instância do Active Directory local e que anteriormente usava autenticação federada. Será exibida uma página na qual você insere o nome de usuário e a senha:

   ![Captura de tela que mostra a página de entrada em que você insere um nome de usuário](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image18.png)

   ![Captura de tela que mostra a página de entrada em que você insere uma senha](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image19.png)

4. Depois de inserir a senha e selecione **Entrar**, você será redirecionado para o portal do Office 365.

   ![Captura de tela que mostra o portal do Office 365](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image20.png)


### <a name="test-seamless-sso"></a>Testar o SSO contínuo

1. Entre em um computador ingressado no domínio que está conectado à rede corporativa.
2. No Internet Explorer ou no Chrome, vá para uma das URLs a seguir (substitua "contoso" pelo seu domínio):

   * https:\/\/myapps.microsoft.com/contoso.com
   * https:\/\/myapps.microsoft.com/contoso.onmicrosoft.com

   O usuário é brevemente redirecionado à página de entrada do Azure AD, que mostra a mensagem "Tentando conectá-lo." Não é solicitado que o usuário informe um nome de usuário ou senha.<br />

   ![Captura de tela que mostra a página de entrada e a mensagem do Azure AD](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image21.png)<br />
3. O usuário é redirecionado e conectado com êxito ao painel de acesso:

   > [!NOTE]
   > O SSO contínuo funciona em serviços Microsoft 365 que dão suporte à dica de domínio (por exemplo, myapps.microsoft.com/contoso.com). Atualmente, o portal.office.com (portal de Microsoft 365) não dá suporte a dicas de domínio. Os usuários precisarão inserir um nome UPN. Depois que um UPN tiver sido inserido, o SSO contínuo recuperará o tíquete Kerberos em nome do usuário. O usuário é conectado sem inserir uma senha.

   > [!TIP]
   > Considere implantar o [Ingresso no Azure AD híbrido no Windows 10](../devices/overview.md) para uma experiência de SSO aprimorada.

### <a name="remove-the-relying-party-trust"></a>Remover o objeto de confiança de terceira parte confiável

Depois de validar que todos os usuários e clientes são autenticados com êxito por meio do Azure AD, é seguro remover o Microsoft 365 confiança de terceira parte confiável.

Se você não usa o AD FS para outras finalidades (ou seja, para outros objetos de confiança de terceira parte confiável), é seguro encerrar o AD FS neste momento.

### <a name="rollback"></a>Reversão

Se você descobrir um grande problema e não puder resolvê-lo rapidamente, poderá reverter a solução para federação.

Veja a documentação de implantação e o design da federação para seus detalhes de implantação específicos. O processo deve envolver estas tarefas:

* Converta domínios gerenciados em autenticação federada usando o cmdlet **Convert-MSOLDomainToFederated**.
* Se necessário, configure regras de declarações adicionais.

### <a name="sync-userprincipalname-updates"></a>Sincronizar atualizações de userPrincipalName

Historicamente, atualizações ao atributo **UserPrincipalName**, que usa o serviço de sincronização do ambiente local, são bloqueadas, a menos que essas duas condições sejam verdadeiras:

* O usuário está em um domínio de identidade gerenciada (não federado).
* Não foi atribuída uma licença ao usuário.

Para saber como verificar ou habilitar esse recurso, veja [Sincronizar atualizações de userPrincipalName](./how-to-connect-syncservice-features.md).

### <a name="troubleshooting"></a>Solução de problemas

A equipe de suporte deve entender como solucionar os problemas de autenticação que ocorrem durante ou após a alteração do modelo de federação para o gerenciado. Use a seguinte documentação de solução de problemas para ajudar sua equipe de suporte a se familiarizar com as etapas comuns de solução de problemas e as ações apropriadas que podem ajudar a isolar e resolver o problema.

[Solucionar problemas Azure Active Directory sincronização de hash de senha](./tshoot-connect-password-hash-synchronization.md)

[Solucionar problemas do logon único contínuo do Azure Active Directory](./tshoot-connect-sso.md)

## <a name="roll-over-the-seamless-sso-kerberos-decryption-key"></a>Substituir a chave de descriptografia do Kerberos de SSO contínuo

É importante sobrepor frequentemente a chave de descriptografia Kerberos da conta de computador AZUREADSSOACC (que representa o Azure AD). A conta de computador AZUREADSSOACC é criada na floresta do Active Directory local. É altamente recomendável que você substitua a chave de descriptografia do Kerberos pelo menos a cada 30 dias para alinhar á maneira como os membros do domínio do Active Directory enviam as alterações de senha. Não há nenhum dispositivo associado anexado ao objeto de conta de computador AZUREADSSOACC, portanto, você deverá executar a sobreposição manualmente.

Inicie a sobreposição da chave de descriptografia Kerberos de SSO contínuo no servidor local que está em execução no Azure AD Connect.

Para obter mais informações, veja [Como sobrepor a chave de descriptografia Kerberos da conta de computador AZUREADSSOACC?](./how-to-connect-sso-faq.md).

## <a name="next-steps"></a>Próximas etapas

* Aprenda os [Conceitos de design do Azure AD Connect](plan-connect-design-concepts.md).
* Escolha a [autenticação correta](./choose-ad-authn.md).
* Saiba mais sobre [topologias com suporte](plan-connect-design-concepts.md).
