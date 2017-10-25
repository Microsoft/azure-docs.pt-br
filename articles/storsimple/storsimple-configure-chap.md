---
title: "Configurar o CHAP para o dispositivo StorSimple série 8000 | Microsoft Docs"
description: Descreve como configurar o CHAP (Challenge Handshake Authentication Protocol) em um dispositivo StorSimple.
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 467044d7-7885-4382-90bd-3148dbbd341f
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: TBD
ms.date: 08/17/2016
ms.author: alkohli
ms.openlocfilehash: 36b4e73d0336deb9560d44163fc5330d1c9d775c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="configure-chap-for-your-storsimple-device"></a>Configure o CHAP para seu dispositivo StorSimple
Este tutorial explica como configurar o CHAP para seu dispositivo StorSimple. O procedimento detalhado neste artigo se aplica ao StorSimple série 8000, bem como dispositivos StorSimple 1200.

CHAP significa Challenge Handshake Authentication Protocol. É um esquema de autenticação usado pelos servidores para validar a identidade de clientes remotos. A verificação baseia-se em uma senha ou segredo compartilhado. O protocolo CHAP pode ser unidirecional ou bidirecional (mútuo). O CHAP unidirecional é quando o destino autentica um iniciador. CHAP mútuo ou inverso, por outro lado, requer que o destino autentique o iniciador e, em seguida, o iniciador autentique o destino. A autenticação do iniciador pode ser implementada sem a autenticação do destino. No entanto, a autenticação do destino pode ser implementada somente se a autenticação do iniciador também for implementada. 

Como prática recomendada, é sugerimos usar o CHAP para aumentar a segurança do iSCSI.

> [!NOTE]
> Tenha em mente que não há suporte para IPSEC no momento em dispositivos StorSimple.
> 
> 

As configurações do CHAP no dispositivo StorSimple podem ser definidas das seguintes maneiras:

* Autenticação unidirecional ou de uma via
* Autenticação inversa bidirecional ou mútua

Em cada um desses casos, o Portal para o dispositivo e o software do iniciador iSCSI do servidor precisam ser configurados. O tutorial a seguir descreve as etapas detalhadas para essa configuração.

## <a name="unidirectional-or-one-way-authentication"></a>Autenticação unidirecional ou de uma via
Na autenticação unidirecional, o destino autentica o iniciador. Essa autenticação exige que você defina as configurações do iniciador CHAP no dispositivo StorSimple e o software Iniciador iSCSI no host. Os procedimentos detalhados para seu dispositivo StorSimple e o host do Windows são descritos a seguir.

#### <a name="to-configure-your-device-for-one-way-authentication"></a>Para configurar seu dispositivo para autenticação unidirecional
1. No Portal clássico do Azure, na página **Dispositivos**, clique na guia **Configurar**.
   
    ![Iniciador CHAP](./media/storsimple-configure-chap/IC740943.png)
2. Role para baixo nesta página e, na seção **Iniciador CHAP** :
   
   1. Forneça um nome de usuário para o iniciador CHAP.
   2. Forneça uma senha para o iniciador CHAP.
      
    > [!IMPORTANT]
    > O nome de usuário do CHAP deve conter menos de 233 caracteres. A senha do CHAP deve conter entre 12 e 16 caracteres. Um nome de usuário ou senha mais longo resultará em falha de autenticação no host do Windows.
   
   3. Confirme a senha.
3. Clique em **Salvar**. Uma mensagem de confirmação será exibida. Clique em **OK** para salvar as alterações.

#### <a name="to-configure-one-way-authentication-on-the-windows-host-server"></a>Para configurar a autenticação unidirecional no servidor de host do Windows
1. No servidor de host do Windows, inicie o Iniciador iSCSI.
2. Na janela **Propriedades do Iniciador iSCSI** , execute as seguintes etapas:
   
   1. Clique na guia **Descoberta** .
      
       ![Propriedades do Iniciador iSCSI](./media/storsimple-configure-chap/IC740944.png)
   2. Clique em **Descobrir Portal**.
3. Na caixa de diálogo **Descobrir Portal de Destino** :
   
   1. Especifique o endereço IP do seu dispositivo.
   2. Clique em **Avançado**.
      
       ![Descobrir Portal de Destino](./media/storsimple-configure-chap/IC740945.png)
4. Na caixa de diálogo **Configurações Avançadas** :
   
   1. Marque a caixa de seleção **Habilitar logon CHAP** .
   2. No campo **Nome** , forneça o nome de usuário especificado para o Iniciador CHAP no Portal clássico.
   3. No campo **Segredo de destino** , forneça a senha especificada para o Iniciador CHAP no Portal clássico.
   4. Clique em **OK**.
      
       ![Configurações gerais avançadas](./media/storsimple-configure-chap/IC740946.png)
5. Na guia **Destinos** da janela **Propriedades do Iniciador iSCSI**, o status do dispositivo deve aparecer como **Conectado**. Se você estiver usando um dispositivo StorSimple 1200, cada volume será montado como um destino iSCSI conforme mostrado abaixo. Portanto, as etapas 3 a 4 precisarão ser repetidas para cada volume.
   
    ![Volumes montados como destinos separados](./media/storsimple-configure-chap/chap4.png)
   
   > [!IMPORTANT]
   > Se você alterar o nome iSCSI, o novo nome será usado para novas sessões do iSCSI. Novas configurações não são usadas para sessões existentes até que você faça logoff e logon novamente.
   > 
   > 

Para saber mais sobre como configurar o CHAP no servidor de host do Windows, vá para [Considerações adicionais](#additional-considerations).

## <a name="bidirectional-or-mutual-authentication"></a>Autenticação bidirecional ou mútua
Na autenticação bidirecional, o destino autentica o iniciador e, em seguida, o iniciador autentica o destino. Isso exige que o usuário defina as configurações do iniciador CHAP, bem como as configurações de CHAP inversas no dispositivo e no software Iniciador iSCSI no host. Os procedimentos a seguir descrevem as etapas para configurar a autenticação mútua no dispositivo e no host do Windows.

#### <a name="to-configure-your-device-for-mutual-authentication"></a>Para configurar seu dispositivo para autenticação mútua
1. No Portal clássico do Azure, na página **Dispositivos**, clique na guia **Configurar**.
   
    ![Destino do CHAP](./media/storsimple-configure-chap/IC740948.png)
2. Role para baixo nessa página e, na seção **Destino do CHAP** :
   
   1. Forneça um **Nome de usuário de CHAP reverso** para seu dispositivo.
   2. Forneça uma **Senha de CHAP reversa** para seu dispositivo.
   3. Confirme a senha.
3. Na seção **Iniciador CHAP** :
   
   1. Forneça um **nome de usuário** para seu dispositivo.
   2. Forneça uma **senha** para seu dispositivo.
   3. Confirme a senha.
4. Clique em **Salvar**. Uma mensagem de confirmação será exibida. Clique em **OK** para salvar as alterações.

#### <a name="to-configure-bidirectional-authentication-on-the-windows-host-server"></a>Para configurar a autenticação bidirecional no servidor de host do Windows
1. No servidor de host do Windows, inicie o Iniciador iSCSI.
2. Na janela **Propriedades do Iniciador iSCSI**, clique na guia **Configuração**.
3. Clique em **CHAP**.
4. Na caixa de diálogo **Segredo do CHAP Mútuo do Iniciador iSCSI** :
   
   1. Digite a **Senha do CHAP Reversa** configurada no Portal clássico do Azure.
   2. Clique em **OK**.
      
       ![Segredo do CHAP Mútuo do Iniciador iSCSI](./media/storsimple-configure-chap/IC740949.png)
5. Clique na guia **Destinos** .
6. Clique no botão **Conectar** . 
7. Na caixa de diálogo **Conectar ao Destino**, clique em **Avançado**.
8. Na caixa de diálogo **Propriedades Avançadas** :
   
   1. Marque a caixa de seleção **Habilitar logon CHAP** .
   2. No campo **Nome** , forneça o nome de usuário especificado para o Iniciador CHAP no Portal clássico.
   3. No campo **Segredo de destino** , forneça a senha especificada para o Iniciador CHAP no Portal clássico.
   4. Marque a caixa de seleção **Executar a autenticação mútua** .
      
       ![Configurações avançadas de autenticação mútua](./media/storsimple-configure-chap/IC740950.png)
   5. Clique em **OK** para concluir a configuração CHAP

Para saber mais sobre como configurar o CHAP no servidor de host do Windows, vá para [Considerações adicionais](#additional-considerations).

## <a name="additional-considerations"></a>Considerações adicionais
O recurso **Conexão Rápida** não dá suporte a conexões com o CHAP habilitado. Quando o CHAP estiver habilitado, use o botão **Conectar** disponível na guia **Destinos** para se conectar a um destino.

![Conectar-se ao destino](./media/storsimple-configure-chap/IC740947.png)

Na caixa de diálogo **Conectar-se ao Destino** apresentada, marque a caixa de seleção **Adicionar esta conexão à lista de Destinos Favoritos**. Isso garante que sempre que o computador for reiniciado, seja feita uma tentativa de restaurar a conexão com os destinos favoritos do iSCSI.

## <a name="errors-during-configuration"></a>Erros durante a configuração
Se a sua configuração do CHAP estiver incorreta, provavelmente você verá uma mensagem de erro de **Falha na autenticação** .

## <a name="verification-of-chap-configuration"></a>Verificação da configuração do CHAP
Você pode verificar se o CHAP está sendo usado executando as etapas a seguir.

#### <a name="to-verify-your-chap-configuration"></a>Para verificar a configuração do CHAP
1. Clique em **Destinos Favoritos**.
2. Selecione o destino para o qual você habilitou a autenticação.
3. Clique em **Detalhes**.
   
    ![Destinos favoritos das propriedades do Iniciador iSCSI](./media/storsimple-configure-chap/IC740951.png)
4. Na caixa de diálogo **Detalhes do Destino Favorito**, observe a entrada no campo **Autenticação**. Se a configuração for bem-sucedida, ela deverá dizer **CHAP**.
   
    ![Detalhes do destino favorito](./media/storsimple-configure-chap/IC740952.png)

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre a [segurança do StorSimple](storsimple-security.md).
* Saiba mais sobre o [uso do serviço StorSimple Manager para administrar seu dispositivo StorSimple](storsimple-manager-service-administration.md).

