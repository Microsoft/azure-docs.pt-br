---
title: Redefinição de senha de autoatendimento para dispositivos Windows-Azure Active Directory
description: Saiba como habilitar a Azure Active Directory redefinição de senha de autoatendimento na tela de entrada do Windows.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/17/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa2d910c017d3cc626f737bdab50315aef8d1e77
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99491378"
---
# <a name="enable-azure-active-directory-self-service-password-reset-at-the-windows-sign-in-screen"></a>Habilitar a Azure Active Directory redefinição de senha de autoatendimento na tela de entrada do Windows

A redefinição de senha de autoatendimento (SSPR) fornece aos usuários no Azure Active Directory (Azure AD) a capacidade de alterar ou redefinir sua senha, sem envolvimento de administrador ou suporte técnico. Normalmente, os usuários abrem um navegador da Web em outro dispositivo para acessar o [portal do SSPR](https://aka.ms/sspr). Para melhorar a experiência em computadores que executam o Windows 7, 8, 8,1 e 10, você pode permitir que os usuários redefinam sua senha na tela de entrada do Windows.

![Exemplo de telas de logon do Windows 7 e 10 que mostra o link do SSPR](./media/howto-sspr-windows/windows-reset-password.png)

> [!IMPORTANT]
> Este tutorial mostra um administrador como habilitar o SSPR para dispositivos Windows em uma empresa.
>
> Se sua equipe de ti não tiver habilitado a capacidade de usar o SSPR do seu dispositivo Windows ou se você tiver problemas durante a entrada, entre em contato com o suporte técnico para obter assistência adicional.

## <a name="general-limitations"></a>Limitações gerais

As seguintes limitações se aplicam ao uso do SSPR na tela de entrada do Windows:

- Atualmente, a redefinição de senha não tem suporte de uma Área de Trabalho Remota ou de sessões avançadas do Hyper-V.
- Alguns provedores de credenciais de terceiros são conhecidos por causar problemas com esse recurso.
- Desabilitação do UAC por meio da modificação de [chave do registro EnableLUA](/openspecs/windows_protocols/ms-gpsb/958053ae-5397-4f96-977f-b7700ee461ec) é conhecida por causar problemas.
- Esse recurso não funciona para redes com autenticação de rede 802.1 x implantada e a opção "executar imediatamente antes do logon do usuário". Para redes com autenticação de rede 802.1 x implantada, é recomendável usar a autenticação do computador para habilitar esse recurso.
- Os computadores ingressados no Azure Active Directory híbrido devem ter a linha de visão de conectividade de rede para um controlador de domínio para usar a nova senha e atualizar as credenciais armazenadas em cache. Isso significa que os dispositivos devem estar na rede interna da organização ou em uma VPN com acesso à rede para um controlador de domínio local.
- Se estiver usando uma imagem, antes de executar o sysprep, veja se o cache da Web está desmarcado para o Administrador interno, antes de executar a etapa CopyProfile. Mais informações sobre esse assunto podem ser encontradas no artigo de suporte [Desempenho insatisfatório ao usar perfil de usuário padrão personalizado](https://support.microsoft.com/help/4056823/performance-issue-with-custom-default-user-profile).
- As configurações a seguir são conhecidas por interferir na capacidade de usar e redefinir senhas em dispositivos Windows 10:
    - Se Ctrl + Alt + Del for exigido pela política no Windows 10, a **redefinição de senha** não funcionará.
    - Se as notificações da tela de bloqueio estiverem desativadas, a opção **Redefinir senha** não funcionará.
    - *HideFastUserSwitching* é definido como Enabled ou 1
    - *DontDisplayLastUserName* é definido como Enabled ou 1
    - *Nobloqueio de tela* está definido como habilitado ou 1
    - *EnableLostMode* está definido no dispositivo
    - Explorer.exe é substituído por um shell personalizado
- A combinação das seguintes três configurações específicas pode fazer com que esse recurso não funcione.
    - Logon interativo: Não exigir CTRL+ALT+DEL = Desabilitado
    - *DisableLockScreenAppNotifications* = 1 ou habilitado
    - O SKU do Windows não é Home ou Professional Edition

> [!NOTE]
> Essas limitações também se aplicam à redefinição de PIN do Windows Hello para empresas na tela de bloqueio do dispositivo.
>

## <a name="windows-10-password-reset"></a>Redefinição de senha do Windows 10

Para configurar um dispositivo Windows 10 para SSPR na tela de entrada, examine os seguintes pré-requisitos e etapas de configuração.

### <a name="windows-10-prerequisites"></a>Pré-requisitos do Windows 10

- Um administrador [deve habilitar a redefinição de senha de autoatendimento do Azure AD no portal do Azure](tutorial-enable-sspr.md).
- Os usuários devem se registrar no SSPR antes de usar esse recurso em [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)
    - Não exclusivo para usar o SSPR da tela de entrada do Windows, todos os usuários devem fornecer as informações de contato de autenticação antes que possam redefinir sua senha.
- Requisitos de proxy de rede:
    - Porta 443 para `passwordreset.microsoftonline.com` e `ajax.aspnetcdn.com`
    - Dispositivos Windows 10 dão suporte apenas à configuração de proxy no nível de computador.
- Executar, pelo menos, o Windows 10, versão Atualização de abril de 2018 (v1803), e os dispositivos precisam estar:
    - Adicionado ao Azure AD
    - Adicionado ao Azure AD híbrido

### <a name="enable-for-windows-10-using-intune"></a>Habilitar para Windows 10 usando o Intune

A implantação da alteração de configuração para habilitar o SSPR na tela de logon usando o Intune é o método mais flexível. O Intune permite que você implante a alteração de configuração para um grupo específico de computadores que você definir. Esse método requer o registro do dispositivo no Intune.

#### <a name="create-a-device-configuration-policy-in-intune"></a>Criar uma política de configuração do dispositivo no Intune

1. Entre no [portal do Azure](https://portal.azure.com) e selecione **Intune**.
1. Crie um novo perfil de configuração de dispositivo acessando o **dispositivo**  >  **perfis** de configuração e, em seguida, selecione **+ Criar perfil**
   - Para **plataforma** *, escolha Windows 10 e posterior*
   - Para **tipo de perfil**, escolha *personalizado*
1. Selecione **criar** e forneça um nome significativo para o perfil, como a *tela de entrada do Windows 10 SSPR*

    Opcionalmente, forneça uma descrição significativa do perfil e, em seguida, selecione **Avançar**.
1. Em *definições de configuração*, selecione **Adicionar** e forneça a seguinte configuração de OMA-URI para habilitar o link Redefinir senha:
      - Forneça um nome significativo para explicar o que a configuração está fazendo, como *Adicionar o link SSPR*.
      - Opcionalmente, forneça uma descrição significativa da configuração.
      - **OMA-URI** definido como `./Vendor/MSFT/Policy/Config/Authentication/AllowAadPasswordReset`
      - **Tipo de dados** definido como **Inteiro**
      - **Valor** definido como **1**

    Selecione **Adicionar** e **Avançar**.
1. A política pode ser atribuída a usuários, dispositivos ou grupos específicos. Atribua o perfil conforme desejado para o seu ambiente, de preferência a um grupo de dispositivos de teste primeiro e selecione **Avançar**.

    Para obter mais informações, consulte [atribuir perfis de usuário e de dispositivo no Microsoft Intune](/mem/intune/configuration/device-profile-assign).

1. Configure as regras de aplicabilidade conforme desejado para o seu ambiente, como para *atribuir o perfil se a edição do sistema operacional for Windows 10 Enterprise* e, em seguida, selecione **Avançar**.
1. Examine seu perfil e, em seguida, selecione **criar**.

### <a name="enable-for-windows-10-using-the-registry"></a>Habilitar para Windows 10 usando o registro

Para habilitar o SSPR na tela de entrada usando uma chave do registro, conclua as seguintes etapas:

1. Entre no computador Windows usando credenciais administrativas.
1. Pressione **Windows**  +  **R** para abrir a caixa de diálogo *executar* e, em seguida, execute **regedit** como administrador
1. Defina a seguinte chave do registro:

    ```cmd
    HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\AzureADAccount
       "AllowPasswordReset"=dword:00000001
    ```

#### <a name="troubleshooting-windows-10-password-reset"></a>Solucionar problemas de redefinição de senha do Windows 10

Se você tiver problemas com o uso do SSPR na tela de entrada do Windows, o log de auditoria do Azure AD incluirá informações sobre o endereço IP e o *ClientType* em que a redefinição de senha ocorreu, conforme mostrado na seguinte saída de exemplo:

![Exemplo de redefinição de senha do Windows 7 no log de auditoria do Azure Active Directory](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

Quando os usuários redefinem sua senha na tela de entrada de um dispositivo Windows 10, uma conta temporária de baixo privilégio chamada `defaultuser1` é criada. Essa conta é usada para manter o processo de redefinição de senha seguro.

A própria conta tem uma senha gerada aleatoriamente, não aparece para entrada do dispositivo e é removida automaticamente depois que o usuário redefine sua senha. Vários perfis `defaultuser` podem existir, mas podem ser ignorados com segurança.

## <a name="windows-7-8-and-81-password-reset"></a>Redefinição de senha do Windows 7, 8 e 8.1

Para configurar um dispositivo Windows 7, 8 ou 8,1 para SSPR na tela de entrada, examine os pré-requisitos e as etapas de configuração a seguir.

### <a name="windows-7-8-and-81-prerequisites"></a>Pré-requisitos do Windows 7, 8 e 8.1

- Um administrador [deve habilitar a redefinição de senha de autoatendimento do Azure AD no portal do Azure](tutorial-enable-sspr.md).
- Os usuários devem se registrar no SSPR antes de usar esse recurso em [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)
    - Não exclusivo para usar o SSPR da tela de entrada do Windows, todos os usuários devem fornecer as informações de contato de autenticação antes que possam redefinir sua senha.
- Requisitos de proxy de rede:
    - Porta 443 para `passwordreset.microsoftonline.com`
- Sistema operacional Windows 7 ou Windows 8.1 corrigido.
- TLS 1.2 habilitado usando a diretriz encontrada em [Configurações de registro do protocolo TLS](/windows-server/security/tls/tls-registry-settings#tls-12).
- Se mais de um provedor de credenciais de terceiros estiver habilitado em seu computador, os usuários verão mais de um perfil de usuário na tela de logon.

> [!WARNING]
> O TLS 1.2 precisa ser habilitado, e não apenas configurado para negociar automaticamente.

### <a name="install"></a>Instalar

Para o Windows 7, 8 e 8,1, um pequeno componente deve ser instalado no computador para habilitar o SSPR na tela de entrada. Para instalar esse componente do SSPR, conclua as seguintes etapas:

1. Baixe o instalador correto para a versão do Windows que você quer habilitar.

    O instalador de software está disponível no centro de download da Microsoft em [https://aka.ms/sspraddin](https://aka.ms/sspraddin)
1. Entre no computador em que você quer instalar e execute o instalador.
1. Após a instalação, uma reinicialização é altamente recomendável.
1. Após a reinicialização, na tela de entrada, escolha um usuário e selecione "esqueceu a senha?" para iniciar o fluxo de trabalho de redefinição de senha.
1. Conclua o fluxo de trabalho seguindo as etapas na tela para redefinir sua senha.

![Exemplo de clique em "Esqueceu a senha?" no Windows 7 Fluxo de SSPR](media/howto-sspr-windows/windows-7-sspr.png)

#### <a name="silent-installation"></a>Instalação silenciosa

O componente SSPR pode ser instalado ou desinstalado sem prompts usando os seguintes comandos:

- Para uma instalação silenciosa, use o comando "msiexec /i SsprWindowsLogon.PROD.msi /qn"
- Para uma desinstalação silenciosa, use o comando "msiexec /x SsprWindowsLogon.PROD.msi /qn"

#### <a name="troubleshooting-windows-7-8-and-81-password-reset"></a>Solucionar problemas de redefinição de senha do Windows 7, 8 e 8.1

Se você tiver problemas com o uso do SSPR na tela de entrada do Windows, os eventos serão registrados no computador e no Azure AD. Os eventos do Azure AD incluem informações sobre o endereço IP e ClientType em que ocorreu a redefinição de senha, conforme mostrado na seguinte saída de exemplo:

![Exemplo de redefinição de senha do Windows 7 no log de auditoria do Azure Active Directory](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

Se for necessário outro registro em log, uma chave do Registro no computador poderá ser alterada para habilitar o registro em log detalhado. Habilite o log detalhado para fins de solução de problemas usando apenas o seguinte valor de chave do registro:

```cmd
HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Authentication\Credential Providers\{86D2F0AC-2171-46CF-9998-4E33B3D7FD4F}
```

- Para habilitar o log detalhado, crie um `REG_DWORD: "EnableLogging"` e o configure como 1.
- Para desabilitar o log detalhado, altere o `REG_DWORD: "EnableLogging"` para 0.

## <a name="what-do-users-see"></a>O que os usuários veem

Com o SSPR configurado para seus dispositivos Windows, o que muda para o usuário? Como eles sabem que podem redefinir sua senha na tela de logon? As capturas de tela de exemplo a seguir mostram as opções adicionais para que um usuário Redefina sua senha usando SSPR:

![Exemplo de telas de logon do Windows 7 e 10 que mostra o link do SSPR](./media/howto-sspr-windows/windows-reset-password.png)

Quando os usuários tentam entrar, eles veem um link **Redefinir senha** ou **esqueceu a senha** que abre a experiência de redefinição de senha de autoatendimento na tela de logon. Essa funcionalidade permite aos usuários redefinir a senha sem a necessidade de usar outro dispositivo para acessar um navegador da Web.

Mais informações para usuários sobre como usar esse recurso podem ser encontradas em [redefinir sua senha corporativa ou de estudante](../user-help/active-directory-passwords-update-your-own-password.md)

## <a name="next-steps"></a>Próximas etapas

Para simplificar a experiência de registro do usuário, você pode [preencher previamente as informações de contato de autenticação do usuário para SSPR](howto-sspr-authenticationdata.md).
