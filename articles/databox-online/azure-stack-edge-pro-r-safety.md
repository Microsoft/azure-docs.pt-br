---
title: Guia de segurança do Azure Stack Edge pro R | Microsoft Docs
description: Descreve as convenções de segurança, diretrizes, considerações e explica como instalar e operar com segurança seu dispositivo pro R Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: alkohli
ms.openlocfilehash: dacc9ecc28ffa482b60d1e48735fe3620b5b7558
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100363059"
---
# <a name="azure-stack-edge-pro-r-safety-instructions"></a>Instruções de segurança do Azure Stack Edge pro R

![Ícone de aviso](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
![Leia o ícone de aviso de segurança](./media/azure-stack-edge-pro-r-safety/icon-safety-read-all-instructions.png)
**LEIA AS INFORMAÇÕES DE SEGURANÇA E INTEGRIDADE**

Leia todas as informações de segurança neste artigo antes de usar o dispositivo pro R Azure Stack Edge. A falha em seguir as instruções pode resultar em incêndios, choques elétricos, ferimentos ou danos às suas propriedades. Leia todas as informações de segurança abaixo antes de usar o Azure Stack Edge pro R.

## <a name="safety-icon-conventions"></a>Convenções de ícones de segurança

As seguintes palavras de sinal para sinais de alerta de risco são:

| ícone | Descrição |
|:--- |:--- |
| ![Símbolo de perigo](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)| **Perigo:** Indica uma situação perigosa que, se não for evitada, resultará em morte ou ferimentos sério. <br> **AVISO:** Indica uma situação perigosa que, se não for evitada, pode resultar em morte ou ferimentos grave. <br> **Cuidado:** Indica uma situação perigosa que, se não for evitada, pode resultar em ferimentos secundário ou moderado.|
|

Os seguintes ícones de risco devem ser observados ao configurar e executar o dispositivo de borda do Azure Stack Edge pro R:

| ícone | Descrição |
|:--- |:--- |
| ![Leia antes todas as instruções](./media/azure-stack-edge-pro-r-safety/icon-safety-read-all-instructions.png) | Leia antes todas as instruções |
| ![Ícone de observação](./media/azure-stack-edge-mini-r-safety/icon-safety-notice.png) **OBSERVAÇÃO:** | Indica informações consideradas importantes, mas não são relacionadas a riscos. || ![Símbolo de perigo](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) | Símbolo de perigo |
| ![Ícone de assistente de dica](./media/azure-stack-edge-pro-r-safety/icon-safety-tip-hazard.png)  | Assistente de dica|
| ![Ícone de peso](./media/azure-stack-edge-pro-r-safety/icon-safety-heavy-weight.png)  | Risco de peso pesado|
| ![Ícone de choque elétrico](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) | Risco de choque elétrico |
| ![Ícone de Sem peças cuja manutenção possa ser feita pelo usuário](./media/azure-stack-edge-pro-r-safety/icon-safety-do-not-access.png) | Nenhuma parte do usuário que seja atendida. Não acesse a menos que seja devidamente treinado. |
| ![Ícone de várias fontes de alimentação](./media/azure-stack-edge-pro-r-safety/icon-safety-disconnect-all-power.png)  | Várias fontes de energia. Desconecte todos os cabos de alimentação para remover toda a energia do equipamento. |
| ![Ícone de pontos de pinçagem](./media/azure-stack-edge-pro-r-safety/icon-pinching-points.png)  | Pontos de pinçagem estão presentes. |
| ![Ícone de componentes dinâmicos ou superfícies](./media/azure-stack-edge-pro-r-safety/icon-hot-component-surface.png)  | Indica componentes ou superfícies quentes. |
|

## <a name="handling-precautions-and-site-selection"></a>Tratamento de precauções e seleção de site

![Ícone de Aviso](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) **AVISO:**

* Equipamentos adequados (por exemplo, a tomada de palete) e o PPE (equipamento de proteção pessoal), por exemplo, luvas devem ser usados ao mover e manipular o dispositivo que foi enviado.

![Ícone de aviso ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)![ ícone de risco de gorjeta ](./media/azure-stack-edge-pro-r-safety/icon-safety-tip-hazard.png) **AVISO:**

* Coloque o equipamento em uma superfície plana, sólida e estável para evitar uma potencial dica ou risco Crushing.
* Não empilhe mais de 2 dispositivos entre si.
* Verifique se o sistema está protegido antes de empilhar.
* Não realoque nem mova dispositivos empilhados.
* Não empilhe os dispositivos que são energizados com cabos externos.
* Verifique se os cabos de energia não estão Crushed ou danificados durante as operações de empilhamento.
* Os dispositivos não devem ser usados como tabelas ou espaços de trabalho.

![Ícone de aviso ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
 ![ ícone de choque elétrico ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png)
 ![ sem serviço usuários ícone de peças ](./media/azure-stack-edge-pro-r-safety/icon-safety-do-not-access.png) **cuidado:**

* Inspecione o dispositivo *da forma como foi recebido* para ver se há danos. Se o compartimento do dispositivo estiver danificado, [entre em contato com suporte da Microsoft](azure-stack-edge-contact-microsoft-support.md) para obter uma substituição. Não tente operar o dispositivo.
* Se você suspeitar de que o dispositivo está com problemas de funcionamento, [entre em contato com suporte da Microsoft](azure-stack-edge-contact-microsoft-support.md) para obter uma substituição. Não tente reparar o dispositivo.
* O dispositivo não contém peças operadas pelo usuário. Níveis de tensão, corrente e energia de risco no aparelho. Não abra. Devolva o dispositivo para a Microsoft para manutenção.

![Ícone de aviso ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) ![ ícone de peso pesado ](./media/azure-stack-edge-pro-r-safety/icon-safety-heavy-weight.png) **AVISO:**

* A remoção do módulo de fonte de alimentação do UPS expõe as peças energizadas dentro do no-break. Não insira objetos estrangeiros dentro do compartimento do módulo de fonte de alimentação.
* Não tente levantar um dispositivo de borda pro R Azure Stack Edge por conta própria. Um compartimento pode se ponderar entre 52 kg e 93 kg (115 lbs e 205 lbs); Use a assistência mecânica ou outra assistência adequada ao mover e levantar equipamentos. Conformidade com os requisitos de integridade e segurança de ocupação local ao mover e levantar equipamentos.
* Não tente levantar o equipamento sem um auxílio mecânico adequado. Lembre-se de que tentar levantar esse peso pode causar sérios ferimentos.

![Ícone de Aviso](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)  **AVISO:**

* O sistema foi projetado para operar em um ambiente controlado. Escolha um site que seja:
  * Ventilated e longe de fontes de calor, incluindo luz de sol direta e radiadores.
  * Não exposto a umidade ou chuva.
  * Localizada em um espaço que minimiza a vibração e choque físico.  O sistema foi projetado para choques e vibração de acordo com MIL-STD-810G.
  * Isolado de campos eletromagnéticos fortes produzidos por dispositivos elétricos.
  * Fornecido com saídas com aterramento adequado.
  * Fornecido com espaço adequado para acessar os cabos de fonte de alimentação, porque eles funcionam como a principal desconexão de energia do produto.
* Os cabos Ethernet não são fornecidos com o produto. Para reduzir a interferência eletromagnética, é recomendável que o cabo de STP (par trançado) Cat 6 blindado seja usado.
* Configurar o equipamento em uma área de trabalho que permite a circulação de ar adequada em todo o equipamento; Verifique se as tampas frontal e traseira estão completamente removidas enquanto o dispositivo está em execução.
* Os cabos Ethernet não são fornecidos com o produto. Para reduzir a interferência eletromagnética, é recomendável que o cabo CAT 6 blindado (STP) seja usado.
* Instale o equipamento na área controlada por temperatura sem contaminants de conduta e permita a circulação de ar adequada em todo o equipamento.
* Mantenha o equipamento longe de fontes de ambientes líquidos e Humid excessivamente.
* Não permita que nenhum Liquid ou qualquer objeto estrangeiro entre no sistema. Não coloque bebidas ou outros contêineres líquidos no sistema ou perto dele.

## <a name="heater-precautions"></a>Precauções do aquecedor

![Ícone de aviso- ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) ![ componentes ativos ou superfícies ícone 1 ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) **AVISO:** 

* A operação de aquecedor automática enquanto o sistema está ligado pode criar um risco de toque devido a temperaturas de superfície alta na cobertura do conjunto de aquecedor. Não toque nessa superfície enquanto o sistema estiver ligado. Permitir um período resfriado de 10 minutos após a desligamento do sistema.

![Ícone de aviso de ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) ![ pontos de pinçamento ícone 1 ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) **AVISO:** 

* Quando o sistema estiver ligado, a actuation automática da porta plenum traseira poderá criar um risco de ponto de pinçagem. Mantenha as mãos claras dessa área quando o sistema estiver ligado.

## <a name="electrical-precautions"></a>Cuidados elétricos

![Ícone de aviso ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) ![ ícone de choque elétrico ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) **AVISO:**

* Forneça uma conexão de aterramento elétrico segura para os cabos de alimentação. O cabo corrente alternado (AC) tem um plugue de aterramento de três fios (um plug que tem um pino de aterramento). Esse plugue se encaixa apenas em uma tomada CA com aterramento. Não ignore o objetivo do pino de aterramento.
* Considerando que o plugue no cabo de alimentação é o dispositivo de desconexão principal, verifique se as saídas de soquete estão localizadas perto do dispositivo e são de fácil acesso.
* Desconecte os cabos de alimentação (puxando o plug-in, não o cabo) e desconecte todos os cabos se alguma das seguintes condições existir:

  * O cabo de alimentação ou o plugue ficou desgastado ou está danificado.
  * Você despejou algo no invólucro do dispositivo.
  * O dispositivo foi exposto a chuva ou umidade em excesso.
  * O dispositivo caiu e o invólucro está danificado.
  * Você acha que o dispositivo precisa de reparos.
* Desconecte permanentemente a unidade antes de movê-la ou se achar que ela foi danificada de alguma forma.
* Para evitar a alta perda atual, quando um único caso de trânsito tem mais de uma fonte de alimentação ininterrupta (UPS), é recomendável que cada UPS esteja conectado a um circuito de ramificação independente. No entanto, no caso de uma PDU (unidade de distribuição de energia) ou outro dispositivo ser usado quando o aterramento de cada UPS se basear em um único condutor de aterramento da PDU, o terminal de aterramento no exterior de cada UPS também deverá ser usado com um condutor de edifício de construção complementar.

  > [!NOTE]
  > Se for usada uma PDU que já tenha um condutor de aterramento complementar, não será necessário usar o terminal de aterramento adicional no no-break.

* Forneça uma fonte de alimentação adequada com proteção contra sobrecarga elétrica para atender às seguintes especificações de energia:

  * Tensão: 100 a 240 volts AC
  * Atual: 20 A, máximo por cabo de alimentação. Os cabos de energia são fornecidos.
  * Frequência: 50 a 60 Hz

![Ícone de aviso ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
 ![ ícone de choque elétrico ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png)
 ![ várias fontes de energia ](./media/azure-stack-edge-pro-r-safety/icon-safety-disconnect-all-power.png) **AVISO:**

* Para sistemas sem uma fonte de alimentação ininterrupta (UPS), desconecte todos os cabos de alimentação CA para remover completamente a energia AC do equipamento.
* Para sistemas com UPS, desconecte todos os cabos de alimentação CA e use o interruptor de energia UPS para desenergizar o sistema. O UPS contém tensões perigosas de AC e DC.
* Se um sistema incluir um no-break, o UPS foi fornecido com um cabo de alimentação de entrada blindado. Você deve usar o cabo de alimentação de entrada blindado, não substitua ou modifique o cabo.

![Ícone de aviso ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
 ![ ícone de choque elétrico ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) **AVISO:**

* Para sistemas equipados com bateria, a tensão AC e/ou DC sempre envolverá um risco potencial de tensão de CA na saída de UPS gerado por meio de baterias ou utilitário. Para evitar danos ao equipamento ou ferimentos pessoais, sempre presuma que pode haver tensão na saída de UPS, mesmo quando desconectado da fonte de alimentação primária.
* Para sistemas equipados com UPS, todas as conexões externas energizadas no UPS são fêmea. Não remova o caso ou insira nada nesse ou em nenhum conector no UPS.

![Ícone de aviso ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
 ![ ícone de choque elétrico ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) **AVISO:**

* Não tente modificar ou usar cabos de energia CA diferentes daqueles fornecidos com o equipamento.

![Ícone de aviso ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
 ![ sem usuário ícone de partes com serviço ](./media/azure-stack-edge-pro-r-safety/icon-safety-do-not-access.png) **cuidado:**

* Este equipamento contém baterias de lítio de cunha e/ou bateria phosphate de ferro. Não tente atender ao equipamento. As baterias neste equipamento não são atendidas pelo usuário. Risco de explosão se a bateria for substituída por um tipo incorreto.
* A remoção do módulo de bateria do UPS expõe as peças energizadas dentro do no-break. Não insira objetos estrangeiros dentro do compartimento de módulo da bateria.

## <a name="regulatory-information"></a>Informações normativas

Esta seção contém informações regulatórias para Azure Stack dispositivo pro R de borda, número do modelo normativo: Azure Stack Edge pro R.

O dispositivo de borda do Azure Stack Edge pro R foi projetado para uso com NRTL listados (UL, CSA, ETL, etc.) e IEC/EN 60950-1 ou IEC/EN 62368-1 em conformidade com o equipamento de tecnologia da informação (CE marcado).

O dispositivo foi projetado para operar nos seguintes ambientes:

| Ambiente | Especificações |
|:--- |:--- |
|Especificações de temperatura | <ul><li>Temperatura de armazenamento: – 33 &deg; c – 63 &deg; c (– 28 &deg; f-145 &deg; f) </li><li>Operação contínua: 5 &deg; c – 43 &deg; c (41 &deg; F – 110 &deg; F)</li><li>Gradiente de temperatura máximo (operacional e armazenamento): 20 &deg; C/h (68 &deg; F/h)</li></ul> |
|Especificações de umidade relativas | <ul><li>Armazenamento: 5% a 95% RH com 33 &deg; C (91 &deg; F) orvalho de ponto máximo. A atmosfera deve ser sem condensação em todos os momentos.</li><li>Operação: 5% a 85% de umidade relativa com 29 &deg; C (84,2 &deg; F) orvalho máximo de pontos de extremidade</li></ul> |
| Especificações máximas de altitude | <ul><li>Operacional (sem UPS): 15.000 pés (4.572 metros)</li><li>Operacional (com UPS): 10.000 pés (3.048 metros)</li><li>Armazenamento: 40.000 pés (12.192 metros)</li></ul> |

<!--|Standard operating temperature specifications | <ul>Continuous operation (for altitude less than 950 m or 3117 ft): +5&deg;C–45&deg;C (41&deg;F–113&deg;F) with no direct sunlight on the equipment</ui>
|Expanded operating temperature specifications | <ul><li>Continuous operation: 5&deg;C to 45&deg;C at 5% to 85% RH with 29&deg;C dew point.</li><li></= 1% of annual operating hours: –5&deg;C to 55&deg;C at 5% to 90% RH with 29&deg;C dew point. |
| Expanded operating temperature restrictions | <ul><li>Do not perform cold start below -15&deg;C according to IEC 60945.</li><li>The operating temperature specified is for a maximum altitude of 950 meters.</li></ul> |
| Gaseous contamination specifications | <ul><li>Copper coupon corrosion rate: < 300 &Aring;/month per Class G1 as defined by ANSI/ISA71.04-1985.</li><li>Silver coupon corrosion rate: < 200 &Aring;/month as defined by AHSRAE TC9.9.</li></ul>|

> [!NOTE]
> Outside the standard operating temperature (10&deg;C to 35&deg;C):
> - The system can operate continuously in temperatures as low as 5&deg;C and as high as 45&deg;C. For temperatures between 35&deg;C and 45&deg;C, de-rate maximum allowable temperature by 1&deg;C per 175 m above 950 m (1&deg;F per 319 ft).
> - The system can operate down to –5&deg;C or up to 55&deg;C for a maximum of 1% of its annual operating hours. For temperatures between 45&deg;C and 55&deg;C, de-rate maximum allowable temperature by 1&deg;C per 125 m above 950 m (1&deg;F per 228 ft).
>
> When operating in the expanded temperature range:
> - System performance may be impacted.
> - Ambient temperature warnings may be reported in the System Event Log.     
>
> Maximum corrosive contaminant levels measured at &lt;/= 50% relative humidity. 


A device that has a UPS installed is designed to operate in the following environments:

| Environment | Specifications |
|:---  |:--- |
|Temperature specifications | <ul><li>Storage temperature: –40&deg;C–70&deg;C (–40&deg;F-158&deg;F) per Mil-Std 810G Method 501.5, Proc 1</li><li>Continuous operation (for altitude less than 950 m or 3117 ft): 5&deg;C–45&deg;C (41&deg;F–113&deg;F), with no direct sunlight on the equipment</li><li>Excursion temperature: 50&deg;C per Mil-Std 810G</li><li>Maximum temperature gradient (operating and storage): 20&deg;C/h (68&deg;F/h)</li></ul> |
|Relative humidity specifications | <ul><li>Storage: 5% to 95% RH with 33&deg;C (91&deg;F) maximum dew point. Atmosphere must be non-condensing at all times.</li><li>Operating: 5% to 85% relative humidity with 29&deg;C (84.2&deg;F) maximum dew point</li></ul>|
|Maximum altitude specifications | <ul><li>Storage: Mil-Std 810G method 500.5, Proc. I, 40,000 ft for 1 hour after stabilization </li><li>Operating: Mil-Std 810G method 500.5, Proc. II, air carriage, 15,000 ft for 1 hour after stabilization</li></ul>|
|Standard operating temperature specifications |<ul><li>Continuous operation (for altitude less than 950 m or 3117 ft)</li><li>+5&deg;C–45&deg;C (41&deg;F–113&deg;F) with no direct sunlight on the equipment</li></ul>|
|Expanded operating temperature specifications | <ul><li>Continuous operation: 5&deg;C to 45&deg;C at 5% to 85% RH with 29&deg;C dew point.</li><li>&lt;/= 1% of annual operating hours: 0&deg;C to 50&deg;C at 5% to 90% RH with 29&deg;C dew point.</li></ul>|
|Expanded operating temperature restrictions | <ul><li>Do not perform cold start below -15C Per IEC 60945</li><li>The operating temperature specified is for a maximum altitude of 950 meters</li></ul> |
|Gaseous contamination specifications | <ul><li>Copper coupon corrosion rate: < 300 &Aring;/month per Class G1 as defined by ANSI/ISA71.04-1985.</li><li>Silver coupon corrosion rate: < 200 &Aring;/month as defined by AHSRAE TC9.9.|-->

<!--
> [!NOTE]
> Outside the standard operating temperature (10&deg;C to 35&deg;C):
> - The system can operate continuously in temperatures as low as 5&deg;C and as high as 45&deg;C. For temperatures between 35&deg;C and 45&deg;C, de-rate maximum allowable temperature by 1&deg;C per 175 m above 950 m (1&deg;F per 319 ft).
> - The system can operate down to 0&deg;C or up to 50&deg;C for a maximum of 1% of its annual operating hours. For temperatures between 45&deg;C and 55&deg;C, de-rate maximum allowable temperature by 1&deg;C per 125 m above 950 m (1&deg;F per 228 ft).
> 
> When operating in the expanded temperature range:
> - System performance may be impacted.
> - Ambient temperature warnings may be reported in the System Event Log.
>
> Maximum corrosive contaminant levels measured at &lt;/= 50% relative humidity. --> 


> ![Ícone de aviso-2 ](./media/azure-stack-edge-pro-r-safety/icon-safety-notice.png) **AVISO:** &nbsp; alterações ou modificações feitas no equipamento não expressamente aprovado pela Microsoft podem anular a autoridade do usuário para operar o equipamento.

#### <a name="canada-and-usa"></a>CANADÁ e EUA:

> ![Ícone de aviso-2 ](./media/azure-stack-edge-pro-r-safety/icon-safety-notice.png) **AVISO:** &nbsp; este equipamento foi testado e foi encontrado para estar em conformidade com os limites de um dispositivo digital de classe a, de acordo com a parte 15 das regras de FCC. Esses limites são projetados para fornecer uma proteção razoável contra interferência prejudicial quando o equipamento é operado em um ambiente comercial. O equipamento gera, usa e pode irradiar energia de radiofrequência. Se não for instalado e usado de acordo com o manual de instruções, poderá causar interferências prejudiciais às comunicações de rádio. A operação deste equipamento em uma área residencial provavelmente causará interferência prejudicial, caso em que o usuário será solicitado a corrigir a interferência por sua própria despesa.

O dispositivo está em conformidade com a parte 15 das Regras da FCC e normas RSS isentas de licença do Canadá. A operação está sujeita a estas duas condições: (1) o dispositivo não pode causar interferência prejudicial e (2) o dispositivo deve aceitar todas as interferências recebidas, inclusive interferências que possam causar uma operação indesejada do dispositivo.

![Aviso de informações regulatórias 1](./media/azure-stack-edge-mini-r-safety/regulatory-information-1.png)


PODE ICES-3 (A)/NMB-3 (A) Microsoft Corporation, One Microsoft Way, Redmond, WA 98052, EUA Estados Unidos: (800) 426-9400 Canada: (800) 933-4750

#### <a name="european-union"></a>UNIÃO EUROPEIA:

Solicite uma cópia da declaração de conformidade da UE. Envie um email para [CSI_Compliance@microsoft.com](mailto:CSI_Compliance@microsoft.com).

![Aviso de ícone de aviso ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) **!**

É um produto classe A. Em um ambiente doméstico, o produto pode causar interferências de rádio. Nesse caso, o usuário talvez tenha que tomar as devidas medidas.

Descarte de baterias e equipamentos eletroeletrônicos:

![Ícone de aviso 14](./media/azure-stack-edge-mini-r-safety/icon-ewaste-disposal.png)

Este símbolo no produto, nas baterias ou embalagem significa que o produto e as baterias contidas não devem ser descartadas no lixo comum. É sua responsabilidade levá-los a um ponto de coleta para reciclagem de baterias e equipamentos eletroeletrônicos. Essa coleta e reciclagem separadas ajudarão a conservar os recursos naturais e impedir possíveis consequências negativas para a saúde humana e o meio ambiente devido à possível existência de substâncias perigosas nas baterias e equipamentos eletroeletrônicos, que podem ser causadas pelo descarte inadequado. Para obter mais informações sobre onde entregar suas baterias e equipamentos eletroeletrônicos, entre em contato com a Prefeitura, com o serviço de coleta seletiva ou a loja que vendeu o produto. Entre em contato com erecycle@microsoft.com para obter mais informações sobre a WEEE.

Este produto contém baterias em forma de moeda.

Microsoft Ireland Sandyford Ind Est Dublin D18 KX32 IRL número de telefone: + 353 1 295 3826 número de fax: + 353 1 706 4110


## <a name="next-steps"></a>Próximas etapas

- [Preparar para implantar a borda pro R do Edge Azure Stack](azure-stack-edge-pro-r-deploy-prep.md)
