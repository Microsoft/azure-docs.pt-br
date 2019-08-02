---
title: Solucionar problemas de conectividade de SMTP de saída no Azure | Microsoft Docs
description: Saiba como solucionar problemas de conectividade de SMTP de saída no Azure.
services: virtual-network
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/20/2018
ms.author: genli
ms.openlocfilehash: 13ed2dc2b304368e468c433b5abf5d056c33e406
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67466473"
---
# <a name="troubleshoot-outbound-smtp-connectivity-issues-in-azure"></a>Solucionar problemas de conectividade de SMTP de saída no Azure

Iniciando em 15 de novembro de 2017, mensagens de email de saída que são enviadas diretamente a domínios externos (por exemplo, outlook.com e gmail.com) de uma máquina virtual (VM) ficam disponíveis somente para determinados tipos de assinatura no Microsoft Azure. As conexões SMTP de saída que usam a porta 25 TCP foram bloqueadas. (a porta 25 é usada principalmente para entrega de email não autenticado).

Essa alteração no comportamento se aplica somente a novas assinaturas e novas implantações desde 15 de novembro de 2017.

## <a name="recommended-method-of-sending-email"></a>Método recomendado para enviar email
É recomendável que você use serviços de retransmissão de SMTP autenticados (que normalmente se conectam por meio da porta TCP 587 ou 443, mas oferece suporte a outras portas também) para enviar email de máquinas virtuais do Azure ou dos Serviços de Aplicativo do Azure. Esses serviços são usados para manter o IP ou a reputação do domínio para minimizar a possibilidade de provedores de email terceiros rejeitarem a mensagem. Esses serviços de retransmissão de SMTP incluem, mas não está limitados a [SendGrid](https://sendgrid.com/partners/azure/). Também é possível que você tenha um serviço de retransmissão de SMTP seguro que esteja em execução no local que você pode usar.

Usando esses serviços de entrega de email não é restrito no Azure, independentemente do tipo de assinatura.

## <a name="enterprise-agreement"></a>Contrato Enterprise
Para usuários do Enterprise Agreement do Azure, não há nenhuma alteração na capacidade técnica de enviar o email sem usar uma retransmissão autenticada. Usuários do Enterprise Agreement novos e existentes podem tentar a entrega de emails de saída das VMs do Azure diretamente aos provedores de email externo sem quaisquer restrições da plataforma do Azure. Embora não seja garantido que os provedores de email aceitarão o email de entrada de um determinado usuário, tentativas de entrega não serão bloqueadas pela plataforma do Azure para VMs dentro de assinaturas do Enterprise Agreement. Você precisará trabalhar diretamente com provedores de email para corrigir qualquer entrega de mensagens ou problemas de filtragem que envolvem provedores específicos de SPAM.

## <a name="pay-as-you-go"></a>Pré-paga
Se você se criou a conta antes de 15 de novembro de 2017 pelas ofertas de assinatura Pagamento Conforme o Uso ou Microsoft Partner Network, não haverá alteração na responsabilidade técnica para tentar a entrega de emails de saída. Você continuará a ser capaz de testar a entrega de emails de saída das VMs do Azure dentro dessas assinaturas diretamente aos provedores de email externos sem quaisquer restrições da plataforma do Azure. Novamente, não é garantido que provedores de email aceitarão emails recebidos de um determinado usuário e os usuários terão que trabalhar diretamente com provedores de email para corrigir qualquer entrega de mensagens ou problemas de filtragem que envolvem provedores específicos de SPAM.

Para assinaturas Pagamento Conforme o Uso ou Microsoft Partner Network criadas após 15 de novembro de 2017, haverá restrições técnicas que bloqueiam o email que é enviado diretamente de VMs dentro dessas assinaturas. Se você quiser a capacidade de enviar email de máquinas virtuais do Azure diretamente para provedores de email externos (não usando uma retransmissão SMTP autenticada), você pode fazer uma solicitação para remover a restrição. As solicitações serão examinadas e aprovadas conforme os critérios da Microsoft e serão concedidas somente após a realização de verificações antifraude. Para fazer uma solicitação, abra um caso de suporte usando o tipo de problema a seguir: **Gerenciamento de assinaturas** tipo de problema: **Solicitação para habilitar o fluxo de email da Port 25**. Certifique-se de adicionar detalhes sobre por que sua implantação tem que enviar emails diretamente aos provedores de email em vez de usar uma retransmissão autenticada.

Depois que uma assinatura paga conforme o uso ou Microsoft Partner Network é isento e as VMs foram 'Parado' & 'Iniciado' do portal do Azure, todas as VMs dentro dessa assinatura estarão isentos no futuro. A isenção só é aplicável para a assinatura solicitada.

> [!NOTE]
> A Microsoft se reserva ao direito de revogar essa isenção se for determinado que ocorreu uma violação dos termos de serviço.

## <a name="msdn-azure-pass-azure-in-open-education-bizspark-and-free-trial"></a>MSDN, Azure Pass, Azure via Open, educação, BizSpark e avaliação gratuita
Se você criou uma assinatura MSDN, Azure Pass, Azure in Open, Education, BizSpark, Azure Sponsorship, Azure Student, Avaliação gratuita ou qualquer Visual Studio depois de 15 de novembro de 2017, haverá restrições técnicas que bloqueiam o email enviado das VMs dentro dessas assinaturas diretamente para os provedores de email. As restrições são realizadas para evitar abusos. Nenhuma solicitação para remover essa restrição será concedida.

Se você estiver usando esses tipos de assinatura, é incentivado a usar os serviços de retransmissão de SMTP, conforme descrito neste artigo ou alterar o tipo de assinatura.

## <a name="cloud-service-provider-csp"></a>Provedor de Serviços de Nuvem (CSP)

Se você estiver usando recursos do Azure por meio do CSP, você pode solicitar o CSP para criar uma solicitação de isenção de desbloqueio com a Microsoft em seu nome, se uma retransmissão SMTP segura não pode ser usada.

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contate o suporte

Se você precisar de Ajuda, ainda [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente, usando o tipo de problema a seguir: **Gerenciamento de assinaturas** tipo de problema: **Solicitação para habilitar o fluxo de email da Port 25**.
