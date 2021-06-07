---
title: Solucionar problemas no ITSMC
description: Saiba como resolver problemas comuns no Conector de Gerenciamento de Serviços de TI.
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: 7094bf07453c06831fecfa2056480bf498b26a72
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102041613"
---
# <a name="troubleshoot-problems-in-it-service-management-connector"></a>Solucionar problemas no Conector de Gerenciamento de Serviços de TI

Este artigo discute problemas comuns no Conector de Gerenciamento de Serviços de TI (ITSMC) e como solucioná-los.

Azure Monitor notifica proativamente sobre alertas quando encontra condições importantes em seus dados de monitoramento. Esses alertas ajudam a identificar e resolver problemas antes que os usuários do seu sistema os observem.

Você pode selecionar como deseja receber alertas. Você pode escolher email, SMS ou webhook, ou até mesmo automatizar uma solução. 

Uma alternativa é ser notificada por meio de ITSMC. O ITSMC oferece a opção de enviar alertas para um sistema de tíquetes externos, como o ServiceNow.

## <a name="use-the-dashboard-to-analyze-incident-and-change-request-data"></a>Use o painel para analisar os dados de incidentes e de solicitação de alteração

Dependendo de sua configuração quando você configurar uma conexão, o ITSMC poderá sincronizar até 120 dias de incidentes e dados de solicitação de alteração. Para obter o esquema de registro de log para esses dados, consulte o artigo [dados sincronizados do produto ITSM](./itsmc-synced-data.md) .

Você pode visualizar o incidente e alterar os dados de solicitação usando o painel do ITSMC:

![Captura de tela que mostra o painel do ITSMC.](media/itsmc-overview/itsmc-overview-sample-log-analytics.png)

O painel também fornece informações sobre o status do conector. Você pode usar essas informações como um ponto de partida para analisar problemas com as conexões. Para obter mais informações, consulte [investigação de erros usando o painel](./itsmc-dashboard.md).

## <a name="use-service-map-to-visualize-incidents"></a>Usar Mapa do Serviço para visualizar incidentes

Você também pode visualizar os incidentes sincronizados em relação aos computadores afetados no Mapa do Serviço.

O Mapa do Serviço descobre automaticamente os componentes de aplicativos em sistemas Windows e Linux e mapeia a comunicação entre os serviços. Ele permite que você exiba seus servidores da maneira como você os considera: como sistemas interconectados que fornecem serviços críticos. 

Mapa do Serviço mostra conexões entre servidores, processos e portas em qualquer arquitetura conectada a TCP. Além da instalação de um agente, nenhuma configuração é necessária. Para obter mais informações, consulte [usando mapa do serviço](../vm/service-map.md).

Se você estiver usando Mapa do Serviço, poderá exibir os itens da central de serviços criados nas soluções de ITSM (gerenciamento de serviços de ti), conforme mostrado neste exemplo:

![Captura de tela que mostra a exibição de Log Analytics.](media/itsmc-overview/itsmc-overview-integrated-solutions.png)

## <a name="resolve-problems"></a>Resolver problemas

As seções a seguir identificam sintomas comuns, possíveis causas e resoluções. 

### <a name="a-connection-to-the-itsm-system-fails-and-you-get-an-error-in-saving-connection-message"></a>Uma conexão com o sistema ITSM falha e você recebe uma mensagem "erro ao salvar a conexão"

**Causa**: a causa pode ser uma destas opções:

* As credenciais estão incorretas.
* Os privilégios são insuficientes.
* Para conexões Service Manager: o aplicativo Web foi implantado incorretamente.

**Resolução**:

* Para as conexões ServiceNow, Cherwell e Provance:
  * Certifique-se de que você inseriu corretamente o nome de usuário, a senha, a ID do cliente e o segredo do cliente para cada uma das conexões.  
  * Para o ServiceNow, verifique se você tem [privilégios suficientes](itsmc-connections-servicenow.md#install-the-user-app-and-create-the-user-role) no produto de ITSM correspondente.

* Para conexões Service Manager:  
  * Verifique se o aplicativo Web foi implantado com êxito e se a conexão híbrida foi criada. Para verificar se a conexão foi estabelecida com êxito com o computador Service Manager local, vá para a URL do aplicativo Web, conforme descrito na [documentação para fazer uma conexão híbrida](./itsmc-connections-scsm.md#configure-the-hybrid-connection).  

### <a name="duplicate-work-items-are-created"></a>Itens de trabalho duplicados são criados

**Causa**: a causa pode ser uma destas duas opções:

* Mais de uma ação de ITSM está definida para o alerta.
* O alerta foi resolvido.

**Resolução**: pode haver duas soluções:

* Verifique se você tem um único grupo de ações de ITSM por alerta.
* ITSMC não dá suporte a atualizações de status de itens de trabalho correspondentes quando um alerta é resolvido. Crie um novo item de trabalho resolvido.

### <a name="work-items-are-not-created"></a>Os itens de trabalho não são criados

**Causa**: pode haver vários motivos para esse sintoma:

* O código foi modificado no lado do ServiceNow.
* As permissões estão configuradas incorretamente.
* Os limites de taxa do ServiceNow são muito altos ou muito baixos.
* Um token de atualização expirou.
* ITSMC foi excluído.

**Resolução**: Verifique o [painel](itsmc-dashboard.md) e examine os erros na seção para obter o status do conector. Em seguida, examine os [erros comuns e suas resoluções](itsmc-dashboard-errors.md).

### <a name="you-cant-create-an-itsm-action-for-an-action-group"></a>Não é possível criar uma ação de ITSM para um grupo de ação

**Causa**: uma instância ITSMC recém-criada ainda não concluiu a sincronização inicial.

**Resolução**: examine os [erros comuns e suas resoluções](itsmc-dashboard-errors.md).

### <a name="sync-connection"></a>Sincronizar conexão 

**Causa**: pode haver vários motivos para esse sintoma:

* Os modelos não são mostrados como parte da definição de ação.
* Incedents/eventos não são criados no ServiceNow.

**Resolução**: [Sincronize o conector](itsmc-resync-servicenow.md).
