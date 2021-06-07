---
title: 'Tutorial: Adicionar um domínio personalizado ao ponto de extremidade'
titleSuffix: Azure Content Delivery Network
description: Use este tutorial para adicionar um domínio personalizado a um ponto de extremidade da Rede de Distribuição de Conteúdo do Azure para que seu nome de domínio esteja visível na URL.
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.topic: tutorial
ms.date: 02/04/2020
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 9bca81e7d0eb68d43c21065f5104fc423115be25
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106065181"
---
# <a name="tutorial-add-a-custom-domain-to-your-endpoint"></a>Tutorial: Adicionar um domínio personalizado ao ponto de extremidade

Este tutorial mostra como adicionar um domínio personalizado a um ponto de extremidade do Azure CDN (Rede de Distribuição de Conteúdo). 

O nome do ponto de extremidade em seu perfil CDN será um subdomínio de azureedge.net. Por padrão, ao fornecer um conteúdo, o domínio do perfil CDN será incluído na URL.

Por exemplo, **https://contoso.azureedge.net/photo.png**.

A CDN do Azure fornece a opção de associar um domínio personalizado a um ponto de extremidade CDN. Essa opção fornece conteúdo com um domínio personalizado na URL em vez do domínio padrão.

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> - Criar um registro DNS CNAME.
> - Associar o domínio personalizado ao seu ponto de extremidade da CDN.
> - Verificar o domínio personalizado.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Para concluir as etapas deste tutorial, crie um perfil CDN e no mínimo um ponto de extremidade CDN. 
    * Para saber mais, confira [Início Rápido: Criar um ponto de extremidade e um perfil de CDN do Azure](cdn-create-new-endpoint.md).

* Caso ainda não tenha um domínio personalizado, adquira um com o provedor de domínio. 
    * Para obter mais informações, confira como [Comprar um nome de domínio personalizado](../app-service/manage-custom-dns-buy-domain.md).

* Caso esteja usando o Azure para hospedar [Domínios DNS](../dns/dns-overview.md), delegue seu domínio personalizado ao DNS do Azure. 
    * Confira [Delegar um domínio ao DNS do Azure](../dns/dns-delegate-domain-azure-dns.md)para saber mais.

* Caso esteja usando um provedor de domínio para lidar com o domínio DNS, prossiga para a opção [Criar um registro DNS CNAME](#create-a-cname-dns-record).


## <a name="create-a-cname-dns-record"></a>Criar um registro DNS CNAME

Para usar um domínio personalizado com um ponto de extremidade CDN do Azure, primeiro crie um registro CNAME (nome canônico) com o DNS do Azure ou seu provedor DNS para apontar para o ponto de extremidade CDN. 

Um registro CNAME é um registro DNS que mapeia um nome de domínio de origem para um nome de domínio de destino. 

Para a CDN do Azure, o nome de domínio de origem é o seu nome de domínio personalizado e o nome de domínio de destino é nome do host do ponto de extremidade da CDN. 

O CDN do Azure roteia o tráfego endereçado ao domínio de origem personalizado para o nome de host do ponto de extremidade CDN de destino depois de verificar o registro CNAME.

Um domínio personalizado e seu respectivo subdomínio podem ser associados a somente um ponto de extremidade por vez. 

Use vários registros CNAME para diferentes subdomínios do mesmo domínio personalizado para diferentes serviços do Azure.

É possível mapear um domínio personalizado com subdomínios diferentes para o mesmo ponto de extremidade CDN.

> [!NOTE]
> Este tutorial usará o tipo de registro CNAME. Se você estiver usando os tipos de registro A ou AAAA, siga as mesmas etapas abaixo e substitua CNAME pelo tipo de registro de sua escolha.

---
# <a name="azure-dns"></a>[**DNS do Azure**](#tab/azure-dns)

O DNS do Azure usa registros de alias para recursos do Azure na mesma assinatura.

Para adicionar um registro de alias do ponto de extremidade CDN do Azure:

1. Entre no [portal do Azure](https://portal.azure.com).

2. No menu à esquerda, selecione **Todos os recursos** e a zona DNS do Azure para o domínio personalizado.

3. Na zona DNS do domínio personalizado, selecione **+ Conjunto de registros**.

4. Em **Adicionar conjunto de registros** insira ou selecione as seguintes informações:

    | Configuração | Valor |
    | ------- | ----- |
    | Nome  | Insira o alias que você deseja usar para o ponto de extremidade CDN. Por exemplo, **www**. |
    | Tipo  | Clique em **CNAME**. |
    | Conjunto de registros de alias | Selecione **Sim**. |
    | Tipo de alias | Clique em **Recurso do Azure**. |
    | Escolha uma assinatura | Selecione sua assinatura. |
    | Recursos do Azure | Selecione o ponto de extremidade CDN do Azure. |

5. Altere o **TTL** do registro e substitua por seu valor.

6. Selecione **OK**.

# <a name="dns-provider"></a>[**Provedor DNS**](#tab/dns-provider)

## <a name="map-the-temporary-cdnverify-subdomain"></a>Mapear o subdomínio cdnverify temporário

Quando você mapeia um domínio existente que está na produção, deve considerar algumas questões. 

Um breve período de tempo de inatividade do domínio poderá ocorrer ao registrar seu domínio personalizado no portal do Azure.

Para evitar a interrupção do tráfego da Web, mapeie o domínio personalizado para usar o nome de host do ponto de extremidade CDN com o subdomínio do Azure, chamado **cdnverify**. Esse processo criará um mapeamento CNAME temporário. 

Com esse método, os usuários podem acessar seu domínio sem interrupção durante o mapeamento de DNS. 

Caso as considerações de tempo de inatividade de produção não sejam um problema, será possível mapear o domínio personalizado diretamente para o ponto de extremidade CDN. Prossiga para a opção [Mapear um domínio personalizado de modo permanente](#map-the-permanent-custom-domain).

Para criar um registro CNAME com o subdomínio cdnverify:

1. Entre no site do provedor DNS do domínio personalizado.

2. Crie uma entrada de registro CNAME para seu domínio personalizado e preencha os campos conforme mostrado na seguinte tabela (os nomes dos campos podem variar):

    | Fonte                    | Type  | Destino                     |
    |---------------------------|-------|---------------------------------|
    | cdnverify. www.contoso.com | CNAME | cdnverify.contoso.azureedge.net |

    - Origem: Insira o nome de domínio personalizado, incluindo o subdomínio cdnverify, no seguinte formato: **cdnverify.\<custom-domain-name>**
        - Por exemplo: **cdnverify. www.contoso.com**

    - Tipo: insira ou selecione **CNAME**.

    - Destino: insira o nome de host do ponto de extremidade CDN, incluindo o subdomínio cdnverify, no seguinte formato: **cdnverify.\<endpoint-name>.azureedge.net**. 
        - Por exemplo: **cdnverify.contoso.azureedge.net**

3. Salve suas alterações.

## <a name="map-the-permanent-custom-domain"></a>Mapear o domínio personalizado permanente

Nesta seção, mapeie o domínio personalizado de modo permanente do ponto de extremidade CDN. 

Para criar um registro CNAME para seu domínio personalizado:

1. Entre no site do provedor de domínio relativo ao seu domínio personalizado.

2. Crie uma entrada de registro CNAME para seu domínio personalizado e preencha os campos conforme mostrado na seguinte tabela (os nomes dos campos podem variar):

    | Fonte          | Type  | Destino           |
    |-----------------|-------|-----------------------|
    | www.contoso.com | CNAME | contoso.azureedge.net |

    - Origem: insira o nome de domínio personalizado.
        - Por exemplo: **www.contoso.com**

    - Tipo: insira ou selecione **CNAME**.

    - Destino: insira o nome de host do ponto de extremidade CDN no seguinte formato: **\<endpoint-name>.azureedge.net**. 
        - Por exemplo: **contoso.azureedge.net**

3. Salve suas alterações.

4. Se você criou um registro CNAME para o subdomínio cdnverify temporário anteriormente, exclua-o. 

5. Caso esteja usando esse domínio personalizado na produção pela primeira vez, siga as etapas abaixo para [Associar o domínio personalizado ao ponto de extremidade CDN](#associate-the-custom-domain-with-your-cdn-endpoint) e [Verificar o domínio personalizado](#verify-the-custom-domain).

---
## <a name="associate-the-custom-domain-with-your-cdn-endpoint"></a>Associar o domínio personalizado ao seu ponto de extremidade da CDN

Depois de registrar seu domínio personalizado, adicione-o ao ponto de extremidade da CDN. 


---
# <a name="azure-portal"></a>[**Portal do Azure**](#tab/azure-portal)

1. Entre no [portal do Azure](https://portal.azure.com/) e navegue até o perfil CDN contendo o ponto de extremidade que você deseja mapear para um domínio personalizado.
    
2. Na página **Perfil CDN**, selecione o ponto de extremidade da CDN a ser associado ao domínio personalizado.

    :::image type="content" source="media/cdn-map-content-to-custom-domain/cdn-endpoint-selection.png" alt-text="Seleção do ponto de extremidade CDN" border="true":::
    
3. Selecione **+Domínio personalizado**. 

   :::image type="content" source="media/cdn-map-content-to-custom-domain/cdn-custom-domain-button.png" alt-text="Botão Adicionar domínio personalizado" border="true":::

4. Em **Adicionar um domínio personalizado**, o **Nome do host do ponto de extremidade** é preenchido previamente e será derivado da URL do ponto de extremidade CDN: **\<endpoint-hostname>** .azureedge.net. Ele não pode ser alterado.

5. Para o **Nome de host personalizado**, insira seu domínio personalizado, incluindo o subdomínio, para usar como o domínio de origem de seu registro CNAME. 
    1. Por exemplo: **www.contoso.com** ou **cdn.contoso.com**. **Não use o nome do subdomínio cdnverify**.

    :::image type="content" source="media/cdn-map-content-to-custom-domain/cdn-add-custom-domain.png" alt-text="Adicionar domínio personalizado" border="true":::

6. Selecione **Adicionar**.

   O Azure verifica se o registro CNAME existe para o nome de domínio personalizado digitado. Se o CNAME estiver correto, seu domínio personalizado será validado. 

   Pode levar algum tempo para que as novas configurações de domínio personalizado sejam propagadas para todos os nós de borda da CDN: 
    - Para perfis da **CDN Standard do Azure da Microsoft**, a propagação geralmente é concluída em dez minutos. 
    - Para perfis da **CDN Standard do Azure da Akamai**, a propagação normalmente é concluída em um minuto. 
    - Para perfis da **CDN Standard do Azure da Verizon** e da **CDN Premium do Azure da Verizon**, a propagação geralmente é concluída em 10 minutos.   

# <a name="powershell"></a>[**PowerShell**](#tab/azure-powershell)

1. Entre no Azure PowerShell:

```azurepowershell-interactive
    Connect-AzAccount

```
2. Use [New-AzCdnCustomDomain](/powershell/module/az.cdn/new-azcdncustomdomain) para mapear o domínio personalizado para o ponto de extremidade da CDN. 

    * Substitua **myendpoint8675.azureedge.net** pela URL do ponto de extremidade.
    * Substitua **myendpoint8675** pelo nome do ponto de extremidade da CDN.
    * Substitua **www.contoso.com** pelo nome de domínio personalizado.
    * Substitua **myCDN** pelo nome do perfil da CDN.
    * Substitua **myResourceGroupCDN** pelo nome do grupo de recursos.

```azurepowershell-interactive
    $parameters = @{
        Hostname = 'myendpoint8675.azureedge.net'
        EndPointName = 'myendpoint8675'
        CustomDomainName = 'www.contoso.com'
        ProfileName = 'myCDN'
        ResourceGroupName = 'myResourceGroupCDN'
    }
    New-AzCdnCustomDomain @parameters
```

O Azure verifica se o registro CNAME existe para o nome de domínio personalizado digitado. Se o CNAME estiver correto, seu domínio personalizado será validado. 

   Pode levar algum tempo para que as novas configurações de domínio personalizado sejam propagadas para todos os nós de borda da CDN: 

- Para perfis da **CDN Standard do Azure da Microsoft**, a propagação geralmente é concluída em dez minutos. 
- Para perfis da **CDN Standard do Azure da Akamai**, a propagação normalmente é concluída em um minuto. 
- Para perfis da **CDN Standard do Azure da Verizon** e da **CDN Premium do Azure da Verizon**, a propagação geralmente é concluída em 10 minutos.   


---
## <a name="verify-the-custom-domain"></a>Verificar o domínio personalizado

Verifique se o domínio personalizado faz referência ao ponto de extremidade CDN após concluir o registro dele.
 
1. Verifique se você tem conteúdo público armazenado em cache no ponto de extremidade. Por exemplo, se o ponto de extremidade da CDN estiver associado a uma conta de armazenamento, a CDN do Azure armazenará conteúdo em cache em um contêiner público. Configure o contêiner para permitir o acesso público. Além disso, ele deverá conter no mínimo um arquivo para testar o domínio personalizado.

2. Em seu navegador, navegue até o endereço do arquivo usando o domínio personalizado. Por exemplo, se o seu domínio personalizado for `www.contoso.com`, a URL para o arquivo armazenado em cache deverá ser semelhante à seguinte URL: `http://www.contoso.com/my-public-container/my-file.jpg`. Verifique se o resultado é igual ao acessar o ponto de extremidade CDN diretamente em **\<endpoint-hostname>** .azureedge.net.

## <a name="clean-up-resources"></a>Limpar recursos

---
# <a name="azure-portal"></a>[**Portal do Azure**](#tab/azure-portal-cleanup)

Remova o domínio personalizado, caso não queira mais associá-lo ao ponto de extremidade, executando as seguintes etapas:
 
1. No seu perfil CDN, selecione o ponto de extremidade com o domínio personalizado que você deseja remover.

2. Na página **Ponto de Extremidade**, em Domínios personalizados, clique com o botão direito do mouse no domínio personalizado que você deseja remover e selecione **Excluir** no menu de contexto. Selecione **Sim**.

   O domínio personalizado é desassociado do ponto de extremidade.

# <a name="powershell"></a>[**PowerShell**](#tab/azure-powershell-cleanup)

Remova o domínio personalizado, caso não queira mais associá-lo ao ponto de extremidade, executando as seguintes etapas:

1. Use [remove-AzCdnCustomDomain](/powershell/module/az.cdn/remove-azcdncustomdomain) para remover o domínio personalizado do ponto de extremidade:

    * Substitua **myendpoint8675** pelo nome do ponto de extremidade da CDN.
    * Substitua **www.contoso.com** pelo nome de domínio personalizado.
    * Substitua **myCDN** pelo nome do perfil da CDN.
    * Substitua **myResourceGroupCDN** pelo nome do grupo de recursos.


```azurepowershell-interactive
    $parameters = @{
        CustomDomainName = 'www.contoso.com'
        EndPointName = 'myendpoint8675'
        ProfileName = 'myCDN'
        ResourceGroupName = 'myResourceGroupCDN'
    }
    Remove-AzCdnCustomDomain @parameters
```

---
## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> - Criar um registro DNS CNAME.
> - Associar o domínio personalizado ao seu ponto de extremidade da CDN.
> - Verificar o domínio personalizado.

Avance para o próximo tutorial a fim de aprender a configurar HTTPS em um domínio personalizado da CDN do Azure.

> [!div class="nextstepaction"]
> [Tutorial: Configurar HTTPS em um domínio personalizado da CDN do Azure](cdn-custom-ssl.md)