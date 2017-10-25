---
title: Monitorar sua infraestrutura de identidade local na nuvem.
description: "Esta é a página de Integridade do Azure AD Connect que descreve o que ele é e por que você deve usá-lo."
services: active-directory
documentationcenter: 
author: karavar
manager: samueld
editor: curtand
ms.assetid: 82798ea6-5cd3-4f30-93ae-d56536f8d8e3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: 881ce13b6e4b10064294e590431434b29da3fb33
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-your-on-premises-identity-infrastructure-and-synchronization-services-in-the-cloud"></a>Monitorar infraestrutura de identidade local e serviços de sincronização na nuvem
O Azure AD (Azure Active Directory) Connect Health ajuda no monitoramento e na obtenção de informações sobre a sua infraestrutura de identidade local e os serviços de sincronização. Ele permite manter uma conexão confiável com o Office 365 e o Microsoft Online Services, fornecendo recursos de monitoramento para os componentes de identidade de chave, como servidores do AD FS (Serviços de Federação do Active Directory), servidores do Azure AD Connect (também conhecido como Mecanismo de Sincronização), controladores de domínio do Active Directory etc. Ele também torna os pontos de dados principais sobre esses componentes facilmente acessíveis para que você possa usá-los e obter informações importantes para tomar decisões informadas.

As informações são apresentadas no [Portal do Azure AD Connect Health](https://aka.ms/aadconnecthealth). No portal do Azure AD Connect Health, você poderá exibir alertas, o monitoramento de desempenho, a análise de uso e outras informações. O Azure AD Connect Health permite a lente única da integridade sobre seus componentes de identidade de chave em um único lugar.

![O que é a Integridade do Azure AD Connect](./media/active-directory-aadconnect-health/aadconnecthealth2.png)

Como aumentam os recursos do Azure AD Connect Health, o portal fornece um único painel por meio das lentes de identidade. Você obtém um ambiente ainda mais robusto, íntegro e integrado para seus usuários aumentar sua capacidade de fazer essas coisas.

## <a name="why-use-azure-ad-connect-health"></a>Por que usar o Azure AD Connect Health?
Ao integrar seus diretórios locais com o Azure AD, os usuários são mais produtivos porque há uma identidade comum para acessar recursos de nuvem e locais. No entanto, essa integração gera os desafios de garantir de que esse ambiente esteja íntegro, para que os usuários possam acessar de forma confiável recursos locais e na nuvem por meio de qualquer dispositivo. O Azure AD Connect Health ajuda você a monitorar e obter percepções sobre sua infraestrutura de identidade local usada para acessar o Office 365 ou outros aplicativos do Azure AD. É tão simples quanto instalar um agente em cada um de seus servidores de identidade locais.

## <a name="azure-ad-connect-health-for-ad-fsactive-directory-aadconnect-health-adfsmd"></a>[Azure AD Connect Health para AD FS](active-directory-aadconnect-health-adfs.md)
O Azure AD Connect Health para AD FS dá suporte ao AD FS 2.0 no Windows Server 2008 R2, no Windows Server 2012 e no Windows Server 2012 R2. Isso também oferece o suporte de monitoramento de servidores proxy do AD FS ou proxy de aplicativo Web que dão suporte à autenticação para acesso à extranet. Com uma instalação fácil e de baixo custo do Agente de Integridade, o Azure AD Connect Health para AD FS oferece o seguinte conjunto de recursos principais:

* Monitoramento com alertas para saber quando os servidores do AD FS e proxy do AD FS não estão íntegros
* Notificações de email para alertas críticos
* Tendências nos dados de desempenho, que são úteis para o planejamento de capacidade do AD FS
* Análise de uso para entradas do AD FS com tabelas dinâmicas (aplicativos, usuários, local de rede etc.), que são úteis para entender como o AD FS está obtendo utilizado
* Relatórios para o AD FS, como os 50 primeiros usuários com tentativas inválidas de nome de usuário/senha e o último endereço IP

O vídeo a seguir fornece uma visão geral do Azure AD Connect Health para AD FS.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-AD-Connect-Health--Monitor-you-identity-bridge/player]
>
>

## <a name="azure-ad-connect-health-for-syncactive-directory-aadconnect-health-syncmd"></a>[Azure AD Connect Health para sincronização](active-directory-aadconnect-health-sync.md)
O Azure AD Connect Health para sincronização monitora e fornece informações sobre as sincronizações que ocorrem entre seu Active Directory local e o Azure AD. O Azure AD Connect Health para sincronização fornece o seguinte conjunto de recursos principais:

* Monitoramento com alertas para saber quando um servidor do Azure AD Connect, também conhecido como Mecanismo de Sincronização, não está íntegro
* Notificações de email para alertas críticos
* Informações operacionais de sincronização, incluindo gráficos de latência para operações de sincronização e tendências em operações diferentes, como adições, atualizações, exclusões
* Informações rápidas sobre propriedades de sincronização e a última exportação com êxito para o Azure AD
* Os relatórios sobre erros de sincronização no nível do objeto \(não exigem o Azure AD Premium\)

O vídeo a seguir fornece uma visão geral do Azure AD Connect Health para sincronização.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-Active-Directory-Connect-Health-Monitoring-the-sync-engine/player]
>
>

## <a name="azure-ad-connect-health-for-ad-dsactive-directory-aadconnect-health-addsmd"></a>[Azure AD Connect Health para AD DS](active-directory-aadconnect-health-adds.md)
O Azure AD Connect Health para AD DS (Active Directory Domain Services) fornece monitoramento para controladores de domínio instalados no Windows Server 2008 R2, no Windows Server 2012, no Windows Server 2012 R2 e no Windows Server 2016. A instalação do Agente de Integridade permite que você monitore o ambiente do AD DS local diretamente da nuvem. O Azure AD Connect Health para o AD DS fornece o seguinte conjunto de recursos principais:

* Alertas de monitoramento para detectar quando controladores de domínio não estão íntegros, juntamente com as notificações por email para alertas críticos
* O painel de Controladores de Domínio que fornece uma visão geral da integridade e do status operacional dos controladores de domínio
* O painel de Status de Replicação com informações mais recentes de replicação e links para guias de solução de problemas quando são detectados erros
* Acesso rápido em qualquer lugar a gráficos de dados de desempenho de contadores de desempenho comuns, necessários para fins de monitoramento e solução de problemas

O vídeo a seguir fornece uma visão geral do Azure AD Connect Health para AD DS.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-AD-Connect-Health-monitors-on-premises-AD-Domain-Services/player]
>
>

## <a name="get-started-with-azure-ad-connect-health"></a>Introdução ao Azure AD Connect Health
Para começar com o Azure AD Connect Health, use as seguintes etapas:

1. [Obtenha o Azure AD Premium](../active-directory-get-started-premium.md) ou [inicie uma avaliação](https://azure.microsoft.com/trial/get-started-active-directory/).
2. [Baixe e Instale os agentes do Azure AD Connect Health](#download-and-install-azure-ad-connect-health-agent) nos seus servidores de identidade.
3. Exiba o painel do Azure AD Connect Health em [https://aka.ms/aadconnecthealth](https://aka.ms/aadconnecthealth).

> [!NOTE]
> Lembre-se de que, antes de você ver os dados no Painel do Azure AD Connect Health, será necessário instalar o agente do Azure AD Connect Health nos servidores de destino.
>
>

## <a name="download-and-install-azure-ad-connect-health-agent"></a>Baixar e instalar o Agente do Azure AD Connect Health
* Verifique se você [atende aos requisitos](active-directory-aadconnect-health-agent-install.md#requirements) para o Azure AD Connect Health.
* Introdução ao uso do Azure AD Connect Health do AD FS
    * [Baixe o Agente do Azure AD Connect Health para AD FS.](http://go.microsoft.com/fwlink/?LinkID=518973)
    * [Veja as instruções de instalação](active-directory-aadconnect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-fs).
* Introdução ao uso do Azure AD Connect Health para sincronização
    * [Baixe e instale a versão mais recente do Azure AD Connect](http://go.microsoft.com/fwlink/?linkid=615771). O Agente de Integridade para sincronização será instalado como parte da instalação do Azure AD Connect (versão 1.0.9125.0 ou superior).
* Introdução ao uso do Azure AD Connect Health para o AD DS
    * [Baixe o Agente do Azure AD Connect Health para AD DS](http://go.microsoft.com/fwlink/?LinkID=820540).
    * [Veja as instruções de instalação](active-directory-aadconnect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-ds).

## <a name="azure-ad-connect-health-portal"></a>Portal do Azure AD Connect Health
O portal do Azure AD Connect Health mostra exibições de alertas, monitoramento de desempenho e análise de uso. A URL https://aka.ms/aadconnecthealth leva você até a folha principal do Azure AD Connect Health. Você pode pensar uma folha como uma janela. Na folha principal, você vê o **Início Rápido**, os Serviços no Azure AD Connect Health e opções de configuração adicionais. Consulte a seguinte captura de tela e explicações breves que seguem a captura de tela. Depois que você implantar os agentes, o serviço de integridade identifica automaticamente os serviços que o Azure AD Connect Health está monitorando.

> [!NOTE]
> Para obter informações sobre licenciamento, consulte o [do Azure AD conectar FAQ](active-directory-aadconnect-health-faq.md) ou [página de preços do Azure AD](https://aka.ms/aadpricing).
    
![Portal do Azure AD Connect Health](./media/active-directory-aadconnect-health/portal4.png)

* **Início rápido**: quando você seleciona essa opção, a folha **Início Rápido** será aberta. Você pode baixar o agente do Azure AD Connect Health selecionando **obter ferramentas**. Você também pode acessar a documentação e fornecer comentários.
* **Serviços de Federação do Active Directory**: essa opção mostra todos os serviços do AD FS que o Azure AD Connect Health está monitorando no momento. Quando você seleciona uma instância, a folha abre mostra informações sobre essa instância de serviço. Essas informações incluem uma análise de visão geral, propriedades, alertas, monitoramento e análise de uso. Leia mais sobre os recursos em [usando o Azure AD Connect Health com o AD FS](active-directory-aadconnect-health-adfs.md).
* **Azure Active Directory Connect (Sincronização)**: essa opção mostra seus servidores do Azure AD Connect que o Azure AD Connect Health está monitorando atualmente. Quando você seleciona a entrada, a folha abre mostra informações sobre os servidores do Azure AD Connect. Leia mais sobre os recursos em [usando o Azure AD Connect Health para sincronização](active-directory-aadconnect-health-sync.md).
* **Active Directory Domain Services**: essa opção mostra todas as florestas do AD DS que o Azure AD Connect Health está monitorando no momento. Quando você seleciona uma floresta, a folha abre mostra informações sobre essa floresta. As informações incluem uma visão geral de informações essenciais, o painel de Controladores de Domínio, o painel de Status de Replicação, alertas e monitoramento. Leia mais sobre os recursos em [usando o Azure AD Connect Health com o AD DS](active-directory-aadconnect-health-adds.md).
* **Configurar**: Esta seção inclui opções para ativar ou desativar o seguinte:

  - Opção para atualizar automaticamente o agente do Azure AD Connect Health para a versão mais recente automática: você será atualizado automaticamente para a versão mais recente do agente do Azure AD Connect Health quando ela estiver disponível. Isso é habilitado por padrão.
  - Permitir que o Microsoft acesse os dados de integridade do seu diretório Azure AD para fins de solução de problemas: se isso estiver habilitado, a Microsoft será capaz de ver os mesmos dados que você vê. Essas informações podem ajudar na solução de problemas e a obter ajuda para problemas. Isso está desabilitado por padrão.

## <a name="related-links"></a>Links relacionados
* [Instalação do Agente do Azure AD Connect Health](active-directory-aadconnect-health-agent-install.md)
* [Operações de Azure AD Connect Health](active-directory-aadconnect-health-operations.md)
* [Usando o Azure AD Connect Health com o AD FS](active-directory-aadconnect-health-adfs.md)
* [Usando o Azure AD Connect Health para sincronização](active-directory-aadconnect-health-sync.md)
* [Usar o Azure AD Connect Health com o AD DS](active-directory-aadconnect-health-adds.md)
* [Perguntas frequentes do Azure AD Connect Health](active-directory-aadconnect-health-faq.md)
* [Histórico de versão do Azure AD Connect Health](active-directory-aadconnect-health-version-history.md)
