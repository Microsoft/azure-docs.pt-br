---
title: Referência de aprimoramentos do Azure Sentinel UEBA | Microsoft Docs
description: Este artigo exibe os aprimoramentos de entidade gerados pela análise de comportamento de entidade do Sentinela do Azure.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 01/04/2021
ms.author: yelevin
ms.openlocfilehash: daba8fc1f645b51dc8668c806be63744b6ae0842
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97901663"
---
# <a name="azure-sentinel-ueba-enrichments-reference"></a>Referência de aprimoramentos do Azure Sentinel UEBA

Essas tabelas listam e descrevem os aprimoramentos de entidade que podem ser usados para focar e ajustar a nitidez de incidentes de segurança.

As duas primeiras tabelas, **insights do usuário** e **insights do dispositivo**, contêm informações de entidade do Active Directory/Azure AD e fontes de inteligência contra ameaças da Microsoft.

<a name="baseline-explained"></a>O restante das tabelas, em **tabelas de informações de atividade**, contém informações de entidade com base nos perfis comportamentais criados pela análise de comportamento de entidade do Sentinela do Azure. As atividades são analisadas em uma linha de base compilada dinamicamente cada vez que é usada. Cada atividade tem seu período de lookback definido, do qual essa linha de base dinâmica é derivada. Esse período é especificado na coluna [**linha de base**](#activity-insights-tables) nesta tabela.

> [!NOTE] 
> O campo **nome de enriquecimento** em todas as três tabelas exibe duas linhas de informações. O primeiro, em **negrito**, é o "nome amigável" do enriquecimento. O segundo *(em itálico e parênteses)* é o nome do campo do enriquecimento como armazenado na tabela de análise de [**comportamento**](identify-threats-with-entity-behavior-analytics.md#data-schema).

## <a name="user-insights-table"></a>Tabela de informações do usuário

| Nome do enriquecimento | Descrição | Valor de exemplo |
| --- | --- | --- | --- |
| **Nome de exibição da conta**<br>*(AccountDisplayName)* | O nome de exibição da conta do usuário. | Administrador, Hayden Cook |
| **Domínio da conta**<br>*(AccountDomain)* | O nome de domínio da conta do usuário. |  |
| **ID do objeto da conta**<br>*(AccountObjectID)* | A ID de objeto da conta do usuário. | a58df659-5cab-446c-9dd0-5a3af20ce1c2 |
| **Raio da transmissão**<br>*(BlastRadius)* | O RADIUS é calculado com base em vários fatores: a posição do usuário na árvore org e as funções e permissões de Azure Active Directory do usuário. | Baixo, Médio, Alto |
| **É conta inativa**<br>*(IsDormantAccount)* | A conta não foi usada nos últimos 180 dias. | Verdadeiro, Falso |
| **É administrador local**<br>*(IsLocalAdmin)* | A conta tem privilégios de administrador local. | Verdadeiro, Falso |
| **É nova conta**<br>*(IsNewAccount)* | A conta foi criada nos últimos 30 dias. | Verdadeiro, Falso |
| **SID local**<br>*(OnPremisesSID)* | O SID local do usuário relacionado à ação. | S-1-5-21-1112946627-1321165628-2437342228-1103 |
|

## <a name="device-insights-table"></a>Tabela de informações do dispositivo

| Nome do enriquecimento | Descrição | Valor de exemplo |
| --- | --- | --- | --- |
| **Navegador**<br>*Browser* | O navegador usado na ação. | Borda, Chrome |
| **Família de dispositivos**<br>*(DeviceFamily)* | A família de dispositivos usada na ação. | Windows |
| **Tipo de dispositivo**<br>*DeviceType* | O tipo de dispositivo do cliente usado na ação | Área de trabalho |
| **INSTRU**<br>*INSTRU* | O provedor de serviços de Internet usado na ação. |  |
| **Sistema operacional**<br>*Operacional* | O sistema operacional usado na ação. | Windows 10 |
| **Descrição do indicador da Intel de ameaça**<br>*(ThreatIntelIndicatorDescription)* | Descrição do indicador de ameaça observado resolvido do endereço IP usado na ação. | O host é membro de botnet: azorult |
| **Tipo de indicador da Intel de ameaça**<br>*(ThreatIntelIndicatorType)* | O tipo do indicador de ameaça resolvido do endereço IP usado na ação. | Botnet, C2, CryptoMining, Darknet, DDoS, MaliciousUrl, malware, phishing, proxy, PUA, Watchlist |
| **Agente do usuário**<br>*UserAgent* | O agente do usuário usado na ação. | Microsoft Azure a biblioteca de cliente do Graph 1,0,<br>Swagger-CodeGen/1.4.0.0/Csharp,<br>EvoSTS |
| **Família de agente do usuário**<br>*(UserAgentFamily)* | A família de agente do usuário usada na ação. | Chrome, Edge, Firefox |
|

## <a name="activity-insights-tables"></a>Tabelas de informações de atividade

#### <a name="action-performed"></a>Ação executada

| Nome do enriquecimento | [Linha de base](#baseline-explained) (dias) | Descrição | Valor de exemplo |
| --- | --- | --- | --- |
| **Primeira vez que o usuário realizou a ação**<br>*(FirstTimeUserPerformedAction)* | 180 | A ação foi realizada pela primeira vez pelo usuário. | Verdadeiro, Falso |
| **Ação executada de forma não comum pelo usuário**<br>*(ActionUncommonlyPerformedByUser)* | 10 | A ação normalmente não é executada pelo usuário. | Verdadeiro, Falso |
| **Ação executada de forma não comum entre os pares**<br>*(ActionUncommonlyPerformedAmongPeers)* | 180 | A ação não é normalmente executada entre os pares do usuário. | Verdadeiro, Falso |
| **Primeira ação executada no locatário**<br>*(FirstTimeActionPerformedInTenant)* | 180 | A ação foi realizada pela primeira vez por qualquer pessoa na organização. | Verdadeiro, Falso |
| **Ação executada de forma não comum no locatário**<br>*(ActionUncommonlyPerformedInTenant)* | 180 | A ação não é executada normalmente na organização. | Verdadeiro, Falso |
|

#### <a name="app-used"></a>Aplicativo usado

| Nome do enriquecimento | [Linha de base](#baseline-explained) (dias) | Descrição | Valor de exemplo |
| --- | --- | --- | --- |
| **Primeira vez que o aplicativo usado pelo usuário**<br>*(FirstTimeUserUsedApp)* | 180 | O aplicativo foi usado pela primeira vez pelo usuário. | Verdadeiro, Falso |
| **Aplicativo usado incomunsmente pelo usuário**<br>*(AppUncommonlyUsedByUser)* | 10 | O aplicativo não é comumente usado pelo usuário. | Verdadeiro, Falso |
| **Aplicativo usado incomuns entre os pares**<br>*(AppUncommonlyUsedAmongPeers)* | 180 | O aplicativo não é comumente usado entre os pares do usuário. | Verdadeiro, Falso |
| **Aplicativo da primeira vez observado no locatário**<br>*(FirstTimeAppObservedInTenant)* | 180 | O aplicativo foi observado pela primeira vez na organização. | Verdadeiro, Falso |
| **Aplicativo usado incomunsmente no locatário**<br>*(AppUncommonlyUsedInTenant)* | 180 | O aplicativo não é normalmente usado na organização. | Verdadeiro, Falso |
| 

#### <a name="browser-used"></a>Navegador usado

| Nome do enriquecimento | [Linha de base](#baseline-explained) (dias) | Descrição | Valor de exemplo |
| --- | --- | --- | --- |
| **Primeira vez que o usuário se conectou via navegador**<br>*(FirstTimeUserConnectedViaBrowser)* | 30 | O navegador foi observado pela primeira vez pelo usuário. | Verdadeiro, Falso |
| **Navegador usado incomunsmente pelo usuário**<br>*(BrowserUncommonlyUsedByUser)* | 10 | O navegador não é comumente usado pelo usuário. | Verdadeiro, Falso |
| **Navegador usado incomuns entre os pares**<br>*(BrowserUncommonlyUsedAmongPeers)* | 30 | O navegador não é comumente usado entre os pares do usuário. | Verdadeiro, Falso |
| **Primeiro navegador observado no locatário**<br>*(FirstTimeBrowserObservedInTenant)* | 30 | O navegador foi observado pela primeira vez na organização. | Verdadeiro, Falso |
| **Navegador usado incomunsmente no locatário**<br>*(BrowserUncommonlyUsedInTenant)* | 30 | O navegador não é normalmente usado na organização. | Verdadeiro, Falso |
| 

#### <a name="country-connected-from"></a>País conectado de

| Nome do enriquecimento | [Linha de base](#baseline-explained) (dias) | Descrição | Valor de exemplo |
| --- | --- | --- | --- |
| **Primeira vez que o usuário se conectou do país**<br>*(FirstTimeUserConnectedFromCountry)* | 90 | A localização geográfica, como resolvida do endereço IP, foi conectada pela primeira vez pelo usuário. | Verdadeiro, Falso |
| **País conectado incomuns do usuário**<br>*(CountryUncommonlyConnectedFromByUser)* | 10 | A localização geográfica, como resolvida do endereço IP, não é normalmente conectada pelo usuário. | Verdadeiro, Falso |
| **País conectado incomuns entre os pares**<br>*(CountryUncommonlyConnectedFromAmongPeers)* | 90 | A localização geográfica, como resolvida do endereço IP, não é normalmente conectada entre os pares do usuário. | Verdadeiro, Falso |
| **Conexão pela primeira vez do país observado no locatário**<br>*(FirstTimeConnectionFromCountryObservedInTenant)* | 90 | O país foi conectado pela primeira vez por qualquer pessoa na organização. | Verdadeiro, Falso |
| **País conectado incomuns de no locatário**<br>*(CountryUncommonlyConnectedFromInTenant)* | 90 | A localização geográfica, como resolvida do endereço IP, não é normalmente conectada da organização. | Verdadeiro, Falso |
| 

#### <a name="device-used-to-connect"></a>Dispositivo usado para se conectar

| Nome do enriquecimento | [Linha de base](#baseline-explained) (dias) | Descrição | Valor de exemplo |
| --- | --- | --- | --- |
| **Primeira vez que o usuário se conectou do dispositivo**<br>*(FirstTimeUserConnectedFromDevice)* | 30 | O dispositivo de origem foi conectado pela primeira vez pelo usuário. | Verdadeiro, Falso |
| **Dispositivo usado incomuns pelo usuário**<br>*(DeviceUncommonlyUsedByUser)* | 10 | O dispositivo não é normalmente usado pelo usuário. | Verdadeiro, Falso |
| **Dispositivo usado incomuns entre os pares**<br>*(DeviceUncommonlyUsedAmongPeers)* | 180 | O dispositivo não é comumente usado entre os pares do usuário. | Verdadeiro, Falso |
| **Primeira vez que o dispositivo é observado no locatário**<br>*(FirstTimeDeviceObservedInTenant)* | 30 | O dispositivo foi observado pela primeira vez na organização. | Verdadeiro, Falso |
| **Dispositivo usado incomunsmente no locatário**<br>*(DeviceUncommonlyUsedInTenant)* | 180 | O dispositivo não é normalmente usado na organização. | Verdadeiro, Falso |
| 

#### <a name="other-device-related"></a>Outros dispositivos relacionados ao dispositivo

| Nome do enriquecimento | [Linha de base](#baseline-explained) (dias) | Descrição | Valor de exemplo |
| --- | --- | --- | --- |
| **Primeira vez que o usuário fez logon no dispositivo**<br>*(FirstTimeUserLoggedOnToDevice)* | 180 | O dispositivo de destino foi conectado pela primeira vez pelo usuário. | Verdadeiro, Falso |
| **Família de dispositivos usada incomunsmente no locatário**<br>*(DeviceFamilyUncommonlyUsedInTenant)* | 30 | A família de dispositivos não é comumente usada na organização. | Verdadeiro, Falso |
| 

#### <a name="internet-service-provider-used-to-connect"></a>Provedor de serviços de Internet usado para se conectar

| Nome do enriquecimento | [Linha de base](#baseline-explained) (dias) | Descrição | Valor de exemplo |
| --- | --- | --- | --- |
| **Primeira vez que o usuário se conectou via ISP**<br>*(FirstTimeUserConnectedViaISP)* | 30 | O ISP foi observado pela primeira vez pelo usuário. | Verdadeiro, Falso |
| **ISP usado incomunsmente pelo usuário**<br>*(ISPUncommonlyUsedByUser)* | 10 | O ISP não é comumente usado pelo usuário. | Verdadeiro, Falso |
| **ISP usado incomunsmente entre os pares**<br>*(ISPUncommonlyUsedAmongPeers)* | 30 | O ISP não é comumente usado entre os pares do usuário. | Verdadeiro, Falso |
| **Conexão pela primeira vez via ISP no locatário**<br>*(FirstTimeConnectionViaISPInTenant)* | 30 | O ISP foi observado pela primeira vez na organização. | Verdadeiro, Falso |
| **ISP usado incomunsmente no locatário**<br>*(ISPUncommonlyUsedInTenant)* | 30 | O ISP não é comumente usado na organização. | Verdadeiro, Falso |
| 

#### <a name="resource-accessed"></a>Recurso acessado

| Nome do enriquecimento | [Linha de base](#baseline-explained) (dias) | Descrição | Valor de exemplo |
| --- | --- | --- | --- |
| **Primeiro recurso acessado pelo usuário**<br>*(FirstTimeUserAccessedResource)* | 180 | O recurso foi acessado pela primeira vez pelo usuário. | Verdadeiro, Falso |
| **Recurso acessado incomuns pelo usuário**<br>*(ResourceUncommonlyAccessedByUser)* | 10 | O recurso não é normalmente acessado pelo usuário. | Verdadeiro, Falso |
| **Recurso incomuns acessado entre pares**<br>*(ResourceUncommonlyAccessedAmongPeers)* | 180 | O recurso não é normalmente acessado entre os pares do usuário. | Verdadeiro, Falso |
| **A primeira vez que o recurso é acessado no locatário**<br>*(FirstTimeResourceAccessedInTenant)* | 180 | O recurso foi acessado pela primeira vez por qualquer pessoa na organização. | Verdadeiro, Falso |
| **Recurso acessado incomuns no locatário**<br>*(ResourceUncommonlyAccessedInTenant)* | 180 | O recurso não é normalmente acessado na organização. | Verdadeiro, Falso |
| 

#### <a name="miscellaneous"></a>Diversos

| Nome do enriquecimento | [Linha de base](#baseline-explained) (dias) | Descrição | Valor de exemplo |
| --- | --- | --- | --- |
| **Última vez que o usuário realizou a ação**<br>*(LastTimeUserPerformedAction)* | 180 | Última vez que o usuário realizou a mesma ação. | <Timestamp> |
| **Uma ação semelhante não foi executada no passado**<br>*(SimilarActionWasn'tPerformedInThePast)* | 30 | Nenhuma ação no mesmo provedor de recursos foi executada pelo usuário. | Verdadeiro, Falso |
| **Local do IP de origem**<br>*(SourceIPLocation)* | *N/A* | O país resolvido do IP de origem da ação. | [Surrey, Inglaterra] |
| **Alto volume de operações incomum**<br>*(UncommonHighVolumeOfOperations)* | 7 | Um usuário realizou uma intermitência de operações semelhantes dentro do mesmo provedor | Verdadeiro, Falso |
| **Número incomum de falhas de acesso condicional do Azure AD**<br>*(UnusualNumberOfAADConditionalAccessFailures)* | 5 | Um número incomum de usuários não pôde se autenticar devido ao acesso condicional | Verdadeiro, Falso |
| **Número incomum de dispositivos adicionados**<br>*(UnusualNumberOfDevicesAdded)* | 5 | Um usuário adicionou um número incomum de dispositivos. | Verdadeiro, Falso |
| **Número incomum de dispositivos excluídos**<br>*(UnusualNumberOfDevicesDeleted)* | 5 | Um usuário excluiu um número incomum de dispositivos. | Verdadeiro, Falso |
| **Número incomum de usuários adicionados ao grupo**<br>*(UnusualNumberOfUsersAddedToGroup)* | 5 | Um usuário adicionou um número incomum de usuários a um grupo. | Verdadeiro, Falso |
|