---
title: "Publicar a Área de Trabalho Remota com o Proxy de Aplicativo do Azure AD | Microsoft Docs"
description: "Cobre as noções básicas sobre os conectores do Proxy de Aplicativo do Azure AD."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2017
ms.author: kgremban
ms.custom: it-pro
ms.reviewer: harshja
ms.openlocfilehash: fa8f63c8da5019ed42ea8ec067d3d3d174976dd8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="publish-remote-desktop-with-azure-ad-application-proxy"></a>Publicar a Área de Trabalho Remota com o Proxy de Aplicativo do Azure AD

O Serviço de Área de Trabalho Remota e o Proxy de Aplicativo do Azure AD trabalham juntos para aumentar a produtividade dos trabalhos que estão fora da rede corporativa. 

O público-alvo deste artigo é:
- Os clientes atuais do Proxy de Aplicativo que desejam oferecer mais aplicativos para seus usuários finais publicando aplicativos locais através dos Serviços de Área de Trabalho Remota.
- Clientes atuais dos Serviços de Área de Trabalho Remota cujo desejo é reduzir a superfície de ataque da respectiva implantação usando o Proxy de Aplicativo do Azure AD. Este cenário fornece um conjunto limitado de verificação em duas etapas e controles de acesso condicional para o RDS.

## <a name="how-application-proxy-fits-in-the-standard-rds-deployment"></a>Como o Proxy de aplicativo se ajusta na implantação do RDS padrão

Uma implantação do RDS padrão inclui vários serviços de função da Área de Trabalho Remota em execução no Windows Server. Observando a [arquitetura dos Serviços de Área de Trabalho Remota](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/desktop-hosting-logical-architecture), há várias opções de implantação. Ao contrário de outras opções de implantação de RDS, a [implantação de RDS com o Proxy de Aplicativo do Azure AD](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/desktop-hosting-logical-architecture) (mostrada no diagrama a seguir) tem uma conexão permanente de saída do servidor executando o serviço do conector. Outras implantações deixam conexões de entrada abertas por meio de um balanceador de carga.

![O proxy de aplicativo fica entre a VM do RDS e a Internet pública](./media/application-proxy-publish-remote-desktop/rds-with-app-proxy.png)

Em uma implantação do RDS, a função Web da Área de Trabalho Remota e a função de Gateway de Área de Trabalho Remota são executados em computadores voltados para a Internet. Esses pontos de extremidade são expostos pelos seguintes motivos:
- A Web de Área de Trabalho Remota fornece ao usuário um ponto de extremidade público para entrar e exibir os diversos aplicativos locais e áreas de trabalho que eles podem acessar. Ao selecionar um recurso, uma conexão de RDP é criada usando o aplicativo nativo no sistema operacional.
- O Gateway de Área de Trabalho Remota entra em cena quando um usuário inicia a conexão de RDP. O Gateway de Área de Trabalho Remota manipula o tráfego de RDP criptografado chegando pela Internet e o converte para o servidor local ao qual o usuário está se conectando. Nesse cenário, o tráfego que o Gateway de Área de Trabalho Remota está recebendo é proveniente do Proxy de Aplicativo do Azure AD.

>[!TIP]
>Se você não implantou o RDS anteriormente ou deseja obter mais informações antes de começar, saiba como [implantar perfeitamente o RDS com o Azure Resource Manager e o Azure Marketplace](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-in-azure).

## <a name="requirements"></a>Requisitos

- Tanto o ponto de extremidade da Web da Área de Trabalho Remota quanto o ponto de extremidade do Gateway de Área de Trabalho Remota devem estar localizados no mesmo computador e com uma raiz comum. A Web da Área de Trabalho Remota e o Gateway de Área de Trabalho Remota serão publicados como um único aplicativo com o Proxy de Aplicativo para que você possa ter uma experiência de logon único entre os dois aplicativos.

- Você já deverá ter [implantado o RDS](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-in-azure) e [habilitado o Proxy de Aplicativo](active-directory-application-proxy-enable.md).

- Esse cenário pressupõe que seus usuários finais passem pelo Internet Explorer em áreas de trabalho do Windows 7 ou do Windows 10 que se conectem por meio da página da Web de RD. Se você precisar de suporte a outros sistemas operacionais, consulte [Suporte a outras configurações de cliente](#support-for-other-client-configurations).

  >[!NOTE]
  >No momento, não há suporte para atualização do Criador do Windows 10.

- No Internet Explorer, habilite o complemento ActiveX do RDS.

## <a name="deploy-the-joint-rds-and-application-proxy-scenario"></a>Implantar o cenário conjunto de RDS e Proxy de Aplicativo

Depois de configurar o RDS e o Proxy de Aplicativo do Azure AD em seu ambiente, siga as etapas para combinar as duas soluções. Essas etapas explicam passo a passo como publicar os dois pontos de extremidade de RDS voltados para a Web (Web da Área de Trabalho Remota e Gateway de Área de Trabalho Remota) como aplicativos, direcionando depois o tráfego em seu RDS para passar pelo Proxy de Aplicativo.

### <a name="publish-the-rd-host-endpoint"></a>Publicar o ponto de extremidade do host de Área de Trabalho Remota

1. [Publicar um novo aplicativo de Proxy de Aplicativo](application-proxy-publish-azure-portal.md) com os seguintes valores:
   - URL interna: https://\<rdhost\>.com/, em que \<rdhost\> é a raiz comum que a Web da Área de Trabalho Remota e o Gateway de Área de Trabalho Remota compartilham.
   - URL externa: esse campo é preenchido automaticamente com base no nome do aplicativo, mas você pode modificá-lo. Os usuários serão levados a essa URL quando acessarem o RDS.
   - Método de pré-autenticação: Azure Active Directory
   - Converter cabeçalhos de URL: não
2. Atribua usuários ao aplicativo de Área de Trabalho Remota publicado. Certifique-se também de que todos eles tenham acesso ao RDS.
3. Deixe o método de logon único para o aplicativo como **Logon único do Azure AD desabilitado**. É solicitado aos usuários que autentiquem uma vez no Azure AD e uma vez para a Web da Área de Trabalho Remota, eles têm logon único para o Gateway de Área de Trabalho Remota.
4. Vá para **Azure Active Directory** > **Registros de Aplicativo** > *Seu aplicativo* > **Configurações**.
5. Selecione **Propriedades** e atualize o campo **URL da Home Page** para apontar para o ponto de extremidade da Web da Área de Trabalho Remota (como https://\<rdhost\>.com/RDWeb).

### <a name="direct-rds-traffic-to-application-proxy"></a>Direcionar o tráfego do RDS para o Proxy de Aplicativo

Conecte-se à implantação do RDS como administrador e altere o nome do servidor de Gateway de Área de Trabalho Remota para a implantação. Essa configuração garante que as conexões passem pelo serviço de Proxy de Aplicativo do Azure AD.

1. Conecte-se ao servidor RDS executando a função de Agente de Conexão de Área de Trabalho Remota.
2. Inicie o **Gerenciador do Servidor**.
3. Selecione **Serviços de Área de Trabalho Remota** no painel à esquerda.
4. Selecione **Visão geral**.
5. Na seção Visão geral da implantação, selecione o menu suspenso e escolha **Editar propriedades de implantação**.
6. Na guia Gateway de Área de Trabalho Remota, altere o campo **Nome do servidor** para a URL externa que você definiu para o ponto de extremidade do host de Área de Trabalho Remota no Proxy de Aplicativo.
7. Altere o campo **Método de logon** para **Autenticação de Senha**.

  ![Tela Propriedades de Implantação no RDS](./media/application-proxy-publish-remote-desktop/rds-deployment-properties.png)

8. Execute este comando para cada coleção. Substitua *\<yourcollectionname\>* e *\<proxyfrontendurl\>* por suas próprias informações. Este comando habilita o logon único entre a Web da Área de Trabalho Remota e Gateway de Área de Trabalho Remota e otimiza o desempenho:

   ```
   Set-RDSessionCollectionConfiguration -CollectionName "<yourcollectionname>" -CustomRdpProperty "pre-authentication server address:s:<proxyfrontendurl>`nrequire pre-authentication:i:1"
   ```

   **Por exemplo:**
   ```
   Set-RDSessionCollectionConfiguration -CollectionName "QuickSessionCollection" -CustomRdpProperty "pre-authentication server address:s:https://gateway.contoso.msappproxy.net/`nrequire pre-authentication:i:1"
   ```

9. Para verificar se a modificação das propriedades personalizadas do RDP, bem como para exibir o conteúdo do arquivo do RDP que será baixado do RDWeb para essa coleção, execute o seguinte comando:
    ```
    (get-wmiobject -Namespace root\cimv2\terminalservices -Class Win32_RDCentralPublishedRemoteDesktop).RDPFileContents
    ```

Agora que você configurou a Área de Trabalho Remota, o Proxy de Aplicativo do Azure AD assumiu como o componente voltado para a Internet do RDS. Você pode remover os outros pontos de extremidade voltados para a Internet pública em seus computadores da Web da Área de Trabalho Remota e do Gateway de Área de Trabalho Remota.

## <a name="test-the-scenario"></a>Testar o cenário

Teste o cenário com o Internet Explorer no computador com Windows 7 ou 10.

1. Vá para a URL externa que você configurou ou localize seu aplicativo no [painel MyApps](https://myapps.microsoft.com).
2. É solicitado que você se autentique no Azure Active Directory. Use uma conta que você atribuiu ao aplicativo.
3. Será solicitado que você autentique a Web da Área de Trabalho Remota.
4. Quando a autenticação de RDS for bem-sucedida, você poderá selecionar o computador desktop ou aplicativo que deseja e começar a trabalhar.

## <a name="support-for-other-client-configurations"></a>Suporte para outras configurações de cliente

A configuração descrita neste artigo é para usuários no Windows 7 ou 10, com o Internet Explorer mais o complemento ActiveX do RDS. Se for necessário, no entanto, você poderá dar suporte a outros sistemas operacionais ou navegadores. A diferença está no método de autenticação que você usa.

| Método de autenticação | Configuração de cliente com suporte |
| --------------------- | ------------------------------ |
| Pré-autenticação    | Windows 7/10 usando o Internet Explorer + complemento ActiveX do RDS |
| Passagem | Qualquer outro sistema operacional que dê suporte ao aplicativo de Área de Trabalho Remota da Microsoft |

O fluxo de pré-autenticação oferece mais benefícios de segurança que o fluxo de passagem. Com a pré-autenticação, você pode usar os recursos de autenticação do Azure AD, como o logon único, o acesso condicional e a verificação em duas etapas, para os seus recursos locais. Você também pode garantir que somente tráfego autenticado alcance sua rede.

Para usar a autenticação de passagem, há apenas duas modificações às etapas listadas neste artigo:
1. Na etapa 1, [Publicar o ponto de extremidade do host de RD](#publish-the-rd-host-endpoint), defina o método de pré-autenticação como **Passagem**.
2. Em [Tráfego de RDS direto para o Proxy de Aplicativo](#direct-rds-traffic-to-application-proxy), ignore totalmente a etapa 8.

## <a name="next-steps"></a>Próximas etapas

[Habilitar acesso remoto ao SharePoint com o Proxy de Aplicativo do Azure AD](application-proxy-enable-remote-access-sharepoint.md)  
[Considerações de segurança para acessar aplicativos remotamente usando o Proxy de Aplicativo do Azure AD](application-proxy-security-considerations.md)
