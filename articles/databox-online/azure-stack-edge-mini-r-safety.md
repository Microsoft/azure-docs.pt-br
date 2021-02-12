---
title: Guia de segurança do Edge Azure Stack mini R | Microsoft Docs
description: Descreve as convenções de segurança, as diretrizes, as considerações e explica como instalar e operar com segurança seu dispositivo de Azure Stack Edge mini R.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 02/08/2021
ms.author: alkohli
ms.openlocfilehash: aa363b1eeddff6c3b10d8e36371becb8b690697c
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2021
ms.locfileid: "99980994"
---
# <a name="azure-stack-edge-mini-r-safety-instructions"></a>Instruções de segurança do Edge Azure Stack mini R

![Ícone de aviso 1 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
 ![ ler ícone ](./media/azure-stack-edge-mini-r-safety/icon-safety-read-all-instructions.png)
 **de aviso de segurança ler informações de segurança e integridade**

Leia todas as informações de segurança neste artigo antes de usar o Azure Stack dispositivo mini R de borda, uma composição de um pacote de bateria, uma fonte de alimentação conectada por AC/DC, um adaptador de energia de módulo e um módulo de servidor. A falha em seguir as instruções pode resultar em incêndios, choques elétricos, ferimentos ou danos às suas propriedades. Leia todas as informações de segurança abaixo antes de usar Azure Stack o mini R de borda.

## <a name="safety-icon-conventions"></a>Convenções de ícones de segurança

As seguintes palavras de sinal para sinais de alerta de risco são:

| ícone | Descrição |
|:--- |:--- |
| ![Símbolo de perigo](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)| **Perigo:** Indica uma situação perigosa que, se não for evitada, resultará em morte ou ferimentos sério. <br> **AVISO:** Indica uma situação perigosa que, se não for evitada, pode resultar em morte ou ferimentos grave. <br> **Cuidado:** Indica uma situação perigosa que, se não for evitada, pode resultar em ferimentos secundário ou moderado.|
|

Os seguintes ícones de risco devem ser observados durante a configuração e execução do dispositivo de borda do Azure Stack Edge:

| ícone | Descrição |
|:--- |:--- |
| ![Leia antes todas as instruções](./media/azure-stack-edge-mini-r-safety/icon-safety-read-all-instructions.png) | Leia antes todas as instruções |
| ![Ícone de observação](./media/azure-stack-edge-mini-r-safety/icon-safety-notice.png) **OBSERVAÇÃO:** | Indica informações consideradas importantes, mas não são relacionadas a riscos. |
| ![Símbolo de perigo](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) | Símbolo de perigo |
| ![Ícone de choque elétrico](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png) | Risco de choque elétrico |
| ![Somente uso interno](./media/azure-stack-edge-mini-r-safety/icon-safety-indoor-use-only.png) | Somente uso interno |
| ![Ícone de Sem peças cuja manutenção possa ser feita pelo usuário](./media/azure-stack-edge-mini-r-safety/icon-safety-do-not-access.png) | Nenhuma parte do usuário que seja atendida. Não acesse a menos que seja devidamente treinado. |
|

## <a name="handling-precautions-and-site-selection"></a>Tratamento de precauções e seleção de site

O dispositivo de borda Azure Stack mini R tem as seguintes precauções de tratamento e critérios de seleção de site:

![Ícone de aviso 2 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
 ![ ícone de choque elétrico ](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png)
 ![ sem usuários ícone de partes com serviço de peças ](./media/azure-stack-edge-mini-r-safety/icon-safety-do-not-access.png) **cuidado:**

* Inspecione o dispositivo *da forma como foi recebido* para ver se há danos. Se o compartimento do dispositivo estiver danificado, [entre em contato com suporte da Microsoft](azure-stack-edge-placeholder.md) para obter uma substituição. Não tente operar o dispositivo.
* Se você suspeitar de que o dispositivo está com problemas de funcionamento, [entre em contato com suporte da Microsoft](azure-stack-edge-placeholder.md) para obter uma substituição. Não tente reparar o dispositivo.
* O dispositivo não contém peças operadas pelo usuário. Níveis de tensão, corrente e energia de risco no aparelho. Não abra. Devolva o dispositivo para a Microsoft para manutenção.

![Ícone de aviso 3 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **cuidado:**

É recomendável operar o sistema:

* Longe de fontes de calor, incluindo luz de sol direto e radiadores.
* Em locais não expostos a umidade ou chuva.
* Localizada em um espaço que minimiza a vibração e choque físico.  O sistema foi projetado para choques e vibração de acordo com MIL-STD-810G.
* Isolado de campos eletromagnéticos fortes produzidos por dispositivos elétricos.
* Não permita que nenhum Liquid ou qualquer objeto estrangeiro entre no sistema. Não coloque bebidas ou outros contêineres líquidos no sistema ou perto dele.

![Ícone de aviso 4 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
 ![ ícone de partes sem serviço de usuário ](./media/azure-stack-edge-mini-r-safety/icon-safety-do-not-access.png) **cuidado:**

* Este equipamento contém uma bateria de lítio. Não tente atender ao pacote de bateria. As baterias neste equipamento não são atendidas pelo usuário. Risco de explosão se a bateria for substituída por um tipo incorreto.

![Ícone de aviso 5 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **cuidado:**

Somente cobrar o pacote de bateria quando ele fizer parte do dispositivo do Azure Stack Edge mini R, não cobrar como um dispositivo separado.

![Ícone de aviso 6 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **cuidado:**

* A opção ligar/desligar no pacote de bateria é descarregando a bateria apenas para o módulo de servidor. Se o adaptador de energia estiver conectado ao pacote de bateria, a energia será passada para o módulo de servidor, mesmo se a opção estiver na posição desligado.

![Ícone de aviso 7 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **cuidado:**

* Não grave ou faça um circuito curto do pacote de bateria. Ele deve ser reciclado ou descartado corretamente.

![Ícone de aviso 8 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **cuidado:**

* Em vez de usar a fonte de alimentação AC/DC fornecida, esse sistema também tem a opção de usar um campo fornecido com o tipo 2590 de bateria. Nesse caso, o usuário final deve verificar se ele atende a todas as normas de segurança, transporte, ambiente e qualquer outra legislação nacional/local aplicável.
* Ao operar o sistema com a bateria do tipo 2590, opere a bateria dentro das condições de uso especificadas pelo fabricante da bateria.

![Ícone de aviso 9 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **cuidado:**

Este dispositivo tem duas portas SFP +, que podem ser usadas com transceptores ópticos. Para evitar a radiação de laser perigosa, use somente com transceptores de classe 1.

## <a name="electrical-precautions"></a>Cuidados elétricos

O dispositivo mini R do Azure Stack Edge tem as seguintes precauções elétricas:

![Ícone de aviso 10 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) ![ ícone de choque elétrico ](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png) **AVISO:**

Quando usado com o adaptador de fonte de energia:

* Forneça uma conexão de aterramento elétrico segura para os cabos de alimentação. O cabo corrente alternado (AC) tem um plugue de aterramento de três fios (um plug que tem um pino de aterramento). Esse plugue se encaixa apenas em uma tomada CA com aterramento. Não ignore o objetivo do pino de aterramento.
* Considerando que o plugue no cabo de alimentação é o dispositivo de desconexão principal, verifique se as saídas de soquete estão localizadas perto do dispositivo e são de fácil acesso.
* Desconecte os cabos de alimentação (puxando o plug-in, não o cabo) e desconecte todos os cabos se alguma das seguintes condições existir:

  * O cabo de alimentação ou o plugue ficou desgastado ou está danificado.
  * O dispositivo é exposto a chuva, excesso de umidade ou outros liquids.
  * O dispositivo foi descartado e a embalagem do dispositivo foi danificada.
  * Você acha que o dispositivo precisa de reparos.
* Desconecte permanentemente a unidade antes de movê-la ou se achar que ela foi danificada de alguma forma.

* Forneça uma fonte de alimentação adequada com proteção contra sobrecarga elétrica para atender às seguintes especificações de energia:

* Tensão: 100-240 volts AC
* Atual: 1,7 amperes
* Frequência: 50 a 60 Hz

![Ícone de aviso 11 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
 ![ ícone de choque elétrico ](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png) **AVISO:**

* Não tente modificar ou usar cabos de energia CA diferentes daqueles fornecidos com o equipamento.

![Ícone de aviso 12 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
 ![ ícone de choque elétrico, ](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png)
 ![ Use somente o aviso de uso interno ](./media/azure-stack-edge-mini-r-safety/icon-safety-indoor-use-only.png) **:**

* A fonte de energia rotulada com este símbolo é classificada apenas para uso interno.

## <a name="regulatory-information"></a>Informações normativas

O seguinte contém informações regulatórias para Azure Stack dispositivo mini R de borda, número do modelo normativo: TMA01.

Azure Stack o dispositivo Microsoft Edge mini R foi projetado para uso com NRTL listado (UL, CSA, ETL, etc.) e IEC/EN 60950-1 ou IEC/EN 62368-1 em conformidade com o equipamento de tecnologia da informação (CE marcado).

Em outros países além dos EUA e do Canadá, os cabos de rede (não fornecidos com o equipamento) não deverão ser instalados com este equipamento se seu comprimento for maior que 3 metros.

O equipamento foi projetado para operar nos seguintes ambientes:

| Ambiente | Especificações |
|:---  |:--- |
| Especificações de temperatura do sistema | <ul><li>Temperatura de armazenamento: – 20 &deg; c – 50 &deg; c (– 4 &deg; f-122 &deg; f)</li><li>Operação contínua: 0 &deg; c – 40 &deg; C (32 &deg; F – 104 &deg; F)</li></ul> |
| Especificações relativas à umidade (RH) | <ul><li>Armazenamento: 5% a 95% de umidade relativa</li><li>Operação: 10% a 90% de umidade relativa</li></ul>|
| Especificações máximas de altitude | <ul><li>Operação: 15.000 pés (4.572 metros)</li><li>Não operacional: 40.000 pés (12.192 metros)</li></ul>|

> ![Ícone de aviso-2 ](./media/azure-stack-edge-mini-r-safety/icon-safety-notice.png) **AVISO:** &nbsp; alterações ou modificações feitas no equipamento não expressamente aprovado pela Microsoft podem anular a autoridade do usuário para operar o equipamento.

#### <a name="canada-and-usa"></a>CANADÁ e EUA:

> ![Ícone de aviso-3 ](./media/azure-stack-edge-mini-r-safety/icon-safety-notice.png) **AVISO:** &nbsp; este equipamento foi testado e foi encontrado para estar em conformidade com os limites de um dispositivo digital de classe a, de acordo com a parte 15 das regras de FCC. Esses limites são projetados para fornecer uma proteção razoável contra interferência prejudicial quando o equipamento é operado em um ambiente comercial. O equipamento gera, usa e pode irradiar energia de radiofrequência. Se não for instalado e usado de acordo com o manual de instruções, poderá causar interferências prejudiciais às comunicações de rádio. A operação deste equipamento em uma área residencial provavelmente causará interferência prejudicial, caso em que o usuário será solicitado a corrigir a interferência por sua própria despesa.

O adaptador USB Netgear A6150 WiFi fornecido com este equipamento deve ser operado próximo ao corpo humano é testado para conformidade com o SAR (taxa de absorção específica) gastado pelo corpo. O limite de SAR definido pela FCC é 1,6 W/kg quando a média é superior a 1 g de tecido. Ao carregar o produto ou usá-lo enquanto estiver gasto em seu corpo, mantenha uma distância de 10 mm do corpo para garantir a conformidade com os requisitos de exposição de RF.

O adaptador USB Netgear A6150 WiFi está em conformidade com ANSI/IEEE C 95.1-1999 e foi testado de acordo com os métodos de medição e os procedimentos especificados no Boletim OET 65 suplemento C.

Taxa de absorção específica de Netgear A6150 (SAR): 1,18 W/kg com média de 1 g de tecido

O adaptador USB Netgear A6150 WiFi deve ser usado somente com antenas aprovadas. Este dispositivo e suas antenas não devem estar colocalizados ou operando em conjunto com qualquer outra antena ou transmissor, exceto de acordo com os procedimentos de produto de multitransmissor da FCC. Para produtos disponíveis no mercado dos EUA, somente o canal 1 ~ 11 pode ser operado. A seleção de outros canais não é possível.

A operação na banda 5150 – 5250 MHz é apenas para uso interno, a fim de reduzir o potencial de interferência prejudicial para sistemas de satélite para dispositivos móveis de cocanales.

![Aviso de informações regulatórias – uso interno](./media/azure-stack-edge-mini-r-safety/regulatory-information-indoor-use-only.png)


Os usuários são avisados de que os radares de alta potência são alocados como usuários primários (usuários prioritários) das bandas 5250 – 5350 MHz e 5650 – 5850 MHz, e esses radars podem causar interferência e/ou danos aos dispositivos LE-LAN.

Este equipamento gera, usa e pode radiar energia de frequência de rádio e, se não estiver instalado e usado de acordo com as instruções, poderá causar interferência prejudicial às comunicações de rádio. No entanto, não há nenhuma garantia de que a interferência não ocorrerá em uma instalação específica.

Se este equipamento causar interferência prejudicial à recepção de rádio ou televisão, o que pode ser determinado ao desligar e ligar o equipamento, o usuário é incentivado a tentar corrigir a interferência por uma ou mais das seguintes medidas:

- Reorientar ou realocar a antena de recebimento.
- Aumente a separação entre o equipamento e o receptor.
- Conecte o equipamento a uma tomada em um circuito diferente daquele ao qual o receptor está conectado.
- Consulte o revendedor ou um técnico de rádio/TV experiente para obter ajuda.

Para obter mais informações sobre problemas de interferência, acesse o site da FCC em [fcc.gov/cgb/consumerfacts/interference.html](https://www.fcc.gov/consumers/guides/interference-radio-tv-and-telephone-signals). Você também pode chamar a FCC às 1-888 – chamar a FCC para solicitar interferência e folhas de fatos de interferência de telefone.

Informações adicionais sobre a segurança do radiofrequency podem ser encontradas no site da FCC no [https://www.fcc.gov/general/radio-frequency-safety-0](https://www.fcc.gov/general/radio-frequency-safety-0) e no site do Canadá do setor em [http://www.ic.gc.ca/eic/site/smt-gst.nsf/eng/sf01904.html](http://www.ic.gc.ca/eic/site/smt-gst.nsf/eng/sf01904.html) .

Este produto demonstrou a conformidade da EMC em condições que incluíam o uso de dispositivos periféricos compatíveis e cabos blindados entre os componentes do sistema. É importante que você use dispositivos de periféricos compatíveis e cabos blindados entre os componentes do sistema para reduzir a possibilidade de causar interferências a rádios, conjuntos de televisão e outros dispositivos eletrônicos.

O dispositivo está em conformidade com a parte 15 das Regras da FCC e normas RSS isentas de licença do Canadá. A operação está sujeita a estas duas condições: (1) o dispositivo não pode causar interferência prejudicial e (2) o dispositivo deve aceitar todas as interferências recebidas, inclusive interferências que possam causar uma operação indesejada do dispositivo.

![Aviso de informações regulatórias 1](./media/azure-stack-edge-mini-r-safety/regulatory-information-1.png)

CAN ICES-3(A)/NMB-3(A)

Microsoft Corporation, One Microsoft Way, Redmond, WA 98052, EUA

Estados Unidos: (800) 426-9400

Canadá: (800) 933-4750

Adaptador USB do Netgear A6150 WiFi ID do FCC: PY318300429
 
Adaptador USB Netgear A6150 WiFi ID IC: 4054A-18300429

O adaptador USB Netgear A6150 WiFi fornecido com este equipamento é compatível com o SAR para limites gerais de exposição de população/não controlado em IC RSS-102 e foi testado de acordo com os métodos de medição e os procedimentos especificados no IEEE 1528. Mantenha pelo menos a distância de 10 mm para a condição de corpo gasto.

O adaptador USB Netgear A6150 WiFi está em conformidade com o limite de exposição de RF portátil do Canadá definido para um ambiente não controlado e é seguro para a operação pretendida, conforme descrito em seu manual. É possível obter mais redução de exposição de RF ao manter o produto o mais longe possível do seu corpo ou definindo o dispositivo com uma potência de saída menor se essa função estiver disponível.

Uma tabela com a taxa de absorção específica (SAR) média acima de 1 g para cada produto pode ser vista na seção EUA acima.

![Aviso de informações regulatórias 2](./media/azure-stack-edge-mini-r-safety/regulatory-information-2.png)

#### <a name="european-union"></a>UNIÃO EUROPEIA:

Solicite uma cópia da declaração de conformidade da UE para este equipamento.

O adaptador USB Netgear A6150 WiFi fornecido com este equipamento está em conformidade com a diretiva 2014/53/EU e também pode ser fornecido na solicitação.

> ![Ícone de aviso 13 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) este é um produto de classe a. Em um ambiente doméstico, o produto pode causar interferências de rádio. Nesse caso, o usuário talvez tenha que tomar as devidas medidas.

Descarte de baterias e equipamentos eletroeletrônicos:

![Ícone de aviso 14](./media/azure-stack-edge-mini-r-safety/icon-ewaste-disposal.png)

Este símbolo no produto, nas baterias ou embalagem significa que o produto e as baterias contidas não devem ser descartadas no lixo comum. É sua responsabilidade levá-los a um ponto de coleta para reciclagem de baterias e equipamentos eletroeletrônicos. Essa coleta e reciclagem separadas ajudarão a conservar os recursos naturais e impedir possíveis consequências negativas para a saúde humana e o meio ambiente devido à possível existência de substâncias perigosas nas baterias e equipamentos eletroeletrônicos, que podem ser causadas pelo descarte inadequado. Para obter mais informações sobre onde entregar suas baterias e equipamentos eletroeletrônicos, entre em contato com a Prefeitura, com o serviço de coleta seletiva ou a loja que vendeu o produto. Entre em contato com erecycle@microsoft.com para obter mais informações sobre a WEEE.

Este produto contém baterias em forma de moeda.

O adaptador USB Netgear A6150 WiFi fornecido com este equipamento deve ser operado próximo ao corpo humano e é testado para conformidade com o SAR (taxa de absorção específica) gastado pelo corpo (veja os valores abaixo). Ao carregar o produto ou usá-lo enquanto estiver gasto em seu corpo, mantenha uma distância de 10mm do corpo para garantir a conformidade com os requisitos de exposição de RF.

**Taxa de absorção específica de Netgear A6150 (SAR):** 0,54 W/kg com média em relação ao 10g de tecido

 
Este dispositivo pode operar em todos os Estados membros da UE. Observe os regulamentos nacionais e locais em que o dispositivo é usado. Este dispositivo é restrito a uso interno somente ao operar no intervalo de frequência de 5150-5350 MHz nos seguintes países:  

![Países da UE que exigem somente uso interno](./media/azure-stack-edge-mini-r-safety/mini-r-safety-eu-indoor-use-only.png)

De acordo com o artigo 10.8 (a) e 10.8 (b) do vermelho, a tabela a seguir fornece informações sobre as faixas de frequência usadas e a potência máxima de transmissão RF de produtos sem fio de Netgear para venda na UE:

**Fio**

| Intervalo de frequência (MHz) | Canais usados | Potência de transmissão máxima (dBm/mW) |
| --------------------- | ------------- | --------------------------- |
| 2400-2483.5 | 1-13    | ODFM: 19,9 dBm (97,7 mW) <br> CCK: 17,9 dBm (61,7 mW) |
| 5150-5320   | 36-48   | 22,9 dBm (195 mW) |
| 5250-5350   | 52-64   | 22,9 dBm (195 mW) com TPC <br> 19,9 dBm (97,7 mW) não-TPC |
| 5470-5725   | 100-140 | 29,9 dBm (977 mW) com TPC <br> 29,6 dBm (490 mW) não-TPC |

Microsoft Ireland Sandyford Ind Est Dublin D18 KX32 IRL

Número de telefone: +353 1 295 3826

Número de fax: +353 1 706 4110

#### <a name="singapore"></a>Cingapura:

O adaptador USB Netgear A6150 WiFi fornecido com este equipamento está em conformidade com os padrões de IMDA.


## <a name="next-steps"></a>Próximas etapas

- [Preparar para implantar o Azure Stack Edge mini R](azure-stack-edge-mini-r-deploy-prep.md)
