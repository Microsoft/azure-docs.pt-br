---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: iot-fundamentals
author: robinsh
ms.service: iot-fundamentals
ms.topic: include
ms.date: 08/07/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: f3e05f213821b053f8cf6abbbc50a14e9ea62295
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60626517"
---
# <a name="internet-of-things-iot-security-architecture"></a>Arquitetura de segurança da Internet das Coisas (IoT)

Durante a criação de um sistema, é importante compreender as ameaças potenciais para esse sistema e adicionar as defesas apropriadas da mesma forma, conforme o sistema é projetado e desenvolvido. É importante projetar o produto desde o início com a segurança em mente porque a compreensão de como um invasor pode conseguir comprometer um sistema ajuda a garantir que as mitigações adequadas estão em vigor desde o início.

## <a name="security-starts-with-a-threat-model"></a>A segurança começa com um modelo de risco

A Microsoft há muito tempo usa modelos de ameaças para seus produtos e tornou o processo de modelagem de ameaças da empresa publicamente disponível. A experiência da empresa demonstra que a modelagem tem benefícios inesperados além da compreensão imediata de quais ameaças são os mais preocupantes. Por exemplo, ela também cria uma avenida para uma discussão aberta com outras pessoas fora da equipe de desenvolvimento, o que pode levar a novas ideias e aprimoramentos no produto.

O objetivo da modelagem de risco é entender como um invasor pode ser capaz de comprometer um sistema e verificar se as mitigações apropriadas estão em vigor. A modelagem de risco força a equipe de design a considerar mitigações conforme o sistema é projetado em vez de depois que um sistema é implantado. Esse fato é extremamente importante, porque a modernização das defesas de segurança para vários dispositivos no campo é inviável, sujeita a erros e coloca os clientes em risco.

Muitas equipes de desenvolvimento fazem um excelente trabalho capturando os requisitos funcionais do sistema que beneficiam os clientes. No entanto, identificar formas não óbvias em que alguém pode utilizar o sistema de maneira indevida é mais desafiador. A modelagem de risco pode ajudar as equipes de desenvolvimento a compreender o que um invasor pode fazer e por quê. A modelagem de risco é um processo estruturado que cria uma discussão sobre as decisões de design de segurança no sistema, bem como alterações no design que são feitas ao longo do caminho que impactam a segurança. Embora um modelo de risco seja simplesmente um documento, esta documentação também representa uma forma ideal de garantir a continuidade do conhecimento, a retenção de lições aprendidas e ajuda a nova equipe a realizar a integração rapidamente. Por fim, um resultado da modelagem de risco é permitir que você considere outros aspectos de segurança, como quais compromissos de segurança deseja fornecer aos seus clientes. Esses compromissos em conjunto com a modelagem de risco informam e orientam o teste de sua solução de IoT (Internet das Coisas).

### <a name="when-to-do-threat-modeling"></a>Quando fazer modelagem de ameaças

A [modelagem de risco](https://www.microsoft.com/en-us/sdl/adopt/threatmodeling.aspx) oferece o valor máximo quando incorporada na fase de design. Ao realizar o design, você tem a maior flexibilidade para fazer alterações para eliminar as ameaças. A eliminação de ameaças por design é o resultado desejado. É muito mais fácil do que adicionar mitigações, testá-las e garantir que elas permaneçam atualizadas e, além disso, essa eliminação nem sempre é possível. Conforme o produto amadurece, fica mais difícil eliminar as ameaças, o que acaba exigindo mais trabalho e concessões muito mais complexas do que a modelagem de risco no início do desenvolvimento.

### <a name="what-to-consider-for-threat-modeling"></a>O que considerar para a modelagem de ameaças

Você deve analisar a solução como um todo e também se concentrar nas seguintes áreas:

* Os recursos de segurança e privacidade
* Os recursos cujas falhas são relevantes para a segurança
* Os recursos que tocam um limite de confiança

### <a name="who-performs-threat-modeling"></a>Quem realiza modelagem de ameaças

A modelagem de risco é um processo como qualquer outro. É uma boa ideia tratar o documento de modelo de risco como qualquer outro componente da solução e validá-lo. Muitas equipes de desenvolvimento fazem um excelente trabalho capturando os requisitos funcionais do sistema que beneficiam os clientes. No entanto, identificar formas não óbvias em que alguém pode utilizar o sistema de maneira indevida é mais desafiador. A modelagem de risco pode ajudar as equipes de desenvolvimento a compreender o que um invasor pode fazer e por quê.

### <a name="how-to-perform-threat-modeling"></a>Como realizar modelagem de ameaças

O processo de modelagem de risco é composto por quatro etapas; as etapas são:

* Modelar o aplicativo
* Enumerar as ameaças
* Atenuar as ameaças
* Validar as mitigações

#### <a name="the-process-steps"></a>As etapas do processo

Há três regras básicas para ter em mente ao criar um modelo de risco:

1. Crie um diagrama da arquitetura de referência.

2. Inicie primeiro por abrangência. Obtenha uma visão geral e entenda o sistema como um todo, antes de se aprofundar. Essa abordagem ajuda a garantir o aprofundamento nas áreas certas.

3. Conduza o processo, não deixe que o processo conduza você. Caso você encontre um problema na fase de modelagem e queira explorá-lo, vá em frente! Não pense que você precisa seguir essas etapas submissamente.

#### <a name="threats"></a>Ameaças

Os quatro principais elementos de um modelo de risco são:

* Processos, como serviços Web, serviços de Win32 e daemons *nix. Algumas entidades complexas (por exemplo, gateways de campo e sensores) podem ser abstraídas como um processo quando um detalhamento técnico nessas áreas não é possível.

* Armazenamentos de dados (qualquer lugar em que os dados são armazenados, como um arquivo de configuração ou banco de dados)

* Fluxo de dados (onde os dados se movem entre outros elementos no aplicativo)

* Entidades externas (qualquer elemento que interage com o sistema, mas não está sob o controle do aplicativo, os exemplos incluem usuários e feeds de satélite)

Todos os elementos no diagrama da arquitetura estão sujeitos a várias ameaças; neste artigo, usamos o mnemônico STRIDE. Leia [Threat Modeling Again, STRIDE](https://blogs.msdn.microsoft.com/larryosterman/2007/09/04/threat-modeling-again-stride/) (Modelagem de risco novamente, STRIDE) para saber mais sobre os elementos do STRIDE.

Diferentes elementos do diagrama de aplicativo estão sujeitos a determinadas ameaças do STRIDE:

* Os processos estão sujeitos ao STRIDE
* Os fluxos de dados estão sujeitos a TID
* Os armazenamentos de dados estão sujeitos a TID e, às vezes, R, quando os armazenamentos de dados são arquivos de log.
* As entidades externas estão sujeitas ao SRD

## <a name="security-in-iot"></a>Segurança na IoT

Dispositivos de finalidade especial conectados têm um número significativo de áreas de superfície de interação potencial e padrões de interação, que devem ser considerados para fornecer uma estrutura para proteger o acesso digital a esses dispositivos. O termo "acesso digital" é usado aqui para distinguir de todas as operações executadas por meio de interação direta do dispositivo em que a segurança de acesso é fornecida por meio do controle de acesso físico. Por exemplo, colocar o dispositivo em uma sala com uma trava na porta. Embora o acesso físico não possa ser negado usando hardware e software, é possível adotar medidas para impedir que o acesso físico leve à interferência do sistema.

Ao explorar os padrões de interação, dê ao "controle de dispositivo" e os "dados de dispositivo" a mesma atenção. O "controle de dispositivo" pode ser classificado como qualquer informação fornecida para um dispositivo por qualquer pessoa com o objetivo de alterar ou influenciar seu comportamento em relação ao seu estado ou o estado de seu ambiente. Os "dados de dispositivo" podem ser classificados como qualquer informação que um dispositivo emite para quaisquer terceiros sobre seu estado e o estado observado de seu ambiente.

Para otimizar as práticas recomendadas de segurança, a arquitetura típica de IoT deve ser dividida em vários componentes ou diferentes zonas como parte do exercício de modelagem de risco. Essas zonas são descritas em detalhes ao longo desta seção e incluem:

* Dispositivo,
* Gateway de campo,
* Gateways de nuvem e
* .

As zonas são uma maneira ampla de segmentar uma solução; cada zona geralmente tem seus próprios requisitos de dados, autenticação e autorização. As zonas também podem ser usadas para o isolamento de danos e a restrição do impacto de zonas de confiança baixa nas zonas de confiança mais alta.

Cada zona é separada por um limite de confiança, que é mostrado como a linha pontilhada vermelha no diagrama a seguir. Ela representa uma transição de dados/informações de uma origem para outra. Durante essa transição, os dados/informações podem estar sujeitos a STRIDE (falsificação, violação, repúdio, divulgação não autorizada de informação, negação de serviço e elevação de privilégio).

![Zonas de segurança da IoT](media/iot-security-architecture/iot-security-architecture-fig1.png) 

Os componentes descritos em cada limite também estão sujeitos a STRIDE, permitindo uma exibição da modelagem de risco completa 360 da solução. As seções a seguir trazem mais detalhes sobre cada um dos componentes, assim como informações sobre problemas de segurança específicos e as soluções correspondentes.

As seções a seguir discutem sobre os componentes padrão geralmente encontrados nessas zonas.

### <a name="the-device-zone"></a>A zona de dispositivo

O ambiente de dispositivo é o espaço físico imediato em torno do dispositivo no qual o acesso físico e/ou o acesso digital ponto a ponto de “rede local” ao dispositivo é possível. Uma "rede local" é considerada uma rede que é distinta e isolada da, mas potencialmente com ponte para, Internet pública e inclui qualquer tecnologia de rádio sem fio de curto alcance que permite a comunicação ponto a ponto de dispositivos. Ela *não* inclui qualquer tecnologia de virtualização de rede que cria a ilusão de uma rede local nem inclui redes de operador públicas que exigiriam que quaisquer dois dispositivos se comunicassem por meio do espaço de rede pública se entrassem em uma relação de comunicação ponto a ponto.

### <a name="the-field-gateway-zone"></a>A zona de gateway de campo

Um gateway de campo é um dispositivo ou um software de computador servidor de finalidade geral que age como um habilitador de comunicação e, potencialmente, como um sistema de controle de dispositivo e um hub de processamento de dados do dispositivo. A zona do gateway de campo inclui o próprio gateway de campo e todos os dispositivos conectados a ele. Como o nome sugere, os gateways de campo atuam for a dos recursos de processamento de dados dedicados, geralmente são associados ao local, podem sofrer invasão física e têm redundância operacional limitada. Tudo para dizer que um gateway de campo normalmente é algo que uma pessoa pode tocar e sabotar sabendo qual é sua função.

Um gateway de campo é diferente de um mero roteador de tráfego em que ele teve uma função ativa no gerenciamento de acesso e fluxo de informações, o que significa que ele é um terminal de sessão ou uma conexão de rede e entidade dirigida para o aplicativo. Um firewall ou dispositivos NAT, por outro lado, não se qualificam como gateways de campo, pois não são terminais de sessão nem de conexão explícitos, mas em vez disso roteiam (ou bloqueiam) sessões ou conexões feitas através deles. O gateway de campo tem duas áreas de superfície distintas. Uma é voltada para os dispositivos que estão conectados a ela e representa o interior da zona, e a outra é voltada para todos os parceiros externos e a borda da zona.

### <a name="the-cloud-gateway-zone"></a>A zona de gateway de nuvem

O gateway de nuvem é um sistema que permite a comunicação remota de e para dispositivos ou gateways de campo de diversos locais pelo espaço de rede pública, normalmente na direção de um controle baseado em nuvem e sistema de análise de dados, uma federação de tais sistemas. Em alguns casos, um gateway de nuvem pode imediatamente facilitar o acesso a dispositivos de finalidade especial de terminais, como telefones ou tablets. No contexto discutido aqui, "nuvem" destina-se a se referir a um sistema de processamento de dados dedicado que não está associado ao mesmo site que os dispositivos ou gateways de campo conectados. Além disso, em uma zona de nuvem, medidas operacionais impedem o acesso físico direcionado e não são necessariamente expostas a uma infraestrutura de "nuvem pública".  

Um gateway de nuvem pode potencialmente ser mapeado em uma sobreposição de virtualização de rede para isolar o gateway de nuvem e todos os seus dispositivos conectados ou gateways de campo de qualquer outro tráfego de rede. O gateway de nuvem em si não é um sistema de controle de dispositivo, nem um recurso de processamento ou armazenamento de dados do dispositivo, esses recursos fazem interface com o gateway de nuvem. A zona de gateway de nuvem inclui o próprio gateway de nuvem juntamente com todos os gateways de campo e dispositivos conectados diretamente ou indiretamente a ele. A borda da zona é uma área de superfície distinta, na qual todas as partes se comunicam.

### <a name="the-services-zone"></a>A zona de serviços

Um "serviço" é definido para este contexto como qualquer componente de software ou módulo que faz interface com dispositivos através de um gateway de nuvem ou campo para coleta e análise de dados, bem como para o comando e controle. Os serviços são mediadores. Eles atuam sob sua identidade para gateways e outros subsistemas, armazenam e analisam dados, emitem comandos de forma autônoma para dispositivos com base em análises de dados ou agendas e expõem informações e controlam os recursos para os usuários finais autorizados.

### <a name="information-devices-versus-special-purpose-devices"></a>Dispositivos de informações versus dispositivos de finalidade especial

PCs, telefones e tablets são basicamente dispositivos de informações interativos. Os telefones e tablets são explicitamente otimizados em função de maximizar o tempo de vida da bateria. Eles preferencialmente são desativados parcialmente quando não estão interagindo imediatamente com uma pessoa, ou quando não estão fornecendo serviços como reproduzindo músicas ou orientando seu proprietário para um local específico. De uma perspectiva de sistemas, esses dispositivos de tecnologia da informação atuam principalmente como proxies para as pessoas. Eles são "acionadores de pessoas" sugerindo ações e "sensores de pessoas" coletando informações.

Os dispositivos de finalidade especial, de sensores de temperatura simples a linhas de produção de fábrica complexas com milhares de componentes dentro delas, são diferentes. Esses dispositivos têm um escopo muito mais definido quanto à finalidade e, mesmo quando fornecem uma interface do usuário, em grande parte eles se limitam a se unirem ou a se integrarem a ativos no mundo físico. Eles medem e relatam circunstâncias ambientais, ativar válvulas, controlam servos, soam alarmes, alternar luzes e realizam várias outras tarefas. Além disso, eles ajudam a realizar aqueles trabalhos para os quais um dispositivo de informações é muito genérico, caro, grande ou frágil. A finalidade concreta imediatamente determina seu design técnico, bem como o orçamento monetário disponível para sua produção e operação agendada do tempo de vida. A combinação desses dois fatores-chave restringe o orçamento de energia operacional disponível, o espaço físico e, portanto, os recursos de armazenamento, computação e segurança disponíveis.

Se algo "dá errado" com dispositivos controláveis remotamente ou automatizados, por exemplo, defeitos físicos ou defeitos lógicos de controle para a manipulação ou intrusão não autorizada deliberada. Os lotes de produção podem ser destruídos, edifícios podem ser saqueados ou incendiados e as pessoas podem ser feridas ou até mesmo morrer. Essa é uma classe de danos totalmente diferente, por exemplo, de uma situação de aumento de limite de um cartão de crédito roubado. O nível de segurança para dispositivos que fazem com que coisas funcionem e para dados de sensor que eventualmente resultam em comandos que fazem com que coisas funcionem deve ser maior do que em qualquer cenário de serviços bancários ou comércio eletrônico.

### <a name="device-control-and-device-data-interactions"></a>Controle de dispositivos e interações de dados do dispositivo

Dispositivos de finalidade especial conectados têm um número significativo de áreas de superfície de interação potencial e padrões de interação, que devem ser considerados para fornecer uma estrutura para proteger o acesso digital a esses dispositivos. O termo "acesso digital" é usado aqui para distinguir de todas as operações executadas por meio de interação direta do dispositivo em que a segurança de acesso é fornecida por meio do controle de acesso físico. Por exemplo, colocar o dispositivo em uma sala com uma trava na porta. Embora o acesso físico não possa ser negado usando hardware e software, é possível adotar medidas para impedir que o acesso físico leve à interferência do sistema.

Ao explorar os padrões de interação, dê ao "controle de dispositivo" e os "dados de dispositivo" a mesma atenção durante a modelagem de risco. O "controle de dispositivo" pode ser classificado como qualquer informação fornecida para um dispositivo por qualquer pessoa com o objetivo de alterar ou influenciar seu comportamento em relação ao seu estado ou o estado de seu ambiente. Os "dados de dispositivo" podem ser classificados como qualquer informação que um dispositivo emite para quaisquer terceiros sobre seu estado e o estado observado de seu ambiente.

## <a name="performing-threat-modeling-for-the-azure-iot-reference-architecture"></a>Executando a modelagem de ameaças para a arquitetura de referência do IoT do Azure

A Microsoft usa a estrutura descrita anteriormente para a modelagem de risco da IoT do Azure. A seção abaixo usa o exemplo concreto da arquitetura de referência da IoT do Azure para demonstrar como pensar sobre a modelagem de risco para IoT e como tratar as ameaças identificadas. Este exemplo identifica quatro áreas principais de foco:

* Dispositivos e fontes de dados,
* Transporte de dados,
* Dispositivo e processamento de eventos e
* Apresentação

![Modelagem de risco para a IoT do Azure](media/iot-security-architecture/iot-security-architecture-fig2.png)

O diagrama a seguir fornece uma exibição simplificada da arquitetura da IoT da Microsoft usando um modelo de Diagrama de Fluxo de Dados que é usado pela Microsoft Threat Modeling Tool:

![Modelagem de risco para a IoT do Azure usando a Ferramenta de Modelagem de Risco da Microsoft](media/iot-security-architecture/iot-security-architecture-fig3.png)

É importante observar que a arquitetura separa os recursos do dispositivo e do gateway. Essa abordagem permite ao usuário aproveitar dispositivos de gateway mais seguros, que conseguem se comunicar com o gateway de nuvem por meio de protocolos seguros, o que geralmente exige uma sobrecarga de processamento maior do que aquela que um dispositivo nativo, como um termostato, poderia fornecer sozinho. Na zona de serviços do Azure, suponha que o gateway de nuvem seja representado pelo serviço do Hub IoT do Azure.

### <a name="device-and-data-sourcesdata-transport"></a>Dispositivos e fontes de dados/transporte de dados

Esta seção explora a arquitetura descrita anteriormente do ponto de vista de modelagem de risco e mostra como lidar com algumas preocupações inerentes. Este exemplo se concentra nos principais elementos de um modelo de risco:

* Processos (tanto sob seu controle quanto itens externos)
* Comunicação (também chamada de fluxos de dados)
* Armazenamento (também chamado de armazenamentos de dados)

#### <a name="processes"></a>Processos

Em cada uma das categorias descritas na arquitetura de IoT do Azure, este exemplo tenta atenuar diversas ameaças nos diferentes estágios em que há dados/informações: processo, comunicação e armazenamento. Veja a seguir uma visão geral das ameaças mais comuns para a categoria "processo", assim como práticas recomendadas de mitigação:

**Falsificação (S)**: Um invasor pode extrair o material da chave de criptografia de um dispositivo, no nível de software ou hardware, e posteriormente acessar o sistema com outro dispositivo físico ou virtual sob a identidade do dispositivo do qual o material da chave foi obtido. Uma boa ilustração são os controles remotos que podem ligar qualquer TV e são ferramentas populares dos pregadores de peças.

**Negação de Serviço (D)**: Um dispositivo pode ficar incapaz de funcionar ou de se comunicar interferindo em frequências de rádio ou cortando fios. Por exemplo, uma câmera de vigilância que teve sua conexão de rede ou de energia intencionalmente suprimida não relata dados, de maneira alguma.

**Violação (T)**: Um invasor pode substituir parcial ou totalmente o software em execução no dispositivo, potencialmente permitindo que o software substituído aproveite a identidade original do dispositivo se o material da chave ou os recursos de criptografia mantendo os materiais de chave estavam disponíveis para o programa ilícito. Por exemplo, um invasor pode aproveitar o material da chave extraído para interceptar e suprimir os dados do dispositivo no caminho de comunicação e substituí-los por dados falsos que são autenticados com o material da chave roubado.

**Divulgação de informações confidenciais (I)**: Se o dispositivo estivesse executando software manipulado, tal software manipulado poderia potencialmente vazar dados para partes não autorizadas. Por exemplo, um invasor pode aproveitar o material da chave extraído para se injetar no caminho de comunicação entre o dispositivo e um controlador ou gateway de campo ou um gateway de nuvem para desviar as informações.

**Elevação de privilégio (E)**: Um dispositivo que realiza uma função específica pode ser forçado a fazer algo diferente. Por exemplo, uma válvula é programada para abrir a metade do caminho pode ser levada para abrir completamente.

| **Componente** | **Ameaça** | **Mitigação** | **Risco** | **Implementação** |
| --- | --- | --- | --- | --- |
| Dispositivo |S |Atribuição de identidade para o dispositivo e autenticação do dispositivo |Substituir o dispositivo ou parte dele por outro dispositivo. Como saber se você está se comunicando com o dispositivo certo? |Autenticar o dispositivo, usando o protocolo TLS ou IPsec. A infraestrutura deve dar suporte ao uso da PSK (chave pré-compartilhada) nos dispositivos que não conseguem lidar com a criptografia assimétrica completa. Aproveite o Azure AD, o [OAuth](https://www.rfc-editor.org/pdfrfc/rfc6755.txt.pdf) |
|| TRID |Aplicação de mecanismos à prova de violações no dispositivo, por exemplo, tornando difícil e até mesmo impossível extrair as chaves e outros materiais de criptografia do dispositivo. |O risco é se alguém está violando o dispositivo (interferência física). Como ter certeza de que o dispositivo não foi adulterado. |A mitigação mais eficaz é uma funcionalidade de TPM (trusted platform module) que permite armazenar chaves em circuitos on-chip especiais nos quais as chaves não podem ser lidas, mas apenas usadas para operações de criptografia que utilizam a chave, mas nunca a divulgam. Criptografia de memória do dispositivo. Gerenciamento de chaves para o dispositivo. Assinar o código. |
|| E |Ter controle de acesso do dispositivo. Esquema de autorização. |Se o dispositivo permitir que ações individuais sejam executadas com base em comandos de uma fonte externa, ou até mesmo sensores comprometidos, isso permite que o ataque execute operações não acessíveis de outra forma. |Ter um esquema de autorização para o dispositivo |
| Gateway de campo |S |Autenticação do gateway de campo no gateway de nuvem (por exemplo, baseada em certificado, PSK ou em declarações.) |Se alguém pode falsificar o gateway de campo, pode se apresentar como qualquer dispositivo. |TLS RSA/PSK, IPSec, [RFC 4279](https://tools.ietf.org/html/rfc4279). As mesmas preocupações de confirmação e armazenamento de chave dos dispositivos em geral, o melhor caso é usar o TPM. Extensão de 6LowPAN para o IPsec para dar suporte a WSN (Redes de Sensores Sem Fio). |
|| TRID |Proteger o gateway de campo contra violação (TPM?) |Os ataques de falsificação que enganam gateway de nuvem pensando que ele está se comunicando com o gateway de campo podem resultar na divulgação não autorizada de informação e na violação dos dados. |Criptografia da memória, TPM, autenticação. |
|| E |Mecanismo de controle de acesso para o gateway de campo | | |

Aqui estão alguns exemplos de ameaças nesta categoria:

**Falsificação**: Um invasor pode extrair o material da chave de criptografia de um dispositivo, no nível de software ou hardware, e posteriormente acessar o sistema com outro dispositivo físico ou virtual sob a identidade do dispositivo do qual o material da chave foi obtido.

**Negação de Serviço**: Um dispositivo pode ficar incapaz de funcionar ou de se comunicar interferindo em frequências de rádio ou cortando fios. Por exemplo, uma câmera de vigilância que teve sua conexão de rede ou de energia intencionalmente suprimida não relata dados, de maneira alguma.

**Violação**: Um invasor pode substituir parcial ou totalmente o software em execução no dispositivo, potencialmente permitindo que o software substituído aproveite a identidade original do dispositivo se o material da chave ou os recursos de criptografia mantendo os materiais de chave estavam disponíveis para o programa ilícito.

**Violação**: Uma câmera de vigilância que está mostrando uma imagem de espectro visível de um corredor vazio pode ser direcionada a uma fotografia de tal um corredor. Um sensor de fumaça ou incêndio pode relatar alguém segurando um isqueiro sob ele. Em ambos os casos, o dispositivo pode ser tecnicamente totalmente confiável para o sistema, mas ele relata informações manipuladas.

**Violação**: Um invasor pode aproveitar o material da chave extraído para interceptar e suprimir os dados do dispositivo no caminho de comunicação e substituí-los com dados falsos que são autenticados com o material da chave roubado.

**Violação**: Um invasor pode substituir parcial ou completamente o software em execução no dispositivo, potencialmente permitindo que o software substituído aproveite a identidade original do dispositivo se o material da chave ou os recursos de criptografia mantendo os materiais de chave estavam disponíveis para o programa ilícito.

**Divulgação de informações confidenciais**: Se o dispositivo estivesse executando software manipulado, tal software manipulado poderia potencialmente vazar dados para partes não autorizadas.

**Divulgação de informações confidenciais**: Um invasor pode aproveitar o material da chave extraído para se injetar no caminho de comunicação entre o dispositivo e um controlador ou gateway de campo ou um gateway de nuvem para desviar as informações.

**Negação de Serviço**: O dispositivo pode ser desativado ou alterado para um modo em que a comunicação não é possível (o que é intencional em muitas máquinas industriais).

**Violação**: O dispositivo pode ser reconfigurado para operar em um estado desconhecido para o sistema de controle (fora de parâmetros de calibragem conhecidos) e, portanto, fornecer dados que podem ser interpretados incorretamente

**Elevação de privilégio**: Um dispositivo que realiza uma função específica pode ser forçado a fazer algo diferente. Por exemplo, uma válvula é programada para abrir a metade do caminho pode ser levada para abrir completamente.

**Negação de Serviço**: O dispositivo pode ser alterado para um estado em que a comunicação não é possível.

**Violação**: O dispositivo pode ser reconfigurado para operar em um estado desconhecido para o sistema de controle (fora de parâmetros de calibragem conhecidos) e, portanto, fornecer dados que podem ser interpretados incorretamente.

**Violação/falsificação/repúdio**: Se não protegido (que raramente é o caso com controles remotos do consumidor), um invasor poderá manipular o estado de um dispositivo anonimamente. Uma boa ilustração são os controles remotos que podem ligar qualquer TV e são ferramentas populares dos pregadores de peças.

#### <a name="communication"></a>Comunicação

Ameaças em torno do caminho de comunicação entre dispositivos, dispositivos e gateways de campo e dispositivos e gateway de nuvem. A tabela a seguir tem algumas diretrizes em torno de soquetes abertos no dispositivo/VPN:

| **Componente** | **Ameaça** | **Mitigação** | **Risco** | **Implementação** |
| --- | --- | --- | --- | --- |
| Hub IoT de dispositivo |TID |(D)TLS (PSK/RSA) para criptografar o tráfego |Espionagem ou interferência na comunicação entre o dispositivo e o gateway |Segurança no nível do protocolo. Com os protocolos personalizados, você precisa descobrir como protegê-los. Na maioria dos casos, a comunicação ocorre do dispositivo para o Hub IoT (o dispositivo inicia a conexão). |
| Dispositivo para dispositivo |TID |(D)TLS (PSK/RSA) para criptografar o tráfego. |Leitura de dados em trânsito entre os dispositivos. Violação de dados. Sobrecarga do dispositivo com novas conexões |Segurança no nível do protocolo (MQTT/AMQP/HTTP/CoAP. Com os protocolos personalizados, você precisa descobrir como protegê-los. A mitigação da ameaça de DoS é para os dispositivos pares por meio de um gateway de campo ou nuvem e fazê-los agir somente como clientes para a rede. O emparelhamento pode resultar em uma conexão direta entre os pares após ter sido agenciado pelo gateway |
| Dispositivo de entidade externa |TID |Emparelhamento forte da entidade externa com o dispositivo |Espionagem da conexão com o dispositivo. Interferência na comunicação com o dispositivo |Emparelhar de forma segura a entidade externa com o dispositivo NFC/Bluetooth LE. Controlar o painel operacional do dispositivo (físico) |
| Gateway de nuvem de Gateway de campo |TID |TLS (PSK/RSA) para criptografar o tráfego. |Espionagem ou interferência na comunicação entre o dispositivo e o gateway |Segurança no nível de protocolo (MQTT/AMQP/HTTP/CoAP). Com os protocolos personalizados, você precisa descobrir como protegê-los. |
| Gateway de nuvem do dispositivo |TID |TLS (PSK/RSA) para criptografar o tráfego. |Espionagem ou interferência na comunicação entre o dispositivo e o gateway |Segurança no nível de protocolo (MQTT/AMQP/HTTP/CoAP). Com os protocolos personalizados, você precisa descobrir como protegê-los. |

Aqui estão alguns exemplos de ameaças nesta categoria:

**Negação de Serviço**: Os dispositivos restritos geralmente estão sob ameaça DoS quando escutam ativamente as conexões de entrada ou datagramas não solicitadas em uma rede, porque um invasor pode abrir várias conexões em paralelo e não atendê-las ou atendê-las lentamente ou o dispositivo pode ser inundado com o tráfego não solicitado. Em ambos os casos, o dispositivo efetivamente pode ficar inoperante na rede.

**Falsificação, Divulgação não autorizada de informação**: Os dispositivos restritos e de finalidade especial geralmente têm recursos de segurança de um-para-todos como proteção de PIN ou senha ou dependem inteiramente da rede, o que significa que eles concedem acesso às informações quando um dispositivo estiver na mesma rede e essa rede geralmente estará protegida apenas por uma chave compartilhada. Isso significa que quando o segredo compartilhado para o dispositivo ou rede é divulgado, é possível controlar o dispositivo ou observar os dados emitidos do dispositivo.  

**Falsificação**: um invasor pode interceptar ou parcialmente substituir a difusão e falsificar o originador (intermediário)

**Violação**: um invasor pode interceptar ou substituir parcialmente a difusão e enviar informações falsas 

**Divulgação não autorizada de informação:** um invasor pode escutar uma difusão e obter informações sem autorização **Negação de serviço:** um invasor pode obstruir o sinal de difusão e negar a distribuição de informações

#### <a name="storage"></a>Armazenamento

Cada dispositivo e gateway de campo têm alguma forma de armazenamento (temporário para enfileirar os dados, armazenamento de imagem de sistema operacional).

| **Componente** | **Ameaça** | **Mitigação** | **Risco** | **Implementação** |
| --- | --- | --- | --- | --- |
| Armazenamento de dispositivo |TRID |Criptografia de armazenamento, assinar os logs |Leitura de dados do armazenamento (dados de PII), violação de dados de telemetria. Violação de dados de controle de comando em cache ou enfileirados. Violação de pacotes de atualização de firmware ou configuração enquanto os armazenados em cache ou enfileirados localmente podem levar os componentes de sistema operacional e/ou sistema a serem comprometidos |Criptografia, MAC (Message Authentication Code) ou assinatura digital. Onde for possível, controle de acesso forte por meio de permissões ou ACLs (listas de controle de acesso) de recurso. |
| Imagem do sistema operacional do dispositivo |TRID | |Violação do sistema operacional/substituição de componentes do sistema operacional |Partição do sistema operacional somente leitura, imagem do sistema operacional assinada, criptografia |
| Armazenamento de gateway de campo (enfileirando os dados) |TRID |Criptografia de armazenamento, assinar os logs |Leitura de dados do armazenamento (dados de PII), violação de dados de telemetria, violação de dados de controle de comando enfileirado ou em cache. Violação de pacotes de atualização de firmware ou configuração (destinados a dispositivos ou gateway de campo) enquanto os armazenados em cache ou enfileirados localmente podem levar os componentes de sistema operacional e/ou sistema a serem comprometidos |BitLocker |
| Imagem do sistema operacional do gateway de campo |TRID | |Violação do sistema operacional/substituição de componentes do sistema operacional |Partição do sistema operacional somente leitura, imagem do sistema operacional assinada, criptografia |

### <a name="device-and-event-processingcloud-gateway-zone"></a>Zona de gateway de nuvem/processamento de eventos e dispositivo

Um gateway de nuvem é o sistema que permite a comunicação remota de e para dispositivos ou gateways de campo de diversos locais pelo espaço de rede pública, normalmente na direção de um controle baseado em nuvem e sistema de análise de dados, uma federação de tais sistemas. Em alguns casos, um gateway de nuvem pode imediatamente facilitar o acesso a dispositivos de finalidade especial de terminais, como telefones ou tablets. No contexto discutido aqui, "nuvem" se refere a um sistema de processamento de dados dedicado não associado ao mesmo site que os gateways de campo ou dispositivos conectados e no qual medidas operacionais impedem o acesso físico direcionado, mas não necessariamente a uma infraestrutura de “nuvem pública”. Um gateway de nuvem pode potencialmente ser mapeado em uma sobreposição de virtualização de rede para isolar o gateway de nuvem e todos os seus dispositivos conectados ou gateways de campo de qualquer outro tráfego de rede. O gateway de nuvem em si não é um sistema de controle de dispositivo, nem um recurso de processamento ou armazenamento de dados do dispositivo, esses recursos fazem interface com o gateway de nuvem. A zona de gateway de nuvem inclui o próprio gateway de nuvem juntamente com todos os gateways de campo e dispositivos conectados diretamente ou indiretamente a ele.

O gateway de nuvem é principalmente uma peça de software personalizada em execução como um serviço com pontos de extremidade expostos aos quais o gateway de campo e os dispositivos se conectam. Como tal, deve ser projetado tendo em mente a segurança. Siga o processo de [SDL](https://www.microsoft.com/sdl) para projetar e criar esse serviço.

#### <a name="services-zone"></a>Zona de serviços

Um sistema de controle (ou controlador) é uma solução de software que faz a interface com um dispositivo, um gateway de campo ou um gateway de nuvem a fim de controlar um ou vários dispositivos e/ou coletar, e/ou armazenar e/ou analisar os dados do dispositivo para fins de apresentação ou controle subsequente. Os sistemas de controle são as únicas entidades no escopo desta discussão que podem facilitar a interação com as pessoas imediatamente. A exceção são as superfícies de controle físico intermediário nos dispositivos, como uma chave que permite que uma pessoa desligue o dispositivo ou altere outras propriedades, e para as quais não há nenhum equivalente funcional que possa ser acessado digitalmente.

As superfícies de controle físico intermediário são aquelas em que a lógica de controle restringe a função da superfície de controle físico, de modo que uma função equivalente possa ser iniciada remotamente ou os conflitos de entrada com a entrada remota possam ser evitado. Tais superfícies de controle intermediadas são conceitualmente anexadas a um sistema de controle local que utiliza a mesma funcionalidade subjacente que qualquer outro sistema de controle remoto que ao qual o dispositivo pode estar anexado em paralelo. As principais ameaças para a computação em nuvem podem ser lidas na página da [CSA (Cloud Security Alliance)](https://cloudsecurityalliance.org/articles/csa-releases-top-threats-to-cloud-computing-deep-dive/).

## <a name="additional-resources"></a>Recursos adicionais

Para obter mais informações, consulte os seguintes artigos:

* [Ferramenta de modelagem de ameaças do SDL](https://www.microsoft.com/sdl/adopt/threatmodeling.aspx)
* [Arquitetura de referência da IoT do Microsoft Azure](https://azure.microsoft.com/updates/microsoft-azure-iot-reference-architecture-available/)
