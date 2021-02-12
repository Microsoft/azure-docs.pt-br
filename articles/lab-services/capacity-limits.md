---
title: Limites de capacidade no Azure Lab Services
description: Saiba mais sobre os limites de capacidade (limites de máquina virtual) em Azure Lab Services.
ms.topic: conceptual
ms.date: 06/26/2020
ms.openlocfilehash: 9866628cd11ec8df67e6fe16ae8806f0f30ae9a1
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94491011"
---
# <a name="capacity-limits-in-azure-lab-services"></a>Limites de capacidade no Azure Lab Services
Azure Lab Services tem limites de capacidade padrão nas assinaturas do Azure para aderir às limitações de cota de computação do Azure e para atenuar a fraude. Todas as assinaturas do Azure terão um limite de capacidade inicial, que pode variar com base no tipo de assinatura, no número de núcleos de computação padrão e em núcleos de GPU disponíveis dentro de Azure Lab Services. Ele restringe quantas máquinas virtuais você pode criar dentro de seus laboratórios antes de precisar solicitar um aumento de limite.  

Se estiver próximo ou tiver atingido o limite de núcleos de máquina virtual da sua assinatura, você verá mensagens de Azure Lab Services quando tentar executar ações que criam máquinas virtuais adicionais. Por exemplo: 

- Criar um laboratório
- Publicar um laboratório
- Ajustar a capacidade do laboratório para adicionar mais máquinas virtuais a um laboratório existente

Essas ações também poderão ser desabilitadas se você já tiver atingido o limite de núcleos. 

![Limites principais-mensagem de aviso](./media/capacity-limits/warning-message.png)

## <a name="subscriptions-with-default-limit-of-zero-cores"></a>Assinaturas com limite padrão de zero núcleos
Alguns tipos de assinatura raros que são mais comumente usados para fraude podem ter um limite padrão de 0 núcleos padrão e 0 núcleos de GPU. Se você estiver usando um desses tipos de assinatura, o administrador que cria sua conta de laboratório precisará solicitar um aumento de limite antes de poder usar Azure Lab Services. 

O administrador pode seguir estas etapas para solicitar um aumento de limite:  

1.  Em sua assinatura, [crie uma conta de laboratório](tutorial-setup-lab-account.md).
2.  Na página **visão geral** da conta do laboratório, clique no botão de **aumento do limite de solicitações** na parte superior. 
3.  Siga as etapas no formulário para enviar uma solicitação de suporte para aumentar o limite.

## <a name="request-a-limit-increase"></a>Solicitar um aumento de limite
Se você atingir o limite de núcleos, poderá solicitar um aumento de limite para continuar usando Azure Lab Services. O processo de solicitação é um ponto de verificação para garantir que sua assinatura não esteja envolvida em casos de fraude ou implantações de grande escala repentinas e não intencionais.

As mensagens sobre o limite de núcleos de máquina virtual no portal de Azure Lab Services incluem um link para solicitar um aumento de limite. O link abre uma nova guia do navegador, na qual você pode criar uma nova solicitação de suporte. As informações de tipo de problema, assinatura e tipo de cota serão preenchidas automaticamente para você, conforme mostrado na imagem a seguir: 

![Nova solicitação de suporte](./media/capacity-limits/new-support-request.png)


Em seguida, você será solicitado a fornecer mais informações sobre o aumento de limite. No campo **Descrição** , forneça os seguintes detalhes:

- O que você está tentando fazer (por exemplo, criar um laboratório para ensinar uma classe de ciência da computação, executar um Hackathon e assim por diante).
- Tamanho da máquina virtual que você está usando para este laboratório
- Número de máquinas virtuais de que você precisa

Depois de enviar a solicitação de suporte, examinaremos a solicitação. Se necessário, entraremos em contato com você para obter detalhes adicionais. 

## <a name="next-steps"></a>Próximas etapas
Consulte o seguinte artigo:
- [Guia do administrador-dimensionamento de VM](administrator-guide.md#vm-sizing).
- [Perguntas frequentes](classroom-labs-faq.md).