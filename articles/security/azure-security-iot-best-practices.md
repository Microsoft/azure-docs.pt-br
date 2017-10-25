---
title: "Práticas recomendadas de segurança de Internet das Coisas | Microsoft Docs"
description: "O artigo fornece uma lista selecionada de práticas recomendadas e recomendações gerais do Internet das Coisas da Microsoft."
services: security
documentationcenter: na
author: TomShinder
manager: StevenPo
editor: TomSh
ms.assetid: 2d5598c5-4c30-481d-b8f4-51ee024ea9a7
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/04/2017
ms.author: yurid
ms.openlocfilehash: 8efc0053458e338ac1afe98d9ce970c1d5cbfa81
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="internet-of-things-security-best-practices"></a>Práticas recomendadas de segurança de Internet das Coisas
Proteger a infraestrutura de IoT (Internet das Coisas) é uma tarefa crítica para qualquer pessoa envolvida com soluções de IoT. Por causa do número de dispositivos envolvidos e da natureza distribuída desses dispositivos, o impacto de um evento de segurança relacionado ao comprometimento de milhões de dispositivos IoT não é trivial, e pode ter impacto generalizado.

Por esse motivo, a segurança do IoT precisa de uma abordagem de segurança detalhada. Os dados devem ser protegidos na nuvem e conforme eles se movem por redes públicas e privadas. É preciso que métodos estejam em vigor para provisionar, com segurança, os dispositivos IoT em si. Cada camada, desde dispositivo, passando pela rede, até o back-end de nuvem, precisa de garantias fortes de segurança.

As práticas recomendadas de IoT podem ser categorizadas da seguinte maneira:

* Fabricante ou integrador de hardware de IoT
* Desenvolvedor de soluções IoT
* Implantador de soluções IoT
* Operador de solução IoT

Este artigo resume as [Práticas recomendadas de segurança de Internet das Coisas](../iot-suite/iot-security-best-practices.md). Consulte esse artigo para obter informações mais detalhadas.

## <a name="iot-hardware-manufacturer-or-integrator"></a>Fabricante ou integrador de hardware de IoT
Siga as práticas recomendadas a seguir se você for um fabricante ou integrador de hardware de IoT:

* **Escopo de hardware para requisitos mínimos**: o design de hardware deve incluir o mínimo de recursos necessários para a operação de hardware e nada mais. 
* **Tornar o hardware à prova de adulteração**: crie mecanismos para detectar a violação física do hardware, tal como a abertura da tampa do dispositivo, remoção de parte do dispositivo, etc. 
* **Criar em hardware seguro**: se o [COGS](https://en.wikipedia.org/wiki/Cost_of_goods_sold) permitir, crie recursos de segurança, como as funcionalidades de inicialização baseadas no armazenamento seguro e criptografado e no TPM (Trusted Platform Module).
* **Tornar as atualizações seguras**: atualizar o firmware durante o tempo de vida do dispositivo é inevitável.

## <a name="iot-solution-developer"></a>Desenvolvedor de soluções IoT
Siga as práticas recomendadas a seguir se você for um desenvolvedor de solução IoT:

* **Seguir a metodologia de desenvolvimento de software seguro**: desenvolver software seguro requer uma consideração inicial desde a concepção do projeto até sua implementação, teste e implantação.
* **Escolher software livre com cuidado**: software livre fornece uma oportunidade o rápido desenvolvimento de soluções.
* **Integrar com cuidado**: há muitas das falhas de segurança de software no limite de bibliotecas e APIs. 

## <a name="iot-solution-deployer"></a>Implantador de soluções IoT
Siga as práticas recomendadas a seguir se você for um implantador de solução IoT:

* **Implantar o hardware com segurança**: implantações de IoT podem exigir que o hardware seja implantado em locais não seguros, como espaços públicos ou localidades sem supervisão.
* **Manter as chaves de autenticação em segurança**: durante a implantação, cada dispositivo requer IDs de dispositivo e chaves de autenticação associadas geradas pelo serviço de nuvem. Mantenha essas chaves fisicamente seguras mesmo após a implantação. Qualquer chave comprometida pode ser usada por um dispositivo mal-intencionado passando-se por um dispositivo existente.

## <a name="iot-solution-operator"></a>Operador de solução IoT
Siga as práticas recomendadas a seguir se você for um operador de solução IoT:

* **Manter sistemas atualizados**: verifique se todos os sistemas operacionais e drivers do dispositivo estão atualizados para as versões mais recentes. 
* **Proteger contra atividades mal-intencionadas**: se o sistema operacional permitir, aplique as funcionalidades antivírus e antimalware mais recentes em cada sistema operacional do dispositivo. 
* **Auditar frequentemente**: auditar problemas relacionados à infraestrutura de IoT é essencial ao responder a incidentes de segurança.
* **Proteger fisicamente a infraestrutura de IoT**: os pior ataques de segurança contra infraestrutura de são iniciados usando o acesso físico aos dispositivos.
* **Proteger credenciais da nuvem**: credenciais de autenticação da nuvem usadas para configurar e operar uma implantação de IoT são possivelmente a maneira mais fácil para acessar e comprometer um sistema de IoT. 

