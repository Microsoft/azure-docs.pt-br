<!--author=alkohli last changed: 02/06/17-->

#### <a name="to-install-an-update-from-the-azure-portal"></a>Para instalar uma atualização por meio do portal do Azure

1. Na página do serviço StorSimple, selecione seu dispositivo. Navegue até **Dispositivos** > **Manutenção**.
2. Na parte inferior da página, clique em **Verificar atualizações**. Um trabalho é criado para verificar se há atualizações disponíveis. Você será notificado quando o trabalho for concluído com êxito.
3. Na seção de **Atualizações de software** na mesma página, as atualizações de software estão disponíveis. É recomendável examinar as notas de versão antes de aplicar uma atualização em seu dispositivo.
4. Na parte inferior da página, clique em **Instalar Atualizações** e em **OK**.
5. Na caixa de diálogo **Instalar atualizações**, certifique-se de que você seguiu as recomendações e selecione **Entendo o requisito acima e estou pronto para atualizar meu dispositivo** e clique no botão de seleção.
   
    ![Mensagem de confirmação](./media/storsimple-install-update2-via-portal/InstallUpdate12_2M.png)
6. Um conjunto de verificações de pré-requisito é iniciado. Essas verificações incluem:
   
   * **Verificações de integridade do controlador** para verificar se ambos os controladores do dispositivo estão em boas condições e online.
   * **Verificações de integridade do componente de hardware** para verificar se todos os componentes de hardware no dispositivo StorSimple estão em boas condições.
   * **Verificações de DATA 0** para verificar se DATA 0 está habilitada no dispositivo. Se essa interface não estiver habilitada, você deverá habilitá-la e repetir a ação.
   * **Verificações de DATA 2 e DATA 3** para verificar se as interfaces de rede DATA 2 e DATA 3 não estão habilitadas. Se essas interfaces estiverem habilitadas, você deverá desabilitá-las e tentar atualizar o dispositivo. Essa verificação será realizada somente se você estiver atualizando de um dispositivo que está executando o software de disponibilidade geral. Os dispositivos que executam versões 0.1, 0.2 ou 0.3 não precisarão dessa verificação.
   * **Verificação de gateway** em qualquer dispositivo executando uma versão anterior à Atualização 1. Essa verificação é realizada em todos os dispositivos que executam o software de pré-atualização 1, mas falha em dispositivos com um gateway configurado para uma interface de rede diferente de DATA 0.
     
     A atualização será aplicada se todas as verificações forem concluídas com êxito. Você será notificado quando as verificações estiverem em andamento.
     
     ![Notificação de pré-verificação](./media/storsimple-install-update2-via-portal/InstallUpdate12_3M.png)
     
     Veja abaixo um exemplo de falha nas verificações. Será necessário verificar se os dois controladores do dispositivo estão em boas condições e online. Também será necessário verificar a integridade dos componentes de hardware. Neste exemplo, os componentes do Controlador 0 e do Controlador 1 precisam de atenção. Talvez seja necessário entrar em contato com o Suporte da Microsoft se você não conseguir resolver esses problemas por conta própria.
     
       ![Falha nas verificações](./media/storsimple-install-update2-via-portal/HCS_PreUpgradeChecksFailed-include.png)
7. Depois que as verificações tiverem sido concluídas com êxito, será criado um trabalho de atualização. Você será notificado quando o trabalho de atualização for criado com êxito.
   
    ![Criação do trabalho de atualização](./media/storsimple-install-update2-via-portal/InstallUpdate12_44M.png)
   
    Em seguida, a atualização será aplicada ao seu dispositivo.
    
8. Para monitorar o andamento do trabalho de atualização, clique em **Exibir trabalho**. Na página **Trabalhos** , você pode ver o andamento da atualização.
9. A atualização demora algumas horas para ser concluída. Selecione o trabalho de atualização e clique em **Detalhes** para exibir os detalhes do trabalho a qualquer momento.
10. Depois que o trabalho for concluída, navegue até a página de **Manutenção** e role para baixo até **Atualizações de software**.

