---
title: Solução de problemas – solucione problemas com a sua configuração do Azure Front Door Service | Microsoft Docs
description: Neste tutorial, você aprenderá como solucionar você mesmo alguns dos problemas comuns que pode enfrentar com o Front Door.
services: frontdoor
documentationcenter: ''
author: sharad4u
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2018
ms.author: sharadag
ms.openlocfilehash: 7a261d65a7bd3eea150dd764c65b94ddd47466b3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60736115"
---
# <a name="troubleshooting-common-routing-issues"></a>Solução de problemas comuns de roteamentos
Este artigo descreve como solucionar alguns dos problemas comuns de roteamento que você pode enfrentar para a sua configuração do Azure Front Door Service. 

## <a name="hostname-not-routing-to-backend-and-returns-400-status-code"></a>O nome do host não está sendo roteado para o back-end e retorna o código de Status 400


### <a name="symptom"></a>Sintoma
- Você criou um Front Door, mas uma solicitação ao host de Front-end está retornando um código de status HTTP 400.

  - Você criou um mapeamento DNS de um domínio personalizado para o host de front-end configurado. No entanto, enviar uma solicitação para o nome de host do domínio personalizado retorna um código de status HTTP 400 e não aparece rotear para os back-ends que você configurou.

### <a name="cause"></a>Causa
- Esse sintoma poderá acontecer se você não tiver configurado uma regra de roteamento para o domínio personalizado que você adicionou como um host de front-end. Uma regra de roteamento deve ser adicionada explicitamente àquele host de front-end, mesmo que já tenha sido configurada uma para o host de front-end no subdomínio do Front Door (*.azurefd.net) para o qual o seu domínio personalizado tem um mapeamento de DNS.

### <a name="troubleshooting-steps"></a>Etapas de solução de problemas
- Adicione uma regra de roteamento do domínio personalizado para o pool de back-end desejado.

## <a name="request-to-frontend-hostname-returns-404-status-code"></a>A solicitação ao nome do host do Front-End Retorna o Código de Status 404

### <a name="symptom"></a>Sintoma
- Você criou um Front Door e configurou um host de front-end, um pool de back-end com pelo menos um back-end e uma regra de roteamento que conecta o host de front-end ao pool de back-end. Seu conteúdo não parece estar disponível ao enviar uma solicitação para o host de front-end configurado, uma vez que o código de status HTTP 404 é retornado.

### <a name="cause"></a>Causa
Há várias causas possíveis para esse sintoma:
 - O back-end não é um back-end voltado para o público e não está visível ao serviço Front Door.

- O back-end está configurado incorretamente, o que está fazendo o serviço Front Door enviar a solicitação incorreta (ou seja, o back-end aceita apenas HTTP, mas você não desmarcou, permitindo solicitações HTTPS, portanto, o Front Door está tentando encaminhar solicitações HTTPS).
- O back-end está rejeitando o cabeçalho de host que foi enviado com a solicitação ao back-end.
- A configuração para o back-end ainda não foi totalmente implantada.

### <a name="troubleshooting-steps"></a>Etapas de solução de problemas
1. Tempo de implantação
    - Verifique se você já esperou cerca de 10 minutos para a configuração ser implantada.

2. Verificar as configurações de back-end
   - Navegue até o pool de back-end para o qual a solicitação deve estar roteando (depende de como você configurou a regra de roteamento) e verifique se que o _tipo de host de back-end_ e o nome de host de back-end estão corretos. Se o back-end for um host personalizado, verifique se você o escreveu corretamente. 

   - Verifique suas portas HTTP e HTTPS. Na maioria dos casos, 80 e 443 (respectivamente) estão corretas e nenhuma alteração é necessária. No entanto, há uma chance de que o back-end não esteja configurado assim e esteja escutando uma porta diferente.

     - Verifique o _Cabeçalho de host de back-end_ configurado para os back-ends para o qual o host de Front-end deve ser roteamento. Na maioria dos casos, esse cabeçalho deve ser igual a _Nome de host de back-end_. No entanto, um valor incorreto poderá causar vários códigos de status HTTP 4xx se o back-end esperar algo diferente. Se você inserir o endereço IP do seu back-end, poderá precisar definir o _Cabeçalho de host de back-end_ para o nome do host de back-end.


3. Verificar as configurações de regra de roteamento
     - Navegue até a regra de roteamento que deve fazer o roteamento do nome do host do Front-end em questão para um pool de back-end. Verifique se os protocolos aceitos estão configurados corretamente; se não estiverem, verifique se o protocolo que o Front Door usará ao encaminhar a solicitação está configurado corretamente. Os _protocolos aceitos_ determinam que solicitações o Front Door deve aceitar e o _Protocolo de encaminhamento_ na guia _Avançado_ determina qual protocolo o Front Door deve usar para encaminhar a solicitação para o back-end.
          - Por exemplo, se o back-end só aceitar solicitações HTTP, as seguintes configurações serão válidas:
               - Os _Protocolos aceitos_ são HTTP e HTTPS. O _Protocolo de encaminhamento_ é HTTP. Solicitação de correspondência não funcionará, já que o HTTPS é um protocolo permitido e, se uma solicitação tiver chegado como HTTPS, o Front Door tentará encaminhá-la usando HTTPS.

               - Os _Protocolos aceitos_ são HTTP. O _Protocolo de encaminhamento_ é solicitação de correspondência ou HTTPS.

   - Clique na guia _Avançado_ na parte superior do painel de configuração de regra de roteamento. _Reescrita de URL_ é desabilitada por padrão e você só deverá usar este campo se quiser restringir o escopo dos recursos hospedados pelo back-end que deseja disponibilizar. Quando desabilitado, o Front Door encaminhará o mesmo caminho de solicitação que ele recebe. É possível que esse campo esteja configurado incorretamente e o Front Door esteja solicitando um recurso do back-end que não está disponível, retornando, assim, um código de status HTTP 404.

