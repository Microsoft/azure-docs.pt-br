---
title: Modelos de consumo de veículo para roteamento | Mapas do Microsoft Azure
description: 'Saiba mais sobre os modelos de consumo que o Azure Maps dá suporte: compensar e elétrico. Veja quais parâmetros cada modelo usa e as restrições de parâmetro de exibição.'
author: subbarayudukamma
ms.author: skamma
ms.date: 05/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: b44186d783a249192a8c13ee97063034ee319df7
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96013132"
---
# <a name="consumption-model"></a>Modelo de consumo

O serviço de roteamento fornece um conjunto de parâmetros para uma descrição detalhada do modelo de consumo específico do veículo.
Dependendo do valor de **vehicleEngineType**, há suporte para dois modelos de consumo principais: _Combustion_ e _Electric_. É incorreto especificar parâmetros que pertencem a modelos diferentes na mesma solicitação. Além disso, os parâmetros do modelo de consumo não podem ser usados com os seguintes valores de **viajamode** : _bicicleta_ e _básicos_.

## <a name="parameter-constraints-for-consumption-model"></a>Restrições de parâmetro de modelo de consumo

Em ambos os modelos de consumo, há algumas dependências ao especificar parâmetros. O que significa que, especificar explicitamente alguns parâmetros pode exigir a especificação de alguns outros parâmetros. Estas são as dependências que devem ser consideradas:

* Todos os parâmetros exigem que **constantSpeedConsumption** seja especificado pelo usuário. É um erro especificar qualquer outro parâmetro de modelo de consumo, se **constantSpeedConsumption** não for especificado. O parâmetro **vehicleWeight** é uma exceção para esse requisito.
* **accelerationEfficiency** e **decelerationEfficiency** devem ser sempre especificados como um par (ou seja, ambos ou nenhum).
* Se **accelerationEfficiency** e **decelerationEfficiency** forem especificados, o produto de seus valores não deve ser maior que 1 (para evitar um movimento perpétuo).
* **uphillEfficiency** e **downhillEfficiency** sempre devem ser especificados como um par (ou seja, ambos ou nenhum).
* Se **uphillEfficiency** e **downhillEfficiency** forem especificados, o produto de seus valores não deve ser maior que 1 (para evitar um movimento perpétuo).
* Se os parâmetros de \*__Efficiency__ são especificados pelo usuário, então, **vehicleWeight** também deve ser especificado. Quando **vehicleEngineType** for _combustão_, **fuelEnergyDensityInMJoulesPerLiter** também deve ser especificado.
* **maxChargeInkWh** e **currentChargeInkWh** devem ser sempre especificados como um par (ou seja, ambos ou nenhum).

> [!NOTE]
> Se apenas **constantSpeedConsumption** for especificado, nenhum outro aspecto de consumo como inclinações acentuadas e aceleração de veículo são levados em consideração para cálculos de consumo.

## <a name="combustion-consumption-model"></a>Modelo de consumo de combustão

O modelo de consumo de combustão é usado quando **vehicleEngineType** é definido como _combustion_.
A lista de parâmetros que pertencem a esse modelo está abaixo. Consulte a seção Parâmetros para ver uma descrição detalhada.

* constantSpeedConsumptionInLitersPerHundredkm
* VehicleWeight
* currentFuelInLiters
* auxiliaryPowerInLitersPerHour
* fuelEnergyDensityInMJoulesPerLiter
* accelerationEfficiency
* decelerationEfficiency
* uphillEfficiency
* downhillEfficiency

## <a name="electric-consumption-model"></a>Modelo de consumo elétrico

O modelo de consumo elétrico é usado quando **vehicleEngineType** é definido como _electric_.
A lista de parâmetros que pertencem a esse modelo está abaixo. Consulte a seção Parâmetros para ver uma descrição detalhada.

* constantSpeedConsumptionInkWhPerHundredkm
* VehicleWeight
* currentChargeInkWh
* maxChargeInkWh
* auxiliaryPowerInkW
* accelerationEfficiency
* decelerationEfficiency
* uphillEfficiency
* downhillEfficiency

## <a name="sensible-values-of-consumption-parameters"></a>Valores razoáveis de parâmetros de consumo

Um conjunto específico de parâmetros de consumo pode ser rejeitado, embora o conjunto possa atender a todos os requisitos explícitos. Acontece quando o valor de um parâmetro específico, ou uma combinação de valores de vários parâmetros, é considerado para levar a magnitudes indesejadas de valores de consumo. Se isso acontecer, ele provavelmente indicará um erro de entrada, pois é exercido um cuidado para acomodar todos os valores razoáveis de parâmetros de consumo. No caso de um determinado conjunto de parâmetros de consumo ser rejeitado, a mensagem de erro conterá uma explicação de texto sobre o(s) motivo(s).
As descrições detalhadas dos parâmetros têm exemplos de valores razoáveis para ambos os modelos.
