# Cercar automatització puntual a Linux

## 1. Fem una primera cerca
```bash
    journalctl -u atd --since "30 days ago"
    # journalctl: És l’eina que permet consultar els logs del sistema quan aquest 
    # sistema utilitza systemd  

    # systemd és el cap del sistema Linux: arrenca el sistema i controla què s'executa, 
    # quan i com

    # -u atd: -u vol dir unit (unitat de systemd), atd és el servei que 
    # executa tasques puntuals. Filtra els logs només del servei atd

    # --since
    #   --since yesterday
    #   --since "2026-02-01"
    #   --since "2 hours ago"
```

Resultat > no troba res

## 2. Creem una tasca puntual de demo
```bash
    # opcional: veure tasques puntuals pendents
    atq

    # crear una tasca puntual (s’executa un sol cop)
    echo "echo 'Hola, tasca puntual' >> /home/javier/tasca_at.log" | at now + 1 minute

    # opcional - comprovar que ha quedat programada
    atq

    # després d’1 minut, comprovar evidència (output)
    cat /home/javier/tasca_at.log

    # opcional - comprovar evidència al log del servei atd
    journalctl -u atd --since "5 minutes ago"
```
