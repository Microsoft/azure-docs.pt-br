<!--author=SharS last changed: 03/17/2016-->

#### <a name="to-install-update-12-from-the-azure-classic-portal"></a>Para instalar a Atualização 1.2 do portal clássico do Azure
1. No portal clássico do Azure, vá até a página **Dispositivos** e selecione seu dispositivo.
2. Navegue até **Dispositivos** > **Configurar**.
3. Em **Interfaces de Rede**, verifique primeiro se você tem pelo menos uma interface de rede que esteja habilitada para iSCSI. Depois localize a interface de rede (diferente de DATA 0) que tem um gateway atribuído.
4. Desabilite a interface de rede que contém um gateway atribuído e salve a configuração modificada. Lembre-se de que as configurações de interface de rede são mantidas e, portanto, quando você reabilitar essa interface de rede mais tarde, o portal será revertido para as configurações originais.
5. Agora você pode [usar o portal clássico do Azure para instalar a Atualização 1.2](#install-update-12-via-the-azure-classic-portal). Siga as instruções a partir da etapa 3 deste procedimento. Depois de instalar todas as atualizações, você pode reabilitar a interface de rede que você desabilitou.

