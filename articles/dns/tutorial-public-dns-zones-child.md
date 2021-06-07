---
title: 'Tutorial: Criando zonas DNS filho do Azure'
titleSuffix: Azure DNS
description: Tutorial sobre como criar zonas DNS filho no portal do Azure.
author: jonbeck
ms.assetid: be4580d7-aa1b-4b6b-89a3-0991c0cda897
ms.service: dns
ms.topic: tutorial
ms.custom: ''
ms.workload: infrastructure-services
ms.date: 7/16/2020
ms.author: jonbeck
ms.openlocfilehash: 1e2eddd821bb7a9d2050913efef3d73b406e32f7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101733203"
---
# <a name="tutorial-creating-a-new-child-dns-zone"></a>Tutorial: Criar uma Zona DNS filho

Neste tutorial, você aprenderá como: 

> [!div class="checklist"]
> * Entrar no portal do Azure.
> * Criar a zona DNS filho por meio de uma nova zona DNS.
> * Criar a zona DNS filho por meio de uma zona DNS pai.
> * Verificar a delegação de NS para a nova zona DNS filho.



## <a name="prerequisites"></a>Pré-requisitos

* Uma conta do Azure com uma assinatura ativa.  Caso não tenha uma conta, você pode [criar uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Zona DNS do Azure pai existente.  

Para fins deste tutorial, usaremos contoso.com como a zona pai e subdomain.contoso.com como o nome de domínio filho.  Substitua *contoso.com* pelo seu nome de domínio pai e *subdomain* pelo seu domínio filho.  Se você ainda não tiver criado a zona DNS pai, confira as etapas para [criar uma zona DNS usando o portal do Azure](./dns-getstarted-portal.md#create-a-dns-zone). 


## <a name="sign-in-to-azure-portal"></a>Entre no portal do Azure

Entre no [portal do Azure](https://portal.azure.com/) com sua conta do Azure.
Caso não tenha uma assinatura do Azure, crie uma conta gratuita antes de começar.

Há duas maneiras de criar a zona DNS filho.
1.  Por meio da página do portal "Criar zona DNS".
1.  Por meio da página de configuração da zona DNS pai.


## <a name="create-child-dns-zone-via-create-dns-zone"></a>Criar a zona DNS filho por meio da página Criar zona DNS

Nesta etapa, criaremos uma zona DNS filho com o nome **subdomain.contoso.com** e a delegaremos à zona DNS pai existente **contoso.com**. Você criará a zona DNS usando as guias na página **Criar zona DNS**.
1.  No menu do portal do Azure ou na **Página Inicial**, selecione **Criar um recurso**. A janela **Novo** é exibida.
1.  Selecione **Rede**, selecione **Zona DNS** e clique no botão **Adicionar**.

1.  Na guia **básico**, digite ou selecione os seguintes valores:
    * **Assinatura**: Selecione uma assinatura na qual a zona será criada.
    * **Grupo de recursos**: Insira seu Grupo de recursos existente ou crie um selecionando **Criar**. Depois, insira *MyResourceGroup* e selecione **OK**. O nome do grupo de recursos deve ser exclusivo na assinatura do Azure.
    * Marque essa caixa de seleção: **Esta zona é um filho de uma zona existente já hospedada no DNS do Azure**
    * **Assinatura da zona pai**: Nesse menu suspenso, pesquise e/ou selecione o nome da assinatura na qual a zona DNS pai *contoso.com* foi criada.
    * **Zona pai**: Na barra de pesquisa digite *contoso.com* para carregá-la na lista suspensa. Depois de carregada, selecione *contoso.com* na lista suspensa.
    * **Nome:** Digite *subdomain* para este exemplo do tutorial. Observe que o nome da zona DNS pai *contoso.com* é adicionado automaticamente como sufixo ao nome quando selecionamos a zona pai na etapa acima.

1. Selecione **Avançar: Revisar + criar**.
1. Na guia **Examinar + criar**, examine o resumo, corrija os erros de validação e selecione **Criar**.
A criação da zona pode levar alguns minutos.

 
    :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-dns-zone-inline.png" alt-text="Captura de tela da página Criar zona DNS." lightbox="./media/dns-delegate-domain-azure-dns/create-dns-zone-expanded.png":::

## <a name="create-child-dns-zone-via-parent-dns-zone-overview-page"></a>Criar a zona DNS filho por meio da página de visão geral da zona DNS pai
Você também pode criar uma zona DNS filho e delegá-la na zona DNS pai usando o botão **Zona Filho** na página de visão geral da zona pai. O uso desse botão ocasiona o preenchimento automático prévio dos parâmetros do pai para a zona filho. 

1.  No portal do Azure, em **Todos os recursos**, abra a zona DNS *contoso.com* no grupo de recursos **MyResourceGroup**. Insira *contoso.com* na caixa **Filtrar por nome** para encontrá-la mais facilmente.
1.  Na página de visão geral da zona DNS, selecione o botão **+ Zona Filho**.

      :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-child-zone-inline.png" alt-text="Captura de tela do botão Zona Filho." border="true" lightbox="./media/dns-delegate-domain-azure-dns/create-child-zone-expanded.png":::

1.  A página Criar zona DNS será aberta. A opção de zona filho já está marcada e a assinatura da zona pai e a zona pai já estão preenchidas para você nesta página.
1.  Digite o nome como *child* para o exemplo deste tutorial. Observe que o nome da zona DNS pai, contoso.com, é adicionado automaticamente como prefixo ao nome.
1.  Selecione **Avançar: Marcas** e, em seguida, **Avançar: Revisar + criar**.
1.  Na guia **Examinar + criar**, examine o resumo, corrija os erros de validação e selecione **Criar**.

    :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-dns-zone-child-inline.png" alt-text="Captura de tela da zona filho selecionada" border="true" lightbox="./media/dns-delegate-domain-azure-dns/create-dns-zone-child-expanded.png":::

## <a name="verify-child-dns-zone"></a>Verificar zona DNS filho
Agora você criou uma zona DNS filho *subdomain.contoso.com*. Para verificar se a delegação ocorreu corretamente, verifique se os registros NS (nameserver) da zona filho estão na zona pai, conforme descrito abaixo.  

**Recuperar servidores de nomes da zona DNS filho:**

1.  No portal do Azure, em **Todos os recursos**, abra a zona DNS *subdomain.contoso.com* no grupo de recursos **MyResourceGroup**. Insira *subdomain.contoso.com* na caixa **Filtrar por nome** para encontrá-la mais facilmente.
1.  Recupere os servidores de nomes na página de visão geral da zona DNS. Neste exemplo, a zona contoso.com foi atribuída aos servidores de nomes *ns1-08.azure-dns.com, ns2-08.azure-dns.net, ns3-08.azure-dns.org* e *ns4-08.azure-dns.info*:

      :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-child-zone-ns-inline.png" alt-text="Captura de tela dos nameservers da zona filho" border="true" lightbox="./media/dns-delegate-domain-azure-dns/create-child-zone-ns-expanded.png":::
**Verifique o registro NS na zona DNS pai:**

Agora, nesta etapa, vamos até a zona DNS pai *contoso.com* e verificamos se a entrada do conjunto de registros NS dos nameservers das zonas filho foi criada.

1. No portal do Azure, em **Todos os recursos**, abra a zona DNS contoso.com no grupo de recursos **MyResourceGroup**. Insira contoso.com na caixa **Filtrar por nome** para encontrá-la mais facilmente.
1.  Na página de visão geral das zonas DNS do *contoso.com*, verifique os conjuntos de registros.
1.  Você verá que o conjunto de registros do tipo NS e o nome subdomain já foi criado na zona DNS pai. Verifique se os valores deste conjunto de registros é semelhante à lista de nameserver que recuperamos da zona DNS filho na etapa acima.

     :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-child-zone-ns-validate-inline.png" alt-text="Captura de tela da validação dos nameservers da zona filho" border="true" lightbox="./media/dns-delegate-domain-azure-dns/create-child-zone-ns-validate-expanded.png":::
## <a name="clean-up-resources"></a>Limpar os recursos
Quando você não precisar mais dos recursos criados neste tutorial, remova-os excluindo o grupo de recursos **MyResourceGroup**. Abra o grupo de recursos **MyResourceGroup** e selecione **Excluir grupo de recursos**.



## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Cenários de Zonas Privadas do DNS do Azure](private-dns-scenarios.md)