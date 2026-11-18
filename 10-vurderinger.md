# Steinli Hekksalg – tre uavhengige vurderingar (J-261118-01-P2)

Dato: 18.11.2026 (Europe/Oslo).  
Kjeldegrunnlag: privat repo `tussienorway/steinli-planteskole` (README og 01–09, særleg 08-evalueringsgrunnlag.md).  
Metode: tre vurderingar med ulike perspektiv, skrivne uavhengig av kvarandre. Ingen kontakt utanfor teamet. Ingen endring, PR, issue eller pushing i repoet.

Merk: Dette er erstatningar for Codex, Antigravity/AGY og ChatGPT, laga av TussieBot sine tilsette, ikkje av dei verktya.

Slik er teksten merkt:
- **Frå repoet:** det som står i kjeldegrunnlaget.
- **Verifisert:** sjekka på nettet i denne runden, med lenke.
- **Vurdering:** eiga tilråding.
- **Usikkert:** ikkje stadfesta, eller motstrid i kjelder.

---

# Vurdering A – teknisk arkitektur og byggbarheit
**Erstatning for Codex – laga av TussieBot sine tilsette, ikkje av det verktyet**

Perspektiv: stack, datamodell, integrasjonar, kodeomfang, kostnad og vedlikehald.

### 1. Er MVP-en riktig fyrste steg? Kva bør kuttast eller leggjast til?

**Frå repoet:** Fase 1 er leverandør-/prisregister i rekneark, landa-kost-kalkulator, bestillingskalender, e-postmalar og lagervarsling, på 2–4 veker og nesten 0 kr. Han godkjenner alt som vert sendt. Ingen nye abonnement i fase 1. Floriday direkte, eige ERP og eigen import er medvite ikkje tilrådd no.

**Vurdering:** Ja, MVP-en er riktig fyrste steg. Han gjev nytte utan ny infrastruktur, og datainnsamlinga (prislister, SKU, minstelager) er uansett naudsynt før all automatikk.

Legg til, utan å auke kostnaden: eit skriftleg skjema for reknearket (feltnamn, typar, nøklar) slik at det seinare kan importerast; eit felt som koplar kvar vare til Squarespace-variant/SKU; og ei enkel sikkerheitskopi av arket. Lagervarsling i fase 1 bør vere eit varsel eller ei sjekkliste, ikkje eit skjørt API-kall, før planen og API-tilgangen er stadfesta.

Ikkje legg til i fase 1: PWA, LLM-proxy, utskriftsagent, faktura-API eller importflyt. Det høyrer til fase 2–4 i 07.

### 2. Kva stack gjev lågast drifts- og vedlikehaldskost?

**Vurdering:** For éin brukar med lite IT-erfaring er den rimelegaste vegen trinnvis, ikkje PWA + serverless frå dag éin.

- Fase 1: rekneark (Google Sheets eller lokalt Calc/Excel) + e-postmalar. Dette passar med «nesten 0 kr» i repoet. Om lagervarsling skal automatiserast, bruk Google Apps Script (ingen ny abonnementsteneste) framfor Make/Zapier.
- Fase 2–3: ein tynn backend (serverless eller ein liten maskin på garden) og PWA berre når bilete- og utskriftsflyten faktisk skal byggjast.
- Ferdig SaaS for innkjøp/planteskule er ikkje vurdert med pris her, og eit nytt fagsystem no bryt prinsippet om å byggje på det han allereie har.

Make/Zapier kan gje rask integrasjon, men innfører månadleg abonnement (pris ikkje henta i denne runda), leverandørlås og dårlegare sporbar datamodell. PWA + serverless frå start gjev høgast vedlikehald og krev ein utviklar som kan overta. Lågast langsiktig kostnad er eit enkelt, dokumentert oppsett med open datamodell, ikkje eit teip av no-code-flytar.

### 3. Korleis bør API-proxyen for tussies LLM-nøkkel byggjast?

**Frå repoet (04):** nøkkelen berre som hemmeleg miljøvariabel på serveren; Steinli får eige prøvetoken, ikkje sjølve nøkkelen; dagleg/månadleg tak på kall og tokens; maks lengde; berre tillatne modellar; kan trekkjast tilbake; sluttdato; logg med dato, tokens og kostnad, ikkje fullt innhald som standard; databehandlaravtale før reelle kundedata.

**Vurdering:** Fylg den designen, og gjer desse tekniske tillegga:

- Nøkkelen skal aldri hamne i klient, repo, e-post, telefon eller feilmeldingar ut igjen.
- Brukartoken skal lagrast hasha, kunne roterast og slettast uavhengig av oppstraumnøkkelen. Kortlevde sesjonstoken oppå eit langlivd utstedt token er betre enn éin evig streng i appen.
- Tak i både kall, tokens og ei kronegrense; varsel før taket; hard stopp når det er nådd. Eige tak hos API-leverandøren som reserve.
- Tillatsliste for modellar og endepunkt. Avvis for store requestar.
- Logg: tidspunkt, token-id (ikkje råtoken), modell, tokens, kostnad, statuskode. Ikkje prompt/svar som standard.
- Ikkje send personopplysningar gjennom proxyen før avtale er på plass.
- Helsesjekk og manuell reserve: dersom proxyen er nede, skal e-postmalar framleis kunne brukast for hand.

Ingen faktisk nøkkel er brukt eller lagra i dette arbeidet (som i repoet).

### 4. Korleis løyse utskrift frå telefon til lokal skrivar?

**Vurdering:** Den mest robuste løysinga for automatisk utskrift etter godkjenning er ein lokal utskriftsagent (t.d. CUPS) på ein alltid-på maskin i same nett som skrivaren, gjerne med kabel. Telefonen snakkar med backend/agenten, ikkje direkte med ein skytenseste for sjølve utskrifta. Skytensester innfører eit ekstra punkt som fell ut når nettet på garden er nede, nett når hentelappen trengst. Leverandørappen til skrivaren er grei som manuell reserve, men dårleg som hovudveg for «godkjenn → print».

Skrivartypar:
- Plantestikkar: termisk etikettkrivar. **Usikkert utan test:** direkte termisk bleiknar ofte i sol; for merkelappar som blir ståande ute er termotransfer eller vêrbestandige etikettar vanlegvis betre. Dette er ei vurdering, ikkje ein prissamanlikning.
- Hentelappar/pakksedlar: A4-laserskrivar.

Ikkje kjøp skrivar før fase 3. Prisar er ikkje henta. Ha ein reserve: PDF på telefonen + manuell utskrift, eller handskriven lapp.

### 5. Er Squarespace Commerce-API-et eigna for lager og ordre?

**Verifisert:** Squarespace har offentlege Commerce API-ar: Inventory (lese og justere lager per variant), Orders (liste, hente, oppfylle, og opprette ordre frå tredjepartskanal), Products (produkt og variantar) og webhook-abonnement. Inventory-API-et gjev lagersaldo og SKU, men ikkje attributt som storleik/farge; det ligg i Products-API-et. Kjelder: [Inventory](https://developers.squarespace.com/commerce-apis/inventory), [Orders](https://developers.squarespace.com/commerce-apis/orders), [Inventory overview](https://developers.squarespace.com/commerce-apis/inventory-overview), [API-nøklar](https://support.squarespace.com/hc/en-us/articles/236297987-Squarespace-API-keys).

**Usikkert:** Hjelpesida seier både at Orders/Inventory er tilgjengelege på Core, Plus, Advanced og Commerce Advanced, og lenger nede at dei «only [are] available in the Commerce Advanced plan». Kva plan Steinli faktisk har, er ikkje kjent.

**Vurdering:** API-et er eigna som kjelde for *butikk-lager og kundeordre*, ikkje som innkjøpssystem. Leverandørpris, MOQ, landa kost, bestillingsfristar og innkjøpsordre må liggje i eiga oversikt (rekneark no, database seinare). Tilråd: Squarespace er sanningskjelde for salslager; eige register for innkjøp; synkroniser lager med vilje og logg, ikkje to konkurrerande «sanne» tal. Verifiser planen før lagervarsling mot API vert lova. Fallback i fase 1: manuell lesing eller eksport frå butikken.

### 6. Korleis unngå feilbestillingar frå biletegjenkjenning?

**Verifisert:** Pl@ntNet-API-et returnerer ei liste med `score` mellom 0 og 1, sortert etter tryggleik, og opptil fem bilete av same individ i éin førespurnad. Kommersiell bruk er tillaten innafor 500 identifiseringar per døgn på gratisplanen. Dokumentasjonen seier at bark og ofte blad gjev dårlegare treffsikkerheit enn blome/frukt, og at bilete under 800×800 px ikkje er garanterte. Kjelder: [identify](https://my.plantnet.org/doc/api/identify), [pris](https://my.plantnet.org/pricing).

**Vurdering:** Pl@ntNet kan høgst gje ein *artshypotese* (t.d. *Thuja occidentalis*). Den seier ikkje kultivar (Smaragd mot Brabant) eller høgd (60–80 mot 180–200). Desse må kome frå eigen varekatalog, frå tekst på lapp, eller frå brukaren.

Reglar:
- Aldri send ordre automatisk (som i 07).
- Match berre mot eiga katalog; ukjend art → manuell SKU-veljar, ikkje «bestill likevel».
- Ikkje fastset ein numerisk terskel no. Kalibrer mot eigne bilete. Inntil vidare: krev at topptraffet er i katalogen, at det ikkje er jevnt med neste treff, og at brukaren ser bilete, foreslått SKU, storleik, tal, leverandør, landa kost og totalsum før godkjenning.
- Storleik og tal er eigne felt, ikkje noko visjonen får lov å dite.
- Handskriven lapp: LLM får berre strukturere; manglar art, storleik eller tal → avvis.
- To steg: forslag, deretter tydeleg godkjenning. Ikkje éin knapp som både tolkar og sender.

### 7. Kva må på plass juridisk?

**Frå repoet:** databehandlaravtale før reelle kundedata; minst mogleg data i logg.

**Verifisert:**
- GDPR art. 28 / personopplysningslova: skriftleg databehandlaravtale når nokon behandlar personopplysningar på vegner av den behandlingsansvarlege. [Datatilsynet](https://www.datatilsynet.no/rettigheter-og-plikter/virksomhetenes-plikter/hvordan-lage-en-databehandleravtale/hva-ma-en-databehandleravtale-innehalde/), [Lovdata art. 28](https://lovdata.no/dokument/NL/lov/2018-06-15-38/gdpr/ARTIKKEL_28).
- Bokføringslova § 10: dokumentasjon skal ikkje endrast etter utstedelse. [Lovdata](https://lovdata.no/dokument/NL/lov/2004-11-19-73).
- Salgsdokument skal minst ha nummer og dato, partar, ytingas art og omfang, tid og stad for levering, vederlag og forfall, og ev. mva i NOK. [Bokføringsforskrifta § 5-1-1](https://lovdata.no/dokument/SF/forskrift/2004-12-01-1558/KAPITTEL_5-1).
- Frå 1.1.2027 skal bokføringspliktige sende e-faktura (EHF/Peppol) til bokføringspliktige kjøparar som er registrerte i ELMA/tilsvarande. PDF er ikkje e-faktura. Unntak for omsetning som ikkje overstig 50 000 kr. [Skatteetaten](https://www.skatteetaten.no/bedrift-og-organisasjon/starte-og-drive/rutiner-regnskap-og-kassasystem/pliktig-efaktura/).

**Vurdering:** Steinli (rett føretak, sjå nedanfor) er behandlingsansvarleg. Den som driftar proxyen er databehandlar; LLM-leverandøren er underdatabehandlar. Avtale med begge før namn, telefon eller e-post går gjennom KI. Personvernerklæring og behandlingsgrunnlag (typisk avtaleoppfylling for ordre) må på plass. Overføring utafor EØS er **usikkert** til leverandør er vald.

Faktura: systemet skal berre lage *utkast*. Utstedelse skjer i eit rekneskapsprogram, av eit menneske. AS-et hadde salsinntekt over 1,1 mill. kr i 2025 (frå repoet), så unntaket på 50 000 kr gjeld truleg ikkje for AS-et. Vel program med EHF før 2027, ikkje bygg eigen fakturamotor. Kva program som vert brukt i dag, er ukjent (frå repoet).

Kva rettssubjekt som eig butikk, innkjøp og kundedata (ENK vs AS) må avklarast før avtalar og fakturamalar.

### Spesifikt: repo-oppsett, datamodell og kodeomfang (fase 1–3)

Ikkje endre research-repoet. Eit framtidig bygge-repo kan sjå slik ut:

```
steinli-innkjop/
  README.md
  docs/skjema.md          # datamodell og felt
  fase1-ark/malar/        # e-postmalar, kalender
  fase2-proxy/            # API-proxy med tak
  fase3-pwa/
  print-agent/
```

Datamodell (kan leve i ark i fase 1):

| Entitet | Nøkkelfelt |
|---|---|
| vare | sku, namn, art, sort/kultivar, storleik, leveringsform (potte/klump/barrot), squarespace_variant_id, min_lager, utsalspris |
| leverandør | id, namn, land, kontakt, valuta, moq-regel, merknad |
| pris | vare_id, leverandør_id, pris, valuta, moq, fraktregel, gyldig_frå/til, tidlegbestilling |
| landa_kost | utrekna (pris + frakt + ev. toll + gebyr) / tal; lagra med kursdato |
| bestilling | id, leverandør, status (utkast/godkjent/sendt/motteke), dato |
| bestillingslinje | vare, tal, avtalt pris, landa kost |
| kalenderfrist | leverandør, type, frist, merknad |
| hending/logg | lagerjustering, synk, godkjenning (utan unødvendige persondata) |

**Kodeomfang (vurdering, ikkje måling):**  
Fase 1 er hovudsakleg datainnsamling; 0 linjer om alt er manuelt, eller typisk nokre hundre linjer om eit script skal varsle om lager. 2–4 veker i repoet er realistisk som kalendertid fordi prislistene tek tid, ikkje fordi koden er stor.  
Fase 2 (proxy, Gmail-utkast, høyringsvakt): usikkert anslag i storleiksorden ca. 800–2000 linjer / 1–3 veker etter at avtale og tilgang ligg føre.  
Fase 3 (PWA, bilete, utskriftsagent, Squarespace-synk): vesentleg større, typisk fleire tusen linjer med UI og feilhandtering. Tidsbruken heng meir på skrivarval og faktisk API-tilgang enn på sjølve artsgjenkjenninga.

**Kriterium:** månadskost i fase 1 nær 0 kr utover det han allereie betaler for Squarespace (beløp ukjent). Tid til fyrste nytte: dagar til eitt par veker så snart dei fyrste prisene er lagde inn. Hovudrisiko: nøkkel-lekkasje (låst med proxydesign) og feil lager-synk. Vedlikehald og overtaking er lettast om skjema og malar ligg i eit vanleg git-repo med kort README, ikkje berre i ein no-code-konto.

---

# Vurdering B – drift og automatisering i kvardagen
**Erstatning for Antigravity/AGY – laga av TussieBot sine tilsette, ikkje av det verktyet**

Perspektiv: telefonflyt, AI-sekretær, kva som kan automatiserast, og kva som skjer når noko sviktar.

### 1. Er MVP-en riktig fyrste steg? Kva bør kuttast eller leggjast til?

**Frå repoet:** same MVP som i 03; test på éi innkjøpsrunde våren 2027.

**Vurdering:** Ja. I kvardagen er den fyrste reelle nytta *kalenderen og e-postmalane*. Å missa tidlegbestillingsvindauget (repoet: Thujakongen har rabatt jan–apr) kostar meir enn eit uferdig register. Landa kost kjem like etter, fordi han elles bestiller på kjensle.

Lagervarsling bør vente til minstelager er sett *sesongvis* (hekk har vår- og hausttopp, frå repoet). Eit varsel i juni på same minstenivå som i mars vert berre støy. Ikkje ta inn AI-sekretær i fase 1.

Legg til ei fast, kort vekerutine på telefonen (t.d. 15 minutt): sjå lager i butikken, sjå fristar, ev. lag eit utkast. Skriv rutina på éi side. Kutt alt som krev ny app-innlogging før rutina er innarbeidd.

### 2. Kva stack gjev lågast drifts- og vedlikehaldskost?

**Vurdering:** For ein gartnar som står på jordet, er den beste stacken den han allereie kan: Squarespace, e-post, telefon og eit rekneark. Ein PWA med serverless-funksjonar som feilar i sesong er dyrare i praksis enn eit abonnement, fordi det ikkje finst IT-vakt.

No-code (Sheets, ev. Make/Zapier seinare) er lettare å forklare enn eigen kode, men berre etter at den manuelle rutina verkar, og berre dersom månadskostnaden er akseptabel (pris ikkje henta her). Ferdig SaaS er interessant berre om det allereie dekkjer innkjøp og lager for norsk planteskule; noko slikt er ikkje kartlagt i repoet.

**Tilråding:** bli i kjende verktøy gjennom fase 1. Automatiser først når same handling er gjenteken og keisam, og når det finst ein manuell reserve.

### 3. Korleis bør API-proxyen byggjast, utan at nøkkelen lek?

**Frå repoet:** token per brukar, tak, tilbaketrekking, sluttdato, logg utan fullt innhald.

**Vurdering:** Designen i 04 er tilstrekkeleg som driftsmodell, med desse kvardagskrava:

- Hardt månadstak i kroner, ikkje berre i tokens. Varsel på e-post eller SMS ved t.d. halvt og nesten oppbrukt tak, deretter stopp.
- Prøveperioden skal ha synleg sluttdato. Token skal kunne drepast på eitt minutt utan at tussies eigne system vert rørde (som i 04).
- Telefonen skal aldri sjå oppstraumnøkkelen. Fell proxyen ut, skal han framleis kunne kopiere ei e-postmal og sende sjølv.
- Ikkje start proxyen før tak, sluttdato og kven som betaler overforbruk er skrivne ned.
- Logg er til styring og rekning, ikkje til å lagre kundesamtalar.

### 4. Korleis løyse utskrift robust?

**Vurdering:** På eit jorde er trådlaust nett og «kontor-PC-en» ustabile. For henting (butikken har berre henting, frå repoet) må hentelappen kunne kome ut også når det er travelt.

Hovudveg: ein eigen liten maskin ved skrivaren, mest mogleg alltid på, gjerne kablet, med utskriftskø. Reserveveg: PDF til telefon og utskrift via skrivaren si eiga app, eller handskriven hentelapp. Ikkje gjer ei skytenseste til einaste vegen.

Start med den A4-skrivaren som truleg allereie finst, til hentelappar. Plantestikkar kan vere handskrivne eller enkle etikettar i fyrste sesong. Stikkar som skal stå ute må tåle regn og sol; ikkje lov robust merking før dette er testa. Prisar og modell er ikkje henta.

### 5. Squarespace-API eller eiga lagerdatabase?

**Vurdering:** I drift er feil lagersaldo det kundane merkar: dei bestiller på nett og kjem til jordet. Difor skal butikklageret bu i Squarespace, som allereie viser lager per vare (frå repoet). Eit andre «sant» lager i ei eiga database vil drive frå kvarandre, særleg ved henting og ved sal som ikkje går gjennom nettet.

Innkjøpsplanlegging (kva som skal bestillast, hos kven, til kva kost) skal *ikkje* bu i Squarespace. Halde innkjøpet i arket. Vis gjerne både «tal i butikken» og «tal telt på feltet» når dei skil seg.

I fase 1: vekevarsel om å sjå på lageret i Squarespace. API-synk kjem når planen er stadfesta (sjå usikkerheit i vurdering A) og når det finst ein måte å registrere sal som ikkje går via nettet.

### 6. Korleis unngå feilbestillingar frå bilete?

**Vurdering:** Dei typiske feila i kvardagen er ikkje «API-en krasjar», men: bilete av feil plante, like artar, feil storleik, utydeleg lapp, og at systemet gjettar ein leverandør. Pl@ntNet treff art, ikkje 'Smaragd' mot 'Brabant' og ikkje centimeter. For hekk er det ofte berre blad eller heile planta å fotografere; det er typisk vanskelegare enn blome (verifisert i Pl@ntNet-dokumentasjonen).

Praktiske steg:
1. Bilete (gjerne fleire) + ev. bilete av lappen.
2. Systemet foreslår berre varer frå eigen katalog, med bilete og SKU side om side.
3. Brukaren stadfestar art/sort, storleik og tal kvar for seg.
4. Systemet viser leverandør, landa kost, totalsum og e-postutkast.
5. Eige trykk for «godkjenn utkast». Sending er eit menneske, eller eit tydeleg andre trykk etter at utkastet er lese.
6. Ved mottak: sjekk mot bestillinga før lageret vert auka.

Ved tvil er standard «ikkje bestill». Terskelverdiar skal settast etter at han har prøvd på eigne plantor, ikkje på førehand.

Feilsituasjonar og reserve:
- Pl@ntNet nede eller 429 (kvote): søk manuelt på SKU i katalog/ark.
- Proxy eller tak nådd: bruk e-postmal.
- Skrivar nede: handskriven lapp.
- Nett nede på garden: køa ventar, eller arbeid frå malar offline.
- Sal på jordet ikkje registrert i butikken: lageret lyger; krev ein enkel «trekk frå lager»-vane, elles vert innkjøpsforslaga feil.
- Squarespace nede i vårrush: ha ei papir-/SMS-rute for avtalt henting.

### 7. Kva må på plass juridisk?

**Vurdering:** I kvardagen er den enklaste regelen: kundnamn, telefon og adresse skal ikkje inn i KI-verktøyet før databehandlaravtale ligg føre. Leverandørpost og høyringsutkast inneheld som regel mindre persondata og kan kome tidlegare, men også der kan namn på kontaktpersonar dukke opp.

Faktura skal vere utkast til han godkjenner i rekneskapsprogrammet. Frå 1.1.2027 gjeld e-fakturaplikt til mange bedriftskundar (verifisert hos Skatteetaten); det rører anleggsgartnerdelen minst like mykje som hekksalet. Avklar kasse- og rekneskapsopplegg før fase 4, ikkje etter at KI lagar «fakturaer» som PDF.

### Spesifikt: agent-arkitektur for «nesten autonom» drift

Målbildet i 07 er riktig som visjon, men autonomi skal tyde «systemet førebudir, mennesket sleppper laus».

**Trygt å automatisere:**
- påminningar om fristar og lågt lager
- hente og samanlikne priser (berre informasjon, som i 04)
- utkast til bestillings-e-post, høyringssvar og faktura
- utskrift av hentelapp *etter* at ei ordre er godkjend eller merkt som klar
- melding til kunden om at varen er klar, *etter* at han har merkt ho som klar
- vekerapport over utkast, lageravvik og forbruk mot API-tak

**Alltid menneske i løkka:**
- sende innkjøpsordre
- betale
- endre utsalspris i butikken
- utstede faktura
- sende høyringssvar eller brev til styresmakter
- stadfeste art, storleik, tal og leverandør før bestilling
- slette eller eksportere kundedata

**Aldri automatisere:**
- sending av pengar
- automatisk innkjøp når lageret kryssar ei grense, utan godkjenning
- kontakt med kundar, leverandørar, Steinli eller styresmakter utan at det er godkjent
- å late ein modell «fikse» lagersaldo åleine

Ein agent bør vere ei kø av *forslag* med status (nytt, sett, godkjent, avvist, feila), ikkje ein prosess som handlar i bakgrunnen. Då kan ein travel gartnar tømme køa på telefonen når han har eit opphald, og alltid sjå kva som ventar.

**Kriterium:** tid til fyrste nytte kjem av kalender og malar, ikkje av agenten. Driftskost skal vere nær null til rutina sit. Største risiko i kvardagen er feil ordre og feil lager, ikkje val av rammeverk. Vedlikehald må tåle at det ikkje står ein utviklar klar i april.

---

# Vurdering C – forretning, kunde, marknad, lovverk og prioritering
**Erstatning for ChatGPT – laga av TussieBot sine tilsette, ikkje av det verktyet**

Perspektiv: om det lønner seg, kven som skal bruke det, risiko og kva som bør kome fyrst. Brukaroppleving for ein travel gartnar.

### 1. Er MVP-en riktig fyrste steg? Kva bør kuttast eller leggjast til?

**Frå repoet:** Steinlegger Steinli AS hadde salsinntekt 1 119 212 kr i 2025, årsresultat 44 203 kr og framleis negativ eigenkapital (−57 450 kr). Lønnskostnaden vart om lag halvert frå 2024 til 2025. ENK-et (Hekksalg) har ikkje offentleg rekneskap. Butikken er Squarespace, berre henting, ikkje levering. Ingen kontakt med verksemda er teken.

**Vurdering:** Ja, MVP-en er riktig, nettopp fordi økonomien er tynn og det ikkje er rom for eit nytt fagsystem på spekulasjon. Den forretningsmessige kjernen er *margin* (landa kost mot utsalspris) og *tidlegbestilling*, ikkje KI.

Legg til ei enkel marginrad per vare i arket. Det er der ein ser om ein «billig» NL-pris faktisk er billig etter frakt, eller om norsk tidlegbestilling vinn. Kutt eller utsett lagervarsling til minstelager og ledetid er kjende. Kutt i fase 1: AI-sekretær, ny kasse, eigen import og Floriday (allereie frårådd i repoet, med minstekjøp 250 000 €/år).

Fyrste suksessmål bør vere det repoet allereie seier: éi ekte innkjøpsrunde mot våren 2027, der arket faktisk vert brukt før bestilling.

### 2. Kva stack gjev lågast drifts- og vedlikehaldskost?

**Vurdering:** Lågast kostnad er ingen ny rekning. Bruk Squarespace, e-post og eit rekneark. PWA + serverless er ei investering som krev nokon som held det i live. No-code-verktøy har typisk månadspris (ikkje henta her) og lønner seg først når rutina er dokumentert og brukt.

Viktigare enn stack: *adopsjon*. Ingen er kontakta enno, og det er usikkert kven som faktisk skal sitje med telefonen til dagleg (Kristian er innehavar/dagleg leiar i registeret; Jan Steinli er omtalt i eldre GAT-saker, aktivitet i dag er usikker – frå repoet). Bygg ikkje arkitektur før det er avklart at nokon vil bruke det, og kva «agrarbossen» var meint å vere (domene ikkje registrert, frå repoet).

### 3. Korleis bør API-proxyen byggjast?

**Vurdering:** Teknisk er opplegget i 04 fornuftig, men dette er òg ein *forretnings- og tilsynsrisiko* for den som lånar ut nøkkelen: kostnad, lekkasje og inntrykk av å vere tenesteleverandør. Start berre etter skriftleg ramme (tak, sluttdato, kven som betaler, kva data som er tillatne). Prøveperioden skal vere stutt og utan kundedata om det let seg gjere. Etterpå skal Steinli ha eiga nøkkel eller betale for tenesta, som repoet allereie seier.

Ikkje gjer tussie til varig IT-leverandør for ein ekstern bedrift utan at det er eit medvete valg.

### 4. Korleis løyse utskrift – og kva skrivar?

**Vurdering:** Ikkje kjøp utstyr før flyten «ordre/henting» faktisk finst. Hentelapp på A4 har kundeverdi (rett plante til rett person ved henting på jordet). Plantestikkar har òg merkevareverdi, men kan startast enkelt. Vent med spesialskrivar til behovet er sett i éi sesong. Prisar er ikkje henta, og det bør innhentast tilbod når (og om) fase 3 vert aktuell – ikkje no.

### 5. Squarespace-API eller eiga database?

**Vurdering:** Butikken er kundekanalen; ikkje flytt salet bort frå Squarespace. Innkjøp og landa kost kan ikkje bu der, så eige register er naudsynt. Forretningsrisiko: API kan krevje ein dyrare plan enn den butikken har i dag (offentleg dokumentasjon er motstridande; planen er ukjend). Ein planoppgradering er ei ny kostnad og må avklarast før ein lovar automatisk lagersynk. Kundene merkar lagerfeil ved henting; det er der tilliten sit, sidan det ikkje finst levering.

### 6. Korleis unngå feilbestillingar frå bilete?

**Vurdering:** Feil art, kultivar eller storleik er dyrt: kapital bunden i plantar som ikkje sel, reklamasjon, og omdøme i eit lokalt marknad. Med svak eigenkapital er overbestilling farlegare enn å missa eit automatisk varsel.

Difor er ikkje éitt-trykks-godkjenning realistisk i fyrste sesong. Kunden (gartnaren) må sjå totalsum, leverandør og kva som faktisk skal sendast. Bilet-AI er eit forslag; handelen er eit vedtak. Etter at feilraten er kjend og låg, kan ein stramme inn til færre steg.

### 7. Kva må på plass juridisk – og kva marknadsrisiko tel med?

**Verifisert og frå repoet, samla:**

- Databehandlaravtale og dataminimering før kundedata i KI (GDPR art. 28).
- Faktura: utkast til godkjenning; utstedt salgsdokument skal ikkje endrast (bokføringslova § 10) og skal ha lovpålagt innhald (§ 5-1-1).
- E-fakturaplikt mot mange bedriftskundar frå 1.1.2027. AS-et ligg langt over unntaksgrensa på 50 000 kr. ENK-omsetning er ukjend.
- To rettssubjekt (Hekksalg ENK og Steinlegger Steinli AS) med same person og adresse. Avtalar, faktura og butikk må knytast til rett føretak.
- Sortiment og «sortlista»: tuja og laurbærhegg er *ikkje* føreslått forbodne i tilrådinga; *Taxus × media* er føreslått forboden, men var ikkje sett i nettbutikken då repoet vart skrive. Forslaget er *ikkje vedteke*. Eige sortiment bør sjekkast mot lista (Cotoneaster, Spiraea, Lonicera m.fl.). Kjelde i repoet: 06 og 09.
- Eigen import frå NL krev DigiPlant, plantesunnheitssertifikat, førehandsmelding og internkontroll. Ikkje start der.
- Floriday direkte passar ikkje volumet (frå repoet).
- Nærliggjande konkurranse: Hageland Fevik og Dømmesmoen ligg allereie i Grimstad (frå repoet). Kjede-medlemskap er ikkje fyrste trekk for ein hekkprodusent med henting på jordet.

**Usikkert:** om plantene er eigne eller innkjøpte; om Jan Steinli framleis er aktiv; kva kasse- og rekneskapssystem som vert brukt.

### Spesifikt: brukaroppleving, er éitt trykk realistisk, tre skisser, opplæring

**Er éitt-trykks-godkjenning realistisk?** Som sluttvisjon, ja, for *gjentekne, kjende* forslag med låg risiko (t.d. «send mal til same leverandør som i fjor, same vare»). Som standard for bilete-til-ordre i fyrste sesong, nei. Ein travel gartnar treng store knappar, lesbar tekst i sollys, kort veg til neste handling, og nok informasjon til å ta ein dårleg idé. Tale eller bilete av lapp er meir realistisk enn at han skal skrive lange skjema med skitne hender. Nett på jordet kan vere dårleg; utkast må ikkje forsvinne om dekninga fell ut.

**Skisse 1 – I dag (hovudskjerm)**

```
[ Steinli innkjøp ]
I dag, 18. nov
Frist: tidlegbestilling thuja (jan–apr)  !om 6 veker
Lager: 3 varer under minste  (opna liste)
Ventande utkast: 2 til godkjenning

[  Ta bilete  ]     [  Godkjenn (2)  ]
[  Kalender   ]     [  Vareregister  ]
```

**Skisse 2 – Etter bilete (forslag, ikkje sending)**

```
[ Bilete av planta          ]
Førelagt: Thuja occ. 'Smaragd' ?
  Alternativ: Brabant  |  Anna vare…

Storleik: [ 60–80 ▼ ]    Tal: [ 68 ]
På lager no: 12    Minste: 30
Kjelde: Thujakongen    Landa kost: … kr/stk
Totalsum utkast: … kr

[ Endre ]   [ Avbryt ]   [ Lag utkast → ]
```

Totalsum og leverandør synlege før neste steg. Ingen «Send» her.

**Skisse 3 – Godkjenningskø**

```
Utkast til godkjenning
1. Bestilling Thujakongen   68 × Smaragd 60–80   … kr
2. Hentelapp ordre #104    Agnbøk 80–100 × 20

Opna 1:
  Til: …    Emne: Bestilling …
  [tekst i utkastet]
  [ Avvis ]  [ Rediger ]  [ Godkjenn sending ]
```

Sending er eit eige, medvite trykk etter at teksten er synt.

**Opplæringsplan (kort, på jobb, ikkje kurs):**
1. Halvtime: vise arket, kalenderen og éi mal; fylle inn fem eigne varer med utsalspris og minst éin innkjøpspris.
2. Fyrste ekte runde: éi bestilling vert laga etter malen; han sender sjølv.
3. Deretter: ti minutt i veka gjennom sesongen (lager, fristar, margin).
4. KI-utkast og bilete først når den manuelle rutina sit. Éi A4-jukselapp: kva knapp, kva som aldri skal sendast automatisk, kva ein gjer når nettet er nede.
5. Ingen fleirtimmars opplæring; målet er at han greier neste vår utan hjelp.

**Prioritering (forretning):**
0. tussie avklarer kontakt og kva «agrarbossen» var; ingen kontakt er teken.
1. Rekneark med skjema, kalender, malar og margin; brukast på innkjøp mot våren 2027.
2. Rett føretak, ev. databehandlaravtale og veg til e-faktura før KI med kundedata og før faktura-utkast.
3. Deretter AI-utkast, så bilete-flyt, så utskrift, så faktura-integrasjon.
4. Import/samkjøp berre om volumet faktisk forsvarar pliktene.

**Kriterium:** månadskost nær null til verdien er vist. Tid til nytte: fyrste veke med priser i arket. Største risiko: pengar og tid brukt på system eigaren ikkje tek i bruk, eller feilbestilling som bind kapital. Vedlikehald skal kunne takast over av ein ny person ved å lese arket og jukselappen, ikkje berre av den som bygde det.

---

# Samla tilråding

Dei tre vurderingane er einige om dette: start med MVP-en i 03 (rekneark, landa kost, kalender, e-postmalar), utan nye tunge system; mennesket skal godkjenne all sending, betaling og fakturautstedelse; Squarespace blir verande salskanal og butikklager, medan innkjøp og landa kost bur i eige register; ikkje Floriday direkte og ikkje eigen NL-import no; databehandlaravtale og minst mogleg data før kundedata i KI; biletegjenkjenning er berre eit forslag, og Pl@ntNet dekkjer art – ikkje kultivar eller storleik.

Dei er ueinige om *kor raskt* ein skal vekk frå reknearket, og om *godkjenninga*. Vurdering A vil tidleg låse ein datamodell og ein stig mot ein liten, dokumentert backend/PWA, og er skeptisk til no-code-abonnement. Vurdering B vil bli i kjende verktøy og manuelle vekerutinar så lenge som mogleg, med agenten som ei kø av forslag og sterk manuell reserve når nett, skrivar eller API sviktar. Vurdering C vil ha null ny kostnad og påvist adopsjon fyrst, tostegs godkjenning (ikkje éitt trykk) i fyrste sesong, og utset skrivar- og API-løfte til plan, margin og kontakt er avklart.

**Fyrste steg:** Ikkje kontakt nokon utanfor teamet. Lag fase 1-arket med tydeleg skjema (vare, leverandør, pris, landa kost, fristar, SKU-kopling til Squarespace) pluss kalender og e-postmalar, og fyll inn offentlege priser som allereie er kjeldefeste i repoet (t.d. Thujakongen). Verifiser Squarespace-planen/API-tilgang utan å endre butikken. Avklar kva rettssubjekt som skal eige verktøy, avtalar og faktura, og vent på tussie når det gjeld kontakt med Steinli og kva «agrarbossen» var. Målet er at arket vert brukt på éi ekte innkjøpsrunde mot våren 2027.

---

# Vedlegg: kva er verifisert og kva er usikkert

## Verifisert i denne runda (med lenke)
- Squarespace Commerce API finst og dekkjer lagerjustering, ordre og produkt/variantar: https://developers.squarespace.com/commerce-apis/inventory , https://developers.squarespace.com/commerce-apis/orders , https://developers.squarespace.com/commerce-apis/inventory-overview , https://support.squarespace.com/hc/en-us/articles/236297987-Squarespace-API-keys
- Pl@ntNet: `score` 0–1, inntil 5 bilete per identifisering, 500/døgn gratis også for kommersiell bruk, bark/blad typisk vanskelegare: https://my.plantnet.org/doc/api/identify , https://my.plantnet.org/pricing
- Databehandlaravtale, GDPR art. 28: https://www.datatilsynet.no/rettigheter-og-plikter/virksomhetenes-plikter/hvordan-lage-en-databehandleravtale/hva-ma-en-databehandleravtale-innehalde/ , https://lovdata.no/dokument/NL/lov/2018-06-15-38/gdpr/ARTIKKEL_28
- Bokføringslova § 10 og salgsdokumentets innhald: https://lovdata.no/dokument/NL/lov/2004-11-19-73 , https://lovdata.no/dokument/SF/forskrift/2004-12-01-1558/KAPITTEL_5-1
- E-fakturaplikt frå 1.1.2027: https://www.skatteetaten.no/bedrift-og-organisasjon/starte-og-drive/rutiner-regnskap-og-kassasystem/pliktig-efaktura/

## Teke frå repoet, ikkje sjekka på nytt mot register/nettbutikk i denne runda
- Organisasjonsdata, rekneskapstal, sortiment, prisdøme, at agrarbossen.no ikkje er registrert, leverandørtabellar, sortliste-status for einskilde artar, Floriday-minstekjøp, Zettle-prisane, at ingen kontakt er teken. Dette er oppgjeve som henta 17.–18.11.2026 i 01 og 09.

## Usikkert
- Motstrid i Squarespace-hjelpeteksten om kva planar som gjev Inventory/Orders-API, og kva plan butikken har.
- Kasse- og rekneskapssystem i bruk; pris på Fiken/Tripletex, Make/Zapier og Squarespace-abonnement (ikkje henta).
- Kodeomfang og tidsbruk i fase 2–3 er anslag, ikkje måling.
- Numerisk terskel for Pl@ntNet-score er ikkje testa på eigne bilete.
- Om plantene er eigenproduserte eller innkjøpte; om Jan Steinli framleis er aktiv; slektskap Kristian/Jan; om veksthus-/sommarblomstersal framleis finst.
- ENK-økonomi (ikkje offentleg rekneskap).
- Tollsats per eksakt varenummer for tuja/hekk (repoet seier at dette må sjekkast i tolltariffen).
- Danske/tyske leverandørprisar (ikkje henta i repoet).
- Overføringsgrunnlag utafor EØS for ev. LLM-leverandør, sidan leverandør ikkje er vald.
- Kva «agrarbossen» var meint å vere.
