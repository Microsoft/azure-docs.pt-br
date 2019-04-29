---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: iot-fundamentals
author: robinsh
ms.service: iot-fundamentals
ms.topic: include
ms.date: 04/24/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 14e8eb7c73a1e2cf5047410d3571008c1cd1e1ca
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60626446"
---
# <a name="security-for-internet-of-things-iot-from-the-ground-up"></a>Segurança para a Internet das Coisas (IoT) a partir do zero

A Internet das Coisas (IoT) apresenta desafios específicos de segurança, privacidade e conformidade para empresas em todo o mundo. Ao contrário da tecnologia cibernética tradicional, na qual esses problemas giram em torno do software e de como ele é implementado, a IoT se preocupa com o que acontece quando os mundos físico e cibernético convergem. Proteger as soluções da IoT exige a garantia de provisionamento seguro dos dispositivos, a conectividade segura entre eles e a nuvem e a proteção garantida dos dados na nuvem durante o processamento e o armazenamento. Trabalhando contra essa funcionalidade, no entanto, estão os dispositivos com recursos limitados, a distribuição geográfica das implantações e um grande número de dispositivos em uma solução.

Este artigo explora como os aceleradores da solução de IoT fornecem uma solução de nuvem de Internet das Coisas privada e segura. Os aceleradores de solução fornecem uma solução completa de ponta a ponta, com a segurança incorporada em cada estágio, desde o princípio. Na Microsoft, desenvolver softwares seguros é parte da prática de engenharia de software, enraizada na longa experiência da Microsoft no desenvolvimento de softwares seguros. Para garantir isso, o SDL (Security Development Lifecycle) é a metodologia de desenvolvimento fundamental, juntamente com uma gama de serviços de segurança em nível de infraestrutura, como a OSA (Garantia de Segurança Operacional) e a Unidade de Crimes Digitais da Microsoft, o Microsoft Security Response Center e o Centro de Proteção contra Malware da Microsoft.

Os aceleradores de solução oferecem recursos exclusivos que tornam o provisionamento, a conexão e o armazenamento de dados dos dispositivos de IoT fáceis e transparentes e, acima de tudo, seguros. Este artigo examina os recursos de segurança dos aceleradores da solução de IoT do Azure e as estratégias de implantação para garantir que a segurança, a privacidade e os desafios de conformidade sejam abordados.

## <a name="introduction"></a>Introdução

A IoT (Internet das Coisas) é a onda do futuro, oferecendo oportunidades imediatas e reais às empresas para reduzir custos, aumentar a receita e transformar os negócios. Muitas empresas, no entanto, demonstram dúvida quanto a implantar a IoT em suas organizações devido a preocupações com segurança, privacidade e conformidade. O principal ponto de preocupação é oriundo da exclusividade da infraestrutura da IoT, que mescla os mundos cibernético e físico, combinando os riscos individuais inerentes a esses dois mundos. A segurança da IoT se refere à garantia da integridade do código em execução nos dispositivos, fornecendo autenticação de usuários e dispositivos, definindo a propriedade correta dos dispositivos (bem como dos dados gerados por esses dispositivos) e sendo resistente a ataques físicos e cibernéticos.

Então, há o problema de privacidade. As empresas querem transparência sobre a coleta de dados, sobre o que está sendo coletado e o motivo, quem pode vê-los, quem controla o acesso e assim por diante. Por fim, há problemas gerais de segurança do equipamento, juntamente com as pessoas que o operam e os problemas de manutenção dos padrões de conformidade.

Dada a segurança, a privacidade, a transparência e as questões de conformidade, escolher o provedor de solução de IoT correto continua sendo um desafio. A união das partes individuais dos software e dos serviços de IoT fornecidos por uma variedade de fornecedores apresenta lacunas na segurança, na privacidade, na transparência e na conformidade, que podem ser difíceis de detectar, sem falar em corrigir. A escolha do software e do serviço de IoT corretos é fundamentada na localização de provedores que tenham vasta experiência em executar serviços que se estendem por verticais e regiões geográficas, mas que também possam ser dimensionados de forma segura e transparente. Da mesma forma, é útil para o provedor selecionado ter décadas de experiência no desenvolvimento de softwares seguros executados em bilhões de computadores em todo o mundo e ter a capacidade de avaliar o panorama de ameaças imposto por esse novo mundo da Internet das Coisas.

## <a name="secure-infrastructure-from-the-ground-up"></a>Infraestrutura segura desde o princípio

A infraestrutura do [Microsoft Cloud](https://azure.microsoft.com) dá suporte para mais de um bilhão de clientes em 127 países. Com base em décadas de experiência da Microsoft na criação de softwares empresariais e na execução de alguns dos maiores serviços online do mundo, o Microsoft Cloud fornece níveis mais altos de segurança, privacidade, conformidade e práticas de atenuação de ameaças aprimorados do que a maioria dos clientes poderia obter por conta própria.

O [SDL (Ciclo de Vida de Desenvolvimento da Segurança)](https://www.microsoft.com/sdl/) fornece um processo de desenvolvimento obrigatório em toda a empresa que incorpora os requisitos de segurança no ciclo de vida do software. Para ajudar a garantir que as atividades operacionais seguirão o mesmo nível de práticas de segurança, o SDL emprega diretrizes rígidas de segurança dispostas em processo de OSA (Garantia de Segurança Operacional) da Microsoft. A Microsoft também trabalha com empresas de auditoria terceirizadas para que haja uma verificação contínua do seu cumprimento com as obrigações de conformidade, e a Microsoft também participa em esforços amplos de segurança por meio da criação de centros de excelência, incluindo a Unidade de Crimes Digitais da Microsoft, o Microsoft Security Response Center e o Centro de Proteção contra Malware da Microsoft.

## <a name="microsoft-azure---secure-iot-infrastructure-for-your-business"></a>Microsoft Azure - infraestrutura segura da IoT para os seus negócios

O Microsoft Azure oferece uma solução completa de nuvem, que combina uma coleção em constante crescimento dos serviços de nuvem integrados — análise, aprendizado de máquina, armazenamento, segurança, rede e web — com um compromisso líder do setor para a proteção e a privacidade dos seus dados. A estratégia de [violação pressuposta](https://azure.microsoft.com/blog/red-teaming-using-cutting-edge-threat-simulation-to-harden-the-microsoft-enterprise-cloud/) da Microsoft emprega uma *equipe vermelha* dedicada de especialistas em segurança de software que simulam ataques, testando a capacidade do Azure de detectar, proteger contra ameaças emergentes e de se recuperar de falhas. A equipe de [resposta a incidentes globais](https://www.microsoft.com/en-us/TrustCenter/Security/DesignOpSecurity) da Microsoft trabalha o tempo todo para reduzir os efeitos de ataques e de atividades mal-intencionadas. A equipe segue procedimentos estabelecidos para gerenciamento de incidentes, comunicação e recuperação e usa interfaces detectáveis e previsíveis com parceiros internos e externos.

Os sistemas da Microsoft oferecem detecção e previsão de intrusão contínua, prevenção de ataques de serviço, testes de penetração regulares e ferramentas forenses que ajudam a identificar e atenuar as ameaças. [Multi-Factor Authentication](../articles/active-directory/authentication/multi-factor-authentication.md) fornece uma camada extra de segurança para que os usuários finais acessem a rede. E, para o aplicativo e para o provedor de host, a Microsoft oferece controle de acesso, monitoramento, antimalware, verificação de vulnerabilidade, patches e gerenciamento de configuração.

Os aceleradores de solução tiram proveito da segurança e da privacidade incorporadas à plataforma do Azure juntamente com os processos de SDL e OSA para o desenvolvimento e a operação com segurança de todos os softwares da Microsoft. Esses procedimentos fornecem proteção da infraestrutura, proteção da rede e recursos de gerenciamento e identidades fundamentais para a segurança de qualquer solução.

O [Hub IoT do Azure](../articles/iot-hub/about-iot-hub.md) nos [aceleradores de solução de IoT](../articles/iot-fundamentals/iot-introduction.md) oferece um serviço completamente gerenciado que habilita uma comunicação bidirecional confiável e segura entre os dispositivos de IoT e os serviços do Azure, como o [Azure Machine Learning](../articles/machine-learning/studio/what-is-machine-learning.md) e o [Azure Stream Analytics](../articles/stream-analytics/stream-analytics-introduction.md), ao usar as credenciais de segurança por dispositivo e o controle de acesso.

Para comunicar melhor os recursos de segurança e privacidade incorporados aos aceleradores de solução de IoT do Azure, este artigo divide o pacote em três áreas de segurança principais.

![Aceleradores de solução de IoT do Azure](media/iot-security-ground-up/securing-iot-ground-up-fig3.png)

### <a name="secure-device-provisioning-and-authentication"></a>Provisionamento e autenticação com segurança dos dispositivos

Os aceleradores de solução protegem os dispositivos enquanto estiverem fora do campo, fornecendo uma chave de identidade exclusiva para cada um deles, que pode ser usada pela infraestrutura de IoT para se comunicar com o dispositivo enquanto ele estiver em operação. O processo é rápido e fácil de configurar. A chave gerada com uma identificação de dispositivo de usuário selecionado constitui a base de um token usado em toda a comunicação entre o dispositivo e o Hub IoT do Azure.

As identificações de dispositivo podem ser associadas a um dispositivo durante a fabricação (ou seja, atualizadas em um módulo de confiança de hardware) ou podem usar uma identidade fixa existente como um proxy (por exemplo, números de série da CPU). Visto que alterar essa informação de identificação do dispositivo não é simples, é importante apresentar identificações de dispositivo lógico caso o hardware do dispositivo subjacente seja alterado, mas o dispositivo lógico permaneça o mesmo. Em alguns casos, a associação de uma identidade de dispositivo pode ocorrer no momento da implantação do dispositivo (por exemplo, um Field Engineer autenticado configura fisicamente um novo dispositivo durante a comunicação com o back-end da solução). O [Registro de identidade do Hub IoT do Azure](../articles/iot-hub/iot-hub-devguide.md) fornece armazenamento seguro de identidades de dispositivo e chaves de segurança para uma solução. As identidades de dispositivos individuais ou em grupo podem ser adicionadas a uma lista de permissões ou a uma lista de contatos bloqueados, permitindo o controle completo sobre o acesso ao dispositivo.

As políticas de controle de acesso do Hub IoT do Azure na nuvem habilitam a ativação e a desabilitação de qualquer identidade do dispositivo, fornecendo um modo para desassociar um dispositivo de uma implantação de IoT quando necessário. Essa associação e desassociação de dispositivos se baseia em cada identidade do dispositivo.

Os recursos de segurança de dispositivo adicionais incluem:

* Os dispositivos não aceitam conexões de rede não solicitadas. Eles estabelecem todas as conexões e rotas somente para saída. Para que um dispositivo receba um comando do back-end, ele deverá iniciar uma conexão para verificar a existência de qualquer comando pendente a ser processado. Quando uma conexão entre o dispositivo e o Hub IoT é estabelecida com segurança, as mensagens da nuvem para o dispositivo e do dispositivo para a nuvem podem ser enviadas de maneira transparente.

* Os dispositivos só devem se conectar ou estabelecer rotas para serviços bem conhecidos com os quais estejam emparelhados, como um Hub IoT do Azure.

* A autorização e a autenticação no nível do sistema usam identidades por dispositivo, tornando as credenciais e as permissões de acesso revogáveis quase que instantaneamente.

### <a name="secure-connectivity"></a>Conectividade segura

A durabilidade das mensagens é um recurso importante de qualquer solução de IoT. A necessidade de fornecer comandos e/ou receber dados de dispositivos de forma permanente é evidenciada pelo fato de que os dispositivos de IoT são conectados via Internet ou por outras redes semelhantes que podem não ser confiáveis. O Hub IoT do Azure oferece durabilidade de mensagens entre a nuvem e os dispositivos por meio de um sistema de confirmações em resposta às mensagens. A durabilidade adicional para envio de mensagens é alcançada pelo caching de mensagens no Hub IoT para até sete dias de telemetria e dois dias para comandos.

A eficiência é importante para garantir a conservação de recursos e a operação em um ambiente com recursos limitados. O HTTPS (HTTP seguro), a versão segura padrão do setor do protocolo de http popular, tem suporte do Hub IoT do Azure, permitindo uma comunicação eficiente. O AMQP (Advanced Message Queuing Protocol) e o MQTT (Transporte de Telemetria de Enfileiramento de Mensagens), com suporte do Hub IoT do Azure, são projetados não apenas para a eficiência em termos de uso de recursos, mas também para a entrega confiável de mensagens. 

A escalabilidade exige a capacidade de interoperar com segurança com uma ampla variedade de dispositivos. O Hub IoT do Azure habilita a conexão segura com dispositivos habilitados para IP ou não. Os dispositivos habilitados para IP são capazes de se conectar diretamente e de se comunicar com o Hub IoT em uma conexão segura. Os dispositivos não habilitados para IP estão com recursos limitados e se conectam apenas por meio de protocolos de comunicação de curta distância, como Zwave, ZigBee e Bluetooth. Um gateway de campo é usado para agregar esses dispositivos e executa a conversão de protocolo para habilitar a comunicação bidirecional segura com a nuvem.

Os recursos de segurança de conexão adicionais incluem:

* O caminho de comunicação entre os dispositivos e o Hub IoT do Azure, ou entre os gateways e o Hub IoT do Azure, é protegido usando o padrão do setor TLS (Segurança de Camada de Transporte) com o Hub IoT do Azure autenticado usando o protocolo X.509.

* Para proteger dispositivos contra conexões de entrada não solicitadas, o Hub IoT do Azure não abre nenhuma conexão com o dispositivo. O dispositivo inicia todas as conexões.

* O Hub IoT do Azure armazena permanentemente as mensagens para os dispositivos e aguarda o dispositivo se conectar. Esses comandos são armazenados por dois dias, permitindo que os dispositivos se conectem esporadicamente devido a questões de conectividade ou de energia para receber esses comandos. O Hub IoT do Azure mantém uma fila por dispositivo para cada dispositivo.

### <a name="secure-processing-and-storage-in-the-cloud"></a>Processamento e armazenamento seguros na nuvem

Desde comunicações criptografadas até o processamento de dados na nuvem, os aceleradores de solução ajudam a manter os dados protegidos. Ele fornece flexibilidade para implementar criptografias adicionais e o gerenciamento de chaves de segurança.

Usando o AAD (Azure Active Directory) para autorização e autenticação de usuário, os aceleradores de solução de IoT do Azure podem fornecer um modelo de autorização baseado em políticas para dados na nuvem, permitindo o fácil gerenciamento de acesso, que pode ser auditado e analisado. Esse modelo também habilita a revogação quase instantânea de acesso aos dados na nuvem e de dispositivos conectados aos aceleradores de solução de IoT do Azure.

Uma vez que os dados estiverem na nuvem, eles podem ser processados e armazenados em qualquer fluxo de trabalho definido pelo usuário. O acesso a cada parte dos dados é controlado com o Azure Active Directory, dependendo do serviço de armazenamento usado.

Todas as chaves usadas pela infraestrutura de IoT são armazenadas na nuvem no armazenamento seguro, com a capacidade de sobrepor caso as chaves precisem ser provisionadas novamente. Os dados podem ser armazenados no [Azure Cosmos DB](../articles/cosmos-db/introduction.md) ou em [Bancos de dados SQL](../articles/sql-database/sql-database-faq.md), permitindo a definição do nível de segurança desejado. Além disso, o Azure fornece uma maneira para monitorar e auditar todo o acesso aos seus dados para alertar você sobre qualquer invasão ou acesso não autorizado.

## <a name="conclusion"></a>Conclusão

A Internet das Coisas começa com suas coisas — as coisas mais importantes para os negócios. A IoT pode fornecer um valor incrível para uma empresa ao reduzir os custos, aumentar a receita e transformar os negócios. O sucesso dessa transformação depende amplamente da escolha do software de IoT e do provedor de serviços corretos. Isso significa encontrar um provedor que não apenas catalise essa transformação ao compreender as necessidades e exigências comerciais, mas que também preste serviços e forneça softwares criados com segurança, privacidade, transparência e conformidade como as principais considerações do projeto. A Microsoft tem ampla experiência no desenvolvimento e implantação de softwares e serviços seguros e continua a ser a líder nesta nova era da Internet das Coisas.

Os aceleradores de solução incorporam medidas de segurança por projeto, permitindo o monitoramento seguro de ativos para melhorar a eficiência, conduz o desempenho operacional para habilitar a inovação e emprega a análise de dados avançada para transformar as empresas. Com sua abordagem em camadas para a segurança, vários recursos de segurança e padrões de design, os aceleradores de solução ajudam a implantar uma infraestrutura que pode ser confiável para transformar qualquer empresa.

## <a name="additional-information"></a>Informações adicionais

Cada acelerador de solução cria instâncias de serviços do Azure, como:

* [**O Hub IoT**](https://azure.microsoft.com/services/iot-hub/): Seu gateway que conecta a nuvem para dispositivos. Você pode dimensionar para milhões de conexões por hub e processar grandes volumes de dados com suporte de autenticação por dispositivo, que ajuda você a proteger sua solução.

* [**Azure Cosmos DB**](https://azure.microsoft.com/services/cosmos-db/): Um serviço de banco de dados escalonável e totalmente indexado para dados semiestruturados que gerencia os metadados para os dispositivos que você provisiona, como atributos, configuração e propriedades de segurança. O Azure Cosmos DB oferece processamento de alto desempenho e alta produtividade, indexação de dados independente de esquema e uma interface de consulta SQL avançada.

* [**Azure Stream Analytics**](https://azure.microsoft.com/services/stream-analytics/): Processamento na nuvem que permite que você desenvolva e implante com rapidez uma solução de análise de baixo custo para descobrir insights em tempo real de dispositivos, sensores, infraestrutura e aplicativos de fluxo em tempo real. Os dados desse serviço totalmente gerenciado podem ser dimensionados para qualquer volume enquanto ainda atingem alta taxa de transferência, baixa latência e resiliência.

* [**Serviços de aplicativo do Azure**](https://azure.microsoft.com/services/app-service/): Uma plataforma de nuvem para criar poderosos aplicativos web e móveis que se conectam aos dados em qualquer lugar; na nuvem ou local. Compile aplicativos móveis atraentes para iOS, Android e Windows. Integre-se com seu SaaS (software como serviço) e com aplicativos empresariais com conectividade integrada para dezenas de serviços baseados em nuvem e aplicativos empresariais. Codifique na sua linguagem e IDE favoritos (.NET, NodeJS, PHP, Python ou Java) para compilar aplicativos Web e APIs com mais rapidez do que nunca.

* [**Aplicativos lógicos**](https://azure.microsoft.com/services/app-service/logic/): O recurso aplicativos lógicos do serviço de aplicativo do Azure ajuda a integrar sua solução de IoT para seus sistemas de linha de negócios existentes e automatizar processos de fluxo de trabalho. Os Aplicativos Lógicos permitem que os desenvolvedores projetem fluxos de trabalho iniciados de um gatilho e, em seguida, executem uma série de etapas — regras e ações que usam conectores poderosos para integrar seus processos de negócios. Os Aplicativos Lógicos oferecem conectividade pronta para uso para um vasto ecossistema de aplicativos de SaaS, baseados em nuvem e locais.

* [**O armazenamento de BLOBs do Azure**](https://azure.microsoft.com/services/storage/): Armazenamento em nuvem confiável e econômico para os dados que os dispositivos enviam para a nuvem.