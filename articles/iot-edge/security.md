---
title: Estrutura de segurança - Azure IoT Edge | Microsoft Docs
description: Saiba mais sobre a segurança, autenticação e padrões de autorização que foram usados para desenvolver o Azure IoT Edge e devem ser considerados ao projetar sua solução
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/30/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 3212493963805de3c8845ec494d87fc92d72998a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "76760106"
---
# <a name="security-standards-for-azure-iot-edge"></a>Padrões de segurança do Azure IoT Edge

Azure IoT Edge resolve os riscos inerentes ao mover seus dados e análises para a borda inteligente. O IoT Edge os padrões de segurança balanceem a flexibilidade para diferentes cenários de implantação com a proteção que você espera de todos os serviços do Azure.

O IoT Edge é executado em várias marcas e modelos de hardware, dá suporte a vários sistemas operacionais e se aplica a diferentes cenários de implantação. Em vez de oferecer soluções concretas para cenários específicos, a IoT Edge é uma estrutura de segurança extensível baseada em princípios bem orientados que são projetados para dimensionamento. O risco de um cenário de implantação depende de muitos fatores, incluindo:

* Propriedade da solução
* Geografia da implantação
* Sensibilidade de dados
* Privacidade
* Vertical do aplicativo
* Requisitos regulatórios

Este artigo apresenta uma visão geral da estrutura de segurança do IoT Edge. Para obter mais informações, consulte [Protegendo borda inteligente](https://azure.microsoft.com/blog/securing-the-intelligent-edge/).

## <a name="standards"></a>Padrões

Padrões de promovem a facilidade de investigação e facilidade de implementação, ambas fundamentais para a segurança. Uma solução de segurança deve ser útil para investigação sob avaliação para criar confiança e não deve ser um obstáculo à implantação. O design da estrutura para proteger o Azure IoT Edge é baseado em protocolo de segurança comprovados do setor e testados pelo tempo para familiaridade e reutilização.

## <a name="authentication"></a>Autenticação

Ao implantar uma solução de IoT, você precisa saber que somente os atores, dispositivos e módulos confiáveis têm acesso à sua solução. A autenticação baseada em certificado é o mecanismo principal de autenticação para a plataforma de Azure IoT Edge. Esse mecanismo é derivado de um conjunto de padrões que governam a PKiX (infraestrutura de chave pública) pela IETF (Internet Engineering Task Force).

Todos os dispositivos, módulos e atores que interagem com o dispositivo Azure IoT Edge devem ter identidades de certificado exclusivas. Essa orientação se aplica se as interações são físicas ou por meio de uma conexão de rede. Nem todo cenário ou componente pode se dar à autenticação baseada em certificado, portanto, a extensibilidade da estrutura de segurança oferece alternativas seguras.

Para obter mais informações, consulte [Azure IOT Edge uso de certificado](iot-edge-certs.md).

## <a name="authorization"></a>Autorização

O princípio de privilégios mínimos diz que os usuários e os componentes de um sistema devem ter acesso somente ao conjunto mínimo de recursos e dados necessários para desempenharem suas funções. Dispositivos, módulos e atores devem obter acesso apenas a recursos e dados no seu escopo de permissão e somente quando permitido em termos de arquitetura. Algumas permissões são configuráveis com privilégios suficientes e outras são impostas de arquitetura. Por exemplo, alguns módulos podem estar autorizados a se conectar ao Hub IoT do Azure. No entanto, não há nenhum motivo pelo qual um módulo em um dispositivo IoT Edge deve acessar a n º de um módulo em outro dispositivo IoT Edge.

Outros esquemas de autorização incluem direitos de assinatura de certificado e RBAC (controle de acesso baseado em função).

## <a name="attestation"></a>Atestado

O atestado garante a integridade dos bits de software, o que é importante para detectar e impedir malware. A estrutura de segurança do Azure IoT Edge classifica Atestado em três categorias principais:

* Atestado estático
* Atestado do runtime
* Atestado do software

### <a name="static-attestation"></a>Atestado estático

O atestado estático verifica a integridade de todos os softwares em um dispositivo durante a ativação, incluindo o sistema operacional, todos os tempos de execução e informações de configuração. Como o atestado estático ocorre durante a ativação, ele é muitas vezes chamado de inicialização segura. A estrutura de segurança para dispositivos IoT Edge se estende aos fabricantes e incorpora recursos de hardware seguros que garantem processos de atestado estáticos. Esses processos incluem inicialização segura e atualização de firmware segura. Trabalhar em forte colaboração com fornecedores de silício elimina as camadas de firmware supérfluas, minimizando a superfície de ameaça.

### <a name="runtime-attestation"></a>Atestado do runtime

Depois que um sistema conclui um processo de inicialização segura, sistemas bem projetados devem detectar tentativas de injetar malware e tomar medidas adequadas. Os ataques de malware podem direcionar as portas e interfaces do sistema. Se os atores mal-intencionados tiverem acesso físico a um dispositivo, eles poderão adulterar o próprio dispositivo ou usar ataques de canal lateral para obter acesso. Tais malcontent, se alterações de configuração de malware ou não autorizadas, não podem ser detectadas pelo atestado estático porque ele é injetado após o processo de inicialização. As contramedidas oferecidas ou impostas pela ajuda de hardware do dispositivo para repelir essas ameaças. A estrutura de segurança para IoT Edge chama explicitamente para extensões que combatem ameaças de tempo de execução.  

### <a name="software-attestation"></a>Atestado do software

Todos os sistemas íntegros, incluindo sistemas inteligentes de borda, precisam de patches e atualizações. A segurança é importante para processos de atualização, caso contrário, eles podem ser vetores de ameaça potenciais. A estrutura de segurança para chamadas de IoT Edge para atualizações por meio de pacotes medidos e assinados para garantir a integridade e autenticar a origem dos pacotes. Esse padrão se aplica a todos os sistemas operacionais e bits de software de aplicativo.

## <a name="hardware-root-of-trust"></a>Raiz de hardware de confiança

Para muitos dispositivos inteligentes de borda, especialmente os dispositivos que podem ser acessados fisicamente por possíveis atores mal-intencionados, a segurança de hardware é a última defesa para proteção. Hardware resistente a adulterações é crucial para essas implantações. O Azure IoT Edge incentiva os fornecedores de hardware de silício seguro a oferecerem diferentes tipos de raiz de confiança de hardware para acomodar vários perfis de risco e cenários de implantação. A confiança do hardware pode vir de padrões de protocolo de segurança comuns como DICE (Mecanismo de Composição de Identificador de Dispositivo) do Trusted Computing Group e Trusted Platform Module (ISO/IEC 11889). Tecnologias de enclave seguras, como TrustZones e SGX (extensões de proteção de software), também fornecem confiança de hardware.

## <a name="certification"></a>Certificação

Para ajudar os clientes a tomar decisões informadas quando adquirir Azure IoT Edge dispositivos para sua implantação, a estrutura de IoT Edge inclui requisitos de certificação. Base para esses requisitos são certificações pertencentes às declarações de segurança e certificações pertencente a validação da implementação de segurança. Por exemplo, uma certificação de declaração de segurança significa que o dispositivo de IoT Edge usa um hardware seguro conhecido por resistir a ataques de inicialização. Uma certificação de validação significa que o hardware seguro foi implementado corretamente para oferecer esse valor no dispositivo. Para manter o princípio de simplicidade, a estrutura tenta manter a carga de certificação mínima.

## <a name="extensibility"></a>Extensibilidade

Com a tecnologia de IoT impulsionando diferentes tipos de transformações de negócios, a segurança deve evoluir em paralelo para abordar os cenários emergentes. A estrutura de segurança do Azure IoT Edge começa com uma base sólida na qual ele cria no extensibilidade em diferentes dimensões a serem incluídas:

* Serviços de segurança de primeira como o serviço de provisionamento do dispositivo para o Azure Hub IoT.
* Serviços de terceiros, como serviços de segurança gerenciados para diferentes verticais de aplicativo (como industrial ou de saúde) ou foco de tecnologia (como monitoramento de segurança em redes de malha ou serviços de atestado de hardware de silício) por meio de uma rede avançada de parceiros.
* Sistemas herdados para incluir o aperfeiçoamento com estratégias de segurança alternativas, como usando a tecnologia segura diferente de certificados para autenticação e gerenciamento de identidades.
* Proteger o hardware para a adoção de novas tecnologias de hardware seguro e contribuições de parceiro de silício.

No final, a proteção da borda inteligente exige contribuições colaborativas de uma comunidade aberta orientada pelo interesse comum na proteção da IoT. Essas contribuições podem ser na forma de tecnologias seguras ou serviços. A estrutura de segurança Azure IoT Edge oferece uma base sólida para a segurança é extensível para a cobertura de máximo oferecer o mesmo nível de confiança e integridade na borda inteligente como com a nuvem do Azure.  

## <a name="next-steps"></a>Próximas etapas

Leia mais sobre como o Azure IoT Edge é [protegendo a borda inteligente](https://azure.microsoft.com/blog/securing-the-intelligent-edge/).
