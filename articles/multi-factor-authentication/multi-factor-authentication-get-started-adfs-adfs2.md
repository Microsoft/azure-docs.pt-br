---
title: Usar o Servidor Azure MFA com o AD FS 2.0 | Microsoft Docs
description: "Esta é a página do Azure Multi-Factor Authentication que descreve como começar a usar o Azure MFA e o AD FS 2.0."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 96168849-241a-4499-a224-d829913caa7e
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/14/2017
ms.author: kgremban
ms.reviewer: yossib
ms.custom: H1Hack27Feb2017, it-pro
ms.openlocfilehash: a05219398aefa158321ae63934743dd43841e33f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="configure-azure-multi-factor-authentication-server-to-work-with-ad-fs-20"></a>Configurar o Servidor de Autenticação Multifator do Azure para trabalhar com o AD FS 2.0
Este artigo é para organizações que estão federadas com o Azure Active Directory e desejam proteger recursos locais ou na nuvem. Proteja seus recursos usando o servidor de autenticação multifator do Azure e configure-o para trabalhar com o AD FS para que a verificação em duas etapas seja disparada para pontos de extremidade de alto valor.

Esta documentação aborda como usar o Servidor Azure Multi-Factor Authentication com o AD FS 2.0. Para saber mais sobre o AD FS, veja [Proteger recursos de nuvem e locais usando o Servidor de Autenticação Multifator do Azure com o Windows Server 2012 R2 AD FS](multi-factor-authentication-get-started-adfs-w2k12.md).

## <a name="secure-ad-fs-20-with-a-proxy"></a>Proteger o AD FS 2.0 com um proxy
Para proteger o AD FS 2.0 com um proxy, instale o Servidor da Autenticação Multifator do Azure no servidor proxy do AD FS.

### <a name="configure-iis-authentication"></a>Configurar a autenticação IIS
1. No Servidor de Autenticação Multifator do Azure, clique no ícone de **Autenticação do IIS** no menu esquerdo.
2. Clique na guia **Baseado em Formulário**.
3. Clique em **Adicionar**.

   <center>![Configuração](./media/multi-factor-authentication-get-started-adfs-adfs2/setup1.png)</center>

4. Para detectar o nome do usuário, a senha e as variáveis de domínio automaticamente, insira a URL de Logon (como https://sso.contoso.com/adfs/ls) na caixa de diálogo Configurar Automaticamente Site Baseado em Formulário e clique em **OK**.
5. Marque a caixa correspondente a **Exigir correspondência de usuário de Autenticação Multifator do Microsoft Azure**  se todos os usuários tiverem sido ou forem importados para o servidor e estiverem sujeitos à verificação em duas etapas. Se um número significativo de usuários ainda não tiver sido importado no Servidor e/ou for isentado da verificação em duas etapas, deixe a caixa desmarcada.
6. Se as variáveis de página não puderem ser detectadas automaticamente, clique no botão **Especificar Manualmente...** na caixa de diálogo Configurar Automaticamente Site Baseado em Formulário.
7. Na caixa de diálogo Adicionar Site Baseado em Formulário, insira a URL à página de logon do AD FS no campo URL de Envio (como https://sso.contoso.com/adfs/ls) e insira um nome para o aplicativo (opcional). O nome do aplicativo aparece nos relatórios do Azure Multi-Factor Authentication e pode ser exibido nas mensagens de autenticação por SMS ou Aplicativo Móvel.
8. Defina o formato da solicitação como **POST ou GET**.
9. Insira a variável do Nome do usuário (ctl00$ContentPlaceHolder1$UsernameTextBox) e a variável da Senha (ctl00$ContentPlaceHolder1$PasswordTextBox). Se a página de logon baseada em formulário exibir uma caixa de texto de domínio, insira a variável do domínio também. Para localizar os nomes das caixas de entrada na página de logon, vá até a página de logon em um navegador da Web, clique com o botão direito do mouse na página e selecione **Exibir Fonte**.
10. Marque a caixa correspondente a **Exigir correspondência de usuário de Autenticação Multifator do Microsoft Azure**  se todos os usuários tiverem sido ou forem importados para o servidor e estiverem sujeitos à verificação em duas etapas. Se um número significativo de usuários ainda não tiver sido importado no Servidor e/ou for isentado da verificação em duas etapas, deixe a caixa desmarcada.
    <center>![Configuração](./media/multi-factor-authentication-get-started-adfs-adfs2/manual.png)</center>
11. Clique em **Avançado...** para examinar as configurações avançadas. As configurações que você pode definir incluem:

    - Selecionar um arquivo de página de negação personalizado
    - Armazenar em cache as autenticações bem-sucedidas no site usando cookies
    - Selecionar como deseja autenticar as credenciais principais

12. Como não há probabilidade de o servidor proxy do AD FS se unir ao domínio, você pode usar LDAP para se conectar ao seu controlador de domínio para importação de usuário e pré-autenticação. Na caixa de diálogo Site Avançado Baseado em Formulário, clique na guia **Autenticação Primária** e selecione **Associação LDAP** para o tipo de autenticação Pré-autenticação.
13. Ao concluir, clique em **OK** para retornar à caixa de diálogo Adicionar Site Baseado em Formulário.
14. Clique em **OK** para fechar a caixa de diálogo.
15. Depois que as variáveis de URL e página forem detectadas ou inseridas, os dados do site serão exibidos no painel Baseado em Formulário.
16. Clique na guia **Módulo Nativo** e selecione o servidor, o site em que proxy do AD FS está em execução (como “Site Padrão”) ou o aplicativo de proxy do AD FS (como “ls” em “adfs”) para habilitar o plug-in do IIS no nível desejado.
17. Clique na caixa **Habilitar autenticação do IIS** na parte superior da tela.

Agora, a autenticação do IIS está habilitada.

### <a name="configure-directory-integration"></a>Configurar a integração de diretório
Você habilitou a autenticação do IIS, mas para executar a pré-autenticação no AD (Active Directory) via LDAP, você deve configurar a conexão LDAP com o controlador de domínio.

1. Clique no ícone de **Integração de Diretório**.
2. Na guia Configurações, selecione o botão de opção **Usar configuração de LDAP específica**.

   <center>![Configuração](./media/multi-factor-authentication-get-started-adfs-adfs2/ldap1.png)</center>

3. Clique em **Editar**.
4. Na caixa de diálogo Editar Configuração de LDAP, popule os campos com as informações exigidas para se conectar ao controlador de domínio do AD. As descrições dos campos estão incluídas no arquivo de ajuda do servidor da Autenticação Multifator do Azure.
5. Teste a conexão LDAP clicando no botão **Testar**.

   <center>![Configuração](./media/multi-factor-authentication-get-started-adfs-adfs2/ldap2.png)</center>

6. Se o teste de conexão LDAP tiver sido bem-sucedido, clique no botão **OK**.

### <a name="configure-company-settings"></a>Definir configurações da empresa
1. Em seguida, clique no ícone de **Configurações da Empresa** e selecione a guia Resolução de **Nome de Usuário**.
2. Selecione o botão de opção **Usar atributo de identificador exclusivo LDAP para correspondência de nomes de usuário**.
3. Se os usuários inserirem seu nome de usuário no formato "domínio\nomedousuário", o servidor precisará ser capaz de retirar o domínio de nome de usuário quando criar a consulta LDAP. Isso pode ser feito por meio de uma configuração do registro.
4. Abra o editor de registro e vá para HKEY_LOCAL_MACHINE/SOFTWARE/Wow6432Node/Positive Networks/PhoneFactor em um servidor de 64 bits. Em um servidor de 32 bits, retire “Wow6432Node” do caminho. Crie uma chave de registro DWORD chamada “UsernameCxz_stripPrefixDomain” e defina o valor para 1. Agora a Autenticação Multifator do Azure está protegendo o proxy do AD FS.

Verifique se os usuários foram importados do Active Directory no Servidor. Confira a [seção IPs confiáveis](#trusted-ips) se você quiser incluir na lista branca os endereços IP internos para que a verificação em duas etapas não seja necessária ao entrar no site por meio desses locais.

<center>![Configuração](./media/multi-factor-authentication-get-started-adfs-adfs2/reg.png)</center>

## <a name="ad-fs-20-direct-without-a-proxy"></a>AD FS 2.0 Direct sem um proxy
Você pode proteger o AD FS quando o proxy do AD FS não é usado. Instale o servidor de autenticação multifator do Azure no servidor do AD FS e configure o servidor de acordo com as etapas a seguir:

1. No Servidor de Autenticação Multifator do Microsoft Azure, clique no ícone de **Autenticação IIS** no menu esquerdo.
2. Clique na guia **HTTP**.
3. Clique em **Adicionar**.
4. Na caixa de diálogo Adicionar URL Base, insira a URL do site do AD FS onde a autenticação HTTP é executada (como https://sso.domain.com/adfs/ls/auth/integrated) no campo URL Base. Em seguida, insira um nome de aplicativo (opcional). O nome do aplicativo aparece nos relatórios do Azure Multi-Factor Authentication e pode ser exibido nas mensagens de autenticação por SMS ou Aplicativo Móvel.
5. Se desejar, ajuste o Tempo limite de ociosidade e o Tempo máximo da sessão.
6. Marque a caixa correspondente a **Exigir correspondência de usuário de Autenticação Multifator do Microsoft Azure** se todos os usuários tiverem sido ou forem importados para o servidor e estiverem sujeitos à verificação em duas etapas. Se um número significativo de usuários ainda não tiver sido importado no Servidor e/ou for isentado da verificação em duas etapas, deixe a caixa desmarcada.
7. Marque a caixa de cache de cookie se desejado.

   <center>![Configuração](./media/multi-factor-authentication-get-started-adfs-adfs2/noproxy.png)</center>

8. Clique em **OK**.
9. Clique na guia **Módulo Nativo** e selecione o servidor, o site (como "Site Padrão") ou o aplicativo do AD FS (como "ls" em "adfs") para habilitar o plug-in do IIS no nível desejado.
10. Clique na caixa **Habilitar autenticação do IIS** na parte superior da tela.

Agora a Autenticação Multifator do Azure está protegendo o ADFS.

Verifique se os usuários foram importados do Active Directory no Servidor. Confira a seção IPs confiáveis se você que incluir endereços IP internos na lista branca para que a verificação em duas etapas não seja necessária ao entrar no site por meio desses locais.

## <a name="trusted-ips"></a>IPs confiáveis
Os IPs Confiáveis permitem que os usuários ignorem a Autenticação Multifator do Azure para solicitações de site que se originam de sub-redes ou endereços IP específicos. Por exemplo, convém isentar os usuários da verificação em duas etapas quando eles entrarem por meio do Office. Para isso, você especifica a sub-rede do escritório como uma entrada de IPs Confiáveis.

### <a name="to-configure-trusted-ips"></a>Para configurar IPs confiáveis
1. Na seção Autenticação IIS, clique na guia **IPs Confiáveis**.
2. Clique em **Adicionar…** .
3. Quando a caixa de diálogo Adicionar IPs Confiáveis aparecer, selecione o botão de opção **IP Único**, **Intervalo de IP** ou **Sub-rede**.
4. Insira o endereço IP, o intervalo dos endereços IP ou a sub-rede que devem ser colocados na lista branca. Se inserir uma sub-rede, selecione a Máscara de Rede apropriada e clique no botão **OK**. O IP confiável foi adicionado.

<center>![Configuração](./media/multi-factor-authentication-get-started-adfs-adfs2/trusted.png)</center>
