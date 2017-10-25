---
title: Exibir e gerenciar alertas de Matriz Virtual do Microsoft Azure StorSimple | Microsoft Docs
description: "Descreve as condições de alerta e gravidade da Matriz Virtual StorSimple, e como usar o serviço StorSimple Manager para gerenciar alertas."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 97ee25a1-0ec3-4883-9a0a-54b722598462
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3062c7c060c3730c5edef3ada16f3a5077cf5558
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-storsimple-device-manager-to-manage-alerts-for-the-storsimple-virtual-array"></a>Usar o Gerenciador de Dispositivos do StorSimple para gerenciar alertas do StorSimple Virtual Array

## <a name="overview"></a>Visão geral

O recurso de alertas no serviço Gerenciador de Dispositivo do StorSimple fornece uma maneira para examinar e limpar os alertas relacionados à Matriz Virtual do StorSimple em tempo real. Use os alertas na folha **Resumo do serviço** para monitorar de forma centralizada os problemas de integridade de suas Matrizes Virtuais do StorSimple e a solução geral do Microsoft Azure StorSimple.

Este tutorial descreve como configurar notificações de alerta, condições de alerta comuns, níveis de gravidade do alerta e como exibir e controlar os alertas. Além disso, ele inclui tabelas de referência rápida de alerta, que permitem que você localize um alerta específico rapidamente e responda o mesmo adequadamente.

![Página de alertas](./media/storsimple-virtual-array-manage-alerts/alerts1.png)

## <a name="configure-alert-settings"></a>Definir configurações de alerta

Você pode escolher se deseja ser notificado por email das condições de alerta para cada uma das Matrizes Virtuais do StorSimple. Além disso, você pode identificar outros destinatários da notificação de alerta, digitando seus endereços de email na caixa **Destinatários de email adicionais** , separados por ponto e vírgula.

> [!NOTE]
> Você pode inserir um máximo de 20 endereços de email por matriz virtual.


Depois de habilitar a notificação por email para uma matriz virtual, os membros da lista de notificação receberão uma mensagem de email sempre que um alerta crítico ocorrer. As mensagens serão enviadas por *storsimple-alerts-noreply@mail.windowsazure.com* e descreverão a condição de alerta. Os destinatários podem clicar em **Cancelar inscrição** para serem removidos da lista de notificação de email.

#### <a name="to-enable-email-notification-for-alerts"></a>Para habilitar a notificação por email para alertas

1. Acesse seu serviço do Gerenciador de Dispositivos do StorSimple e, na seção **Gerenciamento**, selecione e clique em **Dispositivos**. Na lista de dispositivos exibida, selecione e clique em seu dispositivo.
   
    ![Configurações de alerta](./media/storsimple-virtual-array-manage-alerts/alerts2.png)
2. Isso abrirá a folha **Configurações**. Na seção **Configurações do dispositivo**, selecione **Geral**. Isso abrirá a folha **Configurações Gerais**.
   
    ![configuração da notificação de alertas](./media/storsimple-virtual-array-manage-alerts/alerts4.png)
3. Na folha **Configurações Gerais**, acesse a seção **Configurações de alerta** e defina o seguinte:
   
   1. No campo **Habilitar notificação por email**, selecione **SIM**.
   2. No campo **Administradores do serviços de email**, selecione **SIM** se deseja que o administrador de serviços e todos os coadministradores recebam as notificações de alerta.
   3. No campo **Destinatários de email adicionais**, insira os endereços de email de todos os outros destinatários que devem receber as notificações de alerta. Insira os nomes no formato *someone@somewhere.com*. Use ponto e vírgula para separar os endereços de email. Você pode configurar um máximo de 20 endereços de email por dispositivo virtual.
      
       ![configuração da notificação de alertas](./media/storsimple-virtual-array-manage-alerts/alerts6.png)
   4. Para enviar uma notificação de email de teste, clique em **Enviar email de teste**. O serviço Gerenciador de Dispositivo do StorSimple exibirá mensagens de status conforme ele encaminha a notificação de teste.
      
       ![Email de notificação de teste de alertas enviado](./media/storsimple-virtual-array-manage-alerts/alerts7.png)
      
      > [!NOTE]
      > Se a mensagem de notificação de teste não pode ser enviada, o serviço Gerenciador de Dispositivo do StorSimple exibirá uma mensagem apropriada. Clique em **OK**, aguarde alguns minutos e, em seguida, tente enviar a mensagem de notificação de teste novamente.
      > 
      > 
   5. Na parte inferior da página, clique em **Salvar** para salvar sua configuração. Quando solicitado a confirmar, clique em **Sim**.
      
      ![Email de notificação de teste de alertas enviado](./media/storsimple-virtual-array-manage-alerts/alerts10.png)

## <a name="common-alert-conditions"></a>Condições comuns de alerta

A matriz Virtual StorSimple gera alertas em resposta a uma variedade de condições. A seguir estão os tipos mais comuns de condições de alerta:

* **Problemas de conectividade** – esses alertas ocorrem quando há dificuldade na transferência de dados. Podem ocorrer problemas de comunicação durante a transferência de dados de e para a conta de armazenamento do Azure ou devido à falta de conectividade entre os dispositivos virtuais e o serviço Gerenciador de Dispositivo do StorSimple. Problemas de comunicação são alguns dos mais difíceis de corrigir porque há muitos pontos de falha. Você deve sempre verificar primeiro se a conectividade de rede e o acesso à Internet estão disponíveis antes de continuar com a solução de problemas mais avançados. Para obter informações sobre portas e configurações de firewall, vá para [Requisitos do sistema StorSimple Virtual Array](storsimple-ova-system-requirements.md). Para obter ajuda com a solução de problemas, acesse [Solucionar problemas com o cmdlet Test-Connection](storsimple-troubleshoot-deployment.md).
* **Problemas de desempenho** – esses alertas são causados quando o sistema não estiver sendo executado corretamente, como quando ele está sob uma carga pesada.

Além disso, você pode ver alertas relacionados à segurança, atualizações ou falhas no trabalho.

## <a name="alert-severity-levels"></a>Níveis de severidade do alerta

Alertas têm diferentes níveis de severidade, dependendo do impacto que terá a situação de alerta e a necessidade de uma resposta ao alerta. Os níveis de severidade são:

* **Crítico** – esse alerta ocorre em resposta a uma condição que está afetando o desempenho correto do seu sistema. Uma ação é necessária para garantir que o serviço StorSimple não seja interrompido.
* **Aviso** – essa condição poderá se tornar crítica se não for resolvida. Você deve investigar a situação e realizar qualquer ação necessária para resolver o problema.
* **Informações** – esse alerta contém informações que podem ser úteis para controlar e gerenciar seu sistema.

## <a name="view-and-track-alerts"></a>Exibir e controlar os alertas

A folha de resumo do serviço Gerenciador de Dispositivo do StorSimple fornece uma visão rápida do número de alertas em seus dispositivos virtuais, organizados por nível de gravidade.

![Painel de alertas](./media/storsimple-virtual-array-manage-alerts/alerts14.png)

Clique no nível de gravidade para abrir a folha **Alertas**. Os resultados incluem apenas os alertas que correspondem a esse nível de severidade.

![Relatórios de alerta limitados ao tipo de alerta](./media/storsimple-virtual-array-manage-alerts/alerts15.png)

Clique em um alerta na lista para obter detalhes adicionais sobre o mesmo, incluindo a última vez em que o alerta foi relatado, o número de ocorrências do alerta no dispositivo e a ação recomendada para resolver o alerta.

![Lista de alertas e detalhes](./media/storsimple-virtual-array-manage-alerts/alerts16.png)

Você pode copiar os detalhes do alerta para um arquivo de texto se precisar enviar as informações ao Suporte da Microsoft. Depois de seguir a recomendação e resolver a condição do alerta local, limpe o alerta da lista. Selecione o alerta na lista e clique em **Limpar**. Para limpar vários alertas, selecione cada alerta, clique em qualquer coluna exceto na coluna **Alerta** e clique em **Limpar** após ter selecionado todos os alertas a serem limpos.

Ao clicar em **Limpar**, você terá a oportunidade de fornecer comentários sobre o alerta e as etapas que você seguiu para resolver o problema. 

![comentários de alerta](./media/storsimple-virtual-array-manage-alerts/alerts17.png)

Alguns eventos serão removidos pelo sistema se outro evento for disparado com novas informações. 

## <a name="sort-and-review-alerts"></a>Classificação e análise de alertas

A folha **Alertas** pode exibir até 250 alertas. Se você excedeu o número de alertas, nem todos os alertas serão exibidos no modo de exibição padrão. Você pode combinar os campos a seguir para personalizar quais alertas são exibidos:

* **Status** – Você pode exibir alertas **Ativos** ou **Removidos**. Alertas ativos ainda estão sendo disparados em seu sistema, embora os alertas removidos tenham sido limpos manualmente por um administrador ou por meio de programação porque o sistema atualizou a condição de alerta com novas informações.
* **Severidade** – Você pode exibir alertas de todos os níveis de severidade (críticos, avisos, informações) ou apenas uma determinada severidade, como, por exemplo, somente alertas críticos.
* **Fonte** – Você pode exibir alertas de todas as fontes ou limitar os alertas para aqueles que vêm de serviço ou de um ou todos os dispositivos virtuais.
* **Intervalo de tempo** – Especificando os carimbos de data e hora **De** e **Para**, você pode examinar os alertas durante o período de tempo desejado.

## <a name="alerts-quick-reference"></a>Referência rápida de alertas

As tabelas a seguir listam alguns dos alertas do StorSimple que você pode encontrar, bem como recomendações e informações adicionais quando disponíveis. Os alertas da Matriz Virtual do StorSimple se encaixam em uma das seguintes categorias:

* [Alertas de conectividade de nuvem](#cloud-connectivity-alerts)
* [Alertas de configuração](#configuration-alerts)
* [Alertas de falha nos trabalhos](#job-failure-alerts)
* [Alertas de desempenho](#performance-alerts)
* [Alertas de segurança](#security-alerts)
* [Alertas de atualização](#update-alerts)

### <a name="cloud-connectivity-alerts"></a>Alertas de conectividade de nuvem

| Texto de alerta | Evento | Mais informações / ações recomendadas |
|:--- |:--- |:--- |
| O dispositivo *<device name>* não está conectado à nuvem. |O dispositivo nomeado não pode se conectar à nuvem. |Não foi possível conectar-se à nuvem. Isso pode ser devido a um dos seguintes:<ul><li>Pode haver um problema com as configurações de rede no seu dispositivo.</li><li>Pode haver um problema com as credenciais da conta de armazenamento.</li></ul>Para obter mais informações sobre como solucionar problemas de conectividade, vá para a [interface do usuário da web local](storsimple-ova-web-ui-admin.md) do dispositivo. |

### <a name="configuration-alerts"></a>Alertas de configuração

| Texto de alerta | Evento | Mais informações / ações recomendadas |
|:--- |:--- |:--- |
| Configuração do dispositivo virtual local sem suporte. |Desempenho lento. |A configuração atual pode resultar em degradação de desempenho. Certifique-se de que o servidor atenda aos requisitos mínimos de configuração. Para saber mais, acesse os [requisitos do StorSimple Virtual Array](storsimple-ova-system-requirements.md). |
| Você está com pouco espaço em disco provisionado em <*nome do dispositivo*>. |Aviso de espaço em disco. |Você está com pouco espaço em disco provisionado. Para liberar espaço, considere mover cargas de trabalho para outro volume ou compartilhar ou excluir dados. |

### <a name="job-failure-alerts"></a>Alertas de falha nos trabalhos

| Texto de alerta | Evento | Mais informações / ações recomendadas |
|:--- |:--- |:--- |
| O backup de <*nome do dispositivo*> não pôde ser concluído. |Falha no trabalho de backup. |Não foi possível criar um backup. Considere o seguinte:<ul><li>Problemas de conectividade podem estar impedindo que a operação de backup seja concluída com êxito. Certifique-se de que não haja nenhum problema de conectividade. Para obter mais informações sobre solução de problemas de conectividade, vá para a [interface do usuário da Web local](storsimple-ova-web-ui-admin.md) do seu dispositivo virtual.</li><li>Você atingiu o limite de armazenamento disponível. Para liberar espaço, considere a exclusão de todos os backups que não são mais necessários.</li></ul> Resolva os problemas, limpe o alerta e repita a operação. |
| A clonagem de <*nome do dispositivo*> não pôde ser concluída. |Falha no trabalho de clonagem. |Não foi possível criar um clone. Considere o seguinte:<ul><li>sua lista de backup pode não ser válida. Atualize a lista para verificar se ela ainda é válida.</li><li>Problemas de conectividade podem estar impedindo que a operação de clonagem seja concluída com êxito. Certifique-se de que não haja nenhum problema de conectividade.</li><li>Você atingiu o limite de armazenamento disponível. Para liberar espaço, considere a exclusão de todos os backups que não são mais necessários.</li></ul>Resolva os problemas, limpe o alerta e repita a operação. |

### <a name="performance-alerts"></a>Alertas de desempenho

| Texto de alerta | Evento | Mais informações / ações recomendadas |
|:--- |:--- |:--- |
| Você está sofrendo atrasos inesperados na transferência de dados. |Transferência de dados lenta. |Erros de limitação acontecem quando você excede os alvos de escalabilidade de um serviço de armazenamento. O serviço de armazenamento faz isso para garantir que nenhum cliente ou locatário possa usar o serviço a custos de outros. Para saber mais sobre a solução de problemas de sua conta de armazenamento do Azure, vá para [Monitorar, diagnosticar e solucionar problemas do Armazenamento do Microsoft Azure](../storage/common/storage-monitoring-diagnosing-troubleshooting.md). |
| Há pouco espaço local de reserva em disco no <*nome do dispositivo*>. |Tempo de resposta lento. |10% do tamanho total provisionado para <*nome do dispositivo*> é reservado no dispositivo local e agora está acabando o espaço reservado. A carga de trabalho de <*nome do dispositivo*> está gerando uma maior taxa de variação ou você pode ter migrado recentemente uma grande quantidade de dados. Isso pode resultar em desempenho reduzido. Considere uma das ações a seguir para resolver esse problema:<ul><li>Aumentar a largura de banda de nuvem para este dispositivo.</li><li>Reduzir ou mover cargas de trabalho para outro volume ou compartilhamento.</li></ul> |

### <a name="security-alerts"></a>Alertas de segurança

| Texto de alerta | Evento | Mais informações / ações recomendadas |
|:--- |:--- |:--- |
| A senha de <*nome do dispositivo*> expirará em <*número*> dias. |Aviso de senha. |Sua senha expirará em < número> dias. Considere alterar sua senha. Para saber mais, vá para [Alterar a senha de administrador do dispositivo StorSimple Virtual Array](storsimple-virtual-array-change-device-admin-password.md). |

### <a name="update-alerts"></a>Alertas de atualização

| Texto de alerta | Evento | Mais informações / ações recomendadas |
|:--- |:--- |:--- |
| Novas atualizações estão disponíveis para seu dispositivo. |Estão disponíveis atualizações para a Matriz Virtual StorSimple. |Você pode instalar novas atualizações da página **Manutenção** . |
| Não foi possível verificar novas atualizações do <*nome do dispositivo*>. |Falha de atualização. |Ocorreu um erro durante a instalação de novas atualizações. Você pode instalar manualmente as atualizações. Se o problema persistir, contate o [Suporte da Microsoft](storsimple-contact-microsoft-support.md). |

## <a name="next-steps"></a>Próximas etapas

* [Saiba mais sobre o StorSimple Virtual Array](storsimple-ova-overview.md).

