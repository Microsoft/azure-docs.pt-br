---
title: Solucionar problemas de conectividade de SMTP de saída no Azure | Microsoft Docs
description: Conheça o método recomendado para enviar email e como solucionar problemas com a conectividade SMTP de saída no Azure.
services: virtual-network
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/04/2021
ms.author: genli
ms.openlocfilehash: 518b8e33ac9adfdd1aa121e0cb8d1558545980e7
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99054839"
---
# <a name="troubleshoot-outbound-smtp-connectivity-problems-in-azure"></a>Solucionar problemas de conectividade SMTP de saída no Azure

A partir de 15 de novembro de 2017, as mensagens de email de saída enviadas diretamente para domínios externos (como outlook.com e gmail.com) de uma VM (máquina virtual) são disponibilizadas somente para determinados tipos de assinatura no Azure. As conexões SMTP de saída que usam a porta 25 TCP foram bloqueadas. (A porta 25 é usada principalmente para entrega de email não autenticado.)

Essa alteração no comportamento se aplica somente a assinaturas e implantações criadas após 15 de novembro de 2017.

## <a name="recommended-method-of-sending-email"></a>Método recomendado para enviar email

Recomendamos que você use os serviços de retransmissão SMTP autenticados para enviar emails de VMs do Azure ou do serviço Azure App. (Esses serviços de retransmissão normalmente se conectam por meio da porta TCP 587, mas dão suporte a outras portas.) Esses serviços são usados para manter a reputação do IP ou do domínio para minimizar a possibilidade de os provedores de email de terceiros rejeitarem mensagens. O [SendGrid](https://sendgrid.com/partners/azure/) é um desses serviços de retransmissão de SMTP, mas há outros. Você também pode ter um serviço de retransmissão SMTP seguro em execução no local que você pode usar.

Usando esses serviços de entrega de email não é restrito no Azure, independentemente do tipo de assinatura.

## <a name="enterprise-agreement"></a>Contrato Enterprise

Para usuários do Enterprise Agreement do Azure, não há nenhuma alteração na capacidade técnica de enviar o email sem usar uma retransmissão autenticada. Usuários do Enterprise Agreement novos e existentes podem tentar a entrega de emails de saída das VMs do Azure diretamente aos provedores de email externo sem quaisquer restrições da plataforma do Azure. Não há nenhuma garantia de que os provedores de email aceitarão emails de entrada de qualquer usuário específico. Mas a plataforma do Azure não bloqueará as tentativas de entrega para VMs em assinaturas Contrato Enterprise. Você precisará trabalhar diretamente com os provedores de email para corrigir quaisquer problemas de entrega de mensagens ou de filtragem de SPAM que envolvam provedores específicos.

## <a name="pay-as-you-go"></a>Pago conforme o uso

Se você se inscreveu antes de 15 de novembro de 2017, para uma assinatura paga conforme o uso, não haverá nenhuma alteração na sua capacidade técnica de experimentar a entrega de email de saída. Você ainda poderá tentar a entrega de email de saída de VMs do Azure dentro dessas assinaturas diretamente para provedores de email externos sem restrições da plataforma Azure. Novamente, não há nenhuma garantia de que os provedores de email aceitarão emails de entrada de qualquer usuário específico. Os usuários terão que trabalhar diretamente com os provedores de email para corrigir quaisquer problemas de entrega de mensagens ou de filtragem de SPAM que envolvam provedores específicos.

Para assinaturas pagas conforme o uso que foram criadas após 15 de novembro de 2017, haverá restrições técnicas que bloqueiam emails enviados diretamente de VMs dentro das assinaturas. Se você quiser ser capaz de enviar emails de VMs do Azure diretamente para provedores de email externos (sem usar uma retransmissão de SMTP autenticado) e tiver uma conta em boas condições com um histórico de pagamento, você poderá solicitar que a restrição tenha sido removida. Você pode fazer isso na seção de **conectividade** da folha **diagnosticar e resolver** para um recurso de rede Virtual do Azure no portal do Azure. Se sua solicitação for aceita, sua assinatura será habilitada ou você receberá instruções para as próximas etapas. 

Depois que uma assinatura paga conforme o uso é isenta e as VMs são interrompidas e reiniciadas no portal do Azure, todas as VMs nessa assinatura são isentas no futuro. A isenção se aplica somente à assinatura solicitada e somente ao tráfego de VM que é roteado diretamente para a Internet.

> [!NOTE]
> A Microsoft se reserva o direito de revogar essas isenções se for determinado que uma violação dos termos de serviço ocorreu.

## <a name="msdn-azure-pass-azure-in-open-education-azure-for-students-visual-studio-and-free-trial"></a>MSDN, Azure Pass, Azure via Open, educação, Azure for Students, Visual Studio e avaliação gratuita

Se você criou um dos seguintes tipos de assinatura após 15 de novembro de 2017, você terá restrições técnicas que bloqueiam emails enviados de VMs dentro da assinatura diretamente para provedores de email:
- MSDN
- Azure Pass
- Azure via Open
- Educação
- Azure for Students
- Avaliação gratuita
- Qualquer assinatura do Visual Studio  

As restrições estão em vigor para evitar abusos. As solicitações para remover essas restrições não serão concedidas.

Se você estiver usando esses tipos de assinatura, recomendamos que use os serviços de retransmissão SMTP, conforme descrito anteriormente neste artigo, ou para alterar o tipo de assinatura.

## <a name="cloud-solution-provider"></a>Provedor de soluções de nuvem

Se você estiver usando recursos do Azure por meio de um provedor de soluções de nuvem, poderá fazer uma solicitação para remover a restrição na seção **conectividade** do painel **diagnosticar e resolver** para um recurso de rede virtual no portal do Azure. Se sua solicitação for aceita, sua assinatura será habilitada ou você receberá instruções para as próximas etapas.

## <a name="microsoft-partner-network-bizspark-plus-or-azure-sponsorship"></a>Microsoft Partner Network, BizSpark Plus ou Azure Sponsorship

Para assinaturas dos seguintes tipos que foram criados após 15 de novembro de 2017, haverá restrições técnicas que bloqueiam emails enviados diretamente de VMs dentro das assinaturas:

- Microsoft Partner Network (MPN)
- BizSpark Plus
- Azure Sponsorship

Se você quiser ser capaz de enviar emails de VMs do Azure diretamente para provedores de email externos (sem usar uma retransmissão SMTP autenticada), poderá fazer uma solicitação abrindo um caso de suporte usando o seguinte tipo de problema: a  >  conectividade de **rede virtual** técnica  >    >  **não pode enviar email (SMTP/porta 25)**. Certifique-se de adicionar detalhes sobre por que sua implantação tem que enviar email diretamente aos provedores de email em vez de usar uma retransmissão autenticada. As solicitações serão revisadas e aprovadas a critério da Microsoft. As solicitações serão concedidas somente após a conclusão de verificações de antifraude adicionais. 

Depois que uma assinatura é isenta e as VMs são interrompidas e reiniciadas no portal do Azure, todas as VMs nessa assinatura são isentas no futuro. A isenção se aplica somente à assinatura solicitada e somente ao tráfego de VM que é roteado diretamente para a Internet.

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contate o suporte

Caso ainda precise de ajuda, [contate o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente. Use este tipo de problema : a  >  conectividade de **rede virtual** técnica  >    >  **não pode enviar email (SMTP/porta 25)**.
