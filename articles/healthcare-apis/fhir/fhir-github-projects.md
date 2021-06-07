---
title: Projetos do GitHub relacionados para a API do Azure para FHIR
description: Listar todos os repositórios do GitHub (software livre) para a API do Azure para FHIR.
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 02/01/2021
ms.author: ginle
ms.openlocfilehash: 9977765183bd567377592631d65f3e548bef4b34
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105097624"
---
# <a name="related-github-projects"></a>Projetos do GitHub relacionados

Temos muitos projetos de software livre no GitHub que fornecem o código-fonte e as instruções para implantar serviços para vários usos. Você sempre é bem-vindo a visitar nossos repositórios do GitHub para aprender e experimentar nossos recursos e produtos. 

## <a name="fhir-server"></a>Servidor FHIR
* [Microsoft/fhir-Server](https://github.com/microsoft/fhir-server/): servidor fhir de código-fonte aberto, que é a base para a API do Azure para fhir
* Para ver as versões mais recentes, consulte as [notas de versão](https://github.com/microsoft/fhir-server/releases)
* [Microsoft/fhir-Server-Samples](https://github.com/microsoft/fhir-server-samples): um ambiente de exemplo

## <a name="data-conversion--anonymization"></a>Conversão de dados & anonimato

#### <a name="fhir-converter"></a>Conversor de FHIR
* [Microsoft/FHIR-converter](https://github.com/microsoft/FHIR-Converter): um utilitário de conversão para converter formatos de dados herdados em FHIR
* Integrado com a API do Azure para FHIR, bem como FHIR Server para o Azure na forma de operação de $convert dados
* Aprimoramentos contínuos no OSS e integração contínua aos servidores FHIR
 
#### <a name="fhir-converter---vs-code-extension"></a>Conversor de FHIR-extensão de VS Code
* [Microsoft/FHIR-Tools-for-anonimato](https://github.com/microsoft/FHIR-Tools-for-Anonymization): um conjunto de ferramentas para ajudar com a anonimato de dados (em formato FHIR)
* Integrado à API do Azure para FHIR, bem como ao servidor FHIR para o Azure na forma de "exportação de-identificado"

#### <a name="fhir-tools-for-anonymization"></a>Ferramentas de FHIR para anonimato
* [Microsoft/vscode-azurehealthcareapis-Tools](https://github.com/microsoft/vscode-azurehealthcareapis-tools): uma extensão vs Code que contém uma coleção de ferramentas para trabalhar com as APIs de saúde do Azure
* Liberado para Visual Studio Marketplace
* Usado para criar modelos de Liquid a serem usados no conversor de FHIR

## <a name="iot-connector"></a>Conector IoT

#### <a name="integration-with-iot-hub-and-iot-central"></a>Integração com o Hub IoT e IoT Central
* [Microsoft/iomt-fhir](https://github.com/microsoft/iomt-fhir): integração com o Hub IoT ou IOT central ao fhir com a normalização de dados e a conversão de fhir dos dados normalizados
* Normalização: as informações de dados do dispositivo são extraídas em um formato comum para processamento adicional
* Conversão de FHIR: os dados normalizados e agrupados são mapeados para FHIR. As observações são criadas ou atualizadas de acordo com os modelos configurados e vinculadas ao dispositivo e ao paciente.
* [Ferramentas para ajudar a criar o mapa de conversa](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper): visualize a configuração de mapeamento para normalizar os dados de entrada do dispositivo e transformá-los para os recursos do FHIR. Os desenvolvedores podem usar essa ferramenta para editar e testar os mapeamentos, o mapeamento de dispositivo e o mapeamento de FHIR e exportá-los para carregar para o conector IoT no portal do Azure.

#### <a name="healthkit-and-fhir-integration"></a>Integração de HealthKit e FHIR
* [Microsoft/healthkit-on-fhir](https://github.com/microsoft/healthkit-on-fhir): uma biblioteca Swift que automatiza a exportação de dados Apple healthkit para um servidor fhir

 