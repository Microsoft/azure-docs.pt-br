---
title: Solucionar problemas comuns de atualização de dispositivo para o Hub IoT do Azure | Microsoft Docs
description: Este documento fornece uma lista de dicas e truques para ajudar a corrigir muitos possíveis problemas que você pode ter com a atualização de dispositivo para o Hub IoT.
author: lichris
ms.author: lichris
ms.date: 2/17/2021
ms.topic: troubleshooting
ms.service: iot-hub-device-update
ms.openlocfilehash: 3c1f60b214397b1f97e0157b5beca32d504102d6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102030623"
---
# <a name="device-update-for-iot-hub-troubleshooting-guide"></a>Atualização de dispositivo para o guia de solução de problemas do Hub IoT

Este documento lista algumas perguntas comuns e problemas que os usuários de atualização de dispositivo relataram. À medida que a atualização do dispositivo progride por meio da visualização pública, este guia de solução de problemas será atualizado periodicamente com novas perguntas e soluções. Se você encontrar um problema que não aparece neste guia de solução de problemas, consulte a seção [contacting suporte da Microsoft](#contact) para documentar sua situação.

## <a name="importing-updates"></a><a name="import"></a>Importando atualizações

### <a name="q-im-having-trouble-connecting-my-device-update-instance-to-my-iot-hub-instance"></a>P: Estou tendo problemas para conectar a instância de atualização de dispositivo à minha instância do Hub IoT.
_Verifique se as rotas de mensagens do Hub IoT estão configuradas corretamente, de acordo com a documentação de [recursos de atualização de dispositivo](./device-update-resources.md) ._

### <a name="q-im-encountering-a-role-related-error-error-message-in-azure-portal-or-a-403-api-error"></a>P: estou encontrando um erro relacionado à função (mensagem de erro em portal do Azure ou um erro de API 403).
_Talvez você não tenha permissões de acesso configuradas corretamente. Verifique se você configurou as permissões de acesso corretamente de acordo com a documentação de [controle de acesso à atualização do dispositivo](./device-update-control-access.md) ._

### <a name="q-im-encountering-a-500-type-error-when-importing-content-to-the-device-update-service"></a>P: estou encontrando um erro de tipo 500 ao importar o conteúdo para o serviço de atualização de dispositivo.
_Um código de erro no intervalo de 500 pode indicar um problema com o serviço de atualização de dispositivo. Aguarde 5 minutos e tente novamente. Se o mesmo erro persistir, siga as instruções na seção [contatando suporte da Microsoft](#contact) para arquivar uma solicitação de suporte com a Microsoft._

### <a name="q-im-encountering-an-error-code-when-importing-content-and-would-like-to-parse-it"></a>P: estou encontrando um código de erro ao importar o conteúdo e gostaria de analisá-lo.
_Consulte a documentação [códigos de erro de atualização do dispositivo](./device-update-error-codes.md) para obter informações sobre a análise de códigos de erro._

## <a name="device-failures"></a><a name="device-failure"></a>Falhas do dispositivo

### <a name="q-how-can-i-ensure-my-device-is-connected-to-device-update-for-iot-hub"></a>P: como posso garantir que meu dispositivo esteja conectado à atualização do dispositivo para o Hub IoT?
_Você pode verificar se o dispositivo está conectado à atualização do dispositivo verificando se ele aparece na seção dispositivos "desagrupados" no modo de exibição de conformidade de portal do Azure._

### <a name="q-one-or-more-of-my-devices-is-failing-to-update"></a>P: um ou mais dos meus dispositivos estão falhando ao atualizar.
_Há muitas causas raiz possíveis para uma falha de atualização do dispositivo. Valide se o dispositivo é: 1) conectado à sua instância do Hub IoT, 2) conectado à sua instância de atualização de dispositivo e 3) o serviço de otimização de entrega (DO) está em execução. Se todos os três forem verdadeiros para seu dispositivo, siga as instruções na seção [contatando suporte da Microsoft](#contact) para arquivar uma solicitação de suporte com a Microsoft._

## <a name="deploying-an-update"></a><a name="deploy"></a> Implantando uma atualização

### <a name="q-ive-deployed-an-update-to-my-devices-but-the-compliance-status-says-it-isnt-on-the-latest-update-what-should-i-do"></a>P: implantei uma atualização para meus dispositivos, mas o status de conformidade diz que ela não está na última atualização. O que devo fazer?
_O status de conformidade do dispositivo pode levar até 5 minutos para ser atualizado. Aguarde e verifique novamente._
### <a name="q-my-devices-deployment-status-shows-incompatible-what-should-i-do"></a>P: o status de implantação do meu dispositivo mostra incompatível, o que devo fazer?
_As propriedades de fabricante e modelo de um dispositivo de destino podem ter sido alteradas depois de conectar o dispositivo ao Hub IoT, fazendo com que o dispositivo agora seja considerado incompatível com o conteúdo de atualização da implantação atual._

_Verifique a [interface do Adu Core](./device-update-plug-and-play.md) para ver qual fabricante e modelo seu dispositivo está relatando para o serviço de atualização de dispositivo e verifique se ele corresponde ao fabricante e ao modelo especificado no [manifesto de importação](./import-concepts.md) do conteúdo de atualização que está sendo implantado. Você pode alterar essas propriedades para um determinado dispositivo usando o [arquivo de configuração de atualização de dispositivo](./device-update-configuration-file.md)._

### <a name="q-i-see-my-deployment-is-in-active-stage-but-none-of-my-devices-are-in-progress-with-the-update-what-should-i-do"></a>P: vejo que minha implantação está no estágio "ativo", mas nenhum dos meus dispositivos está "em andamento" com a atualização. O que devo fazer?
_Verifique se a data de início da implantação não está definida no futuro. Quando você cria uma nova implantação, a data de início da implantação é padronizada para o dia seguinte como uma proteção, a menos que você a altere explicitamente. Você pode aguardar a chegada da data de início da implantação ou cancelar a implantação em andamento e criar uma nova implantação com a data de início desejada._

### <a name="q-im-trying-to-group-my-devices-but-i-dont-see-the-tag-in-the-drop-down-when-creating-a-group"></a>P: Estou tentando agrupar meus dispositivos, mas não vejo a marca na lista suspensa ao criar um grupo.
_Verifique se você configurou corretamente as rotas de mensagens em seu hub IoT de acordo com a documentação de [recursos de atualização de dispositivo](./device-update-resources.md) . Você precisará marcar o dispositivo novamente depois de configurar a rota._

_Outra causa raiz pode ser que você aplicou a marca antes de conectar o dispositivo à atualização do dispositivo para o Hub IoT. Verifique se o dispositivo já está conectado à atualização do dispositivo. Você pode verificar se o dispositivo está conectado à atualização do dispositivo para o Hub IoT verificando se ele aparece em dispositivos "desagrupados" no modo de exibição de conformidade. Adicione temporariamente uma marca de um valor diferente e, em seguida, adicione a marca desejada novamente quando o dispositivo estiver conectado._

_Se você estiver usando o DPS (serviço de provisionamento de dispositivos), certifique-se de marcar seus dispositivos depois que eles forem provisionados e não durante o processo de criação do dispositivo. Se você já tiver marcado o dispositivo durante a etapa de criação do dispositivo, será necessário marcar temporariamente seu dispositivo com um valor diferente depois que ele for provisionado e, em seguida, adicionar a marca desejada novamente._

### <a name="q-my-deployment-completed-successfully-but-some-devices-failed-to-update"></a>P: minha implantação foi concluída com êxito, mas alguns dispositivos falharam ao atualizar.
_Isso pode ter sido causado por um erro do lado do cliente nos dispositivos com falha. Consulte a seção falhas de dispositivo deste guia de solução de problemas._

### <a name="q-i-encountered-an-error-in-the-ux-when-trying-to-initiate-a-deployment"></a>P: Eu encontrei um erro no UX ao tentar iniciar uma implantação.
_Isso pode ter sido causado por um bug de serviço/UX ou por um problema de permissões de API. Siga as instruções na seção [contatando suporte da Microsoft](#contact) para arquivar uma solicitação de suporte com a Microsoft._

### <a name="q-i-started-a-deployment-but-it-isnt-reaching-an-end-state"></a>P: comecei uma implantação, mas ela não está atingindo um estado final.
_Isso pode ter sido causado por um problema de desempenho de serviço, um bug de serviço ou um bug de cliente. Repita a implantação após 10 minutos. Se você encontrar o mesmo problema, faça pull dos logs de dispositivo e consulte a seção falhas de dispositivo deste guia de solução de problemas. Se o mesmo problema persistir, siga as instruções na seção [contatando suporte da Microsoft](#contact) para arquivar uma solicitação de suporte com a Microsoft._

## <a name="downloading-updates-onto-devices"></a><a name="download"></a> Baixando atualizações em dispositivos

### <a name="q-how-do-i-resume-a-download-when-a-device-has-reconnected-after-a-period-of-disconnection"></a>P: Como fazer retomar um download quando um dispositivo for reconectado após um período de desconexão?
_O download será retomado automaticamente quando a conectividade for restaurada em um período de 24 horas. Após 24 horas, o download precisará ser reiniciado pelo usuário._
## <a name="using-microsoft-connected-cache-mcc"></a><a name="mcc"></a> Usando o MCC (cache conectado da Microsoft)

### <a name="q-i-am-encountering-an-issue-when-attempting-to-deploy-the-mcc-module-on-my-iot-edge-device"></a>P: estou encontrando um problema ao tentar implantar o módulo MCC em meu dispositivo de IoT Edge.
_Consulte a [documentação do IOT Edge]() para implantar módulos de borda em dispositivos IOT Edge. Você pode verificar se o módulo MCC está sendo executado com êxito em seu IoT Edge dispositivo navegando até http://localhost:5100/Summary ._
### <a name="q-one-of-my-iot-devices-is-attempting-to-download-an-update-through-mcc-but-is-failing"></a>P: um dos meus dispositivos IoT está tentando baixar uma atualização por meio do MCC, mas está falhando.
_Há vários problemas que podem estar fazendo com que um dispositivo IoT falhe ao se conectar ao MCC. Para diagnosticar o problema, colete os logs do cliente e do Nginx do dispositivo com falha (consulte a seção [contatando suporte da Microsoft](#contact) para obter instruções sobre como coletar logs do cliente)._

_O dispositivo pode estar falhando ao extrair o conteúdo da Internet para passar para seu módulo MCC porque a URL que ele está usando não é permitida. Para determinar se, você precisará verificar suas variáveis de ambiente de IoT Edge no portal do Azure._
## <a name="contacting-microsoft-support"></a><a name="contact"></a> Contatando Suporte da Microsoft

Se você encontrar problemas que não podem ser resolvidos usando as perguntas frequentes acima, você pode arquivar uma solicitação de suporte com Suporte da Microsoft por meio da interface portal do Azure. Dependendo de qual categoria você indica que seu problema pertence, você pode ser solicitado a coletar e compartilhar dados adicionais para ajudar a Suporte da Microsoft investigar seu problema. 

Consulte abaixo para obter instruções sobre como coletar cada tipo de dados. Você pode usar [Devices]() para verificar se há informações adicionais na resposta de conteúdo da API.

Além disso, as informações a seguir podem ser úteis para restringir a causa raiz do seu problema:
* Que tipo de dispositivo você está tentando atualizar (Azure Percept, gateway de IoT Edge, outro)
* Qual tipo de cliente de atualização de dispositivo você está usando (simulador baseado em imagem, baseado em pacote)
* Qual sistema operacional seu dispositivo está executando
* Detalhes sobre a arquitetura do dispositivo
* Se você usou com êxito a atualização do dispositivo para atualizar um dispositivo antes

Se você tiver uma das informações acima disponíveis, inclua-as em sua descrição do problema.

### <a name="collecting-client-logs"></a>Coletando logs do cliente

* No dispositivo Raspberry Pi, há dois conjuntos de logs encontrados aqui:

    ```markdown
    /adu/logs
    ```

    ```markdown
    /var/cache/do-client-lite/log
    ```

* Para o cliente empacotado, os logs são encontrados aqui:

    ```markdown
    /var/log/adu
    ```

    ```markdown
    /var/cache/do-client-lite/log
    ```

* Para o simulador, os logs são encontrados aqui:

    ```markdown
    /tmp/aduc-logs
    ```

### <a name="error-codes"></a>Códigos do Erro
Você pode ser solicitado a fornecer códigos de erro ao relatar um problema relacionado à importação de uma atualização, a uma falha do dispositivo ou à implantação de uma atualização.

Os códigos de erro podem ser obtidos examinando a interface [ADUCoreInterface](./device-update-plug-and-play.md) . Consulte a documentação de [códigos de erro de atualização do dispositivo](./device-update-error-codes.md) para obter informações sobre como analisar códigos de erro para diagnóstico automático e solução de problemas.

### <a name="trace-id"></a>ID de rastreamento
Você pode ser solicitado a fornecer uma ID de rastreamento ao relatar um problema relacionado à importação ou implantação de uma atualização.

A ID de rastreamento para uma determinada ação de usuário pode ser encontrada na resposta da API ou na seção Histórico de importação da interface do usuário do portal do Azure. 

Atualmente, as IDs de rastreamento para ações de implantação só podem ser acessadas por meio da resposta da API.

### <a name="deployment-id"></a>ID de Implantação
Você pode ser solicitado a fornecer uma ID de implantação ao relatar um problema relacionado à implantação de uma atualização.

A ID de implantação é criada pelo usuário ao chamar a API para iniciar uma implantação.

Atualmente, as IDs de implantação para implantações iniciadas na interface do usuário portal do Azure são geradas automaticamente e não são apresentadas ao usuário.

### <a name="iot-hub-instance-name"></a>Nome da instância do Hub IoT
Você pode ser solicitado a fornecer o nome da instância do Hub IoT ao relatar um problema relacionado a falhas de dispositivo ou implantar uma atualização.

O nome do Hub IoT é escolhido pelo usuário quando o primeiro é provisionado.

### <a name="device-update-account-name"></a>Nome da conta de atualização do dispositivo
Você pode ser solicitado a fornecer o nome da conta de atualização do dispositivo ao relatar um problema relacionado à importação de uma atualização, falhas do dispositivo ou à implantação de uma atualização.

O nome da conta de atualização do dispositivo é escolhido pelo usuário ao se inscrever no serviço pela primeira vez. Mais informações podem ser encontradas na documentação de [recursos de atualização de dispositivo](./device-update-resources.md) .

### <a name="device-update-instance-name"></a>Nome da instância de atualização do dispositivo
Você pode ser solicitado a fornecer o nome da sua instância de atualização do dispositivo ao relatar um problema relacionado à importação de uma atualização, falhas do dispositivo ou à implantação de uma atualização.

O nome da instância de atualização de dispositivo é escolhido pelo usuário quando é provisionado pela primeira vez. Mais informações podem ser encontradas na documentação de [recursos de atualização de dispositivo](./device-update-resources.md) .

### <a name="device-id"></a>ID do Dispositivo
Você pode ser solicitado a fornecer uma ID de dispositivo ao relatar um problema relacionado a falhas de dispositivo ou implantar uma atualização.

A ID do dispositivo é definida pelo cliente quando o dispositivo é provisionado pela primeira vez. Ele também pode ser recuperado do dispositivo do dispositivo.

### <a name="update-id"></a>ID da atualização
Você pode ser solicitado a fornecer uma ID de atualização ao relatar um problema relacionado à implantação de uma atualização.

A ID de atualização é definida pelo cliente ao iniciar uma implantação.

### <a name="nginx-logs"></a>Logs do Nginx
Você pode ser solicitado a fornecer logs do Nginx ao relatar um problema relacionado ao cache conectado da Microsoft.

### <a name="adu-conftxt"></a>ADU-conf.txt
Você pode ser solicitado a fornecer o arquivo de configuração de atualização de dispositivo ("adu-conf.txt") ao relatar um problema relacionado à implantação de uma atualização.

O arquivo de configuração é opcional e criado pelo usuário seguindo as instruções na documentação de [configuração de atualização do dispositivo](./device-update-configuration-file.md) .

### <a name="import-manifest"></a>Importar manifesto
Você pode ser solicitado a fornecer seu arquivo de manifesto de importação ao relatar um problema relacionado à importação ou implantação de uma atualização.

O manifesto de importação é um arquivo criado pelo cliente ao importar conteúdo de atualização para o serviço de atualização de dispositivo.

**[Próxima etapa: Saiba mais sobre códigos de erro de atualização de dispositivo](.\device-update-error-codes.md)**