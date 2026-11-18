# 7. Sluttvisjon: nesten autonom planteskole

Mål: Steinli tar seg av plantene. Alt administrativt går via telefonen, og han godkjenner med ett trykk.

## Eksempelflyt: «bilde → ordre → utskrift»
1. Han tar bilde av en plante, en hylle eller en håndskrevet lapp i appen (PWA på telefonen).
2. **Gjenkjenning:** Pl@ntNet API foreslår art (gratis opptil 500/dag). Deretter matches arten mot hans egen varekatalog (Squarespace-SKU). Lapper og tekst tolkes av en LLM.
3. **Forslag:** «Thuja Smaragd 60–80, 12 igjen, minimum 30. Bestill 68 fra billigste kilde (landet kost X kr)?»
4. **Godkjenn** med ett trykk. Da lages bestillings-e-post (Gmail-utkast eller sendes etter godkjenning), lagertall oppdateres og en hentelapp/plantestikker skrives ut på nettverksskriveren.
5. **Kunde:** når en nettordre kommer, skrives hentelapp ut automatisk og kunden får SMS/e-post.

## Arkitektur (enkel)
```
Telefon (PWA)  ──►  Backend/API-proxy (serverless) ──► LLM (tussies nøkkel i prøveperiode, budsjett-tak)
     │                    │──► Pl@ntNet (plantegjenkjenning)
     │                    │──► Squarespace Commerce (varer/lager/ordre) – API-tilgang må verifiseres
     │                    │──► Gmail (utkast/sending etter godkjenning)
     │                    │──► Regnskap (Fiken/Tripletex API) – faktura-utkast
     │                    │──► Utskriftskø ──► liten print-agent på PC/Raspberry Pi ──► etikett-/A4-skriver
     │                    └──► Høringsvakt + prisvakt (planlagte jobber)
     └── Database: varer, leverandører, priser, bestillinger, logg
```

## Faseplan
| Fase | Innhold | Avhenger av |
|---|---|---|
| 0 (nå) | Avklare agrarbossen, avklare kontakt med Steinli, evaluering av byggemåte | tussie |
| 1 MVP | Pris-/leverandørregister, landet-kost, bestillingskalender, e-postmaler, lagervarsel | Prislister fra leverandører |
| 2 | AI-sekretær via proxy med tak, Gmail-utkast, høringsvakt | Godkjenning + databehandleravtale |
| 3 | Telefonflyt bilde→ordre→utskrift, etikettskriver | Valg av skriver og Squarespace-API |
| 4 | Faktura via regnskaps-API, prognoser, nyhetsbrev, klikk-og-hent | Én sesong med data |
| 5 | «Nesten autonom»: automatiske innkjøpsforslag og ukesrapport, mennesket godkjenner | Stabil drift i fase 1–4 |

## Risiko
- Feil artsgjenkjenning, så det må alltid være bekreftelse før ordre.
- Ingen automatisk sending av penger eller ordre uten godkjenning.
- Avhengighet av tussies API-nøkkel, som derfor har budsjett-tak og sluttdato.
- Personvern (kundedata), som krever databehandleravtale og minst mulig data.
