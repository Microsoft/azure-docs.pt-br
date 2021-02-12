---
title: Criar um aplicativo Azure Remote Rendering pronto para o comércio
description: Estratégias e considerações para a criação de um aplicativo pronto para uso comercial com o Azure Remote Rendering
author: FlorianBorn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.openlocfilehash: 7258b37c9d92f25227eac678dde77963e644e64b
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96483310"
---
# <a name="tutorial-creating-a-commercial-ready-azure-remote-rendering-application"></a>Tutorial: Criar um aplicativo Azure Remote Rendering pronto para o comércio

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
>
> * Gerenciamento de sessão para aplicativos comerciais
> * Acompanhamento de sessões para cobrança
> * Otimização da experiência do usuário referente ao tempo de carregamento da sessão
> * Considerações sobre a latência de rede

## <a name="prerequisites"></a>Pré-requisitos

* Este tutorial é baseado no [Tutorial: Como proteger o Azure Remote Rendering e o armazenamento de modelos](../security/security.md).

## <a name="introduction-to-commercial-readiness"></a>Introdução à preparação comercial

O Azure Remote Rendering expande o que é possível fazer com a realidade misturada. Depois que os itens básicos forem integrados à sua solução, haverá algumas outras considerações para garantir que a solução seja segura, escalonável e pronta para oferecer valor.

Este módulo apresenta algumas funcionalidades adicionais que talvez você precise considerar para seu aplicativo comercial.

Para obter uma visão geral ampla das melhores práticas de arquitetura de todo o sistema, acesse:

* [Centro de Arquitetura do Azure](/azure/architecture/)
* [Guia de Introdução para Desenvolvedores do Azure](../../../../guides/developer/azure-developer-guide.md)

## <a name="analytics"></a>Análise

A integração de ferramentas de análise pode ajudar a gerenciar, acompanhar e aprimorar sua solução.

Para obter uma lista abrangente dos recursos de análise disponíveis para você, acesse:

* [Serviços de Análise do Azure](https://azure.microsoft.com/product-categories/analytics/)

### <a name="tracking-usage-for-billing"></a>Como acompanhar o uso da cobrança

O acompanhamento do consumo do Azure Remote Rendering por várias equipes internas ou clientes externos passa a ser uma consideração importante, especialmente em situações de multilocatário.

Para conseguir isso, o Azure oferece um serviço chamado marcação de recursos, que associa o consumo do serviço Azure Remote Rendering a cada cliente.

Para obter mais informações sobre a nomenclatura e a marcação de recursos, um bom lugar para começar é:

* [Guia de decisão sobre nomenclatura e marcação de recursos](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json)

### <a name="diagnostics"></a>Diagnósticos

Ferramentas avançadas como o ETW (Rastreamento de Eventos para Windows) e o ETL (Log de Rastreamento de Eventos) facilitam a geração de eventos de rastreamento no aplicativo e podem ajudar a diagnosticar problemas de rede, de ingestão de conteúdo, de sessão, do aplicativo e outros que possam surgir em uma implantação de solução comercial.

Para obter mais informações, acesse:

* [Criar rastreamentos de desempenho do lado do cliente](../../../how-tos/performance-tracing.md)
* [Como coletar dados do ETW (Rastreamento de Eventos para Windows)](/visualstudio/profiling/how-to-collect-event-tracing-for-windows-etw-data)
* [Como usar o Portal de Dispositivos do Windows: Log](/windows/mixed-reality/using-the-windows-device-portal)

### <a name="usage-analysis"></a>Análise de uso

O Azure Application Insights ajuda você a entender como as pessoas usam seu aplicativo do Azure Remote Rendering. Toda vez que você atualiza seu aplicativo, pode avaliar como ele funciona para os usuários e aprimorar a solução de acordo. Com esse conhecimento, você pode tomar decisões controladas por dados sobre os próximos ciclos de desenvolvimento.

Para obter mais informações, acesse:

* [Análise de uso com o Application Insights](../../../../azure-monitor/app/usage-overview.md)

## <a name="fast-startup-time-strategies"></a>Estratégias de tempo de inicialização rápidas

Seu caso de uso pode exigir uma inicialização rápida começando na inicialização do aplicativo e terminando na exibição do modelo 3D. Por exemplo, durante uma reunião importante em que é essencial ter tudo funcionando com antecedência. Outro exemplo é durante uma análise de modelo 3D do CAD, em que a iteração rápida de design entre um aplicativo CAD e realidade misturada é o segredo da eficiência.

O Azure Remote Rendering exige modelos 3D pré-processados e, no momento, o Azure leva vários minutos para criar uma sessão e carregar um modelo para renderização. Simplificar e agilizar esse processo o máximo possível exige a preparação dos dados do modelo 3D e da sessão ARR com antecedência.

As sugestões compartilhadas aqui atualmente não fazem parte do Azure Remote Rendering padrão, mas você pode implementá-las por conta própria para obter tempos de inicialização mais rápidos.

### <a name="initiate-early"></a>Início antecipado

Para reduzir o tempo de inicialização, a solução mais simples é mover a criação e a inicialização da sessão o mais rápido possível no fluxo de trabalho de usuário. Uma estratégia é inicializar a sessão assim que você souber que uma sessão ARR será necessária. Isso geralmente ocorrerá quando o usuário começar a carregar um modelo 3D no Armazenamento de Blobs do Azure para uso com o Azure Remote Rendering. Nesse caso, a criação e a inicialização da sessão podem ser iniciadas ao mesmo tempo que o upload do modelo 3D, de modo que ambos os fluxos de trabalho sejam executados em paralelo.

Esse processo pode ser simplificado ainda mais, garantindo que os contêineres de entrada e saída do Armazenamento de Blobs do Azure escolhidos estejam no mesmo data center regional do Azure Remote Rendering.

### <a name="scheduling"></a>Agendamento

Se você souber que terá uma necessidade futura do Azure Remote Rendering, agende uma data e uma hora específicas para iniciar a sessão do Azure Remote Rendering.

Essa opção poderá ser oferecida por meio de um portal da Web em que as pessoas podem carregar um modelo 3D e agendar um horário para vê-lo no futuro. Esse também será um bom lugar para solicitar outras preferências, como a renderização [*Standard*](../../../reference/vm-sizes.md) ou [*Premium*](../../../reference/vm-sizes.md). A renderização *Premium* poderá ser adequada se houver um desejo de mostrar uma combinação de ativos em que o tamanho ideal é mais difícil de ser determinado automaticamente ou uma necessidade de garantir que a região do Azure tenha VMs disponíveis no horário especificado.

### <a name="session-pooling"></a>Pool de sessão

Nas situações mais exigentes, outra opção é o pool de sessão, em que uma ou mais sessões são criadas e inicializadas o tempo todo. Isso cria um pool de sessão para uso imediato por um usuário solicitante. A desvantagem dessa abordagem é que, quando a VM é inicializada, a cobrança pelo serviço é iniciada. Talvez não seja econômico manter um pool de sessão em execução o tempo todo, mas com base na análise, talvez seja possível prever cargas de pico ou ele pode ser combinado com a estratégia de agendamento acima para prever quando as sessões serão necessárias e aumentar e reduzir o pool de sessão de acordo.

Essa estratégia também ajuda a otimizar a escolha entre as sessões *Standard* e *Premium* de maneira mais dinâmica, pois é muito mais rápido alternar entre os dois tipos em uma sessão de usuário individual, como no caso em que um modelo de complexidade *Premium* é exibido primeiro, seguido de um que pode funcionar no *Standard*. Se essas sessões de usuário forem muito demoradas, poderá haver reduções significativas de custo.

Para saber mais sobre as sessões do Azure Remote Rendering, confira:

* [Sessões do Remote Rendering](../../../concepts/sessions.md)

## <a name="standard-vs-premium-server-size-routing-strategies"></a>Estratégias de roteamento de VM Standard vs. Estratégias de roteamento de tamanho de servidor Premium

A necessidade de escolher se um tamanho de servidor *Standard* ou *Premium* apresenta um desafio na criação da experiência do usuário e do sistema de ponta a ponta. Embora o uso apenas de sessões *Premium* seja uma opção, as sessões *Standard* usam muito menos recursos de computação do Azure e são menos caras do que as sessões *Premium*. Isso gera uma forte motivação para o uso das sessões *Standard* sempre que possível e o uso das sessões *Premium* somente quando necessário.

Aqui, compartilhamos várias opções, da menos à mais abrangente, para abordar o intuito de gerenciar as opções de sessão.

### <a name="use-only-standard-or-premium"></a>Usar somente Standard ou Premium

Se você tiver certeza de que as suas necessidades ficarão *sempre* abaixo do limite entre *Standard* e *Premium*, isso simplificará consideravelmente sua decisão. Basta usar o *Standard*. Tenha em mente que o impacto na experiência do usuário será significativo se a complexidade total da soma dos ativos carregados for rejeitada como muito complexa para uma sessão *Standard*.

Da mesma forma, se você espera que uma grande parte dos usos exceda o limite entre *Standard* e *Premium* ou se o custo não é um fator importante no caso de uso, a escolha de *Premium* sempre também é uma opção para simplificar.

### <a name="ask-the-user"></a>Perguntar ao usuário

Se você quiser dar suporte ao *Standard* e ao *Premium*, a maneira mais fácil de determinar o tipo de sessão a ser instanciado será perguntar ao usuário quando ele selecionar os ativos 3D a serem exibidos. O desafio com essa abordagem é que ela exige que o usuário entenda a complexidade do ativo 3D ou, até mesmo, de vários ativos que serão exibidos. Normalmente, isso não é recomendado por esse motivo. Se o usuário fizer uma seleção errada e escolher *Standard*, a experiência do usuário resultante poderá ser comprometida em um momento inoportuno.

### <a name="analyze-the-3d-model"></a>Analisar o modelo 3D

Outra abordagem relativamente simples é analisar a complexidade dos ativos 3D selecionados. Se a complexidade do modelo estiver abaixo do limite para o *Standard*, inicie uma sessão *Standard*; caso contrário, inicie uma sessão *Premium*. Aqui, o desafio é que uma só sessão pode acabar sendo usada para exibir vários modelos, alguns dos quais podem exceder o limite de complexidade de uma sessão *Standard*, resultando na incapacidade de usar diretamente a mesma sessão para uma sequência de diferentes ativos 3D.

### <a name="automatic-switching"></a>Alternância automática

A alternância automática entre as sessões *Standard* e *Premium* pode fazer muito sentido em um design de sistema que também inclua o pool de sessão. Essa estratégia permite uma otimização extra da utilização de recursos. À medida que o usuário carrega modelos para exibição, a complexidade é determinada e o tamanho de sessão correto é solicitado do serviço de pool de sessão.

## <a name="working-with-networks"></a>Como trabalhar com redes

### <a name="diagnostics"></a>Diagnósticos

O Azure Remote Rendering exige uma conexão rápida com a Internet com baixa latência. A qualidade da rede do usuário pode ter um impacto significativo na qualidade da experiência. Considerando que os clientes provavelmente têm configurações de rede diferentes e, apenas ocasionalmente, uma latência de rede insatisfatória, as ferramentas de diagnóstico são fundamentais.  

Para garantir que você possa fornecer uma experiência consistente de alta qualidade, recomendamos integrar ferramentas de análise do lado do servidor e do cliente nos seus aplicativos do Azure Remote Rendering. Se fizer isso, você terá as informações necessárias para diagnosticar e atenuar os problemas de rede que os clientes possam estar enfrentando.

### <a name="client-network-configurations"></a>Configuração de rede do cliente

Um dos maiores desafios no desenvolvimento de soluções de colaboração robustas implantadas em uma ampla variedade de ambientes empresariais é estar preparado para diferentes configurações de firewall empresarial e topologia de rede que os clientes podem usar.

Muitas empresas bloqueiam todo o tráfego ponto a ponto em uma LAN. Isso dificulta o aproveitamento da simplicidade e a experiência do usuário simplificada da descoberta automática de LAN, a fim de estabelecer uma sessão compartilhada local entre todas as instâncias descobertas do aplicativo de realidade misturada.

Outros pontos de falha potenciais são os roteadores configurados para limitar intencionalmente a largura de banda e os firewalls que bloqueiam a maioria das portas TCP/IP.

Sempre que você planejar usar o Azure Remote Rendering em uma rede desconhecida, recomendaremos o seguinte:

* Forneça uma lista de verificação pré-reunião para avaliar a preparação da rede.
* Verifique se o data center regional apropriado pode atender à solicitação.
* Permita tempo suficiente para diagnosticar os problemas.
* Traga um hotspot móvel com um plano de dados de alta largura de banda como backup.

### <a name="end-to-end-bandwidth"></a>Largura de banda de ponta a ponta

É importante avaliar as funcionalidades de largura de banda para cada base da rede que possam existir entre a VM do Azure Remote Rendering e o cliente final. Tenha em mente que o segmento de rede do data center do Azure para o ISP do cliente pode ser mais um fator limitante do que o ISP para o cliente. O Teste de Velocidade de Download de Blob pode ser usado para ajudar a diagnosticar esses problemas.

### <a name="bandwidth-competition"></a>Competição de largura de banda

Ao projetar seu aplicativo de realidade misturada, tenha em mente que diferentes recursos do aplicativo podem competir com o Azure Remote Rendering pela largura de banda. O exemplo imprevisto mais provável é quando muitos participantes em uma só sala estão esperando usar simultaneamente o ARR para ver um ativo 3D. Cada base do fluxo de dados de rede precisará ter capacidade para transportar a soma total de todos os fluxos de dados do ARR combinados.

Outros exemplos incluem vídeo transmitido, uploads em segundo plano simultâneos de outros tipos de conteúdo relacionado e bate-papo de voz, especialmente quando há muitos participantes e o sistema usa uma abordagem ponto a ponto distribuída, em oposição a um servidor de mixagem de áudio na abordagem intermediária.

Para obter mais informações sobre a análise de rede, veja:

* [Teste de Velocidade de Download do Azure Storage Blob](https://www.azurespeed.com/Azure/Download)
* [Estatísticas de latência de ida e volta da Rede do Azure](../../../../networking/azure-network-latency.md)
* [Rastreamentos de desempenho do servidor](../../../overview/features/performance-queries.md)
* [Rastreamentos de desempenho do lado do cliente](../../../how-tos/performance-tracing.md)

## <a name="collaboration-considerations"></a>Considerações sobre colaboração

Alguns dos usos mais úteis do Azure Remote Rendering envolvem a colaboração entre vários participantes que veem a mesma experiência 3D simultaneamente. Nessas sessões compartilhadas, é importante reconhecer que cada participante precisará estar em uma sessão exclusiva do Azure Remote Rendering, independentemente de estarem no mesmo lugar na mesma rede ou não.

Isso é verdade, porque cada participante está realmente vendo a mesma experiência de diferentes posições estratégicas, o que exige que os mesmos ativos 3D sejam renderizados com base em cada uma dessas perspectivas simultaneamente.

### <a name="multiple-azure-remote-rendering-sessions"></a>Sessões múltiplas do Azure Remote Rendering

Se você pretende dar suporte a experiências compartilhadas com o Azure Remote Rendering, os sistemas que você colocou em vigor para criar e gerenciar as sessões ARR precisarão estar preparados para iniciar várias sessões. Essas sessões poderão precisar ser inicializadas em diferentes data centers do Azure se os participantes estiverem geograficamente dispersos.

O sistema também precisa gerenciar a possibilidade de que um ou mais participantes estejam em uma região geográfica que atualmente não tenha suporte do Azure Remote Rendering ou que atualmente não tenha instâncias de VM disponíveis do Azure Remote Rendering.

Esse gerenciamento de várias sessões simultâneas pode ser ainda mais simplificado quando combinado com o pool de sessão e outras estratégias discutidas neste documento.

### <a name="azure-blob-storage-considerations"></a>Considerações sobre o Armazenamento de Blobs do Azure

Todas as sessões ARR simultâneas podem referenciar o mesmo URI de SAS para o modelo convertido a ser exibido. Isso possibilita carregar e converter os ativos 3D desejados uma vez e compartilhá-los em todas as sessões. Isso é especialmente verdadeiro quando os participantes estão colocalizados e usam o mesmo data center quando não há nenhuma preocupação com o desempenho relacionada ao Armazenamento de Blobs do Azure estar localizado em um data center diferente do servidor do Azure Remote Rendering e do usuário.

Se os ativos 3D forem normalmente carregados para uma só sessão de exibição e depois descartados, como em uma sessão de revisão de design, a região geográfica do Armazenamento de Blobs do Azure em relação ao servidor do Azure Remote Rendering também será menos crítica.

No entanto, para ativos 3D que serão usados repetidamente, como em um caso de uso de treinamento, recomendamos manter ativos de 3D prontos para o uso no Armazenamento de Blobs em cada data center regional em que você planeja usar o Azure Remote Rendering. Isso pode ser automatizado por meio da redundância do Armazenamento do Azure. A CDN geralmente é usada para essa finalidade também, mas essa ainda não é uma opção para o Azure Remote Rendering.

Para mais informações:

* [Experiências compartilhadas em realidade misturada](/windows/mixed-reality/shared-experiences-in-mixed-reality)
* [Redundância do Armazenamento do Azure](../../../../storage/common/storage-redundancy.md)

## <a name="managing-model-access"></a>Como gerenciar o acesso ao modelo

O aproveitamento total do Azure Remote Rendering exige uma consideração cuidadosa da infraestrutura de ponta a ponta para gerenciamento de modelos 3D.

Uma vantagem de usar o Azure Remote Rendering é que ativos 3D grandes nunca precisam ser transmitidos diretamente para o dispositivo de realidade misturada antes de serem exibidos.  Além disso, quando um ativo 3D é carregado e convertido para uso com o Azure Remote Rendering, qualquer número de usuários pode compartilhar essa única instância do modelo 3D.

### <a name="considerations-for-3d-model-access"></a>Considerações sobre o acesso ao modelo 3D

Veja a seguir algumas considerações importantes ao escolher sua estratégia de acesso ao modelo.

Com base no caso de uso previsto, determine o melhor local ou a combinação de locais a fim de permitir que um usuário selecione os ativos 3D para exibição. Algumas opções comuns são:

* Diretamente na experiência de realidade misturada
* Por meio de um portal da Web complementar
* Em um desktop ou um aplicativo móvel complementar

Se o seu caso de uso tiver padrões de uso em que o mesmo ativo 3D possa ser carregado várias vezes, o back-end acompanhará quais modelos já foram convertidos para uso com ARR, de modo que um modelo seja pré-processado apenas uma vez para várias seleções futuras. Um exemplo de revisão de design é quando uma equipe tem acesso a um ativo 3D original comum. É esperado que cada membro da equipe examine o modelo usando o ARR em algum momento no fluxo de trabalho. Somente a primeira exibição vai disparar a etapa de pré-processamento. As exibições a seguir pesquisarão o arquivo pós-processado associado no contêiner de saída SAS.

Dependendo do caso de uso, provavelmente, o ideal será determinar e persistir o tamanho correto do servidor do Azure Remote Rendering, *Standard* ou *Premium*, para cada ativo 3D ou grupo de ativos que serão exibidos juntos na mesma sessão.  

### <a name="on-device-model-selection-list"></a>Lista de seleção de modelo no dispositivo

Em muitos casos de uso, como um treinamento, diretrizes de tarefas ou um aplicativo de marketing, o conjunto de ativos 3D a ser normalmente exibido no Azure Remote Rendering pode ser razoavelmente estático. Nessas situações, um conjunto organizado de ativos 3D pode ser convertido previamente e disponibilizado por meio de um banco de dados que contém as informações necessárias para preencher uma lista de seleção dos ativos coletados. Esses dados podem então ser recuperados do aplicativo de realidade misturada para preencher um menu de seleção.

Isso pode ir mais longe, oferecendo também uma forma de carregar ativos 3D particulares, exclusivos a cada indivíduo ou grupo. Essa lista de ativos particulares pode então ser combinada com a lista dos ativos comuns coletados na experiência do usuário para a escolha de ativos 3D para exibição.

### <a name="on-device-onedrive-access"></a>Acesso do OneDrive no dispositivo

Considerando que um seletor de arquivos do OneDrive é inserido nativamente nos dispositivos de realidade misturada da Microsoft, a seleção de ativos 3D no dispositivo do OneDrive é interessante, especialmente para casos de uso em que é comum carregar modelos 3D diferentes ou modificados. Nesse cenário, o usuário selecionará um ou mais ativos 3D por meio do seletor de arquivos do OneDrive no seu aplicativo de realidade misturada. Os ativos 3D serão transferidos para um contêiner de entrada SAS, convertidos em um contêiner de saída SAS e anexados à sessão do ARR. O ideal é que o aplicativo de realidade misturada invoque um processo baseado em nuvem para executar essas etapas, em vez de mover todo o conteúdo do OneDrive para o dispositivo e novamente para o Armazenamento de Blobs do Azure.

Essa abordagem pode ir mais longe, persistindo uma associação entre os ativos 3D que foram exibidos anteriormente, de modo que, quando o mesmo modelo for escolhido novamente no OneDrive, o aplicativo possa ignorar o processo de conversão e carregar diretamente o ativo 3D convertido associado por meio do respectivo URI de SAS.

Para mais informações:

* [Modelo do Microsoft Power Automate para a replicação do OneDrive para o Armazenamento do Azure](https://flow.microsoft.com/galleries/public/templates/2f90b5d3-029b-4e2e-ad37-1c0fe6d187fe/when-a-file-is-uploaded-to-onedrive-copy-it-to-azure-storage-container/)
* [Visão geral da API de Armazenamento de Arquivos do OneDrive](/graph/onedrive-concept-overview)

### <a name="direct-cad-access"></a>Acesso direto ao CAD

Um caso de uso interessante para a realidade misturada são as revisões de design do trabalho em andamento do CAD. Nesse cenário, o tempo de carregamento mais rápido da área de trabalho para a realidade misturada é fundamental. Uma solução ideal pode envolver o desenvolvimento de plug-ins para aplicativos CAD específicos. Esses plug-ins gerenciarão diretamente cada aspecto do processo de carregamento, conversão e exibição:

* Forneça uma experiência do usuário para:
  * Emparelhar o aplicativo CAD com um dispositivo específico de realidade misturada (uma vez).
  * Solicitar que a geometria selecionada seja exibida nesse dispositivo de realidade misturada.
* Se a sessão do Azure Remote Rendering ainda não estiver em execução, crie-a para que ela possa ser processada em paralelo ao carregar e converter o arquivo CAD
* Normalizar dados de geometria do CAD para um dos formatos compatíveis com o Azure Remote Rendering
* Transmitir os dados normalizados diretamente para o contêiner de entrada do Armazenamento de Blobs do Azure
* Iniciar o processo de conversão de modelo
* Vincular o URI de SAS do contêiner de saída do modelo à sessão do Azure Remote Rendering
* Notificar o aplicativo de realidade misturada emparelhado de que o modelo está disponível e pronto para exibição e fornecer o URI de SAS do contêiner de saída, de modo que o aplicativo possa anexá-lo à sessão.

Uma abordagem muito mais simples, mas um pouco menos simplificada, pode automatizar o processo de salvar o modelo 3D em um disco rígido local e iniciar um processo para transmitir esse arquivo salvo para o contêiner de entrada SAS.

### <a name="azure-marketplace"></a>Azure Marketplace

Muitos clientes empresariais determinam que o Azure Stack pode ser implantado nas respectivas contas e credenciais próprias do Azure por motivos de segurança. Para tornar isso possível, considere a possibilidade de empacotar seu aplicativo gerenciado do Azure, de modo que ele possa ser publicado no Azure Marketplace como uma Oferta de Aplicativo Azure.

Para mais informações:

* [Azure Marketplace](https://azure.microsoft.com/marketplace/)
* [Tutorial: Publicar Aplicativos Gerenciados do Azure no Marketplace](../../../../marketplace/create-new-azure-apps-offer.md)

### <a name="security"></a>Segurança

É fundamental criar sua solução do Azure Remote Rendering de ponta a ponta para a segurança desde o início. Há muitos aspectos da segurança a serem considerados no design da solução de ponta a ponta, incluindo:

* Estratégias de autenticação
* Gerenciamento de acesso – grupos, políticas e permissões
* Multilocação
* Armazenamento de dados e criptografia de transferência
* Tokens de uso temporários
* DDoS (negação de serviço distribuído)
* Detecção de ameaças
* VPNs e redes seguras
* Firewalls
* Gerenciamento de certificados e chaves secretas
* Vulnerabilidades de aplicativos e explorações

Para autenticação, recomendamos migrar a maior parte possível do gerenciamento de sessão e a autenticação do ARR para um serviço Web do Azure. Isso resultará em uma solução mais bem gerenciada e mais segura.

Para mais informações:

* [Autenticação de serviço do Azure AD](../../../../spatial-anchors/concepts/authentication.md?tabs=csharp#azure-ad-service-authentication)
* [Fortalecer sua postura de segurança com o Azure](https://azure.microsoft.com/overview/security/)
* [Segurança de nuvem](https://azure.microsoft.com/product-categories/security/)