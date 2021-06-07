---
title: Associar o ASN do par à assinatura do Azure usando o portal
titleSuffix: Azure
description: Associar o ASN do par à assinatura do Azure usando o portal
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: how-to
ms.date: 5/18/2020
ms.author: derekol
ms.openlocfilehash: 22cb179925f95fd0762371cc904fcbd02675339a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91540261"
---
# <a name="associate-peer-asn-to-azure-subscription-using-the-portal"></a>Associar o ASN do par à assinatura do Azure usando o portal

Como um provedor de serviços de Internet ou um provedor de Internet do Exchange, antes de enviar uma solicitação de emparelhamento, primeiro você deve associar o ASN a uma assinatura do Azure usando as etapas abaixo.

Se preferir, conclua este guia usando o [PowerShell](howto-subscription-association-powershell.md).

## <a name="create-peerasn-to-associate-your-asn-with-azure-subscription"></a>Criar PeerAsn para associar seu ASN à assinatura do Azure

### <a name="sign-in-to-the-portal"></a>Entrar no portal
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="register-for-peering-resource-provider"></a>Registrar-se para o provedor de recursos de emparelhamento
Registre-se para o provedor de recursos de emparelhamento em sua assinatura seguindo as etapas abaixo. Se você não fizer isso, os recursos do Azure necessários para configurar o emparelhamento não estarão acessíveis.

1. Clique em **Assinaturas** no canto superior esquerdo do portal. Se você não localizar essa opção, clique em **Mais serviços** e pesquise por ela.

    > [!div class="mx-imgBorder"]
    > ![Abrir assinaturas](./media/rp-subscriptions-open.png)

1. Clique na assinatura que você deseja usar para emparelhamento.

    > [!div class="mx-imgBorder"]
    > ![Iniciar assinatura](./media/rp-subscriptions-launch.png)

1. Quando a assinatura for aberta, à esquerda, clique em **Provedores de recursos**. Em seguida, no painel direito, pesquise *emparelhamento* na janela de pesquisa ou use a barra de rolagem para localizar **Microsoft.Peering** e examine o **Status**. Se o status for **_Registrado_ *_, ignore as etapas abaixo e navegue até a seção _* Criar PeerAsn**. Se o status for **_NotRegistered_ *_, selecione _* Microsoft.Peering** e clique em **Registrar**.

    > [!div class="mx-imgBorder"]
    > ![Início do registro](./media/rp-register-start.png)

1. Observe que o status muda para ***Registrando***.

    > [!div class="mx-imgBorder"]
    > ![Registro em andamento](./media/rp-register-progress.png)

1. Aguarde cerca de um minuto para que ele conclua o registro. Em seguida, clique em **Atualizar** e verifique se o status é **_Registrado_**.

    > [!div class="mx-imgBorder"]
    > ![Registro concluído](./media/rp-register-completed.png)

### <a name="create-peerasn"></a>Criar PeerAsn
Como um provedor de serviços de Internet ou um Provedor do Internet Exchange, você pode criar um novo recurso PeerAsn para associar um ASN (número de sistema autônomo) a uma assinatura do Azure na página [Associar PeerAsn](https://go.microsoft.com/fwlink/?linkid=2129592). Você pode associar vários ASNs a uma assinatura criando uma **PeerASN** para cada ASN que você precisa associar.

1. Na página **Associar um ASN Par**, na guia **Noções Básicas**, preencha os campos conforme mostrado abaixo.

    > [!div class="mx-imgBorder"]
    > ![Guia de Noções Básicas do PeerAsn](./media/peerasn-basics-tab.png)

    * O **nome** corresponde ao nome do recurso e pode ser qualquer um que você escolher.  
    * Escolha a **Assinatura** à qual você precisa associar ao ASN.
    * O **nome do par** corresponde ao nome da sua empresa e precisa ser o mais próximo possível do seu perfil de PeeringDB. Observe que o valor dá suporte apenas aos caracteres a-z, A-Z e espaço
    * Insira seu ASN no campo **ASN Par**.
    * Clique em **Criar** e insira o **ENDEREÇO DE EMAIL** e o **NÚMERO DE TELEFONE** para o NOC (centro de operações de rede)
1. Em seguida, clique em **Examinar + criar** e observe que o portal executa a validação básica das informações inseridas. Isso é exibido em uma faixa de opções na parte superior, como *Executando a validação final...* .

    > [!div class="mx-imgBorder"]
    > ![Captura de tela mostra a guia Noções Básicas de A S N Associar um Par.](./media/peerasn-review-tab-validation.png)

1. Depois que a mensagem na faixa de opções passa a ser *Validação Aprovada*, verifique suas informações e envie a solicitação clicando em **Criar**. Se a validação não for aprovada, clique em **Anterior** e repita as etapas acima para modificar sua solicitação e verificar se os valores inseridos não contêm erros.

    > [!div class="mx-imgBorder"]
    > ![A captura de tela mostra a guia de Noções Básicas de A S N Associar um Par com validação aprovada.](./media/peerasn-review-tab.png)

1. Depois de enviar a solicitação, aguarde até que ela conclua a implantação. Se a implantação falhar, entre em contato com [Emparelhamento da Microsoft](mailto:peering@microsoft.com). Uma implantação bem-sucedida será exibida como a seguir.

    > [!div class="mx-imgBorder"]
    > ![Sucesso do PeerAsn](./media/peerasn-success.png)

### <a name="view-status-of-a-peerasn"></a>Ver o status de um PeerAsn
Depois que o recurso PeerAsn tiver sido implantado com êxito, você precisará aguardar a Microsoft aprovar a solicitação de associação. Podem ser necessárias 12 horas para a aprovação. Depois de aprovado, você receberá uma notificação no endereço de email inserido na seção acima.

> [!IMPORTANT]
> Aguarde até que o ValidationState passe a ser "Aprovado" antes de enviar uma solicitação de emparelhamento. Podem ser necessárias 12 horas para essa aprovação.

## <a name="modify-peerasn"></a>Modificar PeerAsn
No momento, não há suporte para a modificar o PeerAsn. Se precisar modificá-lo, entre em contato com [Emparelhamento da Microsoft](mailto:peering@microsoft.com).

## <a name="delete-peerasn"></a>Excluir PeerAsn
No momento, não há suporte para a excluir um PeerAsn. Se você precisar excluir o PeerAsn, entre em contato com [Emparelhamento da Microsoft](mailto:peering@microsoft.com).

## <a name="next-steps"></a>Próximas etapas

* [Criar ou modificar um Emparelhamento direto usando o portal](howto-direct-portal.md)
* [Converter um Emparelhamento direto herdado para o recurso do Azure usando o portal](howto-legacy-direct-portal.md)
* [Criar ou modificar um emparelhamento do Exchange usando o portal](howto-exchange-portal.md)
* [Converter um emparelhamento do Exchange herdado para o recurso do Azure usando o portal](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>Recursos adicionais

Para obter mais informações, acesse [Perguntas frequentes sobre emparelhamento de Internet](faqs.md)
