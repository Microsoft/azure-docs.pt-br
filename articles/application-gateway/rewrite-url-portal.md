---
title: Regravar a URL e a cadeia de caracteres de consulta com Aplicativo Azure gateway-portal do Azure
description: Saiba como usar o portal do Azure para configurar um gateway de Aplicativo Azure para reescrever a URL e a cadeia de caracteres de consulta
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: how-to
ms.date: 7/16/2020
ms.author: surmb
ms.openlocfilehash: ec58c6f97efdbcb91071bcea98bbbc614833246d
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92215766"
---
# <a name="rewrite-url-with-azure-application-gateway---azure-portal-preview"></a>Regravar URL com Aplicativo Azure gateway-portal do Azure (versão prévia)

Este artigo descreve como usar o portal do Azure para configurar uma instância de [SKU do gateway de aplicativo v2](application-gateway-autoscaling-zone-redundant.md) para regravar a URL.

>[!NOTE]
> O recurso de regravação de URL está em visualização e está disponível somente para Standard_v2 e WAF_v2 SKU do gateway de aplicativo. Não é recomendável para uso no ambiente de produção. Para saber mais sobre visualizações, confira [termos de uso aqui](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="before-you-begin"></a>Antes de começar

Você precisa ter uma instância de SKU do gateway de aplicativo v2 para concluir as etapas neste artigo. Não há suporte para a regravação da URL na SKU v1. Se você não tiver a SKU v2, crie uma instância de [SKU do gateway de aplicativo v2](tutorial-autoscale-ps.md) antes de começar.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no [portal do Azure](https://portal.azure.com/) com sua conta do Azure.

## <a name="configure-url-rewrite"></a>Configurar regeneração de URL

No exemplo abaixo sempre que a URL de solicitação contém */article*, o caminho de URL e a cadeia de caracteres de consulta de URL são regravados

`contoso.com/article/123/fabrikam` -> `contoso.com/article.aspx?id=123&title=fabrikam`

1. Selecione **todos os recursos**e, em seguida, selecione o gateway de aplicativo.

2. Selecione **regravações** no painel esquerdo.

3. Selecione **reescrever conjunto**:

    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-1.png" alt-text="Adicionar conjunto de regravação":::

4. Forneça um nome para o conjunto de regravação e associe-o a uma regra de roteamento:

    a. Insira o nome para a regravação definida na caixa **nome** .
    
    b. Selecione uma ou mais das regras listadas na lista **regras de roteamento associadas** . Isso é usado para associar a configuração de regravação ao ouvinte de origem por meio da regra de roteamento. Você pode selecionar apenas as regras de roteamento que não foram associadas a outros conjuntos de regravação. As regras que já foram associadas a outros conjuntos de regravação estão esmaecidas.
    
    c. Selecione **Avançar**.
    
    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-2.png" alt-text="Adicionar conjunto de regravação":::

5. Criar uma regra de reescrita:

    a. Selecione **Adicionar regra de regravação**.
    
    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-3.png" alt-text="Adicionar conjunto de regravação":::
    
    b. Insira um nome para a regra de reescrita na caixa **reescrever nome da regra** . Insira um número na caixa **sequência de regras** .

6. Neste exemplo, Reescreveremos o caminho da URL e a cadeia de caracteres de consulta de URL somente quando o caminho contiver */article*. Para fazer isso, adicione uma condição para avaliar se o caminho da URL contém */article*

    a. Selecione **Adicionar condição** e, em seguida, selecione a caixa que contém as instruções **If** para expandi-la.
    
    b. Como neste exemplo queremos verificar o padrão */article* no caminho da URL, na lista **tipo de variável a ser verificada** , selecione variável de **servidor**.
    
    c. Na lista **variável de servidor** , selecione uri_path
    
    d. Em **diferenciação de maiúsculas e minúsculas**, selecione **não**.
    
    e. Na lista **operador** , selecione **igual (=)**.
    
    f. Insira um padrão de expressão regular. Neste exemplo, usaremos o padrão `.*article/(.*)/(.*)`
    
      () é usado para capturar a subcadeia de caracteres para uso posterior na composição da expressão para reescrever o caminho da URL. Para saber mais, clique [aqui](rewrite-http-headers-url.md#capturing).

    g. Selecione **OK**.

    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-4.png" alt-text="Adicionar conjunto de regravação":::

 

7. Adicionar uma ação para regravar a URL e o caminho da URL

   a. Na lista **reescrever tipo** , selecione **URL**.

   b. Na lista **tipo de ação** , selecione **definir**.

   c. Em **componentes**, selecione **o caminho da URL e a cadeia de caracteres de consulta da URL**

   d. No **valor do caminho da URL**, insira o novo valor do caminho. Neste exemplo, usaremos **/article.aspx** 

   e. No **valor da cadeia de caracteres de consulta de URL**, insira o novo valor da cadeia de caracteres de consulta de URL. Neste exemplo, usaremos **ID = {var_uri_path_1} &título = {var_uri_path_2}**
    
    `{var_uri_path_1}` e `{var_uri_path_1}` são usados para buscar as subcadeias capturadas ao avaliar a condição nesta expressão `.*article/(.*)/(.*)`
    
   f. Selecione **OK**.

    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-5.png" alt-text="Adicionar conjunto de regravação":::

8. Clique em **criar** para criar o conjunto de regravação.

9. Verifique se o novo conjunto de regravação aparece na lista de conjuntos de regravação

    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-6.png" alt-text="Adicionar conjunto de regravação":::

## <a name="verify-url-rewrite-through-access-logs"></a>Verificar a regravação de URL pelos logs de acesso

Observe os campos abaixo nos logs de acesso para verificar se a reescrita da URL ocorreu de acordo com sua expectativa.

* **originalRequestUriWithArgs**: Este campo contém a URL de solicitação original
* **RequestUri**: Este campo contém a URL após a operação de regravação no gateway de aplicativo

Para obter mais informações sobre todos os campos nos logs de acesso, consulte [aqui](application-gateway-diagnostics.md#for-application-gateway-and-waf-v2-sku).

##  <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como configurar regravações para alguns casos de uso comuns, consulte [cenários comuns de reescrita](rewrite-http-headers.md).
