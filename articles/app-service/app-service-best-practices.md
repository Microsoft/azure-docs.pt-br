---
title: Práticas recomendadas - Serviço de Aplicativo do Azure
description: Aprenda as práticas recomendadas e solução de problemas do Serviço de Aplicativo do Azure.
services: app-service
documentationcenter: ''
author: dariagrigoriu
manager: erikre
editor: mollybos
ms.assetid: f3359464-fa44-4f4a-9ea6-7821060e8d0d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/01/2016
ms.author: dariagrigoriu
ms.custom: seodec18
ms.openlocfilehash: fc3749a9ebfbf0319a57b471b6fce9f62042ba27
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60852675"
---
# <a name="best-practices-for-azure-app-service"></a>Práticas Recomendadas para o Serviço de Aplicativo do Azure
Este artigo resume as práticas recomendadas para usar o [Serviço de Aplicativo do Azure](https://go.microsoft.com/fwlink/?LinkId=529714). 

## <a name="colocation"></a>Colocação
Quando recursos do Azure compondo uma solução, como um aplicativo Web e um banco de dados de recursos estão localizados em regiões diferentes, podem ocorrer os seguintes efeitos:

* Maior latência na comunicação entre recursos
* Encargos monetários para transferência de dados de saída entre regiões, como observado na [página de preços do Azure](https://azure.microsoft.com/pricing/details/data-transfers)

A colocação na mesma região é melhor para que os recursos do Azure que compõem uma solução como um aplicativo Web e um banco de dados ou uma conta de armazenamento usada para armazenar conteúdo ou dados. Durante a criação de recursos, verifique se eles estão na mesma região do Azure, a menos que você tenha motivos de design ou de negócios específicos para que eles não estejam. Você pode mover um aplicativo do Serviço de Aplicativo para a mesma região do banco de dados utilizando o [recurso de clonagem de Serviço de Aplicativo](app-service-web-app-cloning.md) atualmente disponível para aplicativos do Plano do Serviço de Aplicativo Premium.   

## <a name="memoryresources"></a>Quando aplicativos consomem mais memória do que o esperado
Quando você observar que um aplicativo consome mais memória do que o esperado, conforme indicado no monitoramento ou nas recomendações de serviço, considere o [recurso de Reparo Automático de Serviço de Aplicativo](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites). Uma das opções para o recurso de Reparo Automático é tomar ações personalizadas com base em um limite de memória. Ações abrangem o espectro de notificações de email a investigação por meio de despejo de memória no ponto de atenuação ao reciclar o processo de trabalho. A Recuperação Automática pode ser configurada por meio de web.config e por meio de uma interface do usuário amigável, conforme descrito nesta postagem de blog para a [Extensão de Site de Suporte de Serviço de Aplicativo](https://azure.microsoft.com/blog/additional-updates-to-support-site-extension-for-azure-app-service-web-apps).   

## <a name="CPUresources"></a>Quando aplicativos consomem mais CPU do que o esperado
Quando você observar que um aplicativo consome mais CPU que o esperado ou enfrenta picos de CPU repetidos, conforme indicado pelo monitoramento ou pelas recomendações serviço, considere escalar verticalmente ou horizontalmente o plano de Serviço de Aplicativo. Se seu aplicativo estiver com estado, escalar verticalmente será a única opção, enquanto que, se seu aplicativo estiver sem estado, escalar horizontalmente dará mais flexibilidade e maior potencial de escala. 

Para obter mais informações sobre os aplicativos "com estado" versus "sem estado", assista a este vídeo: [Planejar um aplicativo escalonável de várias camadas e de ponta a ponta no Serviço de Aplicativo do Azure](https://channel9.msdn.com/Events/TechEd/NorthAmerica/2014/DEV-B414#fbid=?hashlink=fbid). Para saber mais sobre como as opções de colocação em escala e dimensionamento automático, veja [Dimensionar um aplicativo Web no Serviço de Aplicativo do Azure](web-sites-scale.md).  

## <a name="socketresources"></a>Quando os recursos de soquete são exauridos
Uma razão comum para esgotar as conexões TCP de saída é o uso de bibliotecas de cliente que não são implementadas para reutilizar conexões TCP ou quando um protocolo de nível superior como HTTP - Keep-Alive não estiver sendo utilizado. Consulte a documentação para cada uma das bibliotecas referenciadas pelos aplicativos no seu Plano de Serviço de Aplicativo para garantir que eles são configurados ou acessados em seu código para reutilização eficiente de conexões de saída. Além disso, siga as diretrizes de documentação biblioteca de criação correta e versão ou de limpeza para evitar vazamento de conexões. Embora essas investigações de bibliotecas de cliente estejam em andamento, o impacto pode ser reduzido escalando horizontalmente para várias instâncias.

### <a name="nodejs-and-outgoing-http-requests"></a>Node.js e solicitações de http de saída
Ao trabalhar com Node.js e muitas solicitações de http de saída, lidar com HTTP - Keep-Alive é importante. Você pode usar o pacote [agentkeepalive](https://www.npmjs.com/package/agentkeepalive) `npm` para facilitar no seu código.

Sempre trate a resposta `http`, mesmo que não faça nada no manipulador. Se você não tratar corretamente a resposta, o aplicativo ficará parado, porque não há mais soquetes disponíveis.

Por exemplo, ao trabalhar com o pacote `http` ou `https`:

```javascript
const request = https.request(options, function(response) {
    response.on('data', function() { /* do nothing */ });
});
```

Se você estiver executando o Serviço de Aplicativo no Linux em um computador com vários núcleos, outra prática recomendada é usar PM2 para iniciar vários processos Node.js para executar seu aplicativo. Você pode fazer isso especificando um comando de inicialização para o seu contêiner.

Por exemplo, para iniciar quatro instâncias:

```
pm2 start /home/site/wwwroot/app.js --no-daemon -i 4
```

## <a name="appbackup"></a>Quando o backup de seu aplicativo começa a falhar
Os dois motivos mais comuns para as falhas no backup do aplicativo são: configurações de armazenamento inválidas e configuração de banco de dados inválida. Essas falhas normalmente ocorrem quando há alterações de recursos de armazenamento ou do banco de dados, ou alterações no modo de acesso desses recursos (por exemplo, credenciais atualizadas para o banco de dados selecionado nas configurações de backup). Os backups são normalmente executados com base em um agendamento e exigem acesso ao armazenamento (para gerar o backup dos arquivos) e aos bancos de dados (para copiar e ler o conteúdo a ser incluído no backup). O resultado da falha de acesso de qualquer um desses recursos seria uma falha de backup consistente. 

Quando ocorrerem falhas de backup, examine os resultados mais recentes para entender qual tipo de falha está ocorrendo. Para falhas de acesso ao armazenamento, revise e atualize as configurações de armazenamento usadas na configuração do backup. No caso de falhas de acesso ao banco de dados, analise e atualize suas cadeias de conexão como parte das configurações do aplicativo; em seguida, atualize a configuração de backup para incluir corretamente os bancos de dados necessários. Para saber mais sobre o backup do aplicativo, confira [Fazer backup de um aplicativo Web no Serviço de Aplicativo do Azure](manage-backup.md).

## <a name="nodejs"></a>Quando novos aplicativos Node.js são implantados no Serviço de Aplicativo do Azure
A configuração padrão do Serviço de Aplicativo do Azure para aplicativos Node.js destina-se a melhor atender às necessidades dos aplicativos mais comuns. Se a configuração do seu aplicativo Node.js pode se beneficiar de ajustes personalizados para melhorar o desempenho ou otimizar o uso de recursos de memória/CPU/rede, veja [Práticas recomendadas e guia de solução de problemas para aplicativos Node no Serviço de Aplicativo do Azure](app-service-web-nodejs-best-practices-and-troubleshoot-guide.md). Este artigo descreve as configurações de iisnode que podem precisar ser definidas seu aplicativo Node.js, descreve os vários cenários ou problemas que seu aplicativo pode enfrentar e mostra como resolver esses problemas.

