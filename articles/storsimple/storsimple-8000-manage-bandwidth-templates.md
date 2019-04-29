---
title: Gerenciar modelos de largura de banda para StorSimple série 8000 | Microsoft Docs
description: Descreve como gerenciar modelos de largura do StorSimple, que permitem controlar o consumo da largura de banda.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: 13a3e57bb27c075fc045e87790dbe13369ed9f8e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60699438"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-storsimple-bandwidth-templates"></a>Usar o serviço do Gerenciador de Dispositivos do StorSimple para gerenciar modelos de largura de banda do StorSimple

## <a name="overview"></a>Visão geral

Os modelos de largura de banda permitem configurar a largura de banda de rede em várias agendas do dia para dispor os dados do dispositivo StorSimple em camadas na nuvem.

Com agendas de limitação da largura de banda, você pode:

* Especifica as agendas de largura de banda personalizadas dependendo dos usos da rede de carga de trabalho.
* Centralizar o gerenciamento e reutilizar as agendas em vários dispositivos de uma maneira fácil e direta.

> [!NOTE]
> Este recurso está disponível somente para dispositivos físicos StorSimple (modelos 8100 e 8600) e não para Dispositivos de Nuvem StorSimple (modelos 8010 e 8020).


## <a name="the-bandwidth-templates-blade"></a>A folha Modelos de largura de banda

A folha **Modelos de largura de banda** apresenta todos os modelos de largura de banda para seu serviço exibidos em um formato tabular e contêm as seguintes informações:

* **Nome** : um nome exclusivo atribuído ao modelo de largura de banda quando ele foi criado.
* **Agenda** : o número de agendas contidas em um determinado modelo de largura de banda.
* **Usado por** : o número de volumes usando os modelos de largura de banda.

Você também pode encontrar informações adicionais que ajudam a configurar modelos de largura de banda em:

* [Perguntas e respostas sobre modelos de largura de banda](#questions-and-answers-about-bandwidth-templates)
* [Práticas recomendadas para modelos de largura de banda](#best-practices-for-bandwidth-templates)

## <a name="add-a-bandwidth-template"></a>Adicionar um modelo de largura de banda

Execute as etapas a seguir para criar um novo modelo de largura de banda.

#### <a name="to-add-a-bandwidth-template"></a>Para adicionar um modelo de largura de banda

1. Acesse o serviço do Gerenciador de Dispositivos do StorSimple, clique em **Modelos de largura de banda** e em **+ Adicionar modelo de largura de banda**.

    ![Clique em + Adicionar modelo de largura de banda](./media/storsimple-8000-manage-bandwidth-templates/addbwtemp1.png)

2. Na folha **Adicionar modelo de largura de banda**, execute as seguintes etapas:
   
    1. Especifique um nome exclusivo para o modelo de largura de banda.
    2. Defina uma agenda de largura de banda. Para criar uma agenda:
   
        1. Na lista suspensa, escolha os **Dias** da semana para os quais a agenda será configurada. É possível selecionar vários dias.        
        
        2. Insira uma **Hora Inicial** no formato _hh:mm_. Essa é a hora em que a agenda será iniciada.

        3. Insira uma **Hora Final** no formato _hh:mm_. Essa é a hora em que a agenda será encerrada.
      
           > [!NOTE]
           > Não há permissão para os agendamentos sobrepostos. Se as horas de início e término resultarem em um agendamento sobreposto, você verá uma mensagem de erro sobre isso.

        4. Especifique a **Taxa da Largura de Banda**. Essa é a largura de banda em Megabits por segundo (Mbps) usada pelo dispositivo StorSimple em operações que envolvem a nuvem (uploads e downloads). Forneça um número entre 1 e 1.000 para esse campo.

            ![Defina uma agenda de largura de banda](./media/storsimple-8000-manage-bandwidth-templates/addbwtemp2.png)
         
            Repita as etapas acima para definir várias agendas para seu modelo até terminar.

        5. Clique em **Adicionar** para começar a criar um modelo de largura de banda. O modelo criado é adicionado à lista de modelos de largura de banda.
      

## <a name="edit-a-bandwidth-template"></a>Editar um modelo de largura de banda

Execute as etapas a seguir para editar um modelo de largura de banda.

### <a name="to-edit-a-bandwidth-template"></a>Para editar um modelo de largura de banda

1. Acesse o serviço do Gerenciador de Dispositivos do StorSimple e clique em **Modelos de largura de banda**.
2. Na lista de modelos de largura de banda, selecione o modelo que você deseja excluir. Clique com botão direito do mouse e no menu de contexto, selecione **Excluir**.
3. Quando sua confirmação for solicitada, clique em **OK**. Isso excluirá o modelo de largura de banda. 
4. A lista de modelos de largura de banda é atualizada para refletir a exclusão.

> [!NOTE]
> Você não poderá salvar as alterações se a agenda editada se sobrepor a uma agenda existente no modelo de largura de banda que você está modificando.

## <a name="delete-a-bandwidth-template"></a>Excluir um modelo de largura de banda

Execute as etapas a seguir para excluir um modelo de largura de banda.

#### <a name="to-delete-a-bandwidth-template"></a>Para excluir um modelo de largura de banda

1. Acesse o serviço do Gerenciador de Dispositivos do StorSimple e clique em **Modelos de largura de banda**.
2. Na lista de modelos de largura de banda, selecione o modelo que você deseja excluir. Clique com botão direito do mouse e, no menu de contexto, selecione Excluir.
3. Quando sua confirmação for solicitada, clique em **OK**. Isso excluirá o modelo de largura de banda.
4. A lista de modelos de largura de banda é atualizada para refletir a exclusão.

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

Com os dados de taxa de transferência da rede, identifique a hora do dia e os contêineres de volume em que ocorre o gargalo da rede. Se isso ocorre quando dados estão sendo dispostos em camada na nuvem (obtenha essa informação do desempenho de E/S de todos os contêineres de volume do dispositivo para nuvem), você precisará modificar os modelos de largura de banda associados aos seus contêineres de volume.

Depois que os modelos modificados estiverem em uso, você precisara monitorar a rede novamente em busca de latências significativas. Se elas ainda existirem, você precisará rever seus modelos de largura de banda.

**P**. O que acontece se vários contêineres de volume em meu dispositivo tiverem agendas que se sobrepõem, mas diferentes limites se aplicarem a cada uma?

**R**. Vamos supor que você tenha um dispositivo com 3 contêineres de volume. As agendas associadas a esses contêineres se sobrepõem completamente. Para cada um desses contêineres, os limites de largura de banda usados são 5, 10 e 15 Mbps, respectivamente. Quando a E/S estiver ocorrendo em todos esses contêineres ao mesmo tempo, no mínimo três limites de largura de banda poderão ser aplicados; nesse caso, 5 Mbps, pois essas solicitações de E/S de saída compartilham a mesma fila.

## <a name="best-practices-for-bandwidth-templates"></a>Práticas recomendadas para modelos de largura de banda

Siga estas práticas recomendadas para seu dispositivo StorSimple:

* Configure modelos de largura de banda em seu dispositivo para habilitar a limitação variável da taxa de transferência da rede pelo dispositivo em diferentes horas do dia. Esses modelos de largura de banda quando usados com agendas de backup podem aproveitar eficientemente largura de banda de rede adicionais para operações de nuvem fora dos horários de pico.
* Calcule a largura de banda real necessária para uma implantação específica com base no tamanho da implantação e no RTO (objetivo de tempo de recuperação) necessário.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como [usar o serviço Gerenciador de Dispositivos do StorSimple para administrar o dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

