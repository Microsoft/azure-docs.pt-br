---
title: "Hospedar vários sites com o Gateway de Aplicativo do Azure | Microsoft Docs"
description: "Esta página fornece instruções para configurar um gateway de aplicativo do Azure existente para hospedar vários aplicativos Web no mesmo gateway com o portal do Azure."
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: 95f892f6-fa27-47ee-b980-7abf4f2c66a9
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: davidmu
ms.openlocfilehash: 28a7fcb3e08a9c4b6a27e9fbc8d3ebae309adc62
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="configure-an-existing-application-gateway-for-hosting-multiple-web-applications"></a>Configurar um gateway de aplicativo existente para hospedar vários aplicativos Web

> [!div class="op_single_selector"]
> * [Portal do Azure](application-gateway-create-multisite-portal.md)
> * [PowerShell do Azure Resource Manager](application-gateway-create-multisite-azureresourcemanager-powershell.md)
> 
> 

A hospedagem de vários sites permite que você implante mais de um aplicativo Web no mesmo gateway de aplicativo. Ela depende da presença do cabeçalho de host na solicitação HTTP de entrada, para determinar quais ouvintes devem receber tráfego. O ouvinte, em seguida, direciona o tráfego ao pool de back-end apropriado conforme configurado na definição de regras do gateway. No caso de aplicativos Web habilitados com SSL, o gateway de aplicativo depende da extensão de SNI (Indicação de Nome de Servidor) para escolher o ouvinte correto para o tráfego da Web. Um uso comum para a hospedagem de vários sites é balancear a carga das solicitações para domínios da Web diferentes para pools de servidor back-end diferentes. Da mesma forma, vários subdomínios do mesmo domínio raiz também podem ser hospedados no mesmo Gateway de Aplicativo.

## <a name="scenario"></a>Cenário

No exemplo a seguir, o gateway de aplicativo está fornecendo o tráfego para contoso.com e fabrikam.com com dois pools de servidor back-end: o pool de servidores contoso e o pool de servidores fabrikam. É possível usar uma configuração semelhante para hospedar subdomínios, como app.contoso.com e blog.contoso.com.

![cenário multissite][multisite]

## <a name="before-you-begin"></a>Antes de começar

Esse cenário adiciona suporte multissite a um gateway de aplicativo existente. Para concluir esse cenário, um gateway de aplicativo existente precisa estar disponível para configuração. Acesse [Criar um Gateway de Aplicativo usando o portal](application-gateway-create-gateway-portal.md) para aprender a criar um gateway de aplicativo básico no portal.

A seguir estão as etapas necessárias para atualizar o gateway de aplicativo:

1. Crie pools de back-end para usar para cada site.
2. Crie um ouvinte para cada site a que o gateway de aplicativo dará suporte.
3. Crie regras para mapear cada ouvinte com o back-end apropriado.

## <a name="requirements"></a>Requisitos

* **Pool de servidores back-end:** a lista de endereços IP dos servidores back-end. Os endereços IP listados devem pertencer à sub-rede da rede virtual ou devem ser um IP/VIP público. O FQDN também pode ser usado.
* **Configurações do pool de servidores back-end:** cada pool tem configurações como porta, protocolo e afinidade baseada em cookie. Essas configurações são vinculadas a um pool e aplicadas a todos os servidores no pool.
* **Porta front-end:** essa porta é a porta pública aberta no gateway de aplicativo. O tráfego atinge essa porta e é redirecionado para um dos servidores back-end.
* **Ouvinte:** o ouvinte tem uma porta front-end, um protocolo (HTTP ou HTTPS, esses valores diferenciam maiúsculas de minúsculas) e o nome do certificado SSL (caso esteja configurando o descarregamento SSL). Para Application Gateways habilitados para vários sites, os indicadores de SNI e nome do host também são adicionados.
* **Regra:** a regra vincula o ouvinte e o pool de servidores back-end e define a qual pool de servidores back-end o tráfego deve ser direcionado ao atingir um ouvinte específico. As regras são processadas na ordem em que são listadas e o tráfego será direcionado por meio da primeira regra correspondente, independentemente de especificidade. Por exemplo, se você tiver uma regra usando um ouvinte básico e outra usando um ouvinte multissite, ambas na mesma porta, a regra com o ouvinte multissite deverá ser listada antes daquela com o ouvinte básico, para que a função multissite funcione conforme esperado. 
* **Certificados:** cada ouvinte exige um certificado exclusivo; neste exemplo, dois ouvintes são criados para multissite. Dois certificados .pfx e as respectivas senhas precisam ser criados.

## <a name="create-back-end-pools-for-each-site"></a>Criar pools de back-end para cada site

É necessário um pool de back-end para cada site ao qual esse gateway de aplicativo dá suporte. Neste caso, serão criados dois: um para contoso11.com e outro para fabrikam11.com.

### <a name="step-1"></a>Etapa 1

Navegue para um gateway de aplicativo existente no portal do Azure (https://portal.azure.com). Selecione **Pools de back-end** e clique em **Adicionar**

![adicionar pools do back-end][7]

### <a name="step-2"></a>Etapa 2

Preencha as informações para o pool de back-end **pool1**, adicionando os endereços ip ou FQDNs para os servidores back-end e clique em **OK**

![configurações do pool de back-end pool1][8]

### <a name="step-3"></a>Etapa 3

Na folha de pools de back-end, clique em **Adicionar** para adicionar um pool de back-end **pool2** extra, adicionando os endereços ip ou FQDNS para os servidores back-end e clique em **OK**

![configurações do pool de back-end pool2][9]

## <a name="create-listeners-for-each-back-end"></a>Criar ouvintes para cada back-end

O Gateway de Aplicativo depende de cabeçalhos de host HTTP 1.1 para hospedar mais de um site na mesma porta e endereço IP público. O ouvinte básico criado no portal não contém essa propriedade.

### <a name="step-1"></a>Etapa 1

Clique em **Ouvintes** no gateway de aplicativo existente e clique em **Multissite** para adicionar o primeiro ouvinte.

![folha de visão geral de ouvintes][1]

### <a name="step-2"></a>Etapa 2

Preencha as informações para o ouvinte. Neste exemplo, a terminação SSL está configurada; crie uma nova porta de front-end. Carregue o certificado .pfx a ser usado para a terminação SSL. A única diferença nessa folha em comparação à folha de ouvinte básica padrão é o nome do host.

![folha de propriedades do ouvinte][2]

### <a name="step-3"></a>Etapa 3

Clique em **Multissite** e crie outro ouvinte conforme descrito na etapa anterior para o segundo site. Certifique-se de usar um certificado diferente para o segundo ouvinte. A única diferença nessa folha em comparação à folha de ouvinte básica padrão é o nome do host. Preencha as informações para o ouvinte e clique em **OK**.

![folha de propriedades do ouvinte][3]

> [!NOTE]
> A criação de ouvintes no portal do Azure para o gateway de aplicativo é uma tarefa demorada; pode levar algum tempo para criar dois ouvintes nesse cenário. Ao concluir, os ouvintes são mostrados no portal conforme mostrado na imagem a seguir:

![visão geral do ouvinte][4]

## <a name="create-rules-to-map-listeners-to-backend-pools"></a>Criar regras para mapear ouvintes para pools de back-end

### <a name="step-1"></a>Etapa 1

Navegue para um gateway de aplicativo existente no portal do Azure (https://portal.azure.com). Selecione **Regras** e escolha a regra padrão existente **rule1** e clique em **Editar**.

### <a name="step-2"></a>Etapa 2

Preencha a folha de regras conforme mostrado na imagem a seguir. Escolher o primeiro ouvinte e o primeiro pool e clicar em **Salvar** ao concluir.

![editar regra existente][6]

### <a name="step-3"></a>Etapa 3

Clique em **Regra básica** para criar a segunda regra. Preencha o formulário com o segundo ouvinte e o segundo pool de back-end e clique em **OK** para salvar.

![adicionar folha de regra básica][10]

Esse cenário conclui a configuração de um gateway de aplicativo existente com suporte a multissite por meio do portal do Azure.

## <a name="next-steps"></a>Próximas etapas

Saiba como proteger seus sites com o [Gateway de Aplicativo - Firewall de Aplicativo Web](application-gateway-webapplicationfirewall-overview.md)

<!--Image references-->
[1]: ./media/application-gateway-create-multisite-portal/figure1.png
[2]: ./media/application-gateway-create-multisite-portal/figure2.png
[3]: ./media/application-gateway-create-multisite-portal/figure3.png
[4]: ./media/application-gateway-create-multisite-portal/figure4.png
[5]: ./media/application-gateway-create-multisite-portal/figure5.png
[6]: ./media/application-gateway-create-multisite-portal/figure6.png
[7]: ./media/application-gateway-create-multisite-portal/figure7.png
[8]: ./media/application-gateway-create-multisite-portal/figure8.png
[9]: ./media/application-gateway-create-multisite-portal/figure9.png
[10]: ./media/application-gateway-create-multisite-portal/figure10.png
[multisite]: ./media/application-gateway-create-multisite-portal/multisite.png
