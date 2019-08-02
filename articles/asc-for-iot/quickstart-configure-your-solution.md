---
title: Configurar a Versão Prévia da solução Central de Segurança do Azure para IoT | Microsoft Docs
description: Saiba como configurar sua solução de IoT de ponta a ponta usando a Central de Segurança do Azure para IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: ae2207e8-ac5b-4793-8efc-0517f4661222
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 7f90dba899651b677740e9ceb88bdd579ebb073c
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67616634"
---
# <a name="quickstart-configure-your-iot-solution"></a>Início Rápido: Configure a solução de IoT

> [!IMPORTANT]
> A Central de Segurança do Azure para IoT está em versão prévia pública no momento.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este artigo fornece uma explicação de como realizar a configuração inicial de sua solução de segurança de IoT usando a ASC para IoT. 

## <a name="azure-security-center-asc-for-iot"></a>ASC (Central de Segurança do Azure) para IoT

A ASC para IoT fornece segurança abrangente de ponta a ponta para soluções de IoT com base no Azure.

Com o ASC para IoT, é possível monitorar toda a solução de IoT em um painel, identificando todos os dispositivos IoT, as plataformas IoT e os recursos de back-end no Azure.

Uma vez habilitado em seu Hub IoT, a ASC para IoT identifica automaticamente outros serviços do Azure, também conectados ao seu hub IoT e relacionados à solução de IoT.

Além da detecção automática de relacionamento, também é possível selecionar e escolher outros recursos do Azure para marcar como parte da solução de IoT.
Suas seleções permitem adicionar assinaturas, grupos de recursos ou recursos únicos inteiros.

Depois de definir todas as relações de recursos, a ASC para IoT aproveita a Central de Segurança do Azure para fornecer recomendações de segurança e alertas para esses recursos.

## <a name="add-azure-resources-to-your-iot-solution"></a>Adicionar recursos do Azure à solução de IoT

Para adicionar novos recursos à solução de IoT, faça o seguinte: 

1. Abra o **Hub IoT** no portal do Azure. 
2. No menu à esquerda, selecione e abra **Recursos** em **Segurança**. 
3. Selecione **Adicionar Recursos**.
4. Escolha os recursos que pertencem à solução de IoT.
5. Clique em **Adicionar**. 

Parabéns! Você adicionou um novo recurso à solução de IoT.

Como parte da solução de IoT, a ASC para IoT agora monitora os recursos recentemente adicionados e identifica recomendações e alertas de segurança relevantes.

## <a name="next-steps"></a>Próximas etapas

Avance para o próximo artigo para aprender como criar módulos de segurança...

> [!div class="nextstepaction"]
> [Criar Módulos de Segurança](quickstart-create-security-twin.md)