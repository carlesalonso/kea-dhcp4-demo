# Exemple arxiu configuració servei DHCPv4 amb kea

El servei de dhcp *kea* és un servei de codi obert que permet la gestió de serveis de dhcpv4 i dhcpv6. És el reemplaç del servei *isc-dhcp-server* que des del 2022 ja no té suport per part dels seus desenvolupadors.

## Instal·lació

Per instal·lar el servei de dhcpv4 *kea* cal executar la següent comanda:

```bash
sudo apt install kea-dhcp4-server
```

## Configuració

El servei kea ofereix diversos serveis: dhcpv4, dhcpv6, DDNS (DNS dinàmic) i una API per gestionar el servei. En aquest exemple només es configurarà el servei de dhcpv4.

L'arxiu de configuració és `kea-dhcp4.conf`, es tracta d'un arxiu en format JSON.

### Característiques format JSON

Un arxiu JSON utilitzen una notació específica i utilitzen una sèrie d'elements:

- **Claus {}**: serveixen per delimitar els objectes i són obligatòries per iniciar i finalitzar el contingut.
- **Corxets []**: serveixen per delimitar els arrays.
- **Comes ,***: serveixen per separar els elements d'un array o els atributs d'un objecte, per tant, no s'utilitzen al final d'un array o objecte.
- **Dos punts :**: serveixen per separar els noms dels atributs dels seus valors.

### Exemple arxiu `kea-dhcp4.conf`

```json
{
# Aquí comença la configuració per DHCPv4
   "Dhcp4": {

# Indiquem per quina interfície funcionarà el servidor DHCP
        "interfaces-config": {
                "interfaces": [ "enp0s8" ]
                
        },

# Definim els valors de les variables globals
        #valid-lifetime indica la durada en segons de la concesió
        "valid-lifetime": 4000,
        #renew-timer temps per la renovació de la concesió
        "renew-timer": 1000,
        # rebind-timer: temps per eliminar la configuració 
        # si el servidor DHCP  no contesta al renew
        "rebind-timer": 2000,
        
# Indiquem que les concesions les volem guardar a un arxiu, 
# s'indica la ruta de l'arxiu
        "lease-database": {
                "type": "memfile",
                "persist": true,
                "name": "/var/lib/kea/dhcp4.leases"
        },

# Definim les diferents subxarxes que vulguem tenir
        "subnet4": [
                {
                  # Indiquem la subxarxa de treball
                  "subnet": "192.169.1.0/24",
                  # Aquí indicarem quina adreça de porta d'enllaç i 
                  # servidor DNS enviem als clients
                  "option-data": [
                    {
                        "name": "routers",
                        "data": "192.169.1.254"
                    },
                    {
                         "name": "domain-name-servers",
                         "data": "8.8.8.8"
                    }],

                  # Al pool s'indica el conjunt d'adreces dins la subxarxa 
                  # que s'assignaran via DHCP
                   "pools": [ { "pool": "192.169.1.100-192.169.1.199" } ],
                 # Aquí posem la reserva
                   "reservations": [
                     {
                        "hw-address": "08:00:27:57:20:fc",
                        "ip-address": "192.169.1.50"
                    }]
                                
                
              }
        ]
  }

}
```
