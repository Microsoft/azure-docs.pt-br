---
title: "Habilitar o Microsoft Windows Hello for Business em sua organização |Microsoft Docs"
description: "Instruções de implantação para habilitar o Microsoft Passport na sua organização."
services: active-directory
documentationcenter: 
keywords: "configurar o Microsoft Passport, implantação do Microsoft Windows Hello for Business"
author: MarkusVi
manager: femila
tags: azure-classic-portal
ms.assetid: 7dbbe3c6-1cd7-429c-a9b2-115fcbc02416
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: markvi
ms.openlocfilehash: 58943e1e29755c983e55c675dd4fe7b75ac47b34
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="enable-microsoft-windows-hello-for-business-in-your-organization"></a>Habilitar o Microsoft Windows Hello for Business em sua organização
Depois de [conectar os dispositivos integrados ao domínio do Windows 10 ao Azure Active Directory](active-directory-azureadjoin-devices-group-policy.md), faça o seguinte para habilitar o Microsoft Windows Hello para Empresas em sua organização:

1. Implantar o System Center Configuration Manager  
2. Definir as configurações de política
3. Configurar o perfil de certificado  

## <a name="deploy-system-center-configuration-manager"></a>Implantar o System Center Configuration Manager
Para implantar certificados de usuário baseados em chaves do Windows Hello for Business, você precisa do seguinte:

* **Ramificação atual do System Center Configuration Manager** - Você precisa instalar a versão 1606 ou superior. Para saber mais, confira a [Documentação do System Center Configuration Manager](https://technet.microsoft.com/library/mt346023.aspx) e [Blog da equipe do System Center Configuration Manager](http://blogs.technet.com/b/configmgrteam/archive/2015/09/23/now-available-update-for-system-center-config-manager-tp3.aspx).
* **PKI (infraestrutura de chave pública)**: para habilitar o Microsoft Windows Hello para Empresas usando certificados de usuário, você deve ter uma PKI em vigor. Caso você não tenha uma ou não queira usá-la para certificados de usuário, você pode implantar um novo controlador de domínio que tenha a build 10551 (ou superior) do Windows Server 2016 instalada. Siga as etapas para [instalar um controlador de domínio de réplica em um domínio existente](https://technet.microsoft.com/library/jj574134.aspx) ou para [instalar uma nova floresta do Active Directory, caso você esteja criando um novo ambiente](https://technet.microsoft.com/library/jj574166). (Os ISOs estão disponíveis para download em [Signiant Media Exchange](https://datatransfer.microsoft.com/signiant_media_exchange/spring/main?sdkAccessible=true).)

## <a name="configure-policy-settings"></a>Definir as configurações de política
Para definir as configurações de política no Microsoft Windows Hello para Empresas, você tem duas opções:

* Política de grupo no Active Directory 
* System Center Configuration Manager 

Usar a Política de grupo no Active Directory é o método recomendado para definir as configurações de política no Microsoft Windows Hello para Empresas. 

Usar o System Center Configuration Manager é o método preferencial quando você também for usá-lo para implantar certificados. Este cenário:

* Garante a compatibilidade com os novos cenários de implantação
* Exige do cliente a versão 1607 ou superior do Windows 10.

### <a name="configure-microsoft-windows-hello-for-business-via-group-policy-in-active-directory"></a>Configurar o Microsoft Windows Hello para Empresas usando a Política de Grupo no Active Directory
**Etapas**:

1. Abra o Gerenciador do Servidor e navegue até **Ferramentas** > **Gerenciamento de Política de Grupo**.
2. No Gerenciamento de Política de Grupo, navegue até o nó de domínio que corresponde ao domínio no qual você deseja habilitar o Ingresso no AD do Azure.
3. Clique com o botão direito do mouse em **Objetos de Política de Grupo** e selecione **Novo**. Dê um nome ao seu Objeto de Política de Grupo, por exemplo, Habilitar o Windows Hello for Business. Clique em **OK**.
4. Clique com o botão direito do mouse em seu novo Objeto de Política de Grupo e selecione **Editar**.
5. Navegue até **Configuração do Computador** > **Políticas** > **Modelos Administrativos** > **Componentes do Windows** > **Windows Hello para Empresas**.
6. Clique com o botão direito em **Habilitar o Windows Hello para Empresas** e, em seguida, selecione **Editar**.
7. Selecione o botão de opção **Habilitado** e clique em **Aplicar**. Clique em **OK**.
8. Agora você pode vincular o Objeto de Política de Grupo para um local de sua escolha. Para habilitar essa política para todos os dispositivos do Windows 10 associados ao domínio em sua organização, vincule a Política de Grupo ao domínio. Por exemplo:
   * Uma UO (unidade organizacional) específica no Active Directory onde os computadores ingressados no domínio do Windows 10 estejam localizados.
   * Um grupo de segurança específico com computadores ingressados no domínio do Windows 10 que serão registrados automaticamente no AD do Azure.

### <a name="configure-windows-hello-for-business-using-system-center-configuration-manager"></a>Configurar o Windows Hello for Business usando o System Center Configuration Manager
**Etapas**:

1. Abrir o **System Center Configuration Manager** e, em seguida, navegar até **Ativos e Conformidade > Configurações de Conformidade > Acesso a Recursos da Empresa > Perfis do Windows Hello para Empresas**.
   
    ![Configurar o Windows Hello for Business](./media/active-directory-azureadjoin-passport-deployment/01.png)
2. Na barra de ferramentas na parte superior, clique em **Criar perfil Windows Hello for Business**.
   
    ![Configurar o Windows Hello for Business](./media/active-directory-azureadjoin-passport-deployment/02.png)
3. Na caixa de diálogo **Geral** , execute as seguintes etapas:
   
    ![Configurar o Windows Hello for Business](./media/active-directory-azureadjoin-passport-deployment/03.png)
   
    a. Na caixa de diálogo **Nome**, digite um nome para seu perfil, por exemplo, **Meu Perfil WHfB**.
   
    b. Clique em **Avançar**.
4. Na caixa de diálogo **Plataformas com Suporte**, selecione as plataformas que serão provisionadas com esse perfil do Windows Hello para Empresas e, em seguida, clique em **Próximo**.
   
    ![Configurar o Windows Hello for Business](./media/active-directory-azureadjoin-passport-deployment/04.png)
5. Na caixa de diálogo **Configurações** , execute as seguintes etapas:
   
    ![Configurar o Windows Hello for Business](./media/active-directory-azureadjoin-passport-deployment/05.png)
   
    a. Em **Configurar o Windows Hello para Empresas**, selecionar **Habilitado**.
   
    b. Em **Usar um TPM (Trusted Platform Module)**, selecione **Obrigatório**. 
   
    c. Em **Método de autenticação**, selecione **Baseado em certificado**.
   
    d. Clique em **Avançar**.
6. Na caixa de diálogo de **Resumo**, clique em **Avançar**.
7. Na caixa de diálogo **Conclusão**, clique em **Fechar**.
8. Na barra de ferramentas na parte superior, clique em **Implantar**.
   
    ![Configurar o Windows Hello for Business](./media/active-directory-azureadjoin-passport-deployment/06.png)

## <a name="configure-the-certificate-profile"></a>Configurar o perfil de certificado
Se você estiver usando a autenticação baseada em certificado para autenticação local, você precisará configurar e implantar um perfil de certificado. Essa tarefa exige que você configure um servidor NDES e a função de site do Ponto de Registro de Certificado no System Center Configuration Manager. Para obter mais detalhes, consulte os [Pré-requisitos para perfis de Certificado no Configuration Manager](https://technet.microsoft.com/library/dn261205.aspx).

1. Abra o **System Center Configuration Manager** e, em seguida, navegue até **Ativos e Conformidade > Configurações de Conformidade > Acesso a Recursos da Empresa > Perfis de Certificado**.
2. Selecione um modelo que tenha EKU (uso estendido da chave) para entrada com cartão inteligente.

Na página **Registro do SCEP** do perfil de certificado, você precisa escolher **Instalar Passport for Work se houver falha** como o **Provedor de Armazenamento de Chave**.

## <a name="next-steps"></a>Próximas etapas
* [Windows 10 para a empresa: maneiras de usar dispositivos para o trabalho](active-directory-azureadjoin-windows10-devices-overview.md)
* [Estendendo os recursos de nuvem para dispositivos Windows 10 por meio da Junção do Active Directory do Azure](active-directory-azureadjoin-user-upgrade.md)
* [Autenticando identidades sem senhas com o Microsoft Passport](active-directory-azureadjoin-passport.md)
* [Saiba mais sobre cenários de uso da Junção do Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Conectar dispositivos ingressados no domínio ao AD do Azure para experiências com o Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configurar a Junção do Azure AD](active-directory-azureadjoin-setup.md)

