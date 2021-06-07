---
title: Instruções da área de trabalho virtual do Windows do Azure Advisor – Azure
description: Como resolver as recomendações do Azure Advisor para a área de trabalho virtual do Windows.
author: Heidilohr
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 760e0212d2d863e6b869c23c2e523a0e056a28ed
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "90033023"
---
# <a name="how-to-resolve-azure-advisor-recommendations"></a>Como resolver as recomendações do Azure Advisor

Este artigo descreve como você pode resolver recomendações que aparecem no Azure Advisor para área de trabalho virtual do Windows.

## <a name="no-validation-environment-enabled"></a>"Nenhum ambiente de validação habilitado"

>[!div class="mx-imgBorder"]
>![Uma captura de tela da página de excelência operacional do Azure Advisor. A recomendação "nenhum ambiente de validação habilitado" é realçada em vermelho.](media/no-validation-environment.png)

Essa recomendação aparece sob excelência operacional. A recomendação também deve mostrar uma mensagem de aviso como esta:

"Você não tem um ambiente de validação habilitado nesta assinatura. Quando você criou seus pools de hosts, você selecionou **não** para "ambiente de validação" na guia Propriedades. Para garantir a continuidade dos negócios por meio de implantações do serviço de área de trabalho virtual do Windows, verifique se você tem pelo menos um pool de hosts com um ambiente de validação no qual você pode testar possíveis problemas. "

Você pode fazer essa mensagem de aviso desaparecer habilitando um ambiente de validação em um de seus pools de hosts.

Para habilitar um ambiente de validação:

1. Vá para o portal do Azure home page e selecione o pool de hosts que você deseja alterar.

2. Em seguida, selecione o pool de hosts que você deseja alterar de um ambiente de produção para um ambiente de validação.

3. No pool de hosts, selecione **Propriedades** na coluna esquerda. Em seguida, role para baixo até ver "ambiente de validação". Selecione **Sim** e, em seguida, selecione **aplicar**.

>[!div class="mx-imgBorder"]
>![Uma captura de tela do menu Propriedades. "Ambiente de validação" é realçado em vermelho e a bolha "Sim" é selecionada.](media/validation-yes.png)

Essas alterações não farão com que o aviso saia imediatamente, mas deve desaparecer eventualmente. O Azure Advisor atualiza duas vezes por dia. Até lá, você pode adiar ou ignorar a recomendação manualmente. Recomendamos que você deixe a recomendação desaparecer por conta própria. Dessa forma, o assistente do Azure pode informá-lo se ele se origina em problemas à medida que as configurações mudam.

## <a name="not-enough-production-non-validation-environments-enabled"></a>"Ambientes de produção insuficientes (não validação) habilitados"

Essa recomendação aparece sob excelência operacional.

Para essa recomendação, a mensagem de aviso é exibida por um destes motivos:

- Você tem muitos pools de hosts em seu ambiente de validação.
- Você não tem nenhum pool de hosts de produção.

Recomendamos que os usuários tenham menos da metade de seus pools de hosts em um ambiente de validação.

Para resolver este aviso:

1. Vá para o home page portal do Azure.

2. Selecione os pools de hosts que deseja alterar da validação para a produção.

3. No pool de hosts, selecione a guia **Propriedades** na coluna no lado direito da tela. Em seguida, role para baixo até ver "ambiente de validação". Selecione **não** e, em seguida, selecione **aplicar**.

>[!div class="mx-imgBorder"]
>![Uma captura de tela do menu Propriedades. "Ambiente de validação" é realçado em vermelho e a bolha "não" é selecionada.](media/validation-no.png)

Essas alterações não farão com que o aviso saia imediatamente, mas deve desaparecer eventualmente. O Azure Advisor atualiza duas vezes por dia. Até lá, você pode adiar ou ignorar a recomendação manualmente. Recomendamos que você deixe a recomendação desaparecer por conta própria. Dessa forma, o assistente do Azure pode informá-lo se ele se origina em problemas à medida que as configurações mudam.

## <a name="not-enough-links-are-unblocked-to-successfully-implement-your-vm"></a>"Links insuficientes são desbloqueados para implementar sua VM com êxito"

Essa recomendação aparece sob excelência operacional.

Você precisa desbloquear URLs específicas para garantir que sua VM (máquina virtual) funcione corretamente. Você pode ver a lista na [lista URL segura](safe-url-list.md). Se as URLs não forem desbloqueadas, sua VM não funcionará corretamente.

Para resolver essa recomendação, certifique-se de desbloquear todas as URLs na [lista de URL segura](safe-url-list.md). Você também pode usar marca de serviço ou marcas de FQDN para desbloquear URLs.

## <a name="propose-new-recommendations"></a>Propor novas recomendações

Você pode nos ajudar a melhorar o Azure Advisor enviando ideias para recomendações. Sua recomendação pode ajudar outro usuário a ser um ponto muito difícil. Para enviar uma sugestão, acesse [nosso fórum UserVoice](https://windowsvirtualdesktop.uservoice.com/forums/930847-azure-advisor-recommendations) e preencha o formulário de envio. Ao preencher o formulário, certifique-se de fornecer o máximo de detalhes possível.

## <a name="next-steps"></a>Próximas etapas

Se você estiver procurando por guias mais detalhados sobre como resolver problemas comuns, confira [visão geral da solução de problemas, comentários e suporte para área de trabalho virtual do Windows](troubleshoot-set-up-overview.md).
