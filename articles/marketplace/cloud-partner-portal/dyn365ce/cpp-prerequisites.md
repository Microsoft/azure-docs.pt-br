---
title: Dynamics 365 for Customer Engagement oferta pré-requisitos | O Azure Marketplace
description: Pré-requisitos de publicação de uma oferta do Aplicativo Azure no Azure Marketplace.
services: Dynamics 365 for Customer Engagement offer, Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: pabutler
ms.openlocfilehash: 9cfec65843012e781c8a8dd06cbc4d1225b0dd36
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67338905"
---
# <a name="dynamics-365-for-customer-engagement-prerequisites"></a>Pré-requisitos do Dynamics 365 for Customer Engagement

Este artigo descreve os pré-requisitos técnicos e comerciais para a publicação de uma oferta de aplicativo do Dynamics 365 for Customer Engagement no AppSource Marketplace.  Se você ainda não fez isso, examine os [Office 365, Dynamics 365, PowerApps e Power BI oferecem guia de publicação](../../appsource-offer-publishing-guide.md).


## <a name="technical-requirements"></a>Requisitos técnicos

O aplicativo do Dynamics 365 for Customer Engagement precisa estar em conformidade com as [diretrizes de revisão de aplicativo do Microsoft AppSource](https://smp-cdn-prod.azureedge.net/documents/AppsourceGuidelines/Microsoft%20AppSource%20app%20review%20guidelines_v5.pdf), que inclui os seguintes requisitos:


|              Requisito             |        DESCRIÇÃO           |
|            ---------------           |      ---------------         |
| Integração do Microsoft Azure Active Directory   | Seu aplicativo deve permitir logon único federado do Azure Active Directory (SSO federado AAD) com o consentimento habilitado. Para obter mais informações, confira [Como obter o Certificado do AppSource para o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-get-appsource-certified). |
| Integração aos serviços do Microsoft Cloud (opcional) | Quando essa funcionalidade for necessária, seu aplicativo precisará ser integrado a outros serviços em nuvem da Microsoft, como o Microsoft Power BI, o Microsoft Flow ou serviços do Microsoft Azure, por exemplo, aprendizado de máquina ou serviços cognitivos. |
| Voltado para a linha de negócios            |  Seu aplicativo precisa se concentrar em um problema ou um processo empresarial bem definido, ser direcionado principalmente a clientes comerciais e permitir que os usuários entrem com suas credenciais de trabalho (nome de usuário e senha).  |
| Período de avaliação gratuita e experiência de avaliação |  Um cliente precisa conseguir usar seu aplicativo gratuitamente por um tempo limitado: uma mensagem “Obter agora” para aplicativos gratuitos, uma “Avaliação gratuita” por um período especificado, um demonstrador de “Test Drive” ou uma opção de solicitação “Entrar em contato comigo”.  |
| Configuração inexistente/mínima                 | Seu aplicativo precisa ser fácil e rápido de ser instalado e configurado (sem nenhuma personalização ou nenhum desenvolvimento necessário).  |
| Suporte ao cliente                     | O suporte para seu aplicativo precisa incluir um link de suporte no qual os clientes podem encontrar ajuda.  |
| Disponibilidade/tempo de atividade                  | Seu aplicativo precisa ter um tempo de atividade de, pelo menos, 99,9%. |
|  |  |


## <a name="business-requirements"></a>Requisitos de negócios

Os requisitos comerciais incluem as seguintes obrigações contratuais, legais e de procedimentos:

* Você precisa estar registrado no [MPN (Microsoft Partner Network)](https://partners.microsoft.com/PartnerProgram/simplifiedenrollment.aspx) ou ser um Editor do Marketplace de Nuvem registrado. Caso não esteja registrado, siga as etapas descritas em [Torne-se um Editor do Marketplace de Nuvem](https://docs.microsoft.com/azure/marketplace/become-publisher).  (Você também pode se conectar com o [Dynamics 365 para a equipe de integração do compromisso com o cliente](https://experience.dynamics.com/isvengage/)).

    >[!NOTE]
    >Você deve usar a mesma conta de registro do Microsoft Developer Center para entrar no Portal do Cloud Partner. Você deve ter apenas uma conta da Microsoft para suas ofertas do Azure Marketplace. Esta conta não deve ser específica para serviços ou ofertas individuais.

* Como o AppSource não oferece uma opção de publicação habilitada para comércio, você precisa usar sua infraestrutura atual de ordens e cobrança sem alterações nem investimentos adicionais.
* Você é responsável por disponibilizar o suporte técnico a clientes de modo comercialmente razoável. Esse suporte pode ser gratuito, pago ou por meio de abordagens de comunidade.
* Você é responsável pelo licenciamento do software e quaisquer dependências de software de terceiros.
* Você deve ter criado o manual e os acessórios de marketing associados, como um nome de aplicativo oficial, uma descrição (no formato HTML), imagens de logotipo no formato PNG (40 x 40, 90 x 90, 115 x 115 e 255 x 115 pixels), bem como Termos de Uso e uma Política de Privacidade.  


## <a name="next-steps"></a>Próximas etapas

Depois de atender a esses requisitos, você poderá [criar uma oferta do Dynamics 365 Customer Engagement](./cpp-create-offer.md) 
