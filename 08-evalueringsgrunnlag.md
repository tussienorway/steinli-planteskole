# 8. Evalueringsgrunnlag – til tre eksterne evaluator-agenter

Send samme pakke (README + 03 + 04 + 07) til **Codex**, **Antigravity** og **ChatGPT**. Det kjøres via @TussieBot – styreleder, siden denne agenten ikke kan kjøre dem selv. Be hver om et svar på maks én side per spørsmål med anbefaling, begrunnelse og risiko.

## Felles spørsmål
1. Er MVP-en (regneark + kalender + e-postmaler + lagervarsel) riktig første steg? Hva bør kuttes eller legges til?
2. Hvilken stack gir lavest drifts- og vedlikeholdskost for én bruker med lite IT-erfaring: PWA + serverless, no-code (f.eks. Make/Zapier + Google Sheets) eller ferdig SaaS?
3. Hvordan bør API-proxyen for tussies LLM-nøkkel bygges (token per bruker, budsjett-tak, tilbaketrekking, logging) uten at nøkkelen kan lekke?
4. Hvordan løse utskrift fra telefon til lokal skriver robust (print-agent, skytjeneste eller skriverens egen app)? Hvilken skrivertype til plantestikker og hentelapper?
5. Er Squarespace Commerce-API-et egnet for lager og ordre, eller bør lageret ligge i egen database?
6. Hvordan unngå feilbestillinger fra bildegjenkjenning? Hvilket terskelnivå og hvilke bekreftelsessteg?
7. Hva må på plass juridisk (GDPR, databehandleravtale, regnskapsloven for faktura)?

## Spesifikt per agent
- **Codex:** lag et konkret repo-oppsett og datamodell (varer, leverandører, priser, bestillinger) og estimer kodeomfang i fase 1–3.
- **Antigravity:** vurder agent-arkitekturen for «nesten autonom» drift. Hvilke oppgaver kan trygt automatiseres, og hvilke må alltid ha menneske i løkken?
- **ChatGPT:** vurder brukeropplevelsen for en travel gartner. Er ett-trykks-godkjenning realistisk? Lag tre skisser av hovedskjermen og en enkel opplæringsplan.

## Vurderingskriterier
Kost per måned, tid til første nytte, risiko for feil/lekkasje, vedlikehold og hvor lett det er å overta for en ny utvikler.
