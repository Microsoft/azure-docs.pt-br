---
title: "Mapear o conteúdo da CDN do Azure para um domínio personalizado | Microsoft Docs"
description: "Saiba como mapear o conteúdo da CDN do Azure para um domínio personalizado."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 289f8d9e-8839-4e21-b248-bef320f9dbfc
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: cd6db44f7776859d1e6a893543cf0666182ca41a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="map-azure-cdn-content-to-a-custom-domain"></a>Conteúdo da CDN do Azure para um domínio personalizado
Você pode mapear um domínio personalizado para um ponto de extremidade da CDN para usar seu próprio nome de domínio em URLs para o conteúdo armazenado em cache, em vez de usar um subdomínio do azureedge.net.

Há duas maneiras de mapear seu domínio personalizado para um ponto de extremidade da CDN.

1. [Criar um registro CNAME com o registrador de domínios e mapear o domínio e subdomínio personalizados para o ponto de extremidade da CDN](#register-a-custom-domain-for-an-azure-cdn-endpoint).
   
    Um registro CNAME é um recurso DNS que mapeia um domínio de origem, como `www.contosocdn.com` ou `cdn.contoso.com`, para um domínio de destino. Nesse caso, o domínio de origem é o seu domínio e subdomínio personalizados (um subdomínio, como **Web** ou **cdn** é sempre necessário). O domínio de destino é o ponto de extremidade da CDN.  
   
    O processo de mapeamento de seu domínio personalizado para o ponto de extremidade da CDN pode, no entanto, resultar em um breve período de inatividade para o domínio, enquanto você está registrando o domínio no Portal do Azure.
2. [Adicionar uma etapa de registro intermediária com **cdnverify**](#register-a-custom-domain-for-an-azure-cdn-endpoint-using-the-intermediary-cdnverify-subdomain)
   
    Se atualmente o seu domínio personalizado oferecer suporte a um aplicativo com um contrato de nível de serviço (SLA) que não permita um tempo de inatividade, então você pode usar o subdomínio **cdnverify** do Azure para fornecer uma etapa intermediária de registro para que os usuários possam acessar seu domínio durante o mapeamento do DNS.  

Depois de registrar seu domínio personalizado usando um dos procedimentos acima, [verifique se o subdomínio personalizado referencia seu ponto de extremidade da CDN](#verify-that-the-custom-subdomain-references-your-cdn-endpoint).

> [!NOTE]
> Você deve criar um registro CNAME com seu registrador de domínios para mapear o domínio para o ponto de extremidade da CDN. Os registros CNAME mapeiam subdomínios específicos como `www.contoso.com` ou `cdn.contoso.com`. Não é possível mapear um registro CNAME a um domínio raiz, como `contoso.com`.
> 
> Um subdomínio só pode ser associado a um ponto de extremidade da CDN. O registro CNAME que você criar roteará todo o tráfego endereçado ao subdomínio para o ponto de extremidade especificado.  Por exemplo, se você associar `www.contoso.com` ao ponto de extremidade da sua CDN, não poderá associá-lo a outros pontos de extremidade do Azure, como um ponto de extremidade de conta de armazenamento ou um ponto de extremidade de serviço de nuvem. No entanto, você pode usar outros subdomínios do mesmo domínio para pontos de extremidade de serviço diferentes. Você também pode mapear outros subdomínios para o mesmo ponto de extremidade da CDN.
> 
> Para pontos de extremidade da **CDN do Azure CDN da Verizon** (Standard e Premium), observe pode demorar até **90 minutos** para que as suas alterações de domínio personalizado sejam propagadas para os nós de borda da CDN.
> 
> 

## <a name="register-a-custom-domain-for-an-azure-cdn-endpoint"></a>Registrar um domínio personalizado para um ponto de extremidade da CDN do Azure
1. Faça logon no [Portal do Azure](https://portal.azure.com/).
2. Clique em **Procurar**, em **Perfis CDN** e no perfil CDN com o ponto de extremidade que você deseja mapear para um domínio personalizado.  
3. Na folha **Perfil CDN** , clique no ponto de extremidade da CDN ao qual você deseja associar o subdomínio.
4. Na parte superior da folha de ponto de extremidade, clique no botão **Adicionar Domínio Personalizado** .  Na folha **Adicionar um domínio personalizado** , você verá o nome do host do ponto de extremidade, derivado do ponto de extremidade da CDN, a ser usado para criar um novo registro CNAME. O formato do endereço do nome do host aparecerá como **&lt;EndpointName>.azureedge.net**.  Você pode copiar esse nome de host para usá-lo ao criar o registro CNAME.  
5. Navegue até o site do registrador do domínio e localize a seção para criar registros DNS. Você pode encontrá-lo em uma seção como **Nome de Domínio**, **DNS** ou **Gerenciamento de Servidor de Nomes**.
6. Localize a seção de gerenciamento de CNAMEs. Talvez você precise acessar uma página de configurações avançadas e procurar as palavras CNAME, Alias ou Subdomínios.
7. Crie um novo registro CNAME que mapeie o subdomínio escolhido (por exemplo, **www** ou **cdn**) para o nome de host fornecido na folha **Adicionar domínios personalizados**. 
8. Retorne à folha **Adicionar domínios personalizados** e insira seu domínio personalizado, incluindo o subdomínio, na caixa de diálogo. Por exemplo, insira o nome de domínio no formato `www.contoso.com` ou `cdn.contoso.com`.   
   
   O Azure verificará se o registro CNAME existe para o nome de domínio que você digitou. Se o CNAME estiver correto, seu domínio personalizado será validado.  No entanto, para pontos de extremidade da **CDN do Azure da Verizon** (Standard e Premium), pode levar até 90 minutos para que as configurações de domínio personalizado sejam propagadas para todos os nós de borda da CDN.  
   
   Observe que, em alguns casos, pode levar algum tempo para que o registro CNAME se propague para servidores de nomes na Internet. Se seu domínio não for validado imediatamente e você achar que o registro CNAME está correto, aguarde alguns minutos e tente novamente.

## <a name="register-a-custom-domain-for-an-azure-cdn-endpoint-using-the-intermediary-cdnverify-subdomain"></a>Registrar um domínio personalizado para um ponto de extremidade da CDN do Azure usando o subdomínio intermediário cdnverify.
1. Faça logon no [Portal do Azure](https://portal.azure.com/).
2. Clique em **Procurar**, em **Perfis CDN** e no perfil CDN com o ponto de extremidade que você deseja mapear para um domínio personalizado.  
3. Na folha **Perfil CDN** , clique no ponto de extremidade da CDN ao qual você deseja associar o subdomínio.
4. Na parte superior da folha de ponto de extremidade, clique no botão **Adicionar Domínio Personalizado** .  Na folha **Adicionar um domínio personalizado** , você verá o nome do host do ponto de extremidade, derivado do ponto de extremidade da CDN, a ser usado para criar um novo registro CNAME. O formato do endereço do nome do host aparecerá como **&lt;EndpointName>.azureedge.net**.  Você pode copiar esse nome de host para usá-lo ao criar o registro CNAME.
5. Navegue até o site do registrador do domínio e localize a seção para criar registros DNS. Você pode encontrá-lo em uma seção como **Nome de Domínio**, **DNS** ou **Gerenciamento de Servidor de Nomes**.
6. Localize a seção de gerenciamento de CNAMEs. Talvez você precise acessar uma página de configurações avançadas e procurar as palavras **CNAME**, **Alias** ou **Subdomínios**.
7. Crie um novo registro CNAME e forneça um alias de subdomínio que inclua o subdomínio **cdnverify** . Por exemplo, o subdomínio especificado estará no formato **cdnverify.www** ou **cdnverify.cdn**. Em seguida, forneça o nome de host, que é o seu ponto de extremidade da CDN, no formato **cdnverify.&lt;NomeDoPontoDeExtremidade>.azureedge.net**. O mapeamento de DNS deve ser parecido com: `cdnverify.www.consoto.com   CNAME   cdnverify.consoto.azureedge.net`  
8. Retorne à folha **Adicionar domínios personalizados** e insira seu domínio personalizado, incluindo o subdomínio, na caixa de diálogo. Por exemplo, insira o nome de domínio no formato `www.contoso.com` ou `cdn.contoso.com`. Observe que, nessa etapa, você não precisa prefaciar o subdomínio com **cdnverify**.  
   
    O Azure verificará se o registro CNAME existe para o nome de domínio cdnverify que você inseriu.
9. Nesse ponto, seu domínio personalizado foi verificado pelo Azure, mas o tráfego para seu domínio ainda não está sendo roteado para seu ponto de extremidade da CDN. Após aguardar tempo suficiente para permitir que as configurações de domínio personalizado sejam propagadas para os nós de borda da CDN (90 minutos para a **CDN do Azure da Verizon**, 1 a 2 minutos para a **CDN do Azure da Akamai**), volte ao site do registrador do seu DNS e crie outro registro CNAME que mapeie o seu subdomínio ao seu ponto de extremidade CDN. Por exemplo, especifique o subdomínio como **www** ou **cdn** e o nome de host como **&lt;NomeDoPontoDeExtremidade>.azureedge.net**. Com essa etapa, o registro do seu domínio personalizado está concluído.
10. Por fim, você pode excluir o Registro CNAME criado usando **cdnverify**, já que ele era necessário apenas como uma etapa intermediária.  

## <a name="verify-that-the-custom-subdomain-references-your-cdn-endpoint"></a>verifique se o subdomínio personalizado referencia seu ponto de extremidade da CDN
* Depois de concluir o registro de seu domínio personalizado, você pode acessar o conteúdo armazenado em cache no ponto de extremidade da CDN usando o domínio personalizado.
  Primeiro, verifique se você tem conteúdo público armazenado em cache no ponto de extremidade. Por exemplo, se o ponto de extremidade da CDN estiver associado a uma conta de armazenamento, a CDN armazenará conteúdo em cache em contêineres de blobs públicos. Para testar o domínio personalizado, verifique se o contêiner está definido para permitir acesso público e se contém pelo menos um blob.
* Em seu navegador, navegue até o endereço do blob usando o domínio personalizado. Por exemplo, se seu domínio personalizado for `cdn.contoso.com`, a URL para um blob armazenado em cache será semelhante à seguinte URL: http://cdn.contoso.com/mypubliccontainer/acachedblob.jpg

## <a name="see-also"></a>Consulte também
[Como habilitar a CDN (Rede de Distribuição de Conteúdo) para o Azure](cdn-create-new-endpoint.md)  

