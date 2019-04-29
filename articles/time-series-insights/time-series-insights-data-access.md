---
title: Configurar a segurança para acessar e gerenciar a versão prévia do Azure Time Series Insights | Microsoft Docs
description: Este artigo descreve como configurar a segurança e as permissões como políticas de gerenciamento de acesso e políticas de acesso a dados para proteger a Versão prévia do Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/26/2018
ms.custom: seodec18
ms.openlocfilehash: dd4c5e1652eb4dbff66591aa4bbe74e51be3e6c0
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63759558"
---
# <a name="grant-data-access-to-an-environment"></a>Conceder acesso a dados a um ambiente

Este artigo aborda os dois tipos de políticas de acesso da Versão prévia do Azure Time Series Insights.

## <a name="sign-in-to-tsi"></a>Entrar no TSI

1. Entre no [Portal do Azure](https://portal.azure.com/).
1. Localize o seu ambiente do Time Series Insights. Insira `Time Series` na caixa **Pesquisar**. Selecione **Ambiente do Time Series** nos resultados da pesquisa.
1. Selecione o seu ambiente de Análise de Séries Temporais na lista.

## <a name="grant-data-access"></a>Conceder acesso a dados

Siga estas etapas para conceder acesso a dados para uma entidade de usuário.

1. Selecione **Políticas de Acesso a Dados** e, em seguida, selecione **+ Adicionar**.

    ![Data-access-one][1]

1. Escolha **Selecionar usuário**. Pesquise o nome de usuário ou endereço de email para localizar o usuário que deseja adicionar. Clique em **Selecionar** para confirmar a seleção.

    ![Data-access-two][2]

1. Escolha **Selecionar função**. Escolha a função de acesso apropriada para o usuário:

    * Selecione **Colaborador** se você desejar permitir que o usuário altere os dados de referência e compartilhe consultas e perspectivas salvas com outros usuários do ambiente.

    * Caso contrário, selecione **Leitor** para permitir que o usuário consulte dados no ambiente e salve as consultas pessoais não compartilhadas no ambiente.

   Selecione **OK** para confirmar a escolha da função.

    ![Data-access-three][3]

1. Selecione **OK** na página **Selecionar Função do Usuário**.

    ![Data-access-four][4]

1. Verifique se a página **Políticas de Acesso a Dados** lista os usuários e suas funções.

    ![Data-access-five][5]

## <a name="provide-guest-access-from-another-aad-tenant"></a>Fornecer acesso de convidado de outro locatário do AAD

`Guest` não é uma função de gerenciamento. É um termo usado para uma conta Convidado de um locatário para outro. Depois que a conta Convidado é convidada no diretório do locatário, ela pode ter o mesmo controle de acesso aplicado como qualquer outra conta. Você pode conceder acesso de gerenciamento a um ambiente do Time Series Insights usando a folha de Controle de Acesso (IAM). Ou pode permitir acesso aos dados no ambiente por meio da folha Políticas de Acesso a Dados. Para obter mais informações sobre o acesso de convidado de locatário do Azure AD (Azure Active Directory), leia [Adicionar usuários de colaboração B2B do Azure Active Directory no portal do Azure](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator).

Siga estas etapas para conceder acesso de convidado a um ambiente do Time Series Insights para um usuário do Azure AD de outro locatário.

1. Selecione **Políticas de Acesso a Dados** e, em seguida, selecione **+ Convidar**.

    ![Data-access-six][6]

1. Insira o endereço de email do usuário que deseja convidar. Este endereço de email deve estar associado com o Azure AD. Opcionalmente, você pode incluir uma mensagem pessoal com o convite.

    ![Data-access-seven][7]

1. Procure a bolha de confirmação que aparece na tela.

    ![Data-access-eight][8]

1. Escolha **Selecionar usuário**. Pesquise o endereço de email do usuário convidado para localizar o usuário que deseja adicionar. Clique em **Selecionar** para confirmar a seleção.

    ![Data-access-nine][9]

1. Escolha **Selecionar função**. Escolha a função de acesso apropriada para o usuário convidado:

    * Selecione **Colaborador** se você desejar permitir que o usuário altere os dados de referência e compartilhe consultas e perspectivas salvas com outros usuários do ambiente.

    * Caso contrário, selecione **Leitor** para permitir que o usuário consulte dados no ambiente e salve as consultas pessoais não compartilhadas no ambiente.

   Selecione **OK** para confirmar a escolha da função.

    ![Data-access-ten][10]

1. Selecione **OK** na página **Selecionar Função do Usuário**.

1. Confirme se a página **Políticas de Acesso a Dados** lista o usuário convidado e as funções de cada usuário convidado.

    ![Data-access-eleven][11]

1. Agora o usuário convidado deve seguir as etapas para acessar o ambiente localizado no locatário do Azure para o qual você convidou. Primeiro, os usuários aceitam o convite que você enviou. Esse convite é enviado por email para o endereço de email usado na etapa 5. Eles selecionam **Começar** para aceitar.

    ![Data-access-twelve][12]

1. Em seguida, o usuário convidado aceita as permissões associadas à organização do administrador.

    ![Data-access-thirteen][13]

1. Depois que o usuário convidado aceita o convite e é conectado ao endereço de email que você usou para convidá-lo, ele acessa o insights.azure.com. Ao chegar lá, ele seleciona o avatar ao lado do endereço de email no canto superior direito da tela.

    ![Data-access-fourteen][14]

1. Em seguida, o usuário convidado seleciona seu locatário do Azure no menu suspenso de diretório. Esse locatário é aquele para o qual você o convidou.

    ![Data-access-fifteen][15]

Depois que o usuário convidado selecionar seu locatário, ele verá o ambiente do Time Series Insights para o qual você forneceu acesso. Agora eles têm todos os recursos associados à função que você forneceu-los com na **etapa 5**.

## <a name="next-steps"></a>Próximas etapas

* Saiba [como adicionar uma fonte de evento do Hubs de Eventos do Azure](./time-series-insights-how-to-add-an-event-source-eventhub.md) ao ambiente do Azure Time Series Insights.

* Enviar [eventos para a origem do evento](./time-series-insights-send-events.md).

* Exibir [seu ambiente no gerenciador da Versão prévia do Azure Time Series Insights](./time-series-insights-update-explorer.md).

<!-- Images -->
[1]: media/data-access/data-access-one.png
[2]: media/data-access/data-access-two.png
[3]: media/data-access/data-access-three.png
[4]: media/data-access/data-access-four.png
[5]: media/data-access/data-access-five.png
[6]: media/data-access/data-access-six.png
[7]: media/data-access/data-access-seven.png
[8]: media/data-access/data-access-eight.png
[9]: media/data-access/data-access-nine.png
[10]: media/data-access/data-access-ten.png
[11]: media/data-access/data-access-eleven.png
[12]: media/data-access/data-access-twelve.png
[13]: media/data-access/data-access-thirteen.png
[14]: media/data-access/data-access-fourteen.png
[15]: media/data-access/data-access-fifteen.png
