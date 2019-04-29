---
title: Funcionalidade do sistema operacional no Serviço de Aplicativo – Azure
description: Saiba mais sobre a funcionalidade do sistema operacional disponível para aplicativos Web, back-ends de aplicativos móveis e aplicativos de API no Serviço de Aplicativo do Azure
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: mollybos
ms.assetid: 39d5514f-0139-453a-b52e-4a1c06d8d914
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: e5ab6651503766844b2aeef1849bffff9cf4d7bb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60835491"
---
# <a name="operating-system-functionality-on-azure-app-service"></a>Funcionalidade do sistema operacional no Serviço de Aplicativo do Azure
Este artigo descreve a funcionalidade do sistema operacional de linha de base comum disponível a todos os aplicativos Windows em execução no [Serviço de Aplicativo do Azure](https://go.microsoft.com/fwlink/?LinkId=529714). Essa funcionalidade inclui acesso a arquivos, redes e registros, além de logs de diagnóstico e eventos. 

> [!NOTE] 
> [Aplicativos Linux](containers/app-service-linux-intro.md) no Serviço de Aplicativo são executados em seus próprios contêineres. Nenhum acesso ao sistema operacional do host é permitido, você tem acesso à raiz para o contêiner. Da mesma forma, para [aplicativos em execução em contêineres do Windows](app-service-web-get-started-windows-container.md), você tem acesso administrativo ao contêiner, mas nenhum acesso ao sistema operacional do host. 
>

<a id="tiers"></a>

## <a name="app-service-plan-tiers"></a>Camadas de plano do Serviço de Aplicativo
O Serviço de Aplicativo executa aplicativos do cliente em um ambiente de hospedagem multilocatário. Os aplicativos implantados nas camadas **Gratuito** e **Compartilhado** são executados em processos de trabalho nas máquinas virtuais compartilhadas, enquanto os aplicativos implantados nas camadas **Standard** e **Premium** são executados em máquinas virtuais dedicadas especificamente aos aplicativos associados a um único cliente.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Como o Serviço de Aplicativo dá suporte a uma experiência de dimensionamento perfeita entre camadas diferentes, a configuração de segurança imposta para aplicativos do Serviço de Aplicativo permanece a mesma. Isso garante que os aplicativos Web não se comportem inesperadamente de maneira diferente, falhando de maneiras inesperadas, quando o plano do Serviço de Aplicativo alterna de uma camada para outra.

<a id="developmentframeworks"></a>

## <a name="development-frameworks"></a>Estruturas de desenvolvimento
As camadas de preços do Serviço de Aplicativo controlam a quantidade de recursos de computação (CPU, armazenamento em disco, memória e egresso de rede) disponíveis para aplicativos. No entanto, a amplitude da funcionalidade da estrutura disponível para aplicativos permanece a mesma, independentemente das camadas de dimensionamento.

O Serviço de Aplicativo dá suporte a várias estruturas de desenvolvimento, inclusive ASP.NET, ASP clássico, node.js, PHP e Python, todas executadas como extensões no IIS. Para simplificar e normalizar a configuração de segurança, o Serviço de Aplicativo normalmente executa as diversas estruturas de desenvolvimento com suas configurações padrão. Uma abordagem para configurar os aplicativos poderia ser personalizar a área de superfície da API e a funcionalidade para cada estrutura de desenvolvimento individual. Em vez disso, o Serviço de Aplicativo utiliza uma abordagem mais genérica, habilitando uma linha de base comum de funcionalidade do sistema operacional, independentemente da estrutura de desenvolvimento do aplicativo.

As seções a seguir resumem os tipos gerais de funcionalidade do sistema operacional disponíveis para aplicativos do Serviço de Aplicativo.

<a id="FileAccess"></a>

## <a name="file-access"></a>Acesso a arquivos
Existem diversas unidades dentro do Serviço de Aplicativo, incluindo unidades locais e unidades de rede.

<a id="LocalDrives"></a>

### <a name="local-drives"></a>Unidades locais
Basicamente, o Serviço de Aplicativo é um serviço em execução na infraestrutura do Azure PaaS (plataforma como serviço). Dessa forma, as unidades locais "anexadas" a uma máquina virtual são dos mesmos tipos de unidade disponíveis para qualquer função de trabalho em execução no Azure. Isso inclui:

- Uma unidade do sistema operacional (a unidade D:\)
- Uma unidade de aplicativo que contém os arquivos cspkg do pacote do Azure usada exclusivamente pelo Serviço de Aplicativo do Azure (e inacessível para os clientes)
- Uma unidade de "usuário" (unidade C:\), cujo tamanho varia dependendo do tamanho da VM. 

É importante monitorar a sua utilização de disco à medida que seu aplicativo cresce. Se a cota de disco for atingida, isso pode ter efeitos adversos para seu aplicativo. Por exemplo:  

- O aplicativo pode gerar um erro indicando que não há espaço suficiente no disco.
- Você poderá ver erros de disco ao navegar para o console do Kudu.
- A implantação do VSTS ou do Visual Studio pode falhar com `ERROR_NOT_ENOUGH_DISK_SPACE: Web deployment task failed. (Web Deploy detected insufficient space on disk)`.
- Seu aplicativo pode sofrer um desempenho lento.

<a id="NetworkDrives"></a>

### <a name="network-drives-aka-unc-shares"></a>Unidades de rede (também conhecidas como compartilhamentos UNC)
Um dos aspectos exclusivos do Serviço de Aplicativo que simplifica a implantação e a manutenção de aplicativos é que todo o conteúdo do usuário é armazenado em um conjunto de compartilhamentos UNC. Esse modelo mapeia bem para o padrão comum de armazenamento de conteúdo usado por ambientes de hospedagem na Web local com vários servidores com balanceamento de carga. 

Dentro do Serviço de Aplicativo, existem vários compartilhamentos UNC criados em cada datacenter. Uma porcentagem do conteúdo do usuário para todos os clientes em cada data center é alocada para cada compartilhamento UNC. Além disso, todo o conteúdo de arquivo de uma assinatura do cliente única é sempre colocado no mesmo compartilhamento UNC. 

Por conta da maneira como os serviços do Microsoft Azure funcionam, a máquina virtual específica responsável por hospedar um compartilhamento UNC mudará com o passar do tempo. Existe a garantia de que compartilhamentos UNC serão montados por máquinas virtuais diferentes à medida que elas forem ligadas e desligadas durante o curso normal das operações no Microsoft Azure. Por esse motivo, os aplicativos nunca devem fazer pressuposições codificadas de que as informações da máquina em um caminho de arquivo UNC permanecerão estáveis com o passar do tempo. Em vez disso, eles devem usar o caminho absoluto *faux* **D:\home\site** fornecido pelo Serviço de Aplicativo. Esse caminho absoluto faux oferece um método portátil, independente do aplicativo e do usuário, para se referir ao seu próprio aplicativo. Usando **D:\home\site**, uma pessoa pode transferir arquivos compartilhados de um aplicativo para outro sem que seja necessário configurar um novo caminho absoluto para cada transferência.

<a id="TypesOfFileAccess"></a>

### <a name="types-of-file-access-granted-to-an-app"></a>Tipos de acesso a arquivos concedidos a um aplicativo
A assinatura de cada cliente tem uma estrutura de diretório reservado em um compartilhamento UNC específico dentro de um data center. Como um cliente pode ter vários aplicativos criados dentro em datacenter específico, todos os diretórios pertencentes a uma única assinatura de cliente são criados no mesmo compartilhamento UNC. O compartilhamento pode incluir diretórios como os de conteúdo, erro e logs de diagnóstico, e versões anteriores do aplicativo criadas pelo controle do código-fonte. Conforme esperado, os diretórios do aplicativo de um cliente estão disponíveis para acesso de leitura e gravação em tempo de execução pelo código do aplicativo.

Nas unidades locais conectadas à máquina virtual que executa um aplicativo, o Serviço de Aplicativo reserva uma parte do espaço na unidade C:\ para armazenamento local temporário específico de aplicativos. Embora um aplicativo tenha acesso de leitura/gravação completo em seu próprio armazenamento local temporário, esse armazenamento não se destina, de fato, a ser usado diretamente pelo código do aplicativo. Em vez disso, o objetivo é fornecer um armazenamento de arquivo temporário para o IIS e para as estruturas do aplicativo Web. O Serviço de Aplicativo também limita a quantidade de armazenamento local temporário disponível para cada aplicativo para evitar que aplicativos individuais consumam quantidades excessivas de armazenamento de arquivos local.

Dois exemplos de como o Serviço de Aplicativo utiliza o armazenamento local temporário são o diretório para arquivos do ASP.NET temporários e o diretório para arquivos compactados do IIS. O sistema de compilação do ASP.NET usa o diretório "Arquivos do ASP.NET Temporários" como um local de cache de compilação temporária. O IIS usa o diretório "Arquivos Compactados Temporários do IIS" para armazenar a saída de resposta compactada. Os usos desses tipos de arquivo (bem como outros) são remapeados no Serviço de Aplicativo para o armazenamento local temporário por aplicativo. Esse remapeamento garante que a funcionalidade continua conforme esperado.

Cada aplicativo no Serviço de Aplicativo é executado como uma identidade do processo de trabalho com poucos privilégios aleatório exclusiva chamada de "identidade do pool de aplicativos", descrita mais detalhadamente aqui: [https://www.iis.net/learn/manage/configuring-security/application-pool-identities](https://www.iis.net/learn/manage/configuring-security/application-pool-identities). O código do aplicativo usa essa identidade no acesso somente leitura básico à unidade do sistema operacional (a unidade D:\). Isso significa que o código do aplicativo pode listar estruturas de diretório comuns e ler arquivos comuns na unidade do sistema operacional. Embora isso possa parecer ser um nível de acesso um pouco mais amplo, os mesmos diretórios e arquivos permanecem acessíveis quando você provisiona uma função de trabalho em um serviço hospedado do Azure e ler o conteúdo da unidade. 

<a name="multipleinstances"></a>

### <a name="file-access-across-multiple-instances"></a>Acesso a arquivos em várias instâncias
O diretório base apresenta o conteúdo de um aplicativo, e o código do aplicativo pode gravar nele. Se um aplicativo for executado em várias instâncias, o diretório base será compartilhado entre todas as instâncias para que todas as instâncias vejam o mesmo diretório. Assim, por exemplo, se um aplicativo salvar arquivos carregados no diretório base, esses arquivos estarão imediatamente disponíveis para todas as instâncias. 

<a id="NetworkAccess"></a>

## <a name="network-access"></a>Acesso à rede
O código do aplicativo pode usar protocolos com base em TCP/IP e UDP para estabelecer conexões de rede de saída com pontos de extremidade acessíveis pela Internet que expõem serviços externos. Os aplicativos podem usar esses mesmos protocolos para se conectar aos serviços dentro do Azure&#151; por exemplo, estabelecendo conexões HTTPS com o Banco de Dados SQL.

Também existe uma capacidade limitada para que aplicativos estabeleçam uma conexão de loopback local e um aplicativo escute nesse soquete de loopback local. Esse recurso existe principalmente para permitir que os aplicativos escutem em soquetes de loopback locais como parte de sua funcionalidade. Cada aplicativo vê uma conexão de loopback "privada". O aplicativo "A" não consegue escutar um soquete de loopback local estabelecido pelo aplicativo "B".

Os pipes nomeados também são compatíveis como um mecanismo de comunicação entre processos (IPC) entre processos diferentes que executam coletivamente um aplicativo. Por exemplo, o módulo FastCGI do IIS depende de pipes nomeados para coordenar os processos individuais que executam páginas PHP.

<a id="Code"></a>

## <a name="code-execution-processes-and-memory"></a>Execução de código, processos e memória
Conforme observado anteriormente, os aplicativos são executados em processos de trabalho com poucos privilégios usando uma identidade de pool de aplicativos aleatória. O código do aplicativo tem acesso ao espaço da memória associado ao processo de trabalho, bem como a todos os processos filho que possam ter sido gerados por processos CGI ou outros aplicativos. No entanto, o aplicativo de um cliente não pode acessar a memória ou os dados de outro aplicativo, mesmo que ele esteja na mesma máquina virtual.

Os aplicativos podem executar scripts ou páginas gravadas com estruturas de desenvolvimento na Web com suporte. O Serviço de Aplicativo não define configurações de estrutura na Web como modos mais restritos. Por exemplo, aplicativos do ASP.NET em execução no o Serviço de Aplicativo são executados com confiança "total", e não como um modo de confiança mais restrito. As estruturas na Web, inclusive o ASP clássico e o ASP.NET, podem chamar componentes COM em processo (mas não componentes COM fora de processo) como ADO (ActiveX Data Objects) que são registrados por padrão no sistema operacional Windows.

Os aplicativos podem gerar e executar códigos arbitrários. É aceitável que um aplicativo execute ações como gerar um shell de comando ou executar um script do PowerShell. No entanto, embora códigos e processos arbitrários possam ser gerados com base em um aplicativo, programas executáveis e scripts continuam restritos aos privilégios concedidos ao pool de aplicativos pai. Por exemplo, um aplicativo pode gerar um executável que faz uma chamada HTTP de saída, mas esse mesmo executável não pode tentar desassociar o endereço IP de uma máquina virtual de seu NIC. Fazer uma chamada de rede de saída é permitido para um código com poucos privilégios, mas tentar redefinir as configurações de rede em uma máquina virtual exige privilégios administrativos.

<a id="Diagnostics"></a>

## <a name="diagnostics-logs-and-events"></a>Logs de diagnóstico e eventos
Informações de log são outro conjunto de dados que alguns aplicativos tentam acessar. Entre os tipos de informações de log disponíveis para código em execução no Serviço de Aplicativo estão informações de diagnóstico e log geradas por um aplicativo que também são facilmente acessíveis para o aplicativo. 

Por exemplo, os logs HTTP do W3C gerados por um aplicativo ativo estão disponíveis em um diretório de log no local do compartilhamento de rede criado para o aplicativo ou disponíveis no armazenamento de blobs, caso um cliente tenha definido o registro em log do W3C como armazenamento. A última opção permite que grandes quantidades de logs sejam coletadas sem o risco de exceder os limites de armazenamento de arquivo associados a um compartilhamento de rede.

Do mesmo modo, as informações de diagnóstico em tempo real de aplicativos do .NET também podem ser registradas em log usando-se a infraestrutura de rastreamento e diagnóstico do .NET com opções para gravar as informações de rastreamento no compartilhamento de rede do aplicativo ou em um local de armazenamento de blobs.

As áreas de registro em log de diagnóstico e rastreamento que não estão disponíveis para aplicativos são eventos ETW do Windows e logs de evento do Windows comuns (por exemplo, logs de evento de sistema, aplicativo e segurança). Como as informações de rastreamento ETW podem abranger todo o computador (com as ACLs certas), o acesso de leitura e gravação para eventos ETW permanece bloqueado. Os desenvolvedores podem notar que chamadas à API para ler e gravar eventos ETW e logs de eventos do Windows estão aparentemente funcionando, mas isso é porque o Serviço de Aplicativo está "falsificando" as chamadas para que pareçam ter êxito. Na realidade, o código do aplicativo não tem acesso a esses dados de evento.

<a id="RegistryAccess"></a>

## <a name="registry-access"></a>Acesso ao Registro
Os aplicativos têm acesso somente leitura a grande parte do Registro (mas não em sua totalidade) da máquina virtual em que estão sendo executados. Na prática, isso significa que as chaves do Registro que permitem acesso somente leitura ao grupo Usuários local são acessíveis por aplicativos. Uma área do Registro que atualmente não é compatível com acesso de leitura ou gravação é o hive HKEY\_CURRENT\_USER.

O acesso de gravação ao Registro está bloqueado, inclusive o acesso a chave do Registro por usuário. Do ponto de vista do aplicativo, o acesso de gravação ao Registro nunca deve ser usado no ambiente do Azure porque os aplicativos podem (e vão) ser migrados entre máquinas virtuais diferentes. O único armazenamento gravável persistente que pode ser usado por um aplicativo é a estrutura do diretório de conteúdo por aplicativo armazenada nos compartilhamentos UNC do Serviço de Aplicativo. 

## <a name="remote-desktop-access"></a>Acesso remoto à área de trabalho

O Serviço de Aplicativo não fornece acesso à área de trabalho remota às instâncias da VM.

## <a name="more-information"></a>Mais informações

[Área restrita do Serviço de Aplicativo do Azure](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox) - As informações mais atualizadas sobre o ambiente de execução do Serviço de Aplicativo. Esta página é mantida diretamente pela equipe de desenvolvimento do Serviço de Aplicativo.

