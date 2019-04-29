---
title: Segurança para o Azure Data Box | Microsoft Docs
description: Descreve as convenções de segurança, diretrizes e considerações, e explica como instalar e operar com segurança seu Azure Data Box.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 09/24/2018
ms.author: alkohli
ms.openlocfilehash: d0ef3e407fd256b4db351f86b51964cbcb16e819
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60747645"
---
# <a name="safely-install-and-operate-your-azure-data-box"></a>Instalar e operar o Azure Data Box com segurança
![Ícone de aviso](./media/data-box-safety/warning_icon.png)
![Leia o ícone de aviso de segurança](./media/data-box-safety/read_safety_and_health_information_icon.png) **LEIA AS INFORMAÇÕES DE SEGURANÇA E INTEGRIDADE**

Leia todas as informações de segurança neste artigo antes de usar o Azure Data Box. A falha em seguir essas instruções poderá resultar em lesões, choques elétricos, incêndios ou danos materiais.

## <a name="safety-icon-conventions"></a>Convenções de ícones de segurança
Estes são os ícones que você encontrará ao ler as precauções de segurança observadas ao configurar e executar seu Data Box.

| ícone | DESCRIÇÃO |
|:--- |:--- |
| ![Ícone de perigo](./media/data-box-safety/warning_icon.png) **PERIGO!** |Indica uma situação perigosa que, se não for evitada, resultará em morte ou lesões graves. Essa palavra limitas-e às situações mais graves. |
| ![Ícone de aviso](./media/data-box-safety/warning_icon.png) **AVISO!** |Indica uma situação perigosa que, se não for evitada, pode causar lesões graves ou de morte. |
| ![Ícone de aviso](./media/data-box-safety/warning_icon.png) **CUIDADO!** |Indica uma situação perigosa que, se não for evitada, pode em uma lesão pequena ou moderada. |
| ![Ícone de observação](./media/data-box-safety/notice_icon.png) **OBSERVAÇÃO:** |Indica informações consideradas importantes, mas não são relacionadas a riscos. |
| ![Ícone de choque elétrico](./media/data-box-safety/electrical_shock_hazard_icon.png) **Risco de choque elétrico** |Alta tensão. |
| ![Ícone de peso pesado](./media/data-box-safety/heavy_weight_hazard_icon.png) **Peso pesado** | |
| ![Ícone de nenhuma peça operada pelo usuário](./media/data-box-safety/no_user_serviceable_parts_icon.png) **Nenhuma peça é operada pelo usuário** |Não acesse a menos que seja devidamente treinado. |
| ![Leia o Ícone do Aviso de Segurança](./media/data-box-safety/read_safety_and_health_information_icon.png) **Leia Todas as Instruções Primeiro** | |
| ![Ícone de risco de tombamento](./media/data-box-safety/tip_hazard_icon.png) **Risco de tombamento** | |

## <a name="handling-precautions"></a>Precauções de manuseio

![Ícone de Aviso](./media/data-box-safety/warning_icon.png) ![Ícone de Choque Elétrico](./media/data-box-safety/electrical_shock_hazard_icon.png)![Ícone de Nenhuma Peça Operada pelo Usuário](./media/data-box-safety/no_user_serviceable_parts_icon.png) **CUIDADO** 

* Inspecione o dispositivo *da forma como foi recebido* para ver se há danos. Se o compartimento do dispositivo estiver danificado, entre em contato com o Suporte da Microsoft para obter uma substituição. Não tente operar o dispositivo. 
* O dispositivo está equipado com parafusos à prova de adulteração. Se você suspeitar que o dispositivo está com problemas, entre em contato com o Suporte da Microsoft para obter uma substituição. Não tente reparar o dispositivo. 
* O dispositivo não contém peças operadas pelo usuário. Níveis de tensão, corrente e energia de risco no aparelho. Não abra. Devolva o dispositivo para a Microsoft para manutenção.

![Ícone de aviso](./media/data-box-safety/warning_icon.png) ![Ícone de peso pesado](./media/data-box-safety/heavy_weight_hazard_icon.png) **AVISO!** 

* Um compartimento completamente configurado pode pesar até 22,7 kg; não tente erguê-lo por conta própria.
* Antes de mover o compartimento, certifique-se sempre de que duas pessoas estejam disponíveis para dar suporte ao peso. Esteja ciente que uma pessoa que tente erguer esse peso sozinha pode sofrer lesões.


![Ícone de aviso](./media/data-box-safety/warning_icon.png) ![Ícone de risco de tombamento](./media/data-box-safety/tip_hazard_icon.png) **AVISO!**
* Coloque o dispositivo em uma superfície plana, estável e rígida para evitar o risco de inclinação.
* Não use equipamentos montados em rack, como prateleiras ou espaços de trabalho. Não coloque o Data Box em cima de equipamentos montados em rack. Adicionar qualquer tipo de carga a uma unidade montada em rack estendida pode gerar o risco de inclinação, podendo causar lesões, morte ou danos ao produto.

![Ícone de aviso](./media/data-box-safety/warning_icon.png) **AVISO!**

* Configure o dispositivo em uma área de trabalho, permitindo uma circulação de ar adequada em torno dele.
* Instale o dispositivo em uma área interna com temperatura controlada e sem contaminantes condutivos, e permita uma circulação de ar adequada em torno dele.
* Mantenha o dispositivo longe de líquidos e ambientes excessivamente úmidos.


## <a name="electrical-precautions"></a>Cuidados elétricos

![Warning Icon](./media/data-box-safety/warning_icon.png) ![Electrical Shock Icon](./media/data-box-safety/electrical_shock_hazard_icon.png) **AVISO!**

* Forneça uma conexão de aterramento elétrico segura para os cabos de alimentação. O cabo CA tem um plugue de aterramento com três fios (um plugue com um pino de aterramento). Esse plugue se encaixa apenas em uma tomada CA com aterramento. Não ignore o objetivo do pino de aterramento.
* Considerando que o plugue no cabo de alimentação é o dispositivo de desconexão principal, verifique se as saídas de soquete estão localizadas perto do dispositivo e são de fácil acesso.
* Desconecte o cabo de alimentação (puxando o plugue, não o cabo) e desconecte todos os cabos caso ocorra alguma destas condições:

    - O cabo de alimentação ou o plugue ficou desgastado ou está danificado.
    - Você despejou algo no invólucro do dispositivo.
    - O dispositivo foi exposto a chuva ou umidade em excesso.
    - O dispositivo caiu e o invólucro está danificado.
    - Você acha que o dispositivo precisa de reparos.
* Desconecte permanentemente a unidade antes de movê-la ou se achar que ela foi danificada de alguma forma.
* Forneça uma fonte de alimentação adequada com proteção contra sobrecarga elétrica para atender às seguintes especificações de energia:

    - Tensão: V CA de 100 a 240 V CA
    - Atual: 6, no máximo
    - Frequência: 50 Hz a 60 Hz

![Ícone de Aviso](./media/data-box-safety/warning_icon.png) **CUIDADO:**

* este dispositivo contém baterias em forma de moeda. Não tente reparar o dispositivo. As baterias no dispositivo não são peças operadas pelo usuário. 
* **Para a equipe de serviço somente**: Risco de explosão se a bateria é substituída por um tipo incorreto. Descarte as baterias usadas de acordo com as instruções.

![Ícone de observação](./media/data-box-safety/notice_icon.png) **OBSERVAÇÃO:**

Para a operação adequada do dispositivo e para evitar danos ao produto:

* Verifique se as portas dianteira e traseira estão totalmente abertas enquanto o dispositivo está em execução.

## <a name="regulatory-information"></a>Informações normativas

Esta seção contém informações normativas para o Azure Data Box, número do modelo DB010.

Este dispositivo é:

- Avaliado como ITE (Equipamento de Tecnologia da Informação), projetado para funcionar em um ambiente de sala de dados típico. A adequação do produto a outros ambientes pode exigir avaliação.
- Projetado para uso com equipamentos de Tecnologia da Informação NRTL listado (UL, CSA, ETL etc.) e em conformidade com IEC/EN 60950-1 ou IEC/EN 62368 1 (marcação CE).
- Projetado para funcionar no ambiente abaixo. 
    - Temperatura operacional: 50° a 95° F (10° a 35° C)
    - Temperatura de armazenamento: -20 °C a 50 °C
    - Umidade relativa: 15% a 85% (sem condensação) 
    - Altitude operacional: Testado até 6500 metros 0 aos medidores de 2000

Para as classificações de energia elétrica, consulte o rótulo de classificação do dispositivo fornecido com a unidade. 

![Ícone de observação](./media/data-box-safety/notice_icon.png) **OBSERVAÇÃO:** 

As alterações ou as modificações feitas no dispositivo não expressamente aprovadas pela Microsoft poderão anular a autorização do usuário para operar o dispositivo.

**CANADÁ e EUA:**

![Ícone de observação](./media/data-box-safety/notice_icon.png) **OBSERVAÇÃO:** 

Este equipamento foi testado e considerado em conformidade com os limites de um dispositivo digital Classe A, de acordo com a parte 15 das Regras da FCC. Esses limites são projetados para fornecer uma proteção razoável contra interferência prejudicial quando o equipamento é operado em um ambiente comercial. O equipamento gera, usa e pode irradiar energia de radiofrequência. Se não for instalado e usado de acordo com o manual de instruções, poderá causar interferências prejudiciais às comunicações de rádio. A operação do equipamento em uma área residencial pode causar interferências prejudiciais. Nesse caso, o usuário precisará corrigir a interferência por conta própria.

O dispositivo está em conformidade com a parte 15 das Regras da FCC e normas RSS isentas de licença do Canadá. A operação está sujeito a estas duas condições: (1) este dispositivo não pode causar interferência prejudicial e (2) o dispositivo deve aceitar qualquer interferência recebida, inclusive interferência que pode causar operação indesejada do dispositivo.

![Canadá](./media/data-box-safety/canada.png)

CAN ICES-3(A)/NMB-3(A)

Microsoft Corporation, One Microsoft Way Redmond, WA 98052 USA.

Estados Unidos: (800) 426-9400

Canadá: (800) 933-4750

**UNIÃO EUROPEIA:**

Uma cópia da Declaração de Conformidade da UE está disponível aqui.

![Ícone de Aviso](./media/data-box-safety/warning_icon.png) **AVISO:** 

É um produto classe A. Em um ambiente doméstico, o produto pode causar interferências de rádio. Nesse caso, o usuário talvez tenha que tomar as devidas medidas.

**Descarte de baterias e equipamentos eletroeletrônicos:**

![Ícone de descarte da bateria](./media/data-box-safety/battery_disposal_icon.png)

Este símbolo no produto, nas baterias ou embalagem significa que o produto e as baterias contidas não devem ser descartadas no lixo comum. É sua responsabilidade levá-los a um ponto de coleta para reciclagem de baterias e equipamentos eletroeletrônicos. Essa coleta e reciclagem separadas ajudarão a conservar os recursos naturais e impedir possíveis consequências negativas para a saúde humana e o meio ambiente devido à possível existência de substâncias perigosas nas baterias e equipamentos eletroeletrônicos, que podem ser causadas pelo descarte inadequado. Para obter mais informações sobre onde entregar suas baterias e equipamentos eletroeletrônicos, entre em contato com a Prefeitura, com o serviço de coleta seletiva ou a loja que vendeu o produto. Entre em contato com *erecycle\@microsoft.com* para obter mais informações sobre a WEEE.

Este produto contém baterias em forma de moeda.

Microsoft Ireland Sandyford Ind Est Dublin D18 KX32 IRL

Número de telefone: +353 1 295 3826

Número de fax: +353 1 706 4110 

**Japão**

![Japão](./media/data-box-safety/japan.png)

<!--**South Korea**

![South Korea](./media/data-box-safety/south-korea.png)

**China RoHS Hazardous Substances Table**

![China RoHS hazardous substances](./media/data-box-safety/south-korea.png)

**Taiwan**

![Taiwan](./media/data-box-safety/taiwan.png)-->

Depois de ler esses avisos de segurança, você pode configurar e cabear o dispositivo.

## <a name="next-steps"></a>Próximas etapas

* [Cabear e conectar o Data Box](data-box-deploy-set-up.md)


