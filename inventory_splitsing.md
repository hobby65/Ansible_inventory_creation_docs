# Inleiding 
Een inventory is veelal een grote verzameling van parameters die tezamen de basis vormt voor alle machines onder het beheer van een rhaap van een omgeving.  
Dit wordt al snel onoverzichtelijk als je met meerdere teams (organisaties) 1 inventory moet gaan delen, omdat alles er in moet staan.

In het onderstaande verhaal, omschrijven we een mogelijke oplossing om de complexiteit voor de teams te verlagen en toch de verantwoordelijkheid daar te laten, waar deze hoort.

Dit is mogelijk door de inventory te splitsen in delen...
Elk deel krijgt een eigen git repository, waardoor ieder team verantwoording kan dragen voor het deel waarop ze rechten hebben 

## Basis (automation deel)
Dit deel van de inventory heeft alle noodzakelijke variabelen in zich om nodes te kunnen deployen, behalve de machines van de teams zelf. De noodzakalijke infrastructuur componenten, zijn el opgenomen in de base inventory.
Hieronder een overzich van de base structuur:
```
── group_vars
│   ├── all
│   │   ├── env.yml
│   │   └── satellite.yml
│   ├── dev.yml
│   ├── test_env.yml
│   ├── org_1.yml
│   ├── org_2.yml
│   └── vmware.yml
├── hosts.ini
└── host_vars
    ├── controller.rivm.nl.yml
    ├── gitlab.rivm.nl.yml
    ├── automationhub.rivm.nl.yml
    ├── binrepo.rivm.nl.yml
    ├── satellite.rivm.nl.yml
    ├── vmwareserver.rivm.nl.yml
    └── testserver.rivm.nl.yml
``` 

Zoals al aangegeven, staat hier slechts de definitie van de deployment omgeving.

Hoe sluiten we nu een team aan?

## Team deel
Een team krijgt slechts de beschikking over een deel van de inventory,waarvoor zij verantwoordelijk zijn. Dat deel zal in een eigen gitlab repository staan, waarop zij schrijf rechten hebben.
De structuur voor een team zal er als volgt uitzien:
```
├── org_1.ini
├── group_vars
└── host_vars
    ├── org_1_host1.rivm.nl.yml
    └── org_1_host2.rivm.nl.yml
```

In deze structuur kan het team nieuwe group_vars aanmaken en nieuwe hosts definiëren die gedeployed moeten gaan worden. Er zijn wel een aantal voorwaarden aan verbonden om het goed te laten werken.  
- Hostnamen moeten uniek zijn
- Group_vars bestanden moeten uniek zijn (vooraf gegaan door Org_naam_ )
- Alle groups moeten onder de root van de organisatie hangen (org_1 in dit geval).

In de org_1.ini ziet dat er zo uit:
```
[org_1]
org_1_host1.rivm.nl.yml
org_1_host2.rivm.nl.yml
```

In het geval van extra groepen:
```
[org_1]

[org_1:children]
org_1_groep1

[org_1_groep1]
org_1_host1.rivm.nl.yml
org_1_host2.rivm.nl.yml

```
hiermee kunnen dan in het bestand group_vars/org_1_groep1.yml extra variablen aan deze groep worden meegegeven.

## Hoe koppelen we dit aan elkaar
Door in het automation platform voor elk team een inventory te definieren met 2 sources:
- 1 de basis inventory
- 2 de eigen inventory van de organisatie met als inventory file "org_1.ini"

Echter, hiermee hebben we nog geen werkende inventory, darvoor moet het automation team de basis inventory bij oplevering van een team aanpassen in de hosts.ini. Hieronder een voorbeeld:
```
[dev]

[dev:children]
org_1
org_2
test_env

[test_env]
gitlab.rivm.nl
vcenter.rivm.nl
...
```

het aantal lagen in deze inventory is met opzet beperkt gehouden m het beeld helder te houden.  
Meer organisaties is dan slechts een kwestie van een entry in de hosts.ini (of hosts.yml) 

## Voor- en nadelen
Een gesplitste inventory heeft een aantal voor- en nadelen, hieronder op voorhand een aantal voorbeelden: 

__Voordelen:__  
- Een team heeft echt alleen datgene, waar ze verantwoordelijk voor zijn.
- Voor patching kan een aparte inventory in AAP worden aangelegd met alle andere inventories gecombineerd, waardoor een volledig overzicht ontstaat.
  
__Nadelen:__
- Bij een update aan de basis inventory, moeten alle inventories ververst worden, dit is een stuk extra overhead en gaat standaard niet automatisch bij een static inventory.  
