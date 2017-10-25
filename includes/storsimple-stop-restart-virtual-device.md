#### <a name="to-stop-and-start-a-virtual-device"></a>Parar e iniciar um dispositivo virtual
Para parar um dispositivo virtual, clique em seu nome e, em seguida, clique em **Desligar**. Enquanto o dispositivo virtual estiver desligando, seu status será **Parando**. Depois que o dispositivo virtual parar, seu status será **Parado**.

Use os seguintes cmdlets para parar e iniciar um dispositivo virtual.

`Stop-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

`Start-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

#### <a name="to-restart-a-virtual-device"></a>Parar e reiniciar um dispositivo virtual
Quando um dispositivo virtual estiver em execução e você quiser reiniciá-lo, clique em seu nome e, em seguida, clique em **Reiniciar**. Enquanto o dispositivo virtual estiver reiniciando, seu status será **Reiniciando**. Quando o dispositivo virtual estiver pronto para uso, seu status será **Em execução**.

Use o seguinte cmdlet para parar e reiniciar um dispositivo virtual.

`Restart-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

