---
title: Administração de interface do usuário da Web da StorSimple Virtual Array | Microsoft Docs
description: Descreve como executar tarefas de administração básicas do dispositivo por meio da interface do usuário da Web do StorSimple Virtual Array.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: ea65b4c7-a478-43e6-83df-1d9ea62916a6
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 12/1/2016
ms.author: alkohli
ms.openlocfilehash: 92671206a4171ca838423f55b526191ef30e5c35
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60630233"
---
# <a name="use-the-web-ui-to-administer-your-storsimple-virtual-array"></a>Usar a interface do usuário da Web para administrar o StorSimple Virtual Array
![fluxo do processo de instalação](./media/storsimple-ova-web-ui-admin/manage4.png)

## <a name="overview"></a>Visão geral
Os tutoriais descritos neste artigo se aplicam ao Microsoft Azure StorSimple Virtual Array (também conhecido como o dispositivo virtual local StorSimple) que executa a versão GA (disponibilidade geral) de março de 2016. Este artigo descreve alguns dos fluxos de trabalho complexos e tarefas de gerenciamento que podem ser executadas na StorSimple Virtual Array. É possível gerenciar o StorSimple Virtual Array usando a interface do usuário do serviço StorSimple Manager (conhecida como interface do usuário do portal) e a interface de usuário da Web local para o dispositivo. Este artigo se concentra nas tarefas que você pode executar usando a interface do usuário da Web.

Este artigo inclui os seguintes tutoriais:

* Obter a chave de criptografia de dados do serviço
* Solucionar problemas de erros de instalação de interface do usuário da Web
* Gerar um pacote de log
* Desligar ou reiniciar seu dispositivo

## <a name="get-the-service-data-encryption-key"></a>Obter a chave de criptografia de dados do serviço
Uma chave de criptografia de dados de serviço é gerada quando você registra o primeiro dispositivo com o serviço StorSimple Manager. Essa chave é então necessária com a chave de registro do serviço para registrar dispositivos adicionais no serviço StorSimple Manager.

Se você esqueceu sua chave de criptografia de dados de serviço e precisa recuperá-la, execute as seguintes etapas na interface do usuário da Web local do dispositivo registrado com o serviço.

#### <a name="to-get-the-service-data-encryption-key"></a>Para obter a chave de criptografia de dados do serviço
1. Conecte-se à interface do usuário da Web local. Acesse **Configuração** > **Configurações de Nuvem**.
2. Na parte inferior da página, clique em **Obter chave de criptografia de dados de serviço**. Uma chave será exibida. Copie e salve essa chave.
   
    ![obter chave de criptografia de dados do serviço 1](./media/storsimple-ova-web-ui-admin/image27.png)

## <a name="troubleshoot-web-ui-setup-errors"></a>Solucionar problemas de erros de instalação de interface do usuário da Web
Em alguns casos em que você configura o dispositivo por meio da interface do usuário da Web local, você pode encontrar erros. Para diagnosticar e solucionar esses erros, você pode executar os testes de diagnóstico.

#### <a name="to-run-the-diagnostic-tests"></a>Para executar os testes de diagnóstico
1. Na interface do usuário da Web local, acesse **Solução de problemas** > **Testes de diagnóstico**.
   
    ![executar dignóstico 1](./media/storsimple-ova-web-ui-admin/image29.png)
2. Na parte inferior da página, clique em **Executar Testes de Diagnóstico**. Isso iniciará testes para diagnosticar quaisquer possíveis problemas com suas configurações de rede, dispositivo, proxy da Web, de hora ou de nuvem. Você será notificado de que o dispositivo está executando testes.
3. Depois de os testes terem sido concluídos, os resultados serão exibidos. O exemplo a seguir mostra os resultados dos testes de diagnóstico. Observe que as configurações de proxy da Web não foram definidas neste dispositivo e, portanto, o teste de proxy da Web não foi executado. Todos os outros testes para as configurações de rede, servidor DNS e hora foram bem-sucedidas.
   
    ![executar dignóstico 2](./media/storsimple-ova-web-ui-admin/image30.png)

## <a name="generate-a-log-package"></a>Gerar um pacote de log
Um pacote de log é composto de todos os logs relevantes que podem ajudar o Suporte da Microsoft na solução de quaisquer problemas do dispositivo. Nesta versão, um pacote de log pode ser gerado por meio da interface do usuário da Web local.

#### <a name="to-generate-the-log-package"></a>Para gerar o pacote de log
1. Na interface do usuário da Web local, acesse **Solução de problemas** > **Logs do sistema**.
   
    ![gerar pacote de log 1](./media/storsimple-ova-web-ui-admin/image31.png)
2. Na parte inferior da página, clique em **Criar pacote de log**. Será criado um pacote dos logs do sistema. Isso levará alguns minutos.
   
    ![gerar pacote de log 2](./media/storsimple-ova-web-ui-admin/image32.png)
   
    Você será notificado após o pacote ser criado com êxito e a página será atualizada para indicar a hora e data em que o pacote foi criado.
   
    ![gerar pacote de log 3](./media/storsimple-ova-web-ui-admin/image33.png)
3. Clique em **Baixar pacote de log**. Um pacote compactado será baixado em seu sistema.
   
    ![gerar pacote de log 4](./media/storsimple-ova-web-ui-admin/image34.png)
4. Você pode descompactar o pacote de log baixado e exibir os arquivos de log do sistema.

## <a name="shut-down-and-restart-your-device"></a>Desligue e reinicie seu dispositivo
Você pode desligar ou reiniciar seu dispositivo virtual usando a interface do usuário da Web local. Nós recomendamos que antes de reiniciar, você execute os volumes ou compartilhamentos offline no host e, em seguida, no dispositivo. Isso minimizará a possibilidade de dados corrompidos. 

#### <a name="to-shut-down-your-virtual-device"></a>Para desligar o dispositivo virtual
1. Na interface do usuário da Web local, acesse **Manutenção** > **Configurações de energia**.
2. Na parte inferior da página, clique em **Desligar**.
   
    ![desligamento de dispositivo 1](./media/storsimple-ova-web-ui-admin/image36.png)
3. Um aviso será exibido informando que um desligamento do dispositivo interromperá quaisquer E/Ss em andamento, resultando em um tempo de inatividade. Clique no ícone de verificação  ![ícone de verificação](./media/storsimple-ova-web-ui-admin/image3.png).
   
    ![aviso de desligamento de dispositivo](./media/storsimple-ova-web-ui-admin/image37.png)
   
    Você será notificado de que o desligamento foi iniciado.
   
    ![o desligamento de dispositivo foi iniciado](./media/storsimple-ova-web-ui-admin/image38.png)
   
    O dispositivo será agora desligado. Se você deseja iniciar o dispositivo, você precisa fazer isso por meio do Gerenciador do Hyper-V.

#### <a name="to-restart-your-virtual-device"></a>Para reiniciar o dispositivo virtual
1. Na interface do usuário da Web local, acesse **Manutenção** > **Configurações de energia**.
2. Na parte inferior da página, clique em **Reiniciar**.
   
    ![reinicialização do dispositivo](./media/storsimple-ova-web-ui-admin/image36.png)
3. Um aviso será exibido informando que reiniciar o dispositivo interromperá quaisquer E/Ss em andamento, resultando em um tempo de inatividade. Clique no ícone de verificação  ![ícone de verificação](./media/storsimple-ova-web-ui-admin/image3.png).
   
    ![aviso de reinicialização](./media/storsimple-ova-web-ui-admin/image37.png)
   
    Você será notificado de que a reinicialização foi iniciada.
   
    ![reinicialização iniciada](./media/storsimple-ova-web-ui-admin/image39.png)
   
    Enquanto a reinicialização estiver em andamento, você perderá a conexão à interface do usuário. Você pode monitorar a reinicialização atualizando a interface do usuário periodicamente. Como alternativa, você pode monitorar o status de reinicialização do dispositivo por meio do Gerenciador do Hyper-V.

## <a name="next-steps"></a>Próximas etapas
Saiba como [usar o serviço StorSimple Manager para gerenciar seu dispositivo](storsimple-virtual-array-manager-service-administration.md).

