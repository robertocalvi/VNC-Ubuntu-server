# VNC-Ubuntu-server

## Come installare VNC Server su Ubuntu

In questo tutorial, imparerai come installare VNC su Ubuntu per accedere al desktop grafico da un computer remoto.

VNC (Virtual Network Computing) è un'utilità di controllo remoto multipiattaforma che utilizza il protocollo Remote Frame Buffer (RFB), è un modo per condividere un desktop grafico sulla rete, simile a Remote Desktop su Microsoft Windows.

Puoi usare la seguente guida per installare il server VNC sia su Ubuntu 22.04 LTS che su Ubuntu 20.04 LTS.

Avviare automaticamente il server VNC all'avvio

Per installare e configurare VNC Server su Ubuntu 20.04, eseguire i seguenti passaggi:

- Apri il terminale Ubuntu e usa i seguenti comandi per installare la sessione desktop Xfce:
```
sudo apt-get update
sudo apt-get install xfce4 xfce4-session
```

Useremo il desktop Xfce per la sessione VNC (che funziona perfettamente con Ubuntu Server). Selezionare gdm3 se viene richiesto di impostare il gestore del display.

Una volta installato Xfce, installa il pacchetto tightvncserver su Ubuntu:

`sudo apt-get install tightvncserver -y`

Al termine dell'installazione, avviare il server VNC per la prima volta (IMPORTANTE: è necessario avviare il server VNC utilizzando l'account utente che verrà utilizzato per accedere a VNC, non dall'account root. Pertanto, non eseguire il seguente comando come utente root):

`vncserver`

Quando si esegue il comando vncserver per la prima volta, verrà richiesto di creare una password per la connessione VNC. Creerà anche i file di configurazione necessari per il server VNC.

Successivamente, dobbiamo modificare il file di configurazione di avvio. Per prima cosa, arrestare il server VNC con il comando kill:

`vncserver -kill :1`

Aprire il file ~/.vnc/xstartup:

`nano ~/.vnc/xstartup`

E assicurati che il file xstartup sia simile alla seguente configurazione:

```
#!/bin/sh
unset SESSION_MANAGER
[ -x /etc/vnc/xstartup ] && exec /etc/vnc/xstartup
[ -r $HOME/.Xresources ] && xrdb $HOME/.Xresources
startxfce4 &
```

Salvare il file di configurazione e avviare il server VNC:

`vncserver :1 -geometry 1366x768 -depth 24`

La sessione remota utilizzerà la risoluzione 1366x768 e l'ID desktop è 1.

L'utente ha bisogno di un VNC Viewer per connettersi a Ubuntu dal proprio computer locale. Gli utenti di Ubuntu possono utilizzare il viewer tigervnc o il visualizzatore desktop remoto Vinagre. Per Microsoft Windows, è possibile utilizzare il visualizzatore RealVNC.

client VNC del visualizzatore tigervnc

![vnc-viewer](https://user-images.githubusercontent.com/20637640/219047945-af733f30-f7b5-4482-8d4e-4b00cd177f2a.jpeg)

Inserisci l'indirizzo IP del tuo server Ubuntu e il numero di desktop VNC da collegare.

![ubuntu-vnc-server](https://user-images.githubusercontent.com/20637640/219047592-ed8c646a-15b4-4e75-b69f-4c1e0768c4e9.jpeg)

Installa VNC Server su Ubuntu

Più utenti possono collegare il sistema Ubuntu e lavorare contemporaneamente, ma è necessario avviare più sessioni VNC con diversi ID desktop. Ad esempio, il seguente comando avvierà una sessione VNC con ID desktop 2:

`vncserver :2 -geometry 1366x768 -depth 24`

Per arrestare manualmente il server VNC su Ubuntu, eseguire il comando kill seguito dall'ID desktop:

`vncserver -kill :1`

Per modificare la password VNC, eseguire il seguente comando:

`vncpasswd`
Avviare automaticamente il server VNC all'avvio

Dobbiamo creare un file systemd unit per avviare automaticamente il server VNC quando il server Ubuntu si riavvia. Nell'esempio seguente verrà creata una nuova unità systemd con ID desktop 1.

Per creare un'unità systemd, creare un file chiamato `vncserver@1.service` all'interno della cartella `/etc/systemd/system`  e aggiungere la seguente configurazione (cambiare User=TUO_UTENTE con il proprio nome utente Linux).

```
Unit]
Description=Start VNC Server at startup With Desktop ID 1.
After=multi-user.target network.target

[Service]
Type=forking
User=your_username
ExecStartPre=/bin/sh -c '/usr/bin/vncserver -kill :%i > /dev/null 2>&1 || :'
ExecStart=/usr/bin/vncserver :%i -geometry 1366x768 -depth 24
ExecStop=/usr/bin/vncserver -kill :%i

[Install]
WantedBy=multi-user.target
```

Quindi ricaricare il systemd manager e abilitare `vncservervncserver@1.service`:

```
sudo systemctl daemon-reload
sudo systemctl enable vncserver@1.service
```

# Riepilogo

In questo tutorial, abbiamo imparato come installare VNC su Ubuntu 20.04/18.04 LTS. VNC è un'implementazione multipiattaforma di Remote Desktop Server che utilizza il protocollo Remote Frame Buffer (RFB).

Il modo in cui funziona, il server VNC viene eseguito su Ubuntu Server/Desktop e l'applicazione client VNC viene eseguita sul computer da cui si desidera accedere al desktop remoto.
