---
title: "Migrando as soluções EDI do BizTalk Server para os Serviços BizTalk: guia técnico | Microsoft Docs"
description: "Migrar EDI para MABS; Serviços BizTalk do Microsoft Azure"
services: biztalk-services
documentationcenter: na
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: 61c179fa-3f37-495b-8016-dee7474fd3a6
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: 1b70fc3d199d7f1521acb534dafec8fb3e69500e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="migrating-biztalk-server-edi-solutions-to-biztalk-services-technical-guide"></a>Migrando as soluções EDI do BizTalk Server aos Serviços BizTalk: guia técnico

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Autor: Tim Wieman e Nitin Mehrotra

Revisores: Karthik Bharthy

Escrito usando: Serviços BizTalk do Microsoft Azure – versão de fevereiro de 2014.

## <a name="introduction"></a>Introdução
O intercâmbio de dados eletrônico (EDI) é um dos meios mais utilizados por empresas para trocar dados eletronicamente, também conhecido como transações Business-to-Business ou B2B. BizTalk Server teve suporte a EDI por mais de uma década, desde a versão inicial do BizTalk Server. Com os Serviços BizTalk, a Microsoft continua o suporte às soluções EDI na plataforma Microsoft Azure. As transações B2B são em grande parte externas à organização e, portanto, sua implementação é mais fácil do que se fossem implementadas em uma plataforma de nuvem. O Microsoft Azure fornece essa funcionalidade por meio de Serviços BizTalk.

Embora alguns clientes considerem os Serviços BizTalk como uma plataforma de "ambiente intacto" para novas soluções EDI, vários clientes têm soluções EDI do BizTalk Server atuais que desejam migrar para o Azure. Como o EDI dos Serviços BizTalk foi desenvolvido com base nas mesmas entidades chaves da arquitetura do EDI do BizTalk Server (parceiros comerciais, entidades, acordos), é possível migrar artefatos EDI do BizTalk Server para os serviços BizTalk.

Este documento discute algumas das diferenças envolvidas na migração de artefatos do EDI do BizTalk Server aos Serviços BizTalk. Este documento presume um conhecimento prático do processamento de EDI do BizTalk Server e dos acordos entre parceiros comerciais. Para obter mais informações sobre o EDI do BizTalk Server, consulte [Gerenciamento de parceiros comerciais usando o BizTalk Server](https://msdn.microsoft.com/library/bb259970.aspx).

## <a name="which-version-of-biztalk-server-edi-artifacts-can-be-migrated-to-biztalk-services"></a>Qual versão dos artefatos de EDI do BizTalk Server pode ser migrada para os Serviços BizTalk?
O módulo de EDI do BizTalk Server foi significativamente aprimorado para o BizTalk Server 2010, quando foi remodelado para incluir parceiros, perfis e contratos. Os Serviços BizTalk usam o mesmo modelo para organizar os parceiros comerciais e as divisões de negócios dentro desses parceiros comerciais. Como resultado, migrar artefatos de EDI do BizTalk Server 2010 e versões posteriores para os Serviços BizTalk é um processo muito mais simples. Para migrar artefatos de EDI associados a versões anteriores ao BizTalk Server 2010, você deve primeiro atualizar para o BizTalk Server 2010 e, em seguida, migrar os artefatos de EDI para os Serviços BizTalk.

## <a name="scenariosmessage-flow"></a>Cenários/fluxo de mensagens
Assim como com o BizTalk Server, o processamento de EDI nos Serviços BizTalk é criado com base em uma solução de gerenciamento de parceiros comerciais (TPM). A solução TPM tem os seguintes componentes principais:

* Parceiros comerciais, que representam a organização em uma transação B2B.
* Perfis, que representam divisões em um parceiro comercial.
* Contratos de parceiros comerciais (ou acordos), que representam o acordo comercial entre dois parceiros/perfis.

A ilustração a seguir mostra as semelhanças, bem como as diferenças, entre uma solução EDI do BizTalk Server e a solução EDI dos Serviços BizTalk:

![][EDImessageflow]

As principais diferenças e semelhanças entre um fluxo de solução EDI no BizTalk Server e os nos Serviços BizTalk são:

* Assim como o BizTalk Server usa um pipeline EDIReceive para receber uma mensagem de EDI e um pipeline EDISend para enviar uma mensagem EDI, os Serviços BizTalk usam uma ponte de recepção EDI para receber e uma ponte de envio EDI para enviar mensagens EDI. No BizTalk Server, os pipelines estão associados com um contrato usando portas de envio ou recebimento. Nos Serviços BizTalk, o próprio acordo denota a ponte de envio ou recebimento.
* No BizTalk Server, depois que o pipeline EDIReceive processa a mensagem EDI, a mensagem é despejada em um banco de dados do SQL Server. O pipeline EdiSend pega a mensagem do banco de dados do SQL Server, a processa e a envia para o parceiro comercial.
  
    Nos Serviços BizTalk, depois que a ponte de recebimento de EDI processa a mensagem EDI, ela roteia a mensagem para um processo externo. O processo externo pode ser executado no Microsoft Azure ou local. O processo externo deve rotear a mensagem para a ponte de envio EDI; a ponte de envio não puxa a mensagem inerentemente. Depois de processar a mensagem, a ponte de envio EDI encaminha a mensagem para o parceiro comercial.

Os Serviços BizTalk fornecem uma experiência de configuração fácil de usar para criar e implantar um acordo de B2B entre parceiros comerciais rapidamente sem configurar qualquer instância de computação do Microsoft Azure (funções Web ou de trabalho), qualquer banco de dados do Microsoft Azure SQL ou qualquer conta de Armazenamento do Microsoft Azure. Cenários mais complexos exigirão a associação de fluxos de trabalho ou outro processamento de serviço "ao redor" de um acordo entre parceiros comerciais, ou seja, antes ou depois do processamento de ponte EDI do acordo. Em detalhes, as sequências de eventos a seguir ocorrem durante o processamento de uma mensagem EDI nos Serviços BizTalk.

1. Uma mensagem EDI é recebida do parceiro comercial Fabrikam.  Para receber mensagens EDI de parceiros comerciais, os Serviços BizTalk dão suporte a protocolos de transporte como FTP, SFTP, AS2 e HTTP/S.
2. O processamento do lado de recebimento do acordo entre parceiros comerciais desmonta a mensagem EDI para o formato XML.  Você pode rotear a mensagem EDI desmontada (no formato XML) para os pontos de extremidade do Barramento de Serviço como um ponto de extremidade de Retransmissão do Barramento de Serviço, Tópico do Barramento de Serviço, Fila do Barramento de Serviço ou Ponte dos Serviços BizTalk.
3. As mensagens XML desmontadas podem então ser recebidas no ponto de extremidade para processamento personalizado posterior.  Esses pontos de extremidade podem ser processados por um componente local ou uma instância de computação do Microsoft Azure para processar posteriormente a mensagem em um serviço de fluxo de trabalho do Windows (WF) ou o Windows Communication Foundation (WCF), por exemplo.
4. O "processamento no lado de envio" do acordo entre parceiros comerciais, em seguida, monta a mensagem XML no formato EDI e a envia ao parceiro comercial Contoso.  Para enviar mensagens EDI aos parceiros comerciais, os Serviços BizTalk dão suporte aos mesmos protocolos usados para receber mensagens EDI.

Este documento fornece mais orientações conceituais sobre a migração de alguns dos diferentes artefatos EDI do BizTalk Server para os Serviços BizTalk.

## <a name="sendreceive-ports-to-trading-partners"></a>Enviar/receber portas aos parceiros comerciais
No BizTalk Server, você define os locais e as portas de recebimento para receber mensagens EDI/XML dos parceiros comerciais e configura portas de envio para enviar mensagens EDI/XML ao parceiro comercial. Em seguida, você associa essas portas a um acordo entre parceiros comerciais usando o console de administração do BizTalk Server. Nos Serviços BizTalk, os locais em que você recebe mensagens de parceiros comerciais e em que você envia mensagens para parceiros comerciais são configurados como parte do acordo entre parceiros comerciais em si (como parte das configurações de transporte) no Portal dos Serviços BizTalk.  Então, não há realmente o conceito de "portas de envio" e "locais de recebimento", em si, nos Serviços BizTalk. Para obter mais informações, consulte [Criando contratos](https://msdn.microsoft.com/library/windowsazure/hh689908.aspx).

## <a name="pipelines-bridges"></a>Pipelines (pontes)
No EDI do BizTalk Server, pipelines são entidades de processamento de mensagem que também podem incluir lógica personalizada para recursos específicos de processamento, conforme exigido pelo aplicativo. Para os Serviços BizTalk, o equivalente seria uma ponte EDI. No entanto, nos Serviços BizTalk, por enquanto, as pontes EDI estão "fechadas".  Ou seja, você não pode adicionar suas próprias atividades personalizadas a uma ponte EDI. Qualquer processamento personalizado deve ser feito fora da ponte EDI no seu aplicativo, antes ou depois que a mensagem entra na ponte configurada como parte do acordo entre parceiros comerciais. Pontes EAI têm a opção de fazer o processamento personalizado. Se você quiser um processamento personalizado, você pode usar pontes EAI antes ou depois que a mensagem é processada pela ponte EDI. Para obter mais informações, consulte [Como incluir código personalizado em pontes](https://msdn.microsoft.com/library/azure/dn232389.aspx).

Você pode inserir um fluxo de publicação/assinatura com código personalizado e/ou usar Filas e Tópicos de mensagem do Barramento de Serviço antes que o acordo entre parceiros comerciais receba a mensagem ou depois do contrato processar a mensagem e encaminhá-la para um ponto de extremidade do Barramento de Serviço.

Consulte **Cenários/Fluxo de Mensagens** neste tópico para obter o padrão do fluxo de mensagens.

## <a name="agreements"></a>Contratos
Se você estiver familiarizado com os acordos entre parceiros comerciais do BizTalk Server 2010 usados para processamento de EDI, os acordos entre parceiros comerciais dos Serviços BizTalk são bastante familiares. A maioria das configurações de acordo é igual e usa a mesma terminologia. Em alguns casos, as configurações dos acordos são muito mais simples em comparação com as mesmas configurações no BizTalk Server. Os Serviços BizTalk do Microsoft Azure dão suporte ao transporte X12, EDIFACT e AS2.

Os Serviços BizTalk do Microsoft Azure também fornecem uma ferramenta de **Migração de dados de TPM** para migrar parceiros comerciais e acordos do módulo de parceiros comerciais do BizTalk Server para o Portal dos serviços BizTalk. A ferramenta de migração de dados de TPM está disponível como parte de um pacote de ferramentas, que pode ser baixado no [MABS SDK](http://go.microsoft.com/fwlink/p/?LinkId=235057). O pacote também inclui um arquivo Leia-me que fornece instruções sobre como usar a ferramenta e informações básicas de solução de problemas da ferramenta.

## <a name="schemas"></a>Esquemas
Os Serviços BizTalk oferecem esquemas EDI que podem ser usados em soluções de Serviços BizTalk.  Além disso, os esquemas de EDI do BizTalk Server também podem ser usados com os Serviços BizTalk porque o nó raiz do esquema EDI é o mesmo no BizTalk Server e nos Serviços BizTalk. Assim, você poderá levar seus esquemas EDI do BizTalk Server diretamente e usá-los nas soluções EDI que você desenvolver usando os Serviços BizTalk. Você também pode baixar os esquemas no [SDK MABS](http://go.microsoft.com/fwlink/p/?LinkId=235057).

## <a name="maps-transforms"></a>Mapas (transformações)
Mapas no BizTalk Server são chamados de transformações nos Serviços BizTalk. Migrar mapas do BizTalk Server aos Serviços BizTalk pode ser uma das tarefas mais complexas a serem executadas (dependendo da complexidade do mapa). A ferramenta de mapeamento usada para os Serviços BizTalk é diferente do mapeador do BizTalk. Embora o mapeador pareça igual, o formato de mapa subjacente é diferente. As functoids (chamadas **operações de mapa** nos Serviços BizTalk) disponíveis para os usuários também são diferentes.  Na prática, você não pode usar diretamente um mapa do BizTalk nos Serviços BizTalk. Além disso, nem todas as functoids disponíveis no BizTalk Server estão disponíveis como operações de mapa nos Serviços BizTalk.

### <a name="new-transform-operations"></a>Novas operações de transformação
Embora a lista de operações de mapa de transformação disponíveis possa parecer bastante diferente do mapeador do BizTalk Server, as transformações dos Serviços BizTalk têm novas maneiras de realizar as mesmas tarefas. Por exemplo, as transformações dos Serviços BizTalk têm **operações de lista** disponíveis. Isso não estava disponível no mapeador do BizTalk.  As **operações de lista** permitem criar e operar em uma "lista", em que uma lista é um conjunto de itens (também conhecidos como "linhas") e em que cada item pode ter vários membros (também conhecidos como "colunas").  Você pode classificar a lista, selecionar os itens com base em uma condição, etc.

Outro exemplo de uma nova funcionalidade nas transformações dos Serviços BizTalk são as **operações de loop**.  É difícil criar loops aninhados no mapeador do BizTalk Server.  Assim, as operações de mapa de loop são adicionadas para as transformações dos Serviços BizTalk.

Outro exemplo é a operação de mapa de expressão **If-Then-Else** .  Executar uma operação if-then-else era possível no mapeador do BizTalk, mas exigia várias functoids para realizar uma tarefa aparentemente simples.

### <a name="migrating-biztalk-server-maps"></a>Migrar mapas do BizTalk Server
Os Serviços BizTalk do Microsoft Azure fornecem uma ferramenta para migrar mapas do BizTalk Server para transformações dos Serviços BizTalk. **BTMMigrationTool** está disponível como parte do pacote de **Ferramentas** fornecido com o [ download do SDK dos Serviços BizTalk](http://go.microsoft.com/fwlink/p/?LinkId=235057). Para obter mais informações sobre a ferramenta, consulte [Converter um mapa do BizTalk para uma transformação dos Serviços BizTalk](https://msdn.microsoft.com/library/windowsazure/hh949812.aspx).

Você também pode examinar uma amostra por Sandro Pereira, MVP do BizTalk, sobre como [migrar mapas do BizTalk Server para transformações dos Serviços BizTalk](http://social.technet.microsoft.com/wiki/contents/articles/23220.migrating-biztalk-server-maps-to-windows-azure-biztalk-services-wabs-maps.aspx).

## <a name="orchestrations"></a>Orquestrações
Se você precisar migrar o processamento de orquestração do BizTalk Server para o Microsoft Azure, as orquestrações precisariam ser regravadas porque o Microsoft Azure não oferece suporte à execução de orquestrações do BizTalk Server.  Você poderia reescrever a funcionalidade de orquestração em um serviço do Windows Workflow Foundation 4.0 (WF4).  Isso seria uma regravação completa, pois não há nenhuma migração de orquestrações do BizTalk Server para o WF4 atualmente. Aqui estão alguns recursos do Windows Workflow:

* [*Como integrar um Serviço do Fluxo de Trabalho do WCF com Tópicos e Filas do Barramento de Serviço*](https://msdn.microsoft.com/library/azure/hh709041.aspx) de Paolo Salvatori. 
* [*Sessão* Criando aplicativos com o Windows Workflow Foundation e o Azure](http://go.microsoft.com/fwlink/p/?LinkId=237314) da conferência Build 2011.
* [*Centro de Desenvolvedores do Windows Workflow Foundation*](http://go.microsoft.com/fwlink/p/?LinkId=237315) no MSDN.
* [*Documentação do Windows Workflow Foundation 4 (WF4)*](https://msdn.microsoft.com/library/dd489441.aspx) no MSDN.

## <a name="other-considerations"></a>Outras considerações
A seguir estão algumas considerações que você deve fazer ao usar os Serviços BizTalk.

### <a name="fallback-agreements"></a>Acordos de fallback
O processamento de EDI do BizTalk Server tem o conceito de "Acordos de Fallback".  Os Serviços BizTalk ainda **não** têm um conceito de acordo de fallback.  Consulte os tópicos da documentação do BizTalk [A Função dos Acordos no Processamento de EDI](http://go.microsoft.com/fwlink/p/?LinkId=237317) e [Configurando as Propriedades dos Acordos Globais ou de Fallback](https://msdn.microsoft.com/library/bb245981.aspx) para obter informações sobre como os Acordos de Fallback são usados no BizTalk Server.

### <a name="routing-to-multiple-destinations"></a>Roteamento para vários destinos
As pontes dos Serviços BizTalk, em seu estado atual, não oferecem suporte para rotear mensagens para vários destinos usando um modelo de publicação-assinatura. Em vez disso, você pode rotear as mensagens de uma ponte dos Serviços BizTalk para um tópico do Barramento de Serviço, que pode ter várias assinaturas para receber a mensagem em mais de um ponto de extremidade.

## <a name="conclusion"></a>Conclusão
Os Serviços BizTalk do Microsoft Azure são atualizados em etapas regulares para adicionar mais recursos e capacidades. Com cada atualização, buscamos oferecer suporte a mais funcionalidades para facilitar a criação de soluções de ponta a ponta usando os Serviços BizTalk e outras tecnologias do Azure.

## <a name="see-also"></a>Consulte também
[Desenvolvimento de aplicativos empresariais com o Azure](https://msdn.microsoft.com/library/azure/hh674490.aspx)

[EDImessageflow]: ./media/biztalk-migrating-to-edi-guide/IC719455.png
