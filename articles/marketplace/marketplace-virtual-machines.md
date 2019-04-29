---
title: Guia de Publicação da Oferta de Máquina Virtual para o Azure Marketplace
description: Este artigo descreve os requisitos para publicar uma máquina virtual e uma avaliação gratuita de software para implantação no Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: ellacroi
manager: nunoc
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 07/09/2018
ms.author: ellacroi
ms.openlocfilehash: eb49d0fe610a78b8d1a407644a206fddaba0a67a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60764094"
---
# <a name="virtual-machine-offer-publishing-guide"></a>Guia de Publicação da Oferta de Máquina Virtual

As imagens de Máquina Virtual são uma das principais formas de publicar uma solução no Azure Marketplace. Use este guia para compreender os requisitos dessa oferta. 

Essas são ofertas de transação que são implantadas e cobradas pelo Marketplace. A chamada à ação que um usuário vê é "Obtenha agora".

## <a name="free-trial"></a>Avaliação gratuita 

Você pode solicitar que os usuários testem sua oferta acessando licenças de software de prazo limitado ao usar o modelo de faturamento Bring Your Own License (BYOL). Abaixo estão os requisitos para implantar essa oferta. 

|Requisitos  |Detalhes  |
|---------|---------|
|Período de avaliação gratuita e experiência de avaliação     |   Seus clientes podem experimentar seu aplicativo gratuitamente por tempo limitado. Seus clientes não precisam pagar nenhuma taxa de licença ou assinatura pela sua oferta. Seu cliente não precisa pagar pelo produto ou serviço interno subjacente da Microsoft. Todas as opções da versão de avaliação são implantadas em sua assinatura do Azure. Você tem o controle exclusivo do gerenciamento e da otimização de custos. Você deve escolher uma avaliação gratuita ou uma demonstração interativa. Não importa o que você escolher, sua avaliação gratuita deve fornecer aos clientes um período de tempo predefinido para testar sua oferta sem custos adicionais.|
|Solução facilmente configurável, pronta para uso    |  Seu aplicativo deve ser rápido para configurar e fácil de usar.       |
|Disponibilidade/tempo de atividade    |    Seu aplicativo ou plataforma SaaS deve ter um tempo de atividade de pelo menos 99,9%.     |
|Azure Active Directory     |    Sua oferta deve permitir o SSO (logon único) federado do Azure AD (Azure Active Directory) (SSO federado do Azure AD) com o consentimento habilitado.     |

## <a name="test-drive"></a>Test drive

Você implanta uma ou mais máquinas virtuais por meio de aplicativos de infraestrutura como serviço (IaaS) ou de software como serviço (SaaS). Uma vantagem da opção de publicação de test drive é o provisionamento automático de uma máquina virtual ou da solução inteira mostrada por um tour guiado pelo parceiro hospedado. Um test drive oferece uma avaliação sem custo adicional para o seu cliente. Seu cliente não precisa ser um cliente do Azure existente para aproveitar a experiência de avaliação. 

Entre em contato conosco em [amp-testdrive](mailto:amp-testdrive@microsoft.com) para começar a usar. 

|Requisitos  |Detalhes |
|---------|---------|
| Você tem um aplicativo do Marketplace   |    Um ou mais máquinas de virtuais por meio de IaaS ou SaaS.      |

## <a name="interactive-demo"></a>Demonstração Interativa

Você fornece uma experiência interativa de sua solução aos clientes usando uma demonstração interativa. A vantagem da opção de publicação de demonstração interativa é que você fornece uma experiência de avaliação sem o provisionamento complicado da solução complexa. 

## <a name="virtual-machine-offer"></a>Oferta de Máquina Virtual

Use o tipo de oferta de Máquina Virtual quando implantar um dispositivo virtual na assinatura associada ao cliente. As VM são totalmente habilitadas para venda usando modelos de licença BYOL (traga sua própria licença) e pagamento conforme o uso. A Microsoft hospeda a transação comercial e cobra o cliente em seu nome. Você tem a vantagem de usar a relação de pagamento preferencial entre o cliente e a Microsoft, incluindo os Contratos Enterprise.

> [!NOTE]
> Neste momento, todos os compromissos pecuniários associados a um Contrato Enterprise podem ser usados em relação ao uso do Azure por sua VM, mas não em relação aos valores de licenciamento de seu software.  
> 
> [!NOTE]
> Você pode restringir a descoberta e a implantação de sua VM a um conjunto específico de clientes publicando a imagem e o preço como uma oferta Privada. Ofertas privadas permitem que você crie ofertas exclusivas para seus clientes mais próximos e oferecer software e termos personalizados a eles. Os termos personalizados permitem a você destacar uma variedade de cenários, incluindo negócios de campos especializados com preços e termos especializados, e também acesso antecipado a software de versão limitada. As ofertas privadas permitem que você ofereça preços específicos ou produtos a um conjunto limitado de clientes criando um novo SKU com esses detalhes.  
> *   Para obter mais informações sobre as Ofertas Privadas, visite Ofertas Privadas na página do Azure Marketplace localizada em [azure.microsoft.com/blog/private-offers-on-azure-marketplace](https://azure.microsoft.com/blog/private-offers-on-azure-marketplace).  

| Requisito | Detalhes |  
|:--- |:--- | 
| Cobrança e medição | Sua VM deve dar suporte à cobrança mensal BYOL ou Pagamento Conforme o Uso. |  
| VHD (disco rígido virtual) compatível com Azure | As VMs devem ser criadas em Windows ou Linux. <ul> <li>Para obter mais informações sobre como criar um VHD Linux, consulte [Distribuições do Linux endossadas no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).</li> <li>Para obter mais informações sobre como criar um VHD Windows, consulte [Criar um VHD compatível com o Azure](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md).</li> </ul> |  

>[!Note]
>Cloud Solution Providers (CSP) parceiro canal participar agora está disponível.  Consulte [Cloud Solution Providers](./cloud-solution-providers.md) para obter mais informações sobre sua oferta por meio do Microsoft CSP de marketing de parceiros canais.

## <a name="next-steps"></a>Próximas etapas

Caso ainda não tenha feito isso, 

- [Registre-se](https://azuremarketplace.microsoft.com/sell) no Marketplace.

Se você estiver registrado e estiver criando uma nova oferta ou trabalhando em uma existente,

- [Entre no Portal do Cloud Partner](https://cloudpartner.azure.com) para criar ou concluir a oferta.
- Consulte [Oferta de máquina virtual](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-virtual-machine-offer) para obter mais informações.
