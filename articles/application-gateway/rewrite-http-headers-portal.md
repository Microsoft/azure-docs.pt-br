---
title: Reescrever cabeçalhos de solicitação e resposta HTTP no portal-Aplicativo Azure gateway
description: Saiba como usar o portal do Azure para configurar um gateway de Aplicativo Azure para regravar os cabeçalhos HTTP nas solicitações e respostas que passam pelo gateway
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/13/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: 79314db13531f1fcf518c7931d4a1aa9158a172b
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397188"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-portal"></a>Reescrever cabeçalhos de solicitação e resposta HTTP com Aplicativo Azure gateway-portal do Azure

Este artigo descreve como usar o portal do Azure para configurar uma instância de [SKU do gateway de aplicativo v2](./application-gateway-autoscaling-zone-redundant.md) para regravar os cabeçalhos HTTP em solicitações e respostas.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="before-you-begin"></a>Antes de começar

Você precisa ter uma instância de SKU do gateway de aplicativo v2 para concluir as etapas neste artigo. Não há suporte para a regravação de cabeçalhos na SKU v1. Se você não tiver a SKU v2, crie uma instância de [SKU do gateway de aplicativo v2](./tutorial-autoscale-ps.md) antes de começar.

## <a name="create-required-objects"></a>Criar objetos necessários

Para configurar a reescrita do cabeçalho HTTP, você precisa concluir estas etapas.

1. Crie os objetos que são necessários para a reescrita do cabeçalho HTTP:

   - **Ação de regravação** : usada para especificar os campos de cabeçalho de solicitação e solicitação que você pretende reescrever e o novo valor para os cabeçalhos. Você pode associar uma ou mais condições de regravação a uma ação de regravação.

   - **Condição de regravação** : uma configuração opcional. As condições de regravação avaliam o conteúdo das solicitações e respostas HTTP (S). A ação de regravação ocorrerá se a solicitação ou resposta HTTP (S) corresponder à condição de regravação.

     Se você associar mais de uma condição a uma ação, a ação ocorrerá somente quando todas as condições forem atendidas. Em outras palavras, a operação é uma operação AND lógica.

   - **Regra de regravação** : contém várias combinações de condição de ação de regravação/regravação.

   - **Sequência de regras** : ajuda a determinar a ordem na qual as regras de regravação são executadas. Essa configuração é útil quando você tem várias regras de regravação em um conjunto de regravação. Uma regra de regravação que tem um valor de sequência de regra mais baixo é executada primeiro. Se você atribuir o mesmo valor de sequência de regra a duas regras de reescrita, a ordem de execução será não determinística.

   - **Conjunto de regravação** : contém várias regras de regravação que serão associadas a uma regra de roteamento de solicitação.

2. Anexe a regravação definida a uma regra de roteamento. A configuração de regravação é anexada ao ouvinte de origem por meio da regra de roteamento. Quando você usa uma regra de roteamento básica, a configuração de reescrita de cabeçalho é associada a um ouvinte de origem e é uma reescrita de cabeçalho global. Quando você usa uma regra de roteamento com base em caminho, a configuração de reescrita de cabeçalho é definida no mapa de caminho de URL. Nesse caso, ele se aplica somente à área de caminho específica de um site.

Você pode criar vários conjuntos de regravação de cabeçalho HTTP e aplicar cada regravação definida a vários ouvintes. Mas você pode aplicar apenas uma regravação definida a um ouvinte específico.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no [portal do Azure](https://portal.azure.com/) com sua conta do Azure.

## <a name="configure-header-rewrite"></a>Configurar a regravação do cabeçalho

Neste exemplo, modificaremos uma URL de redirecionamento regravando o cabeçalho de local na resposta HTTP enviada por um aplicativo de back-end.

1. Selecione **todos os recursos** e, em seguida, selecione o gateway de aplicativo.

2. Selecione **regravações** no painel esquerdo.

3. Selecione **reescrever conjunto** :

   ![Adicionar conjunto de regravação](media/rewrite-http-headers-portal/add-rewrite-set.png)

4. Forneça um nome para o conjunto de regravação e associe-o a uma regra de roteamento:

   - Insira o nome para a regravação definida na caixa **nome** .
   - Selecione uma ou mais das regras listadas na lista **regras de roteamento associadas** . Você pode selecionar apenas as regras que não foram associadas a outros conjuntos de regravação. As regras que já foram associadas a outros conjuntos de regravação estão esmaecidas.
   - Selecione **Avançar**.
   
     ![Adicionar nome e Associação](media/rewrite-http-headers-portal/name-and-association.png)

5. Criar uma regra de reescrita:

   - Selecione **Adicionar regra de regravação**.

     ![Adicionar regra de reescrita](media/rewrite-http-headers-portal/add-rewrite-rule.png)

   - Insira um nome para a regra de reescrita na caixa **reescrever nome da regra** . Insira um número na caixa **sequência de regras** .

     ![Adicionar nome da regra de regravação](media/rewrite-http-headers-portal/rule-name.png)

6. Neste exemplo, Reescreveremos o cabeçalho Location apenas quando ele contiver uma referência a azurewebsites.net. Para fazer isso, adicione uma condição para avaliar se o cabeçalho local na resposta contém azurewebsites.net:

   - Selecione **Adicionar condição** e, em seguida, selecione a caixa que contém as instruções **If** para expandi-la.

     ![Adicione uma condição](media/rewrite-http-headers-portal/add-condition.png)

   - Na lista **tipo de variável a ser verificada** , selecione **cabeçalho http**.

   - Na lista **tipo de cabeçalho** , selecione **resposta**.

   - Como neste exemplo estamos avaliando o cabeçalho de local, que é um cabeçalho comum, selecione o  **cabeçalho comum** sob **nome do cabeçalho**.

   - Na lista **cabeçalho comum** , selecione **local**.

   - Em **diferenciação de maiúsculas e minúsculas** , selecione **não**.

   - Na lista **operador** , selecione **igual (=)**.

   - Insira um padrão de expressão regular. Neste exemplo, vamos usar o padrão  `(https?):\/\/.*azurewebsites\.net(.*)$` .

   - Selecione **OK**.

     ![Configurar uma condição if](media/rewrite-http-headers-portal/condition.png)

7. Adicione uma ação para reescrever o cabeçalho de local:

   - Na lista **tipo de ação** , selecione **definir**.

   - Na lista **tipo de cabeçalho** , selecione **resposta**.

   - Em **nome do cabeçalho** , selecione **cabeçalho comum**.

   - Na lista **cabeçalho comum** , selecione **local**.

   - Insira o valor do cabeçalho. Neste exemplo, usaremos `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` como o valor do cabeçalho. Esse valor substituirá *azurewebsites.net* por *contoso.com* no cabeçalho de local.

   - Selecione **OK**.

     ![Adicionar uma ação](media/rewrite-http-headers-portal/action.png)

8. Selecione **criar** para criar o conjunto de regravação:

   ![Selecione Criar](media/rewrite-http-headers-portal/create.png)

9. A exibição do conjunto de regravação será aberta. Verifique se o conjunto de reescrita que você criou está na lista de conjuntos de regravação:

   ![Reescrever conjunto de exibição](media/rewrite-http-headers-portal/rewrite-set-list.png)

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como configurar alguns casos de uso comuns, consulte [cenários de reescrita de cabeçalho comum](./rewrite-http-headers.md).