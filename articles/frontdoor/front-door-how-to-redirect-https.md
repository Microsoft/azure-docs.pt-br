---
title: Criar uma porta da frente com HTTP para redirecionamento HTTPS usando o portal do Azure
description: Saiba como criar uma porta da frente com o tráfego redirecionado de HTTP para HTTPS usando o portal do Azure.
services: front-door
author: sharad4u
ms.service: frontdoor
ms.topic: article
ms.date: 5/21/2019
ms.author: sharadag
ms.openlocfilehash: 7fabc1e3445d3dbd357700ffde3caeb985cc60c4
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67601970"
---
# <a name="create-a-front-door-with-http-to-https-redirection-using-the-azure-portal"></a>Criar uma porta da frente com HTTP para redirecionamento HTTPS usando o portal do Azure

Você pode usar o portal do Azure para criar uma [frente](front-door-overview.md) com um certificado para terminação SSL. Uma regra de roteamento é usada para redirecionar o tráfego HTTP para HTTPS.

Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Criar uma porta da frente com um recurso de aplicativo Web existente
> * Adicionar um domínio personalizado com o certificado SSL 
> * Configurar o redirecionamento HTTPS no domínio personalizado

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-a-front-door-with-an-existing-web-app-resource"></a>Criar uma porta da frente com um recurso de aplicativo Web existente

1. Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. Clique em **Criar um recurso** encontrado na parte superior esquerda do portal do Azure.
3. Pesquise **frente** usando a pesquisa de barra e depois de encontrar o tipo de recurso, clique em **criar**.
4. Escolha uma assinatura e usar um grupo de recursos existente ou crie um novo. Observe que o local solicitado na interface do usuário é para o grupo de recursos. Sua configuração de porta da frente será implantada em todos os [locais do Azure da frente do POP](https://docs.microsoft.com/azure/frontdoor/front-door-faq#what-are-the-pop-locations-for-azure-front-door-service).

    ![Configurar os aspectos básicos para a nova porta da frente](./media/front-door-url-redirect/front-door-create-basics.png)

5. Clique em **próxima** para inserir a guia de configuração. A configuração de porta da frente ocorre em três etapas – adicionar um host de front-end padrão, adicionando o back-ends em um pool de back-end e, em seguida, criar regras de roteamento para mapear o comportamento de roteamento para o host de front-end. 

     ![Designer de configuração de porta da frente](./media/front-door-url-redirect/front-door-designer.png)

6. Clique no ' **+** ' ícone na _front-end hospeda_ para criar um host de front-end, insira um nome globalmente exclusivo para seu host de front-end padrão para a frente (`\<**name**\>.azurefd.net`). Clique em **adicionar** para prosseguir para a próxima etapa.

     ![Adicionar um host de front-end](./media/front-door-url-redirect/front-door-create-fehost.png)

7. Clique no ' **+** ' ícone sobre o _pools de back-end_ para criar um pool de back-end. Forneça um nome para o pool de back-end e, em seguida, clique em '**adicionar um back-end**'.
8. Selecione o tipo de Host de back-end como _serviço de aplicativo_. Selecione a assinatura em que seu aplicativo web é hospedado e, em seguida, selecione o aplicativo web específico no menu suspenso para **nome de host de back-end**.
9. Clique em **Add** para salvar o back-end e clique em **Add** novamente para salvar a configuração de pool de back-end.   ![Adicione um back-end em um pool de back-end](./media/front-door-url-redirect/front-door-create-backendpool.png)

10. Clique no ' **+** ' ícone sobre o _regras de roteamento_ para criar uma rota. Forneça um nome para a rota, diga 'HttpToHttpsRedirect' e, em seguida, em seguida, defina as _protocolos aceitos_ campo **'HTTP'** . Certifique-se de que o apropriada _host de front-end_ está selecionado.  
11. No _detalhes da rota_ seção, defina o _tipo de rota_ para **redirecionar**, certifique-se de que o _redirecionar o tipo_ está definido como  **Encontrada (302)** e _redirecionar o protocolo_ é definido como **somente HTTPS**. 
12. Clique em Adicionar para salvar a regra de roteamento de HTTP para redirecionamento HTTPS.
     ![Adicionar um HTTP à rota de redirecionamento HTTPS](./media/front-door-url-redirect/front-door-redirect-config-example.png)
13. Adicione outra regra de roteamento para tratar o tráfego HTTPS. Clique no ' **+** ' fazer logon na _regras de roteamento_ e forneça um nome para a rota, diga 'DefaultForwardingRoute' e, em seguida, defina a _protocolos aceitos_ campo para **'HTTPS'** . Certifique-se de que o apropriada _host de front-end_ está selecionado.
14. Na seção detalhes da rota, defina a _tipo de rota_ para **Forward**, certifique-se de que o pool de back-end correto está selecionado e o _encaminhamento protocolo_ é definido como  **Somente HTTPS**. 
15. Clique em Adicionar para salvar a regra de roteamento para encaminhamento de solicitação.
     ![Adicione uma rota de encaminhamento para tráfego HTTPS](./media/front-door-url-redirect/front-door-forward-route-example.png)
16. Clique em **revisar + criar** e, em seguida **criar**, para criar seu perfil de porta da frente. Vá para o recurso depois de criado.

## <a name="add-a-custom-domain-to-your-front-door-and-enable-https-on-it"></a>Adicionar um domínio personalizado à sua frente e habilitar o HTTPS
As etapas a seguir demonstram como você pode adicionar um domínio personalizado em um recurso de porta de entrada existente e, em seguida, habilitar HTTP para redirecionamento a HTTPS nele. 

### <a name="add-a-custom-domain"></a>Adicionar um domínio personalizado

Neste exemplo, você adiciona um registro CNAME para o `www` subdomínio (por exemplo, `www.contosonews.com`).

#### <a name="create-the-cname-record"></a>Criar um registro CNAME

Adicione um registro CNAME para mapear um subdomínio para o host de front-end da porta padrão (`<name>.azurefd.net`, onde `<name>` é o nome do seu perfil de porta da frente).

Para o `www.contoso.com` domínio, por exemplo, adicionar um registro CNAME que mapeia o nome `www` para `<name>.azurefd.net`.

Depois de adicionar o CNAME, a página de registros DNS será parecida com o seguinte exemplo:

![Domínio personalizado de CNAME para frente](./media/front-door-url-redirect/front-door-dns-cname.png)

#### <a name="onboard-the-custom-domain-on-your-front-door"></a>Integrar o domínio personalizado na sua frente

1. Na guia designer de porta da frente, clique em '+' ícone nos hosts do front-end da seção para adicionar um novo domínio personalizado. 
2. Insira o nome DNS totalmente qualificado personalizado no campo de nome de host personalizado, exemplo `www.contosonews.com`. 
3. Depois que o mapeamento de CNAME do domínio à sua frente for validado, clique em **adicionar** para adicionar o domínio personalizado.
4. Clique em **salvar** para enviar as alterações.

![Menu de domínio personalizado](./media/front-door-url-redirect/front-door-add-custom-domain.png)

### <a name="enable-https-on-your-custom-domain"></a>Habilitar HTTPS em seu domínio personalizado

1. Clique no domínio personalizado que foi adicionado e, na seção **HTTPS de domínio personalizado**, altere o status para **habilitado**.
2. Você pode deixar o **tipo de gerenciamento de certificado** definido como _gerenciados da frente_ para o certificado livre mantidas, gerenciados e autorotated pela frente. Você também pode optar por usar seu próprio certificado SSL personalizado armazenado com o Azure Key Vault. Este tutorial presume que o uso da porta da frente gerenciado de certificado.
![Habilitar HTTPS para o domínio personalizado](./media/front-door-url-redirect/front-door-custom-domain-https.png)

3. Clique em **atualização** para salvar a seleção e, em seguida, clique em **salvar**.
4. Clique em **Refresh** depois de alguns minutos e, em seguida, clique no domínio personalizado novamente para ver o progresso de provisionamento de certificado. 

> [!WARNING]
> Habilitar HTTPS para um domínio personalizado pode levar vários minutos e também depende de validação de propriedade de domínio se não o CNAME for diretamente mapeado para seu host de porta da frente `<name>.azurefd.net`. Saiba mais sobre [como habilitar HTTPS para um domínio personalizado](./front-door-custom-domain-https.md).

## <a name="configure-the-routing-rules-for-the-custom-domain"></a>Configurar as regras de roteamento para o domínio personalizado

1. Clique na regra de roteamento de redirecionamento criada anteriormente.
2. Clique no menu suspenso para hosts de front-end e selecione seu domínio personalizado para aplicar essa rota para seu domínio também.
3. Clique em **Atualizar**.
4. Fazer a mesma operação para a outra regra de roteamento bem ou seja, para a rota de encaminhamento adicionar o domínio personalizado.
5. Clique em **salvar** para enviar suas alterações.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como o Front Door funciona](front-door-routing-architecture.md).
- Saiba mais sobre [redirecionamento da URL na porta da frente](front-door-url-redirect.md).
