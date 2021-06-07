---
title: Cache do Azure para Redis com o link privado do Azure (versão prévia)
description: O ponto de extremidade privado do Azure é uma interface de rede que conecta você de forma privada e segura ao cache do Azure para Redis da plataforma Azure link privado. Neste artigo, você aprenderá a criar um cache do Azure, uma rede virtual do Azure e um ponto de extremidade privado usando o portal do Azure.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: 22bdf93e7236ae5220a6bb7c6ead898628bb51a1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97007578"
---
# <a name="azure-cache-for-redis-with-azure-private-link-public-preview"></a>Cache do Azure para Redis com o link privado do Azure (visualização pública)
Neste artigo, você aprenderá a criar uma rede virtual e um cache do Azure para a instância Redis com um ponto de extremidade privado usando o portal do Azure. Você também aprenderá a adicionar um ponto de extremidade privado a um cache do Azure existente para a instância do Redis.

O ponto de extremidade privado do Azure é uma interface de rede que conecta você de forma privada e segura ao cache do Azure para Redis da plataforma Azure link privado. 

## <a name="prerequisites"></a>Pré-requisitos
* Assinatura do Azure - [criar uma gratuitamente](https://azure.microsoft.com/free/)

> [!IMPORTANT]
> Para usar pontos de extremidade privados, seu cache do Azure para instância Redis precisa ter sido criado após 28 de julho de 2020.
> Atualmente, a replicação geográfica, as regras de firewall, o suporte ao console do portal, a vários pontos de extremidade por cache clusterizado, a persistência para os caches injetados por firewall e VNet não têm suporte. 
>
>

## <a name="create-a-private-endpoint-with-a-new-azure-cache-for-redis-instance"></a>Criar um ponto de extremidade privado com um novo cache do Azure para instância Redis 

Nesta seção, você criará um novo cache do Azure para a instância Redis com um ponto de extremidade privado.

### <a name="create-a-virtual-network"></a>Criar uma rede virtual 

1. Entre no [portal do Azure](https://portal.azure.com) e selecione **Criar um recurso**.

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="Selecione criar um recurso.":::

2. Na página **novo** , selecione **rede** e, em seguida, selecione **rede virtual**.

3. Selecione **Adicionar** para criar uma rede virtual.

4. Em **Criar rede virtual**, insira ou selecione estas informações na guia **Básico**:

   | Configuração      | Valor sugerido  | Descrição |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Assinatura** | Clique na lista suspensa e selecione sua assinatura. | A assinatura sob a qual criar essa rede virtual. | 
   | **Grupo de recursos** | Clique na lista suspensa e selecione um grupo de recursos ou selecione **Criar** e insira um novo nome de grupo de recursos. | Nome do grupo de recursos no qual criar sua rede virtual e outros recursos. Ao colocar todos os seus recursos de aplicativos em um só grupo de recursos, você pode gerenciá-los ou excluí-los juntos com facilidade. | 
   | **Nome** | Insira um nome de rede virtual. | O nome deve começar com uma letra ou número, terminar com uma letra, número ou sublinhado e pode conter apenas letras, números, sublinhados, pontos ou hifens. | 
   | **Região** | Na lista suspensa e selecione uma região. | Selecione uma [região](https://azure.microsoft.com/regions/) perto de outros serviços que usarão sua rede virtual. |

5. Selecione a guia **endereços IP** ou clique no botão **Avançar: endereços IP** na parte inferior da página.

6. Na guia **endereços IP** , especifique o **espaço de endereço IPv4** como um ou mais prefixos de endereço na notação CIDR (por exemplo, 192.168.1.0/24).

7. Em **nome da sub-rede**, clique em **padrão** para editar as propriedades da sub-rede.

8. No painel **Editar sub-rede** , especifique um **nome de sub-rede** , bem como o **intervalo de endereços de sub-rede**. O intervalo de endereços da sub-rede deve estar na notação CIDR (por exemplo, 192.168.1.0/24). Ele deve estar contido no espaço de endereço da rede virtual.

9. Clique em **Salvar**.

10. Selecione a guia **revisar + criar** ou clique no botão **revisar + criar** .

11. Verifique se todas as informações estão corretas e clique em **criar** para provisionar a rede virtual.

### <a name="create-an-azure-cache-for-redis-instance-with-a-private-endpoint"></a>Criar um cache do Azure para a instância Redis com um ponto de extremidade privado
Para criar uma instância de cache, siga estas etapas.

1. Volte para a home page do portal do Azure ou abra o menu da barra lateral e, em seguida, selecione **criar um recurso**. 
   
1. Na página **Novo**, selecione **Bancos de dados** e, em seguida, **Cache do Azure para Redis**.

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="Selecionar o Cache do Azure para Redis.":::
   
1. Na página **Novo Cache Redis**, defina as configurações para o novo cache.
   
   | Configuração      | Valor sugerido  | DESCRIÇÃO |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Nome DNS** | Insira um nome global exclusivo. | O nome de cache precisa ser uma cadeia de caracteres com 1 a 63 caracteres que contém somente números, letras ou hifens. O nome precisa começar e terminar com um número ou uma letra e não pode conter hifens consecutivos. O *nome do host* de sua instância de cache será *\<DNS name>.redis.cache.windows.net*. | 
   | **Assinatura** | Clique na lista suspensa e selecione sua assinatura. | A assinatura na qual essa nova instância do Cache do Azure para Redis será criada. | 
   | **Grupo de recursos** | Clique na lista suspensa e selecione um grupo de recursos ou selecione **Criar** e insira um novo nome de grupo de recursos. | Nome do grupo de recursos no qual o cache e outros recursos serão criados. Ao colocar todos os seus recursos de aplicativos em um só grupo de recursos, você pode gerenciá-los ou excluí-los juntos com facilidade. | 
   | **Localidade** | Clique na lista suspensa e selecione uma localização. | Selecione uma [região](https://azure.microsoft.com/regions/) perto de outros serviços que usarão o cache. |
   | **Tipo de preços** | Clique na lista suspensa e selecione um [Tipo de preço](https://azure.microsoft.com/pricing/details/cache/). |  O tipo de preço determina o tamanho, o desempenho e os recursos disponíveis para o cache. Para obter mais informações, confira [Visão geral do Cache do Azure para Redis](cache-overview.md). |

1. Selecione a guia **Rede** ou clique no botão **Rede** na parte inferior da página.

1. Na guia **rede** , selecione **ponto de extremidade privado** para o método de conectividade.

1. Clique no botão **Adicionar** para criar seu ponto de extremidade privado.

    :::image type="content" source="media/cache-private-link/3-add-private-endpoint.png" alt-text="Em rede, adicione um ponto de extremidade privado.":::

1. Na página **criar um ponto de extremidade privado** , defina as configurações para seu ponto de extremidade privado com a rede virtual e a sub-rede que você criou na última seção e selecione **OK**. 

1. Selecione **Próximo: Avançado** ou clique no botão **Próximo: Avançado** na parte inferior da página.

1. Na guia **Avançado** de uma instância de cache Básico ou Standard, selecione a alternância Habilitar se desejar habilitar uma porta não TLS.

1. Na guia **Avançado** de uma instância de cache Premium, defina as configurações da porta não TLS, do clustering e da persistência de dados.

1. Selecione **Próximo: Marcas** ou clique no botão **Próximo: Botão** Categorias na parte inferior da página.

1. Opcionalmente, na guia **Marcas**, insira o nome e o valor caso deseje categorizar o recurso. 

1. Selecione **Examinar + criar**. Você será levado para a guia Examinar + criar, na qual o Azure validará a configuração.

1. Depois que a mensagem em verde Validação aprovada for exibida, selecione **Criar**.

A criação do cache demora um pouco. Monitore o progresso na página **Visão Geral** do Cache do Azure para Redis. Quando o **Status** for mostrado como **Em execução**, o cache estará pronto para uso. 
    
> [!IMPORTANT]
> 
> Há um `publicNetworkAccess` sinalizador que é `Disabled` por padrão. 
> Esse sinalizador destina-se a permitir que você opcionalmente permita o acesso de ponto de extremidade público e privado ao cache se ele estiver definido como `Enabled` . Se definido como `Disabled` , ele só permitirá acesso de ponto de extremidade privado. Você pode definir o valor para `Disabled` ou `Enabled` com a solicitação de patch a seguir. Edite o valor para refletir qual sinalizador você deseja para o seu cache.
> ```http
> PATCH  https://management.azure.com/subscriptions/{subscription}/resourceGroups/{resourcegroup}/providers/Microsoft.Cache/Redis/{cache}?api-version=2020-06-01
> {    "properties": {
>        "publicNetworkAccess":"Disabled"
>    }
> }
> ```
>

> [!IMPORTANT]
> 
> Para se conectar a um cache clusterizado, é `publicNetworkAccess` necessário definir como `Disabled` e só pode haver uma conexão de ponto de extremidade privada. 
>

## <a name="create-a-private-endpoint-with-an-existing-azure-cache-for-redis-instance"></a>Criar um ponto de extremidade privado com um cache do Azure existente para a instância do Redis 

Nesta seção, você adicionará um ponto de extremidade privado a um cache do Azure existente para a instância do Redis. 

### <a name="create-a-virtual-network"></a>Criar uma rede virtual 
Para criar uma rede virtual, siga estas etapas.

1. Entre no [portal do Azure](https://portal.azure.com) e selecione **Criar um recurso**.

2. Na página **novo** , selecione **rede** e, em seguida, selecione **rede virtual**.

3. Selecione **Adicionar** para criar uma rede virtual.

4. Em **Criar rede virtual**, insira ou selecione estas informações na guia **Básico**:

   | Configuração      | Valor sugerido  | Descrição |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Assinatura** | Clique na lista suspensa e selecione sua assinatura. | A assinatura sob a qual criar essa rede virtual. | 
   | **Grupo de recursos** | Clique na lista suspensa e selecione um grupo de recursos ou selecione **Criar** e insira um novo nome de grupo de recursos. | Nome do grupo de recursos no qual criar sua rede virtual e outros recursos. Ao colocar todos os seus recursos de aplicativos em um só grupo de recursos, você pode gerenciá-los ou excluí-los juntos com facilidade. | 
   | **Nome** | Insira um nome de rede virtual. | O nome deve começar com uma letra ou número, terminar com uma letra, número ou sublinhado e pode conter apenas letras, números, sublinhados, pontos ou hifens. | 
   | **Região** | Na lista suspensa e selecione uma região. | Selecione uma [região](https://azure.microsoft.com/regions/) perto de outros serviços que usarão sua rede virtual. |

5. Selecione a guia **endereços IP** ou clique no botão **Avançar: endereços IP** na parte inferior da página.

6. Na guia **endereços IP** , especifique o **espaço de endereço IPv4** como um ou mais prefixos de endereço na notação CIDR (por exemplo, 192.168.1.0/24).

7. Em **nome da sub-rede**, clique em **padrão** para editar as propriedades da sub-rede.

8. No painel **Editar sub-rede** , especifique um **nome de sub-rede** , bem como o **intervalo de endereços de sub-rede**. O intervalo de endereços da sub-rede deve estar na notação CIDR (por exemplo, 192.168.1.0/24). Ele deve estar contido no espaço de endereço da rede virtual.

9. Clique em **Salvar**.

10. Selecione a guia **revisar + criar** ou clique no botão **revisar + criar** .

11. Verifique se todas as informações estão corretas e clique em **criar** para provisionar a rede virtual.

### <a name="create-a-private-endpoint"></a>Criar um ponto de extremidade privado 

Para criar um ponto de extremidade privado, siga estas etapas.

1. Na portal do Azure, procure o **cache do Azure para Redis** e pressione Enter ou selecione-o nas sugestões de pesquisa.

    :::image type="content" source="media/cache-private-link/4-search-for-cache.png" alt-text="Pesquise o cache do Azure para Redis.":::

2. Selecione a instância de cache à qual você deseja adicionar um ponto de extremidade privado.

3. No lado esquerdo da tela, selecione (versão **prévia) ponto de extremidade privado**.

4. Clique no botão **ponto de extremidade privado** para criar seu ponto de extremidade privado.

    :::image type="content" source="media/cache-private-link/5-add-private-endpoint.png" alt-text="Adicionar ponto de extremidade privado.":::

5. Na **página criar um ponto de extremidade privado**, defina as configurações para seu ponto de extremidade privado.

   | Configuração      | Valor sugerido  | Descrição |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Assinatura** | Clique na lista suspensa e selecione sua assinatura. | A assinatura sob a qual criar esse ponto de extremidade privado. | 
   | **Grupo de recursos** | Clique na lista suspensa e selecione um grupo de recursos ou selecione **Criar** e insira um novo nome de grupo de recursos. | Nome do grupo de recursos no qual criar seu ponto de extremidade privado e outros recursos. Ao colocar todos os seus recursos de aplicativos em um só grupo de recursos, você pode gerenciá-los ou excluí-los juntos com facilidade. | 
   | **Nome** | Insira um nome de ponto de extremidade privado. | O nome deve começar com uma letra ou número, terminar com uma letra, número ou sublinhado e pode conter apenas letras, números, sublinhados, pontos ou hifens. | 
   | **Região** | Na lista suspensa e selecione uma região. | Selecione uma [região](https://azure.microsoft.com/regions/) perto de outros serviços que usarão seu ponto de extremidade privado. |

6. Clique no botão **Avançar: recurso** na parte inferior da página.

7. Na guia **recurso** , selecione sua assinatura, escolha o tipo de recurso como `Microsoft.Cache/Redis` e, em seguida, selecione o cache ao qual você deseja conectar o ponto de extremidade privado.

8. Clique no botão **próximo: configuração** na parte inferior da página.

9. Na guia **configuração** , selecione a rede virtual e a sub-rede que você criou na seção anterior.

10. Clique no botão **próximo: marcas** na parte inferior da página.

11. Opcionalmente, na guia **Marcas**, insira o nome e o valor caso deseje categorizar o recurso.

12. Selecione **Examinar + criar**. Você é levado para a guia **revisar + criar** , na qual o Azure valida sua configuração.

13. Depois que a mensagem de **validação verde aprovada** for exibida, selecione **criar**.

## <a name="faq"></a>Perguntas frequentes

### <a name="why-cant-i-connect-to-a-private-endpoint"></a>Por que não consigo me conectar a um ponto de extremidade privado?
Se o cache já for um cache injetado de VNet, os pontos de extremidade privados não poderão ser usados com sua instância de cache. Se sua instância de cache estiver usando um recurso sem suporte (listado abaixo), você não poderá se conectar à sua instância de ponto de extremidade privada. Além disso, as instâncias de cache precisam ser criadas depois de 27 de julho para usar pontos de extremidade privados.

### <a name="what-features-are-not-supported-with-private-endpoints"></a>Quais recursos não têm suporte com pontos de extremidade privados?
Replicação geográfica, regras de firewall, suporte ao console do portal, vários pontos de extremidade por cache clusterizado, persistência para regras de firewall e redundância de zona. 

### <a name="how-can-i-change-my-private-endpoint-to-be-disabled-or-enabled-from-public-network-access"></a>Como posso alterar meu ponto de extremidade privado para ser desabilitado ou habilitado a partir do acesso à rede pública?
Há um `publicNetworkAccess` sinalizador que é `Disabled` por padrão. Esse sinalizador destina-se a permitir que você opcionalmente permita o acesso de ponto de extremidade público e privado ao cache se ele estiver definido como `Enabled` . Se definido como `Disabled` , ele só permitirá acesso de ponto de extremidade privado. Você pode definir o valor para `Disabled` ou `Enabled` com a solicitação de patch a seguir. Edite o valor para refletir qual sinalizador você deseja para o seu cache.

```http
PATCH  https://management.azure.com/subscriptions/{subscription}/resourceGroups/{resourcegroup}/providers/Microsoft.Cache/Redis/{cache}?api-version=2020-06-01
{    "properties": {
       "publicNetworkAccess":"Disabled"
   }
}
```

### <a name="are-network-security-groups-nsg-enabled-for-private-endpoints"></a>Os NSG (grupos de segurança de rede) estão habilitados para pontos de extremidade privados?
Não, eles estão desabilitados para pontos de extremidade privados. Embora as sub-redes que contenham o ponto de extremidade privado possam ter o NSG associado a ela, as regras não entrarão em vigor no tráfego processado pelo ponto de extremidade privado. Você deve [desabilitar a imposição de políticas de rede](../private-link/disable-private-endpoint-network-policy.md) para implantar pontos de extremidade privados em uma sub-rede. O NSG ainda é aplicado em outras cargas de trabalho hospedadas na mesma sub-rede. As rotas em qualquer sub-rede de cliente usarão um prefixo/32, a alteração do comportamento de roteamento padrão requer um UDR semelhante. 

Controle o tráfego usando as regras de NSG para o tráfego de saída nos clientes de origem. Implante rotas individuais com o prefixo /32 para substituir as rotas de ponto de extremidade privado. Os logs de fluxo e as informações de monitoramento do NSG para conexões de saída ainda são compatíveis e podem ser usados

### <a name="can-i-use-firewall-rules-with-private-endpoints"></a>Posso usar regras de firewall com pontos de extremidade privados?
Não, essa é uma limitação atual de pontos de extremidade privados. O ponto de extremidade privado não funcionará corretamente se as regras de firewall estiverem configuradas no cache.

### <a name="how-can-i-connect-to-a-clustered-cache"></a>Como posso me conectar a um cache clusterizado?
`publicNetworkAccess` precisa ser definido como `Disabled` e só pode haver uma conexão de ponto de extremidade privada.

### <a name="since-my-private-endpoint-instance-is-not-in-my-vnet-how-is-it-associated-with-my-vnet"></a>Como minha instância do ponto de extremidade privado não está em minha VNet, como ela está associada à minha VNet?
Ele só está vinculado à sua VNet. Como não está em sua VNet, as regras de NSG não precisam ser modificadas para pontos de extremidade dependentes.

### <a name="how-can-i-migrate-my-vnet-injected-cache-to-a-private-endpoint-cache"></a>Como posso migrar o cache injetado de VNet para um cache de ponto de extremidade privado?
Será necessário excluir o cache injetado da VNet e criar uma nova instância de cache com um ponto de extremidade privado.

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre o link privado do Azure, consulte a [documentação do link privado do Azure](../private-link/private-link-overview.md).
* Para comparar várias opções de isolamento de rede para sua instância de cache, consulte [documentação de opções de isolamento de rede do cache do Azure para Redis](cache-network-isolation.md).