---
title: Solucionando problemas dos pontos de extremidade do Azure CDN que retornam o status 404 | Microsoft Docs
description: "Solucione problemas nos códigos de resposta 404 com pontos de extremidade da CDN do Azure."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: b588a1eb-ab69-4fc7-ae4d-157c3e46f4a8
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: f59fbd18413fb44026d8c92b7f6940ed2f8a00a8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshooting-cdn-endpoints-returning-404-statuses"></a>Solucionando problemas de pontos de extremidade CDN que retornam o status 404
Este artigo ajuda a solucionar problemas com [pontos de extremidade CDN](cdn-create-new-endpoint.md) que retornam erros 404.

Se precisar de mais ajuda em qualquer momento neste artigo, você pode contatar os especialistas do Azure nos [fóruns do Azure e do Stack Overflow no MSDN](https://azure.microsoft.com/support/forums/). Como alternativa, você também pode registrar um incidente de suporte do Azure. Acesse o [site de Suporte do Azure](https://azure.microsoft.com/support/options/) e clique em **Obter Suporte**.

## <a name="symptom"></a>Sintoma
Você criou um perfil CDN e um ponto de extremidade, mas seu conteúdo não parece estar disponível na CDN.  Os usuários que tentam acessar seu conteúdo por meio da URL da CDN recebem códigos de status HTTP 404. 

## <a name="cause"></a>Causa
Há várias causas possíveis, incluindo:

* A origem do arquivo não é visível para a CDN
* O ponto de extremidade está configurado incorretamente, fazendo com que a CDN faça uma pesquisa no lugar errado
* O host está rejeitando o cabeçalho de host da CDN
* O ponto de extremidade não teve tempo para ser propagado por toda a CDN

## <a name="troubleshooting-steps"></a>Etapas para solucionar problemas
> [!IMPORTANT]
> Depois de criar um ponto de extremidade CDN, ele não estará imediatamente disponível para uso, pois o registro demora um pouco para ser propagado pela CDN.  Para perfis da <b>CDN do Azure da Akamai</b> , a propagação normalmente é concluída em um minuto.  Para perfis da <b>CDN do Azure da Verizon</b>, a propagação geralmente é concluída em 90 minutos, mas em alguns casos pode levar mais tempo.  Se você concluir as etapas descritas neste documento e ainda obter respostas 404, considere aguardar algumas horas para verificar novamente antes de abrir um tíquete de suporte.
> 
> 

### <a name="check-the-origin-file"></a>Verificar o arquivo de origem
Primeiro, devemos verificar se o arquivo que desejamos armazenar em cache está disponível em nossa origem e se está acessível publicamente.  A maneira mais rápida de fazer isso é abrir um navegador em uma sessão In-Private ou anônima e navegar diretamente até o arquivo.  Basta digitar ou colar a URL na caixa de endereço e ver se isso resulta no arquivo esperado.  Neste exemplo, vou usar um arquivo existente em uma conta do Armazenamento do Azure, disponível em `https://cdndocdemo.blob.core.windows.net/publicblob/lorem.txt`.  Como você pode ver, ele é aprovado no teste.

![Sucesso!](./media/cdn-troubleshoot-endpoint/cdn-origin-file.png)

> [!WARNING]
> Embora essa seja a maneira mais rápida e fácil de verificar se o arquivo está disponível publicamente, algumas configurações de rede em sua organização poderiam dar a impressão de que esse arquivo está publicamente disponível quando, na verdade, só está visível para os usuários de sua rede (mesmo que ele esteja hospedado no Azure).  Se você tiver um navegador externo no qual você poderá realizar esse teste, como um dispositivo móvel que não está conectado à rede de sua organização ou uma máquina virtual no Azure, isso será melhor.
> 
> 

### <a name="check-the-origin-settings"></a>Verificar as configurações de origem
Agora que verificamos se o arquivo está disponível publicamente na Internet, devemos verificar nossas configurações de origem.  No [Portal do Azure](https://portal.azure.com), navegue até o seu perfil CDN e clique no ponto de extremidade que você está solucionando.  Na folha **Ponto de Extremidade** resultante, clique na origem.  

![Folha do ponto de extremidade com a origem realçada](./media/cdn-troubleshoot-endpoint/cdn-endpoint.png)

A folha **Origem** é exibida. 

![Folha da origem](./media/cdn-troubleshoot-endpoint/cdn-origin-settings.png)

#### <a name="origin-type-and-hostname"></a>Tipo e nome do host da origem
Verifique se o **Tipo de origem** está correto e verifique o **Nome do host da origem**.  No meu exemplo, `https://cdndocdemo.blob.core.windows.net/publicblob/lorem.txt`, a parte do nome do host da URL é `cdndocdemo.blob.core.windows.net`.  Como você pode ver na captura de tela, isso está correto.  Para as origens do Armazenamento do Azure, Aplicativo Web e Serviço de Nuvem, o campo **Nome do host da origem** é uma lista suspensa e, portanto, não precisamos nos preocupar em escrevê-lo com a ortografia correta.  No entanto, se você estiver usando uma origem personalizada, será *importantíssimo* escrever o nome do host corretamente.

#### <a name="http-and-https-ports"></a>Portas HTTP e HTTPS
O outro item a ser verificado aqui são as **portas HTTP** e **HTTPS**.  Na maioria dos casos, as portas 80 e 443 estão corretas e não será necessária nenhuma alteração.  No entanto, se o servidor de origem estiver escutando em uma porta diferente, isso precisará ser representado aqui.  Caso você não tenha certeza disso, basta examinar a URL do arquivo de origem.  As especificações de HTTP e HTTPS especificam as portas 80 e 443 como os padrões. Em minha URL, `https://cdndocdemo.blob.core.windows.net/publicblob/lorem.txt`, uma porta não é especificada, o padrão de 443 é adotado e minhas configurações estão corretas.  

No entanto, digamos que a URL do arquivo de origem testado anteriormente seja `http://www.contoso.com:8080/file.txt`.  Observe o `:8080` no final do segmento do nome do host.  Isso informa ao navegador que ele deverá usar a porta `8080` para se conectar ao servidor Web em `www.contoso.com`, portanto, você precisará inserir 8080 no campo **Porta HTTP**.  É importante observar que essas configurações de porta só afetam a porta usada pelo ponto de extremidade para recuperar informações da origem.

> [!NOTE]
> **CDN do Azure da Akamai** não permitem o intervalo de portas TCP completo para origens.  Para obter uma lista das portas de origem que não são permitidas, consulte [CDN do Azure a partir das Portas de Origem Permitidas Akamai](https://msdn.microsoft.com/library/mt757337.aspx).  
> 
> 

### <a name="check-the-endpoint-settings"></a>Verificar as configurações de ponto de extremidade
Novamente na folha **Ponto de extremidade**, clique no botão **Configurar**.

![Folha do ponto de extremidade com o botão Configurar realçado](./media/cdn-troubleshoot-endpoint/cdn-endpoint-configure-button.png)

A folha **Configurar** do ponto de extremidade é exibida.

![Configurar folha](./media/cdn-troubleshoot-endpoint/cdn-configure.png)

#### <a name="protocols"></a>Protocolos
Para **Protocolos**, verifique se o protocolo usado pelos clientes está selecionado.  O mesmo protocolo usado pelo cliente será usado para acessar a origem; portanto, é importante ter as portas de origem configuradas corretamente na seção anterior.  O ponto de extremidade ouve apenas as portas HTTP e HTTPS (80 e 443) padrão, independentemente das portas de origem.

Vamos voltar ao nosso exemplo hipotético com `http://www.contoso.com:8080/file.txt`.  Como você se lembrará, a Contoso especificou `8080` como sua porta HTTP, mas suponhamos também que eles especificaram `44300` como sua porta HTTPS.  Se eles tiverem criado um ponto de extremidade chamado `contoso`, seu nome de host do ponto de extremidade de CDN será `contoso.azureedge.net`.  Uma solicitação de `http://contoso.azureedge.net/file.txt` é uma solicitação HTTP, portanto o ponto de extremidade usará HTTP na porta 8080 para recuperá-la da origem.  Uma solicitação segura via HTTPS, `https://contoso.azureedge.net/file.txt`, faria com que o ponto de extremidade usasse HTTPS na porta 44300 ao recuperar o arquivo da origem.

#### <a name="origin-host-header"></a>Cabeçalho de host de origem
O **Cabeçalho de host de origem** é o valor do cabeçalho de host enviado para a origem com cada solicitação.  Na maioria dos casos, ele deverá ser igual ao **Nome do host de origem** verificado anteriormente.  Em geral, um valor incorreto nesse campo não causará o status 404, mas, provavelmente, causará outros status 4xx, dependendo do que for esperado pela origem.

#### <a name="origin-path"></a>Caminho de origem
Por fim, devemos verificar nosso **Caminho de origem**.  Por padrão, ele está em branco.  Esse campo apenas deverá ser usado se você quiser restringir o escopo dos recursos hospedados na origem que deseja disponibilizar na CDN.  

Por exemplo, em meu ponto de extremidade, como eu queria que todos os recursos em minha conta de armazenamento estivessem disponíveis, deixei o **Caminho de origem** em branco.  Isso significa que uma solicitação para `https://cdndocdemo.azureedge.net/publicblob/lorem.txt` resulta em uma conexão do meu ponto de extremidade a `cdndocdemo.core.windows.net` que solicita `/publicblob/lorem.txt`.  Da mesma forma, uma solicitação de `https://cdndocdemo.azureedge.net/donotcache/status.png` resulta na solicitação de `/donotcache/status.png` a partir da origem pelo ponto de extremidade.

Mas e se não quiser usar a CDN para cada caminho na origem?  Digamos que eu quisesse apenas expor o caminho `publicblob` .  Se eu inserir */publicblob* no campo **Caminho de origem**, isso fará com que o ponto de extremidade insira */publicblob* antes de todas as solicitações feitas para a origem.  Isso significa que a solicitação de `https://cdndocdemo.azureedge.net/publicblob/lorem.txt` agora usará, na verdade, a parte da solicitação da URL, `/publicblob/lorem.txt`, e acrescentará `/publicblob` ao início. Isso resulta em uma solicitação de `/publicblob/publicblob/lorem.txt` da origem.  Se esse caminho não for resolvido para um arquivo real, a origem retornará um status 404.  Na verdade, a URL correta para recuperar lorem.txt neste exemplo seria `https://cdndocdemo.azureedge.net/lorem.txt`.  Observe que não incluímos o caminho */publicblob*, porque a parte da solicitação da URL é `/lorem.txt` e o ponto de extremidade adiciona `/publicblob`, fazendo com que `/publicblob/lorem.txt` seja a solicitação transmitida para a origem.

