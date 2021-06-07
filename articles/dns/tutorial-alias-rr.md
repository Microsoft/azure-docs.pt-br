---
title: 'Tutorial: Criar um registro de alias para se referir a um registro de recurso em uma zona'
titleSuffix: Azure DNS
description: Este tutorial mostra como configurar um registro de alias do DNS do Azure para fazer referência a um registro de recurso na zona.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: tutorial
ms.date: 04/19/2021
ms.author: rohink
ms.openlocfilehash: c20f079fdbccdf003d96a1b2683060c2cd6d0e5a
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107737362"
---
# <a name="tutorial-create-an-alias-record-to-refer-to-a-zone-resource-record"></a>Tutorial: Criar um registro de alias para se referir a um registro de recurso de zona

Registros de alias podem referenciar outros conjuntos de registros do mesmo tipo. Por exemplo, você pode ter um conjunto de registros DNS CNAME como um alias para outro conjunto de registros CNAME do mesmo tipo. Essa funcionalidade é útil se você quer ter alguns conjuntos de registros como aliases e alguns como não aliases em termos de comportamento.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um registro de alias para um registro de recurso na zona.
> * Testar o registro de alias.


Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos
Você deve ter um nome de domínio disponível para hospedar no DNS do Azure para testar. Você deve ter controle total sobre esse domínio. Controle total inclui a capacidade de definir os registros NS (nomes de servidor) para o domínio.

Para obter instruções para hospedar seu domínio no DNS do Azure, confira o [Tutorial: Hospede seu domínio no DNS do Azure](dns-delegate-domain-azure-dns.md).


## <a name="create-an-alias-record"></a>Criar um registro de alias

Crie um registro de alias que aponte para um registro de recurso na zona.

### <a name="create-the-target-resource-record"></a>Criar o registro de recurso de destino
1. Escolha sua zona DNS do Azure para abri-la.
2. Escolha **Conjunto de registros**.
3. Na caixa de texto **Nome**, insira **servidor**.
4. Para o **tipo**, selecione **um**.
5. Na caixa de texto **ENDEREÇO IP**, digite **10.10.10.10**.
6. Selecione **OK**.

### <a name="create-the-alias-record"></a>Crie o registro de alias
1. Escolha sua zona DNS do Azure para abri-la.
2. Escolha **Conjunto de registros**.
3. Na caixa de texto **Nome**, insira **teste**.
4. Para o **tipo**, selecione **um**.
5. Marque **Sim** na caixa de seleção **Conjunto de Registros do Alias**. Em seguida, selecione a opção **Conjunto de registros da zona**.
6. Para o **conjunto de registros da zona**, selecione o **server** registro.
7. Selecione **OK**.

## <a name="test-the-alias-record"></a>Testar o registro de alias

1. Inicie sua ferramenta nslookup favorita. Navegue até [https://network-tools.com/nslook](https://network-tools.com/nslook).
2. Defina o tipo de consulta para os registros A e pesquise **test.\<your domain name\>** . A resposta é **10.10.10.10**.
3. No portal do Azure, altere o **server** um registro **10.11.11.11**.
4. Aguarde alguns minutos e use o nslookup novamente para o registro de **teste**. A resposta é **10.11.11.11**.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando você não precisar mais dos recursos criados para este tutorial, exclua os registros de recurso **servidor** e **teste** em sua zona.


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou um registro de alias para referenciar um registro de recurso dentro da zona. Para saber mais sobre os aplicativos Web e o DNS do Azure, continue com o tutorial para aplicativos Web.

> [!div class="nextstepaction"]
> [Criar registros DNS para um aplicativo Web em um domínio personalizado](./dns-web-sites-custom-domain.md)
