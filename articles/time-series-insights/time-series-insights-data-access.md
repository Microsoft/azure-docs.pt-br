---
title: Configurar a segurança para acessar e gerenciar a versão prévia do Azure Time Series Insights | Microsoft Docs
description: Este artigo descreve como configurar a segurança e as permissões como políticas de gerenciamento de acesso e políticas de acesso a dados para proteger a Versão prévia do Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/01/2019
ms.custom: seodec18
ms.openlocfilehash: 69180e17714b7d7004e63dce0de82a50e1f0b3af
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/17/2019
ms.locfileid: "67164632"
---
# <a name="grant-data-access-to-an-environment"></a>Conceder acesso a dados a um ambiente

Este artigo aborda os dois tipos de políticas de acesso da Versão prévia do Azure Time Series Insights.

## <a name="sign-in-to-time-series-insights"></a>Entrar no Time Series Insights

1. Entre no [Portal do Azure](https://portal.azure.com/).
1. Localize o seu ambiente do Time Series Insights. Insira `Time Series` na caixa **Pesquisar**. Selecione **Ambiente do Time Series** nos resultados da pesquisa.
1. Selecione o seu ambiente de Análise de Séries Temporais na lista.

## <a name="grant-data-access"></a>Conceder acesso a dados

Siga estas etapas para conceder acesso a dados para uma entidade de usuário.

1. Selecione **Políticas de Acesso a Dados** e, em seguida, selecione **+ Adicionar**.

    [![Data-access-um](media/data-access/data-access-one.png)](media/data-access/data-access-one.png#lightbox)

1. Escolha **Selecionar usuário**. Pesquise o nome de usuário ou endereço de email para localizar o usuário que deseja adicionar. Selecione **selecionar** para confirmar a seleção.

    [![Data-access-two](media/data-access/data-access-two.png)](media/data-access/data-access-two.png#lightbox)

1. Escolha **Selecionar função**. Escolha a função de acesso apropriada para o usuário:

    * Selecione **Colaborador** se você desejar permitir que o usuário altere os dados de referência e compartilhe consultas e perspectivas salvas com outros usuários do ambiente.

    * Caso contrário, selecione **Leitor** para permitir que o usuário consulte dados no ambiente e salve as consultas pessoais não compartilhadas no ambiente.

   Selecione **OK** para confirmar a escolha da função.

    [![Dados de acesso de três](media/data-access/data-access-three.png)](media/data-access/data-access-three.png#lightbox)

1. Selecione **OK** na página **Selecionar Função do Usuário**.

    [![Dados de acesso de quatro](media/data-access/data-access-four.png)](media/data-access/data-access-four.png#lightbox)

1. Verifique se a página **Políticas de Acesso a Dados** lista os usuários e suas funções.

    [![Data-access-five](media/data-access/data-access-five.png)](media/data-access/data-access-five.png#lightbox)

## <a name="provide-guest-access-from-another-aad-tenant"></a>Fornecer acesso de convidado de outro locatário do AAD

`Guest` não é uma função de gerenciamento. É um termo usado para uma conta Convidado de um locatário para outro. Depois que a conta Convidado é convidada no diretório do locatário, ela pode ter o mesmo controle de acesso aplicado como qualquer outra conta. Você pode conceder acesso de gerenciamento a um ambiente do Time Series Insights usando a folha de Controle de Acesso (IAM). Ou pode permitir acesso aos dados no ambiente por meio da folha Políticas de Acesso a Dados. Para obter mais informações sobre o acesso de convidado de locatário do Azure AD (Azure Active Directory), leia [Adicionar usuários de colaboração B2B do Azure Active Directory no portal do Azure](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator).

Siga estas etapas para conceder acesso de convidado a um ambiente do Time Series Insights para um usuário do Azure AD de outro locatário.

1. Selecione **Políticas de Acesso a Dados** e, em seguida, selecione **+ Convidar**.

    [![Data-access-six](media/data-access/data-access-six.png)](media/data-access/data-access-six.png#lightbox)

1. Insira o endereço de email do usuário que deseja convidar. Este endereço de email deve estar associado com o Azure AD. Opcionalmente, você pode incluir uma mensagem pessoal com o convite.

    [![Data-access-seven](media/data-access/data-access-seven.png)](media/data-access/data-access-seven.png#lightbox)

1. Procure a bolha de confirmação que aparece na tela.

    [![Data-access-eight](media/data-access/data-access-eight.png)](media/data-access/data-access-eight.png#lightbox)

1. Escolha **Selecionar usuário**. Pesquise o endereço de email do usuário convidado para localizar o usuário que deseja adicionar. Em seguida, **selecionar** para confirmar a seleção.

    [![Dados de acesso de nove](media/data-access/data-access-nine.png)](media/data-access/data-access-nine.png#lightbox)

1. Escolha **Selecionar função**. Escolha a função de acesso apropriada para o usuário convidado:

    * Selecione **Colaborador** se você desejar permitir que o usuário altere os dados de referência e compartilhe consultas e perspectivas salvas com outros usuários do ambiente.

    * Caso contrário, selecione **Leitor** para permitir que o usuário consulte dados no ambiente e salve as consultas pessoais não compartilhadas no ambiente.

   Selecione **OK** para confirmar a escolha da função.

    [![Data-access-ten](media/data-access/data-access-ten.png)](media/data-access/data-access-ten.png#lightbox)

1. Selecione **OK** na página **Selecionar Função do Usuário**.

1. Confirme se a página **Políticas de Acesso a Dados** lista o usuário convidado e as funções de cada usuário convidado.

    [![Data-access-eleven](media/data-access/data-access-eleven.png)](media/data-access/data-access-eleven.png#lightbox)

1. Agora o usuário convidado deve seguir as etapas para acessar o ambiente localizado no locatário do Azure para o qual você convidou. Primeiro, os usuários aceitam o convite que você enviou. Esse convite é enviado por email para o endereço de email usado na etapa 5. Eles selecionam **Começar** para aceitar.

    [![Data-access-twelve](media/data-access/data-access-twelve.png)](media/data-access/data-access-twelve.png#lightbox)

1. Em seguida, o usuário convidado aceita as permissões associadas à organização do administrador.

    [![Dados de acesso de treze](media/data-access/data-access-thirteen.png)](media/data-access/data-access-thirteen.png#lightbox)

1. Depois que o usuário convidado aceita o convite e é conectado ao endereço de email que você usou para convidá-lo, ele acessa o insights.azure.com. Ao chegar lá, ele seleciona o avatar ao lado do endereço de email no canto superior direito da tela.

    [![Dados de acesso de quatorze](media/data-access/data-access-fourteen.png)](media/data-access/data-access-fourteen.png#lightbox)

1. Em seguida, o usuário convidado seleciona seu locatário do Azure no menu suspenso de diretório. Esse locatário é aquele para o qual você o convidou.

    [![Dados de acesso de quinze](media/data-access/data-access-fifteen.png)](media/data-access/data-access-fifteen.png#lightbox)

Depois que o usuário convidado selecionar seu locatário, ele verá o ambiente do Time Series Insights para o qual você forneceu acesso. Agora eles têm todos os recursos associados à função que você forneceu-los com na **etapa 5**.

## <a name="next-steps"></a>Próximas etapas

* Saiba [como adicionar uma fonte de evento do Hubs de Eventos do Azure](./time-series-insights-how-to-add-an-event-source-eventhub.md) ao ambiente do Azure Time Series Insights.

* Enviar [eventos para a origem do evento](./time-series-insights-send-events.md).

* Exibir [seu ambiente no gerenciador da Versão prévia do Azure Time Series Insights](./time-series-insights-update-explorer.md).
