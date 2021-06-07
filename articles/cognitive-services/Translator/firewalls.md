---
title: Converter por trás de firewalls-Tradutor
titleSuffix: Azure Cognitive Services
description: O tradutor de serviços cognitivas do Azure pode traduzir por trás de firewalls usando o nome de domínio ou a filtragem de IP.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: lajanuar
ms.openlocfilehash: f5dd72328180574809c812d670f8165ad84963ae
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98897740"
---
# <a name="how-to-translate-behind-ip-firewalls-with-translator"></a>Como converter por trás de firewalls IP com o tradutor

O tradutor pode fazer a conversão por trás de firewalls usando o nome de domínio ou a filtragem de IP. A filtragem de nome de domínio é o método preferencial. **Não é recomendável** executar o Microsoft Translator por trás de um firewall filtrado por IP. A configuração é suscetível a interrupção no futuro sem aviso prévio.

## <a name="translator-ip-addresses"></a>Endereços IP do Tradutor
Os endereços IP para o api.cognitive.microsofttranslator.com-Translator a partir de 21 de agosto de 2019:

* **Pacífico Asiático:** 20.40.125.208, 20.43.88.240, 20.184.58.62, 40.90.139.163, 104.44.89.44
* **Europa:** 40.90.138.4, 40.90.141.99, 51.105.170.64, 52.155.218.251
* **América do Norte:** 40.90.139.36, 40.90.139.2, 40.119.2.134, 52.224.200.129, 52.249.207.163

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Converter por trás de firewalls IP no Tradutor](reference/v3-0-translate.md)
