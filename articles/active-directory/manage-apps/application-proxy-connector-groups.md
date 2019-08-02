---
title: Publicar aplicativos em redes separadas com Aplicativo Azure AD grupos de conectores de proxy | Microsoft Docs
description: Aborda como criar e gerenciar grupos de conectores no Proxy de Aplicativo Azure AD.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/08/2018
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: dae4eea3e08818d43482c995595cc9fbc3f91910
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68381490"
---
# <a name="publish-applications-on-separate-networks-and-locations-using-connector-groups"></a>Publicar aplicativos em redes e locais separados usando grupos de conectores

Os clientes utilizam o Proxy de Aplicativo Azure AD para cada vez mais cenários e aplicativos. Então, tornamos o Proxy de Aplicativo ainda mais flexível por meio de mais topologias. Você pode criar grupos de conectores de Proxy de Aplicativo para poder atribuir conectores específicos a fim de atender a aplicativos específicos. Esse recurso fornece a você mais controle e mais formas de otimizar a implantação do Proxy de Aplicativo.

Cada conector do Proxy de Aplicativo é atribuído a um grupo de conectores. Todos os conectores que pertencem ao mesmo grupo de conectores agem como uma unidade separada em relação à alta disponibilidade e ao balanceamento de carga. Todos os conectores pertencem a um grupo de conectores. Se você não criar grupos, todos os seus conectores estão em um grupo padrão. O administrador pode criar novos grupos e atribuir conectores a esses grupos no portal do Azure.

Todos os aplicativos são atribuídos a um grupo de conectores. Se você não criar grupos, todos os seus aplicativos são atribuídos a um grupo padrão. Mas se você organizar seus conectores em grupos, poderá configurar cada aplicativo para trabalhar com um grupo específico de conectores. Neste caso, apenas os conectores nesse grupo atendem ao aplicativo mediante solicitação. Esse recurso é útil se os seus aplicativos estiverem hospedados em diferentes locais. Você pode criar grupos de conectores com base no local, para que os aplicativos sejam sempre atendidos pelos conectores que estão fisicamente próximos deles.

> [!TIP]
> Se você tiver uma grande implantação de Proxy de Aplicativo, não atribua nenhum aplicativo ao grupo de conectores padrão. Dessa forma, novos conectores não recebem nenhum tráfego ao vivo até você atribuí-los a um grupo de conectores ativo. Essa configuração permite que você coloque conectores em um modo ocioso, movendo-os de volta ao grupo padrão, para que você possa executar manutenções sem afetar os usuários.

## <a name="prerequisites"></a>Pré-requisitos

Para agrupar seus conectores, você precisa assegurar-se de ter [instalado vários conectores](application-proxy-add-on-premises-application.md). Quando você instala um novo conector, ele automaticamente se junta ao grupo de conector **Padrão** .

## <a name="create-connector-groups"></a>Criar grupos de conectores

Siga estas etapas para criar quantos grupos de conectores desejar.

1. Entre no [Portal do Azure](https://portal.azure.com).
1. Selecione **Azure Active Directory** > **Aplicativos empresariais** > **Proxy de aplicativo**.
1. Selecione **Novo grupo de conectores**. A folha Novo Grupo de Conectores é exibida.

   ![Mostra a tela para selecionar um novo grupo de conectores](./media/application-proxy-connector-groups/new-group.png)

1. Nomeie o novo grupo de conectores e use o menu suspenso para selecionar quais conectores pertencem a esse grupo.
1. Selecione **Salvar**.

## <a name="assign-applications-to-your-connector-groups"></a>Atribuir aplicativos aos grupos de conectores

Siga estas etapas para cada aplicativo publicado com Proxy de Aplicativo. Você pode atribuir um aplicativo a um grupo de conectores quando você o publica pela primeira vez, ou você pode usar estas etapas para alterar a atribuição sempre que desejar.

1. No painel de gerenciamento do seu diretório, selecione **Aplicativos empresariais** > **Todos os aplicativos** > o aplicativo que você deseja atribuir a um grupo de conectores > **Proxy de Aplicativo**.
1. No menu suspenso **Grupo de Conectores**, selecione o grupo que você deseja que o aplicativo use.
1. Clique em **Salvar** para aplicar a alteração.

## <a name="use-cases-for-connector-groups"></a>Casos de uso para grupos de conectores

Os grupos de conectores são úteis para diversos cenários, incluindo:

### <a name="sites-with-multiple-interconnected-datacenters"></a>Sites com vários datacenters interconectados

Muitas organizações têm um número de datacenters interconectados. Nesses casos, você deseja manter o tráfego dentro do datacenter o máximo possível, porque links entre datacenters são caros e lentos. Você pode implantar conectores em cada datacenter para servir apenas os aplicativos que residem dentro do datacenter. Essa abordagem minimiza os links entre datacenters e fornece uma experiência totalmente transparente para os usuários.

### <a name="applications-installed-on-isolated-networks"></a>Aplicativos instalados em redes isoladas

Os aplicativos podem ser hospedados em redes que não fazem parte da rede corporativa principal. Você pode usar grupos de conectores para instalar conectores dedicados em redes isoladas para isolar também os aplicativos para a rede. Isso geralmente acontece quando um fornecedor terceiro mantém um aplicativo específico para sua organização.

Os grupos de conectores permitem que você instale conectores dedicados para redes que publicam apenas aplicativos específicos, facilitando e protegendo a terceirização do gerenciamento de aplicativos para os fornecedores terceiros.

### <a name="applications-installed-on-iaas"></a>Aplicativos instalados no IaaS

Para aplicativos instalados no IaaS para acesso à nuvem, os grupos de conector fornecem um serviço comum para proteger o acesso a todos os aplicativos. Os grupos de conectores não criam dependência adicional em sua rede corporativa nem fragmentam a experiência de aplicativo. Conectores podem ser instalados em cada datacenter na nuvem e servir apenas os aplicativos que residem nessa rede. Você pode instalar vários conectores para obter alta disponibilidade.

Veja um exemplo de uma organização que tem diversas máquinas virtuais conectadas à própria rede virtual IaaS hospedada. Para permitir que os funcionários usem esses aplicativos, essas redes privadas são conectadas à rede corporativa usando VPN site a site. Isso proporciona uma boa experiência para os funcionários locais. Mas, talvez não seja ideal para funcionários remotos, pois exige uma infraestrutura local adicional para rotear o acesso, como você pode ver no diagrama a seguir:

![Diagrama que ilustra a rede IaaS do Azure AD](./media/application-proxy-connector-groups/application-proxy-iaas-network.png)
  
Com os grupos de conectores do Proxy de Aplicativo Azure AD, você pode permitir que um serviço comum proteja o acesso a todos os aplicativos sem criar dependências adicionais em sua rede corporativa:

![Vários fornecedores de nuvem do Azure AD IaaS](./media/application-proxy-connector-groups/application-proxy-multiple-cloud-vendors.png)

### <a name="multi-forest--different-connector-groups-for-each-forest"></a>Várias florestas – grupos de conectores diferentes para cada floresta

A maioria dos clientes que implantou o Proxy de Aplicativo usa seus recursos de SSO (Logon Único) executando a KCD (Delegação restrita de Kerberos). Para conseguir isso, as máquinas do conector precisam ser associadas a um domínio que possa delegar os usuários para o aplicativo. O KCD oferece suporte a recursos entre florestas. Porém, para as empresas que possuem ambientes diferentes de várias florestas sem qualquer relação de confiança entre eles, é possível usar um único conector para todas as florestas. 

Nesse caso, é possível implantar conectores específicos por floresta e configurá-los para atender aos aplicativos publicados para atender apenas aos usuários dessa floresta específica. Cada grupo de conectores representa uma floresta diferente. Embora o locatário e a maior parte da experiência sejam unificados para todas as florestas, os usuários podem ser atribuídos aos seus aplicativos de floresta usando os grupos do Azure AD.

### <a name="disaster-recovery-sites"></a>Sites de Recuperação de Desastre

Há duas abordagens diferentes que podem ser executadas com um site de DR (recuperação de desastres), dependendo de como os sites foram implementados:

* Se o seu site de DR for criado no modo ativo-ativo, no qual ele é exatamente como o site principal e tem as mesmas configurações de rede e do AD, você poderá criar os conectores no site de DR no mesmo grupo de conectores do site principal. Isso permite que o Azure AD detecte failovers para você.
* Se o seu site de DR for separado do site principal, você poderá criar um grupo de conectores diferente no site de DR e 1) ter aplicativos de backup ou 2) desviar manualmente o aplicativo existente para o grupo de conectores de DR, conforme necessário.

### <a name="serve-multiple-companies-from-a-single-tenant"></a>Atender a várias empresas de um único locatário

Há várias maneiras de implementar um modelo no qual um único provedor de serviço implanta e mantém serviços relacionados ao Azure AD para diversas empresas. Os grupos de conectores ajudam o administrador a separar os conectores e os aplicativos em grupos diferentes. Uma maneira, que é adequada para pequenas empresas, é ter um único locatário do Azure AD, e cada empresas têm seu próprio nome de domínio e redes. Isso também vale para cenários de fusão e aquisição, e situações nas quais uma única divisão de TI atende a várias empresas por motivos regulatórios ou comerciais.

## <a name="sample-configurations"></a>Exemplos de configuração

Entre os exemplos que você pode implementar, estão os grupos de conectores a seguir.

### <a name="default-configuration--no-use-for-connector-groups"></a>Configuração padrão – não usa grupos de conectores

Se você não usar grupos de conector, sua configuração terá esta aparência:

![Exemplo do Azure AD sem grupos de conectores](./media/application-proxy-connector-groups/application-proxy-sample-config-1.png)

Essa configuração é suficiente para testes e pequenas implantações. Ela também funcionará bem se a sua organização tiver uma topologia de rede simples.

### <a name="default-configuration-and-an-isolated-network"></a>Configuração padrão e uma rede isolada

Essa configuração é uma evolução da padrão, pois há um aplicativo específico que é executado em uma rede isolada, como a rede virtual IaaS:

![Exemplo do Azure AD sem grupos de conectores e uma rede isolada](./media/application-proxy-connector-groups/application-proxy-sample-config-2.png)

### <a name="recommended-configuration--several-specific-groups-and-a-default-group-for-idle"></a>Configuração recomendada – vários grupos específicos e um grupo padrão para ociosidade

A configuração recomendada para organizações complexas e de grande porte é ter o grupo de conectores padrão como um grupo que não atende aos aplicativos e é usado para conectores ociosos ou recém-instalados. Todos os aplicativos são atendidos usando grupos de conectores personalizados. Isso permite toda a complexidade dos cenários descritos acima.

No exemplo abaixo, a empresa tem dois data centers, A e B, com dois conectores que atendem a cada site. Cada site possui aplicativos diferentes em execução.

![Exemplo de empresa com 2 data centers e 2 conectores](./media/application-proxy-connector-groups/application-proxy-sample-config-3.png)

## <a name="next-steps"></a>Próximas etapas

* [Noções básicas sobre conectores de Proxy de Aplicativo do Azure AD](application-proxy-connectors.md)
* [Habilitar o logon único](what-is-single-sign-on.md)
