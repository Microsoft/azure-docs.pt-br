---
title: Como executar Self-Hosted Integration Runtime no contêiner do Windows
description: Saiba mais sobre como executar Self-Hosted Integration Runtime no contêiner do Windows.
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/05/2020
ms.openlocfilehash: e34e1f589442be41e2a4eac3ac893f377675e4b5
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100379498"
---
# <a name="how-to-run-self-hosted-integration-runtime-in-windows-container"></a>Como executar Self-Hosted Integration Runtime no contêiner do Windows

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Este artigo explicará como executar Self-Hosted Integration Runtime no contêiner do Windows.
Azure Data Factory estão fornecendo o suporte ao contêiner do Windows oficial de Self-Hosted Integration Runtime. Você pode baixar o código-fonte de Build do Docker e combinar o processo de criação e execução em seu próprio pipeline de entrega contínua. 

## <a name="prerequisites"></a>Pré-requisitos 
- [Requisitos do contêiner do Windows](/virtualization/windowscontainers/deploy-containers/system-requirements)
- Docker versão 2,3 e posterior 
- Self-Hosted Integration Runtime versão 4.11.7512.1 e posterior 
## <a name="get-started"></a>Introdução 
1.  Instalar o Docker e habilitar o contêiner do Windows 
2.  Baixar o código-fonte em https://github.com/Azure/Azure-Data-Factory-Integration-Runtime-in-Windows-Container
3.  Baixe a versão mais recente do SHIR na pasta ' SHIR ' 
4.  Abra a pasta no Shell: 
```console
cd "yourFolderPath"
```

5.  Crie a imagem do Docker do Windows: 
```console
docker build . -t "yourDockerImageName" 
```
6.  Executar contêiner do Docker: 
```console
docker run -d -e NODE_NAME="irNodeName" -e AUTH_KEY="IR_AUTHENTICATION_KEY" -e ENABLE_HA=true HA_PORT=8060 "yourDockerImageName"    
```
> [!NOTE]
> AUTH_KEY é obrigatório para este comando. NODE_NAME, ENABLE_HA e HA_PORT são opcionais. Se você não definir o valor, o comando usará valores padrão. O valor padrão de ENABLE_HA é false e HA_PORT é 8060.

## <a name="container-health-check"></a>Verificação de integridade do contêiner 
Após um período de inicialização de 120 segundos, o verificador de integridade será executado periodicamente a cada 30 segundos. Ele fornecerá o status de integridade de IR para o mecanismo de contêiner. 

## <a name="limitations"></a>Limitações
No momento, não há suporte para os recursos abaixo ao executar Self-Hosted Integration Runtime no contêiner do Windows:
- Proxy HTTP 
- Comunicação de nó de nó criptografado com certificado TLS/SSL 
- Gerar e importar backup 
- Serviço daemon 
- Atualização automática 

### <a name="next-steps"></a>Próximas etapas
- Revise os [conceitos de runtime de integração no Azure Data Factory](./concepts-integration-runtime.md).
- Saiba como [criar um runtime de integração auto-hospedada no portal do Azure](./create-self-hosted-integration-runtime.md).