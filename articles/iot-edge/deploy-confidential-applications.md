---
title: Aplicativos confidenciais como módulos Azure IoT Edge
description: Use o SDK e a API do Open enclave para gravar aplicativos confidenciais e implantá-los como IoT Edge módulos para computação confidencial
author: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 01/27/2021
ms.author: kgremban
ms.openlocfilehash: e63397176cae87178cfc5cc343aeed6f76099be2
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98956450"
---
# <a name="confidential-computing-at-the-edge"></a>Computação confidencial na borda

O Azure IoT Edge dá suporte a aplicativos confidenciais que são executados no enclaves seguro no dispositivo. A criptografia fornece segurança para dados em trânsito ou em repouso, mas enclaves fornece segurança para dados e cargas de trabalho em uso. O IoT Edge dá suporte ao Open enclave como um padrão para o desenvolvimento de aplicativos confidenciais.

A segurança sempre foi um foco importante do Internet das Coisas (IoT), pois geralmente os dispositivos IoT geralmente estão fora do mundo, em vez de serem protegidos em uma instalação privada. Essa exposição coloca os dispositivos em risco de violação e falsificação porque estão fisicamente acessíveis a atores ruins. IoT Edge dispositivos têm ainda mais necessidade de confiança e integridade, pois permitem que cargas de trabalho confidenciais sejam executadas na borda. Ao contrário de sensores e atuadores comuns, esses dispositivos inteligentes de borda potencialmente expõem cargas de trabalho confidenciais que antes eram executadas apenas em ambientes de nuvem ou locais protegidos.

O [IOT Edge Security Manager](iot-edge-security-manager.md) aborda uma parte do desafio computacional confidencial. O Gerenciador de segurança usa um HSM (módulo de segurança de hardware) para proteger as cargas de trabalho de identidade e os processos contínuos de um dispositivo IoT Edge.

Outro aspecto da computação confidencial é proteger os dados em uso na borda. Um *ambiente de execução confiável* ("t)" é um ambiente seguro e isolado em um processador e, às vezes, é chamado de *enclave*. Um *aplicativo confidencial* é um aplicativo que é executado em um enclave. Devido à natureza do enclaves, os aplicativos confidenciais são protegidos de outros aplicativos em execução no processador principal ou no "t".

## <a name="confidential-applications-on-iot-edge"></a>Aplicativos confidenciais no IoT Edge

Os aplicativos confidenciais são criptografados em trânsito e em repouso e descriptografados somente para execução dentro de um ambiente de execução confiável. Esse padrão se aplica a aplicativos confidenciais implantados como módulos IoT Edge.

O desenvolvedor cria o aplicativo confidencial e o empacota como um módulo IoT Edge. O aplicativo é criptografado antes de ser enviado por push para o registro de contêiner. O aplicativo permanece criptografado durante todo o processo de implantação IoT Edge até que o módulo seja iniciado no dispositivo IoT Edge. Quando o aplicativo confidencial estiver dentro do "t" do dispositivo, ele será descriptografado e poderá começar a ser executado.

![Diagrama-os aplicativos confidenciais são criptografados dentro de módulos IoT Edge até serem implantados no Secure enclave](./media/deploy-confidential-applications/confidential-applications-encrypted.png)

Os aplicativos confidenciais no IoT Edge são uma extensão lógica da [computação confidencial do Azure](../confidential-computing/overview.md). As cargas de trabalho executadas no Secure enclaves na nuvem também podem ser implantadas para serem executadas no Secure enclaves na borda.

## <a name="open-enclave"></a>Open Enclave

O [Open enclave SDK](https://openenclave.io/sdk/) é um projeto de software livre que ajuda os desenvolvedores a criar aplicativos confidenciais para várias plataformas e ambientes. O SDK do Open enclave opera dentro do ambiente de execução confiável de um dispositivo, enquanto a API do Open enclave atua como uma interface entre o alto e o ambiente de processamento não-de-alto.

O Open enclave dá suporte a várias plataformas de hardware. O suporte a IoT Edge para enclaves atualmente requer o sistema operacional de baixo portátil aberto (so de OP-cento). Para saber mais, confira [Open enclave SDK for op-SO de Altova](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/OP-TEE/Introduction.md).

O repositório Open enclave também inclui exemplos para ajudar os desenvolvedores a começar. Para obter mais informações, escolha um dos artigos introdutórios:

* [Criando amostras do SDK do Open enclave no Linux](https://github.com/openenclave/openenclave/blob/master/samples/BuildSamplesLinux.md)
* [Criando amostras do SDK do Open enclave no Windows](https://github.com/openenclave/openenclave/blob/master/samples/BuildSamplesWindows.md)

## <a name="hardware"></a>Hardware

Atualmente, [TrustBox by Scalys](https://scalys.com/trustbox-industrial/) é o único dispositivo com suporte nos contratos de serviço do fabricante para implantar aplicativos confidenciais como módulos IOT Edge. O TrustBox é criado nos dispositivos TrustBox Edge e TrustBox EdgeXL que ambos vêm pré-carregados com o SDK do Open enclave e o Azure IoT Edge.

Para obter mais informações, consulte [introdução ao Open enclave for the Scalys TrustBox](https://aka.ms/scalys-trustbox-edge-get-started).

## <a name="develop-and-deploy"></a>Desenvolver e implantar

Quando você estiver pronto para desenvolver e implantar seu aplicativo confidencial, a extensão [Microsoft Open enclave](https://marketplace.visualstudio.com/items?itemName=ms-iot.msiot-vscode-openenclave) para Visual Studio Code pode ajudar. Você pode usar o Linux ou o Windows como seu computador de desenvolvimento para desenvolver módulos para o TrustBox.

## <a name="next-steps"></a>Próximas etapas

Saiba como começar a desenvolver aplicativos confidenciais como IoT Edge módulos com a [extensão Open enclave para Visual Studio Code](https://github.com/openenclave/openenclave/tree/master/devex/vscode-extension)
