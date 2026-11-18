# 4. Fase 2 – AI-funksjoner og AI-sekretær

## Funksjoner
| Funksjon | Inndata | Utdata | Menneskelig kontroll |
|---|---|---|---|
| E-postutkast | Stikkord eller tale («bestill 100 Smaragd 60–80 fra X») | Ferdig e-post i Gmail-utkast | Han trykker Send |
| Bestillingsskjema | Bilde av lapp eller tale | Strukturert bestilling (art, str., antall, leverandør) | Godkjennes før sending |
| Faktura | Utført jobb / salg | Utkast i regnskapsprogrammet via API | Godkjennes i programmet |
| Utskrift | Bestilling / salg | Plantestikker, hentelapp eller pakkseddel på nettverksskriver | Automatisk etter godkjent ordre |
| Prisvakt | Leverandørprislister (PDF/nett) | Oppdatert prisregister + varsel om billigere kilde | Kun informasjon |
| Høringsvakt | regjeringen.no / Miljødirektoratets høringer | Varsel + utkast til høringssvar | Han sender selv |

## Midlertidig bruk av tussies LLM-API (prøveperiode) – kun design
- **Nøkkelen ligger bare på serveren** (en liten proxy, f.eks. en serverless-funksjon) som hemmelig miljøvariabel. Den skal aldri ligge i appen, på telefonen, i repoet eller i e-post.
- Steinli får en **egen prøvetilgang** (personlig token) som proxyen kjenner igjen, ikke selve API-nøkkelen.
- **Tak:** daglig og månedlig budsjett (antall kall og tokens), maks lengde per forespørsel og bare tillatte modeller. Proxyen stopper når taket er nådd. I tillegg settes et forbruksvarsel/-tak hos API-leverandøren.
- **Kan trekkes tilbake** ved å slette eller rotere tokenet i proxyen. Det rammer ikke tussies egne systemer. Prøveperioden har sluttdato i konfigurasjonen.
- **Logging:** dato, antall tokens og kostnad per kall, *ikke* fullt innhold som standard (personvern).
- **Overgang:** etter prøveperioden legger Steinli inn sin egen nøkkel eller betaler for tjenesten. Proxyen bytter nøkkel uten endringer i appen.
- **Personvern:** kundedata (navn, telefon) sendes bare når det trengs. Det må lages en databehandleravtale mellom partene før reelle kundedata behandles.
- Ingen faktisk nøkkel er skrevet eller lagret i dette arbeidet.
