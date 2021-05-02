# Päeva pikkuse arvutamise rakendus
### CGI Tartu suvepraktika 2021 proovitöö

## Tehniline info
Projekt on tehtud WebStormi arenduskeskkonnaga ja kasutab Vue.js raamistikku. Käivitamiseks on vajalik Node Package Manager (npm) ja installeeritud peab olema Vue.js.
Kõigepealt on vajalik projekti installeerimine käsuga "npm install" ja veebilehte saab käivitada tavapärase käsuga "npm run serve".

Projektis on kasutatud erinevaid teeke/library'sid:
* SunCalc (https://github.com/mourner/suncalc) päikesetõusu ja -loojangu arvutamiseks
* Vue Leaflet (https://vue2-leaflet.netlify.app/) kaardi kuvamise jaoks
* Vue Google Charts (https://www.npmjs.com/package/vue-google-charts) graafiku kuvamise jaoks

Nende teekide lehtedelt võtsin kasutuseks ka üldist näidiskoodi teekide funktsionaalsuse kasutamiseks.

## Märkmed arendustöö kohta
Esialgu osutus raskeks päeva pikkuse arvutamise implementeerimine. Üritasin seda ise algusest peale teha, kuna valemid on internetist leitavad ja mitte ülemäära keerulised. Sain ka funktsionaalsuse enam-vähem tööle, aga siiski vigadega, ning alles hiljem tuli pähe, et ilmselt leidub just niisuguste arvutuste jaoks juba olemasolevaid teeke.

Ka Vue Leafleti abil kaardi tööle saamine ja sellest info kätte saamine osutusid keeruliseks.

Viimaseks raskuseks oli polaarpäevade ja -öödega arvestamine - SunCalci funktsioonid tagastavad lihtsalt "Invalid Date" ja seetõttu pidin ise kindlaks määrama, kas tegu on polaarpäeva või -ööga, ja kas päeva pikkus on vastavalt 24h või 0h.

## Ajakulu
24.04:
0,5h - projekti üles seadmine ning OpenLayers ja Leafleti kohta lugemine

28.04:
1h -   päikesetõusu ja -loojangu aja arvutamise kohta info otsimine
1h -   Etapp 1 implementeerimine

29.04:
1h -   SunCalc libraryle üleminek ja veebilehe kujundamine
3h -   Vue Leafletiga kaardi tööle saamine ja kaardilt info kätte saamine
0.5h - kujunduse parandamine

30.04:
1.5h - manuaalselt koordinaatide valimine kajastub automaatselt ka kaardil,
l-markeri bugfix

01.05:
2h -   erinevate graafikuraamistike uurimine, Google Vue Chartsi valimine ja graafiku implementeerimine
1.5h - kujunduse ja lehel näha oleva teksti muutmine ja parandamine

02.05:
1.5h - polaarpäeva ja polaarööga arvestamine ja errorite vältimine
1h -   kujunduse parandamine
1h -   dokumentatsiooni täiustamine