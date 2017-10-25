---
title: "Configurar um nome de domínio personalizado nos Serviços de Nuvem | Microsoft Docs"
description: "Saiba como expor seus dados ou seu aplicativo do Azure na Internet em um domínio personalizado definindo as configurações de DNS.  Esses exemplos usam o portal do Azure."
services: cloud-services
documentationcenter: .net
author: Thraka
manager: timlt
editor: 
ms.assetid: 5783a246-a151-4fb1-b488-441bfb29ee44
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: adegeo
ms.openlocfilehash: f5d244fc747b923989407afd50927cda2b8d4a0f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="configuring-a-custom-domain-name-for-an-azure-cloud-service"></a>Configurando um nome de domínio personalizado para um serviço de nuvem do Azure
> [!div class="op_single_selector"]
> * [Portal do Azure](cloud-services-custom-domain-name-portal.md)
> * [Portal clássico do Azure](cloud-services-custom-domain-name.md)
> 
> 

Quando você cria um Serviço de Nuvem, o Azure o atribui a um subdomínio do **cloudapp.net**. Por exemplo, se o Serviço de Nuvem for denominado "contoso", os usuários poderão acessar seu aplicativo usando uma URL como http://contoso.cloudapp.net. O Azure também fornece um endereço IP virtual.

No entanto, você também pode expor seu aplicativo em seu próprio nome de domínio, como **contoso.com**. Este artigo explica como reservar ou configurar um nome de domínio personalizado para funções Web do Serviço de Nuvem.

Você já entendeu o que são os registros CNAME e A? [Pule a explicação](#add-a-cname-record-for-your-custom-domain).

> [!NOTE]
> Os procedimentos nesta tarefa se aplicam aos Serviços de Nuvem do Azure. Para os Serviços de Aplicativos, veja [isto](../app-service/app-service-web-tutorial-custom-domain.md). Para as contas de armazenamento, veja [isto](../storage/blobs/storage-custom-domain-name.md).
> 
> 

<p/>

> [!TIP]
> Comece a trabalhar com mais agilidade: use o NOVO [guia passo a passo do Azure](http://support.microsoft.com/kb/2990804)!  Ele torna rápido a associação de um nome de domínio personalizado E a proteção da comunicação (SSL) com os Serviços de Nuvem do Azure.
> 
> 

## <a name="understand-cname-and-a-records"></a>Entenda os registros CNAME e A
Os registros CNAME (ou registros de alias) e A permitem que você associe um nome de domínio a um servidor específico (ou serviço neste caso), de qualquer forma, cada um deles funciona de modo diferente. Quando você usa registros com serviços de nuvem do Azure, precisa fazer algumas considerações específicas antes de decidir qual deles usar.

### <a name="cname-or-alias-record"></a>Registro CNAME ou de alias
Um registro CNAME mapeia um domínio *específico*, como **contoso.com** ou **www.contoso.com**, para um nome de domínio geral. Neste caso, o nome de domínio geral é o nome de domínio **[myapp].cloudapp.net** do seu aplicativo hospedado no Azure. Uma vez criado, o CNAME cria um alias para **[myapp].cloudapp.net**. A entrada CNAME determinará o endereço IP do seu serviço **[myapp].cloudapp.net** automaticamente, portanto, se o endereço IP do serviço de nuvem for alterado, você não precisará tomar nenhuma ação.

> [!NOTE]
> Alguns registradores de domínio só permitem mapear subdomínios ao usar um registro CNAME, como www.contoso.com, e não nomes de raiz, como contoso.com. Para obter mais informações sobre os registros CNAME, consulte a documentação fornecida por seu registrador, [a entrada da Wikipédia sobre o registro CNAME](http://en.wikipedia.org/wiki/CNAME_record) ou o documento [Nomes de Domínio IETF - Implementação e Especificação](http://tools.ietf.org/html/rfc1035).
> 
> 

### <a name="a-record"></a>Registro A
Um registro *A* mapeia um domínio, como **contoso.com** ou **www.contoso.com** *, ou um domínio curinga* como **\*.contoso.com**, para um endereço IP. No caso de um serviço de nuvem do Azure, o IP virtual do serviço. Portanto, o principal benefício de um registro A em relação ao registro CNAME é que você pode ter uma entrada que usa um caractere curinga, como \***.contoso.com**, que lidaria com as solicitações de vários subdomínios, como **mail.contoso.com**, **login.contoso.com** ou **www.contoso.com**.

> [!NOTE]
> Uma vez que um registro A é mapeado para um endereço IP estático, não é possível resolver automaticamente as alterações ao endereço IP do seu serviço de nuvem. O endereço IP usado pelo seu serviço de nuvem é alocado na primeira vez que você implantar em um slot vazio (produção ou preparo.) Se você excluir a implantação para o slot, o endereço IP será liberado pelo Azure e quaisquer implantações futuras no slot poderão receber um novo endereço IP.
> 
> Convenientemente, o endereço IP do slot de uma determinada implantação (de produção ou de preparo) é mantido durante a troca entre implantações de preparo e de produção ou durante a execução de uma atualização in-loco de uma implantação existente. Para saber mais sobre a execução dessas ações, consulte [Como gerenciar serviços de nuvem](cloud-services-how-to-manage.md).
> 
> 

## <a name="add-a-cname-record-for-your-custom-domain"></a>Adicionar um registro CNAME para seu domínio personalizado
Para criar um registro CNAME, você deve adicionar uma nova entrada na tabela DNS para seu domínio personalizado usando as ferramentas fornecidas pelo seu registrador. Cada registrador tem um método semelhante, mas ligeiramente diferente para especificar um registro CNAME, mas os conceitos são os mesmos.

1. Use um dos seguintes métodos para localizar o nome de domínio **.cloudapp.net** atribuído ao seu serviço de nuvem.
   
   * Faça logon no [portal do Azure], selecione seu serviço de nuvem, examine a seção **Essentials**, em seguida, localize a entrada **URL do Site**.
     
       ![seção rapidamente mostrando a URL do site][csurl]
     
       **OR**
   * Instale e configure o [Azure Powershell](/powershell/azure/overview)e use o seguinte comando:
     
       ```powershell
       Get-AzureDeployment -ServiceName yourservicename | Select Url
       ```
     
     Salve o nome de domínio usado na URL retornada por qualquer método, pois você precisará dele durante a criação de um registro CNAME.
2. Faça logon no site do registrador de DNS e acesse a página de gerenciamento de DNS. Procure links ou áreas do site rotuladas como **Nome de Domínio**, **DNS** ou **Gerenciamento do Servidor de Nome**.
3. Agora, encontre onde você pode selecionar ou inserir registros CNAME. Você pode ter que selecionar o tipo de registro de uma lista suspensa ou acessar uma página de configurações avançadas. Você deve procurar as palavras **CNAME**, **Alias** ou **Subdomínios**.
4. Você também deverá fornecer um alias do domínio ou subdomínio para CNAME, como **www** se quiser criar um alias para **www.customdomain.com**. Se você deseja criar um alias para o domínio raiz, ele pode estar listado como o símbolo '**@**' nas ferramentas de DNS do registrador.
5. Em seguida, você deve fornecer um nome do host canônico, que, neste caso, é o domínio **cloudapp.net** do seu aplicativo.

Por exemplo, o seguinte registro CNAME encaminha todo o tráfego de **www.contoso.com** para **contoso.cloudapp.net**, o nome de domínio personalizado do seu aplicativo implantado:

| Alias/Nome do host/Subdomínio | Domínio canônico |
| --- | --- |
| www |contoso.cloudapp.net |

> [!NOTE]
> Um visitante de **www.contoso.com** nunca verá o host verdadeiro (contoso.cloudapp.net) e, portanto, o processo de encaminhamento será invisível ao usuário final.
> 
> O exemplo acima aplica-se somente ao tráfego no subdomínio **www** . Uma vez que não é possível usar caracteres curinga com registros CNAME, você deve criar um CNAME para cada domínio/subdomínio. Se você quiser direcionar o tráfego a partir dos subdomínios, como *.contoso.com, para o endereço cloudapp.net, poderá configurar uma entrada **Redirecionamento da URL** ou **Encaminhamento da URL** em suas configurações DNS, ou criar um registro A.
> 
> 

## <a name="add-an-a-record-for-your-custom-domain"></a>Adicionar um registro A ao seu domínio personalizado
Para criar um registro, primeiro você deve encontrar o endereço IP do seu serviço em nuvem. Então, em seguida, adicione uma nova entrada na tabela DNS para seu domínio personalizado usando as ferramentas fornecidas pelo seu registrador. Cada registrador tem um método semelhante, mas ligeiramente diferente para especificar um registro A, mas os conceitos são os mesmos.

1. Use um dos seguintes métodos para obter o endereço IP do seu serviço de nuvem.
   
   * Faça logon o [portal do Azure], selecione seu serviço de nuvem, examine a seção **Essentials**, em seguida, localize a entrada **Endereços IP públicos**.
     
       ![seção rapidamente mostrando a VIP][vip]
     
       **OR**
   * Instale e configure o [Azure Powershell](/powershell/azure/overview)e use o seguinte comando:
     
       ```powershell
       get-azurevm -servicename yourservicename | get-azureendpoint -VM {$_.VM} | select Vip
       ```
     
     Salve o endereço IP, pois você precisará dele durante a criação de um registro.
2. Faça logon no site do registrador de DNS e acesse a página de gerenciamento de DNS. Procure links ou áreas do site rotuladas como **Nome de Domínio**, **DNS** ou **Gerenciamento do Servidor de Nome**.
3. Agora, encontre onde você pode selecionar ou inserir registros A. Você pode ter que selecionar o tipo de registro de uma lista suspensa ou acessar uma página de configurações avançadas.
4. Selecione ou digite o domínio ou subdomínio que usará este registro A. Por exemplo, selecione **www** se você quiser criar um alias para **www.customdomain.com**. Se você quiser criar uma entrada curinga para todos os subdomínios, digite '*****'. Isso cobrirá todos os subdomínios, como **mail.customdomain.com**, **login.customdomain.com** e **www.customdomain.com**.
   
    Se você deseja criar um registro A para o domínio raiz, ele pode estar listado como o símbolo '**@**' nas ferramentas de DNS do registrador.
5. Digite o endereço IP do seu serviço de nuvem no campo fornecido. Isto associa a entrada de domínio usada no registro A com o endereço IP da sua implantação do serviço de nuvem.

Por exemplo, o seguinte registro A encaminha todo o tráfego de **contoso.com** para **137.135.70.239**, o endereço IP do seu aplicativo implantado:

| Nome do host/Subdomínio | Endereço IP |
| --- | --- |
| @ |137.135.70.239 |

Este exemplo demonstra como criar um registro A para o domínio raiz. Se você quisesse criar uma entrada curinga para cobrir todos os subdomínios, digitaria '*****' como o subdomínio.

> [!WARNING]
> Endereços IP no Azure são dinâmicos por padrão. Você provavelmente desejará usar um [endereço IP reservado](../virtual-network/virtual-networks-reserved-public-ip.md) para garantir que seu endereço IP não seja alterado.
> 
> 

## <a name="next-steps"></a>Próximas etapas
* [Como gerenciar serviços de nuvem](cloud-services-how-to-manage.md)
* [Como mapear o conteúdo da CDN para um domínio personalizado](../cdn/cdn-map-content-to-custom-domain.md)
* [Configuração geral do serviço de nuvem](cloud-services-how-to-configure-portal.md).
* Saiba como [implantar um serviço de nuvem](cloud-services-how-to-create-deploy-portal.md).
* Configurar [certificados SSL](cloud-services-configure-ssl-certificate-portal.md).

[Expose Your Application on a Custom Domain]: #access-app
[Add a CNAME Record for Your Custom Domain]: #add-cname
[Expose Your Data on a Custom Domain]: #access-data
[VIP swaps]: cloud-services-how-to-manage-portal.md#how-to-swap-deployments-to-promote-a-staged-deployment-to-production
[Create a CNAME record that associates the subdomain with the storage account]: #create-cname
[portal do Azure]: https://portal.azure.com
[vip]: ./media/cloud-services-custom-domain-name-portal/csvip.png
[csurl]: ./media/cloud-services-custom-domain-name-portal/csurl.png
