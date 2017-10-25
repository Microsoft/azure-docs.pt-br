---
title: Gerenciar seus modelos de largura de banda do StorSimple | Microsoft Docs
description: Descreve como gerenciar modelos de largura do StorSimple, que permitem controlar o consumo da largura de banda.
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 0027b90e-91a5-437d-9bd0-06b05674aa5f
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/16/2016
ms.author: alkohli
ms.openlocfilehash: df3ae8bf775370432b3648459a7c942afe69fb17
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-manager-service-to-manage-storsimple-bandwidth-templates"></a>Usar o serviço StorSimple Manager para gerenciar modelos de largura de banda do StorSimple
## <a name="overview"></a>Visão geral
Os modelos de largura de banda permitem configurar a largura de banda de rede em várias agendas do dia para dispor os dados do dispositivo StorSimple em camadas na nuvem.

Com agendas de limitação da largura de banda, você pode:

* Especifica as agendas de largura de banda personalizadas dependendo dos usos da rede de carga de trabalho.
* Centralizar o gerenciamento e reutilizar as agendas em vários dispositivos de uma maneira fácil e direta.

> [!NOTE]
> Esse recurso está disponível somente para dispositivos físicos do StorSimple, e não para dispositivos virtuais.
> 
> 

Todos os modelos de largura de banda para seu serviço são exibidos em um formato de tabela e contêm as seguintes informações:

* **Nome** : um nome exclusivo atribuído ao modelo de largura de banda quando ele foi criado.
* **Agenda** : o número de agendas contidas em um determinado modelo de largura de banda.
* **Usado por** : o número de volumes usando os modelos de largura de banda.

Use a página **Configurar** do serviço StorSimple Manager no Portal clássico do Azure para gerenciar modelos de largura de banda.

Você também pode encontrar informações adicionais que ajudam a configurar modelos de largura de banda em:

* Perguntas e respostas sobre modelos de largura de banda
* Práticas recomendadas para modelos de largura de banda

## <a name="add-a-bandwidth-template"></a>Adicionar um modelo de largura de banda
Execute as etapas a seguir para criar um novo modelo de largura de banda.

#### <a name="to-add-a-bandwidth-template"></a>Para adicionar um modelo de largura de banda
1. Na página **Configurar** do serviço StorSimple Manager, clique em **adicionar/editar modelo de largura de banda**.
2. Na caixa de diálogo **Adicionar/Editar Modelo de Largura de Banda** :
   
   1. Na lista suspensa **Modelo**, selecione **Criar novo** para adicionar um novo modelo de largura de banda.
   2. Especifique um nome exclusivo para o modelo de largura de banda.
3. Defina uma **Agenda de Largura de Banda**. Para criar uma agenda:
   
   1. Na lista suspensa, escolha os dias da semana para os quais a agenda é configurada. Você pode selecionar vários dias marcando as caixas de seleção localizadas antes dos respectivos dias na lista.
   2. Selecione a opção **Dia Inteiro** se a agenda for imposta para o dia inteiro. Quando essa opção é marcada, você não pode especificar uma **Hora de Início** ou uma **Hora de Término**. A agenda é válida das 0:00 às 23:59.
   3. Na lista suspensa, selecione uma **Hora de Início**. Essa é a hora em que a agenda será iniciada.
   4. Na lista suspensa, selecione uma **Hora de Término**. Essa é a hora em que a agenda será encerrada.
      
      > [!NOTE]
      > Não há permissão para os agendamentos sobrepostos. Se as horas de início e término resultarem em um agendamento sobreposto, você verá uma mensagem de erro sobre isso.
      > 
      > 
   5. Especifique a **Taxa da Largura de Banda**. Essa é a largura de banda em Megabits por segundo (Mbps) usada pelo dispositivo StorSimple em operações que envolvem a nuvem (uploads e downloads). Forneça um número entre 1 e 1.000 para esse campo.
   6. Clique no ícone de verificação ![Ícone de verificação](./media/storsimple-manage-bandwidth-templates/HCS_CheckIcon.png). O modelo que você criou será adicionado à lista de modelos de largura de banda na página **Configurar** do serviço.
      
      ![Criar novo modelo de largura de banda](./media/storsimple-manage-bandwidth-templates/HCS_CreateNewBT1.png)
4. Clique em **Salvar** na parte inferior da página e em **Sim** quando solicitada a confirmação. Isso salvará as alterações de configuração que você fez.

## <a name="edit-a-bandwidth-template"></a>Editar um modelo de largura de banda
Execute as etapas a seguir para editar um modelo de largura de banda.

### <a name="to-edit-a-bandwidth-template"></a>Para editar um modelo de largura de banda
1. Clique em **adicionar/editar modelo de largura de banda**.
2. Na caixa de diálogo **Adicionar/Editar Modelo de Largura de Banda** :
   
   1. Na lista suspensa **Modelo** , escolha um modelo de largura de banda existente que deseja modificar.
   2. Conclua as alterações. (Você pode modificar qualquer uma das configurações existentes.)
   3. Clique no ícone de verificação  ![Ícone de verificação](./media/storsimple-manage-bandwidth-templates/HCS_CheckIcon.png). Você verá o modelo modificado na lista de modelos de largura de banda na página Configurar do serviço.
3. Para salvar as alterações, clique em **Salvar** na parte inferior da página. Clique em **Sim** quando solicitada a confirmação.

> [!NOTE]
> Você não poderá salvar as alterações se a agenda editada sobrepuser uma agenda existente no modelo de largura de banda que você está modificando.
> 
> 

## <a name="delete-a-bandwidth-template"></a>Excluir um modelo de largura de banda
Execute as etapas a seguir para excluir um modelo de largura de banda.

#### <a name="to-delete-a-bandwidth-template"></a>Para excluir um modelo de largura de banda
1. Na lista de tabela dos modelos de largura de banda para seu serviço, selecione o modelo que deseja excluir. Um ícone de exclusão (**x**) aparecerá à extrema direita do modelo selecionado. Clique no ícone **x** para excluir o modelo.
2. Será solicitada a sua confirmação. Clique em **OK** para continuar.

Se o modelo estiver sendo usado por algum volume, você não poderá excluí-lo. Você verá uma mensagem de erro indicando que o modelo está em uso. Uma caixa de diálogo de mensagem de erro será exibida avisando que todas as referências ao modelo devem ser removidas.

É possível excluir todas as referências ao modelo acessando a página **Contêineres de Volume** e modificando os contêineres de volume que usam esse modelo para que eles usem outro modelo ou usem uma configuração de largura de banda personalizada ou ilimitada. Quando todas as referências forem removidas, você poderá excluir o modelo.

## <a name="use-a-default-bandwidth-template"></a>Usar um modelo de largura de banda padrão
Um modelo de largura de banda padrão é fornecido e usado pelos contêineres de volume, por padrão, para impor controles de largura de banda durante o acesso à nuvem. O modelo padrão também serve como uma referência pronta para os usuários que criam seus próprios modelos. Os detalhes desse modelo padrão são:

* **Nome** : noites e fins de semana ilimitados
* **Agenda** : uma única agenda de segunda-feira a sexta-feira que se aplica a uma taxa de largura de banda de 1 Mbps no horário das 8:00 às 17:00 do dispositivo. A largura de banda é definida como Ilimitada para o restante da semana.

O modelo padrão pode ser editado. O uso desse modelo (incluindo versões editadas) é rastreado.

## <a name="create-an-all-day-bandwidth-template-that-starts-at-a-specified-time"></a>Criar um modelo de largura de banda de dia inteiro que comece em uma hora especificada
Siga este procedimento para criar uma agenda que comece em uma hora especificada e seja executada o dia todo. No exemplo, a agenda inicia às 9:00 e é executada até às 9:00 do dia seguinte. É importante observar que as horas de início e término de uma determinada agenda devem estar contidas na mesma agenda de 24 horas e não podem abranger vários dias. Se precisar configurar modelos de largura de banda que se estendam por vários dias, você precisará usar várias agendas (conforme mostrado no exemplo).

#### <a name="to-create-an-all-day-bandwidth-template"></a>Para criar um modelo de largura de banda de dia inteiro
1. Crie uma agenda que inicie às 9:00 e seja executada até a meia-noite.
2. Adicione outra agenda. Configure a segunda agenda para execução da meia-noite até às 9:00.
3. Salve o modelo de largura de banda.

A agenda composta terá início na hora de sua escolha e será executa o dia todo.

## <a name="questions-and-answers-about-bandwidth-templates"></a>Perguntas e respostas sobre modelos de largura de banda
**P**. O que acontece aos controles de largura de banda quando você está entre as agendas? (Uma agenda foi encerrada e outra ainda não foi iniciada.)

**R**. Nesses casos, nenhum controle de largura de banda será utilizado. Isso significa que o dispositivo pode usar a largura de banda ilimitada ao dispor dados em camadas na nuvem.

**P**. Você pode modificar os modelos de largura de banda em um dispositivo offline?

**R**. Você não poderá modificar os modelos de largura de banda em contêineres de volumes se o dispositivo correspondente estiver offline.

**P**. Você pode editar um modelo de largura de banda associado a um contêiner de volume quando os volumes associados estão offline?

**R**. Você pode modificar um modelo de largura de banda associado a um contêiner de volume cujos volumes estejam offline. Observe que quando os volumes estiverem offline, nenhum dado será disposto em camadas do dispositivo para a nuvem.

**P**. Você pode excluir um modelo padrão?

**R**. Embora você possa excluir um modelo padrão, não é uma boa ideia fazer isso. O uso de um modelo padrão, incluindo versões editadas, é rastreado. Os dados de rastreamento são analisados e, ao longo do tempo, são usados para melhorar o modelo padrão.

**P**. Como determinar se seus modelos de largura de banda precisam ser modificados?

**R**. Um dos sinais que indicam que é preciso modificar os modelos de largura de banda é quando você começa a perceber a lentidão da rede ou reduções várias vezes no dia. Se isso acontecer, monitore a rede de armazenamento e uso examinando os gráficos do desempenho de E/S e da taxa de transferência de rede.

Com os dados de taxa de transferência da rede, identifique a hora do dia e os contêineres de volume em que ocorre o afunilamento da rede. Se isso ocorre quando dados estão sendo dispostos em camada na nuvem (obtenha essa informação do desempenho de E/S de todos os contêineres de volume do dispositivo para nuvem), você precisará modificar os modelos de largura de banda associados aos seus contêineres de volume.

Depois que os modelos modificados estiverem em uso, você precisara monitorar a rede novamente em busca de latências significativas. Se elas ainda existirem, você precisará rever seus modelos de largura de banda.

**P**. O que acontece se vários contêineres de volume em meu dispositivo tiverem agendas que se sobrepõem, mas diferentes limites se aplicarem a cada uma?

**R**. Vamos supor que você tenha um dispositivo com 3 contêineres de volume. As agendas associadas a esses contêineres se sobrepõem completamente. Para cada um desses contêineres, os limites de largura de banda usados são 5, 10 e 15 Mbps, respectivamente. Quando a E/S estiver ocorrendo em todos esses contêineres ao mesmo tempo, o mínimo dos 3 limites de largura de banda poderá ser aplicado: nesse caso, 5 Mbps, pois essas solicitações de E/S de saída compartilham a mesma fila.

## <a name="best-practices-for-bandwidth-templates"></a>Práticas recomendadas para modelos de largura de banda
Siga estas práticas recomendadas para seu dispositivo StorSimple:

* Configure modelos de largura de banda em seu dispositivo para habilitar a limitação variável da taxa de transferência da rede pelo dispositivo em diferentes horas do dia. Esses modelos de largura de banda quando usados com agendas de backup podem aproveitar eficientemente largura de banda de rede adicionais para operações de nuvem fora dos horários de pico.
* Calcule a largura de banda real necessária para uma implantação específica com base no tamanho da implantação e no RTO (objetivo de tempo de recuperação) necessário.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre o [uso do serviço StorSimple Manager para administrar seu dispositivo StorSimple](storsimple-manager-service-administration.md).

