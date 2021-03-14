---
title: Códigos de erro comuns para o serviço de metadados de instância do Azure (IMDS)
titleSuffix: Azure Load Balancer
description: Visão geral de códigos de erro comuns e métodos de mitigação correspondentes para o serviço de metadados de instância do Azure (IMDS)
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: troubleshooting
ms.date: 02/12/2021
ms.author: allensu
ms.openlocfilehash: e932e211996a05b2740613381735a7de3492e5bf
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100519177"
---
# <a name="error-codes-common-error-codes-when-using-imds-to-retrieve-load-balancer-information"></a>Códigos de erro: códigos de erro comuns ao usar IMDS para recuperar informações do balanceador de carga

Este artigo descreve os erros comuns de implantação e como resolver esses erros durante o uso do serviço de metadados de instância do Azure (IMDS).

## <a name="error-codes"></a>Códigos do Erro

| Código do erro | Mensagem de erro | Detalhes e mitigação |
| --- | ---------- | ----------------- |
| 400 | Parâmetro necessário ausente " \<ParameterName> ". Corrija a solicitação e tente novamente. | O código de erro indica um parâmetro ausente. </br> Para obter mais informações sobre como adicionar o parâmetro ausente, consulte [como recuperar metadados do balanceador de carga usando o serviço de metadados de instância do Azure (IMDS)](howto-load-balancer-imds.md#sample-request-and-response).
| 400 | O valor do parâmetro não é permitido ou o valor \<ParameterValue> do parâmetro "" não é permitido para o parâmetro "ParameterName". Corrija a solicitação e tente novamente. | O código de erro indica que o formato da solicitação não está configurado corretamente. </br> Para obter mais informações, consulte [como recuperar metadados do balanceador de carga usando o serviço de metadados de instância do Azure (IMDS)](howto-load-balancer-imds.md#sample-request-and-response) para corrigir o corpo da solicitação e emitir uma nova tentativa. |
| 400 | Solicitação inesperada. Verifique os parâmetros de consulta e tente novamente. | O código de erro indica que o formato da solicitação não está configurado corretamente. </br> Para obter mais informações, consulte [como recuperar metadados do balanceador de carga usando o serviço de metadados de instância do Azure (IMDS)](howto-load-balancer-imds.md#sample-request-and-response) para corrigir o corpo da solicitação e emitir uma nova tentativa. |
| 404 | Nenhum metadado do balanceador de carga foi encontrado. Verifique se sua VM está usando um balanceador de carga de SKU não básico e tente novamente mais tarde. | O código de erro indica que sua máquina virtual não está associada a um balanceador de carga ou o balanceador de carga é o SKU básico em vez do padrão. </br> Para obter mais informações, consulte [início rápido: criar um balanceador de carga público para balancear a carga de VMs usando o portal do Azure](quickstart-load-balancer-standard-public-portal.md?tabs=option-1-create-load-balancer-standard) para implantar um balanceador de carga padrão.|
| 404 | API não encontrada: caminho = " \<UrlPath> ", método = " \<Method> " | O código de erro indica uma configuração incorreta do caminho. </br> Para obter mais informações, consulte [como recuperar metadados do balanceador de carga usando o serviço de metadados de instância do Azure (IMDS)](howto-load-balancer-imds.md#sample-request-and-response) para corrigir o corpo da solicitação e emitir uma nova tentativa.|
| 405 | O método http não é permitido: caminho = " \<UrlPath> ", método = " \<Method> " | O código de erro indica um verbo HTTP sem suporte. </br> Para obter mais informações, consulte [serviço de metadados de instância do Azure (IMDS)](../virtual-machines/windows/instance-metadata-service.md?tabs=windows#http-verbs) para verbos com suporte. |
| 429 | Número excessivo de solicitações | O código de erro indica um limite de taxa. </br> Para obter mais informações sobre a limitação de taxa, consulte [serviço de metadados de instância do Azure (IMDS)](../virtual-machines/windows/instance-metadata-service.md?tabs=windows#rate-limiting).|
| 400 | O corpo da solicitação é maior que MaxBodyLength:... | O código de erro indica uma solicitação maior que o MaxBodyLength. </br> Para obter mais informações sobre o tamanho do corpo, consulte [como recuperar metadados do balanceador de carga usando o serviço de metadados de instância do Azure (IMDS)](howto-load-balancer-imds.md#sample-request-and-response).|
| 400 | O comprimento da chave de parâmetro é maior que MaxParameterKeyLength:... | O código de erro indica um comprimento de chave de parâmetro maior do que o MaxParameterKeyLength. </br> Para obter mais informações sobre o tamanho do corpo, consulte [como recuperar metadados do balanceador de carga usando o serviço de metadados de instância do Azure (IMDS)](howto-load-balancer-imds.md#sample-request-and-response). |
| 400 | O comprimento do valor do parâmetro é maior que MaxParameterValueLength:... | O código de erro indica um comprimento de chave de parâmetro maior do que o MaxParameterValueLength. </br> Para obter mais informações sobre o tamanho do valor, consulte [como recuperar metadados do balanceador de carga usando o serviço de metadados de instância do Azure (IMDS)](howto-load-balancer-imds.md#sample-request-and-response).|
| 400 | O comprimento do valor do cabeçalho do parâmetro é maior que MaxHeaderValueLength:... | O código de erro indica um comprimento de valor de cabeçalho de parâmetro maior do que o MaxHeaderValueLength. </br> Para obter mais informações sobre o tamanho do valor, consulte [como recuperar metadados do balanceador de carga usando o serviço de metadados de instância do Azure (IMDS)](howto-load-balancer-imds.md#sample-request-and-response).|
| 404 | A API de metadados Load Balancer não está disponível no momento. Tente novamente mais tarde | O código de erro indica que a API pode ser provisionada. Tente a solicitação mais tarde. |
| 404 | /Metadata/loadbalancer não está disponível no momento | O código de erro indica que a API está no andamento da habilitação. Tente a solicitação mais tarde. |
| 503 | Serviço interno indisponível. Tente novamente mais tarde  | O código de erro indica que a API está temporariamente indisponível. Tente a solicitação mais tarde. |
|  |  |

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o [serviço de metadados de instância do Azure](../virtual-machines/windows/instance-metadata-service.md)

