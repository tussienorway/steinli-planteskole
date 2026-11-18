# Fase 1 – innkjøpsark for Steinli Hekksalg

Et regneark med landet kost, bestillingskalender og lagervarsel, pluss e-postmaler. Alt bygger på det som allerede står i repoet. Ingen nye abonnementer. Du godkjenner og sender selv.

## Filer

| Fil | Innhold |
|---|---|
| `steinli-innkjop.xlsx` | Fem ark: Leverandører, Varer og SKU, Landet kost, Bestillingskalender, Lager og varsel |
| `e-postmaler.md` | Fem korte maler (prisliste, tidligbestilling, bestilling, reklamasjon, frakt) |

Excel og LibreOffice tillater ikke skråstrek i arknavn, derfor heter arkene «Varer og SKU» og «Lager og varsel».

## Slik bruker du arket

1. Ta en kopi av `steinli-innkjop.xlsx` før du fyller inn egne tall (for eksempel `steinli-innkjop-2027.xlsx`). Behold originalen i repoet som mal.
2. Åpne kopien i Excel eller LibreOffice Calc. La formlene beregnes automatisk.
3. **Gule celler** er inndata. **Grå celler** er formler – ikke skriv over dem. **Oransje rader merket EKSEMPEL** viser bare utregning og formatering. De skal ikke brukes som bestillingsgrunnlag.
4. På **Varer og SKU**: fyll inn Squarespace-SKU og variant-ID slik de står i nettbutikken. Ikke endre butikken. Sett min. lager sesongvis (vår og høst for hekk er ulike).
5. På **Landet kost**: fyll inn dagskurs for euro, om prisen er inkl. mva (JA/NEI), frakt, ev. toll og gebyr, svinn og ønsket margin. Uten kurs og mva-valg blir utregningen stående som ufullstendig. Tom frakt telles som 0 og gir for lavt landet kost – det står i kolonnen Status.
6. På **Bestillingskalender**: sjekk tidligbestilling hos Thujakongen før januar, slik at vinduet januar–april ikke glipper. Øvrige frister er merket «ukjent» til du får dem bekreftet.
7. På **Lager og varsel**: én gang i uken, les lageret i Squarespace og fyll inn «Tall i butikken». Tell gjerne også på feltet. Rødt varsel «Bestill» er et forslag, ikke en ordre.
8. Kopier en mal fra `e-postmaler.md`, lim inn tall fra arket, og send selv.

## Hva som er kilde, og hva som er eksempel

- **KILDE-rader:** leverandører, varer og priser som allerede er kildelagt i `05-leverandorer-nederland-hekk.md` og `02-agrarbossen-og-markedet.md` (hentet 18.11.2026). Kolonnen Kilde peker til filen.
- **Ukjent:** kontaktpersoner, de fleste frister, frakt, dagskurs, norsk mva-behandling av leverandørprisene, toll for konkret varenummer, og faktisk lagerbeholdning.
- **EKSEMPEL-rader:** oppdiktet kurs, frakt, gebyr, svinn, margin og lagerantall, bare for å vise formlene. Enhetsprisene som inngår i eksemplene er likevel hentet fra kilden, og det står i merknaden.
- **MAL-rader:** tomme linjer til egne oppføringer.
- **Intern SKU** (for eksempel `THU-SMA-180-200`) er et forslag til nøkkel i arket, ikke en butikk-SKU.
- **MVA-sats 25 %** er vanlig norsk sats, lagt inn som redigerbar forutsetning. Den er ikke hentet fra leverandørkildene.
- Prisene er ikke direkte sammenlignbare (størrelse, potte/klump, kvalitet). Bruk dem som startpunkt for forespørsel, ikke som fasit.

## Landet kost – formlene

1. Pris per stk i NOK brutto = pris × kurs (for NOK er kurs 1).
2. Trekk fra mva hvis prisen er merket JA; er den merket NEI, brukes prisen som eks. mva. Står feltet tomt, stoppes utregningen.
3. Landet kost per plante før svinn = (varekost eks. mva + frakt + toll/avgift + gebyr) / antall.
4. Landet kost etter svinn = beløpet over / (1 − svinn%). Tom svinn telles som 0.
5. Foreslått utsalgspris inkl. mva = landet kost etter svinn × (1 + margin) × (1 + mva), avrundet til hele kroner.
6. Margin mot egen utsalgspris sammenligner landet kost med prisen på arket Varer og SKU (for eksempel 549 kr på 180–200 cm).

I EKSEMPEL-raden for FlorAccess (17 stk à 14,92 €) brukes kurs 11,00, frakt 3 500 kr, gebyr 500 kr, svinn 5 % og margin 40 %. Da skal du se 420,43 kr i landet kost etter svinn og foreslått utsalgspris 736 kr. Tallene er bare en demonstrasjon.

## Det dette arket ikke gjør

- Sender ikke e-post, skriver ikke ut og kobler ikke til Squarespace via API.
- Erstatter ikke nettbutikken som salgslager.
- Anbefaler ikke Floriday direkte eller egen import fra Nederland nå.

Når første ekte innkjøpsrunde mot våren 2027 er kjørt i arket, kan fase 2 vurderes.
