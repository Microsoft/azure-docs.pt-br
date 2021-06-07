---
title: Roteiro de instalação do Azure AD Connect e do Azure AD Connect Health. | Microsoft Docs
description: Este documento é uma visão geral das opções de instalação e caminhos disponíveis para instalar o Azure AD Connect e o Connect Health.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/18/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae575aa6544a174a70eb8ea4749566e8660280e2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94873260"
---
# <a name="azure-ad-connect-and-azure-ad-connect-health-installation-roadmap"></a>Roteiro de instalação do Azure AD Connect e do Azure AD Connect Health

## <a name="install-azure-ad-connect"></a>Instalar o Azure AD Connect

> [!IMPORTANT]
> A Microsoft não oferece suporte à modificação ou à operação da sincronização do Azure AD Connect fora das ações formalmente documentadas. Qualquer uma dessas ações pode resultar em um estado inconsistente ou sem suporte de Azure AD Connect sincronização. Como resultado, a Microsoft não pode fornecer suporte técnico para essas implantações.

Você pode encontrar o download para o Azure AD Connect no [Centro de Download da Microsoft](https://go.microsoft.com/fwlink/?LinkId=615771).

| Solução | Cenário |
| --- | --- |
| Antes de iniciar: [Hardware e pré-requisitos](how-to-connect-install-prerequisites.md) |<li>Etapas a serem concluídas antes de começar a instalar o Azure AD Connect.</li> |
| [Configurações Expressas](how-to-connect-install-express.md) |<li>Se você tiver uma única floresta do AD, então, essa será a opção recomendada para usar.</li> <li>Entrada do usuário com a mesma senha usando a sincronização de senha.</li> |
| [Configurações personalizadas](how-to-connect-install-custom.md) |<li>Usadas quando você tem várias florestas. Dá suporte a várias [topologias](plan-connect-topologies.md) locais.</li> <li>Personalize sua opção de entrada, como a autenticação de passagem, o ADFS para federação ou use um provedor de identidade de terceiros.</li> <li>Personalize os recursos de sincronização, como filtragem e write-back.</li> |
| [Atualizar do DirSync](how-to-dirsync-upgrade-get-started.md) |<li>Usado quando você tem um servidor DirSync existente já em execução.</li> |
| [Atualizar do Azure AD Sync ou do Azure AD Connect](how-to-upgrade-previous-version.md) |<li>Há vários métodos diferentes, dependendo de sua preferência.</li> |

[Após a instalação](how-to-connect-post-installation.md) , verifique se ele está funcionando conforme o esperado e atribua licenças aos usuários.

### <a name="next-steps-to-install-azure-ad-connect"></a>Próximas etapas para instalar o Azure AD Connect
|Tópico |Link|  
| --- | --- |
|Baixar o Azure AD Connect | [Baixar o Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771)|
|Instalar usando as Configurações expressas | [Instalação expressa do Azure AD Connect](./how-to-connect-install-express.md)|
|Instalar usando Configurações personalizadas | [Instalação personalizada do Azure AD Connect](./how-to-connect-install-custom.md)|
|Atualizar do DirSync | [Atualizar a partir da ferramenta de sincronização do AD do Azure (DirSync)](./how-to-dirsync-upgrade-get-started.md)|
|Após a instalação | [Verifique a instalação e atribua licenças](how-to-connect-post-installation.md)|

### <a name="learn-more-about-install-azure-ad-connect"></a>Saiba mais sobre como instalar o Azure AD Connect
Você também deve se preparar para questões [operacionais](./how-to-connect-sync-staging-server.md) . Talvez você queira ter um servidor em espera para que possa fazer failover facilmente em caso de [desastre](how-to-connect-sync-staging-server.md#disaster-recovery). Se você pretende fazer alterações de configuração frequentes, deve planejar um servidor no [modo de preparo](how-to-connect-sync-staging-server.md) .

|Tópico |Link|  
| --- | --- |
|Topologias com suporte | [Topologias para o Azure AD Connect](plan-connect-topologies.md)|
|Conceitos de design | [Conceitos de design do Azure AD Connect](plan-connect-design-concepts.md)|
|Contas usadas para instalação | [Mais informações sobre permissões e as credenciais de Conexão do AD do Azure](reference-connect-accounts-permissions.md)|
|Planejamento operacional | [Sincronização do Azure AD Connect: considerações e tarefas operacionais](./how-to-connect-sync-staging-server.md)|
|Opções de entrada de usuário | [Opções de entrada de usuário do Azure AD Connect](plan-connect-user-signin.md)|

## <a name="configure-sync-features"></a>Configurar recursos de sincronização
O Azure Connect AD vem com vários recursos que você pode ativar opcionalmente ou que estão habilitados por padrão. Em alguns casos, alguns recursos podem exigir uma configuração adicional em certos cenários e topologias.

[filtragem](how-to-connect-sync-configure-filtering.md) é usada para limitar quais objetos são sincronizados ao Azure AD. Por padrão, todos os usuários, contatos, grupos e computadores Windows 10 são sincronizados. Você pode alterar a filtragem com base em domínios, UOs ou atributos.

[Sincronização de hash de senhas](how-to-connect-password-hash-synchronization.md) sincroniza o hash de senhas no Active Directory para o Azure AD. O usuário final pode usar a mesma senha no local e na nuvem, mas apenas gerenciá-la em um único lugar. Como ele usa seu Active Directory local como a autoridade, você também poderá usar sua própria política de senha.

[write-back de senha](../authentication/tutorial-enable-sspr.md) permitirá aos usuários alterar e redefinir suas senhas na nuvem e ter sua política de senha local aplicada.

O [write-back do dispositivo](how-to-connect-device-writeback.md) permitirá que um dispositivo registrado no Azure ad seja gravado no local Active Directory para que possa ser usado para acesso condicional.

O recurso para [impedir exclusões acidentais](how-to-connect-sync-feature-prevent-accidental-deletes.md) é ativado por padrão e protege seu diretório na nuvem contra muitas exclusões ao mesmo tempo. Por padrão, ele permite 500 exclusões por execução. Você pode alterar essa configuração, dependendo do tamanho de sua organização.

[Atualização automática](how-to-connect-install-automatic-upgrade.md) está habilitada por padrão para as instalações de configurações expressas e garante que o Azure AD Connect esteja sempre atualizado com a última versão.

### <a name="next-steps-to-configure-sync-features"></a>Próximas etapas para configurar recursos de sincronização
|Tópico |Link|  
| --- | --- |
|Configurar a filtragem | [Sincronização do Azure AD Connect: configurar a filtrar](how-to-connect-sync-configure-filtering.md)|
|Sincronização de hash de senha | [Sincronização de hash de senha](how-to-connect-password-hash-synchronization.md)|
|Autenticação de Passagem | [Autenticação de passagem](how-to-connect-pta.md)
|write-back de senha | [Introdução ao gerenciamento de senhas](../authentication/tutorial-enable-sspr.md)|
|Write-back de dispositivo | [Habilitando write-back de dispositivo no Azure AD Connect](how-to-connect-device-writeback.md)|
|impedir exclusões acidentais | [Sincronização do Azure AD Connect: impedir exclusões acidentais](how-to-connect-sync-feature-prevent-accidental-deletes.md)|
|Atualização automática | [Azure AD Connect: Atualização automática](how-to-connect-install-automatic-upgrade.md)|

## <a name="customize-azure-ad-connect-sync"></a>Personalizar a sincronização do Azure AD Connect
A sincronização do Azure AD Connect vem com uma configuração padrão que tem como objetivo funcionar para a maioria dos clientes e topologias. Porém, sempre há situações em que a configuração padrão não funciona e deve ser ajustada. Há suporte para fazer alterações, conforme documentado nesta seção e nos tópicos vinculados.

Se você não tiver trabalhado com uma topologia de sincronização antes, convém começar a entender os conceitos básicos e os termos usados, conforme descrito em [conceitos técnicos](how-to-connect-sync-technical-concepts.md). O Azure AD Connect é a evolução de MIIS2003, ILM2007 e FIM2010. Mesmo que algumas coisas sejam idênticas, também houve muitas mudanças.

A [configuração padrão](concept-azure-ad-connect-sync-default-configuration.md) pressupõe que pode haver mais de uma floresta na configuração. Nessas topologias, um objeto de usuário pode ser representado como um contato em outra floresta. O usuário também pode ter uma caixa de correio vinculada em outra floresta de recursos. O comportamento da configuração padrão é descrito em [usuários e contatos](concept-azure-ad-connect-sync-user-and-contacts.md).

O modelo de configuração em sincronização é chamado de [provisionamento declarativo](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md). Os fluxos de atributo avançados estão usando [funções](reference-connect-sync-functions-reference.md) para expressar transformações de atributos. Você pode ver e examinar a configuração inteira usando ferramentas que vêm com o Azure AD Connect. Se você precisar fazer alterações na configuração, siga as [práticas recomendadas](how-to-connect-sync-best-practices-changing-default-configuration.md) para que seja mais fácil adotar as novas versões.

### <a name="next-steps-to-customize-azure-ad-connect-sync"></a>Próximas etapas para personalizar a sincronização do Azure AD Connect
|Tópico |Link|  
| --- | --- |
|Todos os artigos sobre a sincronização do Azure AD Connect | [Sincronização do Azure AD Connect](how-to-connect-sync-whatis.md)|
|conceitos técnicos | [Sincronização de Azure AD Connect: conceitos técnicos](how-to-connect-sync-technical-concepts.md)|
|Noções básicas sobre a configuração padrão | [Sincronização do Azure AD Connect: Noções básicas sobre a configuração padrão](concept-azure-ad-connect-sync-default-configuration.md)|
|Noções básicas sobre usuários e contatos | [Azure AD Connect Sync: noções básicas sobre usuários e contatos](concept-azure-ad-connect-sync-user-and-contacts.md)|
|provisionamento declarativo | [Sincronização de Azure AD Connect: Noções básicas sobre expressões de provisionamento declarativo](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)|
|Alterar a configuração padrão | [Práticas recomendadas para alterar a configuração padrão](how-to-connect-sync-best-practices-changing-default-configuration.md)|

## <a name="configure-federation-features"></a>Configurar recursos de federação

O Azure AD Connect fornece vários recursos que simplificam a federação com o Azure AD usando o AD FS e o gerenciamento da sua relação de confiança de federação. O Azure AD Connect oferece suporte ao AD FS no Windows Server 2012 R2 ou posterior.

[Atualize o certificado TLS/SSL do farm de AD FS](how-to-connect-fed-ssl-update.md) mesmo se você não estiver usando Azure ad Connect para gerenciar a confiança da Federação.

[Adicione um servidor do AD FS](how-to-connect-fed-management.md#addadfsserver) ao seu farm para expandir o farm conforme necessário.

[Repare a relação de confiança](how-to-connect-fed-management.md#repairthetrust) com o Azure AD em alguns cliques simples.

O ADFS pode ser configurado para dar suporte a [vários domínios](how-to-connect-install-multiple-domains.md). Por exemplo, você pode ter vários domínios superiores que precisem ser usados para federação.

Se o servidor ADFS não tiver sido configurado para atualizar automaticamente os certificados do Azure AD ou se você usar uma solução não ADFS, você será notificado quando precisar [atualizar os certificados](how-to-connect-fed-o365-certs.md).

### <a name="next-steps-to-configure-federation-features"></a>Próximas etapas para configurar recursos de federação
|Tópico |Link|  
| --- | --- |
|Todos os artigos do AD FS | [Azure AD Connect e federação](how-to-connect-fed-whatis.md)|
|Configurar o ADFS com subdomínios | [Suporte a Vários Domínios para Federação com o Azure AD](how-to-connect-install-multiple-domains.md)|
|Gerenciar o farm do AD FS | [Gerenciamento do AD FS e personalização com o Azure AD Connect](how-to-connect-fed-management.md)|
|Atualização manual dos certificados de federação | [Renovando certificados de Federação para Microsoft 365 e Azure AD](how-to-connect-fed-o365-certs.md)|


## <a name="get-started-with-azure-ad-connect-health"></a>Introdução ao Azure AD Connect Health
Para começar com o Azure AD Connect Health, use as seguintes etapas:

1. [Obtenha Azure ad Premium](../fundamentals/active-directory-get-started-premium.md) ou [inicie uma avaliação](https://azure.microsoft.com/trial/get-started-active-directory/).
2. [Baixe e Instale os agentes do Azure AD Connect Health](#download-and-install-azure-ad-connect-health-agent) nos seus servidores de identidade.
3. Exiba o painel de Azure AD Connect Health em [https://aka.ms/aadconnecthealth](https://aka.ms/aadconnecthealth) .

> [!NOTE]
> Lembre-se de que, antes de você ver os dados no Painel do Azure AD Connect Health, será necessário instalar o agente do Azure AD Connect Health nos servidores de destino.
>
>

## <a name="download-and-install-azure-ad-connect-health-agent"></a>Baixar e instalar o Agente do Azure AD Connect Health
* Verifique se você [atende aos requisitos](how-to-connect-health-agent-install.md#requirements) para o Azure AD Connect Health.
* Introdução ao uso do Azure AD Connect Health do AD FS
    * [Baixe o Agente do Azure AD Connect Health para AD FS.](https://go.microsoft.com/fwlink/?LinkID=518973)
    * [Consulte as instruções de instalação](how-to-connect-health-agent-install.md#install-the-agent-for-ad-fs).
* Introdução ao uso do Azure AD Connect Health para sincronização
    * [Baixe e instale a versão mais recente do Azure AD Connect](https://go.microsoft.com/fwlink/?linkid=615771). O Agente de Integridade para sincronização será instalado como parte da instalação do Azure AD Connect (versão 1.0.9125.0 ou superior).
* Introdução ao uso do Azure AD Connect Health para o AD DS
    * [Baixe o Agente do Azure AD Connect Health para AD DS](https://go.microsoft.com/fwlink/?LinkID=820540).
    * [Consulte as instruções de instalação](how-to-connect-health-agent-install.md#install-the-agent-for-azure-ad-ds).


## <a name="azure-ad-connect-health-portal"></a>Portal do Azure AD Connect Health
O portal do Azure AD Connect Health mostra exibições de alertas, monitoramento de desempenho e análise de uso. A URL https://aka.ms/aadconnecthealth leva você até a folha principal do Azure AD Connect Health. Você pode pensar uma folha como uma janela. Na folha principal, você verá **início rápido**, serviços em Azure ad Connect Health e opções de configuração adicionais. Consulte a seguinte captura de tela e explicações breves que seguem a captura de tela. Depois que você implantar os agentes, o serviço de integridade identifica automaticamente os serviços que o Azure AD Connect Health está monitorando.

> [!NOTE]
> Para obter informações sobre licenciamento, confira as [Perguntas frequentes do Azure AD Connect Health](reference-connect-health-faq.md) ou a [Página de preços do Azure AD](https://aka.ms/aadpricing).
    
![Portal do Azure AD Connect Health](./media/whatis-hybrid-identity-health/portalsidebar.png)

* **Início rápido**: quando você seleciona essa opção, a folha **Início Rápido** será aberta. Você pode baixar o agente do Azure AD Connect Health selecionando **obter ferramentas**. Você também pode acessar a documentação e fornecer comentários.
* **Azure Active Directory Connect (Sincronização)**: essa opção mostra seus servidores do Azure AD Connect que o Azure AD Connect Health está monitorando atualmente. A entrada **Erros de sincronização** mostrará os erros básicos de sincronização de seu primeiro serviço de sincronização integrado por categorias. Quando você seleciona a entrada **Sincronizar serviços**, a folha abre mostra informações sobre os servidores do Azure AD Connect. Leia mais sobre os recursos em [usando o Azure AD Connect Health para sincronização](how-to-connect-health-sync.md).
* **Serviços de Federação do Active Directory**: essa opção mostra todos os serviços do AD FS que o Azure AD Connect Health está monitorando no momento. Quando você seleciona uma instância, a folha abre mostra informações sobre essa instância de serviço. Essas informações incluem uma análise de visão geral, propriedades, alertas, monitoramento e análise de uso. Leia mais sobre os recursos em [usando o Azure AD Connect Health com o AD FS](how-to-connect-health-adfs.md).
* **Active Directory Domain Services**: essa opção mostra todas as florestas do AD DS que o Azure AD Connect Health está monitorando no momento. Quando você seleciona uma floresta, a folha abre mostra informações sobre essa floresta. As informações incluem uma visão geral de informações essenciais, o painel de Controladores de Domínio, o painel de Status de Replicação, alertas e monitoramento. Leia mais sobre os recursos em [usando o Azure AD Connect Health com o AD DS](how-to-connect-health-adds.md).
* **Configurar**: Esta seção inclui opções para ativar ou desativar o seguinte:

   - A **atualização automática** do agente de Azure ad Connect Health para a versão mais recente: o agente de Azure ad Connect Health é atualizado automaticamente sempre que novas versões estão disponíveis. Essa opção é habilitada por padrão.
   - **Acesso a dados** da integridade de diretório do Azure ad pela Microsoft apenas para fins de solução de problemas: se essa opção estiver habilitada, a Microsoft poderá acessar os mesmos dados exibidos pelo usuário. Essas informações podem ser úteis para solução de problemas e para fornecer a assistência necessária. Esta opção está desabilitada por padrão
* **Controle de acesso baseado em função (IAM)** é a seção para gerenciar o acesso aos dados do Connect Health na base de funções. 

## <a name="next-steps"></a>Próximas etapas

- [Pré-requisitos e hardware](how-to-connect-install-prerequisites.md) 
- [Configurações Expressas](how-to-connect-install-express.md)
- [Configurações personalizadas](how-to-connect-install-custom.md)
- [Sincronização de hash de senha](how-to-connect-password-hash-synchronization.md)|
- [Autenticação de passagem](how-to-connect-pta.md)
- [Azure AD Connect e federação](how-to-connect-fed-whatis.md)
- [Instalar agentes do Azure AD Connect Health](how-to-connect-health-agent-install.md) 
- [Sincronização do Azure AD Connect](how-to-connect-sync-whatis.md)