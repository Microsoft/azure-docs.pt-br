---
title: Reescreva cabeçalhos de solicitação e resposta HTTP com o Gateway de aplicativo do Azure - portal do Azure | Microsoft Docs
description: Saiba como usar o portal do Azure para configurar um Gateway de aplicativo do Azure para reconfigurar os cabeçalhos HTTP em solicitações e respostas, passando por meio do gateway
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 04/10/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: 6afc07f98905469b06622e7829ec4a215b94845e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60716100"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-portal"></a>Reescreva cabeçalhos de solicitação e resposta HTTP com o Gateway de aplicativo do Azure - portal do Azure

Este artigo mostra como usar o portal do Azure para configurar uma [SKU do Gateway de aplicativo v2](<https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant>) reescreva os cabeçalhos HTTP em solicitações e respostas.

> [!IMPORTANT]
> O dimensionamento automático e o gateway de aplicativo com redundância de zona SKU está atualmente em visualização pública. Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Veja os [Termos de Uso Adicionais para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter detalhes.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="before-you-begin"></a>Antes de começar

Você precisa ter um v2 de Gateway de aplicativo, pois o cabeçalho de funcionalidade e reconfiguração de SKU não há suporte para a SKU de v1. Se você não tiver o SKU do v2, crie uma [SKU do Gateway de aplicativo v2](https://docs.microsoft.com/azure/application-gateway/tutorial-autoscale-ps) antes de começar.

## <a name="what-is-required-to-rewrite-a-header"></a>O que é necessário para reescrever um cabeçalho

Para configurar a reconfiguração do cabeçalho HTTP, você precisará:

1. Criar os novos objetos necessários para reescrever os cabeçalhos http:

   - **Reescreva a ação**: usado para especificar a solicitação e campos de cabeçalho de solicitação que você pretende reescrever e o novo valor precisam ser reescrito para os cabeçalhos originais. Você pode optar por associar uma ou mais condições de regravação com uma ação de regravação.

   - **Reescreva a condição**: É uma configuração opcional. Se uma condição de regravação é adicionada, ela avaliará o conteúdo das solicitações de HTTP (S) e as respostas. A decisão de executar a ação de regravação associada com a condição de regravação se baseará se a resposta ou solicitação de HTTP (S) de correspondência com a condição de regravação. 

     Se mais de um condições são associados com uma ação e, em seguida, a ação será executada somente quando todas as condições forem atendidas, ou seja, será executada uma operação AND lógica.

   - **Regra de reescrita de**: regra de regravação contém vários ação de regravação - reescrever combinações de condição.

   - **Sequência de regra**: ajuda a determinar a ordem na qual as diferentes regras de reescrita seja executado. Isso é útil quando há várias regras de reescrita em um conjunto de regravação. A regra de reconfiguração com o menor valor de sequência de regra é executada pela primeira vez. Se você fornecer a mesma sequência de regra para duas regras de regravação a ordem de execução será não determinística.

   - **Reescreva o conjunto**: contém várias regras de reconfiguração que serão associadas a uma regra de roteamento de solicitação.

2. Você precisará anexar a reescrita com uma regra de roteamento. Isso ocorre porque a configuração de regravação é anexada ao ouvinte de origem por meio da regra de roteamento. Ao usar uma regra de roteamento básica, a configuração de redirecionamento é associada a um ouvinte de origem e será uma reescrita de cabeçalho global. Quando uma regra de roteamento com base em caminho é usada, a configuração de redirecionamento será definida no mapa de caminho de URL. Então, ela se aplica somente a área de caminho específico de um site.

Você pode criar vários conjuntos de regravação de cabeçalho http e cada conjunto de reconfiguração pode ser aplicado a vários ouvintes. No entanto, você pode aplicar somente um reescrever definido como um ouvinte específico.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no [portal do Azure](https://portal.azure.com/) com sua conta do Azure.

## <a name="configure-header-rewrite"></a>Configurar a reconfiguração de cabeçalho

Neste exemplo, vamos modificar a URL de redirecionamento reescrevendo o cabeçalho location na resposta http enviada pelo aplicativo de back-end. 

1. Selecione **todos os recursos**e, em seguida, selecione seu gateway de aplicativo.

2. Selecione **regrava** no menu à esquerda.

3. Clique em **+ reescrever conjunto**. 

   ![Adicionar conjunto de regravação](media/rewrite-http-headers-portal/add-rewrite-set.png)

4. Forneça o nome para o conjunto de regravação e associá-lo a uma regra de roteamento:

   - Insira o nome a reescrita definido **nome** caixa de texto.
   - Selecione uma ou mais regras listadas na **associados a regras de roteamento** lista. Você só pode selecionar as regras que não foram associadas a outros conjuntos de regravação. As regras que já foram associadas a outros conjuntos de regravação estará esmaecidas.
   - Clique em Avançar.
   
     ![Adicionar nome e a associação](media/rewrite-http-headers-portal/name-and-association.png)

5. Crie uma regra de regravação:

   - Clique em **+ Adicionar regra de regravação**.![ Adicionar regra de regravação](media/rewrite-http-headers-portal/add-rewrite-rule.png)
   - Forneça um nome para a regra de reescrita na caixa de texto de nome de regra de regravação e fornecer uma sequência de regra.![Adicione o nome da regra](media/rewrite-http-headers-portal/rule-name.png)

6. Neste exemplo, podemos irá regravar o cabeçalho location apenas quando ele contém uma referência a "azurewebsites.net". Para fazer isso, adicione uma condição para avaliar se o cabeçalho location na resposta contém azurewebsites.net:

   - Clique em **+ Adicionar condição** e, em seguida, clique na seção com o **se** instruções para expandir o proprietário.![ Adicione o nome da regra](media/rewrite-http-headers-portal/add-condition.png)

   - Selecione **cabeçalho HTTP** da **tipo de variável para verificar** lista suspensa. 

   - Selecione **tipo de cabeçalho** como **resposta**.

   - Como neste exemplo, estamos avaliando o cabeçalho de localização que vem a ser um cabeçalho comuns, selecione **cabeçalho Common** botão de opção como o **nome do cabeçalho**.

   - Selecione **local** da **cabeçalho comuns** lista suspensa.

   - Selecione **nenhuma** como o **diferencia maiusculas de minúsculas** configuração.

   - Selecione **igual (=)** da **operador** lista suspensa.

   - Insira o padrão de expressão regular. Neste exemplo, usaremos o padrão de `(https?):\/\/.*azurewebsites\.net(.*)$` .

   - Clique em **OK**.

     ![Modificar cabeçalho de localização](media/rewrite-http-headers-portal/condition.png)

7. Adicione uma ação para reescrever o cabeçalho de localização:

   - Selecione **definir** como o **tipo de ação**.

   - Selecione **resposta** como o **tipo de cabeçalho**.

   - Selecione **cabeçalho Common** como o **nome do cabeçalho**.

   - Selecione **local** da **cabeçalho comuns** lista suspensa.

   - Insira o valor do cabeçalho. Neste exemplo, usaremos `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` como o valor do cabeçalho. Isso substituirá *azurewebsites.net* com *contoso.com* no cabeçalho location.

   - Clique em **OK**.

     ![Modificar cabeçalho de localização](media/rewrite-http-headers-portal/action.png)

8. Clique em **criar** para criar a reescrita de conjunto.

   ![Modificar cabeçalho de localização](media/rewrite-http-headers-portal/create.png)

9. Você será direcionado para o modo de exibição de conjunto de regravação. Verifique se o conjunto de regravação criado acima está presente na lista de conjuntos de regravação.

   ![Modificar cabeçalho de localização](media/rewrite-http-headers-portal/rewrite-set-list.png)

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a configuração necessária para realizar algumas das comum casos de uso, consulte [cenários de reconfiguração de cabeçalho comum](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers).

   
