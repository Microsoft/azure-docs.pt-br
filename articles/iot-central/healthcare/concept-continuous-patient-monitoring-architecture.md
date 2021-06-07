---
title: Arquitetura contínua de monitoramento de pacientes no Azure IoT Central | Microsoft Docs
description: Tutorial – saiba mais sobre uma arquitetura de uma solução de monitoramento contínuo de pacientes.
author: philmea
ms.author: philmea
ms.date: 12/11/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 6f7359b2b2fb0a1ea6ce92ec52bba15fc74fc75a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103017147"
---
# <a name="continuous-patient-monitoring-architecture"></a>Arquitetura do monitoramento contínuo do paciente

Este artigo descreve a arquitetura de uma solução criada com base no modelo de aplicativo de **monitoramento contínuo de pacientes**:

As soluções de monitoramento contínuo de pacientes podem ser criadas com o modelo de aplicativo fornecido e a arquitetura descrita abaixo como diretrizes.

:::image type="content" source="media/cpm-architecture.png" alt-text="Arquitetura do monitoramento contínuo do paciente":::

## <a name="details"></a>Detalhes

Esta seção descreve cada parte do diagrama da arquitetura com mais detalhes:

### <a name="bluetooth-low-energy-ble-medical-devices"></a>Dispositivos médicos de BLE (Bluetooth de Baixa Energia)

Muitos acessórios médicos usados em soluções de IoT de saúde são dispositivos BLE. Esses dispositivos não podem se comunicar diretamente com a nuvem e precisam usar um gateway para trocar dados com a solução de nuvem. Essa arquitetura usa um aplicativo de telefone celular como gateway.

### <a name="mobile-phone-gateway"></a>Gateway de celular

A função principal do aplicativo de celular é coletar os dados do BLE dos dispositivos médicos e transmiti-los para o IoT Central. O aplicativo também orienta os pacientes na configuração do dispositivo e permite que eles exibam seus dados pessoais de saúde. Outras soluções podem usar um gateway de tablet ou um gateway estático em uma sala de hospital. Um aplicativo móvel de exemplo de software livre está disponível para Android e iOS para usar como ponto de partida para o desenvolvimento do aplicativo. Para saber mais, confira o [Aplicativo móvel de monitoramento contínuo de pacientes do IoT Central](/samples/iot-for-all/iotc-cpm-sample/iotc-cpm-sample/).

### <a name="export-to-azure-api-for-fhirreg"></a>Exportar para a API do Azure para FHIR&reg;

O Azure IoT Central tem conformidade com HIPAA e é certificado pelo HITRUST&reg;. Você também pode enviar dados de saúde dos pacientes para outros serviços usando a [API do Azure para FHIR](../../healthcare-apis/fhir/overview.md). A API do Azure para FHIR é uma API baseada em padrões para dados clínicos de saúde. O [Conector do Azure IoT para FHIR](../../healthcare-apis/fhir/iot-fhir-portal-quickstart.md) permite que você use a API do Azure para FHIR como um destino de exportação de dados contínuos provenientes do IoT Central.

### <a name="machine-learning"></a>Aprendizado de máquina

Use modelos de machine learning com os dados do FHIR para gerar insights e dar suporte à tomada de decisões por sua equipe de atendimento. Para saber mais, confira a [documentação de machine learning do Azure](../../machine-learning/index.yml).

### <a name="provider-dashboard"></a>Dashboard do provedor

Use os dados da API do Azure para FHIR para criar um dashboard de informações de pacientes ou integrá-lo diretamente a um registro médico eletrônico usado por equipes de cuidados. As equipes de cuidados podem usar o dashboard para dar assistência aos pacientes e identificar os sinais antecipados de aviso de deterioração. Para saber mais, confira o tutorial [Criar um dashboard de provedor do Power BI](tutorial-health-data-triage.md).

## <a name="next-steps"></a>Próximas etapas

A próxima etapa sugerida é [Saiba como implantar um modelo de aplicativo de monitoramento contínuo de pacientes](tutorial-continuous-patient-monitoring.md).