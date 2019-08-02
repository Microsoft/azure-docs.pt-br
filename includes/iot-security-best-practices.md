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
ms.openlocfilehash: 2138eed9975abe804442c476d19b5b7229685362
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172037"
---
# <a name="security-best-practices-for-internet-of-things-iot"></a>Práticas recomendadas de segurança para Internet das Coisas (IoT)

Proteger uma infraestrutura de Internet das Coisas (IoT) requer uma estratégia de segurança em camadas rigorosa. Essa estratégia exige que você proteja dados na nuvem, proteja a integridade dos dados enquanto eles estão em trânsito na Internet pública e provisione dispositivos com segurança. Cada camada cria uma maior garantia de segurança na infraestrutura geral.

## <a name="secure-an-iot-infrastructure"></a>Proteger uma infraestrutura de IoT

Essa estratégia de segurança em camadas pode ser desenvolvida e executada com a participação ativa de vários participantes envolvidos na produção, desenvolvimento e implantação de dispositivos e infraestrutura de IoT. Veja a seguir uma descrição de alto nível desses participantes.

* **Fabricante/integrador de hardware de IoT**: Normalmente, esses participantes são os fabricantes de hardware de IoT que está sendo implantado, os integradores montando hardware de vários fabricantes ou os fornecedores de hardware para uma implantação de IoT fabricado ou integrado por outros fornecedores.

* **Desenvolvedor de soluções IoT**: O desenvolvimento de uma solução de IoT normalmente é feito por um desenvolvedor de soluções. Esse desenvolvedor pode ser parte de uma equipe interna ou um SI (integrador de sistema) especializado nesta atividade. O desenvolvedor de soluções de IoT pode desenvolver vários componentes da solução IoT do zero, integrar vários componentes prontos ou de código aberto ou adotar aceleradores de soluções com pequenas adaptações.

* **Implantador de solução IoT**: Depois que uma solução de IoT é desenvolvida, ele precisa ser implantado no campo. Esse processo envolve a implantação de hardware, a interconexão de dispositivos e a implantação de soluções nos dispositivos de hardware ou na nuvem.

* **Operador de solução IoT**: Depois que a solução de IoT é implantada, ele requer operações de longo prazo, monitoramento, atualizações e manutenção. Essas tarefas podem ser feitas por uma equipe interna composta por especialistas em tecnologia da informação, equipes de operações e manutenção de hardware e especialistas em domínio que monitoram o comportamento correto da infraestrutura geral de IoT.

As seções a seguir fornecem práticas recomendadas para cada um desses participantes para ajudar a desenvolver, implantar e operar uma infraestrutura de IoT segura.

## <a name="iot-hardware-manufacturerintegrator"></a>Fabricante/integrador de hardware de IoT

A seguir estão as práticas recomendadas para fabricantes de hardware IoT e integradores de hardware.

* **Escopo de hardware para requisitos mínimos**: O design de hardware deve incluir o mínimo de recursos necessários para a operação de hardware e nada mais. Um exemplo é incluir portas USB somente se necessário para a operação do dispositivo. Esses recursos adicionais expõem o dispositivo a vetores de ataque indesejados, o que deve ser evitado.

* **Tornar o hardware à prova de adulteração**: Crie mecanismos para detectar violação física, como a abertura da tampa do dispositivo ou remover uma parte do dispositivo. Esses sinais de violação podem fazer parte do fluxo de dados carregado para a nuvem, o que poderia alertar os operadores sobre esses eventos.

* **Criar em hardware seguro**: Se o COGS permitir, crie os recursos de segurança, como o armazenamento seguro e criptografado ou a funcionalidade de inicialização com base em Trusted Platform Module (TPM). Esses recursos tornam os dispositivos mais seguros e ajudam a proteger a infraestrutura geral de IoT.

* **Tornar as atualizações seguras**: Atualizações de firmware durante o tempo de vida do dispositivo são inevitáveis. Criar dispositivos com caminhos seguros para atualizações e garantia criptográfica das versões de firmware permitirá que o dispositivo fique seguro durante e após atualizações.

## <a name="iot-solution-developer"></a>Desenvolvedor de soluções IoT

A seguir estão as práticas recomendadas para desenvolvedores de solução de IoT:

* **Siga a metodologia de desenvolvimento de software seguro**: Desenvolvimento de software seguro requer início pensando em segurança, desde o início do projeto até sua implementação, teste e implantação. A escolha de plataformas, linguagens e ferramentas são influenciados por essa metodologia. O Microsoft Security Development Lifecycle fornece uma abordagem passo a passo para a criação de software seguro.

* **Escolher software livre com cuidado**: Software livre fornece uma oportunidade para rapidamente desenvolver soluções. Ao escolher o software livre, considere o nível de atividade da comunidade para cada componente de software livre. Uma comunidade ativa garante que existirá suporte para o software e que os problemas serão descobertos e tratados. Como alternativa, um projeto de software livre obscuro e inativo poderá não ter suporte e os problemas provavelmente não serão descobertos.

* **Integrar com cuidado**: Há muitas falhas de segurança de software no limite de bibliotecas e APIs. Funcionalidades que podem não ser necessárias para a implantação atual ainda podem estar disponíveis por meio de uma camada de API. Para garantir a segurança geral, certifique-se de verificar todas as interfaces de componentes sendo integradas para ver se há falhas de segurança.

## <a name="iot-solution-deployer"></a>Implantador de soluções IoT

A seguir estão as práticas recomendadas para implantadores de solução de IoT:

* **Implantar o hardware com segurança**: implantações de IoT podem exigir que o hardware seja implantado em locais não seguros, como espaços públicos ou localidades sem supervisão. Em tais situações, certifique-se de que a implantação de hardware seja à prova de adulteração o máximo possível. Se USB ou outras portas estiverem disponíveis no hardware, certifique-se de que elas estejam cobertas com segurança. Muitos vetores de ataque podem usá-las como ponto de entrada.

* **Proteger as chaves de autenticação**: Durante a implantação, cada dispositivo requer IDs de dispositivo e chaves de autenticação associadas geradas pelo serviço de nuvem. Mantenha essas chaves fisicamente seguras mesmo após a implantação. Qualquer chave comprometida pode ser usada por um dispositivo mal-intencionado passando-se por um dispositivo existente.

## <a name="iot-solution-operator"></a>Operador de solução IoT

A seguir estão as práticas recomendadas para operadores de solução de IoT:

* **Manter o sistema atualizado**: Certifique-se de que os sistemas operacionais de dispositivos e todos os drivers de dispositivo são atualizados para as versões mais recentes. Se você ativar as atualizações automáticas no Windows 10 (IoT ou outras SKUs), a Microsoft vai mantê-las atualizadas, fornecendo um sistema operacional seguro para dispositivos IoT. Manter outros sistemas atualizados (como o Linux) ajuda a garantir que eles também estejam protegidos contra ataques mal-intencionados.

* **Proteger contra atividades mal-intencionadas**: Se o sistema operacional permitir, instale os recursos de antivírus e antimalware mais recentes em cada sistema operacional do dispositivo. Essa prática pode ajudar a atenuar a maioria das ameaças externas. Você pode proteger os sistemas operacionais mais modernos contra ameaças seguindo as etapas apropriadas.

* **Auditar frequentemente**: Infraestrutura de IoT para problemas relacionados à segurança de auditoria é a chave ao responder a incidentes de segurança. A maioria dos sistemas operacionais fornece o log de eventos internos que deve ser analisado com frequência para certificar-se de que não ocorreu nenhuma violação de segurança. Informações de auditoria podem ser enviadas como um fluxo de telemetria separado para o serviço de nuvem, em que serão analisadas.

* **Proteger fisicamente a infraestrutura de IoT**: Os pior ataques de segurança contra infraestrutura de IoT são iniciados usando o acesso físico aos dispositivos. Uma prática de segurança importante é proteger contra o uso mal-intencionado de portas USB e outros acessos físicos. Um ponto importante para descobrir violações que podem ter ocorrido é registrar o acesso físico em log, tal como o uso da porta USB. Novamente, o Windows 10 (IoT e outros SKUs) habilita o registro em log detalhado desses eventos.

* **Proteger credenciais da nuvem**: Credenciais de autenticação de nuvem usadas para configurar e operar uma implantação de IoT são possivelmente a maneira mais fácil de acessar e comprometer um sistema de IoT. Proteja as credenciais alterando a senha com frequência e evitando o uso dessas credenciais em computadores públicos.

As funcionalidades de diferentes dispositivos IoT variam. Alguns dispositivos podem ser computadores executando sistemas operacionais de desktop comuns, e outros dispositivos podem estar executando sistemas operacionais muito leves. As práticas recomendadas de segurança descritas anteriormente podem ser aplicáveis a esses dispositivos em graus variados. Se fornecidas, as práticas recomendadas de segurança e de implantação do fabricante desses dispositivos deverão ser seguidas.

Alguns dispositivos herdados e restritos podem não ter sido projetados especificamente para a implantação de IoT. Esses dispositivos podem não ter a capacidade de criptografar dados, conectar-se à Internet ou fornecer auditoria avançada. Nesses casos, um gateway de campo moderno e seguro pode agregar dados de dispositivos herdados e fornecer a segurança necessária para conectar esses dispositivos pela Internet. Gateways de campo fornecem autenticação segura, negociação de sessões criptografadas, recebimento de comandos da nuvem e muitos outros recursos de segurança.