---
title: Autenticação IIS e Servidor do Azure MFA — Azure Active Directory
description: Implantação da Autenticação do IIS e Servidor de Autenticação Multifator do Azure.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/11/2018
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: c522fb950e2489afce44553d233ac3eddf138730
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96742163"
---
# <a name="configure-azure-multi-factor-authentication-server-for-iis-web-apps"></a>Configurar o Servidor de Autenticação Multifator do Azure para aplicativos Web do IIS

Usar a seção Autenticação IIS do Servidor de MFA (Autenticação Multifator) do Azure para habilitar e configurar a autenticação IIS para integração com aplicativos Web do IIS da Microsoft. O Servidor de MFA do Azure instala um plug-in que pode filtrar solicitações feitas ao servidor Web do IIS para adicionar a Autenticação Multifator do Azure. O plug-in do IIS dá suporte para autenticação baseada em formulário e para autenticação integrada do Windows HTTP. As IPs confiáveis também podem ser configuradas para isentar os endereços IP de autenticação de dois fatores.

> [!IMPORTANT]
> A partir de 1º de julho de 2019, a Microsoft não oferece mais o servidor MFA para novas implantações. Novos clientes que desejam exigir a MFA (autenticação multifator) durante eventos de entrada devem usar a autenticação multifator do Azure AD baseada em nuvem.
>
> Para começar a usar a MFA baseada em nuvem, consulte [tutorial: proteger eventos de entrada de usuário com a autenticação multifator do Azure ad](tutorial-enable-azure-mfa.md).
>
> Os clientes existentes que ativaram o servidor MFA antes de 1º de julho de 2019 podem baixar a versão mais recente, atualizações futuras e gerar credenciais de ativação como de costume.
>
> Quando você usa a Autenticação Multifator do Azure baseada na nuvem, não há nenhuma alternativa para o plug-in do IIS fornecida pelo Servidor de Autenticação Multifator (MFA) do Azure. Em vez disso, use o Proxy de Aplicativo Web (WAP) com os Serviços de Federação do Active Directory (AD FS) (AD FS) ou o Proxy de Aplicativo do Azure Active Directory.

![Autenticação do IIS no Servidor MFA](./media/howto-mfaserver-iis/iis.png)

## <a name="using-form-based-iis-authentication-with-azure-multi-factor-authentication-server"></a>Usando a autenticação ISS baseada em formulário com o Servidor de Autenticação Multifator do Azure

Para proteger um aplicativo Web do IIS que usa autenticação baseada em formulário, instale o Servidor de Autenticação Multifator do Azure no servidor Web do IIS e configure o Servidor de acordo com o procedimento a seguir:

1. No Servidor de Autenticação Multifator do Azure, clique no ícone de Autenticação do IIS no menu esquerdo.
2. Clique na guia **Baseado em Formulário**.
3. Clique em **Adicionar**.
4. Para detectar o nome do usuário, a senha e as variáveis de domínio automaticamente, insira a URL de Logon (como `https://localhost/contoso/auth/login.aspx`) na caixa de diálogo Configurar Automaticamente Site Baseado em Formulário e clique em **OK**.
5. Marque a caixa **Exigir correspondência do usuário da Autenticação Multifator** se todos os usuários foram ou vierem a ser importados para o Servidor e estiverem sujeitos à autenticação multifator. Se um número significativo de usuários ainda não tiver sido importado no Servidor e/ou for isentado da autenticação multifator, deixe a caixa desmarcada.
6. Se as variáveis de página não puderem ser detectadas automaticamente, clique em **Especificar Manualmente** na caixa de diálogo Configurar Automaticamente Site Baseado em Formulário.
7. Na caixa de diálogo Adicionar Site Baseado em Formulário, digite a URL para a página de logon no campo URL de envio e digite um nome de aplicativo (opcional). O nome do aplicativo aparece nos relatórios da Autenticação Multifator do Azure e pode ser exibido nas mensagens de autenticação por SMS ou Aplicativo Móvel.
8. Selecione o formato correto da solicitação. Isso é definido como **POST ou GET** para a maioria dos aplicativos Web.
9. Insira a variável de Nome de usuário, a variável de Senha e a variável de Domínio (se aparecer na página de logon). Para localizar os nomes das caixas de entrada navegue até a página de logon em um navegador da Web, clique com o botão direito do mouse na página e selecione **Exibir Fonte**.
10. Marque a caixa **Exigir correspondência do usuário da Autenticação Multifator do Azure** se todos os usuários foram ou vierem a ser importados para o Servidor e estiverem sujeitos à autenticação multifator. Se um número significativo de usuários ainda não tiver sido importado no Servidor e/ou for isentado da autenticação multifator, deixe a caixa desmarcada.
11. Clique em **Avançado** para examinar as configurações avançadas, incluindo:

    - Selecionar um arquivo de página de negação personalizado
    - Armazenar em cache as autenticações bem-sucedidas no site, durante um período de tempo, usando cookies
    - Selecione se deseja autenticar as credenciais primárias em um Domínio do Windows, diretório LDAP. ou servidor RADIUS.

12. Clique em **OK** para retornar à caixa de diálogo Adicionar Site Baseado em Formulário.
13. Clique em **OK**.
14. Depois que as variáveis de URL e página forem detectadas ou inseridas, os dados do site serão exibidos no painel Baseado em Formulário.

## <a name="using-integrated-windows-authentication-with-azure-multi-factor-authentication-server"></a>Usando a autenticação integrada do Windows com o Servidor de Autenticação Multifator do Azure

Para proteger um aplicativo Web do IIS que usa a autenticação HTTP integrada do Windows, instale o Servidor de MFA do Azure no servidor Web do IIS e, em seguida, configure o Servidor com as seguintes etapas:

1. No Servidor de Autenticação Multifator do Azure, clique no ícone de Autenticação do IIS no menu esquerdo.
2. Clique na guia **HTTP**.
3. Clique em **Adicionar**.
4. Na caixa de diálogo Adicionar URL Base, insira a URL para o site em que a autenticação HTTP é realizada (como `http://localhost/owa`) e forneça um nome de aplicativo (opcional). O nome do aplicativo aparece nos relatórios da Autenticação Multifator do Azure e pode ser exibido nas mensagens de autenticação por SMS ou Aplicativo Móvel.
5. Ajuste o tempo limite de ociosidade e o tempo máximo de sessão se o padrão não for suficiente.
6. Marque a caixa **Exigir correspondência do usuário da Autenticação Multifator** se todos os usuários foram ou vierem a ser importados para o Servidor e estiverem sujeitos à autenticação multifator. Se um número significativo de usuários ainda não tiver sido importado no Servidor e/ou for isentado da autenticação multifator, deixe a caixa desmarcada.
7. Marque a caixa de **Cache de cookie**, se desejado.
8. Clique em **OK**.

## <a name="enable-iis-plug-ins-for-azure-multi-factor-authentication-server"></a>Habilitar Plug-ins IIS para Servidor de Autenticação Multifator do Azure

Depois de configurar as URLs e configurações da autenticação de HTTP ou da autenticação Baseada em Formulário, selecione os locais nos quais os plug-ins IIS da Autenticação Multifator do Azure devem ser carregados e habilitados no IIS. Use este procedimento:

1. Se estiver executado no IIS 6, clique na guia **ISAPI**. Selecione o site em que o aplicativo Web está sendo executado (por exemplo, Site Padrão) para habilitar o plug-in de filtro ISAPI da Autenticação Multifator do Azure para aquele site.
2. Se estiver executando no IIS 7 ou superior, clique na guia **Módulo Nativo**. Selecione o servidor, os sites ou os aplicativos para habilitar o plug-in do IIS nos níveis desejados.
3. Clique na caixa **Habilitar autenticação do IIS** na parte superior da tela. A Autenticação Multifator do Azure agora está protegendo o aplicativo IIS selecionado. Verifique se os usuários foram importados para o servidor.

## <a name="trusted-ips"></a>IPs confiáveis

Os IPs Confiáveis permitem que os usuários ignorem a Autenticação Multifator do Azure para solicitações de site que se originam de sub-redes ou endereços IP específicos. Por exemplo, convém isentar os usuários da Autenticação Multifator do Azure durante o logon feito no escritório. Para isso, você especifica a sub-rede do escritório como uma entrada de IPs Confiáveis. Para configurar IPs confiáveis, use o seguinte procedimento:

1. Na seção Autenticação IIS, clique na guia **IPs Confiáveis**.
2. Clique em **Adicionar**.
3. Quando a caixa de diálogo Adicionar IPs Confiáveis aparecer, selecione o botão de opção **IP Único**, **Intervalo de IP** ou **Sub-rede**.
4. Insira o endereço IP, o intervalo dos endereços IP ou a sub-rede que devem ser permitidos. Se for inserir uma sub-rede, selecione a Máscara de Rede apropriada e clique em **OK**.
