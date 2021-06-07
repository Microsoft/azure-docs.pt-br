---
title: Faturas do Registro Enterprise do Azure
description: Este artigo explica como gerenciar e usar sua fatura do Azure Enterprise.
author: bandersmsft
ms.author: banders
ms.date: 03/05/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: ruturajd
ms.custom: contperf-fy21q1
ms.openlocfilehash: 71ba1d6e1e45c5c2e72e2be620158b1aa4b71582
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102430795"
---
# <a name="azure-enterprise-enrollment-invoices"></a>Faturas do Registro Enterprise do Azure

Este artigo explica como gerenciar e usar sua fatura do Azure EA (Contrato Enterprise do Azure). A fatura é uma representação de sua conta. Examine sua precisão. Conheça outras tarefas que podem ser necessárias para gerenciar sua fatura.

## <a name="view-usage-summary-and-download-reports"></a>Exibir resumo de uso e baixar relatórios

Os administradores corporativos podem exibir um resumo dos dados de uso, do pagamento antecipado do Azure consumido e das cobranças associadas ao uso adicional no Azure Enterprise Portal. As cobranças são apresentadas no nível do resumo em todas as contas e assinaturas.

Para exibir o uso detalhado de contas específicas, baixe o relatório de detalhes de uso:

1. Entre no Azure Enterprise Portal.
1. Selecione **Relatórios**.
1. Selecione a guia **Baixar Uso**.
1. Na lista de relatórios, selecione **Baixar** para o relatório mensal que deseja obter.

   > [!NOTE]
   > O relatório de detalhe de uso não inclui as tributos incidentes.
   >
   > Pode haver uma latência de até oito horas do momento em que o uso foi feito até ser refletido no relatório.

Para exibir os relatórios e os grafos de resumo de uso:

1. Entre no Azure Enterprise Portal.
1. Selecione uma condição de Pagamento antecipado.
   Para alterar o intervalo de datas do **Resumo de Uso**, você pode alternar de **M** (mensal) para **C** (personalizado) no canto superior direito da página e, em seguida, inserir as datas de início e término personalizadas.  
   ![Criar e exibir o resumo de uso e baixar relatórios no modo de exibição personalizado](./media/ea-portal-enrollment-invoices/create-ea-view-usage-summary-and-download-reports-custom-view.png)
1. Para exibir detalhes adicionais, selecione um período ou mês no gráfico.
   - O gráfico mostra o uso mês a mês com um detalhamento do uso utilizado, os encargos adicionais de serviço, os encargos cobrados separadamente e os encargos do Azure Marketplace.
   - Para o mês selecionado, use os campos abaixo do gráfico para filtrar por departamentos, contas e assinaturas.
   - Você pode alternar entre **Cobrar por Serviços** e **Cobrar por Hierarquia**.
   - Veja os detalhes de **Serviço do Azure**, dos **Encargos Cobrados Separadamente** e do **Azure Marketplace** expandindo as seções pertinentes.

Assista a este vídeo para ver como exibir o uso:

> [!VIDEO https://www.youtube.com/embed/Cv2IZ9QCn9E]

### <a name="download-csv-reports"></a>Baixar relatórios CSV

Os administradores corporativos usam a página de Download de Relatório Mensal para baixar vários relatórios como arquivos CSV:

- Saldo e preço
- Detalhes de uso
- Encargos do Azure Marketplace
- Tabela de preços

Para baixar relatórios:

1. No Azure Enterprise Portal, selecione **Relatórios**.
2. Selecione **Baixar Uso** na parte superior da página.
3. Selecione **Baixar** ao lado do relatório do mês.

   > [!NOTE]
   > Pode haver uma latência de até 72 horas entre a data em que o uso ocorreu e o momento em que ele é mostrado nos relatórios.
   >
   > Os usuários que baixarem arquivos CSV com o Safari para Excel poderão encontrar erros de formatação. Para evitar erros, abra o arquivo usando um editor de texto.

![Exemplo mostrando a página Baixar Uso](./media/ea-portal-enrollment-invoices/create-ea-download-csv-reports.png)

Assista a este vídeo para ver como baixar informações de uso:

> [!VIDEO https://www.youtube.com/embed/eY797htT1qg]

### <a name="advanced-report-download"></a>Download do relatório avançado

Você pode usar o download de relatório avançado para obter relatórios que abrangem intervalos de datas ou contas específicas. O arquivo de saída está no formato CSV para acomodar grandes conjuntos de registros.

1. No Azure Enterprise Portal, selecione **Download de Relatório Avançado**.
1. Selecione um intervalo de datas e as contas apropriadas.
1. Escolha **Solicitar dados de uso**.
1. Selecione o botão **Atualizar** até que o status do relatório seja atualizado para **Baixar**.
1. Baixe o relatório.

### <a name="download-usage-reports-and-billing-information-for-a-prior-enrollment"></a>Baixar relatórios de uso e informações de cobrança de um registro anterior

Você pode baixar relatórios de uso e informações de cobrança para um registro anterior depois que uma transferência de registro tiver sido feita. Os relatórios históricos estão disponíveis no Azure Enterprise Portal e no gerenciamento de custos.

O Azure Enterprise Portal filtra os registros inativos, deixando-os fora da exibição. Você precisará desmarcar a caixa de **Ativo** para exibir registros transferidos inativos.  

![Desmarcar a caixa Ativo permite que o usuário veja registros inativos](./media/ea-portal-enrollment-invoices/unchecked-active-box.png)

## <a name="change-a-po-number-for-an-upcoming-overage-invoice"></a>Alterar o número da OC de uma fatura de excedente a vencer

O Azure Enterprise Portal gera automaticamente uma OC (ordem de compra) padrão, a menos que o administrador corporativo configure uma antes da data da fatura. Um administrador corporativo pode atualizar o número da OC até sete dias após receber um email de notificação automático com a fatura. 

Para evitar a geração automática dos números da OC todos os meses, você pode bloquear o número da OC. Confira [Bloquear número da OC](#lock-po-number-to-prevent-automatic-update-in-upcoming-billing-cycles).

### <a name="update-the-azure-services-purchase-order-number"></a>Atualize o número da ordem de compra dos serviços do Azure

1. No Azure Enterprise Portal, selecione **Relatório** > **Resumo do Uso**.
1. Selecione **Editar números da OC** no canto superior direito.
1. Selecione o botão de opção **Serviços do Azure**.
1. Selecione um **Período da fatura** no menu suspenso com intervalos de datas.
   É possível editar um número de OC durante um período de sete dias depois de obter uma notificação de fatura, mas antes de pagar a fatura.
1. Insira um novo número da OC no campo **Número da OC**.
1. Selecione **Salvar** para enviar a alteração.

### <a name="update-the-azure-marketplace-purchase-order-number"></a>Atualize o número da ordem de compra do Azure Marketplace

1. No Azure Enterprise Portal, selecione **Relatório** > **Resumo do Uso**.
1. Selecione **Editar números da OC** no canto superior direito.
1. Selecione o botão de opção do **Marketplace**.
1. Selecione um **Período da fatura** no menu suspenso com intervalos de datas.  
    É possível editar um número de OC durante um período de sete dias depois de obter uma notificação de fatura, mas antes de pagar a fatura.
1. Insira um novo número da OC no campo **Número da OC**.
1. Selecione **Salvar** para enviar a alteração.

### <a name="lock-po-number-to-prevent-automatic-update-in-upcoming-billing-cycles"></a>Bloquear número da OC para evitar a atualização automática nos próximos ciclos de cobrança

Depois que você bloquear o número da OC, ele permanecerá bloqueado para todas as novas faturas e você não precisará atualizar o número da OC.

1.  No portal do Azure Enterprise, selecione **Relatório** > **Resumo do Uso**.
2.  Selecione **Editar números da OC** no canto superior direito.
3.  Digite um novo número da OC no campo **Número da OC**.
4.  Selecione a caixa **Bloquear número da OC**.
5.  Selecione **Salvar** para enviar a alteração.  
    :::image type="content" source="./media/ea-portal-enrollment-invoices/lock-po.png" alt-text="Captura de tela mostrando a caixa Exibir/editar números da OC." lightbox="./media/ea-portal-enrollment-invoices/lock-po.png" :::


## <a name="azure-enterprise-billing-frequency"></a>Frequência da cobrança do Azure Enterprise

A Microsoft faz a cobrança anualmente na data de início de vigência do registro por qualquer compra do pagamento antecipado dos serviços do Microsoft Azure. Para qualquer uso que exceda os valores do pagamento antecipado, a Microsoft faz a cobrança com atraso de pagamento.

- Os valores do pagamento antecipado são cotados com base em uma taxa mensal e cobrados anualmente com antecedência.
- Valores excedentes são calculados mensalmente e cobrados posteriormente, no final do período de cobrança.

### <a name="billing-intervals"></a>Intervalos de cobrança

O intervalo de cobrança depende de como você opta por fazer suas compras do pagamento antecipado. Seu pagamento antecipado anual é coextensivo com:

- A data de aniversário do registro
- A data de início de vigência da sua assinatura de aditamento de um ano.

A data em que você recebe a fatura excedente depende da data de início e da configuração do registro:

- **Registros diretos com uma data de início anterior a 1º de maio de 2018**:
  - Se você estiver em um EA (Contrato Enterprise) direto, o ciclo de cobrança será anual para os serviços do Azure, exceto serviços do Azure Marketplace. Seu ciclo de cobrança é baseado na data de aniversário: a data em que seu contrato se tornou efetivo.
  - Se você ultrapassar 150% do seu limite do pagamento antecipado do Azure EA, será automaticamente convertido em um período de cobrança trimestral com base na sua data de aniversário. Você também receberá uma fatura de excedente do serviço do Azure.
  - Se você não ultrapassar 150% do seu limite do pagamento antecipado do Azure, seu registro permanecerá em um período de cobrança anual. A fatura de excedente será recebida no final do ano do pagamento antecipado.

- **Registros diretos com uma data de início posterior a 1º de maio de 2018**:
  - As faturas de consumo e cobranças do Azure cobradas separadamente estão em um ciclo de cobrança mensal.
  - Os custos não cobertos pelo seu pagamento antecipado do Azure são devidos como um pagamento de excedente.  

- **Registros indiretos com um registro iniciado antes de 1º de maio de 2018**:

  Se você for um cliente indireto de EA (Contrato Enterprise) com uma data de início antes de 1º de maio de 2018, estará configurado em um ciclo de cobrança trimestral. O CP (parceiro de canal) fatura você diretamente.  

- **Registros indiretos com uma data de início posterior a 1º de maio de 2018**:

  Você está em um ciclo de cobrança mensal.  

### <a name="increase-your-azure-prepayment"></a>Aumentar o pagamento antecipado do Azure

Você pode aumentar seu pagamento antecipado a qualquer momento. Você é cobrado pelo número de meses restantes no período do pagamento antecipado do ano em questão. Por exemplo, se você se inscrever em uma assinatura de aditamento de um ano e depois aumentar seu pagamento antecipado durante o sexto mês, será faturado pelos seis meses restantes desse termo. Suas quantidades do pagamento antecipado serão atualizadas para os últimos seis meses do termo do pagamento antecipado. Essas novas quantidades serão usadas para determinar cobranças excedentes.

### <a name="overage"></a>Excedente

No caso de excedente, você é cobrado pelo uso ou pelas reservas que excedem o pagamento antecipado durante o período de cobrança. Para exibir um detalhamento de como as quantidades excedentes de itens individuais foram calculadas, confira o relatório de resumo do uso ou fale com o parceiro de canal.

Para cada item na fatura, você verá:

- **Valor ampliado**: o total de cobranças
- **Uso do pagamento antecipado**: o valor do pagamento antecipado usado para cobrir os custos
- **Valor líquido**: os custos que excedem o pagamento antecipado

Os tributos incidentes são calculados somente sobre o valor líquido que excede o pagamento antecipado.

O faturamento excedente é automatizado. O cronograma das notificações e das faturas depende da data de término do seu período de cobrança.

- A notificação de excedente normalmente é enviada sete dias após a data de término da cobrança.
- As faturas de excedentes são enviadas de sete a nove dias após a notificação.
- Você pode examinar as cobranças e atualizar os números de OC gerados pelo sistema durante os sete dias entre a notificação de excedente e o faturamento.

### <a name="azure-marketplace"></a>Azure Marketplace

A partir do ciclo de cobrança de abril de 2019, os clientes começaram a receber uma fatura do Azure que combina todas as cobranças do Azure e do Azure Marketplace em uma só fatura, ao invés de duas faturas separadas. Essa alteração não afeta os clientes na Austrália, no Japão ou em Singapura.

Durante a transição para uma fatura combinada, você receberá uma fatura parcial do Azure Marketplace. Essa última fatura separada mostrará as cobranças do Azure Marketplace antes da data da sua atualização de cobrança. As cobranças do Azure Marketplace incorridas após essa data serão exibidas em sua fatura do Azure. Após o período de transição, você verá todas as cobranças do Azure e do Azure Marketplace na fatura combinada.  

### <a name="adjust-billing-frequency"></a>Alterar a frequência da cobrança

A frequência da cobrança do cliente pode ser anual, trimestral ou mensal. O período de cobrança é determinado quando o cliente assina o contrato. A cobrança mensal é o menor intervalo de cobrança.

- É necessária a **aprovação** do administrador corporativo para alterar o ciclo de cobrança de anual para trimestral em registros diretos. É necessária a aprovação do administrador de parceiro para registros indiretos. A alteração entra em vigor no fim do trimestre de cobrança atual.
- É necessário acrescentar **um aditamento** ao contrato para alterar um ciclo de cobrança anual ou trimestral para mensal.  Qualquer alteração no ciclo de cobrança do registro existente exige aprovação de um administrador corporativo ou do "Contato para Cobrança".
- **Envie** sua aprovação para o [suporte do Azure Enterprise Portal](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c). Selecione a categoria do problema: **Cobrança e faturamento**.

A alteração entra em vigor no fim do trimestre de cobrança atual.

Se um Aditamento M503 for assinado, você poderá alterar qualquer contrato de qualquer frequência para a cobrança mensal. 

### <a name="request-an-invoice-copy"></a>Solicitar uma cópia da fatura

Para solicitar uma cópia da fatura, fale com seu parceiro.

## <a name="credits-and-adjustments"></a>Créditos e ajustes

É possível exibir todos os créditos ou ajustes aplicados ao seu registro na seção **Relatórios** do [Azure Enterprise Portal](https://ea.azure.com).

Para exibir créditos:

1. No [Azure Enterprise Portal](https://ea.azure.com), selecione a seção **Relatórios**.
1. Selecione **Resumo do Uso**.
1. No canto superior direito, altere a exibição **M** para **C**.
1. Estenda o campo de ajuste na tabela do pagamento antecipado do serviço do Azure.
1. Você verá os créditos aplicados ao seu registro e uma breve explicação. Por exemplo:  Crédito do Contrato de Nível de Serviço.

## <a name="pay-your-overage-with-your-azure-prepayment"></a>Pagar o excedente com o pagamento antecipado do Azure

Para aplicar o pagamento antecipado do Azure aos excedentes, você precisa atender aos seguintes critérios:

- Ter incorrido cobranças de excedente que não foram pagas e estão no prazo de um ano da data de término do serviço cobrado.
- O valor do pagamento antecipado do Azure disponível cobre o valor total dos custos gerados, incluindo todas as faturas anteriores não pagas do Azure.
- O termo de cobrança que você deseja concluir precisa estar totalmente encerrado. A cobrança se encerra totalmente após o quinto dia de cada mês.
- O período de cobrança que você deseja compensar precisa estar totalmente encerrado.
- O ACD (desconto do pagamento antecipado do Azure) se baseia no novo pagamento antecipado real menos os fundos planejados para o consumo anterior. Este requisito se aplica somente às cobranças de excedente incorridas. Ele só é válido para os serviços que consomem o pagamento antecipado do Azure e, portanto, não se aplica aos custos do Azure Marketplace. As cobranças do Azure Marketplace são feitas separadamente.

Para concluir uma compensação de excedente, você ou a equipe de conta pode abrir uma solicitação de suporte. É necessária uma aprovação por email do administrador corporativo ou do Contato para Cobrança.

## <a name="move-charges-to-another-enrollment"></a>Passe os encargos para outro registro

Os dados de uso só serão movidos se a transferência for retroativa. Há duas opções para mover dados de uso de um registro para outro:

- Transferências de conta de um registro para outro
- Transferências de registro de um registro para outro

Para ambas as opções, é preciso enviar uma [solicitação de suporte](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c) à Equipe de Suporte do EA para obter assistência. 

## <a name="enterprise-agreement-usage-calculations"></a>Cálculos de uso do Contrato Enterprise

Confira [serviços do Azure](https://azure.microsoft.com/services/) e [preços do Azure](https://azure.microsoft.com/pricing/) para saber mais sobre preços, unidades de medida, perguntas frequentes e diretrizes de relatório de uso para cada serviço individualmente. Veja mais informações sobre cálculos de EA nas seções a seguir.

### <a name="enterprise-agreement-units-of-measure"></a>Unidades de medida do Contrato Enterprise

As unidades de medida para Contratos Enterprise costumam ser diferentes daquelas utilizadas em nossos outros programas, como o programa MOSA (Contrato do Microsoft Online Services). Essa diferença significa que, para vários serviços, a unidade de medida é agregada para fornecer os preços normalizados. A unidade de medida mostrada na exibição Resumo do Uso do Azure Enterprise Portal é sempre a medida do Enterprise. Uma lista completa de unidades de medida e conversões atuais para cada serviço é fornecida enviando uma [solicitação de suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="conversion-between-usage-detail-report-and-the-usage-summary-page"></a>Conversão entre o relatório de detalhes de uso e a página de resumo de uso

No relatório de download de dados de uso, é possível ver o uso bruto de recursos de até seis casas decimais. No entanto, os dados de uso mostrados no Azure Enterprise Portal são arredondados para quatro casas decimais para unidades do pagamento antecipado e truncados para zero decimal para unidades de excedente. Os dados de uso brutos são arredondados primeiro para quatro dígitos antes da conversão para as unidades usadas no Azure Enterprise Portal. Em seguida, as unidades do Enterprise convertidas são arredondadas novamente para quatro dígitos. Só é possível ver as horas reais consumidas antes da conversão no relatório de download de uso e não no Azure Enterprise Portal.

Por exemplo:  Se 694,533404 horas SQL Server reais forem relatadas no relatório de detalhes de uso. Essas unidades são convertidas em 6,94533404 de 100 horas de computação que, em seguida, são arredondadas para 6,9453 e exibidas no Azure Enterprise Portal.

- Para determinar o valor da cobrança estendida, as unidades exibidas são multiplicadas pelo preço unitário do pagamento antecipado, e o resultado é truncado para dois decimais. Para o iene japonês (JPY) e o won coreano (KRW), o valor estendido é arredondado para zero decimais.
- No caso de excedente, as unidades faturáveis são truncadas para seis e, em seguida, multiplicadas pelo preço unitário excedente para determinar o valor da cobrança estendida.
- Para a cobrança do MSP (provedor de serviços gerenciados), todo o uso associado a um departamento marcado como MSP é truncado para zero decimais após a conversão para a unidade de medida do EA. Como resultado, a soma desse uso pode ser menor do que a soma total de todos os usos relatados no Azure Enterprise Portal. Isso depende se o MSP está dentro do saldo do pagamento antecipado do Azure ou está em excedente.

### <a name="graduated-pricing"></a>Preços graduados

Atualmente, o preço do Contrato Enterprise não inclui preços graduados, em que as cobranças por unidade diminuem à medida que o uso aumenta. Quando você passa de um programa MOSA com preços graduados para um Contrato Enterprise, seus preços são definidos proporcionalmente à camada base do serviço menos os ajustes aplicáveis para descontos de Contrato Enterprise.

### <a name="partial-hour-billing"></a>Cobrança de hora parcial

Todo uso cobrado tem base em minutos convertidos em horas parciais, e não em incrementos de hora inteira. As taxas por hora listadas do Enterprise são aplicadas ao total de horas, além de horas parciais.

### <a name="average-daily-consumption"></a>Consumo diário médio

Alguns serviços são cobrados mensalmente, mas o uso é relatado diariamente. Nesses casos, o uso é avaliado uma vez por dia, dividido por 31 e somado ao longo do número de dias nesse mês de cobrança. Portanto, as taxas nunca são maiores do que o esperado para qualquer mês e são ligeiramente menores para meses com menos de 31 dias.

### <a name="compute-hours-conversion"></a>Conversão de horas de computação

Antes de 28 de janeiro de 2016, o uso das máquinas virtuais Standard e Basic A0, A2, A3 e A4 e Serviços de Nuvem era emitido como minutos dos medidores da Máquina Virtual A1. As VMs A0 contavam como frações de minutos da VM A1 enquanto A2s, A3s e A4s foram convertidas em múltiplos. Como essa política causou alguma confusão para nossos clientes, implementamos uma alteração para atribuir o uso por minuto para os medidores A0, A2, A3 e A4 dedicados.

A nova medição entrou em vigor entre 27 de janeiro e 28 de janeiro de 2016. No download de CSV que mostra o uso durante esse período de transição, você pode ver:

- O uso emitido no medidor A1
- O uso emitido no novo medidor dedicado correspondente ao tamanho da sua implantação.

| **Tamanho da implantação** | **Uso emitido como múltiplo de A1 até 26 de janeiro de 2016** | **Uso emitido no medidor dedicado a partir de 27 de janeiro de 2016** |
| --- | --- | --- |
| A0 | 0,25 de hora em A1 | 1 hora em A0 |
| A2 | 2 horas em A1 | 1 hora em A2 |
| A3 | 4 horas em A1 | 1 hora em A3 |
| A4 | 8 horas em A1 | 1 hora em A4 |

### <a name="regions"></a>Regiões

Para os serviços em que a zona e a região afetam os preços, confira a seguinte tabela para ver o mapa de regiões geográficas:

| Localização geográfica | Regiões de transferência de dados | Regiões CDN |
| --- | --- | --- |
| Zona 1 | Norte da Europa <br> Europa Ocidental <br> Oeste dos EUA <br> Leste dos EUA <br> Centro-Norte dos EUA <br> Centro-Sul dos EUA <br> Leste dos EUA 2 <br> EUA Central | América do Norte <br> Europa |
| Zona 2 | Leste do Pacífico Asiático <br> Sudeste do Pacífico Asiático <br> Leste do Japão <br> Oeste do Japão <br> Leste da Austrália <br> Sudeste da Austrália | Pacífico Asiático <br> Japão <br> América Latina <br> Oriente Médio e África <br> Leste da Austrália <br> Sudeste da Austrália |
| Zona 3 | Sul do Brasil |   |

Não há cobranças para saída de dados entre serviços hospedados no mesmo data center. Por exemplo, Microsoft 365 e Azure.

### <a name="azure-prepayment-and-unbilled-usage"></a>Pagamento antecipado do Azure e utilização não faturada

O Pagamento Antecipado do Azure é um valor pago antecipadamente pelos serviços do Azure. O pagamento antecipado do Azure é consumido conforme os serviços são usados. Os serviços internos do Azure são cobrados no pagamento antecipado do Azure. No entanto, alguns custos são cobrados separadamente, e os serviços do Azure Marketplace não consomem o pagamento antecipado do Azure.

### <a name="charges-billed-separately"></a>Cobranças arrecadadas separadamente

Alguns produtos e serviços fornecidos por fontes de terceiros não consomem o Pagamento Antecipado do Azure. Ao invés disso, esses itens são cobrados separadamente como parte da fatura excedente do ciclo de cobrança padrão.

Combinamos todas as cobranças do Azure e do Azure Marketplace em uma fatura que se alinha com o ciclo de cobrança do registro. A fatura combinada não se aplica aos clientes na Austrália, no Japão ou em Singapura.

A fatura combinada mostra o uso do Azure primeiro, seguido por qualquer cobrança do Azure Marketplace. Os clientes da Austrália, do Japão ou de Singapura veem as cobranças do Azure Marketplace em uma fatura separada.

Se não houver uso excedente no final do ciclo de cobrança padrão, as cobranças arrecadadas separadamente serão faturadas separadamente. Esse processo é aplicável aos clientes na Austrália, no Japão e em Singapura.

Os seguintes serviços são cobrados separadamente:

- Canônico
- Citrix XenApp Essentials
- Usuário registrado do Citrix XenDesktop
- OpenLogic
- Direitos de Acesso Remoto XenApp Essentials
Usuário Registrado
- Ubuntu Advantage
- Visual Studio Enterprise (mensal)
- Visual Studio Enterprise (anual)
- Visual Studio Professional (mensal)
- Visual Studio Professional (anual)

## <a name="what-to-expect-after-change-of-channel-partner"></a>O que esperar após a alteração do parceiro de canal

Se a alteração do COCP (parceiro de canal) ocorrer no meio do mês, um cliente receberá uma fatura para uso do parceiro associado anterior e outra fatura para uso com o novo parceiro.

As faturas serão lançadas no mês posterior ao término do período de cobrança. Se o ritmo de cobrança for mensal, a fatura de setembro será lançada em outubro para ambos os parceiros. Se o ciclo de cobrança for trimestral ou anual, o cliente poderá esperar uma fatura do parceiro associado anterior pelo uso no período dele; o restante será para o novo parceiro, com base no ritmo de cobrança.

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre como funcionam as faturas e as cobranças, confira [Entender a fatura do Contrato Enterprise do Azure](../understand/review-enterprise-agreement-bill.md).
- Para começar a usar o Azure Enterprise Portal, confira [Introdução ao Portal do EA do Azure](ea-portal-get-started.md).
