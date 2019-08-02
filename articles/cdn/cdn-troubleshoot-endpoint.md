---
title: Solucionando problemas dos pontos de extremidade do Azure CDN que retornam o código de status 404 | Microsoft Docs
description: Solucione problemas nos códigos de resposta 404 com pontos de extremidade da CDN do Azure.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: b588a1eb-ab69-4fc7-ae4d-157c3e46f4a8
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: b665c2f72f50b2d72fd625b49c4212785ab3301d
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593271"
---
# <a name="troubleshooting-azure-cdn-endpoints-that-return-a-404-status-code"></a>Solucionando problemas dos pontos de extremidade do Azure CDN que retornam o código de status 404
Este artigo permite que você solucione problemas com pontos de extremidade de Rede de Distribuição de Conteúdo (CDN) do Microsoft Azure que retornam códigos de status de resposta HTTP 404.

Se você precisar de mais ajuda em qualquer momento neste artigo, você pode contatar os especialistas do Azure nos [fóruns do Azure MSDN e Excedente de Pilha](https://azure.microsoft.com/support/forums/). Como alternativa, você também pode registrar um incidente de suporte do Azure. Vá para o [Site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **Obter suporte**.

## <a name="symptom"></a>Sintoma
Você criou um perfil CDN e um ponto de extremidade, mas seu conteúdo não parece estar disponível na CDN. Os usuários que tentam acessar seu conteúdo por meio da URL da CDN recebem um código de status HTTP 404. 

## <a name="cause"></a>Causa
Há várias causas possíveis, incluindo:

* A origem do arquivo não é visível para a CDN.
* O ponto de extremidade está configurado incorretamente, fazendo com que a CDN faça uma pesquisa no lugar errado.
* O host está rejeitando o cabeçalho de host da CDN.
* O ponto de extremidade não teve tempo para ser propagado por toda a CDN.

## <a name="troubleshooting-steps"></a>Etapas para solucionar problemas
> [!IMPORTANT]
> Depois de criar um ponto de extremidade CDN, ele não estará imediatamente disponível para uso, pois o registro demora um pouco para ser propagado pela CDN:
> - Para perfis da **CDN Standard do Azure da Microsoft**, a propagação geralmente conclui em dez minutos. 
> - Para perfis da **CDN Standard do Azure da Akamai**, a propagação normalmente é concluída em um minuto. 
> - Para perfis da **CDN Standard do Azure da Verizon** e **CDN Premium do Azure da Verizon**, a propagação geralmente conclui em 90 minutos. 
> 
> Se você concluir as etapas descritas neste documento e ainda obter respostas 404, considere aguardar algumas horas para verificar novamente antes de abrir um tíquete de suporte.
> 
> 

### <a name="check-the-origin-file"></a>Verificar o arquivo de origem
Primeiro, verifique se o arquivo para cache está disponível no servidor de origem e está acessível publicamente na internet. A maneira mais rápida de fazer isso é abrir um navegador em uma sessão privada ou anônima e navegar diretamente até o arquivo. Digite ou cole a URL na caixa de endereço e verifique se isso resulta no arquivo esperado. Por exemplo, suponha que você tem um arquivo em uma conta de Armazenamento do Azure, acessível em https:\//cdndocdemo.blob.core.windows.net/publicblob/lorem.txt. Se você consegue carregar com êxito o conteúdo desse arquivo, ele está aprovado no teste.

![Sucesso!](./media/cdn-troubleshoot-endpoint/cdn-origin-file.png)

> [!WARNING]
> Embora essa seja a maneira mais rápida e fácil de verificar se o arquivo está disponível publicamente, algumas configurações de rede em sua organização poderiam dar a impressão de que um arquivo está publicamente disponível quando, na verdade, só está visível para os usuários de sua rede (mesmo que ele esteja hospedado no Azure). Para garantir que este não seja o caso, teste o arquivo com um navegador externo, como um dispositivo móvel que não está conectado à rede de sua organização ou uma máquina virtual no Azure.
> 
> 

### <a name="check-the-origin-settings"></a>Verificar as configurações de origem
Depois de verificar se o arquivo está disponível publicamente na Internet, verifique suas configurações de origem. No [Portal do Azure](https://portal.azure.com), navegue até o seu perfil CDN e selecione o ponto de extremidade que você está solucionando. Na página do **Ponto de extremidade** resultante, selecione a origem.  

![Página do ponto de extremidade com a origem realçada](./media/cdn-troubleshoot-endpoint/cdn-endpoint.png)

A página de **Origem** é exibida. 

![Página de origem](./media/cdn-troubleshoot-endpoint/cdn-origin-settings.png)

#### <a name="origin-type-and-hostname"></a>Tipo e nome do host da origem
Verifique se os valores do **Tipo de origem** e **Nome do host de origem** estão corretos. Neste exemplo, https:\/cdndocdemo.blob.core.windows.net/publicblob/lorem.txt, a parte do nome do host da URL é *cdndocdemo.blob.core.windows.net*, o que está correto. Como as origens do Armazenamento do Azure, do aplicativo Web e do serviço de nuvem usam um valor da lista suspensa para o campo **Nome do host de origem**, grafias incorretas não são um problema. No entanto, se você usar uma origem personalizada, certifique-se de que seu nome de host esteja escrito corretamente.

#### <a name="http-and-https-ports"></a>Portas HTTP e HTTPS
Verifique suas portas **HTTP** e **HTTPS**. Na maioria dos casos, as portas 80 e 443 estão corretas e não será necessária nenhuma alteração.  No entanto, se o servidor de origem estiver escutando em uma porta diferente, isso precisará ser representado aqui. Caso você não tenha certeza, veja a URL do arquivo de origem. As especificações de HTTP e HTTPS usam as portas 80 e 443 como os padrões. Na URL de exemplo, https:\//cdndocdemo.blob.core.windows.net/publicblob/lorem.txt, uma porta não é especificada, então o padrão de 443 é considerado e as configurações estão corretas.  

No entanto, suponha que a URL para o arquivo de origem que você testou anteriormente seja http:\//www.contoso.com:8080/file.txt. Observe a parte *:8080* no final do segmento do nome do host. Número instrui o navegador para usar a porta 8080 para se conectar ao servidor web em www\.contoso.com, portanto, você precisará inserir *8080* no **porta HTTP** campo. É importante observar que essas configurações de porta só afetam a porta usada pelo ponto de extremidade para recuperar informações da origem.

> [!NOTE]
> **Azure CDN Standard da Akamai** não permitem o intervalo de portas TCP completo para origens.  Para obter uma lista das portas de origem que não são permitidas, confira [CDN do Azure das Portas de Origem Permitidas Akamai](/previous-versions/azure/mt757337(v=azure.100)).  
> 
> 

### <a name="check-the-endpoint-settings"></a>Verificar as configurações de ponto de extremidade
Na página **Ponto de extremidade**, selecione o botão **Configurar**.

![Página do ponto de extremidade com o botão Configurar realçado](./media/cdn-troubleshoot-endpoint/cdn-endpoint-configure-button.png)

A página **Configurar** do ponto de extremidade da CDN será exibida.

![Configurar página](./media/cdn-troubleshoot-endpoint/cdn-configure.png)

#### <a name="protocols"></a>Protocolos
Para **Protocolos**, verifique se o protocolo usado pelos clientes está selecionado. Como o mesmo protocolo usado pelo cliente é usado para acessar a origem, é importante ter as portas de origem configuradas corretamente na seção anterior. O ponto de extremidade da CDN ouve apenas as portas HTTP e HTTPS (80 e 443) padrão, independentemente das portas de origem.

Vamos voltar ao nosso exemplo hipotético com http:\//www.contoso.com:8080/file.txt.  Como você se lembrará, a Contoso especificou *8080* como sua porta HTTP, mas suponhamos também que eles especificaram *44300* como sua porta HTTPS.  Se eles tiverem criado um ponto de extremidade chamado *contoso*, seu nome de host do ponto de extremidade de CDN será *contoso.azureedge.net*.  Uma solicitação de http:\//contoso.azureedge.net/file.txt é uma solicitação HTTP, portanto o ponto de extremidade usará HTTP na porta 8080 para recuperá-la da origem.  Uma solicitação segura via HTTPS, https:\//contoso.azureedge.net/file.txt, faria com que o ponto de extremidade usasse HTTPS na porta 44300 ao recuperar o arquivo da origem.

#### <a name="origin-host-header"></a>Cabeçalho de host de origem
O **Cabeçalho de host de origem** é o valor do cabeçalho de host enviado para a origem com cada solicitação.  Na maioria dos casos, ele deverá ser igual ao **Nome do host de origem** verificado anteriormente.  Em geral, um valor incorreto nesse campo não causará o status 404, mas, provavelmente, causará outros status 4xx, dependendo do que for esperado pela origem.

#### <a name="origin-path"></a>Caminho de origem
Por fim, devemos verificar nosso **Caminho de origem**.  Por padrão, ele está em branco.  Esse campo apenas deverá ser usado se você quiser restringir o escopo dos recursos hospedados na origem que deseja disponibilizar na CDN.  

No exemplo de ponto de extremidade, queríamos que todos os recursos na conta de armazenamento estivessem disponíveis, então o **Caminho de origem** foi deixado em branco.  Isso significa que uma solicitação para https:\//cdndocdemo.azureedge.net/publicblob/lorem.txt resulta em uma conexão do ponto de extremidade para cdndocdemo.core.windows.net que solicita */publicblob/lorem.txt*.  Da mesma forma, uma solicitação para https:\//cdndocdemo.azureedge.net/donotcache/status.png resulta na solicitação pelo ponto de extremidade de */donotcache/status.png* da origem.

Mas e se você não quiser usar a CDN para cada caminho na sua origem?  Digamos que você queira expor o caminho *publicblob*.  Se inserirmos */publicblob* no campo **Caminho de origem**, isso fará com que o ponto de extremidade insira */publicblob* antes de todas as solicitações feitas para a origem.  Isso significa que a solicitação para https:\//cdndocdemo.azureedge.net/publicblob/lorem.txt agora pegará, na verdade, a parte da solicitação da URL, */publicblob/lorem.txt* e acrescentará */publicblob* ao início. Isso resulta em uma solicitação para */publicblob/publicblob/lorem.txt* da origem.  Se esse caminho não for resolvido para um arquivo real, a origem retornará um status 404.  Na verdade, a URL correta para recuperar lorem.txt neste exemplo seria https:\//cdndocdemo.azureedge.net/lorem.txt.  Observe que não incluímos o caminho */publicblob*, porque a parte da solicitação da URL é */lorem.txt* e o ponto de extremidade adiciona */publicblob*, fazendo com que */publicblob/lorem.txt* seja a solicitação transmitida para a origem.

