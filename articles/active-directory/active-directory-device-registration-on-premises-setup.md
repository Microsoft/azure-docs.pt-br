---
title: Configurando o acesso condicional local usando o registro de dispositivo do Azure Active Directory | Microsoft Docs
description: "Um guia passo a passo para habilitar o acesso condicional a aplicativos locais usando o AD FS (Serviços de Federação do Active Directory) no Windows Server 2012 R2."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 6ae9df8b-31fe-4d72-9181-cf50cfebbf05
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2017
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 1a6f1c6566468188daa71939db8345280b7a529f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="setting-up-on-premises-conditional-access-by-using-azure-active-directory-device-registration"></a>Configurando o acesso condicional local usando o registro de dispositivo do Azure Active Directory
Quando você exige que os usuários usem o ingresso no local de trabalho para seus dispositivos pessoais no serviço de registro de dispositivo do Azure AD (Azure Active Directory), seus dispositivos poderão ser marcados como conhecidos para sua organização. Veja abaixo um guia passo a passo para habilitar o acesso condicional em aplicativos locais usando o AD FS (Serviços de Federação do Active Directory) no Windows Server 2012 R2.

> [!NOTE]
> Uma licença do Office 365 ou do Azure AD Premium é necessária ao usar dispositivos registrados nas políticas de acesso condicional do serviço de registro de dispositivo do Azure Active Directory. Elas incluem políticas que são impostas pelo AD FS em recursos locais.
> 
> Para obter mais informações sobre os cenários de acesso condicional para recursos locais, consulte [Ingressar no local de trabalho em qualquer dispositivo para SSO e autenticação remota de dois fatores entre aplicativos da empresa](https://technet.microsoft.com/library/dn280945.aspx).

Essas funcionalidades estão disponíveis para os clientes que compram uma licença do Azure Active Directory Premium.

## <a name="supported-devices"></a>Dispositivos com suporte
* Dispositivos Windows 7 ingressados no domínio
* Dispositivos Windows 8.1 pessoais e ingressados no domínio
* iOS 6 e posterior para o navegador Safari
* Android 4.0 ou posterior, Samsung GS3 ou telefones posteriores, Samsung Galaxy Note 2 ou tablets posteriores

## <a name="scenario-prerequisites"></a>Pré-requisitos do cenário
* Assinatura do Office 365 ou do Azure Active Directory Premium
* Um locatário do Azure Active Directory
* Windows Server Active Directory (Windows Server 2008 ou posterior)
* Esquema atualizado no Windows Server 2012 R2
* Licença do Azure Active Directory Premium
* Serviços de Federação do Windows Server 2012 R2, configurados para logon único ao Azure AD
* Proxy de Aplicativo Web do Windows Server 2012 R2 
* Azure AD Connect (Microsoft Azure Active Directory Connect) [(Baixar o Azure AD Connect)](http://www.microsoft.com/en-us/download/details.aspx?id=47594)
* Domínio verificado

## <a name="known-issues-in-this-release"></a>Problemas conhecidos nesta versão
* As políticas de acesso condicional baseadas em dispositivo exigem o write-back de objeto do dispositivo para o Azure Active Directory Active Directory. Pode levar até três horas para que o write-back de objetos do dispositivo seja realizado no Active Directory.
* Os dispositivos iOS 7 sempre solicitam que o usuário selecione um certificado durante a autenticação de certificado do cliente.
* Algumas versões do iOS 8 anteriores ao iOS 8.3 não funcionam.

## <a name="scenario-assumptions"></a>Suposições de cenário
Este cenário pressupõe que você tenha um ambiente híbrido que consiste em um locatário do Azure AD e um Active Directory local. Esses locatários devem ser conectados com o Azure AD Connect, com um domínio verificado e com o AD FS para SSO. Use a lista de verificação a seguir para ajudá-lo a configurar o ambiente de acordo com os requisitos.

## <a name="checklist-prerequisites-for-conditional-access-scenario"></a>Lista de verificação: pré-requisitos para o cenário de acesso condicional
Conecte seu locatário do Azure AD à instância do Active Directory local.

## <a name="configure-azure-active-directory-device-registration-service"></a>Configurar o serviço de registro de dispositivo do Azure Active Directory
Use este guia para implantar e configurar o serviço de registro de dispositivo do Azure Active Directory para sua organização.

Este guia pressupõe que você tenha configurado o Windows Server Active Directory e assinado o Microsoft Azure Active Directory. Consulte os pré-requisitos descritos anteriormente.

Para implantar o serviço de registro de dispositivo do Azure Active Directory com seu locatário do Azure Active Directory, conclua as tarefas na lista de verificação a seguir, na ordem. Quando um link de referência o levar para um tópico conceitual, retorne a esta lista de verificação em seguida, de modo que você possa continuar com as tarefas restantes. Algumas tarefas incluem uma etapa de validação de cenário que pode ajudá-lo a confirmar se a etapa foi concluída com êxito.

## <a name="part-1-enable-azure-active-directory-device-registration"></a>Parte 1: Habilitar o registro de dispositivo do Azure Active Directory
Siga as etapas na lista de verificação para habilitar e configurar o serviço de registro de dispositivo do Azure Active Directory.

| Tarefa | Referência | 
| --- | --- |
| Habilite o registro de dispositivo em seu locatário do Azure Active Directory para permitir que dispositivos ingressem no local de trabalho. Por padrão, a Autenticação Multifator do Azure não está habilitada para o serviço. No entanto, recomendamos usar a Autenticação Multifator ao registrar um dispositivo. Antes de habilitar a Autenticação Multifator no serviço de registro do Active Directory, verifique se o AD FS está configurado para um provedor de Autenticação Multifator. |[Habilitar o registro de dispositivo do Azure Active Directory](active-directory-device-registration-get-started.md)| 
|Os dispositivos descobrem o serviço de registro de dispositivo do Azure Active Directory procurando registros DNS conhecidos. Configure o DNS de sua empresa, de modo que os dispositivos possam descobrir seu serviço de registro de dispositivo do Azure Active Directory. |[Configurar a descoberta de registro de dispositivo do Azure Active Directory](active-directory-device-registration-get-started.md)| 


## <a name="part-2-deploy-and-configure-windows-server-2012-r2-active-directory-federation-services-and-set-up-a-federation-relationship-with-azure-ad"></a>Parte 2: implantar e configurar os Serviços de Federação do Active Directory do Windows Server 2012 R2 e configurar uma relação de federação com o AD do Azure

| Tarefa | Referência |
| --- | --- |
| Implante o Active Directory Domain Services com as extensões de esquema do Windows Server 2012 R2. Não é necessário atualizar nenhum dos seus controladores de domínio para o Windows Server 2012 R2. A atualização do esquema é o único requisito. |[Atualize o esquema do Active Directory Domain Services](#upgrade-your-active-directory-domain-services-schema) |
| Os dispositivos descobrem o serviço de registro de dispositivo do Azure Active Directory procurando registros DNS conhecidos. Configure o DNS de sua empresa, de modo que os dispositivos possam descobrir seu serviço de registro de dispositivo do Azure Active Directory. |[Preparar seu Active Directory para dar suporte a dispositivos](#prepare-your-active-directory-to-support-devices) |

## <a name="part-3-enable-device-writeback-in-azure-ad"></a>Parte 3: habilitar write-back de dispositivos no AD do Azure
| Tarefa | Referência |
| --- | --- |
| Conclua a parte dois de “Habilitando o write-back de dispositivo no Azure AD Connect”. Depois de concluir, retorne a este guia. |[Habilitando write-back de dispositivo no Azure AD Connect](#upgrade-your-active-directory-domain-services-schema) |

## <a name="optional-part-4-enable-multi-factor-authentication"></a>[Opcional] Parte 4: Habilitar a Autenticação Multifator
É altamente recomendável que você configure uma das várias opções para a Autenticação Multifator. Se você desejar exigir a Autenticação Multifator, consulte [Escolher a solução de segurança de Autenticação Multifator ideal para você](../multi-factor-authentication/multi-factor-authentication-get-started.md). Ele inclui uma descrição de cada solução e links para ajudá-lo a configurar a solução de sua escolha.

## <a name="part-5-verification"></a>Parte 5: verificação
A implantação agora foi concluída e você pode experimentar alguns cenários. Use os links a seguir para experimentar o serviço e se familiarizar com seus recursos.

| Tarefa | Referência |
| --- | --- |
| Una alguns dispositivos no local de trabalho usando o serviço de registro de dispositivo do Azure Active Directory. Una dispositivos iOS, Windows e Android. |[Ingressar dispositivos no local de trabalho usando o serviço de registro de dispositivo do Azure Active Directory](#join-devices-to-your-workplace-using-azure-active-directory-device-registration) |
| Exiba e habilite ou desabilite dispositivos registrados usando o portal do administrador. Nesta tarefa, você exibe alguns dispositivos registrados usando o portal do administrador. |[Visão geral do serviço de registro de dispositivo do Azure Active Directory](active-directory-device-registration-get-started.md) |
| Verifique se o write-back dos objetos de dispositivo é feito do Active Directory do Azure para o Windows Server Active Directory. |[Verificar se foi feito o write-back dos dispositivos registrados no Active Directory](#verify-registered-devices-are-written-back-to-active-directory) |
| Agora que os usuários podem registrar seus dispositivos, você pode criar políticas de acesso de aplicativo no AD FS que permitem apenas dispositivos registrados. Nesta tarefa, você cria uma regra de acesso do aplicativo e uma mensagem personalizada de acesso negado. |[Criar uma política de acesso do aplicativo e uma mensagem personalizada de acesso negado](#create-an-application-access-policy-and-custom-access-denied-message) |

## <a name="integrate-azure-active-directory-with-on-premises-active-directory"></a>Integrar o Azure Active Directory ao Active Directory local
Esta etapa ajuda você a integrar o locatário do Azure AD ao Active Directory local, usando o Azure AD Connect. Embora as etapas estejam disponíveis no portal clássico do Azure, anote todas as instruções especiais listadas nesta seção.

1. Entre no portal clássico do Azure usando uma conta que é um administrador global no Azure AD.
2. No painel esquerdo, selecione **Active Directory**.
3. Na guia **Diretório** , selecione o diretório.
4. Selecione a guia **Integração de Diretórios** .
5. Na seção **Implantar e gerenciar**, siga as etapas 1 a 3 para integrar o Azure Active Directory ao diretório local.
   
   1. Adicionar domínios.
   2. Instale e execute o Azure AD Connect usando as instruções descritas em [Instalação personalizada do Azure AD Connect](connect/active-directory-aadconnect-get-started-custom.md).
   3. Verificar e gerenciar a sincronização de diretórios. Instruções de logon único estão disponíveis nessa etapa.
   
   Além disso, configure a federação com o AD FS, conforme descrito em [Instalação personalizada do Azure AD Connect](connect/active-directory-aadconnect-get-started-custom.md).

## <a name="upgrade-your-active-directory-domain-services-schema"></a>Atualizar o esquema de serviços do Domínio do Active Directory
> [!NOTE]
> Depois de atualizar o esquema do Active Directory, o processo não poderá ser revertido. Recomendamos atualizar primeiro em um ambiente de teste.
> 

1. Entre no controlador de domínio com uma conta que tem direitos de administrador corporativo e de administrador de esquema.
2. Copie o diretório **[media]\support\adprep** e os subdiretórios para um dos controladores de domínio do Active Directory (em que **[media]** é o caminho para a mídia de instalação do Windows Server 2012 R2).
4. Em um prompt de comando, acesse o diretório **adprep** e execute **adprep.exe /forestprep**. Siga as instruções na tela para concluir a atualização do esquema.

## <a name="prepare-your-active-directory-to-support-devices"></a>Preparar seu Active Directory para dar suporte a dispositivos
> [!NOTE]
> Isso é uma operação única que você deve executar para preparar a floresta do Active Directory para dar suporte a dispositivos. Para concluir este procedimento, você deve estar conectado com permissões de administrador corporativo e a floresta do Active Directory deve ter o esquema do Windows Server 2012 R2.
> 


### <a name="prepare-your-active-directory-forest"></a>Prepare sua floresta do Active Directory
1. No servidor de federação, abra uma janela de comando do Windows PowerShell e, depois, digite **Initialize-ADDeviceRegistration**. 
2. Quando solicitado que você informe o **ServiceAccountName**, digite o nome da conta de serviço que você selecionou como a conta de serviço do AD FS. Se essa for uma conta gMSA, insira a conta no formato **domain\accountname$**. Para uma conta de domínio, use o formato **domínio\nomedaconta**.

### <a name="enable-device-authentication-in-ad-fs"></a>Habilitar a autenticação de dispositivo no AD FS
1. No servidor de federação, abra o console de gerenciamento do AD FS e acesse **AD FS** > **Políticas de Autenticação**.
2. No painel **Ações**, selecione **Editar Autenticação Primária Global**.
3. Marque a opção **Habilitar autenticação de dispositivo** e, depois, selecione **OK**.
4. Por padrão, o AD FS remove periodicamente dispositivos não utilizados do Active Directory. Desabilite essa tarefa ao usar o serviço de registro de dispositivo do Azure Active Directory para que os dispositivos possam ser gerenciados no Azure.

### <a name="disable-unused-device-cleanup"></a>Desabilitar a limpeza de dispositivos não utilizados
No servidor de federação, abra uma janela de comando do Windows PowerShell e, depois, digite **Set-AdfsDeviceRegistration -MaximumInactiveDays 0**.

### <a name="prepare-azure-ad-connect-for-device-writeback"></a>Preparar o Azure AD Connect para write-back do dispositivo
Conclua a parte 1: Preparar o Azure AD Connect.

## <a name="join-devices-to-your-workplace-by-using-azure-active-directory-device-registration-service"></a>Ingressar dispositivos no local de trabalho usando o serviço de registro de dispositivo do Azure Active Directory

### <a name="join-an-ios-device-by-using-azure-active-directory-device-registration"></a>Ingressar um dispositivo iOS usando o registro de dispositivo do Azure Active Directory
O registro de dispositivo do Azure Active Directory usa o processo de registro de Perfil Over-the-Air para dispositivos iOS. Esse processo é iniciado quando o usuário se conecta à URL de registro de perfil com o Safari. O formato da URL é o seguinte:

    https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/"yourdomainname"

Nesse caso, `yourdomainname` é o nome de domínio configurado com o Azure Active Directory. Por exemplo, se o nome de domínio for contoso.com, a URL será a seguinte:

    https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/contoso.com

Há muitas maneiras diferentes de comunicar essa URL para seus usuários. Por exemplo, um método recomendável é publicar essa URL em uma mensagem personalizada de acesso negado do aplicativo no AD FS. Essas informações são abordadas na próxima seção [Criar uma política de acesso do aplicativo e uma mensagem personalizada de acesso negado](#create-an-application-access-policy-and-custom-access-denied-message).

### <a name="join-a-windows-81-device-by-using-azure-active-directory-device-registration"></a>Ingressar um dispositivo Windows 8.1 usando o registro de dispositivo do Azure Active Directory
1. No dispositivo Windows 8.1, selecione **Configurações do Computador** > **Rede** > **Local de Trabalho**.
2. Insira seu nome de usuário no formato UPN; por exemplo, **dan@contoso.com**.
3. Selecione **Ingressar**.
4. Quando solicitado, entre com suas credenciais. O dispositivo agora está associado.

### <a name="join-a-windows-7-device-by-using-azure-active-directory-device-registration"></a>Ingressar um dispositivo Windows 7 usando o registro de dispositivo do Azure Active Directory
Para registrar dispositivos Windows 7 ingressados no domínio, é necessário implantar o pacote de software de registro de dispositivo. O pacote de software é chamado Workplace Join para Windows 7 e está disponível para download no [site do Microsoft Connect](https://connect.microsoft.com/site1164). 

As instruções sobre como usar o pacote estão disponíveis em [Como configurar o registro automático de dispositivos Windows ingressados no domínio com o Azure Active Directory](active-directory-conditional-access-automatic-device-registration-setup.md).

## <a name="verify-that-registered-devices-are-written-back-to-active-directory"></a>Verificar se foi feito o write-back dos dispositivos registrados no Active Directory
Exiba e verifique se foi feito o write-back dos objetos de dispositivo no Active Directory usando o LDP.exe ou o Editor ADSI. Ambos estão disponíveis com as ferramentas do administrador do Active Directory.

Por padrão, os objetos de dispositivo gravados novamente do Azure Active Directory são colocados no mesmo domínio do farm do AD FS.

    CN=RegisteredDevices,defaultNamingContext

## <a name="create-an-application-access-policy-and-custom-access-denied-message"></a>Criar uma política de acesso do aplicativo e uma mensagem personalizada de acesso negado
Considere o seguinte cenário: você cria uma relação de confiança de aplicativo com a terceira parte confiável no AD FS e configura uma regra de autorização de emissão que permite apenas dispositivos registrados. Agora somente os dispositivos que estão registrados têm permissão para acessar o aplicativo. 

Para facilitar o acesso ao aplicativo para os usuários, configure uma mensagem personalizada de acesso negado que inclui instruções sobre como ingressar seus dispositivos. Agora os usuários têm uma maneira perfeita de registrar seus dispositivos para que possam acessar um aplicativo.

As próximas etapas mostram como implementar esse cenário.

> [!NOTE]
> Esta seção pressupõe que você já tenha configurado uma relação de confiança de terceira parte confiável para o seu aplicativo no AD FS.
> 

1. Abra a ferramenta MMC do AD FS e, em seguida, selecione **AD FS** > **Relações de Confiança** > **Objetos de Confiança de Terceira Parte Confiável**.
2. Localize o aplicativo ao qual essa nova regra de acesso se aplica. Clique com o botão direito do mouse no aplicativo e, depois, selecione **Editar Regras de Declaração**.
3. Selecione a guia **Regras de Autorização de Emissão** e, depois, selecione **Adicionar Regra**.
4. Na lista suspensa do modelo **Regra de declaração**, selecione **Permitir ou Negar Usuários com Base em uma Declaração de Entrada**. Em seguida, selecione **Avançar**.
5. No campo **Nome da regra de declaração**, digite **Permitir o acesso em dispositivos registrados**.
6. Na lista suspensa **Tipo de declaração de entrada**, selecione **É um Usuário Registrado**.
7. No campo **Valor da declaração de entrada**, digite **true**.
8. Selecione o botão de opção **Permitir o acesso a usuários com esta declaração de entrada** .
9. Selecione **Concluir** e, depois, **Aplicar**.
10. Remova as regras que são mais permissivas do que a regra criada. Por exemplo, remova a regra padrão **Permitir Acesso a todos os Usuários**.

Seu aplicativo agora está configurado para permitir acesso somente quando o usuário for proveniente de um dispositivo registrado e adicionado ao local de trabalho. Para obter políticas de acesso mais avançadas, consulte [Gerenciar riscos com a Autenticação Multifator adicional em aplicativos confidenciais](https://technet.microsoft.com/library/dn280949.aspx).

Em seguida, você configura uma mensagem de erro personalizada para seu aplicativo. A mensagem de erro avisará os usuários que eles devem ingressar seus dispositivos no local de trabalho antes que possam acessar o aplicativo. Você pode criar uma mensagem personalizada de acesso negado do aplicativo usando um HTML personalizado e o PowerShell.

No servidor de federação, abra uma janela de comando do PowerShell e, depois, digite o comando a seguir. Substitua partes do comando com itens que são específicos para o seu sistema:

    Set-AdfsRelyingPartyWebContent -Name "relying party trust name" -ErrorPageAuthorizationErrorMessage
Você deve registrar seu dispositivo antes de poder acessar este aplicativo.

**Se você estiver usando um dispositivo iOS, selecione este link para adicionar seu dispositivo**:

    a href='https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/yourdomain.com

Adicione o dispositivo iOS à área de trabalho.

Se estiver usando um dispositivo Windows 8.1, poderá ingressá-lo selecionando **Configurações do Computador**> **Rede** > **Local de Trabalho**.

Nos comandos anteriores, **nome do objeto de confiança de terceira parte confiável** é o nome do objeto de Confiança de Terceira Parte Confiável do aplicativo no AD FS.
Além disso, **yourdomain.com** é o nome de domínio que você configurou com o Azure Active Directory (por exemplo, contoso.com).
Lembre-se de remover as quebras de linha (se houver) do conteúdo HTML passado para o cmdlet **Set-AdfsRelyingPartyWebContent**.

Agora, quando os usuários acessarem seu aplicativo em um dispositivo que não está registrado com o serviço de registro de dispositivo do Azure Active Directory, eles verão uma página com aparência semelhante à captura de tela a seguir.

![Captura de tela de um erro quando os usuários não tiverem registrado seus dispositivos com o Azure AD](./media/active-directory-conditional-access/error-azureDRS-device-not-registered.gif)


