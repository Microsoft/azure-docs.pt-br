---
title: Tutorial – Adicionar um domínio personalizado ao seu ponto de extremidade da CDN | Microsoft Docs
description: Neste tutorial, você mapeia o conteúdo do ponto de extremidade da CDN do Azure para um domínio personalizado.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/11/2018
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: 81db1a7dc01b3d60ee6384f2026ed5ce692ff140
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67666089"
---
# <a name="tutorial-add-a-custom-domain-to-your-azure-cdn-endpoint"></a>Tutorial: Adicionar um domínio personalizado ao seu ponto de extremidade da CDN do Azure
Este tutorial mostra como adicionar um domínio personalizado a um ponto de extremidade do Azure CDN (Rede de Distribuição de Conteúdo). Quando você usa um ponto de extremidade da CDN para distribuir conteúdo, é necessário ter um domínio personalizado se você deseja que seu próprio nome de domínio fique visível na URL da CDN. Ter um nome de domínio visível pode ser conveniente para os clientes e útil para fins de identidade visual. 

Depois que um ponto de extremidade da CDN é criado em seu perfil, o nome do ponto de extremidade, que é um subdomínio de azureedge.net, é incluído na URL que distribui conteúdo da CDN por padrão (por exemplo, https:\//contoso.azureedge.net/foto.png). Para sua conveniência, a CDN do Azure fornece a opção de associar um domínio personalizado a um ponto de extremidade de CDN. Com essa opção, você distribui seu conteúdo com um domínio personalizadona URL em vez de um nome de ponto de extremidade (por exemplo, https:\//www.contoso.com/foto.png). 

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> - Criar um registro DNS CNAME.
> - Associar o domínio personalizado ao seu ponto de extremidade da CDN.
> - Verificar o domínio personalizado.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de concluir as etapas deste tutorial, crie primeiro um perfil CDN e pelo menos um ponto de extremidade da CDN. Para saber mais, confira [Início Rápido: Criar um ponto de extremidade e um perfil de CDN do Azure](cdn-create-new-endpoint.md).

Se você ainda não tiver um domínio personalizado, deverá primeiro adquirir um com um provedor de domínio. Por exemplo, confira [Comprar um nome de domínio personalizado](https://docs.microsoft.com/azure/app-service/manage-custom-dns-buy-domain).

Se você estiver usando o Azure para hospedar seus [domínios DNS](https://docs.microsoft.com/azure/dns/dns-overview), delegue o DNS (Sistema de Nomes de Domínio) do provedor de domínio a um DNS do Azure. Confira [Delegar um domínio ao DNS do Azure](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns)para saber mais. Caso contrário, se você estiver usando um provedor de domínio para lidar com seu domínio do DNS, vá para [Criar um registro DNS CNAME](#create-a-cname-dns-record).


## <a name="create-a-cname-dns-record"></a>Criar um registro DNS CNAME

Antes de usar um domínio personalizado com um ponto de extremidade da CDN do Azure, primeiro crie um registro CNAME (nome canônico) com seu provedor de domínio a fim de apontar para seu ponto de extremidade da CDN. Um registro CNAME é um tipo de registro DNS que mapeia um nome de domínio de origem para um nome de domínio de destino. Para a CDN do Azure, o nome de domínio de origem é o seu nome de domínio personalizado e o nome de domínio de destino é nome do host do ponto de extremidade da CDN. Depois que a CDN do Azure verifica o registro CNAME criado, o tráfego endereçado ao domínio personalizado de origem (como www\.contoso.com) é roteado para o nome de host do ponto de extremidade da CDN de destino especificado (como contoso.azureedge.net). 

Um domínio personalizado e seu subdomínio podem ser associados apenas a um ponto de extremidade de cada vez. No entanto, você pode usar outros subdomínios do mesmo domínio personalizado para pontos de extremidade de serviços Azure diferentes usando vários registros CNAME. Você também pode mapear um domínio personalizado com outros subdomínios para o mesmo ponto de extremidade da CDN.

> [!NOTE]
> Qualquer tipo de registro de alias poderá ser usado para domínios personalizados se você estiver usando o DNS do Azure como o provedor de domínio. Este passo a passo usa o tipo de registro CNAME. Se você estiver usando os tipos de registro A ou AAAA, siga as mesmas etapas abaixo e substitua CNAME pelo tipo de registro de sua escolha. Se você estiver usando um registro de alias para adicionar um domínio raiz como um domínio personalizado e desejar habilitar o SSL, use a validação manual conforme descrito [neste artigo](https://docs.microsoft.com/azure/cdn/cdn-custom-ssl?tabs=option-1-default-enable-https-with-a-cdn-managed-certificate#custom-domain-is-not-mapped-to-your-cdn-endpoint). Para obter mais informações, confira [Apontar o apex de zona para pontos de extremidade da CDN do Azure](https://docs.microsoft.com/azure/dns/dns-alias#point-zone-apex-to-azure-cdn-endpoints).

## <a name="map-the-temporary-cdnverify-subdomain"></a>Mapear o subdomínio cdnverify temporário

Quando você mapeia um domínio existente que está na produção, deve considerar algumas questões. Enquanto você estiver registrando seu domínio personalizado no portal do Azure, o domínio poderá sofrer um breve período de tempo de inatividade. Para evitar a interrupção do tráfego da Web, primeiro mapeie seu domínio personalizado para seu nome de host do ponto de extremidade da CDN com o subdomínio cdnverify do Azure a fim de criar um mapeamento CNAME temporário. Com esse método, os usuários podem acessar seu domínio sem interrupção durante o mapeamento de DNS. 

Caso contrário, se você estiver usando seu domínio personalizado pela primeira vez e nenhum tráfego de produção estiver em execução, poderá mapear diretamente seu domínio personalizado para seu ponto de extremidade da CDN. Vá para [Mapear domínio personalizado permanente](#map-the-permanent-custom-domain).

Para criar um registro CNAME com o subdomínio cdnverify:

1. Entre no site do provedor de domínio relativo ao seu domínio personalizado.

2. Localize a página de gerenciamento de registros DNS consultando a documentação do provedor ou procurando áreas do site rotuladas como **Nome de Domínio**, **DNS** ou **Gerenciamento do servidor de nome**. 

3. Crie uma entrada de registro CNAME para seu domínio personalizado e preencha os campos conforme mostrado na seguinte tabela (os nomes dos campos podem variar):

    | Fonte                    | Type  | Destino                     |
    |---------------------------|-------|---------------------------------|
    | cdnverify.www.contoso.com | CNAME | cdnverify.contoso.azureedge.net |

    - Origem: Insira seu nome de domínio personalizado, incluindo o subdomínio cdnverify, no seguinte formato: cdnverify.&lt;custom domain name&gt;. Por exemplo, cdnverify.www.contoso.com.

    - Digite: Insira *CNAME*.

    - Destino: insira o nome do host do ponto de extremidade da CDN, inclusive o subdomínio cdnverify, no seguinte formato: cdnverify _&lt;nome do ponto de extremidade&gt;_ .azureedge.net. Por exemplo, cdnverify.contoso.azureedge.net.

4. Salve suas alterações.

Por exemplo, o procedimento para o registrador de domínio GoDaddy é o seguinte:

1. Entre e selecione o domínio personalizado que você deseja usar.

2. Na seção Domínios, selecione **Gerenciar Todos** e selecione **DNS** | **Gerenciar Zonas**.

3. Para **Nome de Domínio**, insira seu domínio personalizado e selecione **Pesquisar**.

4. Na página **Gerenciamento de DNS**, selecione **Adicionar** e selecione **CNAME** na lista **Tipo**.

5. Preencha os campos abaixo da entrada CNAME:

    ![Entrada CNAME](./media/cdn-map-content-to-custom-domain/cdn-cdnverify-cname-entry.png)

    - Digite: Deixe *CNAME* selecionado.

    - Host: insira o subdomínio do domínio personalizado a ser usado, incluindo o nome de subdomínio cdnverify. Por exemplo, cdnverify.www.

    - Aponta para: insira o nome do host do ponto de extremidade da CDN, incluindo o nome de subdomínio cdnverify. Por exemplo, cdnverify.contoso.azureedge.net. 

    - TTL: Deixe *1 Hora* selecionado.

6. Clique em **Salvar**.
 
    A entrada CNAME é adicionada à tabela de registros DNS.

    ![Tabela de registros DNS](./media/cdn-map-content-to-custom-domain/cdn-cdnverify-dns-table.png)


## <a name="associate-the-custom-domain-with-your-cdn-endpoint"></a>Associar o domínio personalizado ao seu ponto de extremidade da CDN

Depois de registrar seu domínio personalizado, adicione-o ao ponto de extremidade da CDN. 

1. Entre no [portal do Azure](https://portal.azure.com/) e navegue até o perfil CDN contendo o ponto de extremidade que você deseja mapear para um domínio personalizado.
    
2. Na página **Perfil CDN**, selecione o ponto de extremidade da CDN a ser associado ao domínio personalizado.

   A página **Ponto de Extremidade** se abre.
    
3. Selecione **Domínio personalizado**. 

   ![Botão de domínio personalizado da CDN](./media/cdn-map-content-to-custom-domain/cdn-custom-domain-button.png)

   A página **Adicionar um domínio personalizado** será aberta.

4. Para **Nome de host do ponto de extremidade**, o nome do host do ponto de extremidade a ser usado como o domínio de destino do seu registro CNAME é previamente preenchido e é derivado de sua URL de ponto de extremidade CDN: *&lt;nome de host do ponto de extremidade&gt;* .azureedge.net. Ele não pode ser alterado.

5. Para o **Nome de host personalizado**, insira seu domínio personalizado, incluindo o subdomínio, para usar como o domínio de origem de seu registro CNAME. Por exemplo, www\.contoso.com ou cdn.contoso.com. Não use o nome de subdomínio cdnverify.

   ![Diálogo Domínio personalizado da CDN](./media/cdn-map-content-to-custom-domain/cdn-add-custom-domain.png)

6. Selecione **Adicionar**.

   O Azure verifica se o registro CNAME existe para o nome de domínio personalizado digitado. Se o CNAME estiver correto, seu domínio personalizado será validado. 

   Pode levar algum tempo para que as novas configurações de domínio personalizado sejam propagadas para todos os nós de borda da CDN: 
    - Para perfis da **CDN Standard do Azure da Microsoft**, a propagação geralmente é concluída em dez minutos. 
    - Para perfis da **CDN Standard do Azure da Akamai**, a propagação normalmente é concluída em um minuto. 
    - Para perfis da **CDN Standard do Azure da Verizon** e da **CDN Premium do Azure da Verizon**, a propagação geralmente é concluída em 10 minutos.   


## <a name="verify-the-custom-domain"></a>Verificar o domínio personalizado

Depois de concluir o registro do seu domínio personalizado, verifique se o domínio personalizado referencia seu ponto de extremidade da CDN.
 
1. Verifique se você tem conteúdo público armazenado em cache no ponto de extremidade. Por exemplo, se o ponto de extremidade da CDN estiver associado a uma conta de armazenamento, a CDN do Azure armazenará conteúdo em cache em um contêiner público. Para testar o domínio personalizado, verifique se o contêiner está definido para permitir acesso público e se contém pelo menos um arquivo.

2. Em seu navegador, navegue até o endereço do arquivo usando o domínio personalizado. Por exemplo, se o domínio personalizado for www.contoso.com, a URL para o arquivo armazenado em cache deverá ser semelhante à seguinte URL: http:\//www.contoso.com/my-public-container/my-file.jpg. Verifique se o resultado é o mesmo de quando você acessa o ponto de extremidade da CDN diretamente no *&lt;nome de host do ponto de extremidade&gt;* .azureedge.net.


## <a name="map-the-permanent-custom-domain"></a>Mapear o domínio personalizado permanente

Se você verificou que o subdomínio cdnverify foi mapeado com êxito para seu ponto de extremidade (ou se você estiver usando um novo domínio personalizado que não está na produção), mapeie o domínio personalizado diretamente para seu nome do host do ponto de extremidade da CDN.

Para criar um registro CNAME para seu domínio personalizado:

1. Entre no site do provedor de domínio relativo ao seu domínio personalizado.

2. Localize a página de gerenciamento de registros DNS consultando a documentação do provedor ou procurando áreas do site rotuladas como **Nome de Domínio**, **DNS** ou **Gerenciamento do Servidor de Nome**. 

3. Crie uma entrada de registro CNAME para seu domínio personalizado e preencha os campos conforme mostrado na seguinte tabela (os nomes dos campos podem variar):

    | Fonte          | Type  | Destino           |
    |-----------------|-------|-----------------------|
    | <www.contoso.com> | CNAME | contoso.azureedge.net |

   - Origem: Digite seu nome de domínio personalizado (por exemplo, www\.contoso.com).

   - Digite: Insira *CNAME*.

   - Destino: Insira o nome de host do ponto de extremidade CDN. Ele deve estar no seguinte formato: _&lt;nome do ponto de extremidade&gt;_ .azureedge.net. Por exemplo, contoso.azureedge.net.

4. Salve suas alterações.

5. Se você criou um registro CNAME para o subdomínio cdnverify temporário anteriormente, exclua-o. 

6. Se você estiver usando o domínio personalizado na produção pela primeira vez, siga as etapas para [Associar o domínio personalizado ao seu ponto de extremidade da CDN](#associate-the-custom-domain-with-your-cdn-endpoint) e [Verificar o domínio personalizado](#verify-the-custom-domain).

Por exemplo, o procedimento para o registrador de domínio GoDaddy é o seguinte:

1. Entre e selecione o domínio personalizado que você deseja usar.

2. Na seção Domínios, selecione **Gerenciar Todos** e selecione **DNS** | **Gerenciar Zonas**.

3. Para **Nome de Domínio**, insira seu domínio personalizado e selecione **Pesquisar**.

4. Na página **Gerenciamento de DNS**, selecione **Adicionar** e selecione **CNAME** na lista **Tipo**.

5. Preencha os campos da entrada CNAME:

    ![Entrada CNAME](./media/cdn-map-content-to-custom-domain/cdn-cname-entry.png)

    - Digite: Deixe *CNAME* selecionado.

    - Host: Insira o subdomínio do domínio personalizado a ser usado. Por exemplo, www ou cdn.

    - Aponta para: insira o nome do host do ponto de extremidade da CDN. Por exemplo, contoso.azureedge.net. 

    - TTL: Deixe *1 Hora* selecionado.

6. Clique em **Salvar**.
 
    A entrada CNAME é adicionada à tabela de registros DNS.

    ![Tabela de registros DNS](./media/cdn-map-content-to-custom-domain/cdn-dns-table.png)

7. Se você tiver um registro CNAME cdnverify, selecione o ícone de lápis ao lado dele e selecione o ícone de Lixeira.

8. Selecione **Excluir** para excluir o registro CNAME.


## <a name="clean-up-resources"></a>Limpar recursos

Nas etapas anteriores, você adicionou um domínio personalizado a um ponto de extremidade da CDN. Se você não quiser associar seu ponto de extremidade a um domínio personalizado, remova o domínio personalizado seguindo estas etapas:
 
1. No seu perfil CDN, selecione o ponto de extremidade com o domínio personalizado que você deseja remover.

2. Na página **Ponto de Extremidade**, em Domínios personalizados, clique com o botão direito do mouse no domínio personalizado que você deseja remover e selecione **Excluir** no menu de contexto.  

   O domínio personalizado é desassociado do ponto de extremidade.


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> - Criar um registro DNS CNAME.
> - Associar o domínio personalizado ao seu ponto de extremidade da CDN.
> - Verificar o domínio personalizado.

Avance para o próximo tutorial a fim de aprender a configurar HTTPS em um domínio personalizado da CDN do Azure.

> [!div class="nextstepaction"]
> [Tutorial: Configurar HTTPS em um domínio personalizado da CDN do Azure](cdn-custom-ssl.md)


