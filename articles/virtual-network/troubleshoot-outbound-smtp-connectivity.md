---
title: Solucionar problemas de conectividade de SMTP de saída no Azure | Microsoft Docs
description: Conheça o método recomendado para enviar email e como solucionar problemas de conectividade SMTP de saída no Azure.
services: virtual-network
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/20/2018
ms.author: genli
ms.openlocfilehash: d2e5996da5a1fe3f5b154d57ee509f25e54e30ac
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2020
ms.locfileid: "96862353"
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

Se você se inscreveu antes de 15 de novembro de 2017 para a assinatura paga conforme o uso, não haverá nenhuma alteração na capacidade técnica de experimentar a entrega de email de saída. Você continuará a ser capaz de testar a entrega de emails de saída das VMs do Azure dentro dessas assinaturas diretamente aos provedores de email externos sem quaisquer restrições da plataforma do Azure. Novamente, não é garantido que provedores de email aceitarão emails recebidos de um determinado usuário e os usuários terão que trabalhar diretamente com provedores de email para corrigir qualquer entrega de mensagens ou problemas de filtragem que envolvem provedores específicos de SPAM.

Para assinaturas pagas conforme o uso que foram criadas após 15 de novembro de 2017, haverá restrições técnicas que bloqueiam emails enviados diretamente de VMs dentro dessas assinaturas. Se você quiser a capacidade de enviar emails de VMs do Azure diretamente para provedores de email externos (não usando uma retransmissão de SMTP autenticado) e tiver uma conta em boas condições com um histórico de pagamento, você pode fazer uma solicitação para remover a restrição na seção de **conectividade** da folha **diagnosticar e resolver** para um recurso de rede Virtual do Azure no portal do Azure. Se qualificado, sua assinatura será habilitada ou você receberá instruções sobre as próximas etapas. 

Depois que uma assinatura paga conforme o uso é isenta e as VMs são interrompidas e iniciadas na portal do Azure, todas as VMs nessa assinatura são isentas no futuro. A isenção se aplica somente à assinatura solicitada e somente ao tráfego de VM que é roteado diretamente para a Internet.

> [!NOTE]
> A Microsoft se reserva ao direito de revogar essa isenção se for determinado que ocorreu uma violação dos termos de serviço.

## <a name="msdn-azure-pass-azure-in-open-education-azure-for-students-vistual-studio-and-free-trial"></a>MSDN, Azure Pass, Azure via Open, educação, Azure for Students, Visual Studio e avaliação gratuita

Se você criou um MSDN, Azure Pass, Azure via Open, educação, aluno do Azure, avaliação gratuita ou qualquer assinatura do Visual Studio depois de 15 de novembro de 2017, você terá restrições técnicas que bloqueiam emails enviados de VMs dentro dessas assinaturas diretamente aos provedores de email. As restrições são realizadas para evitar abusos. Nenhuma solicitação para remover essa restrição será concedida.

Se estiver usando esses tipos de assinatura, você é incentivado a usar os serviços de retransmissão de SMTP conforme descrito neste artigo ou a alterar seu tipo de assinatura.

## <a name="cloud-service-provider-csp"></a>Provedor de Serviços de Nuvem (CSP)

Se você estiver usando recursos do Azure por meio do CSP, poderá fazer uma solicitação para remover a restrição na seção de **conectividade** da folha **diagnosticar e resolver** para um recurso de rede virtual no portal do Azure. Se qualificado, sua assinatura será habilitada ou você receberá instruções sobre as próximas etapas.

## <a name="microsoft-partner-network-mpn-bizspark-plus-or-azure-sponsorship"></a>Microsoft Partner Network (MPN), BizSpark Plus ou Azure Sponsorship

Para Microsoft Partner Network (MPN), BizSpark Plus ou Azure Sponsorship assinaturas que foram criadas após 15 de novembro de 2017, haverá restrições técnicas que bloqueiam emails enviados diretamente de VMs dentro dessas assinaturas. Se você quiser a capacidade de enviar emails de VMs do Azure diretamente para provedores de email externos (não usando uma retransmissão de SMTP autenticado), você pode fazer uma solicitação abrindo um caso de suporte usando o **Technical** seguinte tipo de problema: a  >  conectividade de **rede virtual** técnica  >  **Connectivity**  >  **não pode enviar email (SMTP/porta 25)**. Certifique-se de adicionar detalhes sobre por que sua implantação tem que enviar emails diretamente aos provedores de email em vez de usar uma retransmissão autenticada. As solicitações serão revisadas e aprovadas a critério da Microsoft. As solicitações podem ser concedidas somente depois que verificações antifraudes adicionais forem concluídas. 

Depois que uma assinatura é isenta e as VMs são interrompidas e iniciadas no portal do Azure, todas as VMs nessa assinatura são isentas no futuro. A isenção se aplica somente à assinatura solicitada e somente ao tráfego de VM que é roteado diretamente para a Internet.

## <a name="restrictions-and-limitations"></a>Restrições e limitações

- Não há suporte para o tráfego da porta 25 de roteamento por meio dos serviços de PaaS do Azure como o [Firewall do Azure](https://azure.microsoft.com/services/azure-firewall/)

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contate o suporte

Se você ainda precisar de ajuda, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o problema rapidamente usando o seguinte tipo de problema: a conectividade de **Technical**  >  **rede virtual** técnica  >  **Connectivity**  >  **não pode enviar email (SMTP/porta 25)**.
