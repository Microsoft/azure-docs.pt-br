---
title: Lista de verificação de desempenho e escalabilidade do Armazenamento do Azure | Microsoft Docs
description: Uma lista de verificação de práticas comprovadas para uso com o Armazenamento do Azure no desenvolvimento de aplicativos de alto desempenho.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/07/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: ee216bd4d6994179e347465c30039f2f8e293c85
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68233005"
---
# <a name="microsoft-azure-storage-performance-and-scalability-checklist"></a>Lista de verificação de desempenho e escalabilidade do Armazenamento do Microsoft Azure

Desde os serviços de armazenamento do Microsoft Azure, a Microsoft desenvolveu diversas práticas comprovadas para usar esses serviços de modo a obter muito rendimento. Este artigo consolida as práticas mais importantes na forma de uma lista estilo lista de verificação. A finalidade deste artigo é ajudar os desenvolvedores de aplicativos a verificar se eles estão usando as práticas comprovadas com o armazenamento do Azure, bem como ajudá-lo a identificar outras práticas comprovadas que eles podem adotar. Este artigo não tem como objetivo cobrir todas as possibilidades de otimização de desempenho e escalabilidade. Aqui, não abordamos as práticas que apresentam pouco impacto ou que não se aplicam em larga escala. Na medida em que é possível prever o comportamento do aplicativo durante a criação, é útil seguir essas práticas desde o início para evitar problemas de desempenho.  

Todos os desenvolvedores de aplicativos que usam o armazenamento do Azure devem levar algum tempo para ler este artigo e verificar se o aplicativo segue cada uma das práticas comprovadas listadas abaixo.  

## <a name="checklist"></a>Lista de verificação

Este artigo organiza as práticas comprovadas nos grupos a seguir. As práticas comprovadas aplicam-se a:  

* Todos os serviços de armazenamento do Azure (blobs, tabelas, filas e arquivos)
* Blobs
* Tabelas
* Filas  

| Concluído | Área | Categoria | Pergunta |
| --- | --- | --- | --- |
| &nbsp; | Todos os serviços |Metas de escalabilidade |[Seu aplicativo foi criado para evitar a abordagem de metas de escalabilidade?](#subheading1) |
| &nbsp; | Todos os serviços |Metas de escalabilidade |[A convenção de nomenclatura foi projetada para permitir melhor balanceamento de carga?](#subheading47) |
| &nbsp; | Todos os serviços |Rede |[Os dispositivos cliente têm largura de banda suficiente e baixa latência para alcançar o desempenho necessário?](#subheading2) |
| &nbsp; | Todos os serviços |Rede |[Os dispositivos cliente têm um link de alta qualidade?](#subheading3) |
| &nbsp; | Todos os serviços |Rede |[O aplicativo cliente está "próximo" à conta de armazenamento?](#subheading4) |
| &nbsp; | Todos os serviços |Distribuição de conteúdo |[Você usa um CDN para distribuir conteúdo?](#subheading5) |
| &nbsp; | Todos os serviços |Acesso direto do cliente |[Você usa SAS e CORS para permitir o acesso direto ao armazenamento, em vez de usar um proxy?](#subheading6) |
| &nbsp; | Todos os serviços |Cache |[Seu aplicativo armazena em cache os dados que são usados com frequência e que raramente mudam?](#subheading7) |
| &nbsp; | Todos os serviços |Cache |[Seu aplicativo compila atualizações, armazenando-as em cache no cliente e carregando-as em grandes conjuntos?](#subheading8) |
| &nbsp; | Todos os serviços |Configuração .NET |[Você configurou seu cliente para usar uma quantidade suficiente de conexões simultâneas?](#subheading9) |
| &nbsp; | Todos os serviços |Configuração .NET |[Você configurou o .NET para usar uma quantidade suficiente de threads?](#subheading10) |
| &nbsp; | Todos os serviços |Configuração .NET |[Você usa o .NET 4.5 ou posterior, versões com recurso aprimorado de coleta de lixo?](#subheading11) |
| &nbsp; | Todos os serviços |Paralelismo |[Você garantiu a associação adequada do paralelismo para não carregar as funcionalidades do cliente nem as metas de escalabilidade?](#subheading12) |
| &nbsp; | Todos os serviços |Ferramentas |[Você está usando a última versão das bibliotecas e ferramentas fornecidas pela Microsoft?](#subheading13) |
| &nbsp; | Todos os serviços |Novas tentativas |[Você usa uma política de nova tentativa de retirada exponencial para diminuir os erros e a ocorrência de tempos limite?](#subheading14) |
| &nbsp; | Todos os serviços |Novas tentativas |[Seu aplicativo evita novas tentativas para erros que não admitem novas tentativas?](#subheading15) |
| &nbsp; | Blobs |Metas de escalabilidade |[Você tem um grande número de clientes que acessam um único objeto simultaneamente?](#subheading46) |
| &nbsp; | Blobs |Metas de escalabilidade |[Seu aplicativo segue a meta de largura de banda ou escalabilidade operacional para um único blob?](#subheading16) |
| &nbsp; | Blobs |Cópia de blobs |[Seu método de cópia de blobs é eficiente?](#subheading17) |
| &nbsp; | Blobs |Cópia de blobs |[Você usa o AzCopy para copiar blobs em massa?](#subheading18) |
| &nbsp; | Blobs |Cópia de blobs |[Você está usando a função de importação/exportação do Azure para transferir grandes volumes de dados?](#subheading19) |
| &nbsp; | Blobs |Uso de metadados |[Você armazena os metadados sobre blobs usados com frequência?](#subheading20) |
| &nbsp; | Blobs |Carregamento rápido |[Ao tentar carregar um blob rapidamente, você carrega blocos paralelamente?](#subheading21) |
| &nbsp; | Blobs |Carregamento rápido |[Ao tentar carregar muitos blobs rapidamente, você carrega blocos paralelamente?](#subheading22) |
| &nbsp; | Blobs |Tipo de blob correto |[Você usa blobs de página ou de bloco quando necessário?](#subheading23) |
| &nbsp; | Tabelas |Metas de escalabilidade |[Você leva as metas de escalabilidade em consideração para entidades por segundo?](#subheading24) |
| &nbsp; | Tabelas |Configuração |[Você usa JSON para suas solicitações de tabela?](#subheading25) |
| &nbsp; | Tabelas |Configuração |[Você desativou o Nagle para melhorar o desempenho de pequenas solicitações?](#subheading26) |
| &nbsp; | Tabelas |Tabelas e partições |[Você particionou seus dados corretamente?](#subheading27) |
| &nbsp; | Tabelas |Partições mais acessadas |[Você evita padrões do tipo "somente anexar" e "somente incluir"?](#subheading28) |
| &nbsp; | Tabelas |Partições mais acessadas |[Suas inserções/atualizações valem para diversas partições?](#subheading29) |
| &nbsp; | Tabelas |Escopo da consulta |[Você criou seu esquema para permitir o uso de consultas pontuais na maioria dos casos e de consultas de tabelas raramente?](#subheading30) |
| &nbsp; | Tabelas |Densidade da consulta |[As suas consultas geralmente verificam e indicam quais linhas o aplicativo usará?](#subheading31) |
| &nbsp; | Tabelas |Limitação dos dados retornados |[Você usa a filtragem para evitar o retorno de entidades que não são necessárias?](#subheading32) |
| &nbsp; | Tabelas |Limitação dos dados retornados |[Você usa a projeção para evitar o retorno de propriedades que não são necessárias?](#subheading33) |
| &nbsp; | Tabelas |Desnormalização |[Você desnormalizou seus dados a fim de evitar consultas ineficientes sou diversas solicitações de leitura ao tentar obter dados?](#subheading34) |
| &nbsp; | Tabelas |Inserção/atualização/exclusão |[Você compila as solicitações que devem ser transacionais ou que podem ser executadas ao mesmo tempo para diminuir a quantidade de viagens de ida e volta?](#subheading35) |
| &nbsp; | Tabelas |Inserção/atualização/exclusão |[Você evita novas tentativas nas entidades apenas para determinar se a chamada deve ser inserida ou atualizada?](#subheading36) |
| &nbsp; | Tabelas |Inserção/atualização/exclusão |[Você já pensou em armazenar séries de dados que serão recuperados em conjunto frequentemente, em uma única entidade e como propriedades, em vez de serem recuperados como diversas entidades?](#subheading37) |
| &nbsp; | Tabelas |Inserção/atualização/exclusão |[Você já pensou em usar blobs no lugar das tabelas para as entidades que serão recuperadas em conjunto e que podem ser escritas em lotes (por exemplo, dados de séries temporais)?](#subheading38) |
| &nbsp; | Filas |Metas de escalabilidade |[Você leva as metas de escalabilidade em consideração para mensagens por segundo?](#subheading39) |
| &nbsp; | Filas |Configuração |[Você desativou o Nagle para melhorar o desempenho de pequenas solicitações?](#subheading40) |
| &nbsp; | Filas |Tamanho da mensagem |[Suas mensagens são compactas para melhorar o desempenho da fila?](#subheading41) |
| &nbsp; | Filas |Recuperação em massa |[Você recupera diversas mensagens com uma única operação "Obter"?](#subheading42) |
| &nbsp; | Filas |Frequência de votação |[As votações ocorrem com frequência suficiente para reduzir a latência notável do aplicativo?](#subheading43) |
| &nbsp; | Filas |Atualização de mensagem |[Você usa a função de atualização de mensagem para armazenar o progresso do processamento de mensagens, evitando a necessidade de reprocessar toda a mensagem em caso de erro?](#subheading44) |
| &nbsp; | Filas |Arquitetura |[Você usa filas para melhorar a escalabilidade de todo o aplicativo ao manter cargas de trabalho demoradas fora do caminho crítico e escalá-las independentemente?](#subheading45) |

## <a name="allservices"></a>Todos os serviços

Esta seção apresenta as práticas comprovadas aplicáveis ao uso dos serviços de armazenamento do Azure (blobs, tabelas, filas ou arquivos).  

### <a name="subheading1"></a>Metas de escalabilidade

O Armazenamento do Azure em si tem um limite de 250 contas de armazenamento por região e assinatura. Se você atingir esse limite, não poderá criar mais nenhuma conta de armazenamento nessa combinação de assinatura/região.

Cada serviço de armazenamento do Azure tem metas de escalabilidade para capacidade (GB), taxa de transações e largura de banda. Se o seu aplicativo se aproximar ou ultrapassar alguma das metas de escalabilidade, pode haver aumento da latência e restrição das transações. Quando um serviço de armazenamento restringe seu aplicativo, o serviço começa a apresentar os códigos de erro "503 Server busy" (Servidor ocupado) e "500 Operation timeout" (Tempo limite da operação) para algumas das transações de armazenamento. Esta seção trata da abordagem geral para lidar com as metas de escalabilidade e as metas de escalabilidade da largura de banda. As outras seções que tratam de cada serviço de armazenamento tratam das metas de escalabilidade no contexto de cada serviço:  

* [Largura de banda do blob e solicitações por segundo](#subheading16)
* [Entidades de tabela por segundo](#subheading24)
* [Fila de mensagens por segundo](#subheading39)  

#### <a name="sub1bandwidth"></a>Meta de escalabilidade da largura de banda para todos os serviços

No momento da edição, as metas de largura de banda nos EUA para uma conta de GRS (armazenamento com redundância geográfica) são de 10 gigabits por segundo (Gbps) para entrada (dados enviados à conta de armazenamento) e 20 Gbps para saída (dados enviados pela conta de armazenamento). Os limites das contas de LRS (armazenamento com redundância local) são mais altos — 20 Gbps para entrada e 30 Gbps para saída.  Os limites de largura de banda internacional podem ser menores e constam na nossa [página de metas de escalabilidade](https://msdn.microsoft.com/library/azure/dn249410.aspx).  Para saber mais sobre opções de redundância de armazenamento, confira os links em Recursos Úteis abaixo.  

#### <a name="what-to-do-when-approaching-a-scalability-target"></a>O que fazer ao lidar com uma meta de escalabilidade

Se você estiver se aproximando do limite de contas de armazenamento, poderá ter uma combinação específica de assinatura/região, avaliar o aplicativo e o uso de contas de armazenamento e determinar se qualquer uma dessas condições são aplicáveis.

* Usando contas de armazenamento como discos não gerenciados e adicionando esses discos às suas máquinas virtuais. Nesse cenário, recomendamos usar [discos gerenciados](../../virtual-machines/windows/managed-disks-overview.md), pois eles lidam com escalabilidade de disco de armazenamento para você sem a necessidade de criar nem gerenciar contas de armazenamento individuais.
* Usando uma conta de armazenamento por cliente para fins de isolamento de dados. Nesse cenário, recomendamos usar contêineres de armazenamento para cada cliente, em vez de uma conta de armazenamento inteira. Agora o Armazenamento do Azure permite que você especifique o controle de acesso baseado em função por [contêiner](storage-auth-aad-rbac-portal.md).
* Usando várias contas de armazenamento para fragmentar a fim de obter maior escalabilidade de entrada/saída/iops/capacidade. Nesse cenário, se possível, é recomendável aproveitar o [aumento de limites](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/) de contas de armazenamento padrão para reduzir o número de contas de armazenamento necessário para sua carga de trabalho.

Se seu aplicativo estiver lidando com metas de escalabilidade de uma única conta de armazenamento, você pode adotar uma destas abordagens:  

* Repensar a carga de trabalho que faz com que o aplicativo se aproxime da meta de escalabilidade ou a ultrapasse. Você pode alterar o aplicativo para que ele use menos largura de banda, menos capacidade ou menos transações?
* Se o aplicativo ultrapassar uma das metas de escalabilidade propositalmente, você deve criar diversas contas de armazenamento e particionar os dados do seu aplicativo nessas contas. Se você usar esse padrão, crie o aplicativo de forma que seja possível adicionar mais contas de armazenamento posteriormente, para balancear a carga. No momento da gravação, cada assinatura do Azure pode ter até 250 contas de armazenamento por região (quando implantada com o modelo de Azure Resource Manager).  O único custo das contas de armazenamento é o uso dos dados armazenados, das transações feitas ou dos dados transferidos.
* Se o aplicativo alcançar as metas de largura de banda, você pode compactar os dados no cliente para reduzir a largura de banda necessária para enviar os dados ao serviço de armazenamento.  Apesar de economizar a largura de banda e melhorar o desempenho da rede, isso também pode ter impactos negativos.  Você deve avaliar o impacto no desempenho causado por essa alteração, devido aos requisitos de processamento adicionais para compactar e descompactar dados no cliente. Além disso, o armazenamento de dados compactados pode dificultar a solução de problemas, pois pode ser mais difícil visualizar os dados armazenados por meio de ferramentas padrão.
* Se seu aplicativo alcançar as metas de escalabilidade, você deve usar a retirada exponencial para novas tentativas (confira [Novas tentativas](#subheading14)).  O mais recomendado é nunca alcançar as metas de escalabilidade, o que é possível garantir por meio de um dos métodos acima. Porém, isso garante que o aplicativo não faça novas tentativas rapidamente, piorando o problema de limitação.  

#### <a name="useful-resources"></a>Recursos úteis

Os links a seguir apresentam mais detalhes sobre as metas de escalabilidade:

* Confira [Metas de desempenho e escalabilidade do Armazenamento do Azure](storage-scalability-targets.md) para saber mais sobre metas de escalabilidade.
* Confira [Replicação de Armazenamento do Azure](storage-redundancy.md) e a postagem de blog [Azure Storage Redundancy Options and Read Access Geo Redundant Storage](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx) (Opções de redundância do Armazenamento do Azure e armazenamento com redundância geográfica com acesso de leitura) para saber mais sobre as opções de redundância de armazenamento.
* Para obter informações atualizadas sobre os preços dos serviços Azure, confira [Preços do Azure](https://azure.microsoft.com/pricing/overview/).  

### <a name="subheading47"></a>Convenção de nomenclatura de partição

O Armazenamento do Azure usa um esquema de particionamento baseado em intervalo para dimensionar e balancear a carga do sistema. A chave de partição (conta + contêiner + BLOB) é usada para particionar dados em intervalos e esses intervalos têm balanceamento de carga no sistema. Isso significa que as convenções de nomenclatura, como a ordenação lexical (por exemplo, mypayroll, myperformance, MyEmployees etc.) ou o uso de carimbos de data/hora (*log20160101*, *log20160102*, *log20160102*etc.), serão   em si, as partições estão potencialmente colocalizadas no mesmo servidor de partição, até que uma operação de balanceamento de carga as divida em intervalos menores. Por exemplo, todos os BLOBs dentro de um contêiner podem ser servidos por um único servidor até que a carga nesses BLOBs exija um novo balanceamento dos intervalos de partição. Da mesma forma, um grupo de contas pouco carregados com seus nomes organizados em ordem léxica pode ser atendido por um único servidor até que a carga em uma ou em todas essas contas exija a ser dividida em vários servidores de partições. Cada operação de balanceamento de carga pode afetar a latência das chamadas de armazenamento durante a operação. A capacidade do sistema de lidar com uma intermitência repentina de tráfego para uma partição é limitada pela escalabilidade de um único servidor de partição até que a operação de balanceamento de carga seja iniciada e rebalancee o intervalo de chaves de partição.

Você pode seguir algumas práticas recomendadas para reduzir a frequência de tais operações.  

* Se possível, use o blob Put maior ou os tamanhos de bloco put (maior que 4 MiB para contas padrão e maior que 256 KiB para contas Premium) para ativar o HTBB (High-exthroughput Block BLOB). O HTBB fornece ingestão de alto desempenho que não é afetada pela nomenclatura de partição.
* Examine detalhadamente a convenção de nomenclatura usada para contas, contêineres, blobs, tabelas e filas. Considere a possibilidade de prefixar nomes de conta, contêiner ou BLOB com um hash de 3 dígitos usando uma função de hash que melhor atenda às suas necessidades.  
* Se você organizar os dados usando carimbos de data/hora ou identificadores numéricos, precisará garantir que não está usando padrões de tráfego somente acrescentar (ou somente preceder). Esses padrões não são adequados a um sistema de particionamento baseado em intervalo e pode fazer com que todo tráfego vá para uma única partição, limitando a eficácia do sistema no balanceamento de carga. Por exemplo, se você tiver operações diárias que usam um objeto de blob com um carimbo de data/hora como *aaaammdd*, todo o tráfego dessa operação diária será direcionado para um único objeto, que é servido por um único servidor de partição. Veja se os limites por blob e os limites por partição atendem às suas necessidades e considere a divisão dessa operação em vários blobs, se necessário. Da mesma forma, se você armazenar os dados de série temporal em suas tabelas, todo o tráfego poderá ser direcionado para a última parte do namespace chave. Se você precisar usar carimbos de data/hora ou IDs numéricas, Prefixe a ID com um hash de 3 dígitos ou, no caso de carimbos de data/hora, Prefixe a parte de segundos do tempo, como *ssaaaammdd*. Se as operações de listagem e de consulta forem executadas rotineiramente, escolha uma função de hash que limite o número de consultas. Em outros casos, um prefixo aleatório pode ser suficiente.  
* Para obter informações adicionais sobre o esquema de particionamento usado no armazenamento do Azure [, consulte armazenamento do Azure: um serviço de armazenamento em nuvem altamente disponível com coerência forte](https://sigops.org/sosp/sosp11/current/2011-Cascais/printable/11-calder.pdf).

### <a name="networking"></a>Rede

Embora as chamadas de API sejam importantes, muitas vezes as limitações físicas da rede do aplicativo têm impacto considerável no desempenho. A seção a seguir descreve algumas das limitações que os usuários podem enfrentar.  

#### <a name="client-network-capability"></a>Funcionalidade de rede do cliente

##### <a name="subheading2"></a>Produtividade

No caso da largura de banda, muitas vezes o problema está relacionado às funcionalidades do cliente. Por exemplo, embora uma única conta de armazenamento possa receber um fluxo de 10 Gbps ou mais (confira as [metas de escalabilidade da largura de banda](#sub1bandwidth)), a velocidade da rede em uma instância de função de trabalho "pequena" do Azure só consegue lidar com cerca de 100 Mbps. As instâncias maiores do Azure têm NICs com mais capacidade. Por isso, você deve usar uma instância maior ou mais VMs se precisar de limites de rede mais altos em um único computador. Se você estiver acessando um serviço de armazenamento de um aplicativo no local, a mesma regra se aplica: compreender os recursos de rede do dispositivo do cliente e a conectividade de rede para o local de armazenamento do Azure e o aperfeiçoá-los conforme necessário ou projetar seu aplicativo para trabalhar dentro dos seus recursos.  

##### <a name="subheading3"></a>Qualidade do link

Como é necessário com qualquer uso de rede, as condições de rede que resultam em erros e na perda de pacote desaceleram a taxa de transferência.  Usar WireShark ou NetMon pode ajudar a identificar esse problema.  

##### <a name="useful-resources"></a>Recursos úteis

Para saber mais sobre os tamanhos de máquina virtual e sobre a largura de banda alocada, veja [Tamanhos de VM do Windows](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) ou [Tamanhos de VM do Linux](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  

#### <a name="subheading4"></a>Local

Em todos os ambientes, colocar o cliente próximo ao servidor proporciona o melhor desempenho. Para acessar o armazenamento do Azure com o mínimo de latência, o melhor local para o cliente é a região na qual o Azure se encontra. Por exemplo, se você tem um site do Azure que usa o armazenamento do Azure, ambos devem estar na mesma região (por exemplo, no oeste dos EUA ou no sudeste asiático). Isso diminui a latência e o custo. No momento da edição, o uso da largura de banda em uma única região é gratuito.  

Se os aplicativos do seu cliente não estiverem hospedados no Azure (como no caso de aplicativos para dispositivos móveis ou serviços corporativos), colocar a conta de armazenamento próximo aos dispositivos que a conta acessará geralmente diminui a latência. Se seus clientes estiverem amplamente distribuídos (por exemplo, alguns na América do Norte e alguns na Europa), você deve considerar o uso de várias contas de armazenamento: uma localizada em uma região na América do Norte e outra em uma região na Europa. Isso ajuda a diminuir a latência nas duas regiões. A implementação dessa solução é mais fácil se os dados armazenados pelo aplicativo são específicos aos usuários e se não é necessário replicar os dados entre as contas de armazenamento.  Para uma ampla distribuição de conteúdo, recomendamos o uso de uma CDN. Confira a seção a seguir para saber mais detalhes.  

### <a name="subheading5"></a>Distribuição de conteúdo

É possível que um aplicativo precise apresentar o mesmo conteúdo a muitos usuários (por exemplo, um vídeo para demonstrar um produto, apresentado na página inicial de um site) localizados na mesma região ou em diversas regiões. Nesse cenário, você deve usar uma CDN (rede de distribuição de conteúdo), como o Azure CDN. Por sua vez, o CDN deve usar o armazenamento do Azure como fonte dos dados. Diferente das contas de armazenamento do Azure que existem em uma única região e não podem fornecer conteúdos com baixa latência a outras regiões, a CDN do Azure usa servidores em diversos data centers pelo mundo. Além disso, uma CDN geralmente tem limites de saída muito maiores do que as contas de armazenamento individuais.  

Para obter mais informações, confira [CDN do Azure](https://azure.microsoft.com/services/cdn/).  

### <a name="subheading6"></a>Usando SAS e CORS

Quando você precisar autorizar o código como JavaScript no navegador da Web de um usuário ou um aplicativo de telefone celular para acessar dados no Armazenamento do Microsoft Azure, uma abordagem é usar um aplicativo em função Web como um proxy: o dispositivo do usuário autentica com a função web, que por sua vez autoriza o acesso para os recursos de armazenamento. Dessa forma, você pode evitar a exposição das chaves de conta de armazenamento em dispositivos que não são seguros. No entanto, isso gera uma sobrecarga significativa na função Web, porque todos os dados transferidos entre o dispositivo do usuário e o serviço de armazenamento devem ser transmitidos por meio da função Web. Você pode evitar o uso da função web como um proxy para o serviço de armazenamento. Para isso, use SAS (assinaturas de acesso compartilhado), que podem ser combinadas a cabeçalhos de CORS (compartilhamento de recursos entre origens). Por meio das SAS, você pode permitir que o dispositivo do usuário faça solicitações diretamente a um serviço de armazenamento por meio de um token de acesso limitado. Por exemplo, se o usuário quiser carregar uma foto no seu aplicativo, sua função Web poderá gerar e enviar um token SAS que conceda permissão de edição a um blob ou contêiner específico pelos próximos 30 minutos ao dispositivo do usuário (após esse período, o token expira).

Normalmente, o navegador não permite o uso de JavaScript em páginas hospedadas por um site em um domínio para executar operações especificas, como um "PUT" em outro domínio. Por exemplo, se você hospedar uma função Web em "contosomarketing.cloudapp.net" e quiser usar o JavaScript do cliente para carregar um blob em sua conta de armazenamento em "contosoproducts.blob.core.windows.net", a "política de mesma origem" do navegador proibirá essa operação. O CORS é um recurso do navegador que permite que o domínio alvo (neste caso, a conta de armazenamento) comunique-se com o navegador ao qual confia as solicitações que se originam no domínio de origem (neste caso, a função web).  

As duas tecnologias podem ajudar você a evitar cargas e gargalos desnecessários no seu aplicativo Web.  

#### <a name="useful-resources"></a>Recursos úteis

Para saber mais sobre SAS, confira [Shared Access Signatures, Part 1: Understanding the SAS Model](../storage-dotnet-shared-access-signature-part-1.md) (Assinaturas de Acesso Compartilhado, Parte 1: noções básicas sobre o modelo de SAS)  

Para saber mais sobre o CORS, consulte [Suporte a CORS (Compartilhamento de Recursos entre Origens) para os serviços de Armazenamento do Azure](https://msdn.microsoft.com/library/azure/dn535601.aspx).  

### <a name="caching"></a>Cache

#### <a name="subheading7"></a>Obtendo dados

Em geral, obter os dados de um serviço uma única vez é melhor do que obtê-los duas vezes. Tome como exemplo um aplicativo Web MVC em execução em uma função Web que já recuperou um blob de 50 MB do serviço de armazenamento para apresentar ao usuário como conteúdo. O aplicativo pode recuperar esse mesmo blob sempre que o usuário o solicitar, ou pode armazená-lo em cache localmente e reutilizar esse conteúdo em outras solicitações dos usuários. Além disso, sempre que um usuário solicitar os dados, o aplicativo pode emitir um GET com um cabeçalho condicional para tempo de modificação, o que evita obter todo o blob se ele não foi modificado. Você pode aplicar esse mesmo padrão ao trabalho com entidades em tabela.  

Em alguns casos, você pode determinar que seu aplicativo parta do pressuposto de que o blob permanece válido por um curto período após a recuperação e que, durante esse período, o aplicativo não precisa verificar se o blob foi modificado.

O armazenamento em cache é ótimo para configurações, pesquisas e outros dados que são sempre usados pelo aplicativo.  

Para obter mais informações sobre downloads condicionais, consulte [especificando cabeçalhos condicionais para operações de serviço blob](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations).  

#### <a name="subheading8"></a>Carregando dados em lotes

Em algumas situações nos aplicativos, é possível agregar dados localmente e carregá-los em lotes em vez de carregar cada dado imediatamente. Por exemplo, um aplicativo Web deve manter um arquivo de registro de atividades: o aplicativo pode enviar os detalhes de cada atividade assim que elas acontecem, como uma entidade de tabela (que requer muitas operações de armazenamento), ou pode salvar os detalhes da atividade em um arquivo de log local para carregar os detalhes de todas as atividades periodicamente, como um arquivo delimitado, em um blob. Se cada entrada de log tiver 1 KB, será possível carregar milhares de transações de um "blob de put" (é possível carregar um blob para transações individuais com até 64 MB). Se o computador local falhar antes do upload, você potencialmente perderá alguns dados de log: o desenvolvedor do aplicativo deve criar a possibilidade de dispositivo cliente ou falhas de upload.  Se for necessário carregar os dados das atividades para timespans (não apenas por atividade), os blobs são mais indicados do que as tabelas.

### <a name="net-configuration"></a>Configuração do .NET

Esta seção é útil para quem usa o .NET Framework, pois lista diversas configurações rápidas que você pode usar para fazer melhorias significativas no desempenho.  Se estiver usando outras linguagens, verifique se conceitos parecidos aplicam-se à linguagem escolhida.  

#### <a name="subheading9"></a>Aumentar o limite de conexão padrão

No .NET, o código a seguir aumenta o limite de conexão de padrão (que geralmente é 2 em um ambiente de cliente ou 10 em um ambiente de servidor) a 100. Normalmente, você deve definir o valor para aproximadamente igual ao número de threads utilizados pelo seu aplicativo.  

```csharp
ServicePointManager.DefaultConnectionLimit = 100; //(Or More)  
```

Você deve definir o limite da conexão antes de abrir as conexões.  

No caso de outras linguagens de programação, confira a documentação da linguagem para verificar como definir o limite da conexão.  

Para saber mais, confira a postagem no blog [Serviços Web: conexões simultâneas](https://blogs.msdn.com/b/darrenj/archive/2005/03/07/386655.aspx).  

#### <a name="subheading10"></a>Aumentar o número mínimo de threads se estiver usando código síncrono com tarefas assíncronas

Esse código aumentará o número mínimo de threads no pool de threads:  

```csharp
ThreadPool.SetMinThreads(100,100); //(Determine the right number for your application)  
```

Para saber mais, veja [Método ThreadPool.SetMinThreads](https://msdn.microsoft.com/library/system.threading.threadpool.setminthreads%28v=vs.110%29.aspx).  

#### <a name="subheading11"></a>Aproveite o .NET 4,5 e a coleta de lixo superior

Use a versão 4.5 ou posterior do .NET para que o aplicativo cliente aproveite as melhorias de desempenho na coleta de lixo do servidor.

Para saber mais, consulte o artigo [Uma visão geral dos aprimoramentos de desempenho no .NET 4.5](https://msdn.microsoft.com/magazine/hh882452.aspx).  

### <a name="subheading12"></a>Paralelismo não associado

Embora o paralelismo possa ser ótimo para o desempenho, tenha cuidado ao usar o paralelismo não associado (no qual não há limites para a quantidade de threads e/ou de solicitações paralelas) para carregar ou baixar dados, usando diversas funções de trabalho para acessar diversas partições (contêineres, filas ou tabelas) na mesma conta de armazenamento ou para acessar diversos itens na mesma partição. Se o paralelismo não estiver associado, o aplicativo poderá ultrapassar as funcionalidades do dispositivo cliente ou as metas de escalabilidade da conta de armazenamento, o que resultará em limitação e latências mais longas.  

### <a name="subheading13"></a>Bibliotecas e ferramentas de cliente de armazenamento

Sempre use a última versão das bibliotecas e ferramentas fornecidas pela Microsoft. No momento da gravação, há bibliotecas cliente disponíveis para .NET, Windows Phone, Tempo de Execução do Windows, Java e C++, além de bibliotecas de visualização em outras linguagens. Além disso, a Microsoft liberou cmdlets do PowerShell e os comandos da CLI do Azure para trabalhar com o Armazenamento do Azure. A Microsoft desenvolve ativamente essas ferramentas pesando no desempenho, além de mantê-las atualizadas com as últimas versões do serviço e garantir que elas gerenciem muitas das práticas de desempenho comprovadas internamente.  

### <a name="retries"></a>Novas tentativas

#### <a name="subheading14"></a>Limitação e erros de servidor ocupado

Em alguns casos, o serviço de armazenamento pode restringir seu aplicativo ou simplesmente não conseguir atender uma solicitação devido a alguma condição transitória, retornando uma mensagem "503 Server busy" ou "500 Timeout".  Isso pode acontecer se o aplicativo estiver alcançando alguma das metas de escalabilidade ou se o sistema estiver balanceando os dados particionados a fim de aumentar a taxa de transferência.  O aplicativo cliente normalmente deve tentar novamente a operação que gera um erro: a tentativa da mesma solicitação pode ser bem-sucedida mais tarde. No entanto, se o serviço de armazenamento estiver restringindo o aplicativo porque ele ultrapassou as metas de escalabilidade ou porque o serviço não pôde atender a solicitação por algum motivo, novas tentativas geralmente piorarão o problema. Por esse motivo, você deve usar uma retirada exponencial (as bibliotecas cliente são o padrão para esse comportamento). Por exemplo, o aplicativo pode fazer uma nova tentativa em 2 segundos, 4 segundos, 10 segundos e 30 segundos para então abandonar o processo. Esse comportamento faz com que o aplicativo diminua consideravelmente a sua carga no serviço, em vez de agravar qualquer problema.  

Pode haver novas tentativas imediatas em caso de erros de conectividade porque esses erros não são resultado de limitações e devem ser temporários.  

#### <a name="subheading15"></a>Erros sem nova tentativa

As bibliotecas cliente sabem quais erros admitem novas tentativas ou não. No entanto, se você estiver escrevendo seu próprio código contra a API REST de armazenamento, lembre-se há alguns erros que você não deve tentar novamente: por exemplo, uma resposta 400 (solicitação incorreta) indica que o aplicativo cliente enviou uma solicitação que não pôde ser processada porque não estava no formato esperado. O reenvio dessa solicitação sempre resultará na mesma resposta, por isso as novas tentativas não são úteis. Se você está escrevendo o seu próprio código com base na API REST de armazenamento, conheça o significado de alguns erros e saiba o método adequado de fazer uma nova tentativa ou não para cada um deles.  

#### <a name="useful-resources"></a>Recursos úteis

Para obter mais informações sobre os códigos de erro de armazenamento, confira [Status e códigos de erro](https://msdn.microsoft.com/library/azure/dd179382.aspx) no site do Microsoft Azure.  

## <a name="blobs"></a>Blobs

Além das práticas comprovadas para [todos os serviços](#allservices) descritos anteriormente, as práticas comprovadas a seguir se aplicam especificamente ao serviço BLOB.  

### <a name="blob-specific-scalability-targets"></a>Metas de escalabilidade específicas do blob

#### <a name="subheading46"></a>Vários clientes que acessam um único objeto simultaneamente

Se você tiver um grande número de clientes que acessem um único objeto simultaneamente, será preciso considerar as metas de escalabilidade de acordo com o objeto e a conta de armazenamento. O número exato de clientes que podem acessar um único objeto vai variar de acordo com fatores como o número de clientes que solicitam o objeto simultaneamente, o tamanho do objeto, as condições da rede etc.

Se o objeto puder ser distribuído por meio de uma CDN, como imagens ou vídeos disponibilizados em um site, você deverá usar uma CDN. Consulte [aqui](#subheading5).

Em outros cenários, como simulações científicas, em que os dados são confidenciais, você tem duas opções. A primeira é escalonar o acesso à carga de trabalho, de modo que o objeto seja acessado por um período em vez de ser acessado simultaneamente. Como alternativa, você pode copiar temporariamente o objeto para várias contas de armazenamento, aumentando assim o IOPS total por objeto e entre as contas de armazenamento. Em testes limitados, descobrimos que cerca de 25 VMs podem baixar simultaneamente um blob de 100 GB em paralelo (cada VM estava executando o download em paralelo usando 32 threads). No caso de 100 clientes precisando acessar o objeto, primeiro você o copiaria para uma segunda conta de armazenamento e, assim, teria as 50 primeiras VMs acessando o primeiro blob e as próximas 50 VMs acessando o segundo blob. Os resultados variam de acordo com o comportamento dos seus aplicativos e, portanto, você deve fazer testes durante o design. 

#### <a name="subheading16"></a>Largura de banda e operações por blob

É possível ler ou gravar em um único blob no máximo 60 MB/segundo, o que equivale a aproximadamente 480 Mbps e ultrapassa as funcionalidades de muitas redes dos clientes, inclusive do NIC físico no dispositivo do cliente. Além disso, um único blob comporta 500 solicitações por segundo. Se vários dos seus clientes precisarem ler os mesmo blob e você talvez ultrapasse esses limites, é possível usar uma CDN para distribuir o blob.  

Para saber mais sobre a taxa de transferência alvo para os blobs, consulte [Metas de desempenho e escalabilidade do Armazenamento do Azure](storage-scalability-targets.md).  

### <a name="copying-and-moving-blobs"></a>Copiando e movendo BLOBs

#### <a name="subheading17"></a>Copiar blob

A versão 2012-02-12 da API REST do armazenamento introduziu a capacidade útil para copiar blobs entre contas: um aplicativo cliente pode instruir o serviço de armazenamento para copiar um blob de outra origem (possivelmente em outra conta de armazenamento) e permitir que o serviço de realizar a cópia de forma assíncrona. Isso pode diminuir consideravelmente a largura de banda necessária para o aplicativo quando você estiver migrando dados de outras contas de armazenamento, porque você não precisará baixar nem carregar dados.  

No entanto, não é possível garantir em quanto tempo a cópia será concluída, ao se realizar cópias entre contas de armazenamento. Se o aplicativo precisar copiar um blob completo rapidamente com a sua supervisão, pode ser melhor baixar o blob para uma máquina virtual e carregá-lo no destino.  Para que seja possível prever toda a situação, a cópia deve ser feita por uma máquina virtual em execução na mesma região do Azure, ou as condições da rede podem (e provavelmente irão) afetar o desempenho da cópia.  Além disso, você pode monitorar o progresso de uma cópia assíncrona de modo programático.  

As cópias dentro da mesma conta de armazenamento geralmente são concluídas rapidamente.  

Para saber mais, consulte [Copiar Blob](https://msdn.microsoft.com/library/azure/dd894037.aspx).  

#### <a name="subheading18"></a>Usar AzCopy

A equipe de Armazenamento do Azure lançou a ferramenta de linha de comando, "AzCopy", destinada a ajudar na transferência em massa de muitos blobs para dentro, para fora e entre as contas de armazenamento.  Essa ferramenta foi otimizada para esse cenário e pode alcançar altas taxas de transferência.  Ela deve ser usada para baixar e carregar itens, bem como para copiar cenários, em massa. Para saber mais sobre ela e sobre como baixá-la, confira [Transferir dados com o utilitário de linha de comando AzCopy](storage-use-azcopy.md).  

#### <a name="subheading19"></a>Serviço de importação/exportação do Azure

Para grandes volumes de dados (superiores a 1 TB), o Armazenamento do Azure oferece o serviço de Importação/Exportação, que permite o carregamento e o download do armazenamento de blobs por meio do envio de discos rígidos.  Você pode colocar os dados em um disco rígido e enviá-lo à Microsoft para que possamos carregar esses dados ou enviar um disco rígido vazio para que baixemos os dados.  Para saber mais, veja [Usar o serviço de Importação/Exportação do Microsoft Azure para transferir dados ao armazenamento Blob](../storage-import-export-service.md).  Essa ação pode ser muito mais eficiente do que o envio/carregamento desse volume de dados pela rede.  

### <a name="subheading20"></a>Uso de metadados

O serviço blob dá suporte a solicitações de cabeçalho, que podem incluir metadados sobre o blob. Por exemplo, se o aplicativo precisar remover os dados EXIF de uma fotografia, é possível recuperá-la e extrair esses dados. Para economizar largura de banda e melhorar o desempenho, seu aplicativo poderá armazenar os dados EXIF nos metadados do blob quando o aplicativo tiver carregado a foto: é possível recuperar os dados EXIF nos metadados usando apenas uma solicitação HEAD, economizando o tempo de processamento e largura de banda significativa necessários para extrair os dados EXIF cada vez que o blob for lido. Isso é útil quando você precisa apenas dos metadados, não do conteúdo completo do blob.  É possível armazenar apenas 8 KB de metadados por blob (o serviço não aceitará uma solicitação para armazenar volumes maiores). Se esse tamanho não for suficiente, talvez não seja possível usar essa abordagem.  

### <a name="rapid-uploading"></a>Carregamento rápido

Para carregar os BLOBs rapidamente, a primeira pergunta a ser respondida é: você está carregando um ou vários BLOBs?  Use as informações abaixo para identificar o método de uso correto de acordo com o seu cenário.  

#### <a name="subheading21"></a>Carregamento rápido de um blob grande

Para carregar um único blob grande com rapidez, seu aplicativo cliente deve carregar os blocos ou as páginas em paralelo, levando em consideração as metas de escalabilidade para cada blob e a conta de armazenamento como um todo.  As bibliotecas oficiais de cliente de armazenamento RTM fornecidas pela Microsoft (.NET e Java) têm a capacidade de fazer isso.  Para cada uma das bibliotecas, use a propriedade/o objeto especificado abaixo para definir o nível de simultaneidade:  

* .NET: defina ParallelOperationThreadCount em um objeto BlobRequestOptions a ser usado.
* Java/Android: usar BlobRequestOptions.setConcurrentRequestCount()
* Node.js: Use parallelOperationThreadCount nas opções de solicitação ou no serviço BLOB.
* C++: use o método blob_request_options::set_parallelism_factor.

#### <a name="subheading22"></a>Carregamento rápido de diversos blobs

Para carregar diversos blobs com rapidez, carregue-os paralelamente. Esse tipo de carregamento é mais rápido do que o carregamento de um único blob por vez com carregamento paralelo de blocos porque a carga é dividida em diversas partições do serviço de armazenamento. Cada blob dá suporte a uma taxa de transferência de 60 MB/segundo (aproximadamente, 480 Mbps). No momento da gravação, uma conta LRS baseada nos EUA dá suporte a até 20 Gbps de entrada, o que é muito mais do que a taxa de transferência com suporte de um blob individual.  [AzCopy](#subheading18) executa os carregamentos em paralelo por padrão e é a opção recomendada nesse caso.  

### <a name="subheading23"></a>Escolhendo o tipo de blob certo

O armazenamento do Azure oferece suporte a dois tipos de blob: blobs de *página* e blobs de *blocos*. Em um determinado cenário de uso, o tipo de blob escolhido afeta o desempenho e a escalabilidade da solução. Os Blobs de bloco são apropriados quando você deseja carregar grandes quantidades de dados com eficiência: por exemplo, um aplicativo cliente pode precisar carregar fotos ou vídeo no armazenamento de blob. Os Blobs de página são apropriados, se o aplicativo precisa executar gravações aleatórias em dados: por exemplo, os VHDs do Azure são armazenados como blobs de página.  

Para saber mais, confira [Noções básicas sobre Blobs de bloco, Blobs de acréscimo e Blobs de página](https://msdn.microsoft.com/library/azure/ee691964.aspx).  

## <a name="tables"></a>Tabelas

Além das práticas comprovadas para [todos os serviços](#allservices) descritos, as práticas comprovadas a seguir aplicam-se especificamente ao serviço de tabela.  

### <a name="subheading24"></a>Metas de escalabilidade específicas de tabela

Além das limitações da largura de banda de toda uma conta de armazenamento, as tabelas têm o limite de escalabilidade descrito a seguir.  O sistema balanceará a carga conforme o tráfego aumenta, mas se houver um pico de tráfego repentino, talvez você não obtenha esse volume de taxa de transferência imediatamente.  Se você normalmente presencia esses picos, haverá restrições e/ou eventos de tempo limite durante esses picos, pois o serviço de armazenamento balanceia a carga da tabela automaticamente.  Os aumentos graduais geralmente apresentam resultados melhores, pois permitem que o sistema tenha tempo para balancear a carga corretamente.  

#### <a name="entities-per-second-storage-account"></a>Entidades por segundo (conta de armazenamento)

O limite de escalabilidade para acessar tabelas é de até 20 mil entidades (1 KB para cada) por segundo em cada conta.  Em geral, cada entidade inserida, atualizada, excluída ou verificada é computada nessa meta.  Assim, uma inserção em lote com 100 entidades é computada como 100 entidades.  Uma consulta que verifica 1000 entidades e retorna apenas 5 é computada como 1000 entidades.  

#### <a name="entities-per-second-partition"></a>Entidades por segundo (partição)

Em uma única partição, a meta de escalabilidade para acessar tabelas é de 2000 entidades (1 KB para cada) por segundo, usando a mesma contagem descrita na seção anterior.  

### <a name="configuration"></a>Configuração

Esta seção lista diversas configurações rápidas que você pode usar para fazer melhorias significativas no desempenho do serviço de tabela:  

#### <a name="subheading25"></a>Usar JSON

Começando na versão 2013-08-15 do serviço de armazenamento, o serviço Tabela dá suporte ao uso de JSON, em vez do formato AtomPub baseado em XML, para transferir dados de tabela. Isso pode reduzir o tamanho da carga em até 75% e pode melhorar consideravelmente o desempenho do seu aplicativo.

Para saber mais, confira a postagem [Tabelas do Microsoft Azure: introdução ao JSON](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/05/windows-azure-tables-introducing-json.aspx) e [Formato de conteúdo para operações de serviço Tabela](https://msdn.microsoft.com/library/azure/dn535600.aspx).

#### <a name="subheading26"></a>Desabilitar Nagle

O algoritmo de Nagle é implementado largamente em redes TCP/IP como meio de melhorar o desempenho das redes. No entanto, ele não é ideal em todas as circunstâncias (como em ambientes altamente interativos). No caso do armazenamento do Azure, o algoritmo do Nagle tem um impacto negativo sobre o desempenho das solicitações para a tabela e os serviços de fila. Por isso, desabilite-o se possível.

### <a name="schema"></a>Esquema

Como você representa e consulta os seus dados é o maior fator único que afeta o desempenho do serviço de tabela. Embora cada aplicativo seja único, esta seção descreve algumas práticas gerais comprovadas relacionadas:  

* Ao design da tabela
* A consultas eficientes
* A atualizações de dados eficientes  

#### <a name="subheading27"></a>Tabelas e partições

As tabelas são divididas em partições. Cada entidade armazenada em uma partição compartilha a mesma chave de partição e tem uma chave de linha exclusiva que a identifica dentro da partição em questão. As partições trazem benefícios, mas também criam limites de escalabilidade.  

* Benefícios: é possível atualizar as entidades na mesma partição em uma única transação atômica e em lote que contenha até 100 operações de armazenamento independentes (limite total de 4 MB). Partindo do pressuposto de que a mesma quantidade de entidades pode ser recuperada, você também pode consultar dados em uma única partição com mais eficiência do que a consulta a dados presentes em diversas partições (consulte as demais recomendações sobre consulta aos dados das tabelas).
* Limite de escalabilidade: o acesso às entidades armazenadas em uma única partição não pode passar por balanceamento de carga porque as partições dão suporte a transações atômicas em lote. Por esse motivo, a meta de escalabilidade de cada partição da tabela é menor do que a meta do serviço Tabela como um todo.  

Devido a essas características, você deve adotar estes princípios de design:  

* Os dados atualizados ou consultados com frequência por seu aplicativo cliente na mesma unidade de trabalho lógica devem constar na mesma partição.  Isso pode acontecer porque seu aplicativo agrega edições ou porque você que aproveitar a vantagem das operações atômicas em lote.  Além disso, é possível consultar os dados em uma única partição com mais eficiência do que fazer a consulta em diversas partições.
* Os dados não inseridos/atualizados ou consultados com frequência por seu aplicativo cliente na mesma unidade de trabalho lógica (consulta única ou atualização em lote) devem constar em partições diferentes.  Uma observação importante é que não há limite de chaves de partição para uma única tabela. Por isso, ter milhões de chaves de partição não representa um problema e não influencia o desempenho.  Por exemplo, se seu aplicativo é um site popular com logon dos usuários, usar a ID do usuário como chave de partição pode ser uma boa opção.  

#### <a name="hot-partitions"></a>Partições mais acessadas

As partições mais acessadas são aquelas que recebem um porcentual desproporcional do tráfego de uma conta e não podem passar pelo balanceamento de carga porque se trata de uma única partição.  Em geral, essas partições são criadas de duas formas:  

##### <a name="subheading28"></a>Padrões somente anexar e somente incluir

No padrão "somente anexar", todo (ou praticamente todo) o tráfego atribuído a um determinado PK aumenta e diminui de acordo com o momento.  Um exemplo é se o aplicativo usado na data funciona como chave de partição para os dados do log.  Isso faz com que todas as inserções vão para a última partição da sua tabela. O sistema não pode balancear a carga porque todas as edições vão em direção ao final da tabela.  Se o volume do tráfego para a partição em questão ultrapassar a meta de escalabilidade na partição, teremos restrições como resultado.  É melhor garantir que o tráfego seja enviado para diversas partições, a fim de garantir o balanceamento da carga das solicitações na tabela.  

##### <a name="subheading29"></a>Dados de tráfego alto

Se você estiver particionando resultados de esquemas em uma única partição que tem dados mais usados do os dados presentes nas demais partições, também pode haver restrições, pois a partição em questão aproxima-se da meta de escalabilidade de uma única partição.  É melhor garantir que seu esquema de partição não faça com que nenhuma partição específica aproxime-se das metas de escalabilidade.  

#### <a name="querying"></a>Consultas

Esta seção descreve as práticas comprovadas de consulta do serviço Tabela.  

##### <a name="subheading30"></a>Escopo da consulta

Há diversas maneiras de especificar quais entidades devem ser consultadas.  A seguir, tratamos do uso de cada uma delas.  

Em geral, evite as verificações (consultas cujo escopo tem mais de uma entidade). No entanto, se a verificação for necessária, tente organizar os dados de modo que as verificações recuperem os dados necessários sem verificar ou retornar grandes quantidades de entidades desnecessárias.  

###### <a name="point-queries"></a>Consultas de ponto

A consulta recupera exatamente uma entidade. Ela faz isso por meio da especificação da chave de partição e da chave de linha da entidade a ser recuperada. Essas consultas são eficientes e você deve usá-las sempre que possível.  

###### <a name="partition-queries"></a>Consultas de partição

Esse tipo de consulta recupera um conjunto de dados que tem uma chave de partição em comum. Geralmente, a consulta especifica diversos valores de chave de linha ou valores de propriedade de entidade, além de uma chave de partição. Elas são menos eficientes do que as consultas pontuais e devem ser usados com critério.  

###### <a name="table-queries"></a>Consultas de tabela

Esse tipo de consulta recupera um conjunto de entidades que não tem uma chave de partição em comum. Essas consultas não são eficientes e você deve evitá-las sempre que possível.  

##### <a name="subheading31"></a>Densidade da consulta

Outro fator importante na eficiência da consulta é a quantidade de entidades retornadas, em comparação à quantidade de entidades verificadas para localizar o conjunto retornado. Se o aplicativo ficar uma consulta de tabela filtrando por um valor da propriedade comum a apenas 1% dos dados, a consulta verificará 100 entidades para cada entidade retornada. As metas de escalabilidade de tabela discutidas anteriormente se relacionam ao número de entidades verificado e não o número de entidades retornadas: uma densidade de consulta baixa pode facilmente fazer com que o serviço Tabela acelere seu aplicativo porque ele deve verificar muitas entidades para recuperar a entidade que você está procurando.  Confira a seção abaixo sobre [desnormalização](#subheading34) para obter mais informações sobre como evitar isso.  

##### <a name="limiting-the-amount-of-data-returned"></a>Limitando a quantidade de dados retornados

###### <a name="subheading32"></a>Filtragem

Quando souber que uma consulta retornará entidades desnecessárias no aplicativo cliente, você poderá usar um filtro para diminuir a quantidade de entradas retornadas. Embora as entidades não retornadas para o cliente ainda sejam computadas nos limites de escalabilidade, o desempenho do aplicativo melhora devido à redução no tamanho da carga da rede e na quantidade de entidades que seu aplicativo cliente deve processar.  Confira a observação acima sobre a [Densidade da Consulta](#subheading31), mas lembre-se de que as metas de escalabilidade estão relacionadas à quantidade de entidades verificadas. Por isso, as consultas que filtram muitas entidades podem resultar em restrições, mesmo se poucas entidades forem retornadas.  

###### <a name="subheading33"></a>Projeção

Se seu aplicativo clientes precisar apenas de um conjunto limitado de propriedades das entidades da sua tabela, você pode usar a projeção para limitar o tamanho do conjunto de dados retornado. Como no caso da filtragem, isso ajuda a diminuir a carga da rede e o processamento do cliente.  

##### <a name="subheading34"></a>Desnormalização

Diferente do que acontece com os bancos de dados relacionados, as práticas recomentadas para consultar os dados da tabela com eficiência causam a desnormalização dos dados. Ou seja, a duplicação dos mesmos dados em diversas entidades (uma para cada chave, que você pode usar para localizar os dados) para minimizar a quantidade de entidades a serem verificadas por uma consulta para localizar os dados necessários para o cliente, em vez de precisar verificar muitas entidades para localizar os dados necessários para o aplicativo.  Por exemplo, em um site de comércio eletrônico, você pode querer localizar um pedido pela ID do cliente (apresentar os pedidos de um determinado cliente) e por data (apresentar os pedidos de uma determinada data).  No armazenamento de tabela, é melhor armazenar a entidade (ou fazer referência a ela) duas vezes, uma com o nome da tabela, PK e RK para facilitar a localização da ID do cliente e outra para facilitar a localização por data.  

#### <a name="insertupdatedelete"></a>Inserção/atualização/exclusão

Esta seção descreve as práticas comprovadas para modificar as entidades armazenadas no serviço Tabela.  

##### <a name="subheading35"></a>Envio em lote

As transações em lote são conhecidas como ETG (transações do grupo de entidades) no armazenamento do Azure. Todas as operações em uma ETG devem constar em uma única partição e em uma única tabela. Quando possível, use as ETGs para inserir, atualizar e excluir em lotes. Isso diminui a quantidade de viagens de ida e volta entre o aplicativo cliente e o servidor, diminui a quantidade de transações faturáveis (uma ETG conta como uma única transação para fins de cobrança e pode conter até 100 operações de armazenamento) e viabiliza atualizações atômicas (em uma ETG, todas as operações têm êxito ou falham). Os ambientes com altos níveis de latência, como os dispositivos móveis, veem muitos benefícios no uso das ETGs.  

##### <a name="subheading36"></a>Upsert

Use as operações de tabela **Upsert** sempre que possível. Há dois tipos de **Upsert** e os dois podem ser mais eficientes do que as operações tradicionais **Insert** e **Update**:  

* **InsertOrMerge**: use essa operação quando desejar carregar um subconjunto de propriedades da entidade, mas não souber se a entidade já existe. Se a entidade existir, essa chamada atualiza as propriedades incluídas na operação **Upsert** e deixa todas as propriedades existente como elas se encontram. Se a entidade não existir, a operação insere a nova entidade. Isso se parece com o uso de projeção em uma consulta, pois você só precisa carregar as propriedades que estão mudando.
* **InsertOrReplace**: use essa operação quando desejar carregar uma entidade completamente nova, mas não souber se a entidade já existe. Você só deve usar essa opção quando souber que a entidade carregada está perfeita, pois ela substitui completamente a entidade anterior. Por exemplo, você quer atualizar a entidade que armazena um local atual do usuário independente de o aplicativo já ter armazenado ou não os dados de local do usuário; a nova entidade de localização está completa e você não precisa de informações de nenhuma entidade anterior.

##### <a name="subheading37"></a>Armazenando séries de dados em uma única entidade

Às vezes, um aplicativo armazena uma série de dados que ele precisa recuperar uma só vez com frequência: por exemplo, um aplicativo pode controlar o uso da CPU ao longo do tempo para plotar um gráfico sem interrupção dos dados das últimas 24 horas. Uma abordagem é ter uma entidade de tabela por hora, com cada uma delas representando uma hora específica e armazenando o uso da CPU para a hora em questão. Para obter esses dados, o aplicativo precisa recuperar as entidades que contêm os dados das últimas 24 horas.  

Como alternativa, o aplicativo pode armazenar o uso da CPU para cada hora como uma propriedade separada de uma única entidade: para atualizar a cada hora, seu aplicativo pode usar uma única chamada de **InsertOrMerge Upsert** para atualizar o valor para a hora mais recente. Para plotar os dados, o aplicativo precisa apenas recuperar uma única entidade em vez de 24, fazendo uma consulta eficiente (consulte a discussão acima sobre o [escopo da consulta](#subheading30)).

##### <a name="subheading38"></a>Armazenando dados estruturados em blobs

Às vezes parece que os dados estruturados devem ser dispostos em tabelas, mas os intervalos das entidades sempre são recuperados em conjunto e podem ser inseridos em lote.  Um bom exemplo disso são os arquivos de log.  Nesse caso, você pode considerar diversos minutos de log como um lote, inseri-los e recuperar diversos minutos de uma só vez.  Nesse caso, para melhor desempenho, é melhor usar blobs do que tabelas, pois assim é possível diminuir consideravelmente a quantidade de objetos escritos/retornados, além de geralmente diminuir a quantidade de solicitações que devem ser feitas.  

## <a name="queues"></a>Filas

Além das práticas comprovadas para [Todos os Serviços](#allservices) descritos anteriormente, as práticas comprovadas a seguir aplicam-se especificamente ao serviço Fila.  

### <a name="subheading39"></a>Limites de escalabilidade

Uma única fila pode processar aproximadamente 2000 mensagens (1 KB cada) por segundo (cada contagem AddMessage, GetMessage e DeleteMessage como uma mensagem aqui). Se isso for insuficiente para seu aplicativo, você deve utilizar várias filas e distribuir as mensagens entre elas.  

Exiba as metas atuais de escalabilidade em [Metas de desempenho e escalabilidade do Armazenamento do Azure](storage-scalability-targets.md).  

### <a name="subheading40"></a>Desabilitar Nagle

Consulte a seção de configuração da tabela que fala sobre o algoritmo de Nagle. O uso desse algoritmo geralmente não é uma boa opção para o desempenho das solicitações de fila, e você deve desabilitá-lo.  

### <a name="subheading41"></a>Tamanho da mensagem

O desempenho e a escalabilidade da fila diminuem conforme o tamanho da mensagem aumenta. Você deve colocar somente as informações que o receptor precisa em uma mensagem.  

### <a name="subheading42"></a>Recuperação em lote

Você pode recuperar até 32 mensagens de uma fila em uma única operação. Isso pode diminuir a quantidade de viagens de ida e volta de um aplicativo cliente, o que é útil principalmente para ambientes com alta latência, como os dispositivos móveis.  

### <a name="subheading43"></a>Intervalo de sondagem de fila

A maioria dos aplicativos de sondagem para mensagens de uma fila, pode ser uma das principais fontes de transações para o aplicativo. Selecione o intervalo de sondagem com sabedoria: a sondagem muito frequente pode fazer com que seu aplicativo se aproxime das metas de escalabilidade para a fila. No entanto, em 200.000 transações para US $0,01 (no momento da gravação), um único processador sondando uma vez por segundo em um mês custaria menos de 15 centavos, assim o custo de sondagem não é normalmente um fator que afeta sua opção de intervalo de sondagem.  

Para obter informações atualizadas sobre custos, confira [Preços do Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/).  

### <a name="subheading44"></a>Atualizar mensagem

Você pode usar a operação **Atualizar mensagem** para aumentar o tempo limite de invisibilidade ou para atualizar informações de estado de uma mensagem. Embora isso seja poderoso, lembre-se de que cada operação **Atualizar mensagem** conta para o destino de escalabilidade. No entanto, essa abordagem pode ser muito mais eficiente do que ter um fluxo de trabalho que transmite uma tarefa de uma fila para a outra, pois cada etapa da tarefa é concluída. O uso da operação **Atualizar mensagem** permite que seu aplicativo salve o estado do trabalho na mensagem e continue trabalhando, em vez de enfileirar novamente a mensagem para a próxima etapa do trabalho sempre que uma etapa for concluída.  

Para saber mais, confira o artigo [Como alterar o conteúdo de uma mensagem na fila](../queues/storage-dotnet-how-to-use-queues.md#change-the-contents-of-a-queued-message).  

### <a name="subheading45"></a>Arquitetura do aplicativo

Você deve usar filas para que a arquitetura do aplicativo seja escalonável. A seguir temos algumas maneiras de usar filas para que seu aplicativo seja mais escalonável:  

* Você pode usar as filas para criar listas de pendências de trabalho para processamento e suavização das cargas de trabalho no seu aplicativo. Por exemplo, você pode colocar na fila as solicitações dos usuários para execução de trabalhos que requerem trabalho muito intenso do processador, como o redimensionamento das imagens carregadas.
* Você pode usar as filas para desassociar parte do aplicativo, a fim de poder escaloná-las de forma independente. Por exemplo, um front-end da Web pode colocar os resultados de pesquisa dos usuários em uma fila para análise posterior e armazenamento. Você pode adicionar mais instâncias de função de trabalho para processar os dados da fila conforme necessário.  

## <a name="conclusion"></a>Conclusão

Este artigo falou sobre algumas das práticas comprovadas mais comuns para otimizar o desempenho com o uso do armazenamento do Azure. Nós recomendamos que cada desenvolvedor avalie seu aplicativo com base nas práticas descritas acima e considere seguir as recomendações para obter desempenho excelente para seus aplicativos que usam o Armazenamento do Azure.
