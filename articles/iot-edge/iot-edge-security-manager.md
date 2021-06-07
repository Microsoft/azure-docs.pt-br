---
title: Azure IoT Edge Security Manager-Azure IoT Edge
description: Gerencia a postura de segurança do dispositivo IoT Edge e a integridade dos serviços de segurança.
services: iot-edge
keywords: segurança, o elemento seguro, o enclave, TEE, IoT Edge
author: eustacea
manager: philmea
ms.author: eustacea
ms.date: 08/30/2019
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: 468f1b91d6c6157cd2af6de9599bad7f43c1ad8f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103492550"
---
# <a name="azure-iot-edge-security-manager"></a>Gerenciador de segurança do Azure IoT Edge

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

O gerenciador de segurança do Azure IoT Edge é um núcleo de segurança bem delimitado para proteger o dispositivo IoT Edge e todos os seus componentes, abstraindo o hardware de silício seguro. O Gerenciador de segurança é o ponto focal para a proteção de segurança e fornece ponto de integração de tecnologia para OEMs (fabricantes originais de equipamento).

![Gerenciador de segurança do Azure IoT Edge](media/edge-security-manager/iot-edge-security-manager.png)

O gerenciador de segurança IoT Edge visa defender a integridade do dispositivo IoT Edge e todas as operações de software inerentes. O Gerenciador de segurança faz a transição de confiança da raiz de hardware subjacente de hardware de confiança (se disponível) para inicializar o tempo de execução do IoT Edge e monitorar operações em andamento.  O gerenciador de segurança IoT Edge inclui software que trabalha em conjunto com hardware de silício seguro, quando disponível, para ajudar a fornecer as mais altas garantias de segurança possíveis.  

As responsabilidades do gerenciador de segurança do IoT Edge incluem (mas sem limitação):

* Inicialização segura e medida do dispositivo Azure IoT Edge.
* Fornecimento de identidade de dispositivo e transição de confiança, quando aplicável.
* Hospede e proteja componentes de dispositivos de serviços em nuvem, como o Serviço de provisionamento de dispositivos.
* Forneça com segurança identidades exclusivas aos módulos do IoT Edge.
* Gatekeeper para dispositivo hardware raiz de confiança através de serviços notariais.
* Monitora a integridade das operações do IoT Edge no runtime.

O gerenciador de segurança IoT Edge inclui três componentes:

* Daemon de segurança do IoT Edge.
* Camada de abstração da plataforma do módulo de segurança de hardware (HSM PAL).
* Raiz de silício de hardware opcional ou altamente recomendada de hardware ou HSM.

## <a name="the-iot-edge-security-daemon"></a>O daemon de segurança do IoT Edge

O daemon de segurança IoT Edge é responsável pelas operações lógicas do IoT Edge Security Manager. Representa uma parte significativa da base computacional confiável do dispositivo IoT Edge.

### <a name="design-principles"></a>Princípios de design

O daemon de segurança do IoT Edge segue dois princípios básicos: maximizar a integridade operacional e minimizar a sobrecarga e variações.

#### <a name="maximize-operational-integrity"></a>Maximizar a integridade operacional

O daemon de segurança IoT Edge opera com a integridade mais alta possível dentro do recurso de defesa de qualquer raiz de hardware de confiança. Com a integração adequada, a raiz do hardware de confiança mede e monitora o daemon de segurança estaticamente e em runtime para resistir à violação.

O acesso físico é sempre uma ameaça aos dispositivos IoT. A raiz de hardware de confiança desempenha um papel importante na defesa da integridade do daemon de segurança do IoT Edge.  A raiz de hardware de confiança é proveniente de duas variedades:

* elementos seguros para a proteção de informações confidenciais, como segredos e chaves criptográficas.
* enclaves seguros para a proteção de segredos, como chaves, e cargas de trabalho confidenciais, como medição e faturamento.

Existem dois tipos de ambientes de execução para usar a raiz de hardware de confiança:

* O REE (ambiente de execução avançada) ou padrão que depende do uso de elementos seguros para proteger informações confidenciais.
* O sistema de execução confiável (enclave) que depende do uso da tecnologia de segurança segura para proteger informações confidenciais e oferecer proteção à execução de software.

Para dispositivos que usam o Secure enclaves como raiz de hardware de confiança, a lógica confidencial dentro do IoT Edge daemon de segurança deve estar dentro do enclave.  Partes não confidenciais do daemon de segurança podem estar fora do alto-se.  De qualquer forma, os fabricantes de design original (ODM) e os fabricantes de equipamento original (OEM) devem estender a confiança do seu HSM para medir e defender a integridade do daemon de segurança do IoT Edge na inicialização e no tempo de execução.

#### <a name="minimize-bloat-and-churn"></a>Minimizar a sobrecarga e a variação

Outro princípio fundamental para o daemon de segurança IoT Edge é minimizar a rotatividade.  Para obter o mais alto nível de confiança, o daemon de segurança IoT Edge pode se associar à raiz de hardware do dispositivo e operar como código nativo.  É comum que esses tipos de realização atualizem o software daemon por meio da raiz de hardware dos caminhos de atualização segura da confiança (em oposição aos mecanismos de atualização fornecidos pelo sistema operacional), o que pode ser desafiador em alguns cenários.  Embora a renovação de segurança seja recomendada para dispositivos IoT, requisitos de atualização excessivos ou grandes cargas de atualização podem expandir a superfície de ameaças de várias maneiras.  Os exemplos incluem a omissão de atualizações para maximizar a disponibilidade operacional ou a raiz do hardware de confiança, muito restrita para processar grandes cargas úteis de atualização.  Como tal, o design de IoT Edge daemon de segurança é conciso para manter a superfície e a base de computação confiável pequena e minimizar os requisitos de atualização.

### <a name="architecture-of-iot-edge-security-daemon"></a>Arquitetura do daemon de segurança do IoT Edge

![Daemon de segurança do Azure IoT Edge](media/edge-security-manager/iot-edge-security-daemon.png)

O daemon de segurança IoT Edge aproveita qualquer raiz de hardware disponível da tecnologia de confiança para proteger a segurança.  Ele também permite a operação de divisão do mundo entre um REE (ambiente de execução padrão/avançado) e um ambiente de execução confiável (configuração) quando as tecnologias de hardware oferecem ambientes de execução confiáveis. As interfaces específicas de função habilitam os principais componentes do IoT Edge para garantir a integridade do dispositivo IoT Edge e de suas operações.

#### <a name="cloud-interface"></a>Interface de nuvem

A interface de nuvem permite que o daemon de segurança do IoT Edge acesse serviços de nuvem, como Complementos de nuvem para segurança de dispositivo, como renovação de segurança.  Por exemplo, o daemon de segurança IoT Edge usa atualmente essa interface para acessar o serviço de [provisionamento de dispositivos](../iot-dps/index.yml) no Hub IOT do Azure para o gerenciamento do ciclo de vida de identidade do dispositivo.  

#### <a name="management-api"></a>API de gerenciamento

IoT Edge daemon de segurança oferece uma API de gerenciamento, que é chamada pelo agente de IoT Edge ao criar/iniciar/interromper/remover um módulo IoT Edge. O daemon de segurança armazena "registros" para todos os módulos ativos. Esses registros mapeiam a identidade de um módulo para algumas propriedades do módulo. Para obter exemplos, essas propriedades de módulo incluem o identificador de processo (PID) do processo em execução no contêiner e o hash do conteúdo do contêiner do Docker.

Essas propriedades são usadas pela API de carga de trabalho (descrita abaixo) para verificar se o chamador está autorizado para uma ação.

A API de gerenciamento é uma API privilegiada, que é possível chamá-la somente do agente de IoT Edge.  Como as inicializações do daemon de segurança IoT Edge e inicia o agente de IoT Edge, ele verifica se o agente de IoT Edge não foi violado, então ele pode criar um registro implícito para o agente de IoT Edge. O mesmo processo de atestado usado pela API de carga de trabalho também restringe o acesso à API de gerenciamento apenas para o agente de IoT Edge.

#### <a name="container-api"></a>Contêiner de API

A API do contêiner interage com o sistema de contêiner em uso para o gerenciamento de módulo, como Moby ou Docker.

#### <a name="workload-api"></a>API de carga de trabalho

A API de carga de trabalho é acessível a todos os módulos. Ele fornece uma prova de identidade, seja como um token assinado por raiz do HSM ou um certificado X509, e o grupo de confiança correspondente a um módulo. O pacote confiável contém certificados de Autoridade de Certificação para todos os outros servidores nos quais os módulos devem confiar.

O daemon de segurança IoT Edge usa um processo de atestado para proteger essa API. Quando um módulo chama essa API, o daemon de segurança tenta encontrar um registro para a identidade. Se bem sucedido, usa as propriedades do registro para medir o módulo. Se o resultado do processo de medição corresponder ao registro, uma nova prova de identidade será gerada. Os certificados de Autoridade de Certificação correspondentes (pacote confiável) serão retornados ao módulo.  O módulo usa esse certificado para se conectar ao Hub IoT, outros módulos ou iniciar um servidor. Quando o token assinado ou o certificado se aproximar da expiração, é responsabilidade do módulo solicitar um novo certificado.

### <a name="integration-and-maintenance"></a>Integração e manutenção

A Microsoft mantém a principal base de código para o daemon de segurança [IoT Edge no GitHub](https://github.com/Azure/iotedge/tree/master/edgelet).

#### <a name="installation-and-updates"></a>Instalação e as atualizações

A instalação e as atualizações do daemon de segurança IoT Edge são gerenciadas por meio do sistema de gerenciamento de pacotes do sistema operacional. Os dispositivos IoT Edge com raiz de hardware de confiança devem fornecer proteção adicional à integridade do daemon, gerenciando seu ciclo de vida por meio dos sistemas de gerenciamento seguro de inicialização e de atualizações. Os criadores de dispositivos devem explorar esses caminhos com base em seus respectivos recursos de dispositivo.

#### <a name="versioning"></a>Controle de versão

O runtime do IoT Edge rastreia e relata a versão do daemon de segurança IoT Edge. A versão é relatada como o atributo *runtime.platform.version* da propriedade relatada do módulo do agente IoT Edge.

### <a name="hardware-security-module-platform-abstraction-layer-hsm-pal"></a>Camada de abstração de plataforma hardware security module (HSM PAL)

O HSM PAL abstrai toda a raiz do hardware de confiança para isolar o desenvolvedor ou usuário do IoT Edge de suas complexidades.  Ele inclui uma combinação de procedimentos de comunicação da API (interface de programação de aplicativo) e de transporte de domínio, por exemplo, a comunicação entre um ambiente de execução padrão e um enclave seguro.  A implementação real do HSM PAL depende do hardware seguro específico em uso. Sua existência permite o uso de praticamente qualquer hardware de silício seguro.

## <a name="secure-silicon-root-of-trust-hardware"></a>Raiz de silício seguro do hardware de confiança

O silício seguro é necessário para ancorar a confiança dentro do hardware do dispositivo IoT Edge.  O silício seguro é variado para incluir o Trusted Platform Module (TPM), o Secure Element (eSE) incorporado, o ARM TrustZone, o Intel SGX e as tecnologias de silício seguras personalizadas.  O uso da raiz segura do silício de confiança em dispositivos é recomendado devido às ameaças associadas à acessibilidade física de dispositivos IoT.

## <a name="iot-edge-security-manager-integration-and-maintenance"></a>Integração e manutenção do gerenciador de segurança IoT Edge

O objetivo do gerenciador de segurança do IoT Edge é identificar e isolar os componentes que defendem a segurança e a integridade da plataforma Azure IoT Edge para proteção personalizada. Terceiros, como fabricantes de dispositivos, devem fazer uso de recursos de segurança personalizados disponíveis com o hardware do dispositivo.  Confira a próxima seção de etapas para links que demonstram como proteger o gerenciador de segurança da IoT do Azure com o TPM (Trusted Platform Module) nas plataformas Linux e Windows. Esses exemplos usam software ou TPMs virtuais, mas se aplicam diretamente ao uso de dispositivos TPM discretos.  

## <a name="next-steps"></a>Próximas etapas

Leia o blog em [ Protegendo a borda inteligente](https://azure.microsoft.com/blog/securing-the-intelligent-edge/).

Crie e provisione um [dispositivo do IoT Edge com uma TPM virtual em uma máquina virtual do Linux](how-to-auto-provision-simulated-device-linux.md).

Crie e provisione um [dispositivo IoT Edge com um TPM simulado no Windows](how-to-auto-provision-simulated-device-windows.md).