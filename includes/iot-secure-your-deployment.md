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
ms.openlocfilehash: e5acb8e0f8805da7f14bbce58b4bfd2acdc24f23
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172578"
---
# <a name="secure-your-internet-of-things-iot-deployment"></a>Proteja sua implantação da Internet das Coisas (IoT)

Este artigo fornece detalhes avançados para proteger sua a infraestrutura IoT (Internet das Coisas) baseada em Azure IoT. Ele vincula detalhes de nível de implementação para configurar e implantar cada componente. Além disso, ele fornece comparações e opções entre vários métodos de concorrentes.

A proteção da implantação do Azure IoT pode ser dividida nas seguintes áreas de três segurança:

* **Segurança de dispositivo**: Protegendo o dispositivo IoT enquanto ele é implantado em uso.

* **Segurança de Conexão**: Garantir que todos os dados transmitidos entre o dispositivo IoT e IoT Hub é confidenciais e à prova de adulteração.

* **Segurança na nuvem**: Fornecendo um meio para proteger os dados enquanto se movimentam pelo e é armazenado na nuvem.

![Três áreas de segurança](./media/iot-secure-your-deployment/overview.png)

## <a name="secure-device-provisioning-and-authentication"></a>Provisionamento e autenticação com segurança dos dispositivos

Os aceleradores de soluções de IoT protegem dispositivos de IoT usando os dois métodos a seguir:

* Fornecendo uma chave de identidade exclusiva (tokens de segurança) para cada dispositivo, o que pode ser usado pelo dispositivo para se comunicar com o Hub IoT.

* Usando um [certificado X.509](https://www.itu.int/rec/T-REC-X.509-201210-S) no dispositivo e uma chave privada como meio de autenticar o dispositivo no Hub IoT. Esse método de autenticação garante que a chave privada no dispositivo não seja conhecida for do dispositivo em momento algum, o que fornece um nível mais alto de segurança.

O método de token de segurança fornece autenticação para cada chamada feita pelo dispositivo para Hub IoT ao associar a chave simétrica a cada chamada. A autenticação com base em x.509 permite a autenticação de um dispositivo IoT na camada física como parte do estabelecimento de conexão TLS. O método baseado em token de segurança pode ser usado sem a autenticação X.509, que é um padrão menos seguro. A escolha entre os dois métodos é determinada principalmente pelo nível de segurança que a autenticação do dispositivo precisa ter e a disponibilidade do armazenamento de segurança no dispositivo (para armazenar com segurança a chave privada).

## <a name="iot-hub-security-tokens"></a>Tokens de segurança do Hub IoT

O Hub IoT usa tokens de segurança para autenticar dispositivos e serviços a fim de evitar o envio de chaves na rede. Além disso, os tokens de segurança têm limite de escopo e de prazo de validade. Os SDKs do IoT do Azure geram automaticamente tokens sem precisar de configuração especial. No entanto, alguns cenários exigem que o usuário gere e use tokens de segurança diretamente. Entre esses cenários estão o uso direto de superfícies MQTT, AMQP ou HTTP, ou a implementação do padrão de serviço de token.

Mais detalhes sobre a estrutura do token de segurança e seu uso podem ser encontradas nos seguintes artigos:

* [Estrutura de token de segurança](../articles/iot-hub/iot-hub-devguide-security.md#security-token-structure)

* [Usando tokens SAS como um dispositivo](../articles/iot-hub/iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app)

Cada Hub IoT tem um [Registro de identidade](../articles/iot-hub/iot-hub-devguide-identity-registry.md) que pode ser usado para criar recursos por dispositivo no serviço como uma fila contendo mensagens de nuvem para dispositivo em andamento e para permitir o acesso aos pontos de extremidade voltados para o dispositivo. O registro de identidade do Hub IoT fornece armazenamento seguro de identidades de dispositivo e chaves de segurança para uma solução. As identidades de dispositivos individuais ou em grupo podem ser adicionadas a uma lista de permissões ou a uma lista de contatos bloqueados, permitindo o controle completo sobre o acesso ao dispositivo. Os artigos a seguir fornecem mais detalhes sobre a estrutura do Registro de identidade e operações com suporte.

[O Hub IoT dá suporte a protocolos como HTTP, AMQP e MQTT](../articles//iot-hub/iot-hub-devguide-security.md). Cada um desses protocolos usa tokens de segurança do dispositivo para o Hub IoT de maneiras diferentes:

* AMQP: SASL PLAIN e segurança baseada em declarações AMQP (`{policyName}@sas.root.{iothubName}` com tokens de nível do hub IoT; `{deviceId}` com tokens no escopo do dispositivo).

* MQTT: Usa o pacote de conexão `{deviceId}` como o `{ClientId}`, `{IoThubhostname}/{deviceId}` no **Username** campo e uma SAS de token no **senha** campo.

* HTTP: É o token válido no cabeçalho de solicitação de autorização.

O Registro de identidade do Hub IoT pode ser usado para configurar credenciais de segurança e controle de acesso por dispositivo. No entanto, se uma solução de IoT já tiver um investimento considerável em um [Registro de identidade de dispositivo personalizado e/ou em um esquema de autenticação](../articles/iot-hub/iot-hub-devguide-security.md#custom-device-and-module-authentication), ela poderá ser integrada a uma infraestrutura existente com o Hub IoT por meio da criação de um serviço de token.

### <a name="x509-certificate-based-device-authentication"></a>Autenticação de dispositivo com base no certificado x.509

O uso de um [certificado X.509 baseado em dispositivo](../articles/iot-hub/iot-hub-devguide-security.md) e seu par de chaves públicas e privadas associadas permite autenticação adicional na camada física. A chave privada é armazenada com segurança no dispositivo e não é localizável fora do dispositivo. O certificado x.509 contém informações sobre o dispositivo, como ID do dispositivo e outros detalhes organizacionais. Uma assinatura do certificado é gerada usando a chave privada.

Fluxo de provisionamento do dispositivo de alto nível:

* Associe um identificador a um dispositivo físico – identidade do dispositivo e/ou certificado x.509 associado ao dispositivo durante fabricação ou preparação de dispositivo.

* Crie uma entrada de identidade correspondente no Hub IoT – informações de dispositivo associado e identidade do dispositivo no Registro de identidade do Hub IoT.

* Armazene com segurança a impressão digital do certificado x.509 no Registro de identidade do Hub IoT.

### <a name="root-certificate-on-device"></a>Certificado raiz no dispositivo

Ao estabelecer uma conexão TLS segura com o IoT Hub, o dispositivo IoT autentica o Hub IoT usando um certificado raiz que é parte do SDK do dispositivo. Para o SDK do cliente C, o certificado está localizado na pasta "\\c\\certs" sob a raiz do repositório. Embora esses certificados raiz são de vida longa, eles ainda podem expirar ou ser revogados. Se não houver nenhuma maneira de atualizar o certificado no dispositivo, o dispositivo poderá não se conectar subsequentemente ao Hub IoT (ou a qualquer outro serviço de nuvem). Ter um meio para atualizar o certificado raiz quando o dispositivo IoT é implantado reduz efetivamente o risco.

## <a name="securing-the-connection"></a>Protegendo a conexão

A conexão com a Internet entre o dispositivo IoT e o Hub IoT é protegida usando o padrão de TLS (protocolo TLS). O Azure IoT dá suporte a [TLS 1.2](https://tools.ietf.org/html/rfc5246), TLS 1.1 e TLS 1.0, nessa ordem. O suporte para TLS 1.0 é fornecido somente para fins de compatibilidade com versões anteriores. Se possível, use o TLS 1.2 já que ele fornece segurança máxima.

## <a name="securing-the-cloud"></a>Proteger a nuvem

O Hub IoT do Azure permite a definição de [políticas de controle de acesso](../articles/iot-hub/iot-hub-devguide-security.md) para cada chave de segurança. Ele usa o conjunto de permissões a seguir para conceder acesso aos pontos de extremidades de cada Hub IoT. As permissões limitam o acesso a um Hub IoT com base na funcionalidade.

* **RegistryRead**. Concede acesso de leitura ao Registro de identidade. Para saber mais, consulte [Registro de identidade](../articles/iot-hub/iot-hub-devguide-identity-registry.md).

* **RegistryReadWrite**. Concede acesso de leitura e gravação ao Registro de identidade. Para saber mais, consulte [Registro de identidade](../articles/iot-hub/iot-hub-devguide-identity-registry.md).

* **ServiceConnect**. Concede acesso aos pontos de extremidade de comunicação e de monitoramento voltados para o serviço de nuvem. Por exemplo, ela concede permissão para que os serviços de nuvem de back-end recebam mensagens do dispositivo para a nuvem, enviem mensagens da nuvem para o dispositivo e obtenham as confirmações de entrega correspondentes.

* **DeviceConnect**. Concede acesso aos pontos de extremidade de comunicação voltados para o dispositivo. Por exemplo, ela concede permissão de envio de mensagens do dispositivo para a nuvem e de recebimento de mensagens da nuvem para o dispositivo. Essa permissão é usada por dispositivos.

Há duas maneiras de obter as permissões **DeviceConnect** com o Hub IoT com [tokens de segurança](../articles/iot-hub/iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app): usando uma chave de identidade do dispositivo ou uma tecla de acesso compartilhada. Além disso, é importante observar que qualquer funcionalidade acessível por meio de dispositivos fica exposta por padrão em pontos de extremidade com o prefixo `/devices/{deviceId}`.

[Os componentes de serviço somente podem gerar tokens de segurança](../articles/iot-hub/iot-hub-devguide-security.md#use-security-tokens-from-service-components) usando políticas de acesso compartilhado que concedem as permissões apropriadas.

O Hub IoT do Azure e outros serviços que podem ser parte da solução permitem o gerenciamento de usuários usando o Azure Active Directory.

Dados ingeridos pelo Hub IoT do Azure podem ser consumidos por diversos serviços, como Stream Analytics do Azure e armazenamento de blobs do Azure. Esses serviços permitem o acesso de gerenciamento. Leia mais sobre esses serviços e opções disponíveis:

* [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/): Um serviço de banco de dados escalonável e totalmente indexado para dados semiestruturados que gerencia os metadados para os dispositivos que você provisiona, como atributos, configuração e propriedades de segurança. O Azure Cosmos DB oferece processamento de alto desempenho e alta produtividade, indexação de dados independente de esquema e uma interface de consulta SQL avançada.

* [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/): Processamento na nuvem que permite que você desenvolva e implante com rapidez uma solução de análise de baixo custo para descobrir insights em tempo real de dispositivos, sensores, infraestrutura e aplicativos de fluxo em tempo real. Os dados desse serviço totalmente gerenciado podem ser dimensionados para qualquer volume enquanto ainda atingem alta taxa de transferência, baixa latência e resiliência.

* [Serviços de aplicativo do Azure](https://azure.microsoft.com/services/app-service/): Uma plataforma de nuvem para criar poderosos aplicativos web e móveis que se conectam aos dados em qualquer lugar; na nuvem ou local. Compile aplicativos móveis atraentes para iOS, Android e Windows. Integre-se com seu SaaS (software como serviço) e com aplicativos empresariais com conectividade integrada para dezenas de serviços baseados em nuvem e aplicativos empresariais. Codifique na sua linguagem e IDE favoritos (.NET, Node.js, PHP, Python ou Java) para compilar aplicativos Web e APIs com mais rapidez do que nunca.

* [Aplicativos lógicos](https://azure.microsoft.com/services/app-service/logic/): O recurso aplicativos lógicos do serviço de aplicativo do Azure ajuda a integrar sua solução de IoT para seus sistemas de linha de negócios existentes e automatizar processos de fluxo de trabalho. Os Aplicativos Lógicos permitem que os desenvolvedores projetem fluxos de trabalho iniciados de um gatilho e, em seguida, executem uma série de etapas — regras e ações que usam conectores poderosos para integrar seus processos de negócios. Os Aplicativos Lógicos oferecem conectividade pronta para uso para um vasto ecossistema de aplicativos de SaaS, baseados em nuvem e locais.

* [O armazenamento de BLOBs do Azure](https://azure.microsoft.com/services/storage/): Armazenamento em nuvem confiável e econômico para os dados que os dispositivos enviam para a nuvem.

## <a name="conclusion"></a>Conclusão

Este artigo fornece uma visão geral dos detalhes de nível de implantação para projetar e implantar uma infraestrutura de IoT usando o Azure IoT. Configurar cada componente para ser seguro é a chave na proteção da infraestrutura geral de IoT. As opções de design disponíveis no Azure IoT oferecem algum nível de flexibilidade e opções; no entanto, cada opção pode ter implicações de segurança. É recomendável que cada uma dessas opções seja avaliada por meio de uma avaliação de risco e custo.
