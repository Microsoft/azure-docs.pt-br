---
title: Tutorial – Adicionar domínio personalizado à sua configuração do Azure Front Door
description: Neste tutorial, você aprenderá como integrar um domínio personalizado ao Azure Front Door.
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/24/2020
ms.author: duau
ms.openlocfilehash: e1540602bae0779d69c0cb4bb59e93b810b52904
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106550754"
---
# <a name="tutorial-add-a-custom-domain-to-your-front-door"></a>Tutorial: Adicionar um domínio personalizado ao seu Front Door

Este tutorial mostra como adicionar um domínio personalizado ao seu Front Door. Quando você usa o Azure Front Door para distribuir aplicativo, um domínio personalizado será necessário se você quiser que seu próprio nome de domínio fique visível na solicitação do usuário final. Ter um nome de domínio visível pode ser conveniente para os clientes e útil para fins de identidade visual.

Após você criar um Front Door, o host de front-end padrão, que é um subdomínio de `azurefd.net`, é incluído na URL para distribuir conteúdo do Front Door de seu back-end por padrão (por exemplo, https:\//contoso-frontend.azurefd.net/activeusers.htm). Para sua conveniência, o Azure Front Door oferece a opção de associar um domínio personalizado ao host padrão. Com essa opção, você distribui seu conteúdo com um domínio personalizado na URL, em vez de um nome de domínio de propriedade do Front Door (por exemplo, https:\//www.contoso.com/photo.png). 

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> - Criar um registro DNS CNAME.
> - Associe o domínio personalizado ao seu Front Door.
> - Verificar o domínio personalizado.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!NOTE]
> O Front Door **não** dá suporte a domínios personalizados com caracteres [punycode](https://en.wikipedia.org/wiki/Punycode). 

## <a name="prerequisites"></a>Pré-requisitos

* Antes de poder concluir as etapas neste tutorial, é necessário criar primeiro um Front Door. Para saber mais, confira [Início Rápido: Criar um Front Door](quickstart-create-front-door.md).

* Se você ainda não tiver um domínio personalizado, deverá primeiro adquirir um com um provedor de domínio. Por exemplo, confira [Comprar um nome de domínio personalizado](../app-service/manage-custom-dns-buy-domain.md).

* Se você estiver usando o Azure para hospedar seus [domínios DNS](../dns/dns-overview.md), delegue o DNS (Sistema de Nomes de Domínio) do provedor de domínio a um DNS do Azure. Confira [Delegar um domínio ao DNS do Azure](../dns/dns-delegate-domain-azure-dns.md)para saber mais. Caso contrário, se você estiver usando um provedor de domínio para lidar com seu domínio do DNS, vá para [Criar um registro DNS CNAME](#create-a-cname-dns-record).


## <a name="create-a-cname-dns-record"></a>Criar um registro DNS CNAME

Antes de usar um domínio personalizado com o Front Door, você precisará criar primeiro um registro CNAME (nome canônico) com seu provedor de domínio para apontar para o host de front-end padrão do Front Door (digamos, contoso.azurefd.net). Um registro CNAME é um tipo de registro DNS que mapeia um nome de domínio de origem para um nome de domínio de destino. Para o Azure Front Door, o nome de domínio de origem é o seu nome de domínio personalizado e o nome de domínio de destino é nome do host padrão do Front Door. Depois que o Front Door verificar o registro CNAME criado, o tráfego endereçado ao domínio personalizado de origem (como www\.contoso.com) será roteado para o host de front-end padrão do Front Door de destino especificado (como contoso-frontend.azurefd.net). 

Um domínio personalizado e seu subdomínio podem ser associados a apenas um único Front Door de cada vez. No entanto, é possível usar diferentes subdomínios do mesmo domínio personalizado para diferentes Front Doors usando vários registros CNAME. Também é possível mapear um domínio personalizado com diferentes subdomínios para o mesmo Front Door.


## <a name="map-the-temporary-afdverify-subdomain"></a>Mapear o subdomínio afdverify temporário

Quando você mapeia um domínio existente que está na produção, deve considerar algumas questões. Enquanto você estiver registrando seu domínio personalizado no portal do Azure, o domínio poderá sofrer um breve período de tempo de inatividade. Para evitar a interrupção do tráfego da Web, primeiro mapeie seu domínio personalizado para seu host de front-end padrão do Front Door com o subdomínio afdverify do Azure a fim de criar um mapeamento CNAME temporário. Com esse método, os usuários podem acessar seu domínio sem interrupção durante o mapeamento de DNS.

Caso contrário, se você estiver usando seu domínio personalizado pela primeira vez e nenhum tráfego de produção estiver em execução, poderá mapear diretamente seu domínio personalizado para seu Front Door. Vá para [Mapear domínio personalizado permanente](#map-the-permanent-custom-domain).

Para criar um registro CNAME com o subdomínio afdverify:

1. Entre no site do provedor de domínio relativo ao seu domínio personalizado.

2. Localize a página de gerenciamento de registros DNS consultando a documentação do provedor ou procurando áreas do site rotuladas como **Nome de Domínio**, **DNS** ou **Gerenciamento do servidor de nome**. 

3. Crie uma entrada de registro CNAME para seu domínio personalizado e preencha os campos conforme mostrado na seguinte tabela (os nomes dos campos podem variar):

    | Fonte                    | Type  | Destino                     |
    |---------------------------|-------|---------------------------------|
    | afdverify. www.contoso.com | CNAME | afdverify.contoso-frontend.azurefd.net |

    - Origem: Insira o nome de domínio personalizado, incluindo o subdomínio afdverify, no seguinte formato: afdverify. _&lt;nome de domínio personalizado&gt;_ . Por exemplo, afdverify. www.contoso.com. Se você estiver mapeando um domínio curinga, como \*.contoso.com, o valor de origem será o mesmo que seria sem o curinga: afdverify.contoso.com.

    - Tipo: Insira *CNAME*.

    - Destino: Insira o host padrão de front-end do Front Door, incluindo o subdomínio afdverify, no seguinte formato: afdverify. _&lt;nome do ponto de extremidade&gt;_ .azurefd.net. Por exemplo, afdverify.contoso-frontend.azurefd.net.

4. Salve suas alterações.

Por exemplo, o procedimento para o registrador de domínio GoDaddy é o seguinte:

1. Entre e selecione o domínio personalizado que você deseja usar.

2. Na seção Domínios, selecione **Gerenciar Todos** e selecione **DNS** | **Gerenciar Zonas**.

3. Para **Nome de Domínio**, insira seu domínio personalizado e selecione **Pesquisar**.

4. Na página **Gerenciamento de DNS**, selecione **Adicionar** e selecione **CNAME** na lista **Tipo**.

5. Preencha os campos abaixo da entrada CNAME:

    - Tipo: Deixe *CNAME* selecionado.

    - Host: Insira o subdomínio do domínio personalizado a ser usado, incluindo o nome do subdomínio afdverify. Por exemplo, afdverify. www.

    - Aponta para: Insira o nome do host padrão de front-end do Front Door, incluindo o nome do subdomínio afdverify. Por exemplo, afdverify.contoso-frontend.azurefd.net. 

    - TTL: Deixe a opção *uma Hora* selecionada.

6. Clique em **Salvar**.
 
    A entrada CNAME é adicionada à tabela de registros DNS.


## <a name="associate-the-custom-domain-with-your-front-door"></a>Associar o domínio personalizado ao seu Front Door

Depois de registrar seu domínio personalizado, adicione-o ao Front Door.

1. Entre no [portal do Azure](https://portal.azure.com/) e navegue até o Front Door que contém o host de front-end que você deseja mapear para um domínio personalizado.
    
2. Na página **Designer do Front Door**, clique em "+" para adicionar um domínio personalizado.
    
3. Especifique o **Domínio personalizado**. 

4. Para o **Host de front-end**, o host de front-end a ser usado como o domínio de destino do seu registro CNAME é previamente preenchido e derivado do seu Front Door: *&lt;nome do host padrão&gt;* .azurefd.net. Ele não pode ser alterado.

5. Para o **Nome de host personalizado**, insira seu domínio personalizado, incluindo o subdomínio, para usar como o domínio de origem de seu registro CNAME. Por exemplo, www\.contoso.com ou cdn.contoso.com. Não use o nome de subdomínio afdverify.

6. Selecione **Adicionar**.

   O Azure verifica se o registro CNAME existe para o nome de domínio personalizado digitado. Se o CNAME estiver correto, seu domínio personalizado será validado.

>[!WARNING]
> É **necessário** garantir que cada um dos hosts de front-end (incluindo domínios personalizados) no seu Front Door tem uma regra de roteamento com um caminho padrão ("/\*") associado a ela. Ou seja, entre todas as suas regras de roteamento, deve haver pelo menos uma regra de roteamento para cada um dos seus hosts de front-end definida no caminho padrão ('/\*'). Não fazer isso poderá fazer o tráfego do seu usuário final não ser roteado corretamente.

## <a name="verify-the-custom-domain"></a>Verificar o domínio personalizado

Depois de concluir o registro do seu domínio personalizado, verifique se o domínio personalizado referencia seu host de front-end do Front Door padrão.
 
Em seu navegador, navegue até o endereço do arquivo usando o domínio personalizado. Por exemplo, se o domínio personalizado for robotics.contoso.com, a URL para o arquivo em cache deverá ser semelhante à seguinte URL: http:\//robotics.contoso.com/my-public-container/my-file.jpg. Verifique se o resultado é o mesmo de quando você acessa o Front Door diretamente em *&lt;host do Front Door&gt;* .azurefd.net.


## <a name="map-the-permanent-custom-domain"></a>Mapear o domínio personalizado permanente

Se você tiver verificado que o subdomínio afdverify foi mapeado com êxito para seu Front Door (ou se você estiver usando um novo domínio personalizado que não está na produção), será possível mapear o domínio personalizado diretamente para seu host de front-end do Front Door padrão.

Para criar um registro CNAME para seu domínio personalizado:

1. Entre no site do provedor de domínio relativo ao seu domínio personalizado.

2. Localize a página de gerenciamento de registros DNS consultando a documentação do provedor ou procurando áreas do site rotuladas como **Nome de Domínio**, **DNS** ou **Gerenciamento do Servidor de Nome**. 

3. Crie uma entrada de registro CNAME para seu domínio personalizado e preencha os campos conforme mostrado na seguinte tabela (os nomes dos campos podem variar):

    | Fonte          | Type  | Destino           |
    |-----------------|-------|-----------------------|
    | <www.contoso.com> | CNAME | contoso-frontend.azurefd.net |

   - Origem: Digite seu nome de domínio personalizado (por exemplo, www\.contoso.com).

   - Tipo: Insira *CNAME*.

   - Destino: Insira o host padrão de front-end do Front Door. Ele deve estar no seguinte formato: _&lt;nomedohost&gt;_ .azurefd.net. Por exemplo, contoso-frontend.azurefd.net.

4. Salve suas alterações.

5. Se anteriormente você criou um registro CNAME do subdomínio afdverify temporário, exclua-o. 

6. Se estiver usando este domínio personalizado na produção pela primeira vez, siga as etapas para [Associar o domínio personalizado ao seu Front Door](#associate-the-custom-domain-with-your-front-door) e [Verificar o domínio personalizado](#verify-the-custom-domain).

Por exemplo, o procedimento para o registrador de domínio GoDaddy é o seguinte:

1. Entre e selecione o domínio personalizado que você deseja usar.

2. Na seção Domínios, selecione **Gerenciar Todos** e selecione **DNS** | **Gerenciar Zonas**.

3. Para **Nome de Domínio**, insira seu domínio personalizado e selecione **Pesquisar**.

4. Na página **Gerenciamento de DNS**, selecione **Adicionar** e selecione **CNAME** na lista **Tipo**.

5. Preencha os campos da entrada CNAME:

    - Tipo: Deixe *CNAME* selecionado.

    - Host: Insira o subdomínio do domínio personalizado a ser usado. Por exemplo, www ou perfil.

    - Aponta para: Insira o nome do host padrão do Front Door. Por exemplo, contoso.azurefd.net. 

    - TTL: Deixe a opção *uma Hora* selecionada.

6. Clique em **Salvar**.
 
    A entrada CNAME é adicionada à tabela de registros DNS.

7. Se você tiver um registro CNAME afdverify, selecione o ícone de lápis ao lado dele e o ícone de lixeira.

8. Selecione **Excluir** para excluir o registro CNAME.


## <a name="clean-up-resources"></a>Limpar os recursos

Nas etapas anteriores, você adicionou um domínio personalizado a um Front Door. Se você não quiser associar seu Front Door a um domínio personalizado, será possível remover o domínio personalizado seguindo estas etapas:
 
1. No designer do Front Door, selecione o domínio personalizado que você deseja remover.

2. Clique em Excluir no menu de contexto do domínio personalizado.  

   O domínio personalizado é desassociado do ponto de extremidade.


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

* Criar um registro DNS CNAME.
* Associe o domínio personalizado ao seu Front Door.
* Verificar o domínio personalizado.

Para saber como habilitar o HTTPS para o domínio personalizado, prossiga para o próximo tutorial.

> [!div class="nextstepaction"]
> [Habilitar HTTPS para um domínio personalizado](front-door-custom-domain-https.md)
