---
title: Certificados de segurança de dispositivo – Azure IoT Edge | Microsoft Docs
description: O Azure IoT Edge usa um certificado para validar dispositivos, módulos e dispositivos folha e estabelecer conexões seguras entre eles.
author: stevebus
manager: philmea
ms.author: stevebus
ms.date: 08/12/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: mqtt
ms.openlocfilehash: ffe2f2b7f94d546cdfe393170da2fd2ca6ac0149
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103490986"
---
# <a name="understand-how-azure-iot-edge-uses-certificates"></a>Entender como Azure IoT Edge usa certificados

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

IoT Edge certificados são usados pelos módulos e dispositivos IoT downstream para verificar a identidade e a legitimidade do módulo [IOT Edge](iot-edge-runtime.md#iot-edge-hub) tempo de execução do Hub. Essas verificações permitem uma conexão segura TLS (segurança da camada de transporte) entre o runtime, os módulos e os dispositivos IoT. Como o Hub IoT, o IoT Edge requer uma conexão segura e criptografada a partir de dispositivos IoT downstream (ou folha) e módulos IoT Edge. Para estabelecer uma conexão TLS segura, o módulo do hub do IoT Edge apresenta uma cadeia de certificados de servidor para conectar clientes para que eles verifiquem sua identidade.

>[!NOTE]
>Este artigo fala sobre os certificados que são usados para proteger conexões entre os diferentes componentes em um dispositivo IoT Edge ou entre um dispositivo IoT Edge e dispositivos de folha. Você também pode usar certificados para autenticar seu dispositivo de IoT Edge no Hub IoT. Esses certificados de autenticação são diferentes e não são discutidos neste artigo. Para obter mais informações sobre como autenticar seu dispositivo com certificados, consulte [criar e provisionar um dispositivo de IOT Edge usando certificados X. 509](how-to-auto-provision-x509-certs.md).

Este artigo explica como os certificados de IoT Edge podem funcionar em cenários de produção, desenvolvimento e teste. Embora os scripts sejam diferentes (PowerShell versus bash), os conceitos são os mesmos entre Linux e Windows.

## <a name="iot-edge-certificates"></a>Certificados do IoT Edge

Há dois cenários comuns para configurar certificados em um dispositivo IoT Edge. Às vezes, o usuário final, ou operador, de um dispositivo compra um dispositivo genérico feito por um fabricante e, em seguida, gerencia os próprios certificados. Em outras ocasiões, o fabricante trabalha sob contrato para criar um dispositivo personalizado para o operador e faz alguma assinatura de certificado inicial antes de entregar o dispositivo. O design do certificado IoT Edge tenta levar em conta os dois cenários.

A figura a seguir ilustra o uso de certificados da IoT Edge. Pode haver zero, um ou muitos certificados de assinatura intermediários entre o Certificado de Autoridade de Certificação raiz e o Certificado de Autoridade de Certificação do dispositivo, dependendo do número de entidades envolvidas. Aqui, mostramos um caso.

![Diagrama de relacionamentos típicos de certificados](./media/iot-edge-certs/edgeCerts-general.png)

<!--1.1-->
:::moniker range="iotedge-2018-06"

> [!NOTE]
> Atualmente, uma limitação no libiothsm impede o uso de certificados que expiram em 1º de janeiro de 2038. Essa limitação se aplica ao certificado de autoridade de certificação do dispositivo, a todos os certificados no pacote de confiança e aos certificados de ID de dispositivo usados para os métodos de provisionamento X. 509.

:::moniker-end

### <a name="certificate-authority"></a>Autoridade de certificação

A autoridade de certificação ou 'CA' para abreviar, é uma entidade que emite certificados digitais. Uma autoridade de certificação atua como uma terceira parte confiável entre o proprietário e o destinatário do certificado. Um certificado digital certifica a propriedade de uma chave pública pelo destinatário do certificado. A cadeia de certificados de confiança funciona inicialmente emitindo um certificado raiz, que é a base da confiança em todos os certificados emitidos pela autoridade. Depois disso, o proprietário pode usar o certificado raiz para emitir certificados intermediários adicionais (certificados 'leaf').

### <a name="root-ca-certificate"></a>Certificado de AC raiz

Um certificado CA raiz é a raiz da confiança de todo o processo. Em cenários de produção, esse Certificado de Autoridade de Certificação normalmente é adquirido de uma autoridade de certificação comercial confiável, como Baltimore, Verisign ou DigiCert. Se você tiver controle total sobre os dispositivos que se conectam aos dispositivos IoT Edge, é possível usar uma autoridade de certificação de nível corporativo. Em qualquer evento, toda a cadeia de certificados do hub de IoT Edge é acumulada para ele, de modo que os dispositivos IoT folha devem confiar no certificado raiz. Você pode armazenar o Certificado de Autoridade de Certificação raiz no armazenamento de autoridade de certificação raiz confiável ou fornecer os detalhes do certificado no código de aplicativo.

### <a name="intermediate-certificates"></a>Certificados intermediários

Em um processo de fabricação típico para criar dispositivos seguros, os certificados de CA raiz raramente são usados diretamente, principalmente devido ao risco de vazamento ou exposição. O Certificado de Autoridade de Certificação raiz cria e assina digitalmente um ou mais Certificados de Autoridade de Certificação intermediários. Pode haver apenas um, ou pode haver uma cadeia desses certificados intermediários. Cenários que exigem uma cadeia de certificados intermediários incluem:

* Uma hierarquia de departamentos dentro de um fabricante.

* Várias empresas envolvidas em série na produção de um dispositivo.

* Um cliente comprando uma CA raiz e obtendo um certificado de assinatura para o fabricante assinar os dispositivos que eles fazem em nome desse cliente.

Em qualquer caso, o fabricante usa um certificado CA intermediário no final dessa cadeia para assinar o certificado CA do dispositivo colocado no dispositivo final. Geralmente, esses certificados intermediários são guardados de perto na fábrica. Eles passam por processos rigorosos, tanto físicos quanto eletrônicos, para seu uso.

### <a name="device-ca-certificate"></a>Certificado de autoridade de certificação de dispositivo

O certificado de CA do dispositivo é gerado e assinado pelo certificado de CA intermediário final no processo. Esse certificado é instalado no próprio dispositivo IoT Edge, preferencialmente em armazenamento seguro, como um módulo de segurança de hardware (HSM). Além disso, um certificado CA de dispositivo identifica exclusivamente um dispositivo IoT Edge. O certificado de autoridade de certificação do dispositivo pode assinar outros certificados.

### <a name="iot-edge-workload-ca"></a>Carga de trabalho do IoT Edge da autoridade de certificação

O [IoT Edge Security Manager](iot-edge-security-manager.md) gera o Certificado de Autoridade de Certificação da carga de trabalho, o primeiro no lado do "operador" do processo, quando o IoT Edge é iniciado pela primeira vez. Esse certificado é gerado de e assinado pelo certificado de autoridade de certificação do dispositivo. Esse certificado, que é apenas outro certificado de assinatura intermediário, é usado para gerar e assinar quaisquer outros certificados usados pelo runtime do IoT Edge. Atualmente, trata-se principalmente do certificado do servidor de hub do IoT Edge discutido na seção a seguir, mas, no futuro, pode incluir outros certificados para autenticar os componentes do IoT Edge.

### <a name="iot-edge-hub-server-certificate"></a>Certificado do servidor de hub do IoT Edge

O certificado do servidor de hub do IoT Edge é o certificado real apresentado para dispositivos e módulos de folha para verificação de identidade durante o estabelecimento da conexão TLS exigida pelo IoT Edge. Este certificado apresenta a cadeia completa de certificados de assinatura usados para gerá-lo até o certificado de CA raiz, ao qual o dispositivo de IoT de folha deve confiar. Quando gerado por IoT Edge, o nome comum (CN) desse certificado de IoT Edge Hub é definido como a propriedade ' hostname ' no arquivo de configuração após a conversão em minúsculas. Essa configuração é uma fonte comum de confusão com IoT Edge.

## <a name="production-implications"></a>Implicações de produção

Uma pergunta razoável pode ser "por que a IoT Edge precisa do certificado extra de 'CA de carga de trabalho'? Não foi possível usar o Certificado de Autoridade de Certificação do dispositivo para gerar diretamente o certificado do servidor de hub do IoT Edge?". Tecnicamente, era possível. No entanto, o objetivo deste certificado intermediário de "carga de trabalho" é separar as preocupações entre o fabricante do dispositivo e o operador do dispositivo. Imagine um cenário em que um dispositivo IoT Edge é vendido ou transferido de um cliente para outro. Você provavelmente desejaria que o certificado CA do dispositivo fornecido pelo fabricante fosse imutável. No entanto, os certificados de "carga de trabalho" específicos da operação do dispositivo devem ser limpos e recriados para a nova implantação.

Como os processos de fabricação e operação são separados, considere as seguintes implicações ao preparar os dispositivos de produção:

* Com qualquer processo baseado em certificado, o certificado de CA raiz e todos os certificados de CA intermediários devem ser protegidos e monitorados durante todo o processo de implantação de um dispositivo IoT Edge. O fabricante do dispositivo IoT Edge deve ter processos robustos para armazenamento e uso adequados de seus certificados intermediários. Além disso, o certificado de CA do dispositivo deve ser mantido o mais seguro possível no próprio dispositivo, de preferência um módulo de segurança de hardware.

* O certificado do servidor de hub do IoT Edge é apresentado pelo hub do IoT Edge aos dispositivos e módulos do cliente que está se conectando. O nome comum (CN) do certificado de autoridade de certificação do dispositivo **não deve ser** o mesmo que o "nome do host" que será usado no arquivo de configuração no dispositivo de IOT Edge. O nome usado pelos clientes para se conectar ao IoT Edge (por exemplo, por meio do parâmetro GatewayHostName da cadeia de conexão ou do comando CONNECT em MQTT) **não pode ser** igual ao nome comum usado no certificado de autoridade de certificação do dispositivo. Essa restrição acontece porque o hub do IoT Edge apresenta toda a sua cadeia de certificados para verificação pelos clientes. Se o certificado do servidor de hub do IoT Edge e o Certificado de Autoridade de Certificação do dispositivo tiverem o mesmo CN, você entrará em um loop de verificação e o certificado será invalidado.

* Como o Certificado de Autoridade de Certificação do dispositivo é usado pelo daemon de IoT Edge Security para gerar os certificados finais da IoT Edge, ele deve ser um certificado de assinatura, o que significa que ele tem recursos de assinatura de certificado. A aplicação de "V3 Basic constraints CA:True" ao Certificado de Autoridade de Certificação do dispositivo configura automaticamente as propriedades de uso de chave necessárias.

>[!Tip]
> Se você já passou pela instalação do IoT Edge como um gateway transparente em um cenário de desenvolvimento/teste usando nossos "scripts de conveniência" (consulte a próxima seção) e usou o mesmo nome de host ao criar o certificado de autoridade de certificação do dispositivo como fez para o nome do host no arquivo de configuração, você deve estar se perguntando por que ele funcionou. Em um esforço para simplificar a experiência do desenvolvedor, os scripts de conveniência acrescentam um ".ca" ao final do nome que você passa para o script. Portanto, por exemplo, se você usou "mygateway" para o nome do dispositivo nos scripts e o nome do host no arquivo de configuração, o primeiro será transformado em mygateway.ca antes de ser usado como o CN para o certificado de autoridade de certificação do dispositivo.

## <a name="devtest-implications"></a>Implicações de desenvolvimento/teste

Para facilitar o desenvolvimento e os cenários de teste, a Microsoft fornece um conjunto de [scripts de conveniência](https://github.com/Azure/iotedge/tree/master/tools/CACertificates) para gerar certificados de não produção adequados para o IoT Edge no cenário de gateway transparente. Para obter exemplos de como os scripts funcionam, consulte [criar certificados de demonstração para testar IOT Edge recursos do dispositivo](how-to-create-test-certificates.md).

>[!Tip]
> Para conectar seus dispositivos e aplicativos "folha" de IoT do dispositivo que usam nosso SDK de dispositivo de IoT por meio do IoT Edge, você deve adicionar o parâmetro GatewayHostName opcional ao final da string de conexão do dispositivo. Quando o certificado do servidor do Hub do Edge é gerado, ele se baseia em uma versão em letras minúsculas do nome do host do arquivo de configuração, portanto, para que os nomes correspondam e a verificação do certificado TLS seja bem sucedido, você deve inserir o parâmetro GatewayHostName em letras minúsculas.

## <a name="example-of-iot-edge-certificate-hierarchy"></a>Exemplo de hierarquia de certificados do IoT Edge

Para ilustrar um exemplo desse caminho de certificado, a captura de tela a seguir é de um dispositivo IoT Edge de trabalho configurado como um gateway transparente. O OpenSSL é usado para conectar ao hub do IoT Edge, validar e despejar os certificados.

![Captura de tela da hierarquia de certificados em cada nível](./media/iot-edge-certs/iotedge-cert-chain.png)

Você pode ver a hierarquia da profundidade do certificado representada na captura de tela:

| Certificado de AC raiz         | Teste de Cert de autoridade de certificação do Hub IoT do Azure apenas                                                                           |
|-----------------------------|-----------------------------------------------------------------------------------------------------------|
| Certificado de autoridade de certificação intermediária | Somente o teste do certificado intermediário Hub IoT do Azure                                                                 |
| Certificado de autoridade de certificação de dispositivo       | iotgateway.ca ("iotgateway" foi passado como o <nome do host do gateway> para os scripts de conveniência)   |
| Certificado de CA da carga de trabalho     | carga de trabalho iotedge ca                                                                                       |
| Certificado do servidor de hub do IoT Edge | iotedgegw. local (corresponde ao ' hostname ' do arquivo de configuração)                                            |

## <a name="next-steps"></a>Próximas etapas

[Entenda os módulos do Azure IoT Edge](iot-edge-modules.md)

[Configure um dispositivo IoT Edge para atuar como um gateway transparente](how-to-create-transparent-gateway.md)
