---
title: Logon único baseado em cabeçalho para aplicativos locais com Aplicativo Azure AD proxy
description: Saiba como fornecer logon único para aplicativos locais que são protegidos com autenticação baseada em cabeçalho.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/05/2020
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: d4cd69a90c4af2f996bd965ffaa145dce761018f
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99259529"
---
# <a name="header-based-single-sign-on-for-on-premises-apps-with-azure-ad-app-proxy-preview"></a>Logon único baseado em cabeçalho para aplicativos locais com Aplicativo Azure AD proxy (versão prévia)

O proxy de aplicativo Azure Active Directory (Azure AD) nativamente dá suporte ao acesso de logon único a aplicativos que usam cabeçalhos para autenticação. Você pode configurar valores de cabeçalho exigidos por seu aplicativo no Azure AD. Os valores de cabeçalho serão enviados para o aplicativo por meio do proxy de aplicativo. Alguns benefícios de usar o suporte nativo para a autenticação baseada em cabeçalho com o proxy de aplicativo incluem:  

* **Simplifique o fornecimento de acesso remoto aos seus aplicativos locais** -o proxy de aplicativo permite simplificar sua arquitetura de acesso remoto existente. Você pode substituir o acesso VPN a esses aplicativos. Você também pode remover dependências de soluções de identidade local para autenticação. Os usuários não notarão nada diferente quando entrarem para usar seus aplicativos corporativos. Eles ainda podem trabalhar de qualquer lugar e em qualquer dispositivo.  

* **Nenhum software adicional ou alterações em seus aplicativos** – você pode usar os conectores de proxy de aplicativo existentes e não exige a instalação de nenhum software adicional.  

* **Ampla lista de atributos e transformações disponíveis** -todos os valores de cabeçalho disponíveis são baseados em declarações padrão emitidas pelo Azure AD. Todos os atributos e transformações disponíveis para [Configurar declarações para aplicativos SAML ou OIDC](../develop/active-directory-saml-claims-customization.md#attributes) também estão disponíveis para serem usados como valores de cabeçalho. 

## <a name="pre-requisites"></a>Pré-requisitos
Antes de começar a usar o logon único para aplicativos de autenticação baseados em cabeçalho, verifique se o seu ambiente está pronto com as seguintes configurações e configurações:
- Você precisa habilitar o proxy de aplicativo e instalar um conector que tenha uma linha de site para seus aplicativos. Consulte o tutorial [Adicionar um aplicativo local para acesso remoto por meio do proxy de aplicativo](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad) para saber como preparar seu ambiente local, instalar e registrar um conector e testar o conector. 

## <a name="supported-capabilities"></a>Funcionalidades com suporte

A tabela a seguir lista os recursos comuns necessários para aplicativos de autenticação baseados em cabeçalho que têm suporte com o proxy de aplicativo. 

|Requisito   |Descrição|
|----------|-----------|
|SSO federado |No modo previamente autenticado, todos os aplicativos são protegidos com a autenticação do Azure AD e permitem que os usuários tenham logon único. |
|Acesso remoto |O proxy de aplicativo permite o acesso remoto ao aplicativo. Os usuários podem acessar o aplicativo da Internet em qualquer navegador usando a URL externa. O proxy de aplicativo não se destina ao uso de acesso corporativo. |
|Integração baseada em cabeçalho |O proxy de aplicativo faz a integração de SSO com o Azure AD e, em seguida, passa a identidade ou outros dados de aplicativo como cabeçalhos HTTP para o aplicativo. |
|Autorização do aplicativo |As políticas comuns podem ser especificadas com base no aplicativo que está sendo acessado, na associação de grupo do usuário e em outras políticas. No Azure AD, as políticas são implementadas usando o [acesso condicional](../conditional-access/overview.md). As políticas de autorização de aplicativo se aplicam somente à solicitação de autenticação inicial. |
|Autenticação de Step-up |As políticas podem ser definidas para forçar a autenticação adicional, por exemplo, para obter acesso a recursos confidenciais. |
|Autorização refinada |Fornece controle de acesso no nível da URL. Políticas adicionadas podem ser impostas com base na URL que está sendo acessada. A URL interna configurada para o aplicativo, define o escopo do aplicativo ao qual a política é aplicada. A política configurada para o caminho mais granular é imposta.  |

> [!NOTE] 
> Este artigo apresenta a conexão de aplicativos de autenticação baseados em cabeçalho ao Azure AD usando o proxy de aplicativo e é o padrão recomendado. Como alternativa, também há um padrão de integração que usa o PingAccess com o Azure AD para habilitar a autenticação baseada em cabeçalho. Para obter mais detalhes, consulte [autenticação baseada em cabeçalho para logon único com proxy de aplicativo e PingAccess](application-proxy-ping-access-publishing-guide.md).

## <a name="how-it-works"></a>Como ele funciona

:::image type="content" source="./media/application-proxy-configure-single-sign-on-with-headers/how-it-works.png" alt-text="Como o logon único baseado em cabeçalho funciona com o proxy de aplicativo." lightbox="./media/application-proxy-configure-single-sign-on-with-headers/how-it-works.png":::

1. O administrador personaliza os mapeamentos de atributo exigidos pelo aplicativo no portal do AD do Azure. 
2. Quando um usuário acessa o aplicativo, o proxy de aplicativo garante que o usuário seja autenticado pelo Azure AD 
3. O serviço de nuvem do proxy de aplicativo está ciente dos atributos necessários. Portanto, o serviço busca as declarações correspondentes do token de ID recebido durante a autenticação. Em seguida, o serviço converte os valores nos cabeçalhos HTTP necessários como parte da solicitação para o conector. 
4. Em seguida, a solicitação é passada ao conector, que é passado para o aplicativo de back-end. 
5. O aplicativo recebe os cabeçalhos e pode usar esses cabeçalhos conforme necessário. 

## <a name="publish-the-application-with-application-proxy"></a>Publicar o aplicativo com o proxy de aplicativo

1. Publique seu aplicativo de acordo com as instruções descritas em [publicar aplicativos com o proxy de aplicativo](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).  
    - O valor da URL interna determina o escopo do aplicativo. Se você configurar o valor da URL interna no caminho raiz do aplicativo, todos os subcaminhos abaixo da raiz receberão a mesma configuração de cabeçalho e outras configurações do aplicativo. 
    - Crie um novo aplicativo para definir uma configuração de cabeçalho ou atribuição de usuário diferente para um caminho mais granular do que o aplicativo configurado. No novo aplicativo, configure a URL interna com o caminho específico que você precisa e, em seguida, configure os cabeçalhos específicos necessários para essa URL. O proxy de aplicativo sempre corresponderá às suas definições de configuração para o caminho mais granular definido para um aplicativo. 

2. Selecione **Azure Active Directory**   como o **método de pré-autenticação**. 
3. Atribua um usuário de teste navegando para **usuários e grupos** e atribuindo os usuários e grupos apropriados. 
4. Abra um navegador e navegue até a **URL externa**   nas configurações de proxy de aplicativo. 
5. Verifique se você pode se conectar ao aplicativo. Embora você possa se conectar, não é possível acessar o aplicativo ainda que os cabeçalhos não estejam configurados. 

## <a name="configure-single-sign-on"></a>Configurar o logon único 
Antes de começar a usar o logon único para aplicativos baseados em cabeçalho, você já deve ter instalado um conector de proxy de aplicativo e o conector pode acessar os aplicativos de destino. Caso contrário, siga as etapas em [tutorial: proxy de aplicativo do AD do Azure](application-proxy-add-on-premises-application.md)   , volte aqui. 

1. Depois que o aplicativo for exibido na lista de aplicativos empresariais, selecione-o e selecione **logon único**. 
2. Defina o modo de logon único como **baseado em cabeçalho**. 
3. Em configuração básica, **Azure Active Directory**, será selecionado como o padrão. 
4. Selecione Editar lápis, em cabeçalhos para configurar os cabeçalhos para enviar ao aplicativo. 
5. Selecione **Adicionar novo cabeçalho**. Forneça um **nome** para o cabeçalho e selecione **atributo** ou **transformação** e selecione na lista suspensa qual cabeçalho seu aplicativo precisa.  
    - Para saber mais sobre a lista de atributos disponíveis, consulte [personalizações de declarações-atributos](../develop/active-directory-saml-claims-customization.md#attributes). 
    - Para saber mais sobre a lista de transformação disponível, consulte [personalizações de declarações – transformações de declaração](../develop/active-directory-saml-claims-customization.md#claim-transformations). 
    - Você também pode adicionar um **cabeçalho de grupo** para enviar todos os grupos dos quais um usuário faz parte ou os grupos atribuídos ao aplicativo como um cabeçalho. Para saber mais sobre como configurar grupos como um valor, confira: [Configurar declarações de grupo para aplicativos](../hybrid/how-to-connect-fed-group-claims.md#add-group-claims-to-tokens-for-saml-applications-using-sso-configuration). 
6. Selecione Salvar. 

## <a name="test-your-app"></a>Testar seu aplicativo 

Quando você concluir todas essas etapas, seu aplicativo deverá estar em execução e disponível. Para testar o aplicativo: 
1. Abra um novo navegador ou janela particular do navegador para garantir que os cabeçalhos armazenados em cache anteriormente sejam apagados. Em seguida, navegue até a **URL externa**   nas configurações de proxy de aplicativo.
2. Entre com a conta de teste que você atribuiu ao aplicativo. Se você puder carregar e entrar no aplicativo usando o SSO, você estará bom! 

## <a name="considerations"></a>Considerações

- O proxy de aplicativo é usado para fornecer acesso remoto a aplicativos locais ou na nuvem privada. O proxy de aplicativo não é recomendado para lidar com o tráfego originado internamente da rede corporativa.
- O acesso a aplicativos de autenticação baseada em cabeçalho deve ser restrito somente ao tráfego do conector ou outra solução de autenticação baseada em cabeçalho permitida. Normalmente, isso é feito por meio da restrição de acesso à rede para o aplicativo usando uma limitação de firewall ou IP no servidor de aplicativos.

## <a name="next-steps"></a>Próximas etapas

- [O que é logon único?](what-is-single-sign-on.md)
- [O que é o proxy de aplicativo?](what-is-application-proxy.md)
- [Série de Início Rápido no Gerenciamento de Aplicativos](view-applications-portal.md)
