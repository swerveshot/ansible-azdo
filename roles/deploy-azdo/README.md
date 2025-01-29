# Provision Azure DevOps Server 2022

Deze rol installeert Azure DevOps Server 2022

## Vereisten
* Database provisioning is uitgevoerd
* Installation files zijn uitgepakt

## To-do
* Voeg logica toe die voorkomt dat de tweede server gaat installeren voordat de eerste klaar is
* Maak lijst van powershell argumenten beter leesbaar door deze op te knippen
* Selectie van primaire en opeenvolgende AZDO servers aanpassen van ansible_play_hosts_all[0] naar een host variabele
* Aanmaken default collection database
  * Momenteel maakt de installer een database aan voor de default collection, maar voert twee ongewenste acties uit:
    * voegt daarbij ook het AZDO service account toe aan de master database
    * maakt een custom rol TFSEXEROLE aan
* SSH ja of nee
* Cert voor SSL/TLS
  * Self signed voor POC
  * CA Cert voor overige omgevingen
* Hoe het best te controleren of AZDO Server al geïnstalleerd is
  * De win_iis_website module is **geen** goede keuze omdat deze afhankelijk is van een powershell module die pas aanwezig is als de IIS rol is geïnstalleerd. Je kunt dan zoiets doen als dit:

        - name: "Check if Azure DevOps Server website is started"
          win_iis_website:
             name: "Azure DevOps Server"
             state: "started"
           register: iis_website
  * Momenteel is de win_reg_stat module gebruikt omdat bij een succesvolle installatie een key IsConfigured wordt aanmaakt met een waarde 1 in `HKLM:\SOFTWARE\Microsoft\TeamFoundationServer\19.0\InstalledComponents\ApplicationTier`.

        - name: "Check status of IsConfigured regkey"
          win_reg_stat:
            path: "{{ azdo_apptier_regkey }}"
            name: "IsConfigured"
          register: isconfigured

## Gereed
* Aanpassen controle voor uitgepakte files.
  * Gebeurt nu op basis van een file locatie
  * Bij voorkeur aanpassen naar controle van add/remove programs