---
title: O que é o Azure AD Connect e o Connect Health? | Microsoft Docs
description: Saiba mais sobre as ferramentas usadas para sincronizar e monitorar seu ambiente local com o Azure AD.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 01/08/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d8e1af1848405441088796d2e3b42e7b52eedba8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98065109"
---
# <a name="what-is-azure-ad-connect"></a>O que é o Azure AD Connect?

O Azure AD Connect é a ferramenta da Microsoft criada para atender e atingir suas metas de identidade híbrida.  Ela fornece os seguintes recursos:
     
- [Sincronização de hash de senha](whatis-phs.md) – um método de entrada que sincroniza o hash da senha do AD local do usuário com o Azure AD.
- [Autenticação de passagem](how-to-connect-pta.md) – um método de entrada que permite que os usuários usem a mesma senha localmente e na nuvem, mas não exige a infraestrutura adicional de um ambiente federado.
- [Integração de federação](how-to-connect-fed-whatis.md) – a federação é uma parte opcional do Azure AD Connect e pode ser usada para configurar um ambiente híbrido usando uma infraestrutura do AD FS local. Ela também fornece recursos de gerenciamento do AD FS, como renovação de certificado e implantações adicionais de servidor do AD FS.
- [Sincronização](how-to-connect-sync-whatis.md) – responsável pela criação de usuários, grupos e outros objetos.  Também é responsável por garantir que as informações de identidade dos usuários e grupos locais correspondam às da nuvem.  Essa sincronização também inclui os hashes de senha.
- [Monitoramento de Integridade](whatis-azure-ad-connect.md#what-is-azure-ad-connect-health) – o Azure AD Connect Health pode fornecer monitoramento robusto e fornecer um local central no portal do Azure para exibir essa atividade. 


![O que é o Azure AD Connect](./media/whatis-hybrid-identity/arch.png)



## <a name="what-is-azure-ad-connect-health"></a>O que é o Azure AD Connect Health?

O Azure AD (Azure Active Directory) Connect Health fornece monitoramento robusto de sua infraestrutura de identidade local. Ele permite que você mantenha uma conexão confiável com o Microsoft 365 e os Microsoft Online Services.  Essa confiabilidade é obtida fornecendo recursos de monitoramento para seus principais componentes de identidade. Além disso, ela torna os pontos de dados principais desses componentes facilmente acessíveis.

As informações são apresentadas no [Portal do Azure AD Connect Health](https://aka.ms/aadconnecthealth). Use o portal do Azure AD Connect Health para exibir alertas, monitorar o desempenho e analisar o uso, bem como outras informações. O Azure AD Connect Health permite a lente única da integridade sobre seus componentes de identidade de chave em um único lugar.

![O que é a Integridade do Azure AD Connect](./media/whatis-hybrid-identity-health/aadconnecthealth2.png)

## <a name="why-use-azure-ad-connect"></a>Por que usar o Azure AD Connect?
A integração de seus diretórios locais ao AD do Azure torna os usuários mais produtivos ao fornecer uma identidade comum para acesso aos recursos na nuvem e locais. Os usuários e as organizações podem beneficiar-se do seguinte:

* Os usuários podem usar uma única identidade para acessar aplicativos locais e serviços de nuvem, como o Microsoft 365.
* Ferramenta única para fornecer uma experiência de implantação fácil de sincronização e entrada.
* Fornece os recursos mais recentes para seus cenários. O Azure AD Connect substitui as versões mais antigas das ferramentas de integração de identidade, como DirSync e Azure AD Sync. Para saber mais, confira [Comparação das ferramentas de integração de diretórios de Identidade Híbrida](plan-hybrid-identity-design-considerations-tools-comparison.md).

## <a name="why-use-azure-ad-connect-health"></a>Por que usar o Azure AD Connect Health?
No Azure AD, os usuários são mais produtivos porque há uma identidade comum para acessar recursos de nuvem e locais. Garantir que o ambiente seja confiável, para que os usuários possam acessar esses recursos, torna-se um desafio.  O Azure AD Connect Health ajuda no monitoramento e na obtenção de informações sobre a sua infraestrutura de identidade local, garantindo assim a confiabilidade do ambiente. É tão simples quanto instalar um agente em cada um de seus servidores de identidade locais.

O Azure AD Connect Health para AD FS dá suporte ao AD FS 2.0 no Windows Server 2008 R2, no Windows Server 2012, no Windows Server 2012 R2 e no Windows Server 2016. Isso também oferece o suporte de monitoramento de servidores proxy do AD FS ou proxy de aplicativo Web que dão suporte à autenticação para acesso à extranet. Com uma instalação fácil e rápida do Agente de Integridade, o Azure AD Connect Health para AD FS oferece um conjunto de recursos principais.

Principais benefícios e melhores práticas:

|Principais benefícios|Práticas Recomendadas|
|-----|-----|
|Segurança aprimorada|[Tendências de bloqueio de extranet](how-to-connect-health-adfs.md#usage-analytics-for-ad-fs)</br>[Relatório de entradas de falha](how-to-connect-health-adfs-risky-ip.md)</br>[Em conformidade com a privacidade](reference-connect-health-user-privacy.md)|
|Receba alertas sobre [todos os problemas críticos do sistema ADFS](how-to-connect-health-alert-catalog.md#alerts-for-active-directory-federation-services)|Configuração e disponibilidade do servidor</br>[Desempenho e conectividade](how-to-connect-health-adfs.md#performance-monitoring-for-ad-fs)</br>Manutenção regular|
|Fácil de implantar e gerenciar|[Instalação rápida do agente](how-to-connect-health-agent-install.md#install-the-agent-for-ad-fs)</br>Atualização automática do agente até o mais recente</br>Dados disponíveis no portal em minutos|
[Métricas de uso](how-to-connect-health-adfs.md#usage-analytics-for-ad-fs) avançadas|Uso dos principais aplicativos</br>Locais de rede e conexão TCP</br>Solicitações de token por servidor|
|Ótima experiência do usuário|Forma do painel do portal do Azure</br>[Alertas por emails](how-to-connect-health-adfs.md#alerts-for-ad-fs)|


## <a name="license-requirements-for-using-azure-ad-connect"></a>Requisitos de licença para usar o Azure AD Connect

[!INCLUDE [active-directory-free-license.md](../../../includes/active-directory-free-license.md)]

## <a name="license-requirements-for-using-azure-ad-connect-health"></a>Requisitos de licença para usar o Azure AD Connect Health
[!INCLUDE [active-directory-free-license.md](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Próximas etapas

- [Pré-requisitos e hardware](how-to-connect-install-prerequisites.md) 
- [Configurações Expressas](how-to-connect-install-express.md)
- [Configurações personalizadas](how-to-connect-install-custom.md)
- [Instalar agentes do Azure AD Connect Health](how-to-connect-health-agent-install.md)
